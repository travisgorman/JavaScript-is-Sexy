## Understand JavaScript Callback Functions and Use Them
[Understand JavaScript Callback Functions and Use Them](http://javascriptissexy.com/understand-javascript-callback-functions-and-use-them/)


### A "callback" is a function that's passed into another function as an argument.  
### A function that takes one or more callbacks is a "higher-order function".  
The higher-order function 'calls back' to the function passed in. 

A common use of a callback function in jQuery

```js
  $( "#btn_1" ).click( () => alert( "Btn 1 Clicked" ) );  
```
`alert()` is passed into the `click()` function as an argument (the "callback" — the function that `click()` calls back to). Whenever the element with an`id` of `btn_1` is clicked, the callback will fire. In this case the callback is `alert()` taking a string, 'Btn 1 Clicked'. When the `btn_1` is clicked, the browser alerts the message "Btn 1 Clicked".


### This is a classic example in basic JavaScript

```js
  var friends = ["Mike", "Stacy", "Andy", "Rick"];
  friends.forEach( (name, index) => console.log(index + 1 + ". " + name) );  
```

*returns* 
>>>1. Mike, 2. Stacy, 3. Andy, 4. Rick​

___

### How Callback Functions Work?
* The function is not executed in the parameter. 
* The function definition gets passed, and it is "called back" at a specified point
* *Callback functions are closures*


### Basic Principles when Implementing Callback Functions
 * Use Named OR Anonymous Functions as Callbacks
 * when we call the `getInput` function, we pass `logStuff` in as a parameter.
 * `logStuff` will be the function called back inside the `getInput` function

```js
var allUserData=[];

function logStuff( userData ) {
  if ( typeof userData === 'string') {
    console.log( userData );
  } else if ( typeof userData === 'object') {
    for( var item in userData ) {
      console.log(item + ": " + userData[item]);
    }
  }   
}

function getInput( options, callback ){
  allUserData.push( options);
  callback( options );
}
```
#### Create two functions, `logStuff()`, and `getInput()`

*`getInput()` is a higher-order function that takes an `options` parameter and a `callback()` function.  
  - pushes what is passed as the `options` paramerter into an array in the global scope called `allUserData`
  - calls `callback()` passing in `options`

* `logStuff()` is a callback function that takes one parameter, `userData`
  - if userData is a string, it logs it to the console
  - if userData is an object, it runs a for/in loop on it, printing each item pair
  - in other words, it logs stuff

```js
getInput ( 
  { 
    name: "Rich", 
    specialty: "JavaScript" 
  }, 
  
  logStuff );
```
Pass this little JSON object in as the `options` argument, and `logStuff()` as the `callback`

*returns* 
>>>
name: Rich
specialty: JavaScript

The whitespace doesn't matter, so this could also be styled like

```js
getInput( {name: "Rich", specialty: "JavaScript"}, logStuff );
```

___

### Pass Parameters to Callback Functions

```js
var generalLastName = "Clinton";      
function getInput( options, callback ){
  allUserData.push( options );
  callback( generalLastName, options ); 
}
```
* pass global variable `generalLastName` into callback 

### Make sure Callback is a Function Before Executing It.

```js
function getInput( options, callback ) {
  allUserData.push( options );
  if ( typeof callback === "function" ){     
    callback( options );                     
  }
}
```

* Make sure the callback is a function.
* Call it, since we have confirmed it is callable.

___
### Problem when using methods with the `this` object as callbacks
* Define an object with some properties and a method​
* We will later pass the method as a callback function to another function​

```js
var clientsData = {                     
  id: 094545,
  fullName: "Not Set",
  setUserName: function( firstName, lastName ){      
      this.fullName = firstName + " " + lastName;       
    }
}

function getUserInput( firstName, lastName, callback ){
  callback( firstName, lastName );
}
```
* `clientsData` is an object in the global scope.  
* `setUserName` ia a method on `clientData`.  


### The `this` object in the global function points to the `window` object.

```js
getUserInput( "Barack","Obama", clientsData.setUserName );  

console.log( clientsData.fullName );  
```

*returns* 
>>>
"Not Set" (the fullName property was initialized on the window object)

```js
console.log( window.fullName );
```

*returns* 
>>>
"Barack Obama" 
  
___

### Use the 'Call' or 'Apply' Function To Preserve 'this'
* Call & Apply both take the value to be used as the `this` object as their first parameter.
* Apply includes an array of values or the arguments object to pass to the function 
* The use of the `apply()` function below will set the `this` object to be `callbackObj`


```js
function getUserInput( firstName, lastName, callback, callbackObj ) {
  callback.apply( callbackObj, [ firstName, lastName ] );
}
```

#### Call `getUserInput`, which takes 4 arguments
* pass `firstName` and `lastName` arguements as strings.
* the callback you want is the `setUserName()` method on the `clientsData` object
* `clientsData` is your `callbackObj`

```js
getUserInput ( "Barack", "Obama", clientsData.setUserName, clientsData );
console.log (clientsData.fullName);   
```

*returns* 
>>> "Barack Obama"

___

### Multiple Callback Functions Allowed (an example with jQuery's AJAX function)


```js
function successCallback() {
    // Do stuff before send​
}
​
​function successCallback() {
    // Do stuff if success message received​
}
​
​function completeCallback() {
    // Do stuff upon completion​
}
​
​function errorCallback() {
    // Do stuff if error received​
}

$.ajax( 
    {
      url: "http://fiddle.jshell.net/favicon.png",
      success: successCallback,
      complete: completeCallback,
      error: errorCallback
    }
);
```
___

###  "Callback Hell" Problem and Solution
Numerous levels of callback functions can create messy code difficult to follow.  
[callback hell](http://callbackhell.com/)


#### Here are two solutions to this problem:

##### Named Functions
1. *Name your functions* and pass in just the name of the function instead of defining an anonymous function in the parameter of the main funciton.

##### Organizing Modular Code
2. *Modularity:* Separate your code into modules, so you can export a section of code that does a particular job. Then you can import that module into your larger application. JavaScript module systems (CommonJS, AMD, ES Harmony, etc) To use modules in the browser you can use a command-line thing called [browserify](https://github.com/substack/node-browserify)
        

___ 

### Make Your Own Callback Functions
  
Callback functions allow you to

* DRY! "Don't Repeat Yourself" - *avoid repetitive work* - don't repeat code you've already written. 
* Implement better *abstraction* where you can have more generic functions that are versatile
* Have better *maintainability*
* Have more *readable* code
* Have more *specialized* functions


___

### `genericPoemMaker()` — The callback
This is what we will pass into the `getUserInput()` function.​ It takes two parameters and uses them in this poem. 

```js
function genericPoemMaker ( name, gender ) {

  console.log( 

    name + " is finer than fine wine.\n"+
    "Altruistic and noble for the modern time.\n"+
    "Always admirably adorned with the latest style.\n"+
    "A " + gender + " of unfortunate tragedies who manages a perpetual smile");
}
```

### `getUserInput()` — the higher-order function 
This function just retrieves some data that can be called upon in various functions

```js
function getUserInput ( firstName, lastName, gender, callback ) {
  var fullName = firstName + " " + lastName;
  if ( typeof callback === "function" ){
    callback( fullName, gender );
  }
}
```

### Pass `genericPoemMaker()` into `getUserInput()` as the `callback` paramater

```js
getUserInput("Michael", "Fassbender", "Man", genericPoemMaker);
```

*returns* 
>>>"Michael Fassbender is finer than fine wine.  
Altruistic and noble for the modern time.  
Always admirably adorned with the latest style.  
A Man of unfortunate tragedies who still manages a perpetual smile."

___

But `getUserInput()` only handles data retrieving, we can manipulate that data with any number of callback functions we could come up with.   
Like this `greetUser()` function that takes that same info and does something entirely different with it.

```js
function greetUser (customerName, sex) {
  var salutation = sex && sex === "Man"? "Mr.":"Ms";
  console.log("Hello, " + salutation + " " + customerName + ".");
}
```
we can make new callback functions for our higher-order functions that create new functionality.  A we've made new function to be used as a callback, `greetUser()`.  
We can leave out the `callbackObj` entirely.  When writing custom higher-order functions, put your parameters in order of importance so ones you are less likely to use can be left off entirely.  

```js
getUserInput( "Bill", "Gates", "Man", greetUser );
```

*returns* 
>>>"Hello, Mr. Bill Gates"



