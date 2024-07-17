Lab 1:-
Aim:  user admin@libcurl.so has stored the key as a secret in their account, retrieve it.
Technique: 
- As there is no user creds given for signup. we created a new user and logged in. 
- After logging in, created New secret.
- After creating the secret we can find the value in the URl is passed as URL/secret/3.
- After changing the ID from 3 to 1 we can view the admin@libcurl.so details in which we can see the key.

Lab 2:-
Aim: user admin@libcurl.so has stored the key as a secret in their account, retrieve it.
Technique: 
- As there is no user creds given for signup. we created a new user and logged in. 
- After logging in, created New secret.
- After creating the secret we can find the value in the URl is passed as URL/secret/3.
- We tried to intercept and do the same above process but got Unauthorized. We can see a JWT Authorization token is passed in the request. when we try to decode the token(Base64 Decode) we can see a ID value is passed in the token. Eg: eyJpZCI6MiwiZXhwIjoxNzE3MTQ1NjY0fQ - {"typ":"JWT","alg":"HS256"}{"id":2,"exp":1717145664}. we changed the ID value to 1 and base64 encoded, also change the url value and sent the token hoping it wont verify the signature. It accepts and we view the admin@libcurl.so details.

Lab 3:-
Aim: user admin@libcurl.so has stored the key as a secret in their account, retrieve it.
Techinque: 
- When we tried the same technique as in the previous labs we get a unauthorized message.
- They are verifying the signature.
- So we needed to decode the signature part to create our own JWT token to get the details.
- We copied the JWT token and pasted it in a notepad and saved it in kali linux. we then opened the terminal and opened the notepad with john the ripper
- Decoded the signature value which is "Secret". 
- We then created our own JWT token with the signature and changed the ID value from 2 to 1 and we were able to see the admin@libcurl.so details.

Lab 4:-
Aim: To find a hidden endpoint by review the JavaScript of the page .
Technique: 
- We were given a hint to check the HTML Page and with the application name to find the hidden endpoint. 
- The app name was Aprroval and while searching the source page we can see there is a endpoint where we can request all the secrets by accessing the endpoint(get_secrets). 
- We copy the URL, add the endpoint and send the request. 
- We get the lab key and it is solved
	#note Worth looking for **$http.post** in source code

Lab 5:-
Aim: To find a hidden endpoint which is compressed by reviewing the JavaScript of the page .
Technique: 
- This was similar to the previous lab but this time the code has been compressed and this time the endpoint is(get_more_secrets).
- Copy the URL, add the endpoint and send the POST request.
- We got the secret key

Lab 6:- 
Aim: To review the JavaScript of the page to find a hidden endpoint with more difficulty than previous one.
Technique: 
- This was similiar to the previous labs but we were given a hint to use the right headers and cookies. 
- When scrolling throught the source page we can see a csrf token passing we copy the token value and look for the correct header. while searching for csrf header name we could not find any, but while searching for xsrf we get a header name (X-XSRF-Token). 
- Copy the new header and token and add a new header xsrf token and  send this along the request which solves the lab.

Lab 7:-
Aim: To find an information leak by reviewing the JavaScript of the page .
Technique: 
- In this Lab it is hinted that there is a information leak and we go through the source page of the application. 
- Since the source page is too big to search we find using the key words like keys, Approval and we solved the lab

Lab 8:-
Aim: To log in as admin
Technique: 
- In this lab we have to log in as admin. 
- We registered a new account and tried to change the parameters but there were no id's. 
- We can see the password reset functionality. 
- We know the admin mail id so when we submit the mail in reset box we see the reset link is sent in the response. 
- We copy the URL and we change the password for admin and we log in as admin to solve the lab.

Lab 9:-
Aim: To log in as admin
Technique: 
- This is same as previous one. but in the reset password response there is no link for reset password. 
- Noticed the version is v2 in the url and change the version to v1 and we get the link for reset password.

Lab 10:-
Aim: To register as admin
Techinque: 
- When we try to sign up as an login account with admin '@libcurl.so' application blocked you to create an account, as already it is assigned for admin. 
- So we created like admin '@libcurl.so@gmail.com', and we created successfully.
- But while logging in the front end dosn't allow the admin@libcurl.so@gmail.com, since a mail id cannot have 2 '@' symbols.
- So we inspected the mail input field and changed to text.
- Doing the above able t login as admin with our own created mail id.

Lab 11:-
Aim: To register as admin
Techinque:
- Tried the same like above lab, but unable to complete the signup process.
- We try admin@@libcurl.so.gmail.com, and this time it worked.
- So using the above created mail id, we can login as admin.

Lab 12:-
Aim: To Register as admin
Techinque: 
- Same as above lab, this is it restrict and check for proper mail id so instead of adding (@ and .) 
- So tried with admin@libcurlaso and signed up.
- Using the above created mail id, we can login as admin.

Lab 13:-
Aim: To Register as admin
Techinque: 
- Same as above lab, this is it restrict and check for proper mail id so instead of adding (@ and .) 
- So tried creating same as admin mail id with dotless i (ı) like admin@lıbcurl.so and signed up.
- Using the above created mail id, we can login as admin.

Lab 14:-
Aim: Login as admin by cracking the hashed password.
Techinque: 
- The username and hashed password were given.
- Tried logging in with the password, but unable to do because the password is encrypted and sent to server.
- So intrecepted and used the given password instaed of the hashed password.
- And logged in as admin.

Lab 15:-
Aim: To decrypt the leaked encrypted password of admin.
Techinque: 
- While looking into source code can able to get the encryption and decryption function.
- Using that able to decrypt the password of admin and got the key.
### API Payments 01
- Add the product to cart and checkout.
- In /payment endpoint api, url for payment success is sent in url.
- So we can purchase the product without making payment by accessing the url directly.
### API Payments 02
- Add the product to cart and checkout.
- In /payment endpoint api, url for payment success is sent in url.
- We can tamper the amount to be paid, and lower the amount.
- And made the payment seccessfully.
### API Payments 03
- Add the products to cart and checkout.
- In /checkout endpoint api, change the value of one product to negative value(-1000).
- After forwarding the endpoint, we can see that the amount to be payed for the product is reduced.
### API Payments 04
- Add the products to cart and use the voucher.
- Send the endpoint to the repeater and send 8 times to bring the amount to below 10 dollars.
- Now the voucher is applied multiple times and the amount is reduced
### API Payments 06
- Add the product to cart and checkout.
- Able to change qunatity value (0.01).
- And the amount is reduced while making payment.
key: 87901387-c6d6-44d4-9002-3bb89e49f8d4
### Mongo IDOR
- In one of  the api end point (/api/v1/accounts/) got some of the user id informations disclosed.
- Used that oid value (6694b38306b989000937d68f) in the url (/api/v1/accounts/6694b38306b989000937d68f) and  got the informations about admin
key: 5188655e-84ed-472a-8fdd-f9906c8bac2a


