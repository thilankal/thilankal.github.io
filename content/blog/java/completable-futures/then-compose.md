---
title: "Completable Futures - thenApply vs thenCompose"
date: 2024-01-06T23:14:07+05:30
---

*Let's wrap a blocking operation in a CompletablFuture*
```java
public static Integer blockingCall(int x) {
    return x * 2;
}

public static CompletableFuture<Integer> asyncRunner(int x) {
    return CompletableFuture.supplyAsync(() -> blockingCall(x));
}
```

*Here we chain the outputs so that it prints the aggregated results. But this will print the future not the value (16)*
```java
public static void main(String[] args) {
    asyncRunner(4)
        .thenApply(data -> asyncRunner(data)) // returns CompletableFuture<Integer> yikes!  - analogous to map operation
        .thenAccept(System.out::println) // returns CompletableFuture<Void>
}
```

*But I want to return 16 as output but not wrapped in a CompletableFuture.*

*Solution: simply replace `thenApply` with `thenCompose`*

```java
asyncRunner(4)
        .thenACompose(data -> asyncRunner(data)) // returns 16 :)  - analogous to flatMap operation
        .thenAccept(System.out::println) // returns CompletableFuture<Void>
```

## Notes


Non-blocking operations may run in main thread or in a background thread depending on the duration of the operation!

- `runAsync` - For fire and forget tasks
- `supplyAsync` - Get a response back from the task - by default uses the ForkJoin pool
- `thenApply` - Just like the `map` method in `Stream` API
- `thenCompose` - Aanalogous to `flatMap` method in `Stream` API    