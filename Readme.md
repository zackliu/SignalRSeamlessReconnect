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

1. How frequently the app can receive bytes from socket impact how much bytes are buffered. If no return from other side, then we only rely on ping?
1. Do we need any token when reconnecting (Currently we use the previous token which may expire)
1. How to deal with duplication? Transport layer is difficult for handling duplication if we use byte offset. We can rely on message invocation id.

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
1.How to deal bytes duplication when broadcasting? Is it promising to handle ack in byte pipe?