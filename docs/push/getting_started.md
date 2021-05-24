# Getting Started

Push is using [socket.io](https://socket.io/) in order to provide the live feed, and getting started with socket.io in JavaScript is trivial.


First step is to include socket.io-client into your applications package.json

```bash
yarn add socket.io-client
```
 
package.json:
```json
{
 "dependencies": {   
    "socket.io-client": "^2.0.4"   
  }
}
```

Next import the client where in the file you want to use it:
```js
import io from 'socket.io-client'
```

Create the client and setup event listeners:

```js
socket = io(`wss://push.aws.kambicdn.com`, {
            transports: ['websocket'],
            upgrade: false,
            autoConnect: false,
            path: "/socket.io"
})

socket.on("connect", (socket) => {
    console.log("Socket connected");
})

socket.on("disconnect", (reason) => {
    console.log("Socket disconnected: " + reason);
})

socket.on('reconnect', (attemptNumber) => {
    console.log("Socket reconnected");
});

socket.on('error', (error) => {
    console.log("Socket error: " + error);
});

socket.on('reconnect_attempt', (attemptNumber) => {
    console.log("Socket reconnect attempt " + attemptNumber);
});

socket.on("message", data => {
    const msg = JSON.parse(data)
    console.log("On message")
})
```

Now we are ready to open the connection:

```js
socket.open()
```

if open was successful the console should read 'Socket connected'

Nothing will be pushed until we setup a subscription on a topic:

```js
socket.emit('subscribe', {topic: "v2018.kambi.ev.json"})
```

When you are done with the subscription you must unsubscribe:

```js
socket.emit('unsubscribe', {topic: "v2018.kambi.ev.json"})
```
