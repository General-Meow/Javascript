# Learning Javascript

#### Variables
- variables can be defined in a number of ways in JS and newer versions of JS has introduced `let`
- variables can be defined with the following `keywords`
  - `var` : defines a variable that can be reassigned
  - `let` : defines a block (scope) type variable that can be reassigned, introduced in ES6 - prefer this when possible
  - `const` : defines a constant variable that cannot be reassigned, introduced in ES6 - prefer this when possible

#### Reference and Primitive types
- Numbers, booleans and strings are primitive types
  - When you assign a variable from another variable that contains a primitive type, you actually create a copy
  - So watch out for errors when you change the value of a primitive variable
- Objects and Arrays are reference types
  - When assigning a new variable to contain the value of another variable that is a reference type, you copy the reference not the actual value
  - So when changing values of the original variable, the same will be reflected in the other variable

```
//primitive types
let aNumber = 1;
let aSecondNumber = aNumber; //this actually creates a copy of 1

console.log('aNumber', aNumber);
console.log('aSecondNumber', aSecondNumber);

aNumber = 2;

console.log('aNumber', aNumber);
console.log('aSecondNumber', aSecondNumber);
```

- If you really want to make a copy of an reference type (Object or Array) because you want to change it but do so in an immutable way, you can use the `spread` operator (look below) to extract copies of the properties/indexes into a new object

```
const aPerson = {
  name: 'Paul'
}

const secondPerson = aPerson;

secondPerson.name = "bob"

console.log(aPerson.name); //this will print bob
```

```
const aPerson = {
  name: 'Paul'
}

//create a new object and extract the properties of the original object
const secondPerson = {    
  ...aPerson
};

secondPerson.name = "bob"

console.log(aPerson.name); //this will print Paul as the original wasn't modified
console.log(secondPerson.name); //this will print Bob

```

#### Arrays
- Just like in Java there are some useful array functions
  - map: run a function over each element of the array and return a copy of the array with the new elements (doesn't mutate original array)
  - slice : create a copy of array
  - push : add element to end of array
  - pop : remove element at the end of an array
  - shift: remove element at the beginning of array
  - unshift: add element at the beginning of array
  - filter: keep elements in array that calculate to true from the predicate
  - reduce: run a reducer function to will convert the array to a single value

```
const arr = [1,2,3,4,5];
const mapped = arr.map( x => x * 2 );
console.log(mapped);



```
#### Arrow functions
- arrow syntax is an alternative to defining functions.
- there is the normal arrow syntax and the more simpler version

```javascript
//non arrow function square
const square = function(x) {
    return x * x;
}

//arrow function square very
const squareArrow = (x) => {
    return x * x
}

//arrow function lamda style
const squareArrowSimple = (x) => x * x

//you can also remove the brackets if you only send one arg to the function, you cannot use this syntax is you send more than one arg
const squareArrowSimpler = x => x * x

//if you dont have any args you can use the empty brackets
const emptyArgsFunc () => "hello"
```

- objects can also have functions
- when using the normal function syntax, the `this` variable is bound to the encompassing object
- `this` isn't bound when using the arrow function syntax on objects
- a short syntax to define functions on objects is to use the methods syntax `myMethod() {...}` - this allows to usage of the `this` variable
- ES6 makes sure that the `this` variable points to the encompasing function, so if your looping on an array in a method, if you provide a function in the foreach, then `this` wont point to the object but the closest function, in these situations, you could use an arrow function

```javascript
//object with traditional function definition
const eventObj = {
    name: "My birthday",
    printEvent: function() {
        console.log("The event is: ", this.name) //this is pointing to the current obj
    }
}

//arrow functions do not bind the `this` keyword to an encompasing object.
//so arrow functions aren't as well suited for functions (methods) in objects
const eventArrow = {
    name: "My birthday with arrows",
    printEvent: () => {
        console.log("The event is: ", this.name)
    }
}
//simple arrow function, again doesn't work
const eventArrowSimple = {
    name: "My birthday with arrows",
    printEvent: () => console.log("The event is: ", this.name)
}

//object method definition syntax
const eventMethodProperty = {
    name: "My birthday with methods",
    printEvent() {
        console.log("The event is: ", this.name)
    }
}

const eventMethodFunkyThis = {
    name: "My birthday with methods",
    guests: ["Paul"],
    printEvent() {
        console.log("The event is: ", this.name)
        //this works as it points to the eventMethodFunkyThis obj but this
        //in the for each function wont work because it binds this to the outer function
        this.guests.forEach(function(guest){
            console.log(guest + 'is coming to ' + this.name)

            //what is this? seems to point to the global this obj
            console.log('this is: ', this)

        })

        this.guests.forEach((guest) => {
            console.log(guest + ' is coming to ' + this.name)
        })
    }

}

```

### Promises

- A `Promise` is just like a Java Future, its a container for a value to be returned from an async function
  - It can be in one of 3 states
  - `Pending` the initial state and not yet filled
  - `Fulfilled` the async function has returned and placed the value within the promise
  - `Rejected` the async completed in a failed/unhappy state
- methods on a `Promise` are
  - `then` - takes 2 functions (success, failure) to runs the corresponding function once the `Promise` has been resolved
  - `catch` - takes a function and runs it when an error occurs, used when you only care about errors and not the success flow
    - You can have a chain of then/promises and a single catch. this catch will run on any rejection in the chain
    - errors that are thrown or placed in the rejection will be handled by the catch function
    - the closest catch to the thrown error is executed
    - catches can also throw errors, in that case the closet catch to that is executed
    - you can run more code after a catch function runs by adding more than() functions after it
    - if a promise throws an exception with no catch, the error bubbles up and you'll see an unhandledrejection error in the console
  - `finally` - just like exception handling in java, good to use these for clean up, loading indicators etc
  - You can chain the `then` calls when the function within `then` returns a `Promise`. this makes sequential calls to functions look cleaner as its defined in the change of `than` rather than nesting callback methods within each other
  - When rejecting, it's recommended that you return a `new` `Error` object

```javascript

function myFirstCalc() {
  var returnPromise = new Promise((resolve, reject) => {
    try {
      resolve(doSomeLongRunningCalculation(x,y,z));
    } catch(e) {
      reject(new Error("Error message value"))
    }
  })
  return returnPromise
}

function mySecondCalc(valueFromFirstCalc) {
  var returnAnotherPromise = new Promise((resolve, reject) => {
    try {
      resolve(doSomeOtherLongRunningCalculation(x,y,z));
    } catch(e) {
      reject(new Error("Error message value"))
    }
  })
}

var resultPromise = myFirstCalc().then(mySecondCalc).then(console.log)
```

- An alternative to Promises are the async/await syntax, which still uses Promises but is some syntactic sugar to make it easier to use
  - To make a function `async` use the `async` keyword in front the the function name
  - Making a function async automatically wraps the return object in a Promise
- You would typically use async/await as its easier to write
  - you dont need to manually write code to create a Promise and return it
  - you dont need to write the boiler plate function (called the `Executor`) that takes a resolve and reject (the callback functions - provided by javascript)
  - you dont need to place the result into the resolve nor create a new Error object with the error
  - you remove a lot of the `then()` boiler plate if your doing long promise chaining
  - you can keep all values of the promises in the same blocked scope as opposed to promoting them up in the parent scope with promise chaining
- You use the `await` keyword to wait for the Promise to resolve so that you extract the value from it as soon as its available
  - you can only use `await` in functions that are defined as `async`
  - the extraction of a value happens automatically when using `await`
  - await effectively calls the `then` method to extract the value
  - any `thenable` object (an obj with `then` method) can be used with await
  - if the object throws an exception/reject then the await behaves as if it throws an Error, which then can be wrapped around a try catch

```javascript
var myFirstCalc = async function () {
  return someLongCalc()
}

var mySecondCalc = async function (value) {
  return someLongCalc(value)
}

var main = async function() {
  //chaining
  var chainResult = myFirstCalc().then(mySecondCalc).then(console.log)
  //await
  var result1 = await myFirstCalc();
  var result2 = await mySecondCalc(result1);
  console.log(result2)
}
```

```javascript
//myModule.js

const add = function(x, y) {
  return x + y
}

module.exports = add
//if you want to export more than one thing, you can return an object with properties pointing to those things
module.export = {
  addFunction: add,
  anotherThing: blah
}
```

- The `Promise` class has 5 methods on it
  - `promise.all(...)`: takes an array of promises, returns a promise and that new promise will resolve when all the promises in the array resolves.
    - The result in the once the new Promise resolves will be an array of results from all the promises
    - order of results is the same of the promises
    - All or nothing approach, if one promise rejects, the resultant Promise will reject, good if we need all to succeed
  - `promise.allSettled(...)`: just like `.all()` but will wait for a result and resolve the new Promise even if the provided promises fail
    - the result will be an array of objects: `[{status: fulfilled, value: xxx}, {status: rejected, reason:error}]`
    - this method is newer and may not be supported by all browsers but can be polyfilled
  - `promise.race(...)` : like all but only waits for the first promise to resolve before kicking off the resultant resolve.
    - effectively races all the promises and the first wins
  - `promise.resolve(value)`: returns a Promise that will resolve with the provided value
    - not used much in modern code anymore because async/await
  - `promise.reject(value)`: just like resolve, returns a promise that will reject with said value
- Promise handlers (functions for the then, catch, finally) are automatically managed, they are placed in the `PromiseJob` queue aka `microtask queue`
  - queue is fifo
  - runs tasks in queue when nothing else is running

---

### Generators

- A generator is a function that yields (returns) multiple values
  - They are denoted with the `*` character right after the `function` keyword or just before the function name
  - `function* myGenerator(){}` or `function *myGenerator(){}`, former is preferred
  - Generators return a generator object with the `next()` method
  - calling the `next()` method will execute the generator function, up to the first `yield <VALUE>` statement where it will then return an object containing the value
  -  The object will have `value` and `done` `{value: x, done: false}`

```javascript

function* myFirstGenerator() {
  yield 1;
  yield 2;
  return 3;
}

let generatorValues = myFirstGenerator();
let firstValue = generatorValues.next(); //first value will contain 1
console.log(firstValue.value, firstValue.done)
let secondValue = generatorValues.next(); //second value will contain 2
console.log(secondValue.value, secondValue.done)
let thirdValue = generatorValues.next(); //third value will contain 3
console.log(thirdValue.value, thirdValue.done)
```

- generator objects, because they have `next()` are iterable, meaning they can be used with the `for` loop syntax `for(let value of generator) { ... }`
- can also be used with the `spread` operator `...` to deconstruct the values `console.log(...myFirstGenerator())//prints 1,2,3`
  - this can be used on things like arrays to split them up into individual items into a variable
  - this can also be used on objects to split the objects properties and functions into a variable
- the `spread` operator can also be used in method/function arguments e.g. `myFunction(...args)` using it this way will convert the method parameter to a array and this method can take any list of params e.g. `obj.myFunction('a', 'a', 'b')`
- you can manually get the value by calling `.next().value`

---

### Modules

- old module systems included
  - UMD: introduced as there was no clear winner between common.js and AMD, so this format supports both
  - common.js : what most node js modules use. this is the `const x = require(x)` syntax, cannot selectively import parts of a module
  - AMD
- the new module system based on ES6 is in built into js now
- a module can be just a file, one file is one module
- they can use `directives` `export` `import`
  - `export` allows functions, variables etc to be accessible from other modules
  - `import` imports exported things into a module for use
- the `default` keyword is used with export to define the default object to be exported if the import doesn't define anything to import e.g. `export default person`
  - when using a module with a default export, you can import it with a different name e.g. `import prs from './person.js'`
- if your importing an object that isn't exported with the `default` keyword, then you HAVE TO use the same name as used in the `export` line
  - you can however assign an `alias` using the `as` keyword to it during the import e.g. `import {person as prs} from './person.js'`
- You can import ALL of the exported objects from a module using the `*` syntax and assign it to a alias, this alias will then have all the objects assigned to it as properties e.g. `import * as bundled from './person.js'` `bundled.person ...`


```javascript
//utils.js

export function myUtilsFunction() {
    return 5
}

//mainCode.js
import { myUtilsFunction } from 'utils.js'

console.log(myUtilsFunction())
```

```javascript
//main app.js : common.js syntax
const myModule = require('./myModule.js')

const result = myModule.addFunction(1, 1)
```

- when using modules in the browser, you need to load the module using a script tag with a `type` attribute of `module` `<script type='module'>`...

#### features
- by default, modules are `use strict` so you cant use unknown variables
- you get module level scope, so other modules cant see variables and functions are the top level, unless exported
- when using in browsers, you have the `import.meta` object to query data about the module
- `this` is not defined in modules

#### Build tools
- You hardly every use modules as is, you would normally bundle them using build tools such as `webpack`
- build tools do many things
  - usually sort out the main file to be placed in the script tag.
  - do dependency analysis and remove dead code
  - creates a single bundle file and cleans up code to make it smaller

#### Object advanced features

##### Object properties shorthand
- Creating objects with properties from variables defined previously can be cumbersome
  - to make this easier use the object properties shorthand from ES6
  - create an object and only refer to the pre defined variable if you want a property of the same name in the object

```javascript
const name = 'paul'
const sex = 'male'

//create an object using the old way
const oldWay = {
  name: name,
  sex: sex
}

console.log(oldWay)

console.log(oldWay)

const newWay = {
  name, //use the name value and create a property with the same name
  gender: sex, //you can still change the property name
  location: 'london' //if location doesn't exist set default
}

console.log(newWay)
```

##### Object destructuring
- When you want to create new variables from object properties, it can also get cumbersome, so we can use object destructuring
- This is the breaking of properties in an object to variables OR breaking arrays into variables
- use the syntax `const {propertyToBreak} = theObject` destruct objects, this will extract the property `propertyToBreak` and create a new variable with the same name
- use the syntax `const [a, b] = ['arrayEle1', 'arrayEle2'];` this will destruct an array and create 2 new variables a and b
- you can rename the new variable with the syntax `{property:newVariableName}`
- if you extract a property that doesn't exist, it'll be undefined
- you can set a default value if the property is undefined with the syntax `{notHere = 5}`
- destructuring can also be used within a functions parameters
  - `const myFunc = function({propertyA, propertyB}){}`
  - `const myFunc = ({propertyA, propertyB}) => {}`
  - when destructuring function parameters, you must ensure that an object is sent as that argument, otherwise it will try a destructure a `undefined` value which will cause errors
    - to fix this you can do some defensive programming and set a default value for the destructed object so that if you dont provide a param, it will have an empty object and try and destructure that, which will return `undefined` rather and throw errors
    - just like parameters, the destructured params can also have default params
  - in general, its always a good idea to provide a default object when destructuring on function paramters


```javascript
const myObject = {
  name: 'paul',
  sex: 'male',
  location: 'london'
}

//old way
const name = myObject.name
const sex = myObject.sex
const location = myObject.location

console.log(name, sex, location)

//new way
const myNewObject = {
    anotherAge: 18,
    anotherSchool: 'deptford green',
    anotherClazz: 'wd',
    anotherFavSubject: undefined
}

const {anotherAge, anotherSchool: secondarySchool, anotherClazz, anotherFavSubject, derp = 5} = myNewObject

console.log("new way with destructuring:", anotherAge, secondarySchool, anotherClazz, anotherFavSubject, derp)


//defensive programming have have default object param
const aFunc = (param1, {destructProp1, destructProp2} = {}) {
  console.log(param1, destructProp1, destructProp2)
}

//call function without a second parameters therefore trigger the default object param
//this will console log undefined but you can set default values for the destructured properties too if you wish
aFunc('some value for param1')
```

##### Default function params
- You can set default values for function parameters if you run the function without the parameter

```javascript

const greeter = (name = "John Doe") => {
  console.log("Hello " + name)
}

greeter("Paul") //will print Hello Paul

greeter() //will print Hello John Down

```

##### Classes
- Javascript is a prototyple, functional and object oriented language
- inheritence could be done via prototypes or classes
- Classes are defined with the `class` keyword and the name must start with a capital letter
- The `this` keyword is used to assign member variables to the class
- Instances are created using the `new` keyword
- Inheritence can happen between classes with the `extends` keyword
  - when inheriting, if you use the old syntax of supplying a constructor, you must make sure you call the parent constructor using `super()`

```
class Person {
  constructor(){
    this.name = "Bob";
  }

  sayName = () => {
    console.log("My name is: " + this.name);
  }
}

let aPerson = new Person();
aPerson.sayName();
```

- Javascript also supports a newer/shorter syntax for defining a class

```
//newer way to define classes

class Person {
  name = "bob";

  sayName = () => {
    console.log("My name is: " + this.name);

  }
}

let aPerson = new Person();
aPerson.sayName();
```

```
class Human {
  sex = 'Female';
  printSex = () => console.log(this.sex);
}
class Person extends Human {
  name = "bob";
  //sex = "Male"; overriding is a simple case of redefining the value
  sayName = () => console.log(this.name);
  //printSex = () => console.log("my sex is: " + this.sex); override the original method
}

let aPerson = new Person();
aPerson.sayName();
aPerson.printSex();
```

##### Misc
- You can remove properties from a javascript object by using the `delete` keyword e.g. `delete myObject.age` removes the age property from myObject
- `parseInt()` can be used to convert a stringified number to an int
