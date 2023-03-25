# C++中的布隆过滤器计数

> 原文：<https://medium.com/hackernoon/counting-bloom-filter-in-c-9672ec25b3ec>

我最近偶然看到了迈克尔·施马茨的 bloom filter 帖子，它启发我写了一个关于 T2 的 bloom filter 的巧妙变体，我发现它对我的工作很有用。快速复习:布隆过滤器是一种概率性的[数据结构](https://hackernoon.com/tagged/data-structure)，它测试一个元素是否[潜在地](https://hackernoon.com/tagged/potentially)在一个集合中。当测试一个元素是否在集合中时，假阳性是可能的，但是阴性意味着一个元素肯定不在集合中。明白了吗？酷毙了。

计数过滤器本质上是一个 bloom 过滤器，它的一位布尔值被 n 位整数所取代。这使得过滤器比标准的 bloom 过滤器占用更多的空间，但作为回报，我们可以获得特定元素插入的上限计数，并可以从过滤器中删除元素。虽然删除元素可能非常简单，但是如果我们删除了一个从未插入到计数过滤器中的元素，我们就有可能出现假阴性。小心点——[郭对此比我说得更多。](https://pdfs.semanticscholar.org/b0cd/0a280b1c54fb218c1ca37b8f77b634fb6f45.pdf)

## 履行

我用下面的接口设计了我的计数过滤器类:

```
// Adds an element to the counting filter.
void Add(const T *key, int size = sizeof(T));// Removes an element from the counting filter.
void Remove(const T *key, int size = sizeof(T));// Test whether the element has been added. If false, the element 
// is definitely not in the set. If true, the element could be in
// the set or it is a false positive as described above.
bool MaybeContains(const T *key, int size = sizeof(T)) const;// Get the upper bound on the number of times an element could
// have been inserted into the counting filter.
int CountUpperBound(const T *key, int size = sizeof(T)) const;
```

计数器存储在 std::vector 中，如下所示:

```
std::vector<uint8> counters_;
```

我最初用存储在 std::array 对象中的计数器实现了这一点。使用这个数组，过滤器在我的机器上以大约 1.35 倍于 std::unordered_set 插入的运行时间进行了基准测试。对于 vector，它的基准是大约 2.05 倍 std::unordered_set 插入。我选择了较慢的选项，因为代码看起来更干净，并且它允许我们在实例化期间指定 bloom filter 中散列的大小和数量，而不需要使用模板参数(std::arrays 必须在编译时具有已知的大小)。

构造函数非常简单。简单地通过调用 resize()预先分配 counters_ vector，也许只是用零填充向量，所以我应该从展示如何使用散列来获得索引对开始:

```
template <typename T, int64_t kSize, int32_t kNumHashPairs>
void CountingFilter<T, kSize, kNumHashPairs>::IdxFromKey(
  const T *key,
  const int size,
  const uint32_t seed,
  int64_t *idx1,
  int64_t *idx2) const {

  array<uint64_t, 2> results;
  MurmurHash3_x64_128(key, size, seed, results.data());
  *idx1 = results[0] % counters_.size();
  *idx2 = results[1] % counters_.size();
  assert(*idx1 < counters_.size());
  assert(*idx2 < counters_.size());
}
```

请注意，这将实现限制为偶数个散列，但我喜欢我们得到 64 位散列的二对一交易这一事实。

Add()函数必须考虑计数超过最大计数器值的可能性。在这种情况下，除了避免增量并将过滤器数据标记为潜在错误之外，我们没有什么可做的。

```
template <typename T, int64_t kSize, int32_t kNumHashPairs>
void CountingFilter<T, kSize, kNumHashPairs>::Add(
  const T *key, const int size) {for (int32_t xx = 0; xx < kNumHashPairs; ++xx) {
    int64_t idx1, idx2;
    IdxFromKey(key, size, xx, &idx1, &idx2);
    // It's possible that the count can exceed the maximum uint8
    // value, so we'll just leave it be. After many removals,
    // this could result in a false negative, but this is very
    // unlikely. Let's just assert for this case.
    assert(counters_[idx1] <= numeric_limits<uint8_t>::max());
    assert(counters_[idx2] <= numeric_limits<uint8_t>::max());
    counters_[idx1] += 1;
    counters_[idx2] += 1;
  }
  ++num_insertions_;
}
```

移除要简单得多，因为我们只是递减计数器。在这种情况下，是否在递减计数器之前添加计数器非零的断言由您决定:

```
template <typename T, int64_t kSize, int32_t kNumHashPairs>
void CountingFilter<T, kSize, kNumHashPairs>::Remove(
  const T *key, const int size) {

  for (int32_t xx = 0; xx < kNumHashPairs; ++xx) {
    int64_t idx1, idx2;
    IdxFromKey(key, size, xx, &idx1, &idx2);
    assert(counters_[idx1] > 0);
    assert(counters_[idx2] > 0);
    counters_[idx1] -= 1;
    counters_[idx2] -= 1;
  }
  --num_insertions_;
}
```

检查元素是否存在于过滤器中只是检查所有计数器是否非零。获取元素在过滤器中的插入次数的上限需要我们找到最小的计数器。这是一个上限，因为碰撞可能会打乱我们的计算。

```
template <typename T, int64_t kSize, int32_t kNumHashPairs>
int CountingFilter<T, kSize, kNumHashPairs>::CountUpperBound(
  const T *key, const int size) const {

  int count_ub = numeric_limits<uint8_t>::max() + 1;

  for (int32_t xx = 0; xx < kNumHashPairs; ++xx) {
    int64_t idx1, idx2;
    IdxFromKey(key, size, xx, &idx1, &idx2);
    count_ub = min(static_cast<int>(counters_[idx1]), count_ub);
    count_ub = min(static_cast<int>(counters_[idx2]), count_ub);
  }
  return count_ub;
}
```

## 包扎

这种数据结构是对普通 bloom 过滤器的有趣改进。我的实现目前的基准测试是一个预先保留的 std::unordered_set 的插入时间的 2.05 倍。当工作数据集非常大时，我主要使用它来对缓存插入进行挑选，但我很乐意为此找到更多的应用程序。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！