# 3.0.0 Assignment - Foundation of Promises

- [3.0.0 Assignment - Foundation of Promises](#300-assignment---foundation-of-promises)
- [Short Answers](#short-answers)
- [Question 1: resolvedWrapper](#question-1-resolvedwrapper)
- [question 2: rejectedWrapper](#question-2-rejectedwrapper)
  - [value vs Error() constructor](#value-vs-error-constructor)
- [Question 3: handleResolvedPromise](#question-3-handleresolvedpromise)
- [Question 4: handleResolvedOrRejectedPromise](#question-4-handleresolvedorrejectedpromise)
- [Question 5: pauseForMs](#question-5-pauseforms)
- [Question 6 - return4RandomColors MODIFY](#question-6---return4randomcolors-modify)
  - [Analyzing the code](#analyzing-the-code)
  - ["What!? WHY ARE YOU MAKING ME READ THIS WITH MY OWN EYES?"](#what-why-are-you-making-me-read-this-with-my-own-eyes)
  - [Promises to the rescue!](#promises-to-the-rescue)
- [Bonus](#bonus)

# Short Answers
These are important, don't skip them!

# Question 1: resolvedWrapper
Promises have 3 states: pending, fulfilled, or rejected. Pending just means the promise hasn't settled yet, so let's instead work on "fulfilled" first. Remember, a fulfilled promise means that a value was returned without an error.

> ## fulfilled vs resolved
> Now, it's true that "fulfilled" means a promise completed without an error, and "resolved" *technically* means a promise is just completed, and could be either fine *or* have an error. However, **most** people simply say "resolved" meaning no error, and "rejected" meaning an error. So from here on out, that's how we'll refer to promises: **pending, resolved, or rejected.**

The `resolvedWrapper` function takes in any value and then returns a resolved promise of that value. You can use either the `new Promise` or `.resolve` syntax, but make sure you understand both!

```js
// example input/output
resolvedWrapper(10)
// this returns:
// Promise { 10 }

// and we could log it out like this:
resolvedWrapper(10).then(console.log)
// this logs:
// 10
```

# question 2: rejectedWrapper
The other side of the promise coin is rejection. Let's write a function that takes in a string message, and rejects with a [new Error instance](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error/Error) with the given string as the error's message. You can use either the `new Promise` or `.reject` shortcut, but make sure you understand both!

## value vs Error() constructor
Why are we rejecting with an error instead of just a value? Because it's best practice! Don't *throw* a new error, that's bad, just return a new instance of an error. Make sure to check the [Error docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error/Error) if you forget how to make a new error.

```js
resolvedWrapper('Oh no!')
  .catch((err) => console.log(err.message))
// Oh no!
```

# Question 3: handleResolvedPromise
OK, we've made promises, but how do we deal with them? Write a function `handleResolvedPromise`. All it should do is take in a promise and using `.then`, grab the promise's resolved value, console.log it, and then return it!

```js
handleResolvedPromise(Promise.resolve('yo'))
// Logs "yo"

handleResolvedPromise(Promise.resolve('yo'))
  .then(val => console.log(`we still have ${val}`))
// Logs "yo" and "we still have yo"
// We still have access to "yo" since it's also returned as a Promise
```

# Question 4: handleResolvedOrRejectedPromise
Write a new function `handleResolvedOrRejectedPromise`. It also takes in a promise, but this time let's handle any possible rejections as well.

If the passed in promise resolves, handle it with `.then`, the behavior should be identical to `handleResolvedPromise`. However, if the passed in promise rejects your function needs to:
- log with `console.error` the message `"Your error message was: [Error's message]"`
- return `null`

**Do not just log the error object itself! Use the `.message` property!**

```js
handleResolvedOrRejectedPromise(Promise.resolve(100))
  .then(val => val)
// logs: 100
// and also returns: 100

handleResolvedOrRejectedPromise(Promise.reject(Error('Yikes!')))
  .then(val => val === null)
// logs: "Your error message was: Yikes!"
// the final return value is null
```

# Question 5: pauseForMs
JS gives us timers, [`setInterval`](https://developer.mozilla.org/en-US/docs/Web/API/setInterval) and [`setTimeout`](https://developer.mozilla.org/en-US/docs/Web/API/setTimeout). Let's look at `setTimeout` which takes a callback and then calls it after a number of milliseconds have passed:

```js
const myCallback = () => {
  console.log("Delayed for 1 second.");
};

setTimeout(myCallback, 1000);
```

That's cool and all, but even though it's asynchronous code, it's not a promise.

Your mission is to create an asynchronous function `pauseForMs` that takes in a number of milliseconds and returns a promise that resolves after the given number of milliseconds.  This promise will never reject! For example: 

```js
// pauseForMs should return a promise that resolves after 1000 milliseconds (1 second)
// at which point the `.then()` callback will execute and print "it's been a second!"
pauseForMs(1000)
  .then(() => {
    console.log("It's been a second!");
  });
```

To do this, you must wrap `setTimeout` in a promise. The promise does not need to resolve to any particular value, but it *must* resolve after the time is up so that we can execute code after using `.then()`

# Question 6 - return4RandomColors MODIFY
As we talked about earlier, callback hell is when you have to nest many callback functions, and it's the main motivation for making promises in the first place.

Look at the `get4RandomColorsOld` function below. It uses a callback based `crypto.randomFill` function to get an array of 3 random numbers, and then it feeds those into a helper function to make `rgb` strings. But since it's callback based, the only way to get our `rgb` strings array is to pass in *another* callback. Look:

```js
const numsToRGBColor = ([color1, color2, color3]) => {
  return `rgb(${color1}, ${color2}, ${color3})`;
};

const get4RandomColorsOld = (useColorsCallback) => {
  const colors = [];
  crypto.randomFill(new Uint8Array(3), (err1, buffer1) => {
    if (err1) throw new Error(err1);
    colors.push(numsToRGBColor([...buffer1]));

    crypto.randomFill(new Uint8Array(3), (err2, buffer2) => {
      if (err2) throw new Error(err2);
      colors.push(numsToRGBColor([...buffer2]));

      crypto.randomFill(new Uint8Array(3), (err3, buffer3) => {
        if (err3) throw new Error(err3);
        colors.push(numsToRGBColor([...buffer3]));

        crypto.randomFill(new Uint8Array(3), (err4, buffer4) => {
          if (err4) throw new Error(err4);
          colors.push(numsToRGBColor([...buffer4]));

          useColorsCallback(colors);
        });
      });
    });
  });
};

get4RandomColors((colors) => console.log('colors:', colors));
```
## Analyzing the code
Blech, what a mess! Let's start by figuring out what this is really doing. First, let's break down what the `randomFill` function does. It looks like this:

```js
crypto.randomFill(new Uint8Array(3), (err, buffer) => {
  ...
});
```
It takes 2 arguments, a buffer structure (here, an array with 3 numbers between 0-255 in it, each representing a color), and a callback function. If there's an error it'll be the first argument of the callback, `err`. If there's no error, `err` will equal `null` and `buffer` will have the array of numbers in it. So far so good?

Then, it looks like we convert the buffer into an array by spreading it, and then passing it into a helper function that turns it into an RGB string.

We do this 4 times with callbacks, and then *finally* to use our 4 colors, we need to pass in a final callback to access the array. What a pain, this is the only way to get all 4 colors.

## "What!? WHY ARE YOU MAKING ME READ THIS WITH MY OWN EYES?"
Does this code confuse you? Honestly, that's ok! We intentionally chose something *wildly* over engineered (spoiler alert, you do not need `crypto` and buffers to make arrays with 3 numbers in them) because you will see confusing code *all* the time at work. The meta challenge here is to understand the code *enough* to get your job done. You absolutely don't need to understand how `crypto.randomFill` works or what `new Uint8Array(3)` does.

So take a deep breath, and start working out what you *do* need to understand. Carefully read through the starting code (call it, copy it into `playground`, whatever) and then once you have a handle on it, set out on modifying it. I think you'll be surprised at how well you do!

## Promises to the rescue!

Instead of using callback hell, promises keep everything linear by allowing you to chain `then`s and `catch`. Your mission is to use this code in `modify.js`:

```js
const crypto = require('crypto');

const numsToRGBColor = ([color1, color2, color3]) => {
  return `rgb(${color1}, ${color2}, ${color3})`;
};

const getRandomBytes = () => new Promise((resolve, reject) => {
  crypto.randomFill(new Uint8Array(3), (err, buffer) => {
    if (err) return reject(err);
    resolve([...buffer]);
  });
});

const return4RandomColors = () => {
  const colors = [];
  return getRandomBytes()
    .then(() => {
    })
    .then(() => {
    })
    .then(() => {
    })
    .then(() => {
    })
    .catch((err) => {
      console.error(err);
    });
};

return4RandomColors().then(console.log)
// logs the array with 4 colors [ 'rgb(105, 178, 206)' ... ]
```

...and make it so that `return4RandomColors` returns a promised array of `rgb` strings. Do not modify the given functions `numsToRGBColor` or `getRandomBytes` alright? Just read them and use them!

# Bonus
After you've gotten your tests passing and answered all your short answers, try researching `async/await`. **Don't use it on this assignment** (we want to see if you *get* the fundamentals first), but it's a modern way of writing promises with the `async` and `await` keywords. We'll use them later this week, but you can start reading about them now to get a leg up!
