Once connected to the blockchain via an `LCDClient` instance, you can begin querying data from it. Data access is organized into various module APIs, which are accessible from within the `LCDClient` instance. Because they make HTTP requests in the background, they are Promises that can be awaited in order to not block during network IO.

```ts
async main() {
  const marketParams = await terra.market.parameters();
  const exchangeRates = await terra.oracle.exchangeRates();
  console.log(marketParams.base_pool);
  console.log(exchangeRates.get('uusd'));
}

main();
```

Each module has its own set of querying functions. You can explore the documentation to get a comprehensive list:

- [`auth`](https://terra-money.github.io/terra.js/classes/client_lcd_api_authapi.authapi.html)
- [`bank`](https://terra-money.github.io/terra.js/classes/client_lcd_api_bankapi.bankapi.html)
- [`distribution`](https://terra-money.github.io/terra.js/classes/client_lcd_api_distributionapi.distributionapi.html)
- [`gov`](https://terra-money.github.io/terra.js/classes/client_lcd_api_govapi.govapi.html)
- [`market`](https://terra-money.github.io/terra.js/classes/client_lcd_api_marketapi.marketapi.html)
- [`mint`](https://terra-money.github.io/terra.js/classes/client_lcd_api_mintapi.mintapi.html)
- [`msgauth`](https://terra-money.github.io/terra.js/classes/client_lcd_api_msgauthapi.msgauthapi.html)
- [`oracle`](https://terra-money.github.io/terra.js/classes/client_lcd_api_oracleapi.oracleapi.html)
- [`slashing`](https://terra-money.github.io/terra.js/classes/client_lcd_api_slashingapi.slashingapi.html)
- [`staking`](https://terra-money.github.io/terra.js/classes/client_lcd_api_stakingapi.stakingapi.html)
- [`supply`](https://terra-money.github.io/terra.js/classes/client_lcd_api_supplyapi.supplyapi.html)
- [`tendermint`](https://terra-money.github.io/terra.js/classes/client_lcd_api_tendermintapi.tendermintapi.html)
- [`treasury`](https://terra-money.github.io/terra.js/classes/client_lcd_api_treasuryapi.treasuryapi.html)
- [`tx`](https://terra-money.github.io/terra.js/classes/client_lcd_api_txapi.txapi.html)
- [`wasm`](https://terra-money.github.io/terra.js/classes/client_lcd_api_wasmapi.wasmapi.html)
