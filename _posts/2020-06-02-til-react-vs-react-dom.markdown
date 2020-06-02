---
title: "TIL - React vs ReactDOM"
layout: post
date: 2020-06-02 22:08
headerImage: false
tag:
    - reactjs
    - react
    - react-dom
    - front-end
category: blog
author: flo
description: "Today I Learned (TIL) - Why we need to import two packages (react/react-dom) to get started with React and what the main differences of them are."
---

---

## TIL - React vs ReactDOM

Have you ever wondered why we need to import two packages to get started with React? The reason is that the library to create your user interface components and elements is separate from the implementation itself. So what does that mean?

### React

React is a JavaScript library for building user interfaces.[1] React exposes the API to the toolbox that allows you to define and create, e.g., a component. Available tools are:

-   `React.createElement`
-   `React.createClass`
-   `React.Component`
-   `React.Children` among others.

### ReactDOM

ReactDOM deals with the implementation of React and knows where and how to mount a React element into the DOM. Some of the API you can use are:

-   `ReactDOM.render()`
-   `ReactDOM.hydrate()`
-   `ReactDOM.unmountComponentAtNode()`
-   `ReactDOM.createPortal()`
-   and the mystic `ReactDOM.findDOMNode()`

### Conclusion

The decoupling happened with the release of [React 0.14](https://reactjs.org/blog/2015/07/03/react-v0.14-beta-1.html#two-packages) to make life easier for developers who want to use a different implementation of React in their project.

For now, remember to run the same versions of React and ReacDOM in your project to avoid any errors.

#### Resources/References

-   [ReactJS - Two Packages](https://reactjs.org/blog/2015/07/03/react-v0.14-beta-1.html#two-packages)

[1]: https://reactjs.org/docs/getting-started.html
