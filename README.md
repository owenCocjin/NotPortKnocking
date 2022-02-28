# Not Port Knocking
> Same effects as port knocking, but somewhat more secure

While reading up on port knocking, I had the thought to create a project that had the same outcome as port knocking, without the "security through obscurity" mindset.

Here, we can set specific passwords (secret strings) to open specific ports through a firewall for a limited duration. This provides the functionality port knocking does, without relying on obscurity.

---

## How It Works
In a broad view, the server will have one port open for communication. Both client & server will have an RSA keypair, where they each have the other's pub key. They will communicate data in such a way that the server can confirm the request is for it (and not just a random scan), and the server can also confirm the client is legitimate. The overall flow looks like this:

![Flow diagram of Not Port Knocking](/Pics/npk.png)

1. Client send the md5 of it's pub key and a port password, signed by it's priv key, encrypted by the server's pub key, to the server.
2. Server retrieves the client pub key from a database (using the md5 as key), and verify the client is who they say.
3. Server checks the given port key and if valid, opens the corresponding port.
4. Server sends an OK message, the duration the port will stay open (in seconds) and any additional info (thinking future proofing), all encrypted in the client's pub key.
5. Client gets OK and handles any of the other data as needed.

Some notes:
- If any errors occur (such as invalid client signature, invalid port password, etc...), the server will simply send a message back reflecting this error, and nothing will be done.
- All data going between client/server is encrypted using the other's pub key.
- The port passwords are not meant to be secure passwords! They are more used as unique identifiers, but it does help to create strong ones. The main security concern is the private key.
