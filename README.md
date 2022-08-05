# Substrate tracing proposal

This proposal suggests a tracing system for substrate aiming at reliable
and precise indexing of blockchain transactions.

## The problem

In traditional ERP systems all reporting is centered around
history of individual transactions and state changes they implied.

In substrate, the notion of transaction is represented by `call`
and state is stored in a special data structure called `storage`,
but it is impossible to trace state changes back to calls
and even to get the call history itself.

Data indexers ought to rely on a special logging like mechanism called `events`,
however this is fundamentally error-prone and unreliable. 
First, the implementation can simply miss all required events. 
Second, the set of relevant events is always open, 
which means it is easy for data indexers to miss important state changes or actions.
Third, sometimes chain developers treat event mechanism in a creative way and
deposit events in a random order, which further complicates the matter.

## Built-in tracing

Substrate framework incorporates a built-in tracing system which could potentially solve the problem,
however it has the following shortcomings:

1. To enable tracing one has to create a specially compiled version of chain runtime
2. Produced traces are tailored towards debugging and are too ambiguous for automated interpretation.

## Proposal

We propose to implement a special tracing system in such a way, that

1. The tracing would be always available in all runtime compilation modes
2. The question of enabling the trace and were to put the produced data would be left to the hosting client
3. Produced trace would have a precisely defined and easy to interpret format in the vein of 
```
start of extrinsic (extrinsic data)
start of call (origin, arguments)
event emitted (arguments)
storage update (key, old_value, new_value)
start of call (origin, arguments)
storage update (key, old_value, new_value)
end of call (result)
end of call (result)
end of extrinsic (result)
```

We'd be happy to expand the proposal and make it a more technical, once there will be a some interest to implement it.
