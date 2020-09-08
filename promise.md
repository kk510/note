# promise

## 回调地狱callback hell

无法保证顺序的代码

es6参考文档：https://es6.ruanyifeng.com/#docs/let

```javascript
var fs = require('fs')

fs.readFile('./data/a.txt', 'utf8', function(err, data) {
    if (err) {
        return console.log('读取成功')
            //抛出异常
            //1.阻止程序异常
            //2.打印错误
        throw err;
    }
    console.log(data);
})

fs.readFile('./data/b.txt', 'utf8', function(err, data) {
    if (err) {
        return console.log('读取成功')
            //抛出异常
            //1.阻止程序异常
            //2.打印错误
        throw err;
    }
    console.log(data);
})
fs.readFile('./data/c.txt', 'utf8', function(err, data) {
    if (err) {
        return console.log('读取成功')
            //抛出异常
            //1.阻止程序异常
            //2.打印错误
        throw err;
    }
    console.log(data);
})
```

可以保证顺序

```javascript
var fs = require('fs')

fs.readFile('./data/a.txt', 'utf8', function(err, data) {
    if (err) {
        return console.log('读取成功')
            //抛出异常
            //1.阻止程序异常
            //2.打印错误
        throw err;
    }
    console.log(data);
    fs.readFile('./data/b.txt', 'utf8', function(err, data) {
        if (err) {
            return console.log('读取成功')
                //抛出异常
                //1.阻止程序异常
                //2.打印错误
            throw err;
        }
        console.log(data);

        fs.readFile('./data/c.txt', 'utf8', function(err, data) {
            if (err) {
                return console.log('读取成功')
                    //抛出异常
                    //1.阻止程序异常
                    //2.打印错误
                throw err;
            }
            console.log(data);
        })
    })
})
```

为了解决以上编码方式带来的问题（回调低于嵌套），所以在es6中新增了一个API，`Promise`

promise基本语法

```javascript
var fs = require('fs')
    //1.创建promise容器 一旦创建就开始执行代码
var p1 = new Promise(function(resolve, reject) {
        fs.readFile('./data/aa.txt', 'utf8', function(err, data) {
            if (err) {
                //承诺容器钟的任务失败了
                reject(err); //把容器的pending状态变为rejected
            } else {
                //承诺容器中的任务成功了
                // console.log(data);
                //也就是resolve就是then传递的那个function
                resolve(data); //把容器的pendin状态改为resolve
            }
        })
    })
    //then中的function就是容器中的resolve函数
p1.then(function(data) {
    console.log(data)
}, function(err) {
    console.log('失败', err)
})
```

then 的链式调用 解决上部分不保证顺序的问题

```javascript
var fs = require('fs')
    //1.创建promise容器 一旦创建就开始执行代码
var p1 = new Promise(function(resolve, reject) {
    fs.readFile('./data/a.txt', 'utf8', function(err, data) {
        if (err) {
            //承诺容器钟的任务失败了
            reject(err); //把容器的pending状态变为rejected
        } else {
            //承诺容器中的任务成功了
            // console.log(data);
            //也就是resolve就是then传递的那个function
            resolve(data); //把容器的pendin状态改为resolve
        }
    })
})
var p2 = new Promise(function(resolve, reject) {
    fs.readFile('./data/b.txt', 'utf8', function(err, data) {
        if (err) {
            //承诺容器钟的任务失败了
            reject(err); //把容器的pending状态变为rejected
        } else {
            //承诺容器中的任务成功了
            // console.log(data);
            //也就是resolve就是then传递的那个function
            resolve(data); //把容器的pendin状态改为resolve
        }
    })
})
var p3 = new Promise(function(resolve, reject) {
        fs.readFile('./data/c.txt', 'utf8', function(err, data) {
            if (err) {
                //承诺容器钟的任务失败了
                reject(err); //把容器的pending状态变为rejected
            } else {
                //承诺容器中的任务成功了
                // console.log(data);
                //也就是resolve就是then传递的那个function
                resolve(data); //把容器的pendin状态改为resolve
            }
        })
    })
    //then中的function就是容器中的resolve函数

p1.then(function(data) {
        console.log(data)
            //当p1读取成功的时候
            //当前函数中return的结果就可以在then中的function接收 p2是一个promise对象
            //当return一个Promise对象的时候，后续的then中的方法的第一个参数会作为p2的resolve
        return p2
    }, function(err) {
        console.log('失败', err)
    })
    .then(function(data) {
        console.log(data)
        return p3
    }).then(function(data) {
        console.log(data);
    })
```

以上代码封装函数后

```javascript
function pReadFile(filepath) {
    return new Promise(function(resolve, reject) {
        fs.readFile(filepath, 'utf8', function(err, data) {
            if (err) {
                //承诺容器钟的任务失败了
                reject(err); //把容器的pending状态变为rejected
            } else {
                //承诺容器中的任务成功了
                // console.log(data);
                //也就是resolve就是then传递的那个function
                resolve(data); //把容器的pendin状态改为resolve
            }
        })
    })
}
pReadFile('./data/a.txt')
    .then(function(data) {
        console.log(data);
        return pReadFile('./data/b.txt')
    })
    .then(function(data) {
        console.log(data);
        return pReadFile('./data/c.txt')
    }).then(function(data) {
        console.log(data)
    })
```

