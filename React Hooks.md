# React Hooks

## React Hooks要解决的问题：**状态共享**

解决状态共享目前有三种方法：
1. render-props：原理：将函数作为props传给父组件，父组件中的状态共享，通过参数传给函数，实现渲染。

2. higher-order components

3. react hooks

HOC和react hooks的缺点：回调地狱，当依赖了多个需要复用的状态逻辑时，多层嵌套

### React hooks特性:
1. 多个状态不会产生嵌套，写法还是平铺的

2. 允许函数组件使用state和部分生命周期

3. 更容易将组件的UI与状态分离


## useState

useState的用法主要有以下3种：
1. useState接收一个初始值，返回一个数组，数组里面分别是当前值和修改这个值的方法（类似state和setState）

2. useState接收一个函数，返回一个数组

3. setCount可以接收新值，也可以接收一个返回新值的函数

### useState和class state的区别：

虽然函数组件也有了state，但是function state和class state还是有一些差异：
1. function state的粒度更细，class state过于无脑

2. function state保存的是快照，class state保存的是最新值

3. 引用类型的情况下，class state不需要传入新的引用，而function state必须保证是个新的引用

## UseEffect

UseEffect常用于一些副作用操作，在一定程度上可以充当`componentDidMount、componentDidUpdate、componentWillUnmount`这三个生命周期。

UseEffect的用法4种：
1. UseEffect接受两个参数，分别是回调函数、依赖数组

2. 如果依赖数组为空数组，那么回调函数会在第一次渲染结束后（componentDidMount）执行，返回的函数会在组件卸载时（componentWillUnmount）执行。

3. 如果不传依赖数组，那么回调函数会在每一次渲染结束后（componentDidMount 和 componentDidUpdate）执行。

4. 如果依赖数组不为空数组，那么回调函数会在依赖值每次更新渲染结束后（componentDidUpdate）执行，这个依赖值一般是 state 或者 props。
```
function App() {
    useEffect(() => {
        // effect componentDidMount
        const handleScroll = () => {}
        window.addEventListener('scroll', handleScroll)
        return () => {
        // cleanup 组件卸载前执行 componentWillUnmount
        window.removeEventListener('scroll', handleScroll)
    }}, []) // 参数为空数组，只会在第一次渲染结束后执行，返回的函数在组件卸载时执行
    useEffect(() => {
        console.log('每次渲染结束都会执行');
    })
    useEffect(() => {
        console.log('只有在count变化结束后才执行');
    }, [count])
}
```
### UseEffect的作用：

1. 代替部分生命周期

2. 更加reactive，例如mobx的reaction和vue的watch

3. 从命令式变成声明式，只需要关注依赖数据，不需要关注应该在哪一层做某些操作

4. 通过useEffect和useState可以编写一系列自定义的Hook

**函数式组件的Capture Value特性，状态只会跟某次渲染有关。**

## UseCallback

用UseCallback指定依赖项。在无关更新之后，通过 useCallback 取的还是上一次缓存起来的函数。
## useContext

React16中的新版Context 常常有一个提供数据的生产者（Provider），和一个消费数据的消费者（Consumer），我们需要通过Consumer来以render proprs的形式获取数据。如果从祖先组件传来了多个provider，那最终就又陷入了render props嵌套地狱
useContext允许我们以扁平化的形式获取到Context数据。即使有多个祖先组件使用多个Context.Provider传值，我们也可以扁平化获取到每一个Context数据。


## 扩展链接：

* React Hooks 原理与最佳实践：https://mp.weixin.qq.com/s/DS2OjlwWjClboDIgLFuG6A
* 精读《Reack Hooks》：https://zhuanlan.zhihu.com/p/49408348
* React Hooks实战体会：https://www.cnblogs.com/rock-roll/p/11002093.html

