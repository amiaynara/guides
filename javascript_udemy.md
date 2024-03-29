### JavaScript

* Syntax parser
* Lexical Environment: Where the code is sitting, in which part of the overall code. 
* Execution context: Which part of our code is being run.
```
Conceptual Aside: Objects in JS is just a Name: value pair, where value inturn could be name-value
And so on nesting can go on.
Think This when thinking about object : 
{
  name: 'amiay',
  friends: {
    close: ['rocks', 'biplav', 'raushan'],
    distant: ['nilanshi']
  }
}
```

> Global Context : Whenever a javascript code run by javascript engine, the engine creates a Global Context 
and a global Object(different in case of a browser and nodejs). In case of browsers, the global object is called ->
> Global Object: Objects that are not inside of a function is Global

Starter code to run javascript on Browerser: 
```
<!DOCTYPE html>
<html>
  <head>
  </head>
  <body>

  <script src="/path/to/javascript/file.js"></script>
  </body>
</html>
```

Now every code will be present in a javascript file. (path mentioned as 'src')

Let us write some code then. 

```
var a = 1;
```
Some notes about the above code: 
- The execution context(global) has on a variable -> 'a' in addition to window object.
- This (every global var or method) get attached to the global execution context.
- a or 'window.a' are same. 

# Execution Context and Hoisting
```
b(); // Should this work? Yes indeed it does work. 
console.log(a); // How about this? This works but the output is : undefined

function b() {
  console.log('The function b ran');
}
var a = 'Hello world';
b(); // should run as expected. since function call is after function definition. relatable when you are from 
    // c or c++
```
As explained online, they say that treat the function and variable definition to be 'physically' put 
at the top of the code. But this is not what is exactly happening. Otherwise, we would have the value of
'a' as 'Hello world' not **undefined**.
This can and should be explained as below:
Javascript code execution can be explained as a two way process: ***Creation Phase*** and ***Execution Phase***.
**Creation Phase:**
In this phase, the javascript engine sets up space for the functions and variables in the memory, so that the code
can be executed from there. In case of functions, they(the code in the function) are placed in the memory in its
entirety. However, for variables, a place holder (name ***undefined***) is put in the place, in the creation phase.
> Global obj + this + outer env link + setting up of space in the memory

**Execution Phase:**
Execution context runs the code.

Note: 
* What we write as a javascript code is not directly what is run by the computer, javascript engine takes our
and runs it for us, hence we the weird stories like above. Without the involvement of the javascript engine, acces-
ing the value of a variable would have caused an error similar to that in C or in C++.
* In JS, all variables, if not initialized, have the value ***undefined***. ***undefined*** has special meaning in JS.

# 'not defined' is not ***undefined***.
```
var a;
console.log(a) // undefined
if ( a === undefined) {
  console.log('undefined is a keyword in javascript');
}
```

# Single Threaded, synchronous 
Javascript is single thread(only one command at a time) and later code run later.

When a method present in the global context call another method, a new execution context is created and stacked upon
the global execution context and the execution of the global execution context stops for the moment. 

# Scope Chain
Have a look at the code below
```
function b() {
  console.log(myVar);
}
function a() {
  var myVar = 2;
  b();
}
var myVar = 1;
a();

```

```
1   // not 2 not undefined because the outer environment of b is global and for a is also global
```
Both the methods have Global execution context as the 'outer-environment'. Because the **lexical environment** 
of ***b*** is Global environment. since 'b' sits in global env physically. 

Note: in the code below, is present lexically inside the function a. Outer reference of b is a's execution 
context.
```
function a() {
  function b() {
    console.log(myVar);
  }
  var myVar = 2;
  b();
}
var myVar = 1;
a();

```
```
2 // not undefined not 1 bcs the b function sits inside of a's execution context 
```
So the bottom line is : if a variable is called in an execution context then it looks in the outer-env 
reference and keeps on looking until it goes up to the global execution environment. 

Types:
*Javascript is dynamically typed.*
This means that you don't tell while coding which type of data you are assigning to a variable. Javascript
engine figure this about while executing the code.
```
  bool isNew = 'hellow'; // error, this variable can only be assigned a boolean

  // in JS
  var isNew = 'hellow';
  isNew = 3;
  isNew = true;   // are all valid
```
***what implications does it have on performance***

Primitives:
- undefined : leave it for the engine 
- null : this is for you
- boolean : true false
- number : floating point number
- string : '', ""
- symbol : future support by ES6

### Operators
These are just special form of functions. 

```
  var c = 4 + 3;
  // is equivalent to 
  function +(a, b) {  // note that symbol cannot be valid function name; but humour me for now...
    return // hardware implementation of addition two numbers
  }
```
***Precedence:***
sdfasdf

***Associativity:*** 
In what order do the operators functions get called in: left-to-right or right-to-left. Helps when we have two or more
operators of same precedence in the same line.
```
  var a = 2, b = 3, c = 4;
  a = b = c;    // assignment has 'right-to-left' associativity;
  console.log(a, b, c); // 4 4 4
```
*Q. what does '=' return?*
var c = 3, d = 5;
console.log(c = d); // returns 5, hence we have 5 as a log
Note: For associativity you have to know the (right to left or left-to-right) pattern. Ofcourse, you cannot memorize that, 
visit the MDN docs or some reliable source for this information when you encounter operator with same precedence
on the same line.

Coercion:
[Typecasting]
```
  var a = 1 + 2;  // 3
  var b = 'hel' + 'o'; // helo
  var c = 'hell' + 2; // hell2 // js engine coerced 2 as a string. 
```
Note that it was JS engine that did this coercion for you, but you did not explicitly did not tell it to do this. 
*Keep the below in mind*
```
  var c = 1 + +'2'; // 3
  var d = 1 + '2'; // 12
```

*Comparison Operator:* 
```
  console.log(1 < 2 < 3);
```
*Solution:*
- Look at the associativity [ you encountered more than one operators in the same line]
- '<' has 'left-to-right' associativity.
- true < 3
- 1 < 3
- true
- But for 3 < 2 < 1 -> false < 1 -> 0 < 1 -> true, hence the anomalous behaviour  

Note : Number(true) becomes ***1***, Number(false) becomes ***0***.
Therefore we saw a case where the coercion can lead to unexpected behaviour and some panic in the developer's
day-to-day life, but if you understand this. Then again, it is not that you will find the problem instantly, 
you will have a look at the problem, follow the process and then will be able to find the reason for the bug.
For example, 
Number(undefined) -> NaN
Number(null) -> 0
Number(false) -> 0
Number(true) -> 1
Again, ofcourse you cannot ***memorize*** all of this.
### ==
```
  var x = false;
  console.log(x == 0); // true, false is coerced to a 0. Hence 'true'. But this can lead to confusions. 
```
To avoid this confusion we have '===' : strict equality. Always use this.

```
function greet(name) {
  if (name) {
    console.log("how are you ", name);
  } else {
    console.log("no name given");
  }
}

function greetNeatTrick(name) {
  name = name || 'default name'; // Note: operators are functions that RETURN values!!! remember this
  console.log(`Hello, ${name} from greetNeatTrick`);

}
greet("amiay", "swati");
greetNeatTrick('sumit');
```

## Objects and functions
*Object and the dot*
As already mentioned when you think of object think this: 
```
  {
    key: value, // where value can be another object
  }
```
In a more descriptive way, objects can have *properties* and *methods*. 
- Properties : **primitives** and **nested objects** 
- Methods: javascript function sitting inside. 
So the Main object will be sitting in the computer memory and it will have references to the properties and
methods that have been defined inside of it.

Consider the following example: 
```
  var x; // is not an object  but 'undefined' type
  console.log(typeof x); // undefined
  var y = 3; // is a number
  console.log(typeof y); // number 

  var person = new Object(); // a better and simpler way is to just use '{}'
  person['firstName'] = 'amiay';
  person['lastName'] = 'narayan'; // [] is called 'computed member operator'

  console.log(person[fieldName], person['lastName]);
  // dot operator is a more convenient way and a faster way to type as well
  console.log(person.firsName, person.lastName);
  // dot operator can be similarly used to set the values of new property
  person.address.street = 'Naya tola chitranjan road'; // Now this would give error

  // Why the error? Since we just defined 'address', address property get allocated as 'undefined' type and 
  // we cannot use '.' operator on undefined type. That is why!

  // Therefore we need to do this first
  person.address = new Object(); // or {}
  // Now the below is valid
  person.address.street = 'Naya bazar pachna road';
  console.log(person.address);
  console.log(person.address.street);

```

Note : **dot operator has left-to-right associativity**. Important thing to mind. Dot is just another operator
        therefore must have an associativity, similarly, 'computed member access' also has associativity.

### Object literal
Objects can more neatly be created using `{}`. 

```
const person = {
  firstName: 'amiay',
  lastName: 'narayan',
  address: {
    street: 'I live here in this street',
    city: 'Noida',
  }
}
// Preferred way to create an objectA
```
*Note: An object can be created anywhere I want, on the fly. 
`sendObj({prop1: 'value1', prop2: 'value2'})`
We can see that there is a sort of freedom while creating and working with objects. This is because when the 
javascript engine compiles this code, they are all compiled to the same code.

## Namespaces
They are containers for various methods and variables. But javascript does not have the concept of namespaces.
Unline **C++**, where we use `using namespace std`. This is generally required to avoid mix and match of names
of variables and funtions. Allows same function names to be used in two different libraries. But due to the 
presence of object concept in javascript we don't actually need 'namespaces' here. Because here we can use
objects and then declare variables inside of these instead of creating them directly in the global environment.

```
var greetings = "hello";
var greetings = 'hola';
console.log(greetings); // hola

// to create avoid this confusion we have namespaces but in JS we can easily use the objects
var english = {
  greeting : 'hello',
}
var spanish = {
  greeting : 'hola',
}
console.log(english.greeting, 'is the english greeting');
console.log(spanish.greeting, 'is the spanish greeting');
```
**Note: JSON and object literals
```
var objectLiteral = {
  firstName: 'amiay',
  lastName: 'narayan',
} // valid object literal

{
  firstName: 'abhay',
  lastName: 'narayan',
} // valid obj literal but not a valid JSON
{
  "isCute" true,
  "name": "sindbad",
} // is a VALID JSON and a valid object literal syntax
```
***JSON is a subset of object literal syntax, JSON is more strict***

```
const objLiteral = {
  isFast: true,
  name: 'Amiay',
}

JSON.stringify(objLiteral); // obj => JSON string
JSON.parse({"name": "amiay", "isCute" : true}); // JSON string => obj
```
##First Class Functions
Everything that you can do with other types, you can do with these first class functions as well. 
Functions in JS, as you already know, are just another object that resides in the memory. 
Since functions are objects, there we can attach properties and methods to a function as well!!
so we can attach,
- a primitive
- an object
- other functions
- hidden special properties: 'NAME' (optional), CODE: that we add and is invocable. 

```
// just to prove a few things---> MUST try below

function greet() {
  console.log('hi hello, I am a function');
}
greet.language = 'hola'; // We are adding a primitive property to a function, to a function!!!!

console.log(greet); // function greet() { console.log('hi hello, I am a function');} // actually just logs the code
console.log(greet.language); // hola

```
Note: in the example above : 
The **NAME** property of the object(which is a function) is : greet
The **CODE** property of the object(which is a function) is : console.log('hi hello, I am a function');

## Function statements and Function expressions
**Expression**: A unit of code that returns a value
```
a = 3; // is an expresssion, as it return 3 (try it on chrome console), '=' is an operator afterall
1 + 2; // is an expression, returns 3

if ( x === true) { console.log('hhere '); } // this is a statement

function greet() {
  console.log('hola, how are you today');
} // is function statement, does NOT return a value. Just creates an object with NAME and CODE properties
// Also hoisting happens when this part of code is executed, note that the code is only run when we have 
// a function call ->  greet()

// function expression

var anonymousGreet = function () {
  console.log(' this is another function ');
} // function () {} .... creates an object we all know that by now, so this object(function) will exist in the 
// memory and the variable 'anonymousGreet' will have a reference to this function
```
Note: Another thing to keep in mind is : when a function is called before a a function statement definition, it is
totally fine, as the javascript engine 'hoists' it. But when a function is being defined using function EXPRESSION, 
we CANNOT call the method before, the expression, as this will still be 'undefined'. Refer execution phase(start of course).

**Another Cool Thing** that we can do with the `first class functions` is that we can pass them around as function arguments.
```
log(a) {
  a();
}

log(function () {
    console.log('HI this is me');
  }
); // An anonymous function (which is ofcourse an object) is passed as an argument
```

***This style of coding leads to entire new style of coding, functional programming***

###By Value and By reference
When dealing with primivites, the values are copied by value. What that means is: 
When storing a primitive value, the variable name points to an address in memory.
And when this variable is assigned to another variable then another memory location
is assigned and it stores a copy of original value. Look at the below example:
```
// Call by value(primitives)
let a = 3;
let b = a; // value itself is copied

b = 9;
console.log(a); // 3
console.log(b); // 9
```
However in case of objects, the values are assigned 'by reference'. 
```
let a = {'name': 'amiay'};
let b = a;
b.name = 'sumit';
console.log(b); // {'name': 'sumit'} <-- "Mutation"
console.log(a); // {'name': 'sumit'}
b.surname = 'sangakkara';
a.address = 'Purani Bazar, Sri Lanka';
console.log(b); // {'address': 'Purani Bazar, Sri Lanka', 'name': 'sumit', 'surname': sangakkara'}
console.log(a); // {'address': 'Purani Bazar, Sri Lanka', 'name': 'sumit', 'surname': sangakkara'}
```

NOTE: The receiver variable starts pointing to the original memory location(not a duplicate is created).
So when the value is changed in the newer one it is reflected in the older one, and also if the original
(older) object is modified the change is also seen in the new one. 
`
var c = {name : 'narayan'};
console.log(c); // {'name': 'narayan'}
var d = c;
c = {'greetings': 'hola'}'; // Creates a new space in the memory
console.log(c); // {'greetings': 'hola'}
console.log(d); // {name : 'narayan'}
// Also note that, the values are passed by reference in case of objects and 'by-value' in case of primitives
function testFunction(d){
  d.greetings = 'Hola';
  console.log(d.greetings);
}
var d = {'greetings': 'amiay'};
var c = d;
testFunction(d);
console.log(d);
console.log(d);
`

## 'this' object
```
console.log(this); // Window {} <-- the global window object that the javascript engine creates
function a(){
  console.log(this); // Window
  this.newVariable = 'hello'; // gets attached to the global object (Window)
}
function b(){
  console.log(this); // Window
  this.newVariable = 'holaa'; // mutate the global object (Window)
  function c() {
    console.log(this); // Window
  }
}

console.log(this.newVariable); // undefined
console.log(newVariable); // Uncaught Reference error: newVariable is not defined
a();
console.log(this.newVariable); // hello
console.log(newVariable) // hello

```

***Consider another code snippet, this will clarify the 'this' further***

```
var obj = {
  name : 'amiay',
  log : function () {
          console.log(this); // Object {name: 'amiay', log: function}
          this.name = 'Change the name'; // mutate the obj
        }
}
obj.log();
```
consider a similar code snippet

```
var obj = {
  name : 'amiay',
  log : function () {
          console.log(this); // Object {name: 'amiay', log: function}
          this.name = 'Change the name'; // mutate the obj
          var setname = function () {
            console.log(this);
            this.name = 'updated again';
          }
          setname();
          console.log(this); // We might expect the 'name' property on 'obj' object to get changed (but no,
          // a new property named 'name': 'updated again' gets added to the global object (Window) instead
        }
}
obj.log();
```
*You will note that a new property gets added to the Window object instead. Although we thought that inside
'setname' method we were setting the 'name' property of the 'obj' object. This, many people treat as a 'bug'
associated to javascript. They think 'this' inside of 'setname' method should also point to the 'obj', which
is a reasonable ask.*
- **Design Pattern**: It a solution to a repeated problem.
**A Design Pattern** to tackle this situation is described in the following code snippet. 
```
var obj = {
  name : 'amiay',
  log : function () {
          var self = this; // self will be pointing to the same location as the 'this' object
                            // in this case this points to the 'obj' in the memory, so will 'self'
          console.log(this); // Object {name: 'amiay', log: function}
          self.name = 'Change the name'; // mutate the obj
          var setname = function () {
            console.log(self);
            self.name = 'updated again';
          }
          setname();
          console.log(self); // we get {'name': 'updated again', 'log': function}
        }
}
obj.log();
```
**NOTE:** Every programming languages have their qwirks, and clever people try and come up with nice solutions/patterns
to solve situation.

## Arrays : Collection of anything
It is similar to `list` in python.
We can mix and match different types of objects/primitives and store them in a single array.

`var arr = [1, 2, 3]`
```
var arr = [1, arr, {'name': 'amiay'}, true, function(name) {console.log(name); var greet = 'hola';}]; // function expression here NOT statement
// we can call the function as follows
arr[4](arr[2].name);
```
## arguments
Like 'this' or 'arguments' is another variable that gets created with a function.
'arguments' is `array-like` variable that contains all the arguments (parameter values) passed to a function.
```
function greetings(firstName, lastName, language) {
  if (arguments.length === 0) {
    console.log('missing parameters');
    return;
  }
  console.log(firstName, ' is same as ', arguments[0]);
  console.log(lastName);
  console.log(language);

}
greetings(); // undefined undefined undefined  // <-- because of hoisting no error but undefined
greetings('amiay', 'narayan'); // amiay narayan undefined
```
**Aside**
- Note that the function call `greetings()` does not cause error, even though there is not function with
that same signature. We do have a function named `greetings` but that takes three parameters. In Languages,
like ***C++, java*** this would have been an error. But JavaScript is different, the variables that are not
passed get assigned to `undefined`. 
- `function greetings(firstName, lastName, language = 'en') {...}` is a way to assign defaults
- With time 'arguments' will get deprecated, and 'spread arguments' will be prevelant.
- **function overloading** is not available in javascript

#### Dangerous Aside!
Syntax parser in javascript engine, reads any keyword character by character and anticipates what the keyword
is. And in doing so it can leave the code as it is or it can even make necessary changes. For example, ';'
in JavaScript are optional because the syntax parser does it for us. This can lead to some *hard-to-find* 
bugs in the code if we are not careful. Take a look at the below code snippet.

```
function greetings(){
  console.log('hi there');
  return // syntax parser will add the ';' right here and we undefined will be passed
    {
      name: 'narayan'
    } // <- this object does not get passed
}
console.log(greetings()); // undefined
```
**White Spaces:**
***Carriage returns, tabs, spaces*** are used to make the code more readable and maintable and are necessary.And 
the syntax parser is very liberal when it comes to whitespaces. So use comments where ever you think you can 
by adding proper readable white spaces. 

###IIFEs, Immediately Invoked Function Expressions

