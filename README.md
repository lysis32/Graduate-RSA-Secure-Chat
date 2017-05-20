# RSA Secure Chat
This is a secure chat program uses our own implementation of RSA to encrypt messages end-to-end. The client/server use a JSON websocket protocol (chat messages include information such as the receiver, sender and encrypted message). This implementation is not 100% secure and is still susceptible to a number of MITM attacks.

Authors:
- snehitha - Login/registration (/src/main/java/com/rsa/rsasecuritychatui/...)
- Manasee - RSA implementation (/src/main/java/com/security/rsa/...)
- lysis32 - Server/client and chat code, chat UI.

## How It Works
  ### Server Summary:
  * Listens for HTTP traffic on port 4000.
  * Endpoints:
    * /ws - Listens for websocket connections, requires 3 custom headers:
      * X-USER: username
      * X-CLIENT-KEY: public modulus
      * X-CLIENT-EXPONENT: public exponent
  * Once a user connects via the websocket endpoint, the server stores their public key and adds it to the list of connected users. Whenever a new user joins, they receive a join message for every connected user with the following format:
    {"user":"Alec","publicModulus": … ,"publicExponent": … ,"type":"join"}
  * All other clients receive a join message for the new user.
  ### Chat:
  * The server listens on the websocket for messages of the following format:
    {“to”: “bob”, “from”: “alice”, “message”: “<encrypted>”, “type”: “chat”}
  * The server finds the channel belonging to the user in the ‘from’ field and writes the message to their socket.
  * Whenever a user leaves, all clients receive a leave message and the user is removed from the list.
  ### Client Summary:
  * The client first generates a set of keys (public/private).
  * The client attempts to connect to the server at the specified URI, open a websocket, and send the required fields in the headers.
  * The client stores the public key for each user.
  * When the client selects a user, and enters text, a message is sent over the socket of the form:
    {“to”: “bob”, “from”: “alice”, “message”: “<encrypted>”, “type”: “chat”}
  * The message is encrypted with the receiver’s public key, so the server and any other users cannot read it.
  * The client receives messages asynchronously in the client handler. When a new message is received, it is added to a queue.
  * The queue is polled every 100ms and any new messages are decrypted with the user’s private key and displayed in the UI.

# Security Vulnerabilities
* There is no authentication mechanism, meaning clients can easily be impersonated by intercepting a 'join' message and replaying it with different encrypted text.
* The 'to' and 'from' fields are not encrypted and can be modified in replay attacks.

# Build Information
Build with Maven

Run the server with /src/main/java/com/server/Server.java (requires port 4000)

Run the client with /src/main/java/com/rsa/rsasecuritychatui/Startingapplication.java

# Known Issues
- Username/password checking is not implemented right now, so registration is not required and any username/password can be used.
- Clients do not disconnect properly and will not be removed from the list chat window UI.
- Chat message length restricted to 128 characters (each message is encrypted with RSA using 1024-bit keys).
