## Introduction

Well, time to teach myself something I don't understand again...
In this article, we will explore a technique called currying. Currying is another concept in functional programming. It goes as follow: when a function expects several arguments, we break it down into successive chained functions that each take a single argument.

We reduce the **arity** of each function to one ( arity is the arguments length of a function ). Let's see some examples.

*Note: You should probably understand closures before going further. Maybe with <a href='https://dev.to/damcosset/explaining-closures-to-myself' target='_blank'>this</a>?* 

## Simple currying

Let's start with a non-curried example. We want to calculate the sum of the arguments given to a function:

```javascript runnable
const sum = (...args) => {
  let sum = 0
  for( let i = 0; i < args.length; i++ ) {
    sum += args[i]
  } 
  return sum
}

sum( 1, 2, 3, 4, 5, 6, 8) 
```

A curried version of the sum function would look like so:

``` javascript
sumCurry(1)(2)(3)(4)(5)(6)(8) //29
```

How would we implement currying then? Well, let's try something like this:

```javascript
const curried = 
  ( func, arity = func.length, nextFunc ) =>
    (nextFunc = prevArgs => 
      nextArg => {
        let args = prevArgs.concat( [nextArg] )

        if( args.length >= arity ){
          return func( ...args )
        } 
        else {
          return nextFunc( args )
        }
      })( [] )

const sumCurry7 = curried( sum, 7)
sumCurry7(1)(2)(3)(4)(5)(6)(8) // 29
```

Ok, let's break the *curried* function down a bit.

It takes 2 arguments, the first is the function to be called at the end ( sum in our case ), the second is the arity we expect ( the number of arguments we want before calling sum ).

This function returns a IIFE ( Immediately Invoked Function Expression ). This IIFE takes a empty array as a parameter on its first call. It returns another function with the next argument in our curry sequence as an argument and the function adds it to our collection of arguments. On the first call, our array is empty so the variable args is equal to [ 1 ].

We check if we have enough arguments to call our original function. If not, we create another curried function and keep collecting arguments. If we do, we call *sum* with all of our arguments.

## Another example

Let's imagine we are in a restaurant. We can order one dish and a dessert. We have four different components to our order, the customer's id, its table number, the dish and the dessert.

Our curried function would look like so:

`curriedOrder(customerId)(tableNumber)(dish)(dessert)`

Let's create a function that just returns the order:

```javascript
const displayOrder = (...infos) => {
  let order = `Customer ${infos[0].toUpperCase()} at table ${infos[1]} wants ${infos[2]} ${infos[3] ? `and ${infos[3]} for dessert.` : '.'}`
  return order
}
```

Nothing fancy here. Let's curry this with the function we created earlier:

```javascript
const mealOneDish = curried( displayOrder, 3 )
const mealWithDessert = curried( displayOrder, 4)

const orderOne = mealOneDish('Joe')(3)('cheeseburger')
// Customer JOE at table 3 wants cheeseburger .

const orderTwo = mealWithDessert('Sarah')(6)('fries')('waffles')
// Customer SARAH at table 6 wants fries and waffles for dessert.
```

## Why use currying?

The first reason to use a technique like currying is that you can separate in your codebase when and where your arguments are specified. You may not have all the arguments necessary to call the *sum* function right away, or all the order informations to print them out.

The client gets in the restaurant, you don't know where she is going to sit yet. Her name is Johanna.

`const johanna = mealWithDessert('Johanna')`

She walks around and finally decides to sit at the table number 7.

`const johannaTable7 = johanna(7)`

The waiter takes her order.

`const johannaT7Order = johannaTable7('nuggets')`

- Would you like a dessert? 
- No, thank you

```javascript
johannaT7Order() // Customer JOHANNA at table 7 wants Nuggets .
```

- Hold on, I changed my mind. I'll have an ice cream for dessert.

```javascript
johannaT7Order('ice cream') //Customer JOHANNA at table 7 wants Nuggets and ice cream for dessert.
```

The same issue could be applied if we wanted to calculate a sum with our earlier function.

```javascript
const sumCurry4 = curried( sum, 4) 
// I want to calculate the sum of four numbers, I don't know them yet.

const firstNumber = sumCurry4(3) // Ok first one

//...Doing other important stuff ...

const secondAndThird = firstNumber(5)(20) // I need one more

//...Feeding the cat...

const final = secondAndThird(2) // 3 + 5 + 20 + 2 = 30

```

If we don't have the number of arguments required yet, our curry returns a function. We only call the original function when all the arguments are there.

The second reason to work with curried functions is because it is a lot easier to work with unary ( single argument ) functions. Composition is more effective with single argument functions, and a key component of functional programming.

I also like how readable it makes the code, but it might be a personal preference.

Well, that was an introduction about currying. I hope I've been clear enough on a subject I'm still exploring myself. I guess I could have gone a bit further with my *curried* implementation and curry that also... Any feedback/rectification is welcome!
