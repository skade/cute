# Cute - Learning Rust Futures through simple executors 

[Rust Futures][futures] have the reputation of being hard. I don't think this is the case. They are, in my opinion, one of the easiest concurrency concepts around and have an intuitive explanation.

However, there are good reasons for that perception. Futures have three concepts at their base that seem to be a constant source of confusion: deferred computation, asynchronicity and independence of execution strategy.

These concepts are not hard, but something many people are not used to. This base confusion is amplified by many implementations being very advanced and hard to understand. Most explanations of these implementations also target advanced users.

Futures are a concept that abstracts of how code is run. By themselves, they do nothing. This is a weird concept in an imperative language, where usually one thing happens after the other - right now.

So how do Futures run? It's up to you! Futures do nothing without the piece of code _executing_ them. This part is called an _executor_. An _executor_ decides _when_ and _how_ to execute your futures.
Usually, that decision for stategy happens by proxy of picking an implementation from [crates.io][crates]. The most common ones are [futures-cpupool][cpupool] and [tokio-core][tokio]. Both are very advanced and are highly recommendable. But both are not the best pieces of software to look at for learning purposes.

Cute is the implementation of an `executor` meant for understanding Futures. It prefers simple implementations over efficient ones, gives very detailed logging for every action to take and comes with a tutorial on how it was built.

Let's start with a little bit of motivational theory, though.

[crates]: https://crates.io
[cpupool]: 
[tokio]: 
[futures]: