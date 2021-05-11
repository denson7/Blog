[TOC]
## forwardRef回顾
```react
import React, {forwardRef, useRef} from "react"

const ChildInput = forwardRef((props, ref) => {
    return <input ref={ref} type="text"/>
})

export default function forwardRefDemo() {
    //定义一个useRef
    const inputRef = useRef()
    return (
        <div>
            <ChildInput ref={inputRef}/>
            <button onClick={e => inputRef.current.focus()}>聚焦</button>
        </div>
    )
}
```
上述方法对于父组件来说，可以获取到子组件的Dom，就可以对子组件进行任何操作，这样的话，对外暴露的权限太大了，使用useImpreativeHandle可以做一些限制。
```react
import React, {forwardRef, useImperativeHandle, useRef} from "react"

const ChildInput = forwardRef((props, ref) => {
    const inputRef = useRef()
    useImperativeHandle(ref, () => ({
        // 这里设置想要对外暴露的方法
        focus: () => {
            inputRef.current.focus();
        }
    }), [inputRef.current])

    return <input ref={inputRef} type="text"/>
})

export default function UseImperativeHandleDemo() {
    //定义一个useRef
    const inputRef = useRef()

    return (
        <div>
            <ChildInput ref={inputRef}/>
            <button onClick={e => inputRef.current.focus()}>聚焦</button>
        </div>
    )
}
```

