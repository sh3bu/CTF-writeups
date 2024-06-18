## Chapter 2 : THE ANATOMY OF WEB APIS

**Singleton resource:**  represents a unique object (`/api/user/{user_id}`)

**Collection:** represents a group of resources (`/api/user/{user_id}/settings` - endpoint to access the settings subcollection of a specific [singleton] user)

**C.R.U.D** stands for **Create, Read, Update, Delete**

### API Types -

1. RESTful API
2. GraphQL API

#### RESTful API:

- Designed to improve inefficiencies of SOAP.
- REST API's are stateless. Hence the client tneeds to send send all the required authorization token/key to make a request.
- REST API's have some commmon headers like **Authorization[_Basic- Uses Base64 encoded creds, Bearer- Uses API token, AWS-HMAC-SHA256 is a AWS authorization type_], Content-Type, Middleware (X) headers**.

#### GraphQL

- GraphQL is Query-centric.
- In REST API's, a specific endpoint will return all the data it is supposed to but incase of GraphQL we can query specifically the info that we want.
