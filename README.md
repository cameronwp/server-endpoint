# Server Endpoint

_The server side of simplified socket.io messaging._

See also [client-endpoint](https://github.com/cameronwp/client-endpoint).

```sh
npm i -S server-endpoint
```

## Sample Usage

All examples assume that on{message,request} handlers were set before any {message,request} were sent.

```js
// server.js
const ServerEndpoint = require('@cameronwp/server-endpoint');

const port = '8000';
const server = new ServerEndpoint(port);
const namespace = server.createNamespace('chat'); // create as many namespaces as you want

// example server -> client push
namespace.pushMessage('new-message', 'lorem ipsum...');

// example client -> server post
namespace.onmessage('config-change', val => {
    console.log(JSON.stringify(val)); // logs "{config: 'object'}"
});

// example server response to a request
namespace.onrequest('test-request', 'ack');

// example server responding to a request with a function
namespace.onrequest('test-request-doubler-function', data => {
  return data * 2;
});
```

```js
// client.js
const ClientEndpoint = require('@cameronwp/client-endpoint');

const client = new ClientEndpoint('http://localhost:8000/chat');  // note the port and "/chat" namespace

// example server -> client push
client.onmessage('new-message', val => {
    console.log(val); // logs "lorem ipsum..."
});

// example client -> server post
client.postMessage('config-change', {config: 'object'});

// example server response to a request
client.request('test-request').then(console.log); // logs "ack"

// example server response to a request
client.request('test-request-doubler-function', 2).then(console.log); // logs 4
```

## API

### Classes

<dl>
<dt><a href="#Namespace">Namespace</a> ⇐ <code>Endpoint</code></dt>
<dd></dd>
<dt><a href="#ServerEndpoint">ServerEndpoint</a></dt>
<dd></dd>
</dl>

### Typedefs

<dl>
<dt><a href="#socketCallback">socketCallback</a> : <code>function</code></dt>
<dd><p>Connection callback. Use socket.emit(&#39;message&#39;, data) to send data to client.</p>
</dd>
</dl>

<a name="Namespace"></a>

### Namespace ⇐ <code>Endpoint</code>
**Kind**: global class
**Extends**: <code>Endpoint</code>

* [Namespace](#Namespace) ⇐ <code>Endpoint</code>
    * [new Namespace()](#new_Namespace_new)
    * [.onconnection(cb)](#Namespace+onconnection)
    * [.pushMessage(type, data)](#Namespace+pushMessage) ⇒
    * [.onrequest(type, response)](#Namespace+onrequest)

<a name="new_Namespace_new"></a>

#### new Namespace()
Wraps a namespaced socket.io server.

<a name="Namespace+onconnection"></a>

#### namespace.onconnection(cb)
Set onconnection handler when new clients join.

**Kind**: instance method of [<code>Namespace</code>](#Namespace)

| Param | Type |
| --- | --- |
| cb | [<code>socketCallback</code>](#socketCallback) |

<a name="Namespace+pushMessage"></a>

#### namespace.pushMessage(type, data) ⇒
Generic message sender. Difficult to promisify because it's sent to many listeners.

**Kind**: instance method of [<code>Namespace</code>](#Namespace)
**Returns**: this

| Param | Type |
| --- | --- |
| type | <code>string</code> |
| data | <code>any</code> |

<a name="Namespace+onrequest"></a>

#### namespace.onrequest(type, response)
Sets up request handlers.

**Kind**: instance method of [<code>Namespace</code>](#Namespace)

| Param | Type | Description |
| --- | --- | --- |
| type | <code>string</code> |  |
| response | <code>any</code> | If string or number, this method adds a handler to respond to client requests of type with the response of response. If response is a function, response is called when the request of type is received. |

<a name="ServerEndpoint"></a>

### ServerEndpoint
**Kind**: global class

* [ServerEndpoint](#ServerEndpoint)
    * [new ServerEndpoint(io)](#new_ServerEndpoint_new)
    * [.createNamespace([name])](#ServerEndpoint+createNamespace) ⇒ [<code>Namespace</code>](#Namespace)

<a name="new_ServerEndpoint_new"></a>

#### new ServerEndpoint(io)
A factory for creating namespaced socket.io servers.


| Param | Type | Description |
| --- | --- | --- |
| io | <code>Socket</code> | Socket.io server. Probably received from SocketServer. |

<a name="ServerEndpoint+createNamespace"></a>

#### serverEndpoint.createNamespace([name]) ⇒ [<code>Namespace</code>](#Namespace)
Create handlers for communications. Must create a namespace before ServerEndpoint is usable!

**Kind**: instance method of [<code>ServerEndpoint</code>](#ServerEndpoint)

| Param | Type |
| --- | --- |
| [name] | <code>string</code> |

<a name="socketCallback"></a>

### socketCallback : <code>function</code>
Connection callback. Use socket.emit('message', data) to send data to client.

**Kind**: global typedef

| Param | Type | Description |
| --- | --- | --- |
| socket | <code>socket</code> | connection to client. |

