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