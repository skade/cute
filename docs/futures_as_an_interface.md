# Futures as an interface

> I have looked into the future, everyone is slightly older.

-- Future of the Left -- The Plot Against Common Sense

As introduced in [The Road to Futures][road-to-futures], Futures abstract over _computation_. The part they abstract about is the "where" and the "when". Futures describe the "what".

For that, they aim to break code into small, composable actions that can then be executed by a part of our system. Let's take a tour through what it means to compute things to find where we can abstract.

## An easy view of computation

While computation is a subject to write a whole [book][understanding-computation] about, a very simplified view of them suffices for us:

* computation is a sequence of composable operations
* they can branch based on a decision
* they either run to succession and yield a result or they can yield an error

## Skip to the end

Usually, our focus when programming is that at any place in our program, we consider the previous work done. Everything that happened before might have worked or not. If you wrote more then "Hello, world!" in Rust, you've met our type for that:

```rust
enum Result<T,E> {
    Ok(T),
    Err(E)
}
```

`Result` describes the outcome of a previous computation. That's either `Ok` - it worked, and here's your resulting value - or `Err` - something didn't work out and here's a description of the error.

`Result`s are a good way to implement the semantics of computations from the past. But I wouldn't go as far as calling `Result`s in themselves an _abstractions_. `Result`s are a very concrete implementation of an idea, they are a value with very define semantics in Rust. Their behaviour is complex, but all spelled out in the standard library.

## Orienting towards the beginning

`Result`s talk about the past. The past has a drawback: all decisions have been made. It has an advantage: the outcome is visible. We can unwrap the presents of program past and then decide what to do with what's in it.

But here's a problem: we wanted to abstract over _computation_ to be allowed to let someone else choose how to run it. That's fundamentally incompatible with looking at the results of previous computation all the time. So, let's find a type that describes a computation without running it. Your first guess might be a function.

```rust
fn do_stuff() -> Result<T, E> {
    // ...
}
```

You can call that at any time, so you are in full control on when you call it. But here's the problem: the moment you call it, you transfer control to the called function. This is independent of the style of function you use, closure have the same issue.

Speaking in terms of time again, we can only take action _before_ calling the function or _after_ the function returned. This is not desireable, as it takes from us the ability to do something _while_ it runs. When working with parallel code, this would take from us the ability to start a parallel task while the first runs (because we gave away control).

This is the moment where we could reach for [threads][threads]. But threads are a very specific concurrency primitive and we said that we are searching for an abstraction.

What we are searching is something that represents ongoing work towards a result in the future. Whenever we say `something` in Rust, we almost always mean a trait. Let's start with an incomplete definition of the `Future` trait:

```rust
trait Future {
    type Item;
    type Error;
    
    fn wait(self) -> Result<Self::Item, Self::Error>;
}
```

Looking at it closely, we see the following: it is generic over two types, the `Item` and the `Error`. This closely resembles `Result`, and unsurprisingly, they end up in the Result that `wait(self)` returns. `wait(self)` must be explained: calling this method makes the caller wait until a `Future` has run to succession and returns the fruits of this computation as a `Result`. Calling `wait` _consumes_ the future, it cannot be used afterwards. 

Another curious symmetry is that the first associated type is called `Item`. There's another trait in Rust that uses this terminology: `Iterator`. The trait `Iterator` - in contrast to the value `Result` - also produces values based on given computation when `next()` is called.

There is a final piece missing: a way to check the Future for progress and retrieving its result, _without_ resorting to waiting for it to end. This process is polling.

## Poll

The final important method of a future is `poll()`. Its signature is as follows:

```rust
fn poll(&mut self) -> Poll<Self::Item, Self::Error>;
```

`Poll` is a type alias, defined as:

```rust
type Poll<T, E> = Result<Async<T>, Error>;
```

where `Async` is:


```rust
//TODO: auf Korrektheit checken
enum Async {
    Waiting,
    Finished(T)
}
```

Every call to `poll()` can result in one of these three cases:

1. An Error occurred. The Future has stopped running.
2. The Result is `Ok`, but wrapping `Async::Waiting`. The Future is still running and cannot give a result yet.
3. The Result is `Ok`, wrapping `Async::Finished`. The Future finished executing and the value can be read.

Note that calling `poll` after case 3 happened is undefined behaviour. We'll explain why later.

## Conclusion

Deriving generic interface over computation isn't too hard. Working from `Result`, we searched for something that expresses working towards `Result` available _sometime later_. We then defined a way to force that computation to run to succession: `wait`. From there, we added a way to inspect a running computation: `poll`. 

[road-to-futures]
[understanding-computation]
[threads]