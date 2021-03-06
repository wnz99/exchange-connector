[![CircleCI](https://circleci.com/gh/wnz99/exchange-connector.svg?style=svg&circle-token=a889f103118facb3f2784375fb88bfe5bc3e3059)](https://circleci.com/gh/wnz99/exchange-connector)

# Exchange connector

An API to simplify interaction with token Exchanges.

Original code of [slyfox42](https://github.com/slyfox42) and [vshjxyz](https://github.com/vshjxyz) @ https://github.com/RigoBlock/rigoblock-monorepo/tree/master/packages/exchange-connector

## Importing the package

```javascript
import exchangeConnector, { supportedExchanges, NETWORKS, exchanges } from 'exchange-connector'
```

## How to use it

You can instantiate a new exchange using the `exchangeConnector` function, passing the exchange name as first parameter and an options object as second parameter.
The available options are: `networkdId` (defaults to 1), `httpUrl` and `wsUrl`. Supported relayers do not need the API's http url and websocket url to be specified, but they are needed when instantiating a 0x standard relayer.

To take advantage of the singleton, recommended is to have a separate file exporting a ExchangeConnector instance, and to import the instance in the files where we plan to use it.

example:

```javascript
// exchangeConnector.js
import ExchangeConnector from 'exchange-connector'

export default new ExchangeConnector()

// someOtherFile.js
import { supportedExchanges, NETWORKS } from 'exchange-connector'
import connector from './exchangeConnector.js'

const ethfinex = connector.getExchange(supportedExchanges.ETHFINEX, {
  networkId: NETWORKS.KOVAN
})

const tickersKovan = await ethfinex.http.getTickers({
  symbols: ['ZRXETH']
})
```

Some methods require specific parameters to be passed, these are saved under a public property `options` on the class instance.

```javascript
import {
  NETWORKS,
  supportedExchanges
} from 'exchange-connector'
import connector from './exchangeConnector.js'

const ethfinex = connector.getExchange(supportedExchanges.ETHFINEX, {
  networkId: NETWORKS.KOVAN
})

const orders = await ethfinex.http.getOrders({
  symbols: 'ZRXETH',
  precision: ethfinex.options.OrderPrecisions.P4
})
```

The first websocket method that gets invoked will create the connection, which is reused by subsequent calls. A callback is to be passed to these calls as a second parameter to be able to receive the data. All the websocket methods return an unsubscribe function that removes the even listener added to the connection.

To close the websocket connection, you can call the `close()` method.

```javascript
const unsubscribe = await ethfinex.ws.getTickers(
  { symbols: 'ZRXETH' },
  (error, data) => (error ? console.error(error) : console.log(data))
)
// later when you wish to stop listening
unsubscribe()

await ethfinex.ws.close()
```

'RAW' exchange classes will return data unfiltered and unformatted from the API, while non RAW ones will return the data formatted.

## Setup

### Available Scripts

In the project directory, you can run:

### `yarn build`

Builds the app for production to the `dist` folder.

### `yarn test:unit`

Runs tests with Jest.

### `yarn test:unit:watch`

Runs tests with Jest watching for changes.

### `yarn lint`

Lints all typescript files.