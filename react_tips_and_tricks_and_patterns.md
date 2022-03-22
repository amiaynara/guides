###1. Rely on React instead of your own method calls
We can leave it as it is to reduce the time effort expending in this ticket but in future this methods should be replaced for a react component.
Then we can relay in React flow to reduce the call of the method and render the component only when necessary.
```
// We have a class component
class UserModal extends Component {
  // lifeCycle methods
  render() {
    return (

    )
  }

  /** Method to return a form as JSX
    *
    *
   **/
   _getForm() {
    return (
      <!-- Some html or react UI component tree that creates and handles form -->
    )
   }
}
```
**NOTE:** In the above code example we can make the JSX present in `_getForm` another React component, instead and rely on React to
optimally call the render the form whenever needed instead of relying on the `render` method of the `UserModal` class.

###2. 
