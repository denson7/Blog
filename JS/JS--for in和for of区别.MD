##for in和for of区别

##for in
使用for in 也可以遍历数组，但是会存在以下问题：

```

1.index索引为字符串型数字，不能直接进行几何运算

2.遍历顺序有可能不是按照实际数组的内部顺序

3.使用for in会遍历数组所有的可枚举属性，包括原型。例如上栗的原型方法method和name属性

```

所以for in更适合`遍历对象`，可以获取对象的key和value。

```

Object.prototype.method = function(){
  console.log(this);
}

const  myObject = { a: 1, b: 2, c: 3 }
for (let key in myObject) {
  console.log(key); // a b c method
}
```

此外，for in 还可以遍历到myObject的`原型方法`method，如果不想遍历原型方法和属性的话，可以在循环内部判断一下，使用`hasOwnPropery`方法可以判断某属性是否是该对象的实例属性。

```


for (let key in myObject) {
  if(myObject.hasOwnProperty(key)){
    console.log(key); // a b c 
  }
}
```

##for of

与for in 不同的是，for in遍历的是数组的索引（即键名），而for of遍历的是数组元素值。所以如果是遍历数组，使用for of 会更好一些。

```

Array.prototype.method = function(){
  console.log(this.length);
}
const myArray = [1, 2, 4, 5, 6, 7]
myArray.name = "数组";
for (let value of myArray) {
  console.log(value); // 1 2 4 5 6 7
}
```

注意：for of遍历的只是数组内的元素，而不包括数组的原型属性method和索引name。