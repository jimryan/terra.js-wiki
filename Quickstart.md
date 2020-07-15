We highly recommend using TypeScript or JavaScript in a text editor with type hints to follow along.

## Introduction

Terra.js seeks to provide a compatible way to work with the Terra blockchain within JavaScript runtimes such as Node.js and the browser. The main functionalities of Terra.js are:

- deserializing blockchain data into JavaScript objects with native data types and methods
- serializing objects back into a blockchain-compatible format
- providing access to the `terrad` node API (LCD) from a JavaScript-based interface
- additional utilities such as hash functions and key signing algorithms

## Working with core data objects

Terra.js exposes nearly all of the relevant blockchain data classes from the root. Although many of the classes such as `Account` or `Validator` are used to store information from the blockchain rather than provide direct utility to the user, there will be some objects that will be used a lot.

### Numeric types

Terra.js includes `Dec` and `Int`, which represent decimal numbers and integer numbers, in a Cosmos-SDK compatible way.

```ts
import { Dec, Int } from '@terra-money/terra.js';

// conversion into dec
const d = new Dec(123.11);

// addition
d.add(3).sub(5).div(3).mod(2);
```

### Coin & Coins

A `Coin` represents a single coin, which is a pair of a denomination and amount. `Coins` represents a collection of `Coin` objects, that many operators

```ts
import { Coin, Coins } from '@terra-money/terra.js';

const c = new Coin('uluna', 1500000); // 1.5 LUNA
const c2 = new Coin('uluna', 3000000); // 3 LUNA
c.add(c2); // 4.5 LUNA

const cs = new Coins([c, c2]);
const cs2 = new Coins({ uluna: 12002, ukrw: 12399 });
cs2.map(x => console.log(`${x.denom}: ${x.amount}`));
```

### Creating a Key & Wallet

### Creating messages

### Signing and Broadcasting

## Other blockchain features