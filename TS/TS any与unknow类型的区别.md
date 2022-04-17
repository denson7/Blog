## any VS unknown
```javascript
functiontest1(callback:any) {
 callback();
}
test1(1);// throws "TypeError: callback is not a function"


functiontest2(callback:unknown) {
 // callback();
 // This expression is not callable.  Type '{}' has no call signatures.
 if(typeofcallback==='function') {// 在使用一个数据类型是unknown时，需要对变量先进行类型检查，然后才能进行使用
   callback()
  }
}
test2(1);

// 相同点：
// 都可以将任意类型的数据赋值给any或unknown类型
// 不同点：
// unknown提供了更安全的类型,如果想对unknown类型的数据进行操作，必须使用类型断言或缩小到一个特定的类型。而any则没有此判断，推荐使用unknown类型
```


