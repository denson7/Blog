[TOC]
## useLayoutEffect
useLayoutEffect和useEffect非常相似，只有一点不同：
1.useEffect会在渲染的内容更新到DOM上`后`执行，不会阻塞DOM的更新。
2.useLayoutEffect则会在渲染的内容更新到DOM上`之前`执行，会阻塞DOM的更新。
如果我们希望在某些操作发生之后再更新DOM，那么可以使用useLayoutEffect。

```react
import React, {useLayoutEffect, useState} from "react"

export default function UseLayoutEffectDemo() {
    const [count, setCount] = useState(10)

    useLayoutEffect(() => {
        //这时使用useEffect会渲染两次，页面会有一个闪烁，所以使用useLayoutEffect
        if (count === 0) {
            setCount(Math.random)
        }
    }, [count])

    return (
        <div>
            <h3>当前的值： {count}</h3>
            <button onClick={e => setCount(0)}>修改数字</button>
        </div>
    )
}

```

