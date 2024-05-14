
| Serialization                                                                     | Deserialization                                        |
| --------------------------------------------------------------------------------- | ------------------------------------------------------ |
| Changing the format of data that is going to tranfer in network                   | Changing back to original format                       |
| Eg: Transfering a table in parcel service by dismantaling it(changing the format) | Assembling back the table at the destination to use it |
Similar names for serialization in other programming language

| Ruby        | Python   |
| ----------- | -------- |
| Marshalling | Pickling |
Impact:
- RCE
- Privilege escalation
- Arbitrary file access
- DOS attack

How to prevent:
- Deserialization of user input should be avoided unless absolutely necessary.
- Implement a digital signature to check the integrity of the data.
- Any checks must take place **before** beginning the deserialization process.

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
