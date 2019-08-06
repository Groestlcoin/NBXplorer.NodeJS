# NBXplorer Client

A NodeJS client for NBXplorer.

```
npm install nbxplorer-client
```

## Usage

**FIRST READ THE NBXplorer DOCs AND UNDERSTAND HOW THE API WORKS**

### Bare minimum no-wallet methods

```js
const cli = new NBXClient({
  uri: 'https://nbx.example.com',
  cryptoCode: 'grs',
  cookieFilePath: '/home/user/.nbxplorer/Main/.cookie', // Only if noauth is not active
});
const txData = await cli.getTransactionNoWallet(txid);
const nbxStatus = await cli.getStatus();
const broadcastTxResponse = await cli.broadcastTx(txBuffer);
await cli.rescanTx([txArg, txArg, txArg]); // See NBX docs
const feeRate = await cli.getFeeRate(blockCount);
const events = await cli.getEvents();
const updatedPsbt = await cli.updatePsbt({ psbt: 'base64 string' })
// updatedPsbt will be the merged PSBT
// creating PSBT will require an HD wallet though
```

### Tracking address

```js
const cli = new NBXClient({
  uri: 'https://nbx.example.com',
  cryptoCode: 'grs',
  address: 'Fo5Xvgc58JMsMXsfwEY8TvjxX2x4Tdm5jf',
  cookieFilePath: '/home/user/.nbxplorer/Main/.cookie',
});
await cli.track(); // MUST DO ONCE PER LIFETIME OF THE WALLET
```

### Tracking HD (multisig OK)

**SEE API DOCS FOR derivationScheme FORMAT**

```js
const cli = new NBXClient({
  uri: 'https://nbx.example.com',
  cryptoCode: 'grs',
  derivationScheme: 'xpub661MyMwAqRbcEkSxvXEo7YjYynZAQ2MTEGgdKpUUXukRf33ymeCJasAS3g3QJoMmtCRPrJ1cvexQr7SF8W97dyjfR4miTksU9UtntYopk1B-[legacy]',
  cookieFilePath: '/home/user/.nbxplorer/Main/.cookie',
});
await cli.track(); // MUST DO ONCE PER LIFETIME OF THE WALLET
```

### Wallet methods (address OR derivationScheme)

```js
const txes = await cli.getTransactions()
const tx = await cli.getTransaction(txid) // must be related to wallet
const utxos = await cli.getUtxos()
```

### Wallet methods (derivationScheme ONLY)

```js
const addressInfo = await cli.getAddress()
const keyInfo = await cli.getExtPubKeyFromScript(scriptPubKeyHex) // must be related to wallet
await cli.scanWallet() // starts a scan of the utxo set (/utxo/scan endpoint)
const status = await cli.getScanStatus() // get status of scan
// createPsbt will throw an error if fee info is not available, use fallbackFeeRate just in case
const psbtData = await cli.createPsbt({destinations: [{ destination: address }]})
await cli.addMeta(key, value);
const meta = await cli.getMeta(key); // meta is an object with key `key` and value of type value passed to addMeta
await cli.removeMeta(key);
await cli.prune(); // prunes transactions of spent txos.
```
