# useImperativeHandle
官方说`useImperativeHandle` 可以让你在使用 `ref` 时自定义暴露给父组件的实例值,其实一句话就是父组件拿子组件值   
`useImperativeHandle(ref, createHandle, [deps])`   
* ref：定义 current 对象的 ref createHandle：一个函数，返回值是一个对象，即这个 ref 的 current
* 对象 [deps]：即依赖列表，当监听的依赖发生变化，useImperativeHandle 才会重新将子组件的值输出到父组件,
```
const Children1 = React.forwardRef((props, ref) => {
  const [count, setCount] = useState(0)
  useImperativeHandle(ref, () => ({
    getCount: () => count
  }), [count])
   return (<>
    <p>{count}</p>
    <button onClick={() => {setCount(count + 1)}}>add</button>
  </>)
})

export default () => {
  const [ count, setCount ] = useState(0)
  const countRef = useRef();
  const click = () => {
    console.log(countRef.current.getCount())
  }
  return (<>
    <Children1 ref={countRef} />
    <button onClick={() => {click()}}>获取子组件值</button>
  </>)
}
```
当点击　获取子组件值　按钮时,控制台可以正常打印出子组件`count`的值
