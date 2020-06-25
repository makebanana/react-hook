# useMemo　(性能优化)

由于react有Capture Value机制,组件状态修改后,整个组件都会从新去执行   
当我们某些函数(组件)返回的值没有依赖更改的状态是不需要从新渲染的,这个时候就需要useMemo

```
const testUseMemo = (num) => {
  console.log('testUseMemo:我是否渲染了？')
  return num + 1
}

const ChilrenComponent = ({num, num1, result}) => {
  console.log('ChilrenComponent:我是否渲染了？')
  return (
    <div>{num}-{num1}-{result}</div>
  )
}

export default () => {
  const [num, setNum] = useState(1)
  const [num1, setNum1] = useState(1)
  const result = testUseMemo(num)
  return (
    <>
      <ChilrenComponent num={num} num1={num1} result={result} />
      <button onClick={() => setNum(num + 1)} >click num</button>
      <button onClick={() => setNum1(num1 + 1)} >click num1</button>
    </>
  )
}
```
* 1 当我们进入页面控制台会输出`testUseMemo:我是否渲染了？`和`ChilrenComponent:我是否渲染了？`   (正常)   

* 2 当我们点击`click num`控制台输出`testUseMemo:我是否渲染了？`和`ChilrenComponent:我是否渲染了？`　(正常)

* 3 当我们点击`click num1`控制台输出`testUseMemo:我是否渲染了？`和`ChilrenComponent:我是否渲染了？`　(不正常)   
上面1肯定是正常的,因为刚进入页面开始渲染,所以控制台都会输出   
第2步,也正常因为`testUseMemo`函数里面用到了`num`这个状态   
第3步还输出了`ChilrenComponent:我是否渲染了？`就有点诡异了,我们点击`click num1`这个操作,这个操作并不需要执行`testUseMemo`函数,但是他却执行了.这是我们不愿意看到的，所以我们需要`useMemo`让它不要执行

```
const testUseMemo = (num) => {
  console.log('testUseMemo:我是否渲染了？')
  return num + 1
}

const ChilrenComponent = ({num, num1, result}) => {
  console.log('ChilrenComponent:我是否渲染了？')
  return (
    <div>{num}-{num1}-{result}</div>
  )
}

export default () => {
  const [num, setNum] = useState(1)
  const [num1, setNum1] = useState(1)
  const result = useMemo(() => testUseMemo(num), [num])
  return (
    <>
      <ChilrenComponent num={num} num1={num1} result={result} />
      <button onClick={() => setNum(num + 1)} >click num</button>
      <button onClick={() => setNum1(num1 + 1)} >click num1</button>
    </>
  )
}
```
这样当我们再次点击click num1按钮,就不会执行`testUseMemo`函数,控制台也不会输出`ChilrenComponent:我是否渲染了？`了   
上面是函数案例,我们下面用一个组件案例
```
const ChilrenComponent = ({num1}) => {
  console.log('我是否渲染了？')
  return (
    <div>num1的值是 {num1}</div>
  )
}
export default () => {
  const [num, setNum] = useState(1)
  const [num1, setNum1] = useState(1)
  return (
    <>
      <div>num的值是 {num}</div>
      <ChilrenComponent num1={num1} />
      <button onClick={() => setNum(num + 1)} >click num</button>
      <button onClick={() => setNum1(num1 + 1)} >click num1</button>
    </>
  )
}
```
当这样写，点击`click num`控制台也会输出 `我是否渲染了？`我们修改一下

```
const ChilrenComponent = ({num1}) => {
  console.log('我是否渲染了？')
  return (
    <div>num1的值是 {num1}</div>
  )
}
export default () => {
  const [num, setNum] = useState(1)
  const [num1, setNum1] = useState(1)
  const UseMemoChilrenComponent = useMemo(() => <ChilrenComponent num1={num1} />, [num1])
  return (
    <>
      <div>num的值是 {num}</div>
      {UseMemoChilrenComponent}
      <button onClick={() => setNum(num + 1)} >click num</button>
      <button onClick={() => setNum1(num1 + 1)} >click num1</button>
    </>
  )
}
```

当这样写，点击`click num`控制台就不会输出 `我是否渲染了？`