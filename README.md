# RSA Secure Chat
This is a secure chat program uses our own implementation of RSA to encrypt messages end-to-end. The client/server use a JSON websocket protocol (chat messages include information such as the receiver, sender and encrypted message). This implementation is not 100% secure and is still susceptible to a number of MITM attacks.

Authors:
- snehitha - Login/registration (/src/main/java/com/rsa/rsasecuritychatui/...)
- Manasee - RSA implementation (/src/main/java/com/security/rsa/...)
- lysis32 - Server/client and chat code, chat UI.

# Build Information
Build with Maven

Run the server with /src/main/java/com/server/Server.java (requires port 4000)

Run the client with /src/main/java/com/rsa/rsasecuritychatui/Startingapplication.java

# Known Issues
- Username/password checking is not implemented right now, so registration is not required and any username/password can be used.
- Clients do not disconnect properly and will not be removed from the list chat window UI.
- Chat message length restricted to 128 characters (each message is encrypted with RSA using 1024-bit keys).
