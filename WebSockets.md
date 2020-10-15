## `WebSocketClient`

Terra.js comes with `WebSocketClient`, which abstracts a subscription to Tendermint RPC's WebSocket endpoint. This requires access to a Terra node's RPC server, which may required privileged access as it exposes functions that can kill the node's operation. With LocalTerra, the WebSocket endpoint can be accessed at `ws://localhost:26657/websocket`.

```ts
import { LocalTerra, WebSocketClient } from '@terra-money/terra.js';

const wsclient = new WebSocketClient({
  URL: 'ws://localhost:26657/websocket',
});

const terra = new LocalTerra();

let count = 0;
wsclient.subscribe('NewBlock', {}, (_, socket) => {
  console.log(count);
  count += 1;

  if (count === 3) {
    socket.close();
  }
});

// send tracker
wsclient.subscribe('Tx', { 'message.action': 'send' }, data => {
  console.log('Send occured!');
  console.log(data.value);
});
```

### Supported Events

You can subscribe to the following recognized Tendermint events:

  - `CompleteProposal`
  - `Evidence`
  - `Lock`
  - `NewBlock`
  - `NewBlockHeader`
  - `NewRound`
  - `NewRoundStep`
  - `Polka`
  - `Relock`
  - `TimeoutPropose`
  - `TimeoutWait`
  - `Tx`
  - `Unlock`
  - `ValidatorSetUpdates`
  - `ValidBlock`
  - `Vote`

### Query

In order to specify the Tendermint query, the following syntax is used:

```ts
const tmQuery = {
  "message.action": "send", // 
  "tx.timestamp": [">=", new Date()],
  "store_code.abc": ["EXISTS"],
  "abc.xyz": ["CONTAINS", "terra1..."]
};

wsclient.subscribe('Tx', tmQuery, (data, socket) => {
  // do something with data ...
  socket.close(); // close the connection
});
```

The resultant query will be:

`tm.event='Tx' AND tx.timestamp >= 2020-12-12 AND store_code.abc EXISTS AND abc.xyz CONTAINS 'terra1...'`

### `subscribeTx`

It is a common use case to subscribe to transactions with a Tendermint query, such as listening for when specific addresses send or receive funds, or when specific events are triggered from within smart contracts. However, it is hard to extract data because the transaction result is encoded in Base64 Amino encoding. If you use `subscribeTx`, Terra.js will automatically inject the `txhash` into the resultant data value so you can more easily look up the transaction to decode it using `LCDClient`.

```ts
// swap tracker
wsclient.subscribeTx({ 'message.action': 'swap' }, async data => {
  console.log('Swap occured!');
  const txInfo = await terra.tx.txInfo(data.value.TxResult.txhash);
  if (txInfo.logs) {
    console.log(txInfo.logs[0].eventsByType);
  }
});
```