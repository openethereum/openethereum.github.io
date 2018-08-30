---
title: Runtime
---

Runtime is a part of the code that defines the business logic of the chain.

It can be viewed as a state transition function for the chain. Essentially, it is an independent library with quite a thin API.

##TODO: Document functions that are required by Substrate.

All interaction between Substrate and runtime is done by calling one of these entrypoint functions. On the lowest level, these functions take a byte blob as an argument and return a byte blob as a result.

It's not convenient to work with byte blobs directly though, it's more desired to work with high-level objects (such as a block). So typically, the data that is passed around is encoded via substrate-codec.

This is so common so we have a special macro for that: [`impl_stubs!`](impl_stubs). It lets you write entrypoint functions as if they really take high-level objects while in reality they just deserialize from and serialize these object to byte blobs.

Runtime defines what data is [stored on the chain](decl_storage), how [extrinsics](Extrinsic) are interpreted and how state transitions are performed.

In this repository, you'll find tools for creating runtimes, but they are only for convenience and do not have to be used as long as the basic requirement that a runtime provides certain entry points are met.
