# React.js v16 Keynotes
## 1. Error Boundaries
Use `componentDidCatch()` to catch the errors of the component. For example:
```javascript
componentDidCatch(error, info) {
  this.setState({ ...state, hasError: true});
  logErrorToMyService(error, info);
}

render() {
  if (this.state.hasError) {
     return <h6>There is an error in this component.</h6>
  } else {
     return <MY_COMPONENT></MY_COMPONENT>
  }
}
```
- The best practice is to create a `<ErrorBoundary>` component and wrap it around the other components.
- Note that error boundaries only catch errors in the components below them in the tree. 
- As of React 16, errors that were not caught by any error boundary will result in unmounting of the whole React component tree.
- Error boundaries do not catch errors inside event handlers. If you need to catch an error inside event handler, use the regular JavaScript try / catch statement.

## 2. Render multiple elements
You can render multiple elements without a wrapper component now:
```javascript
const list = [
  <li key='item1'>Apple</li>,
  <li key='item2'>Banana</li>,
  <li key='item3'>Pineapple</li>,
]
```

## 3. Pure text component
In React v16, you don't have to wrap your text component with a `<span>` or `<p>` if they are not necessary. Just return text will work.

## 4. Render Elements Outside the Current React 
It is possible to render an external component from React code with `ReactDOM.createPortal`:
```javascript
const newComponent = <div>Welcome to React v16!</div>
ReactDOM.createPortal(
  newComponent,
  document.querySelector('body')
);
```
This is useful when creating some components such as modal, dialog, alert etc.



###### Video source: [https://egghead.io/courses/leverage-new-features-of-react-16](https://egghead.io/courses/leverage-new-features-of-react-16)
