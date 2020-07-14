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

## Interacting with the Blockchain

There are two main modes by which a user can interact with the blockchain:

- accessing its data (querying)
- modifying its state (broadcasting transaction)

### Connecting to a chain

However, in order to perform these actions, a connection must first be made. You can connect to the blockchain by using an `LCDClient` object, which represents a connection to a node running LCD (Light Client Daemon), which serves a RESTful API over HTTP -- Terra.js will abstract away the details of making raw API calls and provide you a neat interface to work with.

```ts
import { LCDClient } from '@terra-money/terra.js';

const terra = new LCDClient({
   URL: 'https://lcd.terra.dev',
   chainID: 'columbus-3'
});
```

### Querying the blockchain

Once connected to the blockchain via an `LCDClient` instance, you can begin querying data from it. Data access is organized into various module APIs, which are accessible from within the `LCDClient` instance. Because they make HTTP requests in the background, they are Promises that can be awaited in order to not block during network IO.

```ts
async main() {
  const marketParams = await terra.market.params();
  const exchangeRates = await terra.oracle.exchangeRates();
  console.log(marketParams.base_pool);
  console.log(exchangeRates.get('uusd'));
}

main();
```

Each module has its own set of querying functions. You can explore the documentation to get a comprehensive list:

- [`auth`](https://terra-project.github.io/terra.js/classes/authapi.html)
- [`bank`](https://terra-project.github.io/terra.js/classes/bankapi.html)
- [`distribution`](https://terra-project.github.io/terra.js/classes/distributionapi.html)
- [`gov`](https://terra-project.github.io/terra.js/classes/distributionapi.html)
- [`market`](https://terra-project.github.io/terra.js/classes/marketapi.html)
- [`msgauth`](https://terra-project.github.io/terra.js/classes/msgauthapi.html)
- [`oracle`](https://terra-project.github.io/terra.js/classes/oracleapi.html)
- [`slashing`](https://terra-project.github.io/terra.js/classes/slashingapi.html)
- [`staking`](https://terra-project.github.io/terra.js/classes/stakingapi.html)
- [`supply`](https://terra-project.github.io/terra.js/classes/supplyapi.html)
- [`tendermint`](https://terra-project.github.io/terra.js/classes/tendermintapi.html)
- [`treasury`](https://terra-project.github.io/terra.js/classes/treasuryapi.html)
- [`tx`](https://terra-project.github.io/terra.js/classes/txapi.html)
- [`wasm`](https://terra-project.github.io/terra.js/classes/wasmapi.html)


### Creating a Key & Wallet

### Creating messages

### Signing and Broadcasting

## Other blockchain features