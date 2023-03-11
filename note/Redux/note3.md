# Redux-React学习笔记（三）

# React-Redux

## Provider
`<Provider store>` 使组件层级中的 `connect()` 方法都能够获得 `Redux store`，一般来说，使用方法如下

```javascript
ReactDOM.render(
  <Provider store={store}>
    <RootComponent />
  </Provider>,
  document.getElementById('root')
)
```

## connect

连接 `React` 组件与 `Redux store`，返回一个新的已与 `Redux store` 连接的组件类。

```javascript
connect([mapStateToProps], [mapDispatchToProps], [mergeProps], [options])
```
* `[mapStateToProps(state, [ownProps]): stateProps] (Function)` 
  
  如果定义该参数，组件将会监听 `Redux store` 的变化，只要 `Redux store` 发生改变，`mapStateToProps` 函数就会被调用，如果指定了该回调函数中的第二个参数 `ownProps`，则该参数的值为传递到组件的 props，而且只要组件接收到新的 `props`，`mapStateToProps` 也会被调用

* `[mapDispatchToProps(dispatch, [ownProps]): dispatchProps] (Object or Function)`

    如果传递的是一个对象，那么每个定义在该对象的函数都将被当作 `Redux action creator`，对象所定义的方法名将作为属性名；每个方法将返回一个新的函数，函数中 `dispatch` 方法会将 `action creator` 的返回值作为参数执行。这些属性会被合并到组件的 `props` 中。

    如果传递的是一个函数，该函数将接收一个 `dispatch` 函数，然后由你来决定如何返回一个对象，这个对象通过 `dispatch` 函数与 `action creator` 如果省略这个 `mapDispatchToProps` 参数，默认情况下，`dispatch` 会注入到组件的 `props` 中。

## Examples

* 只注入 `dispatch`，不监听 `store`
```js
export default connect()(TodoApp)
```

* 注入 `dispatch` 和 `state` 中的某个值
```js
function mapStateToProps(state) {
  return { x: state.x }
}

export default connect(mapStateToProps)(TodoApp)
```