# useLayoutEffect
和`useEffect`很像，唯一的区别就是执行时机不一样   
`useEffect`异步,`useLayoutEffect`是同步   
`useEffect`不会阻塞`UI`界面加载,`useLayoutEffect`会阻塞`UI`界面加载
```
const [ count, setCount ] = useState(0)
  useEffect(() => {
    console.log('useEffect执行')
    alert('useEffect alert')
    return () => {
      console.log('useEffect销毁')
    }
  }, [count])
  useLayoutEffect(() => {
    console.log('useLayoutEffect执行')
    alert('useLayoutEffect alert')
    return () => {
      console.log('useLayoutEffect销毁')
    }
  },[count])
  return (<>
    <p>{count}</p>
    {console.log('ui执行')}
    <button onClick={() => setCount(count + 1)}>add</button>
  </>)
```
上面的执行顺序是
* 1 控制台输出　ui执行
* 2 控制台输出　useLayoutEffect执行
* 3 页面弹出　useLayoutEffect　alert
当点击了　useLayoutEffect　alert
* 4 页面渲染 (useLayoutEffect alert刚才阻塞了ui渲染，因为他和ui执行是同步的)
* 5 控制台输出　useEffect执行
* 6 页面弹出　useEffect　alert   
点击alert 结束   
如果我们点击一下页面　add看一下执行顺序   
* 1 控制台输出　ui执行
* 2 控制台输出　useLayoutEffect销毁
* 3 控制台输出　useLayoutEffect执行
* 4 页面弹出　useLayoutEffect　alert
当点击了　useLayoutEffect　alert
* 5 页面渲染(页面上面的count从0变成了1)
* 6 控制台输出　useEffect销毁
* 7 控制台输出　useEffect执行   
点击alert 结束   