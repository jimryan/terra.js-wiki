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
  | GreetingMsg // ADD HERE
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
    | Greeting.Data // ADD HERE
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

Finally, you will need to register the messages to be exported in `src/core/index.ts`:

```ts
...
// greeting
export 'greeting/msgs';
```

## Adding Parameter Changes

Terra.js provides an easy way to generate `ParameterChangeProposal`s, which is a proposal for changing the blockchain parameters associated with a module. If your module has parameters that can be changed via proposal, you should create the following files:

`src/core/greeting/params.ts`

```ts
import { ParamChange } from '..';
import { Convert } from '../../util/convert';

type MaxHellos = ParamChange.Type<
  'greeting',
  'maxhellos',
  number
>;

type MaxGoodbyes = ParamChange.Type<
  'greeting',
  'maxgoodbyes',
  number
>;

export type GreetingParamChange =
  | MaxHellos
  | MaxGoodbyes;

export namespace GreetingParamChange {
  export type Data =
    | ParamChange.Data.Type<MaxHellos>
    | ParamChange.Data.Type<MaxGoodbyes>
}

export interface GreetingParamChanges {
  greeting?: {
    maxhellos?: number;
    maxgoodbyes?: number;
  };
}

export namespace GreetingParamChanges {
  export const ConversionTable = {
    greeting: {
      maxhellos: [Convert.toNumber, Convert.toFixed],
      maxgoodbyes: [Convert.toNumber, Convert.toFixed],
    },
  };
}
```

Then register the parameter change types:

`src/core/params/ParamChange.ts`

```ts
...
import { GreetingParamChange, GreetingParamChanges } from '../greeting/params';
...

export type ParamChanges = DistributionParamChanges &
  GovParamChanges &
  GreetingParamChanges & // ADD HERE
  MarketParamChanges &
  OracleParamChanges &
  SlashingParamChanges &
  StakingParamChanges &
  TreasuryParamChanges &
  WasmParamChanges;

export namespace ParamChanges {
  export const ConversionTable = {
    ...DistributionParamChanges.ConversionTable,
    ...GovParamChanges.ConversionTable,
    ...GreetingParamChanges.ConverstionTable, // ADD HERE
    ...MarketParamChanges.ConversionTable,
    ...OracleParamChanges.ConversionTable,
    ...SlashingParamChanges.ConversionTable,
    ...StakingParamChanges.ConversionTable,
    ...TreasuryParamChanges.ConversionTable,
    ...WasmParamChanges.ConversionTable,
  };

...

export type ParamChange =
  | DistributionParamChange
  | GovParamChange
  | GreetingParamChange // ADD HERE
  | MarketParamChange
  | OracleParamChange
  | SlashingParamChange
  | StakingParamChange
  | TreasuryParamChange
  | WasmParamChange;

...

```


## Adding API to LCDClient

If there are API endpoints that exist for the new module, you will need to add this functionality to `LCDClient` so that they are accessible.

Assume that our `greeting` module has the following endpoints:

- `GET /greeting/hello/{accAddress}`
- `GET /greeting/parameters`

You will need to create `src/client/lcd/api/GreetingAPI.ts` with the following:

```ts
import { BaseAPI } from "./BaseAPI";
import { AccAddress } from "../../../core/strings";

export interface GreetingParams {
  max_hellos: number;
  max_goodbyes: number;
}

export namespace GreetingParams {
  export interface Data {
    max_hellos: string;
    max_goodbyes: string;
  }
}

export class GreetingAPI extends BaseAPI {
  public async hello(accAddress: AccAddress): Promise<AccAddress[]> {
    return this.c
      .get<AccAddress[]>(`/greeting/hello/${accAddress}`)
      .then(d => d.result);
  }

  public async parameters(): Promise<GreetingParams> {
    return this.c
      .get<GreetingParams.Data>(`/greeting/parameters`)
      .then(d => d.result)
      .then(d => ({
        max_hellos: Number.parseInt(d.max_hellos),
        max_goodbyes: Number.parseInt(d.max_goodbyes)
      }));
  }
}
```

Then, you need to register it inside `src/client/lcd/api/index.ts`:

```ts
export * from './AuthAPI';
export * from './BankAPI';
export * from './DistributionAPI';
export * from './GovAPI';
export * from './GreetingAPI'; // ADD HERE
export * from './MarketAPI';
export * from './MsgAuthAPI';
export * from './OracleAPI';
export * from './SlashingAPI';
export * from './StakingAPI';
export * from './SupplyAPI';
export * from './TendermintAPI';
export * from './TreasuryAPI';
export * from './TxAPI';
export * from './WasmAPI';
```

Finally, you need to add it to `src/client/lcd/LCDClient.ts`:

```ts
...
import {
  AuthAPI,
  BankAPI,
  DistributionAPI,
  GovAPI,
  GreetingAPI, // ADD HERE
  MarketAPI,
  MsgAuthAPI,
  OracleAPI,
  SlashingAPI,
  StakingAPI,
  SupplyAPI,
  TendermintAPI,
  TreasuryAPI,
  TxAPI,
  WasmAPI,
} from './api';
...


export class LCDClient {
  public config: LCDClientConfig;
  public apiRequester: APIRequester;

  // API access
  public auth: AuthAPI;
  public bank: BankAPI;
  public distribution: DistributionAPI;
  public gov: GovAPI;
  public greeting: GreetingAPI; // ADD HERE
  public market: MarketAPI;
  public msgauth: MsgAuthAPI;
  public oracle: OracleAPI;
  public slashing: SlashingAPI;
  public staking: StakingAPI;
  public supply: SupplyAPI;
  public tendermint: TendermintAPI;
  public treasury: TreasuryAPI;
  public wasm: WasmAPI;
  public tx: TxAPI;

  /**
   * Creates a new LCD client with the specified configuration.
   *
   * @param config LCD configuration
   */
  constructor(config: LCDClientConfig) {
    this.config = {
      ...DEFAULT_LCD_OPTIONS,
      ...config,
    };

    this.apiRequester = new APIRequester(this.config.URL);

    // instantiate APIs
    this.auth = new AuthAPI(this.apiRequester);
    this.bank = new BankAPI(this.apiRequester);
    this.distribution = new DistributionAPI(this.apiRequester);
    this.gov = new GovAPI(this.apiRequester);
    this.greeting = new GreetingAPI(this.apiRequester); // ADD HERE
    this.market = new MarketAPI(this.apiRequester);
    this.msgauth = new MsgAuthAPI(this.apiRequester);
    this.oracle = new OracleAPI(this.apiRequester);
    this.slashing = new SlashingAPI(this.apiRequester);
    this.staking = new StakingAPI(this.apiRequester);
    this.supply = new SupplyAPI(this.apiRequester);
    this.tendermint = new TendermintAPI(this.apiRequester);
    this.treasury = new TreasuryAPI(this.apiRequester);
    this.wasm = new WasmAPI(this.apiRequester);
    this.tx = new TxAPI(this);
  }
```