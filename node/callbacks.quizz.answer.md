# Answer 1

This was a trick question.
Callbacks are not async by default, this was a sync call so the message var was not defined by the time the callback was called.
To make this work we need to wrap our call to `cb` in either a `setImmediate` or a `process.nextTick`.

```js
function doAsyncTask(cb) {
  // cb();

  setImmediate(() => {
    console.log("setImmediate Task Calling Callback");
    cb();
  }, 1);

  process.nextTick(() => {
    console.log("Next Tick Task Calling Callback");
    cb();
  });
}

doAsyncTask(() => console.log(message));

let message = "Callback Called";
console.log("This is logged before the timeout");
//looking at this (the code before the solution has been added) at first glance, it should work. However as the requirement of the question is to not move the position of the let statment, code needs to be added to make that run first so that it is defined by the time the function is called.
```

# Answer 2

```js
const fs = require("fs");

function readFileThenDo(next) {
  fs.readFile("./blah.nofile", (err, data) => {
    if (err) {
      next(err);
    } else {
      next(null, data);
    }
  });
}

readFileThenDo((err, data) => {
  if (err) {
    console.error(err);
  } else {
    console.log(data);
  }
});
```

# Answer 3

Or if the error is serious, you can throw the error as soon as you see it.

try..catch desn't work as you expect with callbacks, it only really works with synchronous code.

By the time the callback throws the error we have moved on from the try..catch, the throw happens in the root scope and will just cause the program to exit.

```js
const fs = require("fs");

function readFileThenDo(next) {
  fs.readFile("./blah.nofile", (err, data) => {
    if (err) throw err;
    next(null, data);
  });
}

try {
  readFileThenDo((_, data) => console.log(data));
} catch (err) {
  console.error(err);
}
```
