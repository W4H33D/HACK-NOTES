# TryHackMe Room: OWASP Top 10


Task 5 (command injection practicle)

Q1:  What strange text file is in the website root directory? 
A: drpepper.txt

Q2: How many non-root/non-service/non-daemon users are there?
A: 0

Q3: What user is this app running as?
A: www-data

Q4: What is the user's shell set as?
A: /usr/sbin/nologin

Q5: What version of Ubuntu is running?
A:  18.04.4 
 ## this is found by seing the lsb-release file in /etc/ directry

 Q6: Print out the MOTD.  What favorite beverage is shown?
 A:  DR PEPPER
## This is found by first use command "locate motd" they show you list of file that have this name but in the Question hint we have "00-header" and one of the file have this name so just cat/view content of that file and you found that answer


Tast 7 (Broken Authentication Practical)

Q1: What is the flag that you found in darren's account? 
A:  fe86079416a21a3c99937fea8874b667 
## just folow the mentioned instruction in the task 

Q2: What is the flag that you found in arthur's account?
A:  d9ac0f7db4fda460ac3edeb75d75e16e
## repeat the same process

Task 11 [Severity 3] Sensitive Data Exposure (Challenge) 

Q1:What is the name of the mentioned directory? 
A: /assets
## read the source of login page of the website there is a comment by the develper the give to this directory 

Q2: Navigate to the directory you found in question one. What file stands out as being likely to contain sensitive data?
A: webapp.db 
## As we learned the previous section about the "flat_file" database that store in the disk of the computer

Q3: Use the supporting material to access the sensitive data. What is the password hash of the admin user?
A: 6eea9b7ef19179a06954edd0f6c05ceb
## This is find by follow the previouse mentioned step for asessing the database file and how to dump file just replace the name of the database in the command with the users directory in the database

Q4: What is the admin's plaintext password?
A: qwertyuiop
## go to the crackstaion website and paste the hash and they give you the password

Q5: Login as the admin. What is the flag?
A: THM{Yzc2YjdkMjE5N2VjMzNhOTE3NjdiMjdl}
## login with the username: admin and password:qwertyuiop in the website and you lodin in the admin account and they give you the flag


Task 16 [Severity 4] XML External Entity - Exploiting

Q3:What is the name of the user in /etc/passwd
A:falcon

Q4:Where is falcon's SSH key located?
A:/home/falcon/.shh/id_rsa		

Q5:What are the first 18 characters for falcon's private key
A: MIIEogIBAAKCAQEA7b



Task 18  [Severity 5] Broken Access Control (IDOR Challenge) 


Q3: Look at other users notes. What is the flag?
A: flag{fivefourthree} 
## this can be find by changing the value of the parameter to 0 like this http://{machine IP}/note.php?note=0



Task 19  [Severity 6] Security Misconfiguration 

Q2: Hack into the webapp, and find the flag!
A: thm{4b9513968fd564a87b28aa1f9d672e17}

## this can be find by first search for the webapp name to google they show you the github page for this webapp there you will find default username and password



 Task 20 [Severity 7] Cross-site Scripting 

Q2: Navigate to http://MACHINE_IP/ in your browser and click on the "Reflected XSS" tab on the navbar; craft a reflected XSS payload that will cause a popup saying "Hello".
A: ThereIsMoreToXSSThanYouThink

## first you have to register your account in the webapp and then you have to go to the reflected xss tab in the browser you find it on menu bar on top and there you have to paste the payload that are describe priviously and that is 

payload = <script>alert(“Hello World”)</script>


Q3: On the same reflective page, craft a reflected XSS payload that will cause a popup with your machines IP address.
A: ReflectiveXss4TheWin

## this can be find by using the script describe in the hint and are following

## hint: In Javascript window.location.hostname will show your hostname, in this case your deployed machine's hostname will be its IP.

## from this hint "window.location.hostname" is the script use this in the following manner to gain flag

<script>alert(window.location.hostname)</script>

that do the trick

Q4 Now navigate to http://10.10.251.222/ in your browser and click on the "Stored XSS" tab on the navbar; make an account.

Then add a comment and see if you can insert some of your own HTML.
A: HTML_T4gs

## This will be done by using some html tag on the comment box and they will do the trick use the the following or take this as a refrence 

Code:
        <html>
               <title>
                You are being Hacked
               </title>
                                      <body>
                                      <b>Happy</b><i>Hacking</i>
                                      </body>
        </html>

## this is the html code i think that will do the trick


Q5: On the same page, create an alert popup box appear on the page with your document cookies.
A: W3LL_D0N3_LVL2

## this flag can be find by adding a script in the comment called "document.cookie" this will popup the cookie as an alert

script : <script>alert(document.cookie)</script>

This will do the trick

Q6: Change "XSS Playground" to "I am a hacker" by adding a comment and using Javascript.
A: websites_can_be_easily_defaced_with_xss

## we get this flag by use the hint script in the comment box and they will show the answer in the page after question no 3 

script : <script>document.querySelector('#thm-title').textContent = 'I am a hacker'</script>



Task 21 [Severity 8] Insecure Deserialization 

Q1: Who developed the Tomcat application?
A: the Apache Software Foundation

## this will find by google tomcat developer and they show the name and also see the tomcat wiki 
## note This is company name not person name 

Q2: What type of attack that crashes services can be performed with insecure deserialization?
A: denial of services



Task 22 [Severity 8] Insecure Deserialization - Objects 

Q1: if a dog was sleeping, would this be:
A: a behaviour



Task 23 [Severity 8] Insecure Deserialization - Deserialization 

Q1:  What is the name of the base-2 formatting that data is sent across a network as?  
A: binary



Task 24 [Severity 8] Insecure Deserialization - Cookies 

Q1:  If a cookie had the path of webapp.com/login , what would the URL that the user has to visit be? 
A: webapp.com/login

Q2: What is the acronym for the web technology that Secure cookies work over?
A: https



 Task 25 [Severity 8] Insecure Deserialization - Cookies Practical 

 Q1: 1st flag (cookie value) 
 A:THM{good_old_base64_huh}
 
 ## this will be find by going to the inspect element mode of the machine after registration and login and go to storage in the inspect mode there is alot of couloms there u will see the sessiondid coulom their value is base64 encoded just copy the all string and use it with the following command in kali linux terminal 

 command: echo '{you cookie string here}' | base64 -d

 this will decode the string and in output of the decoded data there you find that string 


 Q2: 2nd flag (admin dashboard)
 A:THM{heres_the_admin_flag} 

 ## this will find by first changing the userType value in the inspect element of storage menu with admin like this 

 userType admin 

 after that change the url with http://machine_ip/admin 
 there you will go to the admin page and there u find that flag

 

 Task 26 [Severity 8] Insecure Deserialization - Code Execution

 Q1: flag.txt
 A: 4a69a7ff9fd68

 ## this will find by fist follow the step describe in the room first download the exploit and change its value with your ip and run the exploit this give you a string just copy the string in the '' 
 ## after that go to the myprofile section again by re register your user account and then go to inspect element and then storage their change the value of encodedpayload section with value you just copy and then click on the provide feedback section in webapp after that you will find you shell in your lisner 
 ## note setup your lisner first


 ## when you get the shell back so use the following command to get your flag 
 command: cat /home/cmnatic/flag.txt

 

 Task 27 [Severity 9] Components With Known Vulnerabilities - Intro 

 

 Task 28 [Severity 9] Components With Known Vulnerabilities - Exploit 

 

 Task 29 [Severity 9] Components With Known Vulnerabilities - Lab

 Q1:  How many characters are in /etc/passwd (use wc -c /etc/passwd to get the answer) 
 A: 1611 

 ## this will be find by first google it cse bookstore exploit and try to find the exploit on in my case exploit is this

 link: https://www.exploit-db.com/exploits/47887

 download it and try to run this.note this is python3 exploit use the the following command

 python3 47887.py

 this will show you an error about url so the following command insted 

 python3 47887.py http://machine_ip

 so this will try to exploit it so and they will ask you to launch the shell here so press y and you have a shell now so use the following command to get the answer of the question 

 command: wc -c /etc/passwd

 this will show you the answer 

 

 Task 30 [Severity 10] Insufficient Logging and Monitoring 

 Q1: What IP address is the attacker using?
 A: 49.99.13.16

 ## this will find in the login.txt file that you download there you see in this ip more then one login attempts occur in short priod of time with diffrent user accounts

 Q2: What kind of attack is being carried out?
 A: brute force

 ## this attack use for cracking the username and password attacker use this attack for making such request to the webapp

 

 Task 31 What Next? 

 

 ## 
I think now you got your badge for compleating the owasp top 10 good for u kuddos!
 ##
