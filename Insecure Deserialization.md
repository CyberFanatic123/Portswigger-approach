
Primarily Server-side attack
Client side is also possible

| Serialization                                                                     | Deserialization                                        |
| --------------------------------------------------------------------------------- | ------------------------------------------------------ |
| Changing the format of data that is going to tranfer in network                   | Changing back to original format                       |
| Eg: Transfering a table in parcel service by dismantaling it(changing the format) | Assembling back the table at the destination to use it |
Similar names for serialization in other programming language

| Ruby                                                 | Python                                        | Java                                | PHP             | .Net                                           |
| ---------------------------------------------------- | --------------------------------------------- | ----------------------------------- | --------------- | ---------------------------------------------- |
| Marshalling<br>`Marshal.load()`, `Marshal.restore()` | Pickling<br>`pickle.load()`, `pickle.loads()` | `ObjectInputStream`, `readObject()` | `unserialize()` | `BinaryFormatter`, `NetDataContractSerializer` |

Where it this issue worth searching,
- All places where serialized data is used such as HTTP requests, files, databases, cookies, files,or inter-process communications.
- **Web Application Endpoints**: Test these endpoints by sending modified serialized data and see how the application processes it.
- **Inter-Process Communication (IPC)**: Check IPC mechanisms where serialized data might be exchanged between different components of the application.

###  FUZZ testing - send random or malformed data to deserialization endpoints and see the how it process it.


# Server-side client aspect:
- Attacker sends malicious serialized data to the server.
- Server deserializes this data without proper validation or sanitization.
- During the deserialization process, the server executes harmful code or alters the application’s behavior.

# Client-side client aspect:
- **Client-Side Data Storage**: Client application stores data locally in a serialized format (e.g., cookies, local storage, files) and later deserializes it, an attacker with access to the client’s environment could manipulate this data to exploit the application.
- **Inter-Client Communication**: In some distributed systems or peer-to-peer applications, clients may exchange serialized data. Insecure deserialization in such scenario could lead to one client attacking another.

## Impact:
- RCE
- Privilege escalation
- Arbitrary file access
- DOS attack
- Data corruption

## How to prevent:
- #### Server-side mitigation
- Focus on securing endpoints where serialized data is received.

- #### Client-side mitigation
- Ensure serialized data stored on the client is protected (e.g., encryption, integrity checks).
- Validate and sanitize data before deserializing, even on the client side

- Deserialization of user input should be avoided unless absolutely necessary.
- Implement a digital signature to check the integrity of the data.
- Any checks must take place **before** beginning the deserialization process.[Validate and sanitize]

- Avoid using native serialization formats, Opt for safer formats like JSON, XML, or protocol buffers which do not support direct execution of code.
- Use libraries that support whitelisting of safe classes for deserialization, eg:use `PyYAML` with safe loading
- Run deserialization code in a low-privilege environment to limit the impact.
- Use containerization or sandboxing techniques


### Modifying serialized objects
- Serialization-based session mechanism is used.

Steps:
1. Burpsuite will identify the presence of serialized object.
2. Sending it to repeater, and decoding the cookie session with base64 and  into normal string.
3. It consist of 2 objects : username, admin role
4. By changing the boolean value to 1 from 0 (i.e b=1)


### Modifying serialized data types
-  Change the username to `administrator` and update the length of the `username` attribute to `13`
- Change the access token to the integer `0` as we remove access token string [need to remove the double-quotes surrounding the value]
- Apply changes
- Access the `/admin` which the path for `administrator` user pannel


### Using application functionality to exploit insecure deserialization
- Noticed `POST` request to `/my-account/delete`  available in page after logging in to own account.
- Noticed hat the serialized object has an `avatar_link` attribute and it contains he file path to avatar.
- Replaced the serialized data of  `avatar_link` pointing to `/home/carlos/morale.txt`.[update the length indicator]
```
s:11:"avatar_link";s:23:"/home/carlos/morale.txt"
```
- Apply changes which re-encode and update the request.
- After sending the request we get some access token 
- Replace it with the access token in the session cookie 


