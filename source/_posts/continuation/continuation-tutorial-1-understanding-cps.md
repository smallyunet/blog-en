---
title: "Continuation Tutorial: Understanding CPS"
date: 2025-07-23 12:12:12
draft_date: 2025-07-19 01:10:18
tags:
- continuation
- Tutorial
---

> This is a continuation tutorial series:
> 1. [Continuation Tutorial: Understanding CPS](/2025/07/23/continuation-tutorial-1-understanding-cps/)
> 2. [Continuation Tutorial: Implementing Coroutine Scheduling with yield](/2025/07/23/continuation-tutorial-2-implementing-coroutine-scheduling-with-yield/)
> 3. [Continuation Tutorial: Implementing Coroutine Scheduling with call/cc](/2025/07/23/continuation-tutorial-3-implementing-coroutine-scheduling-with-call-cc/)
> 4. [Continuation Tutorial: Implementing Coroutine Scheduling with shift/reset](/2025/07/23/continuation-tutorial-4-implementing-coroutine-scheduling-with-shift-reset/)
> 5. [Continuation Tutorial: Exploring the Racket Language](/2025/07/23/continuation-tutorial-5-exploring-the-racket-language/)
> 6. [Continuation Tutorial: Implementing Preemptive Coroutine Scheduling](/2025/07/23/continuation-tutorial-6-implementing-preemptive-coroutine-scheduling/)

Let us study the principles of continuation and its application scenarios step by step, from shallow to deep. The content of this tutorial series is unrelated to Yin Wang's continuation workshop. It is the result of my own learning and research, so there are no copyright concerns. That said, it is precisely because I took the foundation course and built a solid foundation that I knew how to study continuation on my own and understand the concept properly. This article will reveal a small portion of the skills learned in the foundation course, because continuation is advanced material relative to that course, and there is no way to understand it while skipping the foundational skills.

### Recursion

Let us first write a factorial function `fact` in recursive form. We are already very familiar with this style, so it does not need much explanation:

```js
function fact(n)
{
  if (n === 0)
  {
    return 1;
  }
  else
  {
    return n * fact(n - 1);
  }
}

console.log("fact1=", fact(1)); // 1
console.log("fact3=", fact(3)); // 6
console.log("fact5=", fact(5)); // 120
```

### Tail recursion

Next, rewrite the `fact` function in tail-recursive form. Tail recursion adds one more parameter than plain recursion. This new parameter is used to carry the value accumulated by each call:

```js
function factTail(n, prod)
{
  if (n == 0)
  {
    return prod;
  }
  else
  {
    return factTail(n - 1, prod * n);
  }
}

console.log("factTail1=", factTail(1, 1)); // 1
console.log("factTail3=", factTail(3, 1)); // 6
console.log("factTail5=", factTail(5, 1)); // 120
```

### CPS form

Based on the tail-recursive form of `fact`, let us add another parameter `k`. This `k` is a function. Instead of returning the computed value directly, `fact` passes the result to `k`, like this:

```js
function factTailCPS(n, prod, k)
{
  if (n == 0)
  {
    return k(prod);
  }
  else
  {
    return factTailCPS(n - 1, prod * n, k);
  }
}

factTailCPS(1, 1, x => console.log("factTailCPS1=", x)); // 1
factTailCPS(3, 1, x => console.log("factTailCPS3=", x)); // 6
factTailCPS(5, 1, x => console.log("factTailCPS5=", x)); // 120
```

This `k` is the continuation. It tells the `fact` function how computation should continue after the result has been produced. No doubt about it: this function fully matches CPS, or Continuation-Passing Style.

### Typical CPS

However, using tail recursion together with a continuation parameter is clearly not concise enough, and it does not count as the typical CPS form. Typical CPS is harder to understand, so there is no need to derive it from scratch by yourself. Just look at this ready-made example, where we improve the recursive `fact` function:

```js
function factCPS(n, k)
{
  if (n == 0)
  {
    return k(1);
  }
  else
  {
    return factCPS(n - 1, r => k(n * r));
  }
}
```

This can feel a bit confusing at first, but do not panic. Let us break it down. First, `k` still represents the continuation, and `k` is a function. Then we call it like this:

```js
let factCPS1 = factCPS(0, x => x);
console.log("factCPS1=", factCPS1); // 1

let factCPS3 = factCPS(3, x => x);
console.log("factCPS3=", factCPS3); // 6

let factCPS5 = factCPS(5, x => x);
console.log("factCPS5=", factCPS5); // 120
```

The key is that when calling the function, the second argument is `x => x`. Once you combine that with the `r => k(n * r)` inside the function, it is very easy to get lost.

This really is the hardest part to understand. Let us use the factorial of 2 as an example and write out the step-by-step execution of `factCPS`. The technique used here is single-step substitution, which is taught in the very first lesson of the foundation course and is extremely helpful for understanding recursion. If you have been trained in the foundation course and built a solid base, it truly helps when you move on to more complex things, such as the CPS calls here:

```js
let factCPS2 = factCPS(2, x => x);
console.log("factCPS2=", factCPS2); // 2

// n=2, k=x=>x, return factCPS(1, r => k(2 * r));
  // n=1, k=r=>(x=>x)(2*r), return factCPS(0, r => k(1 * r));
    // n=0, k=r=>(r=>(x=>x)(2*r)(1*r)), return k(1);
      // k(1) = r=>(x=>x)(2*r)(1*1)
      //      = (x=>x)(2)
      //      = 2
```

Even though I have already broken it down according to the correct line of reasoning, it is still very hard to understand from the reader's perspective. Try unpacking it yourself and gradually understand how typical CPS executes. It is normal if it takes several hours to really understand these steps.

To summarize: each CPS call uses a closure to store the value computed at the current step. Tail recursion passes values directly through parameters, while CPS passes the value of the next step through a closure. That is the relationship between them. Of course, to understand this, you first need to know what a closure is. That is also a major focus of the foundation course, especially in the interpreter section where you implement the constructs for closures yourself. That gives you a very thorough understanding of closures.

### CPS for the fib function

The factorial function `fact` has one characteristic: it makes only one recursive call in the function body. Now let us look at the Fibonacci function `fib`, which makes two recursive calls in the function body. How should CPS handle that case?

The recursive definition of `fib` looks like this:

```js
function fib(n)
{
  if (n == 0)
  {
    return 0;
  }
  else if (n == 1)
  {
    return 1;
  }
  else
  {
    return fib(n - 1) + fib(n - 2);
  }
}

console.log("fib(2)=", fib(2)); // 1
console.log("fib(5)=", fib(5)); // 5
```

Here is the CPS version of `fib`. Then we can look at how it works:

```js
function fibCPS(n, k)
{
  if (n == 0)
  {
    return k(0);
  }
  else if (n == 1)
  {
    return k(1);
  }
  else
  {
    return fibCPS(n - 1, r1 => fibCPS(n - 2, r2 => k(r1 + r2)));
  }
}
```

As you can see, when two recursive calls are needed, CPS places the other recursive call inside the original `r => k(r)` function, so the second internal call becomes a subcall during the recursive call to `fib`.

The CPS form of `fib` is called like this:

```js
let fibCPS1 = fibCPS(1, x => x);
console.log("fibCPS1=", fibCPS1); // 1

let fibCPS2 = fibCPS(2, x => x);
console.log("fibCPS2=", fibCPS2); // 1

let fibCPS4 = fibCPS(4, x => x);
console.log("fibCPS4=", fibCPS4); // 3

let fibCPS5 = fibCPS(5, x => x);
console.log("fibCPS5=", fibCPS5); // 5
```

Let us use the Fibonacci number of 3 as an example and break down the exact execution steps. One thing to note is that this process is extremely complex, much more complex than the `fact` function. You really need to write it out yourself to fully understand it:

```js
let fibCPS3 = fibCPS(3, x => x);
console.log("fibCPS3=", fibCPS3); // 1+1=2

// n=3, k=x=>x,
       // return fibCPS(2, r1 => fibCPS(1, r2 => (x=>x)(r1+r2)));
// n=2, k=r1 => fibCPS(1, r2 => (x=>x)(r1+r2)),
       // return fibCPS(1, r1 => fibCPS(0, r2 => (r1 => fibCPS(1, r2 => (x=>x)(r1+r2)))(r1+r2)));
// n=1, k=r1 => fibCPS(0, r2 => (r1 => fibCPS(1, r2 => (x=>x)(r1+r2)))(r1+r2)),
       // return k(1)
       // return (r1 => fibCPS(0, r2 => (r1 => fibCPS(1, r2 => (x=>x)(r1+r2)))(r1+r2)))(1)
       // return fibCPS(0, r2 => (r1 => fibCPS(1, r2 => (x=>x)(r1+r2)))(1+r2))
          // n=0, k=r2 => (r1 => fibCPS(1, r2 => (x=>x)(r1+r2)))(1+r2)
              // return k(0)
              // return (r2 => (r1 => fibCPS(1, r2 => (x=>x)(r1+r2)))(1+r2))(0)
              // return (r1 => fibCPS(1, r2 => (x=>x)(r1+r2)))(1+0)
              // return fibCPS(1, r2 => (x=>x)(1+r2))
                  // n=1, k=r2 => (x=>x)(1+r2)
                  // return k(1)
                  // return (x=>x)(1+1)
                  // return 2
```

After working through `fact` and `fib`, we now know what CPS looks like and how its execution proceeds step by step. Understanding CPS is only the beginning. Next, we will use continuation to build more interesting programs.

### Exercise

Suppose we have a recursive `sumFrom` function that takes two parameters, `a` and `b`. Its job is to compute the value of `a+(a+1)+...+(b-1)+b`. For example, when the parameters are `1` and `4`, it computes `1+2+3+4`:

```js
function sumFrom(a, b)
{
  if (a == b)
  {
    return a;
  }
  else
  {
    return b + sumFrom(a, b - 1);
  }
}

console.log(sumFrom(1, 3)); // 6
console.log(sumFrom(2, 5)); // 14
```

The exercise is to transform `sumFrom` into CPS form. Fill in the blank in the `sumFromCPS` function so that the program matches the output in the test cases:

```js
function sumFromCPS(a, b, k)
{
  // ____
}

sumFromCPS(1, 3, x => console.log(x)); // 6
sumFromCPS(2, 5, x => console.log(x)); // 14
```

### Further reading

We have now experienced the process of manually transforming a recursive program into CPS form. In practice, there are also methods that can automatically transform code into CPS, which is what the legendary "Yin Wang 40 lines of code" is doing. You can refer to these two links for more:

- [Is Yin Wang's "40 lines of code" really as impressive as he says?](https://www.zhihu.com/question/20822815)
- [GTF - Great Teacher Friedman](https://www.yinwang.org/blog-cn/2012/07/04/dan-friedman)

Because automatic CPS transformation is fairly difficult, I do not plan to study or implement it myself.