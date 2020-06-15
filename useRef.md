# useRef

本应该讲完 useState　接着将　useEffect的,但上章讲解的可以很好的理解 useRef  
先看上章代码

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
先点击一次 增加 count，然后再紧接着点击一次 减少 count,  
因为react的 Capture Value 特性会先弹出来 count:1 然后弹出来 count:0 因为react的 Capture Value 特性  

那我们如果想在3s后获取此时此刻count的值,而不是3s前点击count的值?  
官方给出的解决方案是 useRef
```
const countRef = useRef(0)
useEffect(()=>{
  setTimeout(() => {
    alert('count: ' + countRef.current);
  }, 10000)
}, [])

return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => {countRef.current = 1000}}>
        增加 count
      </button>
    </div>
  )

```

这样　在刷新页面的后　快读点击　增加 count 那么弹出来是　count:1000  
(进入页面useEffect只执行一次,因为他的第二个参数是[],快速点击需要在useEffect里面的setTimeout延迟的10秒内点击,在10秒延迟未结束之前不论对countRef.current改变成什么样子,10秒到后,他总是弹出的是你最近改变的那个值)

```
const countRef = useRef(0)

console.log(countRef.current) // 0

countRef.current = 1000
console.log(countRef.current) // 1000
```