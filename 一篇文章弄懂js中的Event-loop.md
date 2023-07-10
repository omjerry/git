# 一篇文章弄懂js中的Event-loop

JavaScript 的事件队列是一种机制，用于管理和执行异步任务。当涉及到异步操作时，例如定时器、事件处理程序、Promise、Ajax 请求等，这些任务会被添加到事件队列中，按照特定的顺序依次执行。

事件队列遵循事件循环的模型，分为主线程和任务队列两部分。主线程用于执行同步代码，而异步任务则被放置在任务队列中。当主线程中的同步代码执行完毕时，它会检查任务队列，将任务按照先进先出的顺序逐个执行。

具体来说，当事件队列中的任务被触发或满足执行条件时，它们会被添加到任务队列中，等待执行。主线程在空闲时，会从任务队列中取出一个任务并执行。这个过程会不断重复，形成了事件循环。

在事件队列中，有两种类型的任务：宏任务（macrotasks）和微任务（microtasks）。

- 宏任务包括定时器回调、事件回调、Ajax 请求的回调等。它们在每一轮事件循环中只执行一个任务，执行完成后，才会执行下一个宏任务。
- 微任务是一个更细粒度的任务，它包括 Promise 的回调函数、`MutationObserver` 的回调函数等。它们在宏任务执行完毕后立即执行，即在当前事件循环中的微任务队列中依次执行。

## 面试题

首先,来看一下这个比较典型的面试题:

```js
const myPromise=Promise.resolve(Promise.resolve('Promise!'))
function funcOne(){
    myPromise.then(res=>res).then(res=>console.log(res));
    setTimeout(() => {
        console.log('timeout!');
        
    }, 0);
    console.log('last line!');
}
async function funcTwo() {
    const res=await myPromise
    console.log(await res);
    setTimeout(() => {
        console.log('timeout!');
        
    }, 0);
    console.log('last line');
}
funcOne()
funcTwo()
```

## 分析代码

首先，让我们逐行分析给定的代码，并说明每一行的作用：

```
javascriptCopy code
const myPromise = Promise.resolve(Promise.resolve('Promise!'));
```

在这一行，我们创建了一个被解析为'Promise!'的Promise对象，并将其分配给名为`myPromise`的变量。

```
javascriptCopy codefunction funcOne() {
  myPromise
    .then(res => res)
    .then(res => console.log(res));
  setTimeout(() => {
    console.log('timeout!');
  }, 0);
  console.log('last line!');
}
```

在这个函数中，我们定义了一个名为`funcOne`的函数。在函数内部，我们使用`myPromise`调用了两个`.then()`方法。这些方法将在Promise被解析后按顺序执行，并将结果传递给下一个`.then()`方法。在第二个`.then()`方法中，我们通过`console.log()`输出了结果。

接下来，我们使用`setTimeout()`函数创建了一个延时为0毫秒的定时器，并输出字符串'timeout!'。最后一行使用`console.log()`输出字符串'last line!'。

```
javascriptCopy codeasync function funcTwo() {
  const res = await myPromise;
  console.log(await res);
  setTimeout(() => {
    console.log('timeout!');
  }, 0);
  console.log('last line');
}
```

在这个函数中，我们定义了一个名为`funcTwo`的异步函数。在函数内部，我们使用`await`关键字等待`myPromise`的解析结果，并将结果分配给变量`res`。然后，我们使用`console.log()`输出了`res`的值。

接下来，我们使用`setTimeout()`函数创建了一个延时为0毫秒的定时器，并输出字符串'timeout!'。最后一行使用`console.log()`输出字符串'last line'。

```
javascriptCopy codefuncOne();
funcTwo();
```

在最后几行，我们分别调用了函数`funcOne()`和`funcTwo()`。

现在，我们来分析事件队列是如何执行给定的代码的。

1. 当调用`funcOne()`时，以下操作发生：
   - `myPromise.then(res => res).then(res => console.log(res));`被添加到事件队列中，但尚未执行。
   - `setTimeout(() => console.log('timeout!'), 0);`被添加到事件队列中，但尚未执行。
   - `console.log('last line!');`直接执行，并输出'last line!'。
2. 当调用`funcTwo()`时，以下操作发生：
   - `myPromise`被等待并解析为`res`。
   - `console.log(await res);`直接执行，并输出'Promise!'。
   - `setTimeout(() => console.log('timeout!'), 0);`被添加到事件队列中，但尚未执行。
   - `console.log('last line');`直接执行，并输出'last line'。

现在，让我们来分析事件队列的执行顺序：

1. 首先，主线程执行同步代码，并输出'last line!'。
2. 然后，事件队列中的任务开始执行：
   - 首先，执行`myPromise.then(res => res).then(res => console.log(res));`中的`.then()`链。由于`myPromise`是一个立即解析为'Promise!'的Promise对象，第一个`.then()`返回`'Promise!'`。第二个`.then()`将`'Promise!'`作为参数，并执行`console.log(res)`，输出'Promise!'。
   - 接着,执行队列中的同步代码,输出''last line!'。
   - 接着，执行第一个`setTimeout()`的回调函数，输出'timeout!'。
   - 最后，执行第二个`setTimeout()`的回调函数，输出'timeout!'。

综上所述，事件队列的执行顺序如下：

1. 同步代码执行。
2. `.then()`链中的代码执行。
3. 第一个`setTimeout()`的回调执行。
4. 第二个`setTimeout()`的回调执行。

## 总结

总的来说，事件队列的运作方式是：主线程执行同步代码，遇到异步任务时将其放入相应的任务队列中。当主线程空闲时，会从任务队列中取出任务执行。这个过程不断重复，直到任务队列为空。