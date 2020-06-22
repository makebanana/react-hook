# useEffect

react 官方介绍 useState 可以让你在函数组件中执行副作用操作  
每次 Render 都有自己的 Effect

```
const [count, setCount] = useState(0)

useEffect(() => {
  document.title = `你点击了 ${count} 次`;
})

return (<>
  <p>你点击了 {count} 次</p>
  <button onClick={() => { setCount(count + 1) }}>click</button>
</>)

```
上面的 useEffect 在每次 Render 过程中，拿到的 count 都是固化下来的常量

### 回收机制

在组件被销毁时，通过 useEffect 注册的监听需要被销毁，这一点可以通过 useEffect 的返回值做到,

```
useEffect(() => {
  ChatAPI.subscribeToFriendStatus(props.id, handleStatusChange)
  return () => {
    // 这个函数只有在组件被销毁 才调用
    ChatAPI.unsubscribeFromFriendStatus(props.id, handleStatusChange)
  };
})
```
在组件被销毁时，会执行返回值函数内回调函数。
在 react class中,一般是在 componentDidMount 中设置订阅，并在 componentWillUnmount 中清除它．在hook中　一个useEffect即可搞定,并且将 订阅和清楚写在了一起   

为什么要在 effect 中返回一个函数？ 这是 effect 可选的清除机制。每个 effect 都可以返回一个清除函数。如此可以将添加和移除订阅的逻辑放在一起。它们都属于 effect 的一部分。  
React 何时清除 effect？ React 会在组件卸载的时候执行清除操作。正如之前学到的，effect 在每次渲染的时候都会执行。这就是为什么 React 会在执行当前 effect 之前对上一个 effect 进行清除。   
一个组件可以写多个 useEffect  

   

### Effect进阶
```
const [count, setCount] = useState(0)
useEffect(() => {
  setTimeout(() => {
    console.log(`你点击了 ${count} 次`)
  }, 3000)
})
return (
  <div>
    <p>你点击了 {count} 次</p>
    <button onClick={() => setCount(count + 1)}>
      Click
    </button>
  </div>
)
```
点击三次按钮分析整个过程组件进行了四次渲染:
* 1初始化 render0：打印 你点击了 0 次
* 2修改 count 值为1，render1：打印 你点击了 1 次
* 3修改count 值为2，render2：打印 你点击了 2 次
* 4修改count值为3，render3：打印 你点击了 3 次   

通过整个例子我们可以知道，在每次渲染中，useEffect 也是独立的   
```
// 我们把上面代码的
useEffect(() => {
  setTimeout(() => {
    console.log(`你点击了 ${count} 次`)
  }, 3000)
})
// 修改为
useEffect(() => {
  console.log(`你点击了 ${count} 次`)
  return () => {
    console.log('销毁')
  }
})
```
点击按钮 3 次，控制台中打印的结果如下

* 1你点击了 0 次
* 2销毁
* 3你点击了 1 次
* 4销毁
* 5你点击了 2 次
* 6销毁
* 7你点击了 3 次   


从上面代码总结effect 是在重新渲染时被清除的.   
假设现在有render0和render1两次渲染:
* 1react渲染render1的ui
* 2浏览器绘制,呈现render1的ui
* 3render清楚render0的effect
* 4react运行render1的effect   

`react只会在浏览器绘制后运行effect,这使得应用更流畅,因为effect并不会阻塞屏幕更新`   
```
const [count, setCount] = useState(0)
useEffect(() => {
  setCount(99)
  console.log(count)
  return() => {
    console.log('销毁')
  }
})
console.log('我肯定最先执行！')
return (
  <div>
    <p>你点击了 {count} 次</p>
    <button onClick={() => setCount(count + 1)}>
      Click me
    </button>
  </div>
)

```   
运行上面代码(此时组件状态为初始化render0)

* 1 我肯定最先执行！
* 2 0
* 3 我肯定最先执行！
* 4 销毁
* 5 99
* 6 我肯定最先执行！   

查看打印结果,我们得出
* 1 React 渲染初始化的UI（render0）
* 2 执行 useEffect
* 3 调用 setCount 方法修改 count 值为 99，组件重新渲染（render1）
* 4 根据执行顺序，继续执行 render0 状态下的 useEffect，打印 count 的值为 0。（render0 下 count 值为0
* 5 React 重新渲染UI（render1
* 6 执行 render0 的 useEffect 的清除函数
* 7 执行 render1 的useEffect
* 8 调用 setCount 方法修改 count 值为 99（由于传入的值没有改变，所以组件没有重新渲染）
* 9 打印 count 的值为 99   
其中 我肯定最先执行 可以看到组件被调用了

### 设置依赖
在实际应用中,我们不需要每次组件更新时,都去执行useEffect,这个时候我们可以给useEffect设置依赖来告诉react什么时候去执行useEffect   
```
useEffect(() => {
  document.title = 'Hello, ' + name
}, [name])
// 这个例子,当name发送改变才会执行useEffect
```

### 正确地设置依赖
```
const [count, setCount] = useState(0)

useEffect(() => {
  const id = setInterval(() => {
    setCount(count + 1)
}, 1000)
  return () => clearInterval(id)
}, [])
```
我们只希望设置一次 setInterval 定时器，所以将依赖设置为了 []，但是由于组件每次渲染拥有独立的 state 和 effects，所以上面代码中的 count 值，一直是 0，当一次执行完 setCount后，后续的setCount操作都是无效的。   
那既然这样，我们可以在依赖里面添加依赖 count就可以解决问题了吧？思路是正确的，但是这样就违背了我们 “我们只希望 setInterval 执行一次” 的初衷，且很可能造成一些不必要的bug。
解决方案：使用函数式更新(useState章节有讲)
```
const [count, setCount] = useState(0)

useEffect(() => {
  const id = setInterval(() => {
    setCount(preCount => preCount + 1)
}, 1000)
  return () => clearInterval(id)
}, [])
```

当我们定时器依赖别的数据时候

```
const [count, setCount] = useState(0)
const [step, setStep] = useState(1)
useEffect(() => {
  const id = setInterval(() => {
    setCount(c => c + step)
  }, 1000);
  return () => clearInterval(id)
}, [step])

return (
  <>
    <h1>{count}</h1>
    <input value={step} onChange={e => setStep(Number(e.target.value))} />
  </>
)

```

当我们在修改 step 变量时，会重新设置定时器。这是我们不愿意看到的，那应该怎么去优化呢？这个时候我们就需要用到useReducer了。