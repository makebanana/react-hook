# useState

useState API 用于 state　变量定义　　
```
const [count, setCount] = useState(0)
// [state变量, 修改state变量的方法] = useState(state变量的默认值)
```
以上代码定义了一个名叫 count　的 state变量,默认值为0  
****
修改conut值为1
```
setCount(1)
```

如果新的state需要通过之前的state计算得出,可以将函数传递给 setCount.该函数将接收先前的state,并返回一个更新后的值,
```
<button onClick={() => {setCount(prevCount => prevCount+1)}}>点击+1</button>
```

如果初始state需要通过复杂计算,可以直接传入一个函数，在函数中计算并返回初始的 state,此函数只在初始渲染时被调用

```
const [state, setState] = useState(() => {
  const a = 1 + 1 + 1
  const b = a * 2
  return b * 10
})
// state初始化值为60
```

需要注意的是 const 声明的是常量, 常量是不会变的, useState 里面是不存在 Proxy 之类的双向绑定,只是一个常量存在于每次 Render 中,每次 Render，const 声明的常量值都会被固化为 1、2、3

```
const [num, setNum] = useState(1)

const log = () => {
  setTimeout(() => {
    console.log(num) // 打印 1
  }, 3000)
}

return <div onClick={() => {
  log()
  setNum(3)
}}>
  click
</div>
```

解释一下为什么打印1, 在 log 函数执行的那个 Render 过程里，num 的值可以看作常量 1，执行 setNum(3) 时会交由一个全新的 Render 渲染，所以不会执行 log 函数。而 3 秒后执行的内容是由 num 为 1 的那个 Render 发出的，所以结果自然为 1.  
这个特性是官方特地设置的机制，叫　`Capture Value`; 官方的意思是　防备因react 以为 props 或 state没有变动引发的bug,


```
  const [count, setCount] = useState(0)

  useEffect(()=>{
    setTimeout(() => {
      alert('count: ' + count);
    }, 3000)
  }, [count])

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        增加 count
      </button>
      <button onClick={() => setCount(count - 1)}>
        减少 count
      </button>
    </div>
  )
```

先点击一次 增加 count，然后再紧接着点击一次 减少 count，会先弹出来 count:1 然后弹出来 count:0  
通过上面示例代码可以很好理解每次 Render 的内容都会形成一个快照并保留下来，因此当状态变更而 Rerender 时，就形成了 N 个 Render 状态，而每个 Render 状态都拥有自己固定不变的 Props 与 State  
