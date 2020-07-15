While a `Key` has the ability to sign transactions, you have seen that it can be tedious to build transactions because of the many steps involved. Fortunately, if you have an `LCDClient` instance, you can build a `Wallet` object from your `Key` and access more convenient methods for creating and signing transactions.

## Creating a Wallet

Use `LCDClient.wallet()` to create a `Wallet` from a `Key`.

```ts
import { LCDClient, MnemonicKey } from '@terra-money/terra.js';

const terra = new LCDClient({
  URL: 'https://lcd.terra.dev',
  chainId: 'columbus-3'
});

const mk = new MnemonicKey();
const wallet = terra.wallet(mk);
```

In the above example, we used a `MnemonicKey` for our wallet, but any type of `Key` implementation can be used instead.

## Usage

### Getting account number and sequence

A wallet is connected to the Terra blockchain and can poll the values of an account's account number and sequence directly:

```ts
console.log(await wallet.accountNumber());
console.log(await wallet.sequence());
```

### Creating transactions

A wallet makes it easy to create a transaction by automatically fetching the account number and sequence from the blockchain. The fee parameter is optional -- if you don't include it, Terra.js will automatically use your LCD's fee estimation settings to simulate the transaction within the node and include the resultant fee in your transaction.

```ts
const msgs = [ ... ]; // list of messages
const fee = StdFee(...); // optional fee

const unsignedTx = wallet.createTx({
  msgs,
  // fee, (optional)
  memo: 'this is optional'
});
```

You can then sign the transaction with the wallet's key, which will create a `StdTx` which you can later broadcast:

```ts
const tx = wallet.key.signTx(unsignedTx);
```

You can also use the convenience function `Wallet.createAndSignTx()`, which automatically generates a signed transaction to be broadcast:

```ts
const tx = wallet.createAndSignTx({
  msgs,
  fee,  
  memo: 'this is optional'
});
```