# mpesa-mz-nodejs-lib [Stable but not production-ready]

![Github](https://img.shields.io/github/package-json/v/ivanruby/mpesa-mz-nodejs-lib)
![Github](https://img.shields.io/npm/v/mpesa-mz-nodejs-lib)
![GitHub](https://img.shields.io/github/license/ivanruby/mpesa-nodejs-api)

A Node.js library for the M-Pesa Mozambique API

Initially, a port of [mpesa-php-api](https://github.com/abdulmueid/mpesa-php-api) to Node.js

## Features

- Simple syntax
- Promise-based
- Agnostic library. Treats config and transaction details as data, rather than dependencies
- Validation of transaction parameters to ensure only valid requests get sent to the MPesa API

## Status

- [x] C2B Transaction
- [x] Transaction status query
- [x] Transaction reversal

## Roadmap

- [x] Unit tests passing for all features (v0.4.x)
- [ ] Code refactoring (entire codebase) (v0.5.x)
- [ ] Documentation (code coverage, features) (v0.6.x)
- [ ] Linting, code quality (v0.7.x)
- [ ] Continuous Integration (v0.8.x)
- [ ] Rename library (v0.9.x)
- [ ] All tests passing, functionalities stable (v1.0.0)

## Installation

```
npm install mpesa-mz-nodejs-lib
```

## Examples

All parameters used in the examples correspond to the parameters required by the MPesa API. See the documentation on the [MPesa Developer Portal](https://developer.mpesa.vm.co.mz/) for more information.

### Customer to Business (C2B) transaction

```javascript
// include the library
Transaction = require('mpesa-mz-nodejs-lib')

// create the config object
let config = {
    public_key: '<Public key>',
    api_host: 'api.sandbox.vm.co.mz',
    api_key: '<API key>',
    origin: '<Origin>',
    service_provider_code: '<Service provider code>',
    initiator_identifier: '<Initiator Identifier>',
    security_credential: '<Security Credential>'
}

// instantiate the Transaction object, initializing it with valid config
transaction = new Transaction(config)

// initiate a promise-based C2B transaction
transaction.c2b({
    amount: <floating-point number>,
    msisdn: '<valid/invalid MSISDN>',
    reference: '<Transaction Reference>',
    third_party_reference: '<Third-party reference>'
  })
  // handle success
  .then(function(response){
      console.log(response)
  })
  // handle error
  .catch(function(error){
      console.log(error)
  })
```

### Querying status of an existing transaction

```javascript
// ... Assuming an initialized Transaction object

// query the status of an existing transaction
transaction
  .query({
    query_reference: "<Transaction reference>",
    third_party_reference: "<Third-party reference>",
  })
  // handle success
  .then(function (response) {
    console.log(response);
  })
  // handle error
  .catch(function (error) {
    console.log(error);
  });
```

### Reversal of an existing transaction

```javascript
// ... Assuming an initialized Transaction object

// Reverse a committed transaction
transaction
  .reverse({
    amount: "<floating-point number>",
    transaction_id: "<Transaction ID>",
    third_party_reference: "<Third-party reference>",
  })
  // handle success
  .then(function (response) {
    console.log(response);
  })
  // handle error
  .catch(function (error) {
    console.log(error);
  });
```

### Error-handling

The parameters for the initialization of the `Transaction` object as well as for the `c2b`, `query` and `reversal` methods are validated by the library.

If any parameter is non-existent, empty or invalid, the library throws the error:

`Missing or invalid Config/C2B/Query/Reversal parameters`

The names of the missing or invalid parameters are also appended to the error message.

Example

```js
Transaction = require("mpesa-mz-nodejs-lib");

// create the config object, missing api_host
let config = {
  public_key: "<Public key>",
  api_key: "<API key>",
  origin: "<Origin>",
  service_provider_code: "<Service provider code>",
  initiator_identifier: "<Initiator Identifier>",
  security_credential: "<Security Credential>",
};

// instantiate the Transaction object, initializing it with incomplete config
transaction = new Transaction(config);
```

Will throw a `Missing or invalid Configuration parameter: API Host` error

And

```js
Transaction = require('mpesa-mz-nodejs-lib')

// create the config object, missing api_host
let config = {
    public_key: '<Public key>',
    api_host: '<API host>',
    api_key: '<API key>',
    origin: '<Origin>',
    service_provider_code: '<Service provider code>',
    initiator_identifier: '<Initiator Identifier>',
    security_credential: '<Security Credential>'
}

// instantiate the Transaction object, initializing it with incomplete config
transaction = new Transaction(config)

// initiate a C2B transaction, missing reference parameter
transaction.c2b({
    amount: <floating-point number>,
    msisdn: '<valid/invalid MSISDN>',
    third_party_reference: '<Third-party reference>'
  })
  // handle success
  .then(function(response){
      console.log(response)
  })
  // handle error
  .catch(function(error){
      console.log(error)
  })
})
```

Will throw a `Missing or invalid C2B parameter: C2B Reference` error

### Responses

Response format from MPesa API:

```JS
{
  output_ResponseCode: 'INS-0',
  output_ResponseDesc: 'Request processed successfully',
  output_ResponseTransactionStatus: 'Completed',
  output_ConversationID: '3b46f68931324acb857ae4fe52b826b5',
  output_ThirdPartyReference: 'XXXXX'
}
```

The response format provided by the HTTP client library used, ([Axios](https://github.com/axios/axios)), is structured as:

```
{
  status: '',
  statusText: '',
  headers: {},
  config: {},
  data: {}
}
```

In the current version of the library, all returned objects correspond to the `data` property (data returned from MPesa API).
Future versions will distinguish `response` (full Axios response object) from `response.data` in returned messages according to the environment (dev/prod)

## License

[MIT License](LICENSE) &copy; 2020 Ivan Ruby
