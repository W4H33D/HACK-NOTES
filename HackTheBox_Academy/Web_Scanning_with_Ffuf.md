# HTB Academy 

## Module: Attacking Web Application with ffuf 




### Skill Assessment 


Qustion 1: `Run a sub-domain/vhost fuzzing scan on 'academy.htb' for the IP shown above. What are all the vhosts you can identify?`
Ans: `archive faculty test`
##### Solution:
Use the following command to get the `vhosts` but first make sure you add the DNS entry in /etc/host file  
```
$ ffuf -w wordlist.txt:FUZZ -u http://academy.htb:PORT/ -H 'Host: FUZZ.academy.htb' -fs xxx
```
There you have to replace `xxx` with the filter size number so do that properly to get the above answer

Question 2: `Before you run your page fuzzing scan, you should first run an extension fuzzing scan. What are the different extensions accepted by the domains?`
Ans: `.php .php7 .phps`
##### Solution:
Use the following command to get the valid extension
```
$ ffuf -w wordlist.txt:FUZZ -u http://SERVER_IP:PORT/indexFUZZ 
```

Question 3:`One of the pages you will identify should say 'You don't have access!'. What is the full page URL?`
Ans: `http://faculty.academy.htb:PORT/courses/linux-security.php7`
##### Solution:
Use the following syntax to get the above result as you find all the valid extension so try all the extension and you find that page 
```
$ ffuf -w wordlist.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ -recursion -recursion-depth 1 -e .php -v
```
There you have to change `.php` with all the extension you found to get the answer or you can add all the extension simultaneously like `.php,.php7,.phtml` etc 

Question 4:`In the page from the previous question, you should be able to find multiple parameters that are accepted by the page. What are they?`
Ans: `user username`
##### Solution:
Use this command to get that parameters.use the valid filters to get the result 
```
$ ffuf -w wordlist.txt:FUZZ -u http://admin.academy.htb:PORT/cources/linux-security?FUZZkey -fs xxx 
```
There also you have to change `xxx` with appropriate filter size value  

Question 5:`Try fuzzing the parameters you identified for working values. One of them should return a flag. What is the content of the flag?`
Ans: `HTB{w3b_fuzz1n6_m4573r}`
#### Solution
first scan for common sub directories. common sub directory found  `/courses`
this sub directory found on "archive" & "faculty"  subdomains. Now start page fuzzing scan to find the page that give us the page that show us the message **you don't have a permission**

The faculty sub directory have the page `linux-security.php7` that shows message **you don't have permission**

As they don't give us the permissions so that means we have to give him some other parameters also then the give us access so start parameter fuzzing scan. By doing parameter fuzzing using GET method found "user" parameter but when go to the following link 
```
http://faculty.academy.htb:PORT/courses/linux-security.php7?user=key
```
They show message **This method is no longer use** so we have to do this using `Post` method so by doing this they not show the result so i start finding more parameters and  found two parameters "user and username" now enter this parameter in the link like 
```
http://faculty.academy.htb:PORT/courses/linux-security.php7?username=key
```
They show message **You don't have access!**
As far i found parameter 'username' but for get the flag we also have the one more parameter for this 

In `Seclist` there is a lot of username word list available so by using the following word list i found the three username parameter 

WORDLIST: `SecLists/Usernames/xato-net-10-million-usernames-dup.txt`
 
 Found user parameter  'harry', 'Harry', 'HARRY'

so by sending POST request to all of the user parameter one of them give me the flag 

Command for the post request is following:
```
curl -X POST -d 'username={user parameter place here}' http://faculty.academy.htb:{port no here}/courses/linux-security.php7

```
The valid username parameter is third one 'HARRY'



# HTB CheateSheet


#### Word list  

##### Seclist
1.   Directory/Page Wordlist
     `SecLists/Discovery/Web-Content/directory-list-2.3-small.txt`                                 
2. Extensions Wordlist    
    `SecLists/Discovery/Web-Content/web-extensions.txt`                                                                                
3. Domain Wordlist  
    `SecLists/Discovery/DNS/subdomains-top1million-5000.txt`                                                                       
4. parameters wordlist     
    `SecLists/Discovery/Web-Content/burp-parameter-names.txt`                                                                     
##### Commands 
1.  Directory Fuzzing 
  `ffuf -w wordlist.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ`                                                                          
2.  Extension Fuzzing 
   `ffuf -w wordlist.txt:FUZZ -u http://SERVER_IP:PORT/indexFUZZ`                                                                     
3. Page Fuzzing
`ffuf -w wordlist.txt:FUZZ -u http://SERVER_IP:PORT/blog/FUZZ.php`                                                                  
4. Recursive Fuzzing
`ffuf -w wordlist.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ -recursion -recursion-depth 1 -e .php -v`                                   

5.  Sub-domain Fuzzing 
`ffuf -w wordlist.txt:FUZZ -u https://FUZZ.hackthebox.eu`                                                                         
6. VHost Fuzzing
`ffuf -w wordlist.txt:FUZZ -u http://academy.htb:PORT/ -H 'Host: FUZZ.academy.htb' -fs xxx`
Where `xxx` contains the appropriate numbers according to the size  

7.  Parameter Fuzzing - GET
`ffuf -w wordlist.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php?FUZZkey -fs xxx`                                                                                           

8. Parameter Fuzzing - POST
`ffuf -w wordlist.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'FUZZkey' -H 'Content-Type: application/x-www-form-urlencoded' -fs xxx`                         

9. Value Fuzzing
`ffuf -w ids.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'idFUZZ' -H 'Content-Type: application/x-www-form-urlencoded' -fs xxx`                                          

#### Misc Commands
1.  Add DNS entry 
`sudo sh -c 'echo "SERVER_IP  academy.htb" >> /etc/hosts'`                            
2.  Create Sequence Wordlist
`for i in $(seq 1 1000); do echo $i >> ids.txt; done`                       
3. curl w/ POST
`curl http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'idkey' -H 'Content-Type: application/x-www-form-urlencoded'`
