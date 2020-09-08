

# ts

## --outDir

```javascript
tsc --outDir .\dist .\src\hello.ts //创建一个文件夹专门存放编译后得js代码
tsc --outDir .\dist --target es6 .\src\hello.ts //决定编译后代码得es版本
tsc --outDir .\dist --target es6 --watch .\src\hello.ts//监听更改后得内容 相当于nodemon
```



## 编译配置文件

```javascript
{
    "compilerOptions":{
        "outDir":"./dist",
        "target":"es5",
        "watch":true
    },
    "include":["./src/**/*"]
}
```

# ts中的类型系统

## 基础的简单类型标注

```javascript
基础类型：string,number,boolean
	let title:string='开课吧'
	let n:number = 100
	let isOk:boolean = true
空和未定义类型: 
因为Null和Undefined这两种类型有且只有一个值，在标注一个变量为Null和Undefined类型，那就表示该变量不能修改了
	let a: null;//ok
	let a:1 //error
默认情况下null和undefined是所有类型得子类型，就是说你可以把null和undefin赋给其他得变量
	let a: number;
	a = null;
如果一个变量声明了，但是未赋值，那么该变量得值未undefined，但是如果它同时也没有标注类型得话，默认类型为any
	//类型为 `number`，值为`undefined`
	let a:number;
	//类型为：`any` 值为`undefined`
小技巧：
	因为null和undefined都是其它类型得子类型，所以默认情况下会有一些隐藏问题
    let a : number;
	a = null;//ok 实际上运行是有问题的
	a.toFixed(1)//小技巧1：指定strictNullChecks配置为true，可以有效得检测null或者undefined，避免很多常见问题

	let a:number;
	a = null//error
	a.toFixed(1)
对象类型
数组类型
元组类型
枚举类型
无值类型
Never类型
任意类型
未知类型


```

# 接口与高级类型

## 可选属性

```javascript
interface Point{
    x:number;
    y:number;
    color?:string;
}
    color?标识属性是可选的
```

## 只读属性

```javascript
interface Point{
   readonly x:number;
    y:number;
    color?:string;
}
```

## 任意属性-字符串类型索引

```javascript
interface Point{
   readonly x:number;
   readonly y:number;
    // color?:number;
    [key:string]:number
}
//注意：索引签名参数类型必须为string或number之一，但两者可同时出现
```

## 使用接口描述函数

```javascript
interface IEventFunc{
    (e:MouseEvent):void
}
function on(el:HTMLElement,evname:string,callback:IEventFunc){

}
let div = document.querySelector('div');
if(div){
    on(div,'click',function(e){
        e.clientX
    })
}

    function todo(callback:IFunc){
    let v = callback(1,2)
}

todo(function(a:number,b:number):number{
    return a+b;
})
```

## 接口合并

```javascript
interface Box{
    height:number;
    width:number;
}
interface Box{
    scale:number;
}
let box: Box={
    height:5,width:6,scale:10
}
//如果合并的接口存在同名的非函数成员，则必须保证他们类型一致，否则编译报错
//接口中的同名函数则是采用重载
```

## 类型保护

我们通常在JS中通过判断来处理一些逻辑，在TS中这种条件语句种还有另外一个特性：根据判断逻辑的结果，缩小类型范围（有点类似断言），这种特性称为类型保护，触发条件：

+ 逻辑条件语句块：if、else、elseif
+ 特定的一些关键字：type、instanceof、in....



# 高级类型

## 联合类型

联合类型也可以称为多选类型，当我们希望标注一个变量为多个类型之一时可以选择联合类型标注  或的关系

```javascript
function css(ele:Element,attr:string,value:string|number){
    //```
}
let box = document.querySelector('div')
if(box){
    css(box,'width','100px')
    css(box,'width',100);
    // css(box,'width',[1,2])
}
```

## 交叉类型

交叉类型也可以称为合并类型，可以把多种类型合并到一起成为一种新的类型，并且的关系

对一个对象进行扩展：

```javascript
interface o1{x:number;y:string};
interface o2 {z:number}

let o:o1& o2 =Object.assign({},{x:1,y:'likun'},{z:2})

```

## 字面量类型

有的时候，我们希望标注的不是某个类型，而是一个固定值，就可以使用字面量类型，配合联合类型会更有用

```javascript
function setPosition(ele:Element,direction:'left'|'top'|'right'|'bottom'){
//
}
let box = document.querySelector('div')
if(box){
    setPosition(box,'left')
}
```

## 类型别名

有的时候类型标注比较复杂，这个时候我们可以将类型标注起一个相对简单的名字

```javascript
type dir = 'left'|'top'|'right'|'bottom'
function setPosition(ele:Element,direction:dir){
//
}
type callback = (a:string)=>void;
let fn1:callback = function(a){}
```

## interface 和type的区别

interface

+ 只能描述object/class/function 类型
+ 同名interface 自动合并，利于扩展

type

+ 不能重名
+ 能描述所有数据

## 类型推导

每次都显示标注类型会比较麻烦，ts提供了一种更加方便的特性：类型推导。ts编译器会根据当前上下文自动的推导出对应的类型标注，这个过程发生在：

+ 初始化变量
+ 设置函数默认参数值
+ 返回函数值

```javascript
let a = '开课吧';
function fn(a=1){
    if(true){
        return 'kaikeba'
    }else{
        return 1;

    }
}
```

## 类型断言

有的时候，我们可能标注一个更加精确多的类型。比如：

```javascript
let img = document.querySelector("#img")
```

我们可以看到img的类型为Element，而Element类型其实只是元素类型的通用类型，如果我们去访问src这个属性是有问题的，我们需要把它的类型标注得更为精确，HTMLImageElement类型，这个时候，我们就可以使用类型断言，它类似于一种类型转换;

```javascript

let img = document.querySelector('#img');
if(img){
    (<HTMLImageElement>img).src 
}
```

```javascript
let img = document.querySelector('#img');
if(img){
    // (<HTMLImageElement>img).src 
    (img as HTMLImageElement).src
}
```

注意：断言只是一种预判，并不会对数据本身产生实际的作用，即：类似转换，但并非真的转换了

# 函数的标注

```javascript
function fn1(a:string):string{
    return ''
;}

let fn2:(a:string)=>string = function(a){
    return '';
}

interface ICallBack{
    (a:string):string
}
let fn4: ICallBack = function(e){
    return '';
}

```

## 可选参数 和默认参数

### 可选参数

通过参数名后面添加？来标注该参数是可选的

```javascript
function css(el:HTMLElement,attr:string,val?:any){

}

let div = document.querySelector('div');

div && css(div,'width','100px')
div && css(div,'width')
```

### 默认参数

我们还可以给参数设置默认值

+ 有默认值的参数也是可选的
+ 设置了默认值的参数可以根据值自动推导类型

```javascript
function sort(items:Array<any>,order:'desc'|'asc' = 'desc'){

}

sort([1,2,3],'asc')//ok
sort([1,2,3],'ac')//error
```

### 剩余参数

```javascript
interface IObj{
    [key:string]:any;
}

function merge(target,...others:Array<IObj>){
    return Object.assign(target,...others)
}
let newObj = merge({x:1},{y:2},{z:3})
```

### this    普通函数

```javascript
interface T{
    a:number;
    fn:(x:number)=>void
}

let obj :T={
    a:1,
    fn(this:T,x:number){
        // (<T>this).a;
        this;
    }
}
```

### this 箭头函数

```javascript
interface T {
    a:number;
    fn:(x:number)=>void;
}
let obj2:T={
    a:1,fn(this:Window,x:number){
        return ()=>{
            this
        }
    }
}
```

### 函数重载

有的时候，同一个函数会接收不同类型的参数返回不同类型的返回值，我们可以使用函数重载来实现，通过下面的例子来体会一下函数重载

```javascript
function showOrHide(el:HTMLElement,attr:'display'|'opacity',value:'block'|'none'|number){
//
}

let div3 = document.querySelector('div');

if(div3){
    showOrHide(div,'display','block')
    showOrHide(div,'display',1)//ok
    
}

//函数重载
function showOrHide(ele:HTMLElement,attr:'display',value:'block'|'none');
function showOrHide(ele:HTMLElement,attr:'opacity',value:number);
function showOrHide(el:HTMLElement,attr:any,value:any){
    //
}
let div3 = document.querySelector('div');
if(div3){
    showOrHide(div,'display','block')//ok
    showOrHide(div,'display',1)  //error
    showOrHide(div,'opacity','none')//error
}
```

# 面向对象

## 类的基础

```javascript
class User{
    //具体特征是 {}中定义的
    id:number;
    username:string;
    constructor(id:number,
        username:string )
        {
        //创建类的函数，当类通过new实例化的时候，就会执行该函数
        console.log('这是构造函数，对类中成员属性进行初始化赋值');
        this.id = id;
        this.username = username;
    }
    postArticle(title:string,content:string){
        console.log(`${this.username}发表了一篇文章:${title}`);
        
    }

}

let user1 = new User(1,'likun ');
console.log(user1);
user1.postArticle('标题','内容')


//public
class User{
    //具体特征是 {}中定义的
    // id:number;
    // username:string;

    /* 当我们给构造函数参数设置了访问修饰符 public，那么ts会做如下两件事情
        -给当前类添加同名的成员属性
        -在类的实例化的时候，会把传入的参数值赋值给对应的成员属性
    */    
    constructor( public id : number,
        public username : string)
        {
        //创建类的函数，当类通过new实例化的时候，就会执行该函数
        console.log('这是构造函数，对类中成员属性进行初始化赋值');
       //this.id = id;
       //this.username =username;
    }
    postArticle(title:string,content:string){
        console.log(`${this.username}发表了一篇文章:${title}`);
        
    }

}

let user1 = new User(1,'likun ');
console.log(user1);
user1.postArticle('标题','内容')

```





### 继承

```javascript
class User1{
   
    constructor( public id : number,
        public username : string)
        {
        
        console.log('这是构造函数，对类中成员属性进行初始化赋值');
    
    }
    postArticle(title:string,content:string){
        console.log(`${this.username}发表了一篇文章:${title}`);
        
    }

}
class VIP extends User1{
    constructor(
        id:number,
        username:string,
        public source:number
    ){
        super(id,username);
        //必须在super调用后才能访问this
        console.log("子类构造函数");
    }
    postAttachment(file:string):void{
        console.log(`${this.username}上传了一个附件:${file}`);
        
    }
}
let vip1 = new VIP(1,'likun',32)
vip1.postArticle('标题2','2')
vip1.postAttachment('1.png')
```

### 重写和重载

```javascript
class User1{
   
    constructor( public id : number,
        public username : string)
        {
        
        console.log('这是构造函数，对类中成员属性进行初始化赋值');
    
    }
    postArticle(title:string,content:string){
        console.log(`${this.username}发表了一篇文章:${title}`);
        
    }

}
class VIP extends User1{
    constructor(
        id:number,
        username:string,
        public score:number
    ){
        super(id,username);
        //必须在super调用后才能访问this
        console.log("子类构造函数");
    }
    /*重新实现了父类的方法：参数的个数和参数的类型一致  */
    postArticle(title:string,content:string){
        this.score++;
        console.log(`${this.username}发表了一篇文章:${title},积分:${this.score}`)
    }
    postAttachment(file:string):void{
        console.log(`${this.username}上传了一个附件:${file}`);
        
    }
}
let vip1 = new VIP(1,'likun',32)
vip1.postArticle('标题2','2')
vip1.postAttachment('1.png')
// console.log(vip1);

```

### 重载

```javascript
class User1{
   
    constructor( public id : number,
        public username : string)
        {
        
        console.log('这是构造函数，对类中成员属性进行初始化赋值');
    
    }
    postArticle(title:string,content:string){
        console.log(`${this.username}发表了一篇文章:${title}`);
        
    }

}
class VIP extends User1{
    constructor(
        id:number,
        username:string,
        public score:number
    ){
        super(id,username);
        //必须在super调用后才能访问this
        console.log("子类构造函数");
    }
   //重载
    postArticle(title:string,content:string):void;
    postArticle(title:string,content:string,file:string):void;
    postArticle(title:string,content:string,file?:string){
        super.postArticle(title,content);
        if(file){
            this.postAttachment(file)
        }
    };
    
    postAttachment(file:string):void{
        console.log(`${this.username}上传了一个附件:${file}`);
        
    }
}
let vip1 = new VIP(1,'likun',32)
vip1.postArticle('标题2','2')
vip1.postAttachment('1.png')
// console.log(vip1);

```

## 修饰符

有的时候，我们希望对类成员(属性、方法)进行一定的访问控制，来保证数据的安全，通过类xiushfu可以做到这一点，目前TS提供了四种修饰符：

+ public :公有，默认
+ protected：受保护
+ private:私有
+ readonly:只读

public 修饰符

这个是类成员的默认修饰符，它的访问级别为：自身，子类，类外

protected：自身，子类

private:自身

## 静态成员

前面我们说到的是成员属性和方法都是实例对象的，但是有的时候，我们需要给类本身添加成员，区分某成员是静态还是实例的：

+ 该成员属性或方法是类型的特征还是实例化对象的特征
+ 如果一个成员方法中没有使用或依赖this，那么该方法就是静态的

## 抽象类

有的时候，一个父类的一些方法无法确定具体的行为，而是由继承的子类去实现，

# 模块化

commonjs  : module.exports = {}   - > require  不能直接适用于浏览器端（基于文件系统，同步加载）

AMD:requireJS库

```javascript
//b.js
define(function() {
    let a = 1;
    let b = 2;
    //return 导出 也可以通过module.exports 导出  同时在function种function(require,exports,module)
    //module.exports = {
    	//x:a,
        //y:b
//}
    return {
        x: a,
        y: b
    }
})
//a.js
//define(['./b'], function(require,exports,module) {
    //let b =require('./b')
	//console.lob(b)
//})
define(['./b'], function(b) {
    console.log(b);
})
//1.html
<script src="./libs/require.mini.js" data-main="./js/a.js"></script>
```

UMD

```javascript
(function(root, factory) {
    if (typeof module === "object" && typeof module.exports === "object") {
        module.exports = factory();
    } else if (typeof define === "function" && define.amd) {
        define(factory);
    } else {
        root.kkb = factory;
    }
})(this, function() {
    let a = 1;
    let b = 2;
    return {
        x: a,
        y: b
    }
})
```

ESM  export - import

```javascript
//b.js
let a = 1;
let b = 1;

export var x = a;
export var y = b;
export default a + b;
//a.js
import v, { x, y } from './b.js'
//a1.js 动态导入
//动态导入
document.onclick = function() {
    import ('./b.js').then(data => {
        // console.log(data);
        console.log(data.default, data.x, data.y);
    })
}
console.log(x, y);
console.log(v);
```

# 装饰器

## 配置

```javascript
{
    "compilerOptions": {
        "outDir": "./dist",
        "target": "ES6",
        "watch": true,
        "strictNullChecks": true,
        "noImplicitAny": true,
        "experimentalDecorators": true
    },
    "include": "./src/**/*"
}
```

装饰器是一个函数，它可以通过@装饰器函数这种特殊的语法附加在类、方法、访问符、属性、参数上，对它们进行包装，然后返回一个包装后的目标对象(类、方法、访问符、属性、参数)，装饰器工作在类的构建阶段，而不是使用阶段

```javascript
function log(target:any,name:string,descriptor:PropertyDescriptor){
    /* 
        target:当前被装饰的方法所属的类
        name:被装饰的方法的名称
        descriptor:描述符
    */
//    console.log(target,name,descriptor)
//把原始的方法提取出来
let fn =descriptor.value;

descriptor.value = function(a:number,b:number){
    // console.log('这是新的方法')
      let result =   fn(a,b);
        console.log("日志",{
            name,a,b,result
        })
        return result;
}

}



class M{
    @log
    static add(a:number,b:number){
        return a+b;
    }
    @log
    static sub(a:number,b:number){
        return a-b;
    }
}
let v1 = M.add(1,2);
console.log(v1);
let v3 = M.sub(1,2);
console.log(v3);

```



## 类装饰器

目标

+ 应用于类的构造函数

参数

+ 第一个参数(也只有一个参数)
  - 类的构造函数作为其唯一的参数

## 方法装饰器

目标

+ 应用于累的方法上

参数

+ 第一个参数

## 属性装饰器

目标

+ 应用于属性上

参数

+ 第一个参数
  + 静态方法：类的构造函数
  + 实例方法：类的原型对象
+ 第二个参数
  + 属性名称

## 访问器装饰器

目标

+ 应用于类的访问器上（getter，setter）上

参数

+ 第一个参数
  + 静态方法：类的构造函数
  + 实例方法：类的原型对象
+ 第二个参数
  + 属性名称
+ 第三个参数
  + 方法描述对象

## 参数装饰器

目标

+ 应用在参数上

参数

+ 第一个参数
  + 静态方法：类的构造函数
  + 实例方法：类的原型对象
+ 第二个参数
  + 方法名称
+ 第三个参数
  + 参数在函数参数列表中的索引

```javascript
function d1(target:Function){
    console.log(typeof target,target)
}
function d2(target:any,name:string){
    console.log(typeof target,name);
}
function d3(target:any,name:string,descriptor:PropertyDescriptor){
    console.log(typeof target,name,descriptor);
    
}
function d4(target:any,name:string,descriptor:PropertyDescriptor){
    console.log(typeof target,name,descriptor);
}
function d5(target:any,name:string,index:number){
    //name:当前参数所在的方法
    console.log(typeof target,name,index);
}
@d1
class MyClass{

    @d2
    a:number;
    @d2
    static property1:number;
    @d3
    get b(){
        return 1
    }
    @d3
    static get c(){
        return 2;
    }
    @d4
    public method1(@d5 x:number,@d5 y:number){
        //
    }
    @d4
    public static method2(){

    }
}
```



## 装饰器执行顺序

实例装饰器

+ 属性=》访问符=》参数=》方法

静态装饰器

+ 属性=》访问符=》参数=》方法

类

+ 类