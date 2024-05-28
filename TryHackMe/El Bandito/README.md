## Banner :

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/d5e0a376-f9b1-4aff-88d2-435067461624)

## Recon :

### NMAP

```bash
┌──(kali㉿kali)-[~/thm/el-bandito]
└─$ cat recon/rustscan.out 
# Nmap 7.94SVN scan initiated Sun May 26 07:02:21 2024 as: nmap -vvv -p 22,80,8080,631 -sC -sV -oN recon/rustscan.out 10.10.145.9
Nmap scan report for 10.10.145.9
Host is up, received echo-reply ttl 63 (0.16s latency).
Scanned at 2024-05-26 07:02:21 EDT for 164s

PORT     STATE SERVICE  REASON         VERSION
22/tcp   open  ssh      syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 50:94:48:bb:53:53:5b:de:98:54:51:15:20:73:f5:40 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCxcoMc7sUz7unufjKpJFnmMVpKXAka8SVyaKEWqDnGDJFEdbFFRaF+rra1A46s+Xw7fWTN96wNslhhOJ9s+jMFjK8NnYSg5SQFWynJIpZ8hrPErVQEdz/Szxv1CfbVjP34w0W+2ax393Nl/kXGr2gm8Bw0/ASJINL7GSk0RBNbRiJmxsQ7BBv+K8rAfRKDlytn50Ewgja0oj4cS9yQ6ua5Ti5AkSdJX6EU1RiAWfyYRNQDNs/+qSWPDNCMOp9tvAf5GXPXaZbfUBAT7SOijcD40HBTncuxOViE30xtYChXjTlYKhIIxpnzzDtqL99MKk3axIY5qteDpsv+8MkPqaz9vFhZzXfy1QRyCfu2xP0VaKV64ybYPZf1JS45TU7eXWIdtIP30ETY2vNvcRQTFfEJ7sGEYsB7WC9QJ55lHuK8Q3v+TVaqkXHrFhDhQsuygBARHL7WHrSKxxfXhHzTCW4/jJ7A405JJo69jqO1qWtWoY+q+58tOqPHsTPNZ7GXwYE=
|   256 ca:8f:d0:ce:9c:34:c7:58:61:47:b9:3c:a2:d5:44:de (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBKhoccA+FE50YY2T8Z5sBX05mIB51MEJNXMsHOqRkxKGJqMCz2hvuGR/uny5DWRGzWU6yU3PvYFI92NeV9tUMhY=
|   256 62:e1:d7:14:19:5a:c4:6b:76:63:77:c5:2f:9c:7b:8e (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIN3hTJWOlJHWp1m1ha8lWpGGWKjRpl36TgwdmljMAueP
80/tcp   open  ssl/http syn-ack ttl 62 El Bandito Server
| http-methods: 
|_  Supported Methods: POST OPTIONS HEAD GET
|_ssl-date: TLS randomness does not represent time
|_http-title: Site doesn't have a title (text/html; charset=utf-8).
|_http-server-header: El Bandito Server
| ssl-cert: Subject: commonName=localhost
| Subject Alternative Name: DNS:localhost
| Issuer: commonName=localhost
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2021-04-10T06:51:56
| Not valid after:  2031-04-08T06:51:56
| MD5:   b27d:890a:f042:b213:5197:0a59:8e7d:7032
| SHA-1: c7ef:94a2:c156:0d67:0228:3e0e:ec9d:208b:1ef7:2610
| -----BEGIN CERTIFICATE-----
| MIIC2TCCAcGgAwIBAgIURBblAejJw5ScYoNqRPbL4wjFDX0wDQYJKoZIhvcNAQEL
| BQAwFDESMBAGA1UEAwwJbG9jYWxob3N0MB4XDTIxMDQxMDA2NTE1NloXDTMxMDQw
| ODA2NTE1NlowFDESMBAGA1UEAwwJbG9jYWxob3N0MIIBIjANBgkqhkiG9w0BAQEF
| AAOCAQ8AMIIBCgKCAQEA1IGvkDTK2XXvNxpEVtjI2IaOL92aEiF6Jf7e0F5RNLa7
| duXZ6A/R5ZAGLbiAZez8YWrcQ4kTQe6NJ+MfteA0uhoXv+I4D8ZlKPxhgRU9FKFB
| x0LJjZLqISdw7QJSqpCd1swAtX+v9TbRrXxmoAW1G/Ym9reeJk/0DE4SZlyehrFA
| KKbmtnxYYaGp1ntM+uPMY4jAu0YrEW99sPfYlalgPTTQB7g79Ee0QEud3+dZXKCI
| qgEOmOmJ9Xlxpg7ETyd+h3piBtCZAQdtAeHBOOcQknERCYGYZh9dG7rNzET2Ek+b
| 7xlKd9DYHivRAjoJnZs17qlfJNt/vYC8NK/eJpjsMwIDAQABoyMwITAJBgNVHRME
| AjAAMBQGA1UdEQQNMAuCCWxvY2FsaG9zdDANBgkqhkiG9w0BAQsFAAOCAQEAHeuL
| DWTNT+UZfRnvujyxEXUbxOyOnbXsF8pjEFkQez7MBJcpPpOh3Wyqp8CdilDfPmLc
| 5i3yw62yrOTo0p93dY+Cq7SW5G5MABY0caGFFmsVi9G/eFs8AQt+34uWj0V0dimO
| BdvLjfXe/q+YLfpJQgfcm6Xk32X0rOPBaE9b1uIabJUMUyq4fpDkCjhor8xV0vvu
| 8ga4weYUAY0XJxxdPIT/VL6Y3qJooY0AQHrDsYBhogbwbCpXVuqtPgyMR2QcqRDG
| cpjjdmLH+E5ecB7qsqRmwH1iDssf67+5/AX1pWOQI67K6UBS7PaWQAMZ7gOgq+xu
| ptK8Vhti1lPidVjeeQ==
|_-----END CERTIFICATE-----
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.1 404 NOT FOUND
|     Date: Sun, 26 May 2024 11:03:45 GMT
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 207
|     Content-Security-Policy: default-src 'self'; script-src 'self'; object-src 'none';
|     X-Content-Type-Options: nosniff
|     X-Frame-Options: SAMEORIGIN
|     X-XSS-Protection: 1; mode=block
|     Feature-Policy: microphone 'none'; geolocation 'none';
|     Age: 0
|     Server: El Bandito Server
|     Connection: close
|     <!doctype html>
|     <html lang=en>
|     <title>404 Not Found</title>
|     <h1>Not Found</h1>
|     <p>The requested URL was not found on the server. If you entered the URL manually please check your spelling and try again.</p>
|   GetRequest: 
|     HTTP/1.1 200 OK
|     Date: Sun, 26 May 2024 11:02:49 GMT
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 58
|     Content-Security-Policy: default-src 'self'; script-src 'self'; object-src 'none';
|     X-Content-Type-Options: nosniff
|     X-Frame-Options: SAMEORIGIN
|     X-XSS-Protection: 1; mode=block
|     Feature-Policy: microphone 'none'; geolocation 'none';
|     Age: 0
|     Server: El Bandito Server
|     Accept-Ranges: bytes
|     Connection: close
|     nothing to see <script src='/static/messages.js'></script>
|   HTTPOptions: 
|     HTTP/1.1 200 OK
|     Date: Sun, 26 May 2024 11:02:50 GMT
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 0
|     Allow: POST, OPTIONS, HEAD, GET
|     Content-Security-Policy: default-src 'self'; script-src 'self'; object-src 'none';
|     X-Content-Type-Options: nosniff
|     X-Frame-Options: SAMEORIGIN
|     X-XSS-Protection: 1; mode=block
|     Feature-Policy: microphone 'none'; geolocation 'none';
|     Age: 0
|     Server: El Bandito Server
|     Accept-Ranges: bytes
|     Connection: close
|   RTSPRequest: 
|_    HTTP/1.1 400 Bad Request
631/tcp  open  ipp      syn-ack ttl 63 CUPS 2.4
|_http-title: Forbidden - CUPS v2.4.7
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: CUPS/2.4 IPP/2.1
8080/tcp open  http     syn-ack ttl 62 nginx
|_http-title: Site doesn't have a title (application/json;charset=UTF-8).
|_http-favicon: Spring Java Framework
```

### Website - 80

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/784c7ec3-59b0-428d-bc96-61b76ae5e7a8)

Clicking on View-source, we can see there there is a script src pointing  to **message.js**

```js
nothing to see <script src='/static/messages.js'></script>
```

Two functions stand out in message.js:

- `/getMessages` endpoint

```js
// Function to fetch messages from the server
	function fetchMessages() {
		fetch("/getMessages")
			.then((response) => {
				if (!response.ok) {
					throw new Error("Failed to fetch messages");
				}
				return response.json();
			})
			.then((messages) => {
				userMessages = messages;
				userMessages.JACK === undefined
					? (userMessages = { OLIVER: messages.OLIVER, JACK: [] })
					: userMessages.OLIVER === undefined &&
					  (userMessages = { JACK: messages.JACK, OLIVER: [] });

				displayMessages("JACK");
			})
			.catch((error) => console.error("Error fetching messages:", error));
	}
```

- `/send_message` endpoint

```js
// Function to send a message to the server
	function sendMessage() {
		const messageText = writeMessageInput.value.trim();
		if (messageText !== "") {
			const activeUser = headerName.innerText;
			const urlParams = new URLSearchParams(window.location.search);
			const isBot =
				urlParams.has("msg") && urlParams.get("msg") === messageText;

			const messageData = {
				message: messageText,
				sender: isBot ? "Bot" : activeUser, // Set the sender as "Bot"
			};
			userMessages[activeUser].push(messageData);
			appendMessage(messageText);
			writeMessageInput.value = "";
			scrollToBottom();
			console.log({ activeUser });
			fetch("/send_message", {
				method: "POST",
				headers: {
					"Content-Type": "application/x-www-form-urlencoded",
				},
				body: "data="+messageText
			})
				.then((response) => {
					if (!response.ok) {
						throw new Error("Network response was not ok");
					}
					console.log("Message sent successfully");
				})
				.catch((error) => {
					console.error("Error sending message:", error);
					// Handle error (e.g., display error message to the user)
				});
		}
	}
```

The GET request to `/getMessages` endpoint has a login page:

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/22756b6b-ad78-4218-b6d9-85f83d32710a)

The POST request to `/send_message` also gives usthe same login page.

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/a0dc40a1-b1a6-4a59-920f-b562df43193d)

### Website - 8080

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/18aaf2f8-fa91-42d1-beae-9470808b8e39)

The `/services.html` endpoint shows the status of 2 sites whether it is up/down:

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/bd4ff72a-c79a-4b37-a693-85412bfe696a)

The `/burn.html` endpoint allows us to burn a token & nothing else.

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/4d0f0e2e-d88f-4196-838c-d3f63ab45f73)

The **Network** tab in dev-tools indicates that the `app.js` file is not found.

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/f15bc457-d419-44e8-832d-e93349952861)

When trying to access it, we get a **Whitelabel error page**. This confirms the favicon that it is a **Spring Boot application.**

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/dcab9aac-9bf2-471f-9997-d20e990c2d82)

So I queried for the following endpoints - `/actuators`, `/env`, `/mappings`.

The `/actuators` endpoint is not available & the `/env` returns a **403 Forbidden page** by Nginx indicating that there is a NGINX reverse proxy in place that is preventing us from accessing this page.

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/6a3e1521-ec78-4516-8777-d3447e3c7d06)

The `/mappings` endpoint gives us some interesting endpoints:

> **/mappings** endpoint displays a collated list of all @RequestMapping paths.

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/25618a0c-1d8f-42dd-8ff7-eca035f12eab)

The key thing to note here are these 2 endpoints: `/admin-creds` & `/admin-flag`

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/fa9955cb-1a51-4af2-a987-bfda114cba98)

## First flag

The NGINX proxy does not allow us to access those 2 endpoints by sending a **403 Forbidden** page.

Here the NGINX proxy prevents us to view the contents of the page but we can access the page directly by bypassing the proxy & sending request to the backend server.

We can perform **Websocket Request Smuggling** to achieve this.The NGINX proxy checks the status code of WS upgrade request. So we need to trick the NGINX proxy to beleive that we indeed made a successfull WS hand. For this, we need to find an SSRF vulnerability on this site.

> Good read regarding this - https://github.com/0ang3el/websocket-smuggle#22-scenario-2

The Network tab in the **_services.html_** page shows that the site makes a GET request to `/isOnline?url=http://bandito.websocket.thm` endpoint to retrive the status of the domains. We can abuse this feature by making requests to our own server.

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/b2ade55b-ba00-4a15-8428-d105c564f59c)

Upon entering our webserver URL, we get a hit on our server.

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/f52a14ae-465f-488f-a858-bfc3c82c6cb7)

```bash
┌──(kali㉿kali)-[~/thm/el-bandito]
└─$ python3 -m http.server 9090
Serving HTTP on 0.0.0.0 port 9090 (http://0.0.0.0:9090/) ...
10.10.71.75 - - [27/May/2024 10:49:02] "GET / HTTP/1.1" 200 -
```
Now we can have a script that responds with `101  Switching protocols` status code when the server tries to request the contents of our webserver. By this way, the NGINX proxy assumes we have established a WS communication with the backend server but infact we haven't.

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
````

Now when we make a request, to our server & send a smuggled request to the backend server, we get the contents of `/env` as expected.

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/bb270f77-adbb-4cba-af90-097bbb5227b7)

- Contents of `/admin-flag`.

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/d007b969-0148-4892-b876-4ff4389f9724)

- Contents of `/admin-creds` flag.

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/918b28ee-8782-42b3-a759-4afb3f4cd275)

With the creds obtained, we can now login to the site at port 80.

Upon login, we have a chat application page:

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/9093b825-2c49-4c9e-94d7-4303afe309c8)

From the below request and response, we can understand that OLIVER & JACK are also in the same chat sending messages.

The contents of **message.js** explains what is happening behind:

This script fetches messages from the server, handles the display of these messages in a chat interface for two users (JACK and OLIVER), and updates the chat area with the fetched messages when the DOM content is fully loaded.

```js
document.addEventListener("DOMContentLoaded", function () {
	const discussions = document.querySelectorAll(".discussion");
	const messagesChat = document.querySelector(".messages-chat");
	const headerName = document.querySelector(".header-chat .name");
	const writeMessageInput = document.querySelector(".write-message");
	let userMessages = {
		JACK: [],
		OLIVER: [],
	};

	// Function to fetch messages from the server
	function fetchMessages() {
		fetch("/getMessages")
			.then((response) => {
				if (!response.ok) {
					throw new Error("Failed to fetch messages");
				}
				return response.json();
			})
			.then((messages) => {
				userMessages = messages;
				userMessages.JACK === undefined
					? (userMessages = { OLIVER: messages.OLIVER, JACK: [] })
					: userMessages.OLIVER === undefined &&
					  (userMessages = { JACK: messages.JACK, OLIVER: [] });

				displayMessages("JACK");
			})
			.catch((error) => console.error("Error fetching messages:", error));
	}

	// Function to display messages for the selected user
	function displayMessages(userName) {
		headerName.innerText = userName;
		messagesChat.innerHTML = "";
		userMessages[userName].forEach(function (messageData) {
			appendMessage(messageData);
		});
	}

	// Function to append a message to the chat area
	function appendMessage(messageData) {
		const newMessage = document.createElement("div");
		console.log({ messageData });
		newMessage.classList.add("message", "text-only");
		newMessage.innerHTML = `
           ${messageData.sender !== "Bot" ? '<div class="response">' : ""}
        <div class="text">${messageData}</div>
    ${messageData.sender !== "Bot" ? "</div>" : ""}
        `;
		messagesChat.appendChild(newMessage);
	}
```

Since this room is based on HTTP request smuggling, I thought that might also be the way here for the seconD flag.

### HTTP/2 Desync Attacks

> The following blog post by James Kettle explains this in depth  - https://portswigger.net/research/http2 

#### Request Smuggling via HTTP/2 Downgrades -

HTTP/2 downgrading is when a **front-end server speaks HTTP/2 with clients**, but r**ewrites requests into HTTP/1.1 before forwarding them on to the back-end server**. This protocol translation enables a range of attacks, including HTTP request smuggling:

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/d48f2275-1ce4-4c87-abbf-da4e167e5c23)


#### H2 request smuggling -

H2 request smuggling is essentially a variant of request smuggling, but instead of a confusion about the headers Content-Length and chunked encoding, **H2 request smuggling takes advantage of H2 compatible proxies rewriting H2 requests into HTTP/1.1**. 

One of the things that can go wrong in this conversion is that the **content length is not required in HTTP/2 [rfc7540]** due to H2’s frame structure. However, **if an incorrect content length is specified in the H2 request and written to the new HTTP/1.1 request without any checks, a confusion can arise between the server where a request starts and ends**.

### Exploit POC -

We have 2 requests here to perform request smuggling.

1. `/send_message`

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/44576a2c-135d-4158-8db9-0be05fac4f9d)

2. `/getmMssages`

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/d811dd33-5d58-4871-9ead-4fccdcd85728)


To confirm  H2.CL request smuggling, we first send the following request:

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/6214534b-4a8e-4d94-b31f-2f40c2362db6)

In the above image, we have sent  POST request to **/** with **Content-Length: 0** in **HTTP/2** is followed by a smuggled **GET /doesnotexist** request with a **Foo:** header, causing the server to treat Foo: as an invalid header for the next incoming request, thus rendering the smuggled request invalid and preventing it from executing correctly.

Upon sending subsequent request to **/** endpoint, we get a **404 Not found** response as the webserver tried to perform a GET request to **/doesnotexist** endpoint.

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/61f13589-89d4-4226-8775-ae851a39e9f3)

This confirms we have a `H2.CL request smuggling` vulnerability.

To get the request sent by another user, we can send a smuggled request as follows:

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/98afdb99-afda-491a-90de-bea78112cecd)

The request will be in waiting state as the server expects a response content of length 800. Once the victim user performs any action, the request will get appended to our smuggled request & we will get a response of **503 Service Unavailable**.

After a minute,we will get a response of **503 Service Unavailable** which indicates that victim has sent a request which has filled the remaining Content-Length of our smuggled request.Now when we request the `/getMessages` endpoint, we can see the request made from Jack user. The cookie of Jack's account contains the flag.

![image](https://github.com/sh3bu/CTF-writeups/assets/67383098/6b514f84-310d-4742-92a1-da7784232bf6)

