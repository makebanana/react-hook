# useReducer

当我们想更新一个状态，并且这个状态更新依赖于另一个状态的值时，我们可能需要使用useReducer去替换它们   
const [state, dispatch] = useReducer(reducer, initialState)   
const [状态,　动作] = useReducer(声明的动作, 初始化值)

```
import React, { useReducer, useEffect } from 'react'
const initialState = {
  count: 0,
  step: 1
}

const reducer = (state, action) => {
  const { count, step } = state
  if (action.type === 'tick') {
    return { count: count + step, step }
  } else if (action.type === 'step') {
    return { count, step: action.step }
  } else {
    throw new Error()
  }
}

const Counter = () => {
  const [state, dispatch] = useReducer(reducer, initialState)
  const { count, step } = state
  useEffect(() => {
    const id = setInterval(() => {
      dispatch({ type: 'tick' })
    }, 1000)
    return () => {
      console.log('测试：修改状态我是否会被执行')
      clearInterval(id)
    }
  }, [dispatch])
  return (<>
    <h1>{count}</h1>
    <input value={step} onChange={e => {
      dispatch({
        type: 'step',
        step: Number(e.target.value)
      })
    }} />
  </>)
}
// React 会确保 dispatch 函数的标识是稳定的，并且不会在组件重新渲染时改变
```