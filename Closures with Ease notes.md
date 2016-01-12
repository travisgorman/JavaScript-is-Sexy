# Understand JavaScript Closures With Ease 
A closure is an inner function that has access to the enclosing function's variables - scope chain.  

[Understand Closures with Ease](http://javascriptissexy.com/understand-javascript-callback-functions-and-use-them/)

// A Basic Example of Closure in JavaScript

```js
function showName(firstName, lastName){
  var nameIntro = "Your name is";
  function makeFullName(){
    return nameIntro + firstName + " "+ lastName;
  }
  return makeFullName();
}
```


```js
showName("Michael", "Jackson");         // --> "Your name is Michael Jackson"
showName("Travis", "Gorman");           // --> "Your name is Travis Gorman"
```

// jQuery Example 

```js
$(function(){
  var selections = [];
  $(".niners").click(function(){        // has access to the selections variable
    selections.push(this.prop("name"));   // and updates it in the outer functions scope
  });
});
```

// 1. Closures have access to the outer function’s variable EVEN AFTER the outer function returns

```js
function celebrityName(firstName){
  var nameIntro= "This celebrity is ";
  function lastName(theLastName){
    return nameIntro + firstName + " " + theLastName;
  }
  return lastName;
}
```


```js
var mjName = celebrityName("Michael");        // the celebrityName outer function has returned

                      // The closure (lastName) is called here after the outer function has returned above
mjName("Jackson");            // --> "This celebrity is Michael Jackson "
```

// 2. Closures store REFERENCES to the outer function's variables; NOT the actual value. 

```js
function celebrityID(){
  var celebrityID = 999;
  return {
    getID: function(){
      return celebrityID;
    },
    setID: function(theNewID){
      celebrityID = theNewID;
    }
  }
}
```

```js
var mjID = celebrityID();     // clebrityID outer function has returned
mjID.getID();                 // 999
mjID.setID(567);              // Changes the outer function's 'celebrityID' variable
mjID.getID();                 // --> 567 (returns the updated 'celebrityID' variable)
```



// 3. Closures Gone Awry
// Since closures have access to the updated values of outer function variables, 
// they can lead to bugs when the outer function's variable changes with a for loop.


```js
function celebrityIDCreator(theCelebrities){
  var i;
  var uniqueID = 100;
  for (i = 0; i < theCelebrities.length; i++){
    theCelebrities[i]["id"] = function(){
      return uniqueID + i;
    }
  }
  return theCelebrities;
}
```

```js
var actionCelebs = [{name: "Stalone", id:0},{name:"Cruise", id:0},{name:"Willis", id:0}];
var createIdForActionCelebs = celebrityIDCreator(actionCelebs);

var stalloneID = createIdForActionCelebs[0]; 
console.log(stalloneID.id());     
```

*returns* 
>>>103



// The number 3 (length of array) was added to the uniqueID (100) for ALL of the celebritiesID.
// So every position in the returned array get id = 103, instead of 100, 101, 102, etc...
// To fix this bug, you can use an Immediately Invoked Function Expression, such as the following...


```js
function celebrityIDCreator(theCelebrities){
  var i;
  var uniqueID = 100;
  for (i = 0; i < theCelebrities.length; i++){

    theCelebrities[i]["id"] = function(j) {   // the 'j' parametric is the 'i' passed in on invocation of this IIFE
      return function(){            // each iteration passes the current value of 'i' into this IIFE 
        return uniqueID + j;        // and it saves the correct value to the array 
      } ()            // by adding () at the end of this function, we are executing it immediately
                    // and returning the value of (uniqueID + j), instead of returning a function.
    } (i);              // immediately invoke the function, passing the 'i' variable as a parameter
  }

  return theCelebrities;
}
```


```js
var actionCelebs = [{name: "Stalone", id:0},{name:"Cruise", id:0},{name:"Willis", id:0}];
var createIdForActionCelebs = celebrityIDCreator(actionCelebs);
```


```js
var stalloneID = createIdForActionCelebs[0];
console.log(stalloneID.id);  
```
*returns* 
>>>100



```js
var cruiseID = createIdForActionCelebs[1];
console.log(cruiseID.id);  
```
*returns* 
>>>101

___

## Closure 'recipe' from Hack Reactor

```js
function checkScope(){                  
  var innerVariable = "localScope";     
    function innerFunction(){           
      return innerVariable;
    };
    return innerFunction;               
}
```
1. Create parent function
1. Define variables in parents local scope (these can be accessed by child function)
1. Define a function inside parent function - a child function
1. Return that function from inside the parent function



