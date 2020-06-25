# useCallback (性能优化)

useCallback和useMemo很像,不过是useMemo返回的是值,useCallback返回的是函数
```
const [query, setQuery] = useState(1)
const [num, setNum] = useState(1)
const getData = () => {
  console.log('我要发起http请求咯',此时)
  ajax(url: `https://api.baidu.com/quert=${query}`) // 伪代码
}
useEffect(() => {
  //　进入页面后我就要发起一次
  getData()
}, [getData])

return (<>
  <p>{num}-{query}</p>
  <button onClick={() => setQuery(query + 1)}>setQuery</button>
  <button onClick={() => setNum(num + 1)} >setNum</button>
</>)
```
刚进入页面发起了一次请求,正常业务是点击setQuery继续发起请求,是正常的．但是当点击setNum还发起了请求,这是我们不想要(只要修改状态组件就会从新执行,不论点击那个按钮都会从新执行)


```
const [query, setQuery] = useState(1)
const [num, setNum] = useState(1)
const getData = useCallback(() => {
  console.log('我要发起http请求咯')
  ajax(url: `https://api.baidu.com/quert=${query}`) // 伪代码
}, [query])
useEffect(() => {
  //　进入页面后我就要发起一次
  getData()
}, [getData])

return (<>
  <p>{num}-{query}</p>
  <button onClick={() => setQuery(query + 1)}>setQuery</button>
  <button onClick={() => setNum(num + 1)} >setNum</button>
</>)
```
此时我们点击setNum就不会发起请求了,因为我们用useCallback把getData函数缓存了,点击setQuery发起请求是因为我们useCallback依赖了quert的值,他就是告诉react,当quert改变了，他在更新，不改变就不更新

