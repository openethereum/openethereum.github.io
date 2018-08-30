---
title: Light client
---

As opposed to a [full client](Substrate-Full-Client), _light client_ is an operating mode where only the absolute minimum is stored on the disk.

This mode is especially handy for mobile applications and allows users to synchronize and perform transactions within a reasonable amount of time.

Practice showed that it's not a trivial task to add light client support to an existing blockchain implementation. For that reason, [Substrate](Parity-Substrate) was specifically designed with light client support in mind right from the start, so that blockchains that were built on top of Substrate would have light client support almost out of the box.

TODO: Substrate specifics.
