[TOC]
## 自定义Hook

自定义Hook本质上只是一种函数代码逻辑的抽取，并不算React的特性。
自定义Hook与普通函数没有其他区别，除了以use开头以外，此外，如果一个函数以use开头，那么它就是一个自定义hook，就可以在函数内部使用hook其他函数。

```react
import React, { useState, useEffect, useRef, useCallback } from "react";

function Counter(props) {
  const size = useSize();
  return (
    <div>
      <p>{props.count}</p>
      <p>
        Counter-Size:{size.width}X{size.height}
      </p>
    </div>
  );
}

function useCounter(count) {
  return <div>{count}</div>;
}

// 自定义Hooks函数,以use开头
function useCount(defaultCount) {
  const [count, setCount] = useState(defaultCount);
  const it = useRef();

  useEffect(() => {
    it.current = setInterval(() => {
      setCount((count) => count + 1);
    }, 1000);
  }, []);

  useEffect(() => {
    if (count > 10) {
      clearInterval(it.current);
    }
  });
  return [count, setCount];
}

// 自定义hooks函数useSize
function useSize() {
  const [size, setSize] = useState({
    width: document.documentElement.clientWidth,
    height: document.documentElement.clientHeight
  });

  const onResize = useCallback(() => {
    setSize({
      width: document.documentElement.clientWidth,
      height: document.documentElement.clientHeight
    });
  });

  useEffect(() => {
    window.addEventListener("resize", onResize, false);
    return () => {
      window.removeEventListener("resize", onResize, false);
    };
  }, [onResize]);

  return size;
}

function App(props) {
  // const [count, setCount] = useState(() => {
  //   // 延迟初始化
  //   return props.count || 0;
  // });
  const [count, setCount] = useCount(0);
  const Counter2 = useCounter(count);
  const size = useSize();
  return (
    <React.Fragment>
      <button onClick={() => setCount(count + 1)}>Click</button>
      <span>{count}</span>
      <Counter count={count}/>
      {Counter2}
      <p>
        App-Size: {size.width}X{size.height}
      </p>
    </React.Fragment>
  );
}

export default App;
```