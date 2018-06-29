---
title: Bluzelle API Reference

language_tabs: # must be one of https://git.io/vQNgJ

toc_footers:

includes:


search: true
---

# Introduction

Bluzelle combines the sharing economy with the token economy - renting individuals' computer storage space to earn tokens while dApp developers use tokens to have their dApp's data stored and manage d.

This guide covers API for our JavaScript & Python libraries, Solidity contracts, and raw WebSocket communications.



## CRUD Application

Bluzelle comes with a CRUD (Create, Read, Update, Delete, etc.) application that is built with the Bluzelle libraries. 

You can find binaries in our [OSX](https://bluzelle.jfrog.io/bluzelle/list/OSX/) and [Debian](https://bluzelle.jfrog.io/bluzelle/list/debian-local/) repositories, or build the project yourself from the [GitHub repository](https://github.com/bluzelle/crud).


# JS API

## Installation

> Our client JavaScript library can be installed through `npm`.

```javascript
npm install bluzelle
```

> You import *Bluzelle* into your application the same as any other library.

```javascript
const bluzelle = require('bluzelle');
```

This portion of the documentation covers our **JavaScript** tools. Each function in the JavaScript API wraps a request-response pair in the WebSocket API.



## Sample Application

```javascript
const bluzelle = require('bluzelle');
  
bluzelle.connect('ws://testnet.bluzelle.com:51010', '45498479-2447-47a6-8c36-efa5d251a283');


bluzelle.create('myKey', 'myValue').then(() => {

    bluzelle.read('myKey').then(value => {

        console.log(value); // 'myValue'

    }).catch(e => console.log(e.message));

}).catch(e => console.log(e.message));
```


The Bluzelle JavaScript library works with promises to model asynchronous behavior. Ensure that dependent calls to the Bluzelle database are within `.then()` calls as in the sample application to the right. Also ensure that promises errors are caught and handled.

You can also program using [ES6 async/await syntax](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function) to avoid excessive use of `.then()`.



--------------


## connect(ws, uuid)


```javascript
bluzelle.connect('ws://1.2.3.4:51010', '96764e2f-2273-4404-97c0-a05b5e36ea66');
```


```python
b = pyBluzelle.create_connection("127.0.0.1", 51011, "137a8403-52ec-43b7-8083-91391d4c5e67")
```

Configures the address, port, and UUID of the connection. This may be called multiple times, and between other API calls.

*Bluzelle* uses `UUID`'s to identify distinct databases on a single swarm. We recommend using <a href="https://en.wikipedia.org/wiki/Universally_unique_identifier#Version_4_(random)">Version 4 of the universally unique identifier</a>.


Argument  | Description
----------|------------
ws       | The WebSocket entry point to connect to.
uuid     | The universally unique identifier (UUID), <a href="https://en.wikipedia.org/wiki/Universally_unique_identifier#Version_4_(random)">Version 4 is recommended</a>


### Returns

Nothing



<aside class="warning">
You must replace <code>UUID</code> with your personal UUID.
</aside>


## create(key, value)


```javascript
// promise syntax
bluzelle.create('mykey', { a: 13 }).then(() => { ... }, error => { ... });

// async/await syntax
await bluzelle.create('mykey', { a: 13 });
```


Creates a field. *Bluzelle* supports JavaScript types that are `JSON`-serializable (strings, objects, numbers, booleans, arrays, and combinations theereof) and also serial data in the form of an <a href="">ArrayBuffer</a>. Functions are not valid.



Argument  | Description
----------|------------
key       | The name of the key
value     | The value to set the key


### Returns

A [promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) resolving to no value.


### Fail Conditions

Fails when a response is not received from the connection or invalid value.



## read(key)

```javascript
// promise syntax
bluzelle.read('mykey').then(value => { ... }, error => { ... });

// async/await syntax
const value = await bluzelle.read('mykey');
```


Retrieve the value of a key.


Argument  | Description
----------|------------
key      |  The key to retrieve.


### Returns

A [promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) resolving the value of the key. This is anything that is valid in the *Bluzelle* database. (See <a href="#update-key-value">`update(key, value)`</a>)


### Fail Conditions

Fails when a response is not recieved or the key does not exist in the database.


## update(key, value)

```javascript
// promise syntax
bluzelle.update('mykey', { a: 13 }).then(() => { ... }, error => { ... });

// async/await syntax
await bluzelle.update('mykey', { a: 13 });
```


> Serial data example.

```javascript

const arr = new Uint8Array(1000);

// Fill arr with data

await bluzelle.update('mykey', arr);
```


Update a field in the database.


Argument  | Description
----------|------------
key       | The name of the key to query
value     | The value to set the key


### Returns

A [promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) resolving to nothing.


### Fail Conditions

Fails when a response is not received from the connection or invalid value.



## remove(key)


```javascript
// promise syntax
bluzelle.remove('mykey').then(() => { ... }, error => { ... });

// async/await syntax
await bluzelle.remove('mykey');
```


Deletes a field from the database.


Argument  | Description
----------|------------
key       | The name of the key to delete.


### Returns

A [promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) resolving to nothing.


### Fail Conditions

Fails when a response is not received from the connection or key not in database.


## has(key)


```javascript
// promise syntax
bluzelle.has('mykey').then(hasMyKey => { ... }, error => { ... });

// async/await syntax
const hasMyKey = await bluzelle.has('mykey');
```


Query to see if a key is in the database.


Argument  | Description
----------|------------
key       | The name of the key to query


### Returns

A [promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) resolving to a boolean value, `true` of `false`, representing whether or not the key is in the database.


### Fail Conditions

Fails when a response is not received from the connection.



## keys()

```javascript
// promise syntax
bluzelle.keys().then(keys => { ... }, error => { ... });

// async/await syntax
const keys = await bluzelle.keys();
```


Retrieve a list of all keys.


Argument  | Description
----------|------------


### Returns

A [promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) resolving to an array of strings representing keys in the database. ex. `["Key1", "Key2", ...]`


### Fail Conditions

Fails when a response is not received from the connection.



--------------------





# Python API

## Installation

```python
import pyBluzelle
```

Please refer to the instructions [here](https://github.com/bluzelle/pyBluzelle).




--------------


## connect(host, port, uuid)


```python
b = pyBluzelle.create_connection(
    "127.0.0.1", 
    51011, 
    "137a8403-52ec-43b7-8083-91391d4c5e67")
```

Configures the address, port, and UUID of the connection.

*Bluzelle* uses `UUID`'s to identify distinct databases on a single swarm. We recommend using <a href="https://en.wikipedia.org/wiki/Universally_unique_identifier#Version_4_(random)">Version 4 of the universally unique identifier</a>.


Argument  | Description
----------|------------
host       | The host name of the entry point.
port      | The port of the entry point.
uuid     | The universally unique identifier (UUID), <a href="https://en.wikipedia.org/wiki/Universally_unique_identifier#Version_4_(random)">Version 4 is recommended</a>


### Returns

A connection object.



## create(key, value)


```python
b.create("myKey", "myValue")
```


Creates a field.



Argument  | Description
----------|------------
key       | The name of the key
value     | The value to set the key


### Returns

`True` if successful, `False` otherwise.


## read(key)

```python
b.read('myKey');
```


Retrieve the value of a key.


Argument  | Description
----------|------------
key      |  The key to retrieve.


### Returns

The value of the key, `None` otherwise.




## update(key, value)

```javascript
b.update('myKey', 'myNewValue')
```


Updates a value in the database.


Argument  | Description
----------|------------
key       | The name of the key to query
value     | The value to set the key


### Returns

`True` if successful, `False` otherwise.




## delete(key)


```javascript
b.remove('myKey')
```


Deletes a field from the database.


Argument  | Description
----------|------------
key       | The name of the key to delete.


### Returns

`True` if successful, `False` otherwise.




## has(key)


```javascript
b.has('myKey')
```


Query to see if a key is in the database.


Argument  | Description
----------|------------
key       | The name of the key to query


### Returns

`True` if they key is in the database, `False` otherwise.



## keys()

```javascript
b.keys()
```


Retrieve a list of all keys.


Argument  | Description
----------|------------


### Returns

A list of keys in the database.




# Solidity API

Please refer to the documentation of the solidity API [here](https://github.com/bluzelle/eth-oracle).




# WebSocket API

The *Bluzelle* architecture consists of a request-response system through WebSockets.

> WebSocket database messages are JSON strings with embedded base64-encoded serial data.

```json-doc
{
    "bzn-api": "database",
    "msg": ""
}
```

> The JSON is considered static except for the value of the "msg" field, which may vary.

Database requests are [protobuf](https://github.com/google/protobuf) serial output embedded in JSON. The master database protofile is given [here](https://github.com/bluzelle/swarmDB/blob/devel/proto/database.proto). The protobuf message type for requests is `database_msg`.

Database responses are direct serial output through WebSocket. The protobuf message type for responses is `database_response`.


## Steps to send a request and recieve a response

1. Generate a `database_msg` object as specified in [database.proto](https://github.com/bluzelle/swarmDB/blob/devel/proto/database.proto).
2. Serialize the message.
3. Convert the serial data to a base64 string.
4. Embed the base64 string into `"{ "bzn-api": "database", "msg": "YOUR STRING HERE" }"`.
5. Establish a WebSocket connection to a Bluzelle node and send the message as a string.
6. The response, coming as raw bytes, should be interpreted as a `database_response`.  


## Example messages with `bluzelle-js`

```
// uuid: '8078e15c-ac47-4db9-83df-da6dba71231a'

bluzelle.create('hello', 'world');

{
    "bzn-api":"database",
    "msg":"Uj0SKAokODA3OGUxNWMtYWM0Ny00ZGI5LTgzZGYtZGE2ZGJhNzEyMzFhEARSERIFaGVsbG8aCAEid29ybGQi"
}



bluzelle.read('myKey');

{
    "bzn-api":"database",
    "msg":"UjMSKAokODA3OGUxNWMtYWM0Ny00ZGI5LTgzZGYtZGE2ZGJhNzEyMzFhEAZaBxIFbXlLZXk="
}



bluzelle.remove('hello');

{
    "bzn-api":"database",
    "msg":"UjMSKAokODA3OGUxNWMtYWM0Ny00ZGI5LTgzZGYtZGE2ZGJhNzEyMzFhEAdqBxIFaGVsbG8="
}



bluzelle.has('abcd');

{
    "bzn-api":"database",
    "msg":"UjISKAokODA3OGUxNWMtYWM0Ny00ZGI5LTgzZGYtZGE2ZGJhNzEyMzFhEAlyBhIEYWJjZA=="
}


```