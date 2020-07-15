This document explains how to influence the blockchain's state by broadcasting transactions.

Transactions include:

- a list of messages
- an optional memo
- a fee
- a signature from a key

The **messages** included in a transaction contain the information that will be routed to a proper message handler in the node, which in turn parses the inputs and determines the next state of the blockchain.

## Creating Transactions

### Create a Wallet

You will first want to create a wallet which you can use to sign transactions.

```ts
import { MnemonicKey, LCDClient } from '@terra-money/terra.js';

const mk = new MnemonicKey();
const terra = new LCDClient({
  URL: 'https://soju-lcd.terra.dev',
  chainId: 'soju-0014'
});
const wallet = terra.wallet(mk);
```

### Create Messages

```ts
import { MsgSend } from '@terra-money/terra.js';

const send = new MsgSend(
  wallet.address,
  "<random-terra-address>",
  { uluna: 1000 }
);
```

### Create and Sign Transaction

```ts
const tx = await wallet.createAndSignTx({
  msgs: [send],
  memo: "Hello"
});
```

### Broadcast Transaction

```ts
await terra.tx.broadcast(tx);
```