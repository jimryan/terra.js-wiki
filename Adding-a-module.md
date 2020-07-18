Everytime a new module is added to Terra Core, that addition should also be reflected in Terra.js. There are several places that you need to update. The first thing you need to do is to create a new folder in the `src/core` folder with your module name:

- `src/core/greeting`

## Adding Messages

You will need to register new messages in a subdirectory in the folder that you created for your new module:

`src/core/greeting/msgs`

For this example, let's assume that you are creating a two new messages, `MsgHello` and `MsgGoodbye`. The example code for `MsgHello` is shown below, and you can extrapolate from it how a `MsgGoodbye` would be implemented.

`src/core/greeting/msgs/MsgHello.ts`

```ts
import { JSONSerializable } from '../../../util/json';
import { AccAddress } from '../../strings'

/**
 * Just a simple greeting on the blockchain.
 */
export class MsgHello extends JSONSerializable<MsgHello.Data> {
  constructor(
    public recipient: AccAddress
  ) {
    super();
  }

  public static fromData(data: MsgHello.Data): MsgHello {
    const {
      value: { recipient }
    } = data;
    return new MsgHello(recipient);
  }

  public toData(): MsgHello.Data {
    const { recipient } = this;
    return {
      type: 'greeting/MsgHello',
      value: {
        recipient
      },
    };
  }
}

export namespace MsgHello {
  export interface Data {
    type: 'greeting/MsgHello';
    value: {
      recipient: AccAddress
    };
  }
}

```

Then, create the following file, which will index your new messages.

`src/core/greeting/msgs/index.ts`

```ts
import { MsgHello } from './MsgHello';
import { MsgGoodbye } from './MsgGoodbye';

export * from './MsgHello';
export * from './MsgGoodbye';

export type GreetingMsg = MsgHello | MsgGoodbye;

export namespace GreetingMsg {
  export type Data = MsgHello.Data | MsgGoodbye.Data;
}
```

Finally, we need to register them in `src/core/Msg.ts`, so that they can be parsed correctly.

`src/core/Msg.ts`

```ts
// import greeting module messages
...
import {
  MsgHello,
  MsgGoodbye,
  GreetingMsg
} from './greeting/msgs';
...

// register GreetingMsg
export type Msg =
  | BankMsg
  | DistributionMsg
  | GovMsg
  | GreetingMsg
  | MarketMsg
  | MsgAuthMsg
  | OracleMsg
  | SlashingMsg
  | StakingMsg
  | WasmMsg;

...

// register GreetingMsg.Data
export namespace Msg {
  export type Data =
    | BankMsg.Data
    | DistributionMsg.Data
    | GovMsg.Data
    | Greeting.Data
    | MarketMsg.Data
    | MsgAuthMsg.Data
    | OracleMsg.Data
    | SlashingMsg.Data
    | StakingMsg.Data
    | WasmMsg.Data;
...

  // register deserializer in Msg.fromData(...)
  export function fromData(data: Msg.Data): Msg {
  ...
      // greeting
      case 'greeting/MsgHello':
        return MsgHello.fromData(data);
      case 'greeting/MsgGoodbye':
        return MsgGoodbye.fromData(data);
  ...
  }
}
```