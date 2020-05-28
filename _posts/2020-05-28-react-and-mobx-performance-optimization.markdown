---
title: "React and MobX: Performance Optimization"
layout: post
date: 2020-05-28 19:35
headerImage: false
tag:
    - reactjs
    - mobx
    - performance
    - front-end
category: blog
author: flo
description: "Best practices and quick wins when optimizing a React app that uses MobX for state management."
---

---

## React and MobX: Performance Optimization

Lately, I encountered some performance issues while working on a React application that uses MobX for state management. In the following, I will draw your attention to some quick wins as well as the best practices you should consider to achieve a sound performance baseline.

### List renders - use small, dedicated components and unique ids as key

React is well known at how bad rendering of extensive collections can end up. The reason is that the initializer of the render has to evaluate every component produced by a collection on each collection change. The recommendation is to use small, dedicated components that are responsible for one thing to render. Not only but mainly with lists, try to avoid using a global index so that any two items would have different ids.

**Bad:**

```react
interface IMyComponentProps {
    presenter: MyPresenter;
}

export const MyComponent =
    observer <
    IMyComponentProps >
    (({ presenter }) => {
        const { todos, user } = presenter;

        return (
            <div>
                {user.name}
                <ul>
                    {todos.map((todo: ITodo, index: number) => (
                        <TodoView todo={todo} key={index} />
                    ))}
                </ul>
            </div>
        );
    });
```

To be clear. It won't re-render the Todos. However, React has to calculate if it is necessary to update the DOM and that the reconciliation process is often expensive itself.

**Good:**

```react
interface IMyComponentProps {
  presenter: MyPresenter
}

export const MyComponent = observer<IMyComponentProps>(({ presenter }) => {
  const { todos, user } = presenter

  return (
    <div>
      {user.name}
      <TodosView todos={todos} />
    </div>
  )
})

interface ITodo {
  id: number
  title: string
}

interface ITodosViewProps {
  todos: ITodo[]
}

export const TodosView = observer<ITodosViewProps>((props: ITodosView) => {
  const { todos } = props
  const uuid = uuidv4

  return (
    <ul>
      {todos.map((todo: ITodo) => (
        <TodoView todo={todo} key={todo.id} />
      ))}
    </ul>
  )
})

interface ITodoProps {
  todo: ITodo
  key: string
}

export const TodoView = (props: ITodoProps) => {
  const { todo, key } = props

  return <li key={key}>{todo.title}</li>
}

```

### Dereference values as late as possible

The common misconecption of MobX is that MobX tracks the values of an observable. In reality, MobX only tracks property access, not values. Hence, values are not observable. Let's have a look at the following example:

**Bad:**

```react
<DisplayTitle title={todo.title} />
```

**Good:**

```react
<DisplayTitle todo={todo} />
```

Changes in the `title` property in the first example will trigger a re-render of the parent component as the dereferencing is happening as part of the same parent component. Whereas in the second, only the `DisplayTitle` component will re-render as it deals with the dereferencing further down the component tree.

Then, you may use the component like this:

```react
render() {
  const { todo } = this.props;
  return (
    <>
    <TodoView getTitle={() => todo.title} />
    <TodoView getTitle={todo.getTitle} />
    <TodoView getTitle={this.getTodoTitle} />
    </>
  );
}

getTodoTitle = () => this.props.todo.title;

...
class Todo {
  @observable title
  getTitle = () => this.title
}
```

There are different ways how and where we can place a functions:

-   **getTitle={() => todo.title}**: create the function in the render method as part of the properties
-   **todo.getTitle**: assign the function to a functionHandler and place it in the component (as with getManufacturerNameTracked), or
-   **this.getTodoTitle**: create a function on the object that contains the data (as with getModelTracked).

The next section will explain why the placement of a function is important.

### Bind functions early

Binding functions early will allow you to avoid cretaing new closures in the render method and further re-renders of components.

Arrow functions as well binding a function in the render method will always result in creating a new closure/function each time the components renders. Consider the following examples:

**Bad:**

```react
handleClick() {
  console.log('Hello world!')
}

render() {
  return <Button onClick={this.handleClick()} />
}


render() {
  return <Button onClick={() => { console.log('Hello World!') }} />
}

render() {
  return <Button onClick={this.handleClick.bind(this)} />
}
```

**Good:**

```react
handleClick = () => {
  console.log('Hello world!')
}

render() {
  return <Button onClick={this.handleClick} />
}
```

By doing `this.handelClick(),` we are calling handleClick function instead of passing it as a reference. Creating the function directly in the render function will always result in the **shouldComponentUpdate** - used in the Button component - to yield false. Further, you pass a new function each time, so the parent has to re-rendered. The same applies to bind a function directly. You are ending up getting a different function with the same name but new closure every time. Further, the component will re-render as component strict identity comparison yields false.

### Conclusion

All examples and best practices mentioned in this are not React specific but
iff you're experiencing performance issues, try this:

-   **List renders** - use small, dedicated components and unique ids as key to avoiding misleading DOM keys and nested renders.
-   **Dereference values** as late as possible to avoid parent component renders
-   **Bind functions early** to avoid triggering React's `shouldComponentUpdate`

You may find the above quick wins very helpful as they follow the Pareto principle, 80% output for 20% input. Remember to carefully balance each performance action to avoid going down the premature performance optimization road.

#### Resources/References

-   [MobX - Optimizing rendering React components](https://mobx.js.org/best/react-performance.html)
-   [MobX - What does MobX react to?](https://mobx.js.org/best/react.html)
-   [ReactJS - Passing Functions to Components](https://reactjs.org/docs/faq-functions.html)
-   [ReactJS - Optimizing Performance](https://reactjs.org/docs/optimizing-performance.html)
