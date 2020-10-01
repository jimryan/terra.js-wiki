**NOTE:** The API for Station Extension is undergoing rapid development and is highly unstable. If you are developing a DApp, please check regularly for updates as breaking changes may be introduced frequently.

## What is Station Extension?

Terra Station Extension is a web-wallet extension for Chrome that enables webpages to create requests for signing and broadcasting transactions. The webpage can detect the presence of Station Extension and generate a prompt whereby the user can confirm the transaction to be signed.

## Connecting

```ts
import { Extension, Wallet } from "@terra-money/@terra.js";

let wallet: Wallet;
const extension = Extension();
extension.connect();
extension.on("connect", (w: Wallet) => {
  w = wallet;
});
```

## Signing a Message

```ts
import { MsgSend } from "@terra-money/terra.js";

extension.post({
  msgs: []
});
```