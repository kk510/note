

# ES6

## 浅拷贝concat()和slice()

```javascript
var arr = ['old', 1, true, null, undefined];
var new_arr = arr.concat();
new_arr[0] = 'new';
console.log(arr) // ["old", 1, true, null, undefined]
console.log(new_arr) // ["new", 1, true, null, undefined]
```

## 深拷贝

```javascript
var new_arr = JSON.parse( JSON.stringify(arr) );//不能拷贝函数
```

