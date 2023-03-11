# Redux-React 学习笔记（二）

上一篇对 `Redux` 思想的实现基于纯 `Javascript`，使用 `Reducer` 的API为我们提供了便利

一个完整的流程是

  * 定义 `action` 或 `action 创建函数`
  * 根据 `action` 编写 `reducer`
  * 将 `reducer` 合并传入 `store`

下面以 `TodoList App`为例

# 定义Action

`Action` 本质上是 `JavaScript` 普通对象，`action` 内必须使用一个字符串类型的 `type `字段来表示将要执行的动作

```javascript
{
  type: 'ADD_TODO',
  text: 'Build my first Redux app'
}
{
  type: 'SET_VISIBILITY_FILTER',
  filter: 'SHOW_ALL'
}
{ 
  type: 'TOGGLE_TODO',
  index: 1 
}
```

`action创建函数` 是返回值为 `action` 的函数，上述示例等价于如

```javascript
const addTodo = text => {
  return {
    type: 'ADD_TODO',
    text
  }
}
const setVisibilityFilter = filter => {
  return {
    type: 'SET_VISIBILITY_FILTER',
    filter
  }
}
const toggleTodo = id => {
  return {
    type: 'TOGGLE_TODO',
    id
  }
}
```

通过 `store.dispatch()` 将 `action` 传到 `store`。

`Redux` 中只需把 `action创建函数` 的结果传给 `dispatch()` 方法即可发起一次 `dispatch` 过程

```javascript
store.dispatch(addTodo(text))
```

# 编写Reducer

`Reducer` 只是一些纯函数，它接收先前的 `state` 和 `action`，并返回新的 `state`。

```javascript
(previousState, action) => newState
```

模型的更新逻辑全部集中于一个特定的层，即 `Reducer`

根据上面定义的 `action`，我们编写如下的 `reducer`
```javascript
const visibilityFilter = (state = 'SHOW_ALL', action) => {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return action.filter
    default:
      return state
  }
}
const todos = (state = [], action) => {
  switch (action.type) {
    case 'ADD_TODO':
      return [
        ...state,
        {
          text: action.text,
          completed: false
        }
      ]
    case 'TOGGLE_TODO':
        return state.map(todo =>
          (todo.id === action.id) 
            ? {...todo, completed: !todo.completed}
            : todo
        )
      default:
        return state
    default:
      return state
  }
}
```

然后将它们合并

```javascript
const todoApp = (state = {}, action) => {
  return {
    visibilityFilter: visibilityFilter(state.visibilityFilter, action),
    todos: todos(state.todos, action)
  }
}
```

或者直接用 `redux` 的 `combineReducers` 合并

```javascript
const todoApp = combineReducers({
  todos,
  visibilityFilter
})
```

# Store

一个 `Redux` 应用只有一个单一的 `store`，它用于维护应用的 `state`

`store` 的创建方式为 `createStore`，第一个参数为一个 `reducer`

```javascript
import { createStore } from 'redux'
let store = createStore(todoApp)
```

`store` 提供以下 `API`

  * `getState()` 获取 `state tree`
  * `dispatch(action)` 更新 `state`
  * `subscribe(listener)` 注册监听器，返回一个用于注销监听器的函数

我们可以直接对 `store` 操作

```javascript
store.dispatch(addTodo('Learn about actions'))
store.dispatch(toggleTodo(0))
store.dispatch(setVisibilityFilter(VisibilityFilters.SHOW_COMPLETED))
```

通过 `getState()` 查看 `state tree`

```javascript
console.log(store.getState())
```

# Redux 中的数据流

* 调用 `store.dispatch(action)`
* `store` 调用传入的 `reducer`，将当前的 `state` 和 `action` 传入 `reducer`
* `reducer` 执行并将返回的结果合并为一个 `state tree`
* `store` 保存 `state tree`