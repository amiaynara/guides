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


