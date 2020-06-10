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
<button onClick={() => {setCount(prevCount => prevCount++)}}>点击+1</button>
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