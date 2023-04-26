```
┌───────────────────┐              ┌─────────────────┐
│                   │              │                 │
│            Socket │              │  App            │
│                   │   input      │                 │
│ Normal Pipe   ────┼──────────────┼──► ParseAckPipe │
│                   │              │                 │
│   AckPipeR    ◄───┼──────────────┼──── AckPipeW    │
│                   │   output     │                 │
└───────────────────┘              └─────────────────┘
```

Call `AckPipeReader.Ack()` in `ParseAckPipe.ReadAsync()` to release buffer

1. How frequently the app can receive bytes from socket impact how much bytes are buffered. If no return from other side, then we only rely on ping? Do we need more frequent ack from peer?
2. How to deal with duplication? Transport layer is difficult for handling duplication if we use byte offset. Can we rely on message invocation id? Or we only support at least once?

```
                      ┌───────────────
                ┌─────┼┤► Same Payload
                │     └───────────────
                │
                │
      Message   │     ┌───────────────
    ────────────┼─────┼┤► Same Payload
                │     └───────────────
                │
                │
                │     ┌───────────────
                └─────┼┤► Same Payload
                      └───────────────
```

3. How to deal bytes duplication when broadcasting? Is it a good practice to handle ack in byte pipe?
4. What's the sign of keep reconnecting or stop reconnecting. What if `_webSocket = await factory(context, cancellationToken).ConfigureAwait(false);` throws when reconnecting?
5.Security concern. Can you request a normal token to reconnect to a specific connection-id and take over?