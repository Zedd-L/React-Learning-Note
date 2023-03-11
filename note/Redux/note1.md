# Redux-React学习笔记（一）

# 状态(state)

在一个js应用中，我们需要管理不同的状态，在一个组件中，通常用 `react` 的 `useState` 便可以初始化不同的状态，然而当状态越来越多，不同页面之间的参数传递使管理它们变成了一件十分困难的事。

# Redux的思想

`redux` 将整个应用的 `state` 被储存在一棵 `object tree` 中，并且这个 `object tree` 只存在于唯一一个 `store` 中。
```javascript
{
  状态1: 状态值
  状态2: 状态值
  ........
}
```
这些状态值可以是任何变量，对象，函数

在redux的文档中，给出了一个 `TodoList App` 的示例，它的 `state` 如下

```javascript
{
  todos: [
    {
      text: 'Eat food',
      completed: true
    }, 
    {
      text: 'Exercise',
      completed: false
    }],
  visibilityFilter: 'SHOW_COMPLETED'
}
```

在 `redux` 的思想里，想要更新 `state` 中的数据，只能通过发起 `action`，这里的 `action` 只是一些普通的 `JS对象`，它们描述了状态是怎么改变的。

```javascript
{ type: 'ADD_TODO', text: 'Go to swimming pool' }
{ type: 'TOGGLE_TODO', index: 1 }
{ type: 'SET_VISIBILITY_FILTER', filter: 'SHOW_ALL' }
```

# Reducer
有了 `state` 和 `action`，我们还需要一个函数用于接收它们，在函数内部根据 `action` 的描述进行一些处理，并返回一个新的 `state`，这个函数叫做 `reducer`

比如想要修改 `state tree` 中的 `visibilityFilter`，传入的 `action` 为
```javascript
{ type: 'SET_VISIBILITY_FILTER', filter: 'SHOW_ALL' }
```
`reducer` 函数可能是这样的

```javascript
function visibilityFilter(state = 'SHOW_ALL', action) {
  if (action.type === 'SET_VISIBILITY_FILTER') {
    return action.filter;
  } else {
    return state;
  }
}
```
再为修改 `todos` 定义一个 `reducer`
```javascript
function todos(state = [], action) {
  switch (action.type) {
  case 'ADD_TODO':
    return state.concat([{ text: action.text, completed: false }]);
  case 'TOGGLE_TODO':
    return state.map((todo, index) =>
      action.index === index ?
        { text: todo.text, completed: !todo.completed } :
        todo
   )
  default:
    return state;
  }
}
```
可以看到，我们规定了 `function visibilityFilter()`的返回值类型一定为 `state tree` 中 `visibilityFilter` 的值，`function todos()` 的值一定为一个列表，也就是`state tree` 中 `todos` 的值

最后，我们用一个函数将这两个函数调用，这相当于两个小 `reducer`合并成一个大的 `reducer`。
```javascript
function todoApp(state = {}, action) {
  return {
    todos: todos(state.todos, action),
    visibilityFilter: visibilityFilter(state.visibilityFilter, action)
  };
}
```
这个 `todoApp` 根据 `action` 对象来更新 `state`，并将新的 `state` 返回。