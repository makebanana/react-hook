# 什么是Hook?

Hook 是 React 16.8 的新增特性。它可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性.  
Hook 可以解决在 class 中组件逻辑在生命周期函数内分散导致的逻辑混乱.  
Hook 可以解决在 class 中 this 的复杂问题.



```
import React, { useState } from 'react'

function counter () {
  //　声明变量count　初始值为0
  const [count, setCount] = useState(0)

  return (
    <p>count当前的值为: {count}</p>
    <button onClick={() => {setCount(count++)}}>点击+1</button>
    <button onClick={() => {setCount(count--)}}>点击-1</button>
  )
}

```

### [useState](./useState.md)
### [useRef](./useRef.md)
### [useEffect](./useEffect.md)
### [useReducer](./useReducer.md)
### [useMemo](./useMemo.md)
### [useCallback](./useCallback.md)
### [useContext](./useContext.md)
### [useLayoutEffect](./useLayoutEffect.md)
### [useImperativeHandle](./useImperativeHandle.md)

