---
layout: page
title: "References"
---

## RZG2L_PORT_PINMUX

```
/*
 * Create the pin index from its bank and position numbers and store in
 * the upper 16 bits the alternate function identifier
 */
#define RZG2L_PORT_PINMUX(b, p, f)      ((b) * RZG2L_PINS_PER_PORT + (p) | ((f) << 16))
```
