# Table of Content

- [1. Low Severities](#Low)
  * [Self-XSS](#self2)
  * [Hidden Directories](#Hidden Directories)
  * [Cross-Site Request Forgery](#CSRF)
  * [No Password Policy](#noPass)
  * [Weak Reset Password Implementation](#WeakPass)
  * [Automatic User Enumeration](#userE)
  * [No Password Required for account deletion](#nopass)
  * [Simultaneous sessions are being kept active on the same browser](#session)
- [2. Medium Severities](#Medium)
  * [No rate limiting](#nolimit)
  * [Failure to invalidate the session after password change](#Fsession)
  * [Clickjacking](#click)
  * [Bruteforce of password leading to account takeover](#brutepass)
- [3. High Severities](#High)
  * [Insecure Direct Object Reference](#IDOR)
  * [Server-Side Request Forgery](#SSRF)
  * [Stored-XSS](#Stored-XSS)
  * [Stored-XSS](#Self-XSS)
  * [Account takeover via IDOR](#IDORt)
  * [Blind SSRF](#bssrf)
  * [Chaining of IDOR with XSS](#ccix)
- [4. Critical Severities](#Critical)
  * [JWT Authentication](#JWT)
  * [NoSQL Injection](#noSQL)

In  this section we will talk about the solution to all the vulnerabilities present on the Threads application. Threads web application consist of various vulnerabilities with different levels of severity like Low, Medium, High & critical. Vulnerabilities and their solution  are mentioned according to their category below :

# Low <a name="Low"></a>

## Self-XSS <a name="self2"></a>


1. This self-xss is present inside the chat box option given in the application.

2. Simple javascript payload containing script tag will  work. Like:
```
<script>alert(0)</script>
```
![xss2](/images/s2.png)

3. Use the above script and you will see the pop up from the chat box.
![xss3](/images/s3.png)

## Hidden Directories <a name="Hidden Directories"></a>

1. We can use [Dirsearch tool](https://github.com/maurosoria/dirsearch) to enumerate the hidden directories from the application with and without login. Perform directory brute forcing on application to find the hidden end points of the application.

2. You can get [Dirsearch tool from here](https://github.com/maurosoria/dirsearch) and after the installation use this command in your terminal. 
```
python3 dirsearch.py -u <URL> -e <EXTENSIONS>
```
 Example : 
```
python3 dirsearch.py -u http://localhost:3000 -e html,php
```
3. The http status code 500  is shown  on the directory  **/management** when enumeration is done after login. Which looks interesting.
![dir](/images/dirsearch.png)

4. These can be accessed by using the endpoint after login with the domain name in the URL in your browser. Like **http://localhost:3000/management**. It shows a message Not Authorized after visiting http://localhost:3000/management.
![dir](/images/dirsearch2.png)

5. There is an  email for the admin account which is **admin@threadsapp.co.in**. You can use this email to register again as an admin from registration and see if it allows it or not.
![dir](/images/dirsearch3.png)

6.Here it allows you to login as admin after registration
![dir](/images/dirsearch4.png)

## Cross-site Request Forgery <a name="CSRF"></a>

1. Open two accounts. One on the main browser window and the  other on incognito mode.

2. Let's name them browser A and browser B.

3.  On browser A open your profile  page and edit anything. Put your intercept on in your BurpSuite and click on update button in your profile page to update the credentials you have changed. This outgoing request will be captured in BurpSuite.

4. Check this outgoing request in your BurpSuite, following window will appear, right click on it > Engagement Tools > Generate CSRF POC.
![csrf](/images/csrf.png)

5. On the following window, you can see various details like name, email, password etc. Now you can change the password here by typing it on ”password” and ”confirm password” value but make sure that you change the user Id value to the browser B user id, else the attack won't work.
![csrf](/images/csrf2.png)

6. Click on "Test in browser" button given below and copy the code which will be displayed.

7. Open browser B and enter the code there and hit enter.

8. Click on Submit Request.
 

## No Password Policy <a name="noPass"></a>

1. Create a new account, On the password section enter any password like: “123”,”Abc” or  just copy paste the email used.
2. This is an example of no password policy being in place.
3. Some policies suggest or impose requirements on what type of password a user can choose, such as:
   - The use of both upper-case and lower-case letters (case sensitivity).
   - Inclusion of one or more numerical digits.
   - Inclusion of special characters, such as @, #, $.
   - Prohibition of words found in a password blacklist.
   - Prohibition of words found in the user's personal information.
   - Prohibition of use of company name or an abbreviation.
   - Prohibition of passwords that match the format of calendar dates, license plate numbers, telephone numbers, or other common numbers.

## Weak Reset Password Implementation <a name="WeakPass"></a>

1. Create an account with the password – “123456”. Now go to my account section and change password.
![Weak](/images/weak.png)

2. Enter the new password the same as 123456.
![Weak](/images/weak2.png)

3. It will be updated however the same password will be used.
![Weak](/images/weak3.png)

4.This is an issue most of the major websites have policies which disables you to use/enter your old password as your new password.
![Weak](/images/weak4.png)

## Automatic User Enumeration <a name="userE"></a>

1. Open the login page and enter any random credentials.

2. If the email is correct and password is wrong, it will display an error like ” Password is incorrect”.
![user](/images/userE.png)

3. If the email is wrong, then it will display an error like ”Email not found”
![user](/images/userE2.png)

4. This is bad security practice.What we can do is add a neutral code like ”Email id or password is incorrect, kindly recheck” this way others won't be able to enumerate what users are registered on the website.

## No password required for account deletion <a name="nopass"></a>

1. The removal of an account is one of the sensitive parts of a web application that needs to be protected, therefore removing an account should validate the authenticity of the user, however  when removing an account, the system did not require the user to input the account password.

2. The user logins to a shared computer (office, library, cafe)and  leaves the account open.

3. Intruder came and tried to delete the user's account Intruder can easily delete the account because the system did not protect it by asking the password to validate that the person deleting the account is the real user.

## Simultaneous sessions are being kept active on the same browser <a name="session"></a>

1. Login using any user on one tab and go to the account section.

2. Open a new tab and go to profile again.

3.  Logout from that browser.

4. Login with a different account.

5. Now go to account section again and now you can change the details of both the accounts without any interference


# Medium <a name="Medium"></a>

## No rate limiting <a name="nolimit"></a>

1. Make two accounts and create a post from 1st account.
![limit](/images/number.png) 

2. Then go to the second account and add a comment from that account to the post created by the first account.

3. Capture the comment  request in the Burpsuite proxy. 
![limit](/images/number2.png)

4. Send this request to the intruder tab in the BurpSuite proxy.

5. Set the target in target section 
![limit](/images/number3.png)

6. In positions section  change accept language  so it can look like this "Accept-Language: en-US,en;q=0.§5§" so we can bruteforce continously.
![limit](/images/number4.png)

7. Go to payload section select and add numbers 1 to 100 (or set any limit according to how many comments you want to add on that post) and then start the attack.
![limit](/images/number5.png)
![limit](/images/number6.png)

8. Check the application and the comment will be added as many times till which you have set the limit.
![limit](/images/number7.png)

## Failure to invalidate the session after password change <a name="Fsession"></a>

1. The application Failure to invalidate session after password. In this scenario changing the password doesn't destroy the other sessions which are logged in with old passwords.

2. Open the same account on two browsers.
![failed](/images/cpass.png)

3. Let's say they are browser A and browser B.

4. Change the password on browser A.
![failed](/images/Cpass2.png)

5. The session on browser B will still be active.
![failed](/images/cpass3.png)

6. It’s a failsafe kept by many big organizations to invalidate all the concurrent sessions whenever someone changes.

## Clickjacking <a name="click"></a>

1. Clickjacking is an attack that tricks a user into clicking a webpage element which is invisible or disguised as another element this can cause users to unwittingly download malware, visit malicious web pages,provide credentials or sensitive information, transfer money, or purchase products online.

2. Make a html file with the following code:
![clickjacking](/images/clickjacking.png)

3. Instead of http://www.yoursite.com/sensitive-page enter the profile link. The URL which shows when you open your profile.

4. Open that file you saved. If the following is shown then its clear that the website is vulnerable to clickjacking.
![clickjacking](/images/clickjacking2.png)

## Bruteforce of password leading to account takeover <a name="brutepass"></a>

1. Go to the login page then enter the email id of the victim, enter any random password then before clicking on login, open burpsuite and click on intercept to intercept this request. Now click on login.

2. The following request will pop up on burpsuite
![brutepass](/images/brutepass.png)

3. Send this request to the Intruder tab. Then click on position tab which comes under intruder tab and then click on clear button present on the right side.
![brutepass](/images/brutepass2.png)

4. Now highlight the password value and click the add button on the right side.
![brutepass](/images/brutepass3.png)

5. Now go to the payloads section. You can use a custom password dictionary from the internet which contains a list of very common passwords. For ease of access  we will use the numbers section and set payloads as 1 to 1000 because our password is "123" which lies between no. from 1 to 1000.
![brutepass](/images/brutepass4.png)

6. Click on start attack option and then check the status section
![brutepass](/images/brutepass5.png)

7. Here we can see that the payload number 123 has status 200 code (HTTP OK success code). Thus the correct password was 123 to which it showed 200 status code this will let the attacker know your correct password. 


# High <a name="High"></a>

## Insecure Direct Object Reference <a name="IDOR"></a>

1. The IDOR is present in the  deletion of a post or on the comment of that post.

2. As you can see that there is a feature present in the Threads application where a user can delete only and only his post that he has created and also he can delete only his comment that he has made on a post. So a user cannot delete any other users post but as mentioned that there is an IDOR vulnerability in the application so it can be done using the BurpSuite.

3. This IDOR vulnerability is present in the section of deletion of a post or comment which means we can delete any other users post or comment though it’s not possible with the feature  inside the application but we can do it with the help of BurpSuite.So open your BurpSuite and connect it with the  browser you are working on.

4. To solve  this vulnerability  we need two accounts. So let’s login with our first account (let's say the first account name is “Pentester”) and create a post with our first account.

5. As you can see the post from the Pentester account has been created.
![IDOR](/images/idor1.png)

6. Similar way to create a post from another account "Pentester2" account.
![IDOR](/images/idor3.png)

7. For the post created by Pentester2,  create a delete request and capture it in burp. Captured request sends it to the repeater tab.
![IDOR](/images/idor4.png)

8. Now as you know that Pentester2  can delete only his post which he created according to the feature of the application but as we see application is deleting post by UUID of post so we can try to delete other users post too by replacing it with post of pentester ID's.
![IDOR](/images/idor5.png)

9. Get the UUID of the pentester post and replace it with pentester2 UUID.Once that is done.Right click and show response in Browser, it will delete the post of the user Pentester you can check it by refreshing the page. 
![IDOR](/images/idor6.png)


11. So basically Pentester2 made his deletion request as a bait so instead of his post UUID he can replace it with Pentester UUID and send that deletion request to delete another user post from his account.

## Server-Side Request Forgery <a name="SSRF"></a>

1. So there is a simple ssrf which is present in the profile section of this application under the url field which is used for uploading an image via url.
![SSRF](/images/ssrf1.png)

2. So the /etc/passwd file is leaking which we have to find out.The /etc/passwd file on Unix systems contains password information. An attacker who has accessed the etc/passwd file may attempt a brute force attack of all passwords on the system. An attacker may attempt to gain access to the etc/passwd file through HTTP, FTP, or SMB. Typically this is done through one of the CGI scripts installed on the server, so this event may be seen in conjunction with other events of that type.

3. So go to your profile section the  URL field  is used to upload an image via URL.

4. So now let’s use the file protocol on this field and try to get /etc/passwd file from the server. Use **file:///etc/passwd** on the url field to fetch the file from the server.
![SSRF](/images/ssrf2.png)

5. Update this and open your profile section again and you will see that  in the image section there is no image because obviously that was not an url for uploading an image.
![SSRF](/images/ssrf3.png)

6. Now go to the profile image section on left side and right to copy the image location. Open a new tab and paste the image location and it will ask you to save the image as shown in below image
![SSRF](/images/ssrf4.png)
	
7. Open your terminal and go to where you have saved that image and open that image file using the nano command “nano [file name]”.
![SSRF](/images/ssrf5.png)

8. As you can see now you have access to the /etc/passwd file which you have accessed using file protocol.

## Stored-XSS <a name="Stored-XSS"></a>

1. This XSS is present inside the profile section inside the website input field. It is a stored XSS which executes only when other users visit the profile of a user whose website field is updated with malicious script. So first let’s go to the  profile section and put a simple javascript  inside the website field. 
```
<script>alert(“Hacker”)</script>
```
![Stored](/images/stored1.png)

2. Now login to another user account and visit the website updated user profile.
![Stored](/images/stored2.png)

3. once you click on view the xss will execute
![Stored](/images/stored3.png)

## Stored-XSS <a name="Self-XSS"></a>

1. The stored-xss is present in the name field of the profile section.So go to your profile by clicking on Account button:

2. In name field enter a script as input:
```
><script>alert(1)</script>
``` 
![XSS](/images/self2.png)

3. Click on update. After this you will see that the entered XSS payload is getting  executed on your browser and it will keep getting executed whenever you try to access your profile section through the icon given on the home page or profile name.
![XSS](/images/self3.png)

4. Another execution end point : When you visit the same user profile from another user given script executes
![XSS](/images/self4.png)

## Account Takeover via IDOR <a name="IDORt"></a>

1. Create two Accounts for eg. nodie and nodiea
![IDOR](/images/idort1.png)
![IDOR](/images/idort2.png)

2. Now login and capture the account update request of both the accounts and send them to the
repeater. as you can see each user is having a unique UserId
![IDOR](/images/idort3.png)
![IDOR](/images/idort4.png)

3. So you can change the UserId(6045dca4ac25b3049e46a6c6) of one user(nodie) with
another(nodiea) UserId(6045ef9cac25b3049e46a6c7) and update the data in input field which
you want to change for another user(nodiea)for eg. Changing the name of user nodiea to
something else shown in screenshot.
![IDOR](/images/idort5.png)

4. As in the above screenshot it responded with 200 ok and that means our request worked
completely ,let's cross check it by login to another user account.

5. Similar ways other actions can be done by changing the UserId like: changing the
password,Bio,Website,User Name etc.

## Blind SSRF <a name="bssrf"></a>

1. Application's upload image via image url is vulnerable to blind SSRF in the account section.
![Blind SSRF](/images/bssrf1.png)

2.Now goto burp pro and get the http url or host your own server to get the request from the application server.
![Blind SSRF](/images/bssrf2.png)

## Chaining of Idor with XSS <a name="ccix"></a>

1. Login with your account and go to home

2. From there choose a victim whose name you want to change.
![ccix](/images/ccix.png)

3. Open there profile and copy there ID from the URL
![ccix](/images/ccix2.png)

4. Then open your account and update your information and capture that request.
![ccix](/images/ccix3.png)

4. In the captured request change the name to **"><img src=x onerror=alert(0)>** and change the id at the bottom to the victim’s id which you copied.
![ccix](/images/ccix4.png)

5. Forward this request . Now open the profile again and an xss will pop up.
![ccix](/images/ccix.5png)
![ccix](/images/ccix6.png)


# Critical <a name="Critical"></a>

## JWT Authentication <a name="JWT"></a>

1. So basically first you have to create an account with the given email ID **admin@threadsapp.co.in**  because that is the hardcoded email.

2. There is a /management page which has an authentication system which works via JWT API .

3. Visit this registration page in the application  and register with the above email admin@threadsapp.co.in. Capture this sign-in request  going out of your browser with the  BurpSuite proxy.

4. Open the proxy tab in your BurpSuite to check the outgoing request.Copy this request and send it to the repeater.

5. In the repeater tab send that request to check out the response for that request. As you can see in the response section you have got the token written with green ink. Now copy that token and send it to [jwt.io](https://jwt.io/ ). 
![JWT](/images/jwt1.png)

6. The JWT token is divided in three parts: algo,payload,signature.
![JWT](/images/jwt2.png)

7. So basically we have to find out the secret key used in this token in the signature section because the new token which we will make it for the unauthorized user to access the admin section should  have the same signature .

8. The application matches the signature with the signature present on the server side to give access to our unauthorized user. Mainly  we have to fool the application showing them that we are an admin user  and we are entering as an admin.

9. To crack the secret key inside this token you can use this tool [JWT secret key cracking tool](https://github.com/ticarpi/jwt_tool/blob/master/jwt_tool.py) . Click on the link provided and install the tool  in your system.

10. To do brute forcing we need a wordlist file (where many passwords are given) which we can use for getting the secret key. So for this application the wordlist file is given on Enciphers github visit this link provided  and save this file[Password file](https://github.com/enciphers/WebHacking-Training-Resources/blob/master/jwt-wordlist). Save this file with any  name in your system  like `passwd.txt`.

11. Now   use this tool to find the secret key. Go to your terminal and open wherever you installed that tool. Syntax of the  command is:

```
python3 jwt_tool.py  [jwt token you got on your BurpSuite] -C -d  [the wordlist file you saved]
```
12. The command is  example(according to the jwt token I got and the wordlist file I saved with the name of jwt-wordlist.txt ):
```
python3 jwt_tool.py eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJlbWFpbCI6ImFiaGluYXZAZW5jaXBoZXJzLmNvbSIsImlhdCI6MTYwNDY1OTIwNiwiZXhwIjoxNjA0NjY5MjA2fQ.2CrCcllttd02ktDrnUlUFiWj-oHxd4HXvf20yiYAo1M -C -d jwt-wordlist.txt
```
![jwt](/images/jwt3.png)

13. As you can see the secret key for the token is **thr3@ds@000**. Use this key in this website [JWT.io](https://jwt.io/)  to encode a new token for your account(unauthorized user). Then use that token while logging in on the management page with your account. You will get the access with your account in the management section.

## NoSQL Injection <a name="noSQL"></a>

1. With the help of NoSQL Injection we can login as admin and also as another user without knowing their password .

2. Open Login page.

3. Enter anything in email and password field.
![nosql](/images/nosql.png)

4. Now click on login and intercept this outgoing request on burpsuite.
![nosql](/images/nosql2.png)

5. If you check this request so instead of the values which are going in the email and name  field change them with this payload : {“$gt”:””}

6. So the new values which will be going through the email field and name field will be : {“email” :{“$gt”:””}},”password”:{“$gt”:””}}
![nosql](/images/nosql3.png)

7. Now forward this request and you will see you will get login to the application with the admin accounts credentials. This is because :{“$gt”:””} automatically selects  the very first data entries in the database which are always of an admin and make you login with the admin account.
![nosql](/images/nosql4.png)

8. Let’s say if you want to enter another user’s account so for that you just have to know the mail of that user's account.

9. Go to the login page, enter that user’s mail and enter anything on the password field.
![nosql](/images/nosql5.png)

10. Now click on login and intercept this outgoing request from the browser.
![nosql](/images/nosql6.png)

11. Now just change the value of password to : {“$gt”:””}
![nosql](/images/nosql7.png)

12. Forward this request and you will be logged in as the user whose email you have used.
![nosql](/images/nosql8.png)


Happy learning,Happy hacking!




