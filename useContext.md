# useContext
在react应用中,数据是通过`props`属性从上到下进行传递,当父组件A,引用了子组件B,B组件又引用了子组件C,那么子组件C想用父组件A的某值传递起来是非常繁琐的．`Context` 提供了一种在组件之间共享值的方式   
每个 `Context` 对象都会返回一个 `Provider React` 组件，它允许消费组件订阅 `context` 的变化
```
import React, {useState, useContext, createContext} from 'react'
// createContext 方法创建一个 Context 对象
const AddCountContext = createContext({})
// 子组件
const Children1 = () => {
  // 取父(或父父父）的值,只要引入了AddCountContext就行)
  const { count } = useContext(AddCountContext);
  return <div>{count}</div>
}
// 父组件
export default () => {
  const [count, setCount] = useState(0)
  return (
    // 用Context对象返回的组件包裹子组件，将要传递给子组件的值放在value中
    <AddCountContext.Provider value={{count}}>
      <Children1 />
      <button onClick={() => setCount(count + 1)}>add</button>
    </AddCountContext.Provider>
  )
}
```
通过上面demo可以学习到,只要父组件用`Context`包裹并且`value`值有传递状态,那么子组件就可以通过引入