---
layout: post
title:  "Web Authorizations"
date:   2019--06-29 10:07:19
categories: [Web]
comments: true
---

We are going to see different types of Authorizations. Before going to that why we need a Authorization and session management in Web Applications. 
> HTTP is a stateless protocol means each request is independent of before and after requests.

<!--more-->

## Static and Dynamic Applications

For a static websites, if you make a request it would not check who you are. It just serves the web pages pointing to the path you specified. But if you are using a dynamic web pages, you request a web page and the system checks who you are and can it share the web content to you.

### **Scenario**

- Let us suppose you are logged into a banking website with your credentials to home page.
- You requested again for a the amount in your account. As HTTP is stateless it does not know who you are and if it should send the data.
- To overcome that scenario backend sends a token information to browser(client)  and every time you make a request it uses token to identify you.

This token can be generated and used using three methods depending upon the application architecture and client type(Browser,Android App, etc).

1. Session Authorization
2. JSON Web Token(JWT)
3. OAUTH Authorization

## Session Authorization

This is most popular way of providing the autharizaton. Whenever user provided the credentials backend creates a **session** stores it and shares it with frontend as **cookies**. A new request is made with this cookie will have the autherisation to the particular user content.

### **Limitations**

- We may have the multiple backend servers below a load balancer and request may go to any server. So the session may not be in other servers
- If we use a common (redis)cache server for storing sessions. If that goes down all the sessions will be terminated.
- In microservices architecture the request may go different services where Authorization is required. So every service must have the session access which is difficult.

## JSON Web Authorization(JWT)

JSON web Authorization is most recently used Authorization mechanism for its simplicity and efficiency. A simple JSON token is as below.

> eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.OsYeNeIRzSGp0Ss232RIL9xjKwsmo-c6slPSi0plpWE

This contains three parts divided by period(.) to Header,Payload and Signature. Use the jwt.io to practice below.

- base64 Decoding of first part (eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9) gives   a json header 
  `{"alg":"HS256","typ":"JWT"}`
- base64 Decoding of second part (eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ)   gives a json payload `{"sub":"1234567890","name":"John Doe","iat":1516239022}`
- Third one is the signature generated by the server using the server SecretKey with the below given Logic. So if you change the payload and sends with same signature it throws an error stating not valid signature.
```
HMACSHA256(
  base64UrlEncode(header) + "." +base64UrlEncode(payload),
  SecretKey
)
```
### **Limitations**

- The main limitation is what if my JWT token is stolen and used. We are not storing any sessions on serverside to check and deactivate.
- We can not logout the session if a JWT token is created. We can use the same token and it never expires.

To overcome this always use your JWT with SSL enabled(https). Get knowledge on [SSL](/articles/2017-12/ssl)



## OAUTH Authorization

OAuth is used for providing authorization to services. For suppose you want to provide google storage access to a gaming application. You can not simply give your username and password to the gaming application. If you do so you will be providing access to all your drive, mail, documents, Chats, Contacts and meetings. So, google creates a seperate OAUTH Token which only gives access to a particular storage service. For doing that we need a Token which should have all allowed permissions and trustable. So here the token format is **JWT**.

### Terminology

**Client :** The appliction which is trying to access the service. Here gaming application is the Client
**Resource Server :** The service which needs to be accessed is the resource server. Here google storage is resource server
**Authorization Seerver :** The service which provides the token to client for accessing the resource server is the Authorization server.

There are different flows followed for implementig this OAUTH authorization. Few of them are 

- **Authorization Code Flow** 
- **Implicit Flow**
- **Client Credential Flow**