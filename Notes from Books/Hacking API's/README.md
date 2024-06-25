# Chapter 2 : THE ANATOMY OF WEB APIS

**Singleton resource:**  represents a unique object (`/api/user/{user_id}`)

**Collection:** represents a group of resources (`/api/user/{user_id}/settings` - endpoint to access the settings subcollection of a specific [singleton] user)

**C.R.U.D** stands for **Create, Read, Update, Delete**

## API Types -

1. RESTful API
2. GraphQL API

### RESTful API:

- Designed to improve inefficiencies of SOAP.
- REST API's are stateless. Hence the client tneeds to send send all the required authorization token/key to make a request.
- REST API's have some commmon headers like **Authorization[_Basic- Uses Base64 encoded creds, Bearer- Uses API token, AWS-HMAC-SHA256 is a AWS authorization type_], Content-Type, Middleware (X) headers**.

### GraphQL

- GraphQL is Query-centric.
- In REST API's, a specific endpoint will return all the data it is supposed to but incase of GraphQL we can query specifically the info that we want.

### SOAP API

- SOAP is one of the older web APIs that works over HTTP, SMTP, TCP, and UDP, it was primarily designed for use over HTTP. When SOAP is used over HTTP, the requests are all made using _HTTP POST_.
**SOAP Request -**
```http
POST /Inventory HTTP/1.1
Host: www.soap-shop.com
Content-Type: application/soap+xml; charset=utf-8
Content-Length: nnn
<?xml version="1.0"?>
<soap:Envelope
	xmlns:soap="http://www.w3.org/2003/05/soap-envelope/"
soap:encodingStyle="http://www.w3.org/2003/05/soap-encoding">
	<soap:Body
		xmlns:m="http://www.soap-shop.com/inventory">
		<m:GetInventoryPrice>
			<m:InventoryName>ThebestSOAP</m:InventoryName>
		</m:GetInventoryPrice>
	</soap:Body>
</soap:Envelope>
```

**SOAP Response**
```http
HTTP/1.1 200 OK
Content-Type: application/soap+xml; charset=utf-8
Content-Length: nnn
(continued)

<?xml version="1.0"?>
<soap:Envelope
	xmlns:soap="http://www.w3.org/2003/05/soap-envelope/"
soap:encodingStyle="http://www.w3.org/2003/05/soap-encoding">
	<soap:Body xmlns:m="http://www.soap-shop.com/inventory">
		<soap:Fault>
			<faultcode>soap:VersionMismatch</faultcode>
			<faultstring, xml:lang='en">
Name does not match Inventory record
			</faultstring>
		</soap:Fault
	</soap:Body>
</soap:Envelope>
```
SOAP API messages have 4 parts - Envelope , header (both are necessary) and body , fault (which are optional)

- **Envelope** is an XML tag which signifies it is the beginning of the SOAP message.
- The **header** can be used to process a message; in this example, the Content-Type request header lets the SOAP provider know the type of content being sent in the POST request (application/soap+xml)
- The **body** is the primary payload of the XML message, meaning it contains the data sent to the application.
- The **fault** is an optional part of a SOAP response that can be used to provide error messaging

### API authentication

- [ ] Basic Auth [`base64(username:password)`]
- [ ] API keys
- [ ] JWT
- [ ] HMAC - It is primarily used by AWS. [`HMAC(data+secret_key)`]
- [ ] OAUTH token

# PART III : ATTACKING API's

## Discovering API's -

### Passive Recon

- [ ] Google dorking
- [ ] Shodan
- [ ] Amass
- [ ] Exposed info in github

### Active Recon

- [ ] NMAP
- [ ] robots.txt
- [ ] Dev tools (JS files & XHR requests)
- [ ] Bruteforce with assetnote wordlist
- [ ] Kiterunner [`kr scan http://192.168.195.132:8090 -w ~/api/wordlists/data/kiterunner/routes-large.kite`]

## Endpoint Analysis -

- [ ] Find hidden endpoints using kiterunner.
- [ ] Analyze functionality of each endpoint.
- [ ] Testing intended use & try to test unintended way.
- [ ] Perform Priviledged actions
- [ ] Finding info disclosures, verbose errors, unencrypted communication, excessive dat exposure, business logic flaws.

## Attacking API authentication

- [ ] Password bruteforce attack
- [ ] 2FA and password reset bruteforce attack
- [ ] JWT abuse [None attack, Alg switch attack, JWT secret crack ]

## API fuzing

- [ ] Password bruteforce attack
