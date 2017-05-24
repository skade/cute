# The Road to Futures

A notable point about Rust is _fearless concurrency_. Also, Rust being a low-level language, it's about fearless concurrency _without picking a specific implementation strategy_. This means we _must_ abstract over the strategy, to allow choice _later_, if we want to have any way to share code between users of different strategies.

## Send and Sync

Luckily, concurrent Rust already has two well-known and effective concepts abstracting over sharing between Rust concurrent parts of a program: Send and Sync. It is notable that both abstract over _strategies_ of concurrent working, they don't come with an implementation. Also, they compose.

Send abstracts over passing data ina computation over to another concurrent computation (a receiver), losing access to it on the sender side. In many programming languages, this strategy is commonly implemented, but missing support from the language side expects you to keep up this behaviour yourself. This is a regular source of bugs: senders keeping handles to sent things around and maybe even working with them after sending. Rust mitigates this problem by making this behaviour known. Types can be `Send` or not (by implementing the appropriate marker trait), allowing or disallowing sending them around.

Note how I avoided any word like _"thread"_, but instead opted for "computation". The full power of `Send` (and subsequently also `Sync`) is that they relieve you of the burden of knowing _what_ shares. At the point of implementation you only need to know which method of sharing is appropriate for the type at hand. It keeps reasoning local and not influenced by whatever implementation the user of that type later uses.

`Sync` is about sharing data between two concurrent parts of a program. This is another common pattern: as writing to a memory location or reading while another party is writing is inherently unsafe, this access needs to be moderated through synchronisation.[1] There are many common ways of two parties to agree on not using the same part in memory at the same time, for example mutexes and spinlocks. Again, Rust gives you the option of (safely!) not caring. Rust gives you the ability to express that something _needs_ synchronisation while not being specific about the _how_.

`Send` and `Sync` can be composed in interesting fashions, but that's beyond the scope here.

To sum up: Rust gives us the ability to safely abstract over important properties of concurrent programs: their data sharing. It does so in a very lightweight fashion: the language itself only knows about the two markers `Send` and `Sync` and helps us a little by deriving them itself, when possible. The rest is a library concern.

## Further

But we're not at the end of the road yet. `Send` and `Sync` are about data. But while programs are about data, they are also about what we do with the data: _computation_. And that's what [Futures][futures] do. We are going to have a close look at how that works in the next chapter. I'm a huge fan of working on your language, so here's my plain english explanation of futures. Futures go from this plan:

* Do X
* If X succeeds, do Y

towards

* Start doing X
* Once X succeeds, start doing Y

Remember the talk about "deferred computation" in the intro? That's all it is. Instead of telling the computer what to execute and decide upon _now_, you tell it what to do at some point in the... well... `Future`.

[1]: Two parties reading while it is guaranteed that no one is writing is always safe.

[futures]: https://docs.rs/futures/0.1/futures/
