## Specifying a Fee

```ts
import { StdFee } from '@terra-money/terra.js';

const fee = new StdFee(50000, { uluna: 4500000 });
const tx = await wallet.createAndSignTx({ msgs, fee });
```

## Automatic Fee Estimation

If you don't specify a fee when you create your transaction, it will automatically be estimated by simulating it within the node.

```ts
const tx = await wallet.createAndSignTx({ msgs });
```

You can define the fee estimation parameters when you create your `LCDClient` instance. The defaults are:

```ts
const terra = new LCDClient({
  URL: 'https://lcd.terra.dev',
  chainId: 'columbus-3',
  gasPrices: { uluna: 0.015 },
  gasAdjustment: 1.4
});
```

You can override these settings by estimating it yourself and specifying the resultant fee:

```ts
const fee = await terra.tx.estimateFee(tx, { gasPrices: { ukrw: 1, uluna: 5 } });
const tx = await wallet.createAndSignTx({ msgs, fee });
```
