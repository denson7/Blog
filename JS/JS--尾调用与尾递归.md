## 尾调用
`尾调用（Tail Call）`是函数式编程的一个重要概念，它本身非常简单，一句话就能说清楚。就是指：`某个函数的最后一步是调用另一个函数`。
```javascript
function g(x) {
  console.log(x);
}
function f(x) {
  return g(x);
}
console.log(f(1));
// 上面代码中，函数 f 的最后一步是调用函数 g，这就是尾调用。尾调用不一定出现在函数尾部，只要是最后一步操作即可。
```

## 尾递归
函数调用自身，称为`递归`。如果尾调用自身，就称为尾递归。
递归非常耗费内存，因为需要同时保存成千上百个调用帧，很容易发生栈溢出错误。但是对尾递归来说，由于只存在一个调用帧，所以永远不会发生栈溢出错误。
```javascript
function factorial(n) {
  if (n === 1) {
    return 1
  }
  return n * factorial(n - 1)
}
// 上面代码是一个阶乘函数，计算 n 的阶乘，最多需要保存 n 个调用数据，复杂度为 O（n），如果改写成尾调用，只保留一个调用记录，复杂度为 O（1）。
function factor(n, total=1) {
  if (n === 1) {
    return total
  }
  return factor(n - 1, n * total)
}
function Fibonacci(n) {
  if (n <= 1) {
    return 1;
  }
  return Fibonacci(n - 1) + Fibonacci(n - 2);
}

//尾递归
function Fibona(n, ac1 = 1, ac2 = 1) {
  if (n <= 1) {
    return ac2;
  }
  return Fibona(n - 1, ac2, ac1 + ac2);
}
```
