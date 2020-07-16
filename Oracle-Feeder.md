The following code segment demonstrates how to create a simple price oracle feeder with Terra.js, using a [LocalTerra](https://github.com/terra-project/LocalTerra) network. The prices aren't live in this example -- it just tracks the public LCD node's current registered oracle price and submits them to the local testnet.

Note the following:

1. Prevotes are automatically generated using `MsgExchangeRateVote.getPrevote()`

2. `MsgExchangeRateVote` messages must precede `MsgExchangeRatePrevote` messages in sequence inside a transaction otherwise the `MsgExchangeRatePrevote` would override the current registered outstanding prevote for the validator.

### Pseudo Feeder 

```ts
import {
  LCDClient,
  MnemonicKey,
  MsgExchangeRateVote,
} from "@terra-money/terra.js";
​
async function main() {
  const mainnetClient = new LCDClient({
    URL: "https://lcd.terra.dev/",
    chainID: "columbus-3",
  });
​
  const testnetClient = new LCDClient({
    URL: "http://localhost:1317/",
    chainID: "localterra",
  });
​
  const mk = new MnemonicKey({
    mnemonic:
      "satisfy adjust timber high purchase tuition stool faith fine install that you unaware feed domain license impose boss human eager hat rent enjoy dawn",
  });
  const wallet = testnetClient.wallet(mk);
​
  let lastSuccessVotePeriod: number;
  let lastVotePeriodVoteMsgs: MsgExchangeRateVote[] = [];
​
  while (true) {
    const [rates, oracleParams, latestBlock] = await Promise.all([
      mainnetClient.oracle.exchangeRates(),
      testnetClient.oracle.parameters(),
      testnetClient.tendermint.block(),
    ]);
​
    const oracleVotePeriod = oracleParams.vote_period;
    const currentBlockHeight = parseInt(latestBlock.block.header.height, 10);
    const currentVotePeriod = Math.floor(currentBlockHeight / oracleVotePeriod);
    const indexInVotePeriod = currentBlockHeight % oracleVotePeriod;
​
    if (
      (lastSuccessVotePeriod && lastSuccessVotePeriod === currentVotePeriod) ||
      indexInVotePeriod >= oracleVotePeriod - 1
    ) {
      continue;
    }
​
    const voteMsgs: MsgExchangeRateVote[] = rates
      .toArray()
      .map(
        (r) =>
          new MsgExchangeRateVote(
            r.amount,
            r.denom,
            "salt",
            mk.accAddress,
            mk.valAddress
          )
      );
​
    const prevoteMsgs = voteMsgs.map((vm) => vm.getPrevote());
    const msgs = [...lastVotePeriodVoteMsgs, ...prevoteMsgs];
    const tx = await wallet.createAndSignTx({ msgs });
​
    const result = await testnetClient.tx.broadcast(tx);
    console.log(`vote_period: ${currentVotePeriod}, txhash: ${result.txhash}`);
​
    lastSuccessVotePeriod = currentVotePeriod;
    lastVotePeriodVoteMsgs = voteMsgs;
    await new Promise((resolve) => setTimeout(resolve, 1000));
  }
}
​
main().catch(console.error);
```