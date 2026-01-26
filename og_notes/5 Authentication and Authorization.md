# 5. Authentication and Authorization

Authorization can be explained as a mechanism to assign and identity to subject.

*Who are you? - AUTHENTICATION*

*What can you do? - AUTHORIZATION*

These two sentences can summarize the concept of authentication and authorization.

## Authentication

Authentication used to be implied. Meaning it was based on trust. A trusted person would vouch for another person.

This isn’t scalable.

This marks the search for → explicit authentication

We needed proof of authentication independent of any other party

An earliest example of this could be a **wax seal**. That acted as signatures on letters.

→ Lead to cryptography

These seals were widely adopted “identification tokens”. They were used for verification.

One bypass/attack to this were **forging** seals. This led to further innovation.

During the Industrial Revolution, communication systems started to progress and evolve.

The **telegraph** was introduced. 

They came up with **pass-phrases** for security. (Static passwords)

This authentication shift can be seen as **“something you possess”** into **“something you know”**

Down the line we made:

→ passwords

→ hashing (irreversible fixed length representations): a computational algorithm takes an input string and will return a fixed length representation.

**Asymmetric Cryptography**

DH key exhange.

Two parties to establish a sdhared secret over an untrusted medium.

Thjis became the backbonme of modern authentication prototls

PKI - public key infrastructure systems

A need for scalable authentications rised during 1990s. Username / password couldn’t stand bruteforce attacks

Lead to **MFA (multi factor authentication):**

it combined:

→ something you know

→ something you have

→ something you are

Combined different layers of principles for authentication.

Identify users based on unique traits.

**21st Century**

With the rise of cloud computing, api architectures, more advanced authentication frameworks were needed.

1. OAuth 2.0
2. JWT
3. Zero Trust Architecture
4. Passwordless Authentication (web authentication, public/private keys stored in hardware)

**Future**

1. Decentralized identity (like blockchain)
2. Behavioral biometrics
3. post quantum cryptography (once quantum computers are used heavily, they will break all our cryptography right now)

### Needed context

Sessions, JWTs, Cookies

**Sessions**

HTTP emerged as the backbone of communication between clients and servers.

By design HTTP was stateless. It treated every request as an isolated interaction. (NO MEMORY OF PAST EXCHANGES)

This was ideal for most web in the past. People didn’t need continuity, or memory between requests.

As we evolved into **dynamic content** the statelessness of HTTP became a bottleneck.

For example:

- remembering items in a cart
- how can a user remain logged in while navigating between different pages

Lead to **stateful interactions**.

A session provides a way to establish temporary server side context for each user. Has some memory of user.

1. Session creation: creates a unique session ID and is stored with relevant user data. Stored in a persistent store like (redis). The server stores this.
2. Session ID: is sent to the user/client as a **cookie.** All requests that the client makes after the session creation, are sent with the Session ID.
3. Session Expiry: these session ID’s can expire after for example 15min. After expiry they require a new ID.

The session ID enables servers to have memory for the sake of the client.

Initially sessions were stored as file based on the server. This was a scalability problem. This moved to DB’s.

When we moved to distributed architecture, we moved the storage for sessions to something like Redis/mem cache for **faster** lookups.

**JWT**

Even though stateful systems were effective, they caused a bottleneck. Maintaining session data for millions of users became costly (data storage/ large overhead). Also synchronizing session data across different servers across the world introduced latency. (Delays in authentication users and consisteny challenges)

Lead to the birth of JWT. A solution that could offlead state from the server while maintaingin integrrity.

They were initially a **stateless** mechanisms for transfering claimsin in a statless manner betweeen two parties.

The key innovation here was that they were **self-contained**. they had the user data, and the crypographic signatures in ONE token (base64 encoded).

![5-jwtexample.png](/images/5-jwtexample.png)

The first part is the header: specifics metadata, like signing algorithm.

```jsx
{
	"alg": "HS256",
	"typ": "JWT"
}
```

Then there is the payload: has data.

```jsx
{
	"sub": "1234567890",    // can be a userId
	"name": "John Doe",     // option fields
	"iat" : 1516239022      // issued at
}
```

Lastly there is the signature: to verify if YOU are the one who issued it. You do this with a key with SECRET key.

```jsx
a-string-secret-at-least-256-bits-long
```

Now you don’t have to store the session information anymore. It became stateless, lightweight, saves storage cost. And happens with every request.

1. Statelessness = no server side storage cost
2. Scalability = servers use distributed architecture so a cookie for on server cannot be used across servers on a domain. The JWT allows the client to send requests to different servers, all these servers will have the secret key to decode the JWT and check authorization.
3. Portability = JWTs can be passed between different systems, can be stored in many limited spaces

Challenges of JWT

1. Token theft: if someone has access to your JWT they can impersonate you. No way for the server to invalidate the JWT unless it changes the secret key.
    1. Problem: the server cant change the secret key just to invalidate one user because all users will be affected then
2. Revoking: since JWTs are stateless the server cannot do anything unless they expire. they can’t track them or anything.

So we made a **hybrid** approach with JWTs.

1. User logs in, server sends a JWT token which the user stores in the system
2. With every subsequent request the user sends the JWT for identification.
3. But now the server stores the JWT in a persistent storage (like redis) to keep track of JWTs they can potentially *blacklist* incase the users get hacked or something

This brings up the question, didnt we make JWT to stop storing? Why not just use the first stateful solution? 

This question wasn’t answered in the video lol. Sriniously says don’t worry about it just use an AuthProvider. *Thats their headache lol.*

It’s usually a good idea to go with an AuthProvider for medium-large apps. Unless you’re very confident with authentication workflows.

**Cookies**

A way of storing a piece of information in a users browser *FROM THE SERVER SIDE*

Servers can store information on the clients side.

Given:

- the cookie is only accessible to that server

Cookies are sent in every subsequent request from the users browser.

During the authentication workflow:

→ Auth with username and password

→ Server sends a cookie with the auth token

→ this is stored in the clients browser

→ the client uses this cookie to verify authentication on requests

### Types of Authentication

1. stateful
2. stateless
3. API key
4. OAuth 2.0

### Stateful Authentication

1. Client sends user/pass to server
2. Server checks validity
3. If correct, the server generates a sessionID, and user data and **stores in Redis**
4. This sessionID is stored in a (HTTP only) cookie and sent back to the client. Because it’s HTTP only, Javascript cannot access the cookie.
5. All requests that are made from the client from now on, will have the cookie with the sessionID.
6. In Redis it will verify the sessionIDs existance and check expiry.

Note: the sessionID can be whatever you want (some cryptographic thing)

### Stateless Authentication

1. Client sends user/pass to server
2. Server checks authenticy of user/pass.
3. if correct the server generastes a signed JWT token with a secret key (this jwt also contains user info like userId, role etc). the server has its own secret key (Stored securly)
4. the server then sends this JWT back to the client
5. from now every request the client makes back to server will be authorized:
    1. send the JWT back in a header called “Authorizaton”: <JWT>
6. the server extracts the token, verifies it with the servers secret key. then it extract information like userID, and proceed with a response

Note: stateless because any information abou thte user is stored in the token itself which also signs itself. a stateless method of authentication

### When to choose stateful or stateless?

**Stateful**

Pros:

- you have centralized control over all the sessions
- this is all available in your persistent store
- you can easily revoke access, you have the session / control in your hands
- well suited for applciations with modern traffic and strict session requirements
- most application sshould go with stateful

cons:

- limited scalability issues
- operational complexity with multiple servers and regions (syncing)

**Stateless**

Pros:

- scalability
- no session store dependany
- ideal for distributed architecures nad mobile

Cons:

- token revoking is complex: until it expires you cannot revoke access
    - can only do by cahnign secret key which is an inconvenience to all users

Solutions: make certain systems use stateless, and orthers usse stateful

### API Key based Authentication

API key based authenication:

1. You are already on a platform
2. you press generate api key, you get a cryptographic key
3. you can now use that key to gain access to *that* server for that platform

Think about ChatGPT.

You can can go to a browser chatgpt and search stuff.

There are servers for chatgpt that respond to the browser / client searches

But OpenAI (comany that make chatgpt) also made API keys

This is for different usecases like programmatic access to the server.

An API key user wants to search but doesnt want the UI part that is in the browser, just the response.

THINK LIKE GPT WRAPPERS

**API keys provide access to your server for a different set of users.**

API keys are unique, and allow to identify user based data like (tokens remaining, usage, etc)

Advantages:

1. easy to generate
2. ideal for machine to machine communication (straight code/data, no UI)

### OAuth 2.0

With all previous authentication methods, we still use username/passwords and stuff. Passwords are no longer secure, and one breach can compromise a bunch of accounts. Like reusing same passwords on multiple websites

Also because of the number of accounts made, there is fatigue for an owner to manage.

**Delegation**

An important concept in OAuth is delegation. We came to a problem where **one website would need access to another websites resources**.

Example: 

a hotel booking app → this needed access to your google account to scan your flight tickets

A social media app → import contacts from other platforms

This use case gave rise to OAuth.

The initial solution was to share passwords between apps. This was a terrible idea because it gave **full access** to each accounts. The other issue was that it was impossible to revoke access without changing the password everywhere → because users used the same password everywhere

**The Delegation Problem**

Apps wanting to access resources from other apps

2007 - birth of OAuth (Open Authorization) - A revolutionary idea

Instead of sharing passwords, we shared tokens instead,

But these tokens have specific permissions to specific parts of an account.

(share password → share complete account, share specified token → limited account access)

**OAuth 1.0**

Resource owner - user who owns data

client- app requesting access

resource server - the one giving the resource

authorization server - the server that issues the token

The flow looked like:

1. the client redirects the user to the authorization server
2. the user authenticates and grants permission to the client
3. the authorization server sends a token to the client
4. the client uses the token to access the resources

**OAuth 2.0**

came out in 2010ish

1.0 limitations:

- was very complex for developers to implement
- used cryptographic signatures which were error prone

2.0 Introduced **bearer** **tokens**.

- This made implementations more simple
- allowed developers to choose flows based on app type (mobile, serverside, smart tv, browser)

some flow examples:

1. Auth code flow (for server side apps)
2. Implicit based flow (for brower based apps -  discouraged now)
3. client credentials flow (for machine to machine communication - no user)
4. device code flow (for devices with limited input -like keyboards and mouse / smart tv)

OAuth was great for **authorization** but not for **authentication:**

- authentication is WHO you are (identity)
- authorization is WHAT you can do (permissions)

In 2014, people came up with OpenID connect (OIDC) which was built on OAuth 2.0 to fill the authentication gap.

OIDC **extended** OAuth2.0 by introducing **ID token** (a JWT which had info like userID, when they logged in, from where, etc)

This allowed many platforms now to login from different apps.

- login with google
- login with facebook
- login with twitter
- login with github

How it works:

1. user clicks on sign in with google
2. the client takes the user to the authorization server
3. user logs into the google account from google authorization server, give permissions to the client
4. authorization server sends an **authorization code** and and **id token** to the client (an id token is not ALWAYS sent back)
5. the client uses the **authorization code** and exchanges it with the authorization server for an **access token** on behalf of the user. (if the id token wasn’t received above, it will be received now)
6. now the client can do actions on behalf of the user in the resource server **with the access token**

the client can “pose” as you to get resources from the external server

OAuth2.0 and OIDC are gatekeepers of the modern age. they ensure no one gets more access than they need or that they have asked for from the user.

From password sharing chaos → a secure modern interconnected system

### When to use which authentication

1. stateful authentication:
    1. typical saas base model, user specific data session data stored in server
2. stateless authentication:
    1. ideal for apis, or scalable systems which have distributed serveres all over the world. token carry data
3. OAuth authentication:
    1. ideal for third party integrations and login via external providers
4. API key authentication:
    1. ideal for server to server (NO UI)

for backend engineers, you will mostly use 1 or 2.

## **Authorization**

Think about users and administrators.

They both have different permissions and capabilities.

To separate interfaces for special permission you could have a specific “string” added to your communication to give you “god mode” access. But this has a security flaw if anyone gains access to the string and replicates it in their communication.

The other issue with this approach is that lets say you want to give other users special permissions, you either give them the same string, or make new strings and tell your server to allow them. This complicates the system and access more.

Providing specific permissions to specific users on a platform is authorization.

Not all users on the platform have the same level of access.

One of the most famous authorizations:

**RBAC - Role Based Access Control**

Different roles have different permissions:

- admin
- moderator
- user

typical workflow:

1. user signs up
2. server assigns a role depending on user
3. once user authenticates, it sends its identification
4. the server can deduce what role the user has based on the token or a database lookup
5. role is attached to the information
6. the next set of communication will show what role the user has and this determines their access capabilities

## Extra but IMPORTANT

1. Error messages

“user not found”, “incorrect password”, “account locked due to too many failed attempts”

While these messages are helpful for real users, for attackers these act as hints.

For example:

- incorrect password means the user/email does exist and WAS CORRECT, and can tell the attacker to use a different password

Attack possibilities increase.

**As a backend engineer, for any authentication you need to keep error messages generic.**

1. Timing Attacks

This is a typical authentication workflow when a user submits email/password.

1. find user
2. find if account locked
3. compare password with stored hash password in database (the hash pass cannot be decrypted)

side note: the way passwords work with databases is that an initial password is sent on creation. the actual string value of the password is NOT stored in a database. Instead, the string password is hashed and the HASHED alphanumeric is stored. When a user logs in, the server hashes the inputted password and compares if it matches in the DB.

anyways back to timing attacks:

depending on the step of the workflow, it might cancel and return a response back at different speeds.

for example:

- if the user doesnt exist, the workflow will cancel right away and sent a response back
- BUT if the user does exist, and the account isnt locked, it needs to compare the hash against the database which introduces more DELAY AND TAKES LONGER before sending a response back

This way attackers can gain more hints about the account they are trying to breach because of the RESPONSE TIME / DELAY.

**To defend this, you can introduce a method to equalize response times.**

one method: constant time operations. makes sure that execution time doesnt vary based on input similarity

second method: simulate response delay with sleep, settimeout, etc. (fake delay)