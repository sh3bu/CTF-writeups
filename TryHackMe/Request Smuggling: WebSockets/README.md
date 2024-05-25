## Intro 

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/57fcdd41-e1df-47ea-8188-eec08a6817af)

## Websockets

The WebSocket protocol allows the creation of **two-way communication channels** between a browser and a server by establishing a long-lasting connection that can be used for **full-duplex communications**.

WebSocket connections are normally created using client-side JavaScript like the following:

```js
var ws = new WebSocket("wss://normal-website.com/chat");
```

### Upgrading HTTP connections to Websockets:

To establish the connection, the browser and server perform a WebSocket handshake over HTTP. The browser issues a WebSocket handshake request like the following:

```http
GET /chat HTTP/1.1
Host: normal-website.com
Sec-WebSocket-Version: 13
Sec-WebSocket-Key: wDqumtseNBJdhkihL6PW7w==
Connection: keep-alive, Upgrade
Cookie: session=KOsEJNuflw4Rd9BDNrVmvwBF9rEijeE2
Upgrade: websocket
```

If the server accepts the connection, it returns a WebSocket handshake response like the following:

```http
HTTP/1.1 101 Switching Protocols
Connection: Upgrade
Upgrade: websocket
Sec-WebSocket-Accept: 0FFP+2nmNIf/h+4BP36k9uzrYGk=
```

- The client sends an initial HTTP request with an `Upgrade: websocket` header and other additional headers.
- If the server supports WebSockets, it responds with a `101 Switching Protocols` response and upgrades the connection accordingly.
- From that point onwards, the connection uses the WebSocket protocol instead of HTTP.

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/908a4716-6f78-4188-b381-d8fb78e4497d)

> 1. The `Connection` and `Upgrade` headers in the request and response **indicate that this is a WebSocket handshake**.
> 2. The `Sec-WebSocket-Version` request header **specifies the WebSocket protocol version** that the client wishes to use. This is typically 13.
> 3. The `Sec-WebSocket-Key` request header **contains a Base64-encoded random value**, which should be randomly generated in each handshake request.
> 4. The `Sec-WebSocket-Accept` response header **contains a hash of the value submitted in the Sec-WebSocket-Key request header, concatenated with a specific string defined in the protocol specification**. This is done to prevent misleading responses resulting from misconfigured servers or caching proxies.


If we now add a proxy in the middle, something interesting happens: 

- Instead of fronting the connections themselves, **most proxies won't handle the upgrade but will instead relay them to the backend server**.
- **Once the connection is upgraded, the proxy will establish a tunnel between the client and server**, so any further WebSocket traffic is forwarded without interruptions.

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/39ec08c2-ca0b-42cb-824e-0f1b6684d4b7)

## Abusing Websockets for Request Smuggling

### Smuggling HTTP requests through broken WebSocket Tunnels:

- To smuggle requests through a vulnerable proxy, we can create a malformed request such that **_the proxy thinks a WebSocket upgrade is performed, but the backend server doesn't really upgrade the connection_**.
- This will **_force the proxy into establishing a tunnel between client and server that will go unchecked since it assumes it is now a WebSocket connection, but the backend will still expect HTTP traffic_**.

But how to do it?

- One way to force this is to send an upgrade request with an invalid `Sec-Websocket-Version` header.
- If the server supports the requested version, it should issue a `101 Switching Protocols` response and upgrade the connection.

But we aren't interested in upgrading the connection.

- If we send an** _unsupported value_** for the `Sec-Websocket-Version` header, the server will send a `426 Upgrade Required` response to indicate the upgrade was unsuccessful.

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/a277c56e-52ef-41df-8d96-c79c1564fbe6)
