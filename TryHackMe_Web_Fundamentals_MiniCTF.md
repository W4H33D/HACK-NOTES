# TryHackMe Web Fundamentals Room 


### Mini CTF 


### Tasks


There's a web server running on http://MACHINE_IP:8081. Connect to it and get the flags!

    1. GET request. Make a GET request to the web server with path /ctf/get
    2. POST request. Make a POST request with the body "flag_please" to /ctf/post
    3. Get a cookie. Make a GET request to /ctf/getcookie and check the cookie the server gives you
    4. Set a cookie. Set a cookie with name "flagpls" and value "flagpls" in your devtools (or with curl!) and make a GET request to /ctf/sendcookie



### Task 1 

command:`curl http://10.10.81.75:8081/ctf/get`


Flag: `thm{162520bec925bd7979e9ae65a725f99f}`



### Tast 2


command:`curl -X POST --data flag_please http://10.10.81.75:8081/ctf/post`

Flag:`thm{3517c902e22def9c6e09b99a9040ba09}`


### Task 3

command:`curl http://10.10.81.75:8081/ctf/getcookie`

This command show msg "check your cookies" so you have to visit link with your browser and after that in firefox press F12 a pane is open then you are in develpor mode
or inspect mode there you will see storage option in that you find cookies 

Flag:`thm{91b1ac2606f36b935f465558213d7ebd}` 


### Task 4

Using firefox in develper/storage/cookies
change the cookie name and value with "flagpls" and reload the page

Flag:`thm{c10b5cb7546f359d19c747db2d0f47b3}`
