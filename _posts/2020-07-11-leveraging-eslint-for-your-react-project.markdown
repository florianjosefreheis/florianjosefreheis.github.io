---
title: "Leveraging ESLint for your React project"
layout: post
date: 2020-07-11 10:14
headerImage: false
tag:
    - react
    - react.js
    - linting
    - eslint
    - eslint-plugin-react
    - eslint-plugin-react-hooks
    - eslint-plugin-jsx-a11y
    - coding standards
    - code quality
    - cognitive load
    - front-end
category: blog
author: flo
description: Leveraging ESLint for your React project
---

---

## Leveraging ESLint for your React project

JavaScript is a dynamic and flexible language. However, one trade-off is that this freedom allows syntax errors, problematic patterns, and wrong set variables (like global variables) to slip into your code without realizing it. All of this happens because you need to run the code, whereas compiled languages will give you immediate feedback.

Wikipedia gives us a hint where linting originated from:

> lint, or a linter, is a tool that analyzes source code to flag programming errors, bugs, stylistic errors, and suspicious constructs. The term originates from a Unix utility that examined C language source code.
> -- <cite>[Wikipedia][1]

To lint your code falls in the category of white-box testing as the [internal structure/design/implementation of the item being tested is known][2] to the Software Engineer.
Linting tools are powerful, available for various programming languages, and well established in today's Software Development processes. In short, linting means:

-   **Performance improvements**
-   **Pre-code review**
-   **Readability improvement**
-   **Expose (syntax) errors before execution**
-   **Define common standards/style guides**
-   **Reduce cognitive load as you don't need to worry about where a semicolon belongs to**
-   **Guarantee consistency across internal projects/teams**

Today we want to take a closer look at how we can leverage ESLint for React projects.

### Adapting ESLint for your React project

Following, I want to highlight three plugins from above that will generate instant quick wins for your development process besides increasing the user experience.

#### eslint-plugin-react

The es-lint-plugin-react package provides you React specific linting rules like

-   **react/no-access-state-in-setstate:** Reports when this.state is accessed within setState
-   **react/no-render-return-value:** Prevent usage of the return value of React.render
-   **react/no-did-mount-set-state:** Prevent usage of setState in componentDidMount
-   **react/no-direct-mutation-state:** Prevent direct mutation of this.state
-   **react/jsx-no-script-url:** Forbid `javascript:` URLs
-   **react/jsx-no-bind:** Prevents usage of Function.prototype.bind and arrow functions in React component props

The last rule in the above list is essential to be one step closer to a performant React app. As mentioned in [React and MobX: Performance Optimization][2], binding functions early will allow you to avoid creating new closures in the render method and further re-renders of components.

#### eslint-plugin-react-hooks

Hooks are a relevant new addition to React (16.8). Now you don't need a class to use state or other React features.

Let's take a close look at the rules of this ESLint package.

##### react-hooks/rules-of-hooks

One important thing beforehand is that React relies on the order in which Hooks are called.

-   Don't use Hoosk inside a condition
-   Call Hooks at the top level of our components
-   Call hooks only from React function components
-   Prefix custom hooks with `use` to align with naming conventions
-   Calling hooks from custom hooks is ok

##### react-hooks/exhaustive-deps

-   Declare functions needed by a hook inside of it
-   Add all used variables to the dependency array

#### eslint-plugin-jsx-a11y

The accessibility of websites and apps is one of the most overlook areas these days. Helpers like ESLint packages or Google Chrome Lighthouse reports help to identify and solve existing gaps.

One great advantage of adding this package to your codebase is that it reminds you adding arias roles as much as an alt text for your images or not using `javascript:void` in your anchor tags. Every single element on the web has that follows accessibility guidelines like W3C contributes to an open and inclusive space.

### Fin

Don't worry if your linter feels a bit overwhelmed after adding those plugins. In my experience, they contribute to a sound foundation of your codebase and your process. Every team needs to find its sweet spot as much linting, and too many active rules define limitations in the wrong place.

Checkout the reference to the office docuemnation below for more details.

### References

-   [ESLint](https://eslint.org/)
-   [React Hooks - Performance Optimizations](https://reactjs.org/docs/hooks-faq.html#performance-optimizations)
-   [React - Rules of Hooks](https://reactjs.org/docs/hooks-rules.html)
-   [eslint-plugin-react](https://github.com/yannickcr/eslint-plugin-react)
-   [eslint-plugin-react-hooks](https://www.npmjs.com/package/eslint-plugin-react-hooks)
-   [eslint-plugin-jsx-a11y](https://github.com/jsx-eslint/eslint-plugin-jsx-a11y#readme)

[1]: https://en.wikipedia.org/wiki/Lint_(software)
[2]: https://qa-platforms.com/what-is-white-box-testing/
[3]: https://florianjosefreheis.com/blog/react-and-mobx-performance-optimization/
