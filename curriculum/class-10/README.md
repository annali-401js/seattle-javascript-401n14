# Class 10 - Authentication

## Learning Objectives

* Describe cryptographic hash and cypher algorithms
* Model a User and safely store their sensitive data
* Implement a Basic Authorization parser
* Create a Bearer Authentication parser

## Outline
* :05 **Housekeeping/Recap**
* :30 **Whiteboard/DSA Review**
* :15 **Lightning Talk**
* Break
* :30 **CS/UI Concepts** -
* :20 **Code Review**
* Break
* :60 **Main Topic**

## Computer Science Concept:
* Cyphers and Encryption

## UI Concept:
* Sending Headers
* Receiving, storing and using Cookies
* Media Queries

### User Modeling
Modern web applications need to model sensitive information about their users. When a users provides an applications with sensitive information, they are trusting that it will not be leaked are misused. This means that it is a developers responsibility to store that information responsibly. Some information, like emails, usernames, and addresses can be stored in plain text, as long as the database is password protected and/or behind a firewall. Other information, like a users password, should be encrypted using a hashing algorithm before it is ever stored. this prevents anyone (including developers with database permissions) from ever getting access to their password.

User models that have sensitive data that should **NEVER** be sent to client applications. If your application requires that users be able to read each others personal information, create a second Profile model to hold that data and strictly limit access to the Profile model. Safely using a second model will ensure that no users will accidentally (or maliciously) get access to sensitive information.

### Cryptography
Cryptograhpy is the science which studies methods for encoding messages so that they can be read only by a person who knows the secret information required for decoding, called the key; it includes cryptanalysis, the science of decoding encrypted messages without possessing the proper key, and has several other branches.

\- [GNU Collaborative International Dictionary of English](http://gcide.gnu.org.ua)

### Hash Algorithms
A Cryptographic Hash Algorithm takes a piece of data and produces a hash that is deliberately difficult to reverse. If identical data is passed into the algorithm the same hash will always be produced. Hash algorithms are often used for checking the integrity of data.

In a User model, a hash password can be stored when the user signs up. When the user needs to login, they can resend their password and the server can hash the login password using the same hash algorithm. The server can then compare the hashed login password with previously stored hashed password to determine if the user should be authenticated.

### Cypher Algorithms
A Cryptographic Cypher Algorithm takes a piece of data and a key and produces encrypted data. Later the encrypted data can be reversed into the original data by decrypting it using the same key.

User tokens can be created by associated a random unique string (tokenSeed) with a user account and, in turn, encrypting the tokenSeed with a secret key that only the server knows. We can then send the encrypted token to a client application. When the client makes a future request they send back the token. The server can reverse the token into the tokenSeed by decrypting it with the secret key. This is because the tokenSeed was unique to the database and can be queried to produce the user who made the request.

### Basic Authorization
Basic Authorization is a common method used to send a username and password in an HTTP request. The username and password are joined with a ':' then base64 encoded and placed after the string 'Basic '. The resulting string is set to the value of an Authorization header.

A Server can decode the Basic Authorization header to retrieve the username and password. Its important to note that base64 encoding is not a form of encryption. The client and server must use HTTPS to protect the username and password as it travels across the network.

``` javascript
let encoded = window.btoa('slugbyte:secretpassword')
// c2x1Z2J5dGU6c2VjcmV0cGFzc3dvcmQ=

request({
  method: 'GET',
  url: 'https://api.example.com/login',
  headers: {
    Authorization: `Basic ${encoded}`,
  },
})
.then(handleLogin)
.catch(handleLoginError)
```
