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
const list = () => [
  <li key='item1'>Apple</li>,
  <li key='item2'>Banana</li>,
  <li key='item3'>Pineapple</li>,
];
...
render() {
  return (
    <ul>
      {list}
    </ul>
  );
}
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

## 5. Changes in DOM attributes 
- `class` is now available in React v16
- Since React 16, you now can provide custom attributes to DOM components, like a div. This wasn't the case in React 15, as it used to ignore unknown DOM attributes.
- Valid values for attributes are strings. Passing numbers will call `toString()` to convert them into strings.
- Attributes staring with "on" are not passed through, as this could become a potential security hole.

## 6. Avoid rendering the same value in `setState()`
In React v16, you can update the `setState()` to check if the value has been changed to avoid re-render the component:
```javascript
this.setState(state => {
  if (state.value === newValue) {
    return null;
  }
  return {
    value: newValue
  };
});
```
This code above simply return null for `setState()` when the value doesn't change. This prevents an update being triggered.

## 7. Fragments 
Fragments let you group a list of children without adding extra nodes to the DOM. 
```javascript
render() {
  return (
    <React.Fragment>
      <ChildA />
      <ChildB />
      <ChildC />
    </React.Fragment>
  );
}
```
- When using Fragment, no DOM element is rendered, and behaves as if your work return an array of elements. One definite benefit here is that you can omit the keys.
- You can use `<></>` the same way you’d use any other element except that it doesn’t support keys or attributes.

## 8. Forwarding Refs
Ref forwarding is an opt-in feature that lets some components take a ref they receive, and pass it further down (in other words, “forward” it) to a child.

In the example below, FancyButton uses React.forwardRef to obtain the ref passed to it, and then forward it to the DOM button that it renders:
```javascript
const FancyButton = React.forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));

// You can now get a ref directly to the DOM button:
const ref = React.createRef();
<FancyButton ref={ref}>Click me!</FancyButton>;
```
This way, components using FancyButton can get a ref to the underlying button DOM node and access it if necessary—just like if they used a DOM button directly. When the ref is attached, `ref.current` will point to the <button> DOM node.

- You can't use `createRef()` on functional components because they don't have instances and it will stay as `null`
- Ref forwarding is not limited to DOM components. You can forward refs to class component instances, too.
- When you start using `forwardRef()` in a component library, you should treat it as a breaking change and release a new major version of your library. 
- `forwardRef()` can be used in HOC in the following way:
    ```javascript
    function logProps(Component) {
      class LogProps extends React.Component {
        componentDidUpdate(prevProps) {
          console.log('old props:', prevProps);
          console.log('new props:', this.props);
        }

        render() {
            const {forwardedRef, ...rest} = this.props;

          // Assign the custom prop "forwardedRef" as a ref
          return <Component ref={forwardedRef} {...rest} />;
        }
      }

      // Note the second param "ref" provided by React.forwardRef.
      // We can pass it along to LogProps as a regular prop, e.g. "forwardedRef"
      // And it can then be attached to the Component.
      return React.forwardRef((props, ref) => {
        return <LogProps {...props} forwardedRef={ref} />;
      });
    }
    ```
## 9. Use `getDerivedStateFromProps()` to update the component
`getDerivedStateFromProps()` is lifecycle hook introduced with React 16.3 and intended as a replacement for `componentWillReceiveProps()`. It is invoked after a component is instantiated as well as when it receives new props. It should return an object to update state, or null to indicate that the new props do not require any state updates.
- It's a static function that receives two parameters, `nextProps` and `prevState`
- You can use `if (prevState.value !== nextProps.value)` to compare the props value and do updates accordingly
- It is called right after the component is instantiated, as well as when it receives new props.
- It is a static function, you won't have access to the component instance using `this`.

## 10. Use `getSnapshotBeforeUpdate()` to capture values
`getSnapshotBeforeUpdate()` is a lifecycle hook that was introduced with React 16.3. It is invoked right before the most recently rendered output is committed and the value returned by it will be passed as a third parameter to componentDidUpdate. It enables your component to capture current values for example a scroll position before they are potentially changed.

The following is an example of a chatbox window. By calling `getSnapshotBeforeUpdate()` will automatically scroll the window to the bottom when it receives a new message:
```javascript
getSnapshotBeforeUpdate(prevProps, prevState) {
  const wrapper = this.wrapperRef.current;
  return wrapper.scrollTop + wrapper.offsetHeight >= wrapper.scrollHeight;
}

componentDidUpdate(prevProps, prevState, snapshot) {
  if (snapshot) {
    this.wrapperRef.current.scrollTop = this.wrapperRef.current.scrollHeight;
  }
}
```

- It receives prevProps and prevState
- `getSnapshotBeforeUpdate()` is invoked right before the most recently rendered output is committed to the DOM

###### Video source: [https://egghead.io/courses/leverage-new-features-of-react-16](https://egghead.io/courses/leverage-new-features-of-react-16)
