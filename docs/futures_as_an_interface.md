# Futures as an interface

> I have looked into the future, everyone is slightly older.

-- Future of the Left -- The Plot Against Common Sense

As introduced in [The Road to Futures][road-to-futures], Futures abstract over _computation_. The part they abstract about is the "where" and the "when". Futures describe the "what".

For that, they aim to break code into small, composable actions that can then be executed by a part of our system. Let's take a tour through what it means to compute things to find where we can abstract.

## An easy view of computation

While computation is a subject to write a whole [book][understanding-computation] about, a very simplified view of them suffices for us:

* computation is a sequence of composable operations
* they can branch based on a decision
* they either run to succession or they can yield an error

[road-to-futures]
[understanding-computation]
