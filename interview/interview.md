#functional programming
Functional programming produces programs by composing mathematical functions
and avoids shared state & mutable data.
- Pure functions / function purity
- avoid side-effects
- example: haskell, scala, Erlang
- features like: high order function

#Difference between classical inheritance and prototypal inheritance
Class Inheritance: instances inherit from classes (like a blueprint — a description of the class), and create sub-class relationships: hierarchical class taxonomies. Instances are typically instantiated via constructor functions with the `new` keyword. Class inheritance may or may not use the `class` keyword from ES6.

Prototypal Inheritance: instances inherit directly from other objects. Instances are typically instantiated via factory functions or `Object.create()`. Instances may be composed from many different objects, allowing for easy selective inheritance.

#What are two-way data binding and one-way data flow, and how are they different?
Two way data binding means that UI fields are bound to model data dynamically such that when a UI field changes, the model data changes with it and vice-versa.

One way data flow means that the model is the single source of truth. Changes in the UI trigger messages that signal user intent to the model (or “store” in React). Only the model has the access to change the app’s state. The effect is that data always flows in a single direction, which makes it easier to understand.

One way data flows are deterministic, whereas two-way binding can cause side-effects which are harder to follow and understand.

#What are the pros and cons of monolithic vs microservice architectures?
A monolithic architecture means that your app is written as one cohesive unit of code whose components are designed to work together, sharing the same memory space and resources.

A microservice architecture means that your app is made up of lots of smaller, independent applications capable of running in their own memory space and scaling independently from each other across potentially many separate machines.

#What is asynchronous programming, and why is it important in JavaScript?

source: https://medium.com/javascript-scene/10-interview-questions-every-javascript-developer-should-know-6fa6bdf5ad95
------------------------------

### 5 Typical JavaScript Interview Exercises

#Question 1: Scope

Consider the following code:

(function() {
   var a = b = 5;
})();

console.log(b);

What will be printed on the console?

The code above prints 5.

The trick of this question is that in the IIFE there are two assignments but the variable a is declared using the keyword var. What this means is that a is a local variable of the function. On the contrary, b is assigned to the global scope.

The other trick of this question is that it doesn’t use strict mode ('use strict';) inside the function. If strict mode was enabled, the code would raise the error Uncaught ReferenceError: b is not defined.

#Question 2: Create “native” methods
Define a repeatify function on the String object. The function accepts an integer that specifies how many times the string has to be repeated. The function returns the string repeated the number of times specified. For example:

console.log('hello'.repeatify(3));

Should print hellohellohello.

//response
String.prototype.repeatify = String.prototype.repeatify || function(times) {
   var str = '';

   for (var i = 0; i < times; i++) {
      str += this;
   }

   return str;
};

#Question 3: Hoisting

What’s the result of executing this code and why.

function test() {
   console.log(a);
   console.log(foo());
   
   var a = 1;
   function foo() {
      return 2;
   }
}

test();

Answer
The result of this code is undefined and 2.
The reason is that both variables and functions are hoisted (moved at the top of the function) but variables don’t retain any assigned value. So, at the time the variable a is printed, it exists in the function (it’s declared) but it’s still undefined.

#Question 4: How this works in JavaScript

What is the result of the following code? Explain your answer.

var fullname = 'John Doe';
var obj = {
   fullname: 'Colin Ihrig',
   prop: {
      fullname: 'Aurelio De Rosa',
      getFullname: function() {
         return this.fullname;
      }
   }
};

console.log(obj.prop.getFullname());

var test = obj.prop.getFullname;

console.log(test());

Answer
The code prints Aurelio De Rosa and John Doe. The reason is that the context of a function, what is referred with the this keyword, in JavaScript depends on how a function is invoked, not how it’s defined.

#Question 5: call() and apply()
Fix the previous question’s issue so that the last console.log() prints Aurelio De Rosa.

Answer
The issue can be fixed by forcing the context of the function using either the call() or the apply() function. If you don’t know them and their difference, I suggest you to read the article What’s the difference between function.call and function.apply?. In the code below I’ll use call() but in this case apply() would produce the same result:

console.log(test.call(obj.prop));

source: https://www.sitepoint.com/5-typical-javascript-interview-exercises/
-----------------------------------------

###5 More JavaScript Interview Exercises

#Question 1: Closures

```javascript
var nodes = document.getElementsByTagName('button');
for (var i = 0; i < nodes.length; i++) {
   nodes[i].addEventListener('click', function() {
      console.log('You clicked element #' + i);
   });
}
```

What will be printed on the console if a user clicks the first and the fourth button in the list? Why?

The code prints two times You clicked element #NODES_LENGTH where NODES_LENGTH is the number of the nodes retrieved. The reason is that after the for loop is completed, the variable i assumes a value equal to the length of the nodes list. In addition, because i was in scope at the time the code attached the handler, the variable belongs to handler’s closure. As you’ll recall, the value of the variables in closures isn’t static, hence the value of i isn’t the value at the time the handler was added (0 for the first button in the list, 1 for the second, and so on). At the time the handler will be executed, on the console will be printed the current value of the variable i, that is equal to the length of the nodes list.

#Question 2: Closures
Fix the previous question’s issue so that the handler prints 0 for the first button in the list, 1 for the second, and so on.

The first solution involves the use of an IIFE to create another closure so that the value of i will be the one expected. The code implementing this approach is the following:

```javascript
var nodes = document.getElementsByTagName('button');
for (var i = 0; i < nodes.length; i++) {
   nodes[i].addEventListener('click', (function(i) {
      return function() {
         console.log('You clicked element #' + i);
      }
   })(i));
}
```

Another possible solution doesn’t involve the use of an IIFE and moves the function outside the loop. This approach is implemented by the following code:

```javascript
function handlerWrapper(i) {
   return function() {
      console.log('You clicked element #' + i);
   }
}

var nodes = document.getElementsByTagName('button');
for (var i = 0; i < nodes.length; i++) {
   nodes[i].addEventListener('click', handlerWrapper(i));
}
```

#Question 3: Data Types

```javascript
console.log(typeof null);
console.log(typeof {});
console.log(typeof []);
console.log(typeof undefined);
```

What’s the output?

Answer

object
object
object
undefined

We have to use `instanceof` to know if a variable is an array. `if (myArray instanceof Array) {`

#Question 4: Event Loop

```javascript
function printing() {
   console.log(1); 
   setTimeout(function() { console.log(2); }, 1000); 
   setTimeout(function() { console.log(3); }, 0); 
   console.log(4);
}

printing();
```

Answer

1
4
3
2

To understand why the numbers are printed in this order, you have to understand what setTimeout() does and how the browser’s event loop works. The browser has an event loop which checks the event queue and processes pending events. UI events (such as click, scroll, and so on), Ajax callbacks, and callback provided to setTimeout() and setInterval() are all processed one at a time by the event loop. Therefore, when calling the setTimeout() function the callback provided is queued, even if the delay specified is zero. The callback stays in the queue until the time specified has elapsed and the engine is ready to perform the action (i.e. if it isn’t performing another action at the moment). So, although a callback passed to setTimeout() is delayed by zero milliseconds, it’ll be queued and executed after other non-delayed statements declared in the same function.

With this in mind it’s easy to understand that “1” is printed first because it’s the first statement of the function and it’s not delayed using the setTimeout() function. Then, we have “4” because it’s the first non-delayed number to print, so it isn’t queued, after the delayed ones. Now, there are “2” and “3” left. Both have been added to the queue but while the former has to wait one second, the latter can be printed after 0 seconds (which means instantaneously after the engine has completed all the other processes). This explains why “3” is printed before “2”.

#Question 5: Algorithms

functions that determines if a number is primer

Answer

```javascript
function isPrime(number) {
   // If your browser doesn't support the method Number.isInteger of ECMAScript 6,
   // you can implement your own pretty easily
   if (typeof number !== 'number' || !Number.isInteger(number)) {
      // Alternatively you can throw an error.
      return false;
   }

   if (number < 2) {
      return false;
   }
   
   if (number === 2) {
      return true;
   } else if (number % 2 === 0) {
      return false;
   }

   var squareRoot = Math.sqrt(number);
   for(var i = 3; i <= squareRoot; i += 2) {
      if (number % i === 0) {
         return false;
      }
   }

   return true;
}
```

-------------------------------

###21 Essential JavaScript Interview Questions
https://www.codementor.io/nihantanu/21-essential-javascript-tech-interview-practice-questions-answers-du107p62z

-------------------------------

###JS Algorithms Interview
http://thatjsdude.com/interview/js1.html

-------------------------------

###JS: Basics and Tricky Questions
http://www.thatjsdude.com/interview/js2.html

-------------------------------

### Javascript design patterns
https://medium.com/front-end-weekly/javascript-design-patterns-ed9d4c144c81

