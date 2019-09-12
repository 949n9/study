# mongoose

操作对象一样操作数据库

步骤：

```js
1.安装并引入  const mongoose = require("mongoose")
2.建立连接  mongoose.connect("mongodb://127.0.0.1/myapp")
    如果在数据库没有myapp这个数据库，连接时，并不会自动创建这个数据库 
3.定义一个Schema，它需要和你最终在数据库中创建的字段保持一样
    Schema创建完后，也不会在数据库自动创建这个数据库
4.根据Schema创建model 
5.实例化model
6.通过save保存数据到数据库,创建数据库
```

## Schemas

Mongoose 的一切始于 Schema。每个 schema 都会映射到一个 MongoDB collection ，并定义这个collection里的文档的构成。

```js
const mongoose = require("mongoose")

let UserSchema = mongoose.Schema({
    username: String,
    age: {
      type: Number,  //类型
      default: 18  //默认值
    },
    status: Number
},{versionKey: false})
//versionKey设置成false后，插入的数据不会加入"__v"字段，官方建议加上，于版本控制相关。

let User = mongoose.model("User", UserSchema,"collectionName")
//第三个参数是制定集合名字，如果不加第三个参数，Mongoose会字段将第一个参数变成小写，然后变成复数作为集合的名字。
let person1 = new User({
    username: "WangCai",
    age: 100,
    status: 1
})
//新建示例

person1.save(function (err) {
    if (err){
        console.log(err);
    }
    console.log("数据保存成功");
})
//写入示例
```

## Model

[Models](http://www.mongoosejs.net/docs/api.html#model-js) 是从 `Schema` 编译来的构造函数。 它们的实例就代表着可以从数据库保存和读取的 [documents](http://www.mongoosejs.net/docs/documents.html)。 从数据库创建和读取 document 的所有操作都是通过 model 进行的。

### 构造 documents

[Documents](http://www.mongoosejs.net/docs/documents.html) 是 model 的实例。 创建它们并保存到数据库非常简单：

```javascript
var Tank = mongoose.model('Tank', yourSchema);

var small = new Tank({ size: 'small' });
small.save(function (err) {
  if (err) return handleError(err);
  // saved!
})

// or

Tank.create({ size: 'small' }, function (err, small) {
  if (err) return handleError(err);
  // saved!
})
```

要注意，直到 model 使用的数据库连接（ connection ）被打开，tanks 才会被创建/删除。每个 model 都有一个绑定的连接。 如果 model 是通过调用 `mongoose.model()` 生成的，它将使用 mongoose 的默认连接。

```javascript
mongoose.connect('localhost', 'gettingstarted');
```

如果自行创建了连接，就需要使用 connection 的 `model()` 函数代替 mongoose 的 `model()` 函数。

```javascript
var connection = mongoose.createConnection('mongodb://localhost:27017/test');
var Tank = connection.model('Tank', yourSchema);
```

###Queries 查询

[Query API](http://www.mongoosejs.net/docs/api.html#query-js) 文档中有查询函数的完整列表。

比较常用的几个：

[findOne()](http://www.mongoosejs.net/docs/api.html#model_Model.findOne) 是单个文档（有可能是 null ）
[find()](http://www.mongoosejs.net/docs/api.html#model_Model.find) 是文档列表 
[count()](http://www.mongoosejs.net/docs/api.html#model_Model.count) 是文档数量
[update()](http://www.mongoosejs.net/docs/api.html#model_Model.update) 是被修改的文档数量。

以下方法都是在Model上面的

#### save()

向数据库保存数据，每`save()`一次，保存一条数据.

```js
news.save(function(err){
     if(err) console.log(err) 
     console.log("保存数据成功了...")
)
```

#### find()

示例：

```js
User.find({},function (err,person) {
    if (err){
        console.log(err);
    }
    console.log(person);
})
//Mongoose 中每一处查询，被传入的回调函数都遵循 callback(error, result) 这种模式。
```

#### updateOne()

更新一条数据

```js


User.updateOne(
  {"_id": "5d6cd911297b007dce3a122f"},
  {"username": "WangHuaHua"},
  function (err,res) {
    if (err){
        console.log(err);
    }
    console.log(res);
})
//{ n: 1, nModified: 1, ok: 1 } 如果成功打印 
//{ ok: 0, n: 0, nModified: 0 } 如果失败打印
```

#### deleteOne()

删除一条数据

```js
News.deleteOne({"_id":"5d6c81351c9a341f18623d0c"},(err,data)=>{
    if(err) console.log(err) 
    console.log(data) // { n: 1, ok: 1, deletedCount: 1 }
})
```



##  主要指令

#### find

```js
    User.find({},(err,items) => {
        if(err) { throw err}
        res.json({
            code: 200,
            data: {
                total: items.length,
                items: items
            }
        })
    })
```



#### findOneAndRemove

```js
    User.findOneAndRemove({_id：id}).then(res =>{
        res.json({
            code: 200,
            data:{
                code:200,
                success: true
            }
        })
    })
```

#### findOneAndUpdate

```js
    User.findOneAndUpdate({_id}, { $set: update },{new: true}).then(resu =>{
        res.json({
            code: 200,
            data:{
                code:200,
                success: true
            }
        })
    })
    
    //update，是需要更新的数据，可以按需更新。
```



#### deleteMany

```js
    User.deleteMany({ _id: { $in : idList}}).then(()=>{
        res.json({
            code: 200,
            success: true
        })
    }).catch(err => console.log(err))
//idList 是存有id的数组，批量删除
```

