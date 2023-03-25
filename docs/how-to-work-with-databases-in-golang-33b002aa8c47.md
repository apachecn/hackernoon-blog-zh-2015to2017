# 如何通过 Golang 使用数据库

> 原文：<https://medium.com/hackernoon/how-to-work-with-databases-in-golang-33b002aa8c47>

在 Golang 中有很多不同的方法来使用数据库。我不喜欢这些方法，尤其是 ORM 的。很多时候，它们会生成低效的查询、错误的锁、检索太多的数据，而你却不知道发生了什么。经过几次实现后，我决定采用这种模式。该模式使用 jmoiron 的 sql 抽象库 [sqlx](https://github.com/jmoiron/sqlx) ，并允许完全定制和控制。

项目结构将如下所示:

*   **db/db.go** = >包含接口定义并嵌入默认的 sqlx 结构
*   **模型/** = >包含所有的数据库模型
*   **utils/** = >包含通用函数

**db.go** 文件包含所有的魔法和接口，本质上是默认 sqlx 函数的包装器。该结构尝试在模型本身中查找自定义实现，如果找不到，则返回错误或默认实现。该结构定义了默认行为，如(有限的)选择、计数、选择、插入、更新和删除。

**db.go**

```
package dbimport (
 "errors"
 "fmt"
 "reflect""github.com/jmoiron/sqlx"
 "github.com/op/go-logging"
)var log = logging.MustGetLogger("db")type Query stringtype Queryx struct {
 Query  Query
 Params []interface{}
}type DB struct {
 *sqlx.DB
}type Tx struct {
 *sqlx.Tx
}var (
 ErrNoGetterFound   = errors.New("No getter found")
 ErrNoDeleterFound  = errors.New("No deleter found")
 ErrNoSelecterFound = errors.New("No getter found")
 ErrNoUpdaterFound  = errors.New("No updater found")
 ErrNoInserterFound = errors.New("No inserter found")
)func Limit(offset, count int) selectOption {
 return &limitOption{offset, count}
}type limitOption struct {
 offset int
 count  int
}func (o *limitOption) Wrap(query string, params []interface{}) (string, []interface{}) {
 query = fmt.Sprintf("SELECT a.* FROM (%s) a LIMIT ?, ?", query)
 params = append(params, o.offset)
 params = append(params, o.count)
 return query, params
}type selectOption interface {
 Wrap(string, []interface{}) (string, []interface{})
}func (tx *Tx) Selectx(o interface{}, qx Queryx, options ...selectOption) error {
 q := string(qx.Query)
 params := qx.Paramslog.Debug(q)for _, option := range options {
  q, params = option.Wrap(q, params)
 }if u, ok := o.(Selecter); ok {
  return u.Select(tx.Tx, Query(q), params...)
 }stmt, err := tx.Preparex(q)
 if err != nil {
  return err
 }return stmt.Select(o, params...)
}func (tx *Tx) Countx(qx Queryx) (int, error) {
 stmt, err := tx.Preparex(fmt.Sprintf("SELECT COUNT(*) FROM (%s) q", string(qx.Query)))
 if err != nil {
  return 0, err
 }count := 0
 err = stmt.Get(&count, qx.Params...)
 return count, err
}func (tx *Tx) Getx(o interface{}, qx Queryx) error {
 if u, ok := o.(Getter); ok {
  return u.Get(tx.Tx, qx.Query, qx.Params...)
 }stmt, err := tx.Preparex(string(qx.Query))
 if err != nil {
  return err
 }return stmt.Get(o, qx.Params...)
}func (tx *Tx) Get(o interface{}, query Query, params ...interface{}) error {
 if u, ok := o.(Getter); ok {
  return u.Get(tx.Tx, query, params...)
 }stmt, err := tx.Preparex(string(query))
 if err != nil {
  return err
 }return stmt.Get(o, params...)
}func (tx *Tx) Update(o interface{}) error {
 if u, ok := o.(Updater); ok {
  return u.Update(tx.Tx)
 }log.Debug("No updater found for object: %s", reflect.TypeOf(o))
 return ErrNoUpdaterFound
}func (tx *Tx) Delete(o interface{}) error {
 if u, ok := o.(Deleter); ok {
  return u.Delete(tx.Tx)
 }log.Debug("No deleter found for object: %s", reflect.TypeOf(o))
 return ErrNoDeleterFound
}func (tx *Tx) Insert(o interface{}) error {
 if u, ok := o.(Inserter); ok {
  err := u.Insert(tx.Tx)
  if err != nil {
   log.Error(err.Error())
  }
  return err
 }log.Debug("No inserter found for object: %s", reflect.TypeOf(o))
 return ErrNoInserterFound
}func (db *DB) Begin() *Tx {
 tx := db.MustBegin()
 return &Tx{tx}
}type Updater interface {
 Update(*sqlx.Tx) error
}type Inserter interface {
 Insert(*sqlx.Tx) error
}type Selecter interface {
 Select(*sqlx.Tx, Query, ...interface{}) error
}type Getter interface {
 Get(*sqlx.Tx, Query, ...interface{}) error
}type Deleter interface {
 Delete(*sqlx.Tx) error
}
```

这是 person 模型的一个示例实现。

```
package modelimport (
        "fmt"
        "time""github.com/jmoiron/sqlx"db "./db"
)type Gender stringvar (
        GenderMale    Gender = "male"
        GenderFemale  Gender = "female"
)func (u *Gender) Scan(value interface{}) error {
        if value == nil {
                return nil
        }b := value.([]byte)
        *u = Gender(b)
        return nil
}func (u Gender) Value() (driver.Value, error) {
        return string(u), nil
}type Person struct {
        PersonID      utils.UUID `db:"person_id"`FirstName     string     `db:"first_name"`
        LastName      string     `db:"last_name"`Active        Bool       `db:"active"`
        Gender        Gender     `db:"gender"`ModifiedDate  time.Time  `db:"modified_date"`
}var (
        queryPersons        db.Query = "SELECT person_id, first_name, last_name, gender, active, modified_date FROM persons"
        queryPersonByID     db.Query = "SELECT person_id, first_name, last_name, gender, active, modified_date FROM persons WHERE person_id=:person_id"
        queryPersonInsert   db.Query = "INSERT INTO persons (person_id, first_name, last_name, gender, active, modified_date) VALUES (:person_id, :first_name, :last_name, :gender, :active, :modified_date)"
        queryPersonUpdate   db.Query = "UPDATE persons SET first_name=:first_name, last_name=:last_name, gender=:gender, modified_date=:modified_date, active=:active WHERE person_id=:person_id"
)func QueryPersons(offset, count int) db.Queryx {
        return db.Queryx{
                Query: queryPersons,
                Params: []interface{}{
                },
        }
}func QueryPersonByID(personID utils.UUID) db.Queryx {
        return db.Queryx{
                Query: queryPersonByID,
                Params: []interface{}{
                        personID,
                },
        }
}func NewPerson() *Person {
        return &Person{PersonID: utils.NewUUID(), ModifiedDate: time.Now() }
}func (s *Person) Insert(tx *sqlx.Tx) error {
        _, err := tx.NamedExec(string(queryPersonInsert), s)
        return err
}func (s *Person) Update(tx *sqlx.Tx) error {
        s.ModifiedDate = time.Now()_, err := tx.NamedExec(string(queryPersonUpdate), s)
        return err
}func (s *Person) Delete(tx *sqlx.Tx) error {
        s.Active = false
        return s.Update(tx)
}
```

现在已经定义了数据库和模型，您可以使用如下模式:

```
tx := db.Begin()var err error
defer func() {
        if err != nil {
                tx.Rollback()
                return
        }tx.Commit()
}()// retrieve single person
person := model.Person{}
if err := tx.Getx(&person, model.QueryPersonByID(personID)); err != nil {
        return err
}person.Lastname = "Doe"// update the person
if err := tx.Update(&person); err != nil {
        return err
}index := 0
count := 50// retrieve multiple paged persons
persons := []model.Person{}
if err := ctx.tx.Selectx(&persons, model.QueryPersons(user), db.Limit(index, count)); err == sql.ErrNoRows {
} else if err == nil {
} else {
        return err
}// count number of results
total, err := ctx.tx.Countx(model.QueryPersons())
if err != nil {
        return err
}
```

Defer 将检查是否发生了错误，以及是否将回滚事务。否则，它将只提交事务。我们不需要在实现中更新最后的修改日期，这在模型中已经考虑到了。我们还可以通过设置活动标志来定义不同的删除行为。可以实现附加的 selectOptions，如 limitOption。

这种模式有以下优点:

*   完全负责查询定义，很容易定义连接、子查询或特定的优化
*   使用这个新函数，你可以用缺省值进行初始化
*   每个操作(插入、更新或删除)都可以有一个带有附加检查或行为的自定义实现
*   支持默认操作克林计数和限制
*   所有的操作都是强类型的，避免了很多错误
*   所有操作都组织在一个包中
*   所有的查询都是相互靠近的，这使得查询的验证变得容易(在添加字段或过滤器的情况下)
*   每个查询都包装在一个事务中
*   可以使用枚举(如性别)
*   测试可以很容易地在 db 类中实现
*   通过一些修改，查询可以根据所使用的数据库以不同的方式实现
*   查询本身可以部分使用 go generate 生成

缺点之一是你需要写相当多的代码，但是作为回报，所有的东西都是结构化的，可测试的，并且错误会少得多。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！