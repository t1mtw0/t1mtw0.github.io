---
title: "Number Representations"
date: 2026-09-27
summary: "Number Representations"
tags: ["Computer Systems"]
---

## Unsigned Representations

This is simply traditional binary representations: $(b_{n-1} \ldots b_1b_0)_2 = \sum_{i=1}^{n}{2^{b_i}}$.

## Signed Representations

The traditional implementation is one's-complement: here we have $0$ to $2^{k-1} - 1$ equal to the standard unsigned representations, then loop back from $-(2^{k-1} - 1)$ back all the way to $-0$. In this representation $-0$ and $-0$ are technically different.

## Floating-Point Representations

We detail the IEEE standard here. In this standard we have three parts: $s$, consisting of one bit, $E$, the exponent, consisting of 8 bits for single-precision and 11 bits for double-precision, and finally, $f$, consisting of 23 bits for single-precision and 52 bits for double-precision. From here on out we assume singl-precision: double-precision can be done similarly.

### Normalized values

When $E \neq 0$ and $E \neq 255$. Then $E=e-Bias$, where $Bias = 2^{k-1} - 1$ (127 for single-precision). Thus exponent values range from $-126$ to $127$ (remember, we are excluding $0$ and $255$). $f$ is realized as the binary representation $0.f_{n-1} \ldots f_1f_0$, where $f_i$ are the bits in our representation.

### Denormalized values

This case occurs when $E$ consists of all zeros (i.e. $E=0$). In this case we put $E=1-Bias$ and $f=1.f_{n-1} \ldots f_1f_0$.

### Special values

In the case where $E=255$, we can have special values. If in addition, $f$ consists of all zeros, then the value represented is $Infinity$. If in addition $f$ is not all zeros, then the value represented is $NaN$ (not a number).