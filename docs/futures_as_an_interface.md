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

You can call that at any time, so you are in full control on when you call it. But here's the problem: the moment you call it, you give up control.


[road-to-futures]
[understanding-computation]
