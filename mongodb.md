### shell异步回调函数

```javascript
function add(x,y,callback){
    //callback 就是回调函数
    //var x =10
    //var y =20
    //var callback = function(ret){console.log(ret)}
    console.log(1);
    setTimeout(function(){
        var ret = x +y;
        callback(ret)
    },1000)
}
add(10,20,function(ret){
    consoloe.log(ret)
})
```



#### 异步封装ajax方法(jquery)

```javascript
<script>
        function get(url, callback) {
            var oReq = new XMLHttpRequest();
            //当请求加载成功之后要调用指定的函数
            oReq.onload = function() {
                //我现在需要得到这里的oReq.responseText
                callback(oReq.responseText)
            };
            oReq.open("get", url, true);
            oReq.send();
        }
        get('data.json', function(data) {
            console.log(data);
        })
    </script>
```

基于原生XMLHTTPRequest的get方法

# Mongodb

## 关系型数据库和非关系型数据库

表就是关系

或者说表与表之间存在关系

+ 所有的关系型数据库都需要`sql`语言来操作

+ 所有的关系型数据库在操作者之前都需要设计表结构

+ 而且数据表还支持约束

  - 唯一的
  - 主键
  - 默认值
  - 非空

+ 非关系型数据库非常的灵活

+ 有的非关系型数据库就是key-valued对

+ 但是MongoDB是长的最像关系型数据库的非关系数据库

  - 数据库 -》数据库
  - 数据表-》集合(数组)
  - 表记录-》(文档对象)

  MongoDB不需要设计表结构

## 	启动和关闭

#mongodb默认使用c盘data中的db作为数据存储目录

#所以在第一次执行该命令之前自己手动创建一个/data/db

`mongod`

如果想要修改默认的数据目录可以

```
mongod --dbpath=数据存储目录路径
```



停止：

```
ctrl +c
```

连接

```
mongo  
```

退出

```
#在连接状态输入exit退出
```

## 基本命令

+ `show dbs`
  + 查看显示所有数据库
+ db
  + 查看当前的数据库默认是test
+ `use数据库名称`
  + 切换到指定的数据库（如果没有会新建）
+ 插入数据

## 在node中如何操作mongodb数据

### 使用官方mongodb包

​	https://github.com/mongodb/node-mongodb-native

### 使用第三方mongoose来操作MongoDB数据库

第三方包，mongoose

https://mongoosejs.com/

使用：

```
npm install mongoose
```



```shell
const mongoose = require('mongoose');
//引包
mongoose.connect('mongodb://localhost:27017/test', { useNewUrlParser: true, useUnifiedTopology: true });
//连接
const Cat = mongoose.model('Cat', { name: String });
//创建一个模型，就是在设计数据库 
//mongoose就可以让你的设计编写过程非常的简单
const kitty = new Cat({ name: 'Zildjian' });
//实例化一个cat
kitty.save().then(() => console.log('meow'));
//持久化保存一个kitty实例
```

```shell
use test//使用test数据库
show collections//展示所有的集合
db.cats.find()//找到所有
```

## 本数据概念

+ 可以有多个数据库

+ 一个数据库可以有多个集合(表)

+ 一个集合中可以有多个文档(表记录)

+ 文档结构很灵活，没有任何限制

+ MongoDB非常灵活，不需要像MySQL一样先创建数据库、表、设计表结构

  + 在这里只需要，当你需要插入数据的时候，只需要指定往哪个数据库的哪个集合操作就行了
  + 一切都有MongoDB来帮你自动完成

+ ```javascript
  {
  qq:{
  users:[
     {name:'张三',age:'12'}, 
     {name:'李四',age:12}, 
     {name:'sasa',age:14},
      ...
  ],
  products:[
       
   ],
     ... 
  },
      
  taobao:{
  
  },
  baidu:{
  
  },
  }
  ```

## 官方指南

### 设计Scheme发布Model

```shell
var mongoose = require('mongoose');
var Schema = mongoose.Schema;
//1.连接本机test 指定连接的数据库不需要存在，当你插入第一条数据之后就会自动被创建出来
mongoose.connect('mongodb://localhost:27017/itcast', { useNewUrlParser: true, useUnifiedTopology: true });
//2.设计集合结构(表结构)
//字段名称就是表结构中的属性名称
//约束的目的是为了保证数据的完整性，不要有脏数据
var userSchema = new Schema({
    username: {
        type: String,
        required: true //必须有
    },
    password: {
        type: String,
        required: true
    },
    email: {
        type: String
    }
});
//3.将文档结构发布为模型
//mongoose.modal()就是将一个架构发布为model的
//第一个参数：传入一个大写名词单数字符串用来表示你的数据库名称，mongoose会自动将大写名词的字符串生成小写复数的名称
//第二个参数：架构Schema
//返回：模型构造函数
var User = mongoose.model('User', UserSchema);
```

### 增加数据

```shell
var admin = new User({
    username: 'admin',
    password: '123456',
    email: 'admin@admin.com'
})
admin.save(function(err, ret) {
    if (err) {
        console.log('保存失败')
    } else {
        console.log('保存成功')
        console.log(ret);
    }

})
```

### 查询

查询所有

```shell
User.find({ username: 'zs' }, function(err, data) {
    if (err) {
        console.log('查询失败')
    } else {
        console.log(data);
    }
})
```

按条件查询所有

```javascript
  User.find({ username: 'zs' }, function(err, data) {
    if (err) {
        console.log('查询失败')
    } else {
        console.log(data);
    }
})
```

按条件查询单个

```shell
User.findOne({
    username: 'zs',
    password:'123456'
}, function(err, ret) {
    if (err) {
        console.log(err)
    } else {
        console.log(ret)
    }
})
```

删除数据

```shell
User.remove({ username: 'zs' }, function(err, ret) {
    if (err) {
        console.log('删除失败')
    } else {
        console.log('删除成功')
    }
})
```

根据条件删除一个

```shell
Model.findeOneAndRemove(conditions,[options],callback)
```

根据id删除一个

```
Model.findByIdAndRemove(id,[options],callback
```

更新数据

```shell
User.findByIdAndUpdate('5f0d133129b4403884062c88', { password: "123" }, function(err, ret) {
    if (err) {
        console.log('更新失败')
    } else {
        console.log('更新成功')
    }
})
```

根据条件更新所有

```
Model.findOneAndUpdate([conditions,[update],[options],[callback])
```

根据指定条件更新一个

```
Model.findOneAndUpdate([conditons],[update],[options],[callback])
```

