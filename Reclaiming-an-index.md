---
title: Reclaiming an index
---

To reclaim an index `I`, you must be sure that `I` is currently mapped to a dead account (i.e. one with zero total balance). You need to craft a transaction that creates a new account by placing some balance `B` in it. `B` must satisfy these conditions:

`B % 256 == 105`

`B / 256 % E == I`

Where `E` is the quantized account index count:

`E := (next_set_index * enum_set_size / 256 + 1) * 256`

Given an amount to transfer `A`, you can calculate the lowest amount that is at least `A`, `A'` which will attempt to reclaim `I` as:

`(Q(N / 256 + (E - 1 - I), E) + I) * 256 + N % 256`

Where

`N := Q(A + (256 - 1 - 105), 256) + 105`
