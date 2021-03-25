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

- [`auth`](https://terra-project.github.io/terra.js/classes/client_lcd_api_authapi.authapi.html)
- [`bank`](https://terra-project.github.io/terra.js/classes/client_lcd_api_bankapi.bankapi.html)
- [`distribution`](https://terra-project.github.io/terra.js/classes/client_lcd_api_distributionapi.distributionapi.html)
- [`gov`](https://terra-project.github.io/terra.js/classes/client_lcd_api_govapi.govapi.html)
- [`market`](https://terra-project.github.io/terra.js/classes/marketapi.html)
- [`mint`](https://terra-project.github.io/terra.js/classes/mintapi.html)
- [`msgauth`](https://terra-project.github.io/terra.js/classes/msgauthapi.html)
- [`oracle`](https://terra-project.github.io/terra.js/classes/oracleapi.html)
- [`slashing`](https://terra-project.github.io/terra.js/classes/slashingapi.html)
- [`staking`](https://terra-project.github.io/terra.js/classes/stakingapi.html)
- [`supply`](https://terra-project.github.io/terra.js/classes/supplyapi.html)
- [`tendermint`](https://terra-project.github.io/terra.js/classes/tendermintapi.html)
- [`treasury`](https://terra-project.github.io/terra.js/classes/treasuryapi.html)
- [`tx`](https://terra-project.github.io/terra.js/classes/txapi.html)
- [`wasm`](https://terra-project.github.io/terra.js/classes/wasmapi.html)
