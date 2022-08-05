# Substrate tracing proposal

This proposal suggests a tracing system for Substrate aiming at a reliable
and accurate indexing of the blockchain historical state.

## The problem

In traditional ERP systems all the reporting is based on
the history of individual transactions and the state changes they implied.

In Substrate, the notion of a transaction is represented by `call`
and the state is stored in a special data structure called `storage`.
It is impossible, however, to trace the state changes back to the calls
and even to obtain the call stack.

Data indexers ought to rely on a special logging like mechanism provided by `events`,
however this is fundamentally error-prone and unreliable. 
First, the implementation can simply miss all the required events reconstruct the state. 
Second, the set of relevant events is always open, 
which means it is easy for data indexers to miss important state changes or actions.
Third, sometimes chain developers treat the event mechanism in a creative way and
emit events in a random order, which further complicates the matter.

## Built-in tracing

The Substrate framework incorporates a built-in tracing system which could potentially solve the problem,
however it has the following shortcomings:

1. To enable tracing one has to create a specially compiled version of the chain runtime
2. The output traces are tailored towards runtime debugging and are too ambiguous for automated interpretation and indexing.

## Proposal

We suggest implementing a special tracing system in such a way, that

1. The tracing would be always available in all runtime compilation modes
2. The question of enabling the trace and were to put the produced data would be left to the hosting client
3. The produced trace would have a precisely defined and easy to interpret format in the vein of 
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

We'd be happy to expand the proposal and provide more technical details, as long as there is some interest to implement it.
