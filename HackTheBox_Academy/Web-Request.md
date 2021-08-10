# HTB Academy 
## Module Web-Request 


### Question's in Request and Response Part 

**Q1: What method use in the request.**
**Ans: GET**

**Q2: what version use in the server**
**Ans: 2.4.46 **



### Question in the GET Method Part 

**Q: Send a GET request to flag.php with two parameters num1 and num2 such that their sum is 1337.**
**A: 5a72e87efbfbb08bf7365eb52f841f25**
Solution: By brute forcing to the  parameters using ffuf i found the two  parameters is "000" and "1337" so in the `http` request format that is like
```
http://{IP Address and Port Number}/flag.php?num1=000&num2=1337
```
They gave us a flag  


### Question in POST Method Part 

***Q: Login with the credentials (guest:guest), and try to get to the admin user from what you learned in this section and the previous section.**
**A: 607f8f255845ecb4b0b53fae4d3ef29e**
###### Solution:
The guest user have to cookie intercept it with burp and decode it in base64 this will somthing like this
```
guest_{some rendom number or string}
```
Every time you login this cookie same with the guest part but the random string will changing so they tell us they have lot of guest user but how about admin user site have only one admin so what happen if we do following.

Change the string "admin" into base64 we can do this using the following command 
 ```
 $ echo "admin" | base64 -d
 ```
 and use it as a cookie so we login as a admin 


### Question in PUT and DELETE Methods Part

**Q1: Create a file named "flag.php" with contents `<?=cat /flag.txt;?>` and request it to get the flag.**
**A: 989297b616767ea48ea42aed195e765e**
