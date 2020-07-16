This document explains how to perform tasks related to working with smart contracts with Terra.js.

## Uploading Code

You will first need a compiled WASM smart contract's binary to upload.

```ts
import { MsgStoreCode } from '@terra-money/terra.js';

const storeCode = new MsgStoreCode(

);
```

## Creating a Contract

For Terra smart contracts, there is a distinction between uploading contract code and creating a contract. This allows multiple contracts to share the same code if there are only minor variations in their logic which can be configured at contract creation. This configuration is passed in an **InitMsg**, and provides the initial state for the contract.

To create (instantiate) a smart contract, you must first know the code ID of an uploaded code. You will reference it in a `MsgInstantiateContract` alongside the InitMsg to create the contract. Upon successful creation, your contract will be located at an address that you specify.

```ts
import { MsgInstantiateContract } from '@terra-money/terra.js';

const instantiateContract = new MsgInstantiateContract(

);
```

## Executing a Contract

Smart contracts respond to JSON messages called **HandleMsg** which can exist as different types. The smart contract writer should provide any end-users of the smart contract with the expected format of all the varieties of HandleMsg the contract is supposed to understand, in the form of a JSON schema. The schema thus provides an analog to Ethereum contracts' ABI.

```
import { MsgExecuteContract } from '@terra-money/terra.js';

const executeContract = new MsgExecuteContract(

);
```


## Query Data from a Contract

```ts
const terra.wasm.contractQuery("", {});
```