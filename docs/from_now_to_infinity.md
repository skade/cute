# From now to infinity

A surprisingly difficult aspect about Futures is where in the future they point to. We can classify them im three rough categories.

## Now

A little unintuitive, but it is not unusual for Futures to resolve immediately. This allows wrapping of already computed values in an interface compatible with other futures.

This mostly happens through the `futures::{ok,err,result}` functions.