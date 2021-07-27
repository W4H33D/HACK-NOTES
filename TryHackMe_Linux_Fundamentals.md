                                                   TryHackme Linux Fundamentals Part 1
                                                   ===================================

Task 1: Introduction

Task 2: A Bit of Background on Linux

Q: Research: What year was the first release of a Linux operating system?
A: 1991

Task 3:  Interacting With Your First Linux Machine (In-Browser)

Task 4:  Running Your First few Commands

# Commands                   Discription

   echo                 Output any text that we provide

   whoami               Find out what user we're currently logged in as!

Q1: If we wanted to output the text "TryHackMe", what would our command be?
A: echo TryHackMe

Q2: What is the username of who you're logged in as on your deployed Linux machine?
A: tryhackme

# use whoami in terminal of diployed machine

Task 5:  Interacting With the Filesystem!

# Commands              Full Name

  ls                  listing
  cd                  change directory
  cat                 concatenate
  pwd                 print working directory

Discription

ls: This command is use to list all the content that exits in the directory

#  Pro tip: You can list the contents of a directory without having to navigate to it by using ls and the name of the directory. I.e. ls Pictures

cd: this command is use to change directory with any exitsting directory

cat: this command is use to seing the content of the file   

# Pro tip: You can use cat to output the contents of a file within directories without having to navigate to it by using cat and the name of the directory. I.e. cat /home/ubuntu/Documents/todo.txt

pwd: this command is use to print the full path of the current working directory

Q1: On the Linux machine that you deploy, how many folders are there?
A: 4
## use ls command

Q2: Which directory contains a file?
A: folder4
## use ls folder4

Q3: What is the contents of this file?
A: Hello World!
## cat folder4/note.txt

Q4: Use cd to navigate to this file and find out the new current working directory. What is the path?
A: /home/tryhackme/folder4
## first cd to folder4 and the use pwd command to get the full path of that file

Task 6:  Searching for Files

# Command

find: this command is use to search some specific file in the given directory with some criteria of our choice 

grep: this command is use to search some specific content in the file or terminal output

Q: Use grep on "access.log" to find the flag that has a prefix of "THM". What is the flag?
A: THM{ACCESS}
## use "cat access.log | grep THM " command

Task 7: An Introduction to Shell Operators

Symbol/Operator              Discription

&                     this operator allows you to run commands in the background of your terminal.

&&                    this operator allows you to combine multiple commands together in one line of your terminal.

>                     this operator is a redirector-meaning that we can take the output from a command(such as using cat to output a                      
                      file) and direct it elsewhere.

>>                    this operator does the same function of the > operator but appends the output rather than replacing (meaning                        
                      nothing is overwritten)

Q1: If we wanted to run a command in the background, what operator would we want to use?
A: &

Q2: If I wanted to replace the contents of a file named "passwords" with the word "password123", what would my command be?
A: echo password123 > password

Q3: Now if I wanted to add "tryhackme" to this file named "passwords" but also keep "passwords123", what would my command be 
A: echo tryhackme >> password

Task 8: Conclusions & Summaries

Task 9: Linux Fundamentals Part 2


                                                   TryHackme Linux Fundamentals Part 2
                                                   ===================================

Task 1:  Introduction

Task 2:  Accessing Your Linux Machine Using SSH (Deploy)

Task 3:  Introduction to Flags and Switches

# some command need some argument to extend the behaviour of the command is called flag or switches. this can be identified by the hyphen for example the command 
$ ls -al 
## there "ls" is the command that shows the content in the directory but they are not show us the hidden files that are in the directory and also not show us the permissions of the files so by giving him the argumane "-al" this will show us all the hiddend and the permissions of the file and directories.
### Most of the commands accept --help or -h flag or switch that show us the detail of the accepted flags or switches that the command support.

The Man(ual) Page
-----------------
The manuals are the great source of knowing about the system commands and application these can be find in system or online

To access the manual in linux there is a command called "man" that is use to show tha manual of the command if they have it.

Q2:What directional arrow key would we use to navigate down the manual page?
A: down

Q3: What flag would we use to display the output in a "human-readable" way?
A: -h

Task 4:  Filesystem Interaction Continued


command            Discription

touch            this command is use to create a file

mkdir            this command is use to create a directory

cp               this command is use to copy files and directry to some other place

mv               this command is use to move files and directry to some other place or you can also use this command to rename the file and directry                   

rm               this command is use to remove file and directry

file             this command is use to determine the type of the file 

# Protip: Similarly to using cat, we can provide full file paths, i.e. directory1/directory2/note for all of these commands

Q1: How would you create the file named "newnote"?
A: touch newnote

Q2: On the deployable machine, what is the file type of "unknown1" in "tryhackme's" home directory?
A: ASCII text
## first connect to the machine with ssh then use file unknow1 to see the file type

Q3: How would we move the file "myfile" to the directory "myfolder" 
A: mv myfile myfolder

Q4: What are the contents of this file?
A: THM{FILESYSTEM}
## use commmand cat myfile to see that flag

Task 5:  Permissions 101

Q1:On the deployable machine, who is the owner of "important"?
A: user2
# use ls -l to see the user permission and their u see the owner details

Q2:What would the command be to switch to the user "user2"?
A: su user2

Q4:Output the contents of "important", what is the flag?
A: THM{SU_USER2}

Task 6:  Common Directories

/etc: this root directory is use to store system file that are used by the operating system

/var: this root directory is use to store the variable data like log files or other data that is not necessarily associated with a specific user(i.e,databases and the like.)

/root: this root directry is the home directry of the root user like /home directry of common users

/tmp: this root directry is used to store the temporary data that are deleted when system is power off this directory use can write thing here by default.

Q2:What is the directory path that would we expect logs to be stored in?
A: /var/log

Q3:What root directory is similar to how RAM on a computer works?
A: /tmp

Q4:Name the home directory of the root user 
A: /root

Task 7:  Conclusions and Summaries

Task 8:  Linux Fundamentals Part 3


                                               TryHackme Linux Fundamentals Part 3
                                               ===================================


Task 1:  Introduction

Task 2:  Deploy Your Linux Machine

Task 3:  Terminal Text Editors


Utility                       Discription

nano                  this is most easy and comman text editor use in linux

vim                   this is a little hard but powerfull text editor in linux 


Q2: Edit "task3" located in "tryhackme"'s home directory using Nano. What is the flag?
A: THM{TEXT_EDITORS}

Task 4:  General/Useful Utilities

Utility             Discription

wget            this utility is use for download files and transfer files 

scp             this utility is use for transfer the file using ssh if you have valid credential so you can copy files with this .scp stand for secure copy

python http.server     this utility enable a mini web server on the computer that you can use to transfer files 

Q3:What are the contents?
A:THM{WGET_WEBSERVER}


Task 5: Processes 101
   
Command                 Discription

ps                   this command is use to see the running processes of that user
# if you want to see the process of other users so we need to give aux to ps like this 
$ ps aux

top                  this command is use to see the real time statistics about the processess running on the system

kill                 this command is use to terminate the process

systemctl            this command is use to manage processes in systemd

Q2:If we were to launch a process where the previous ID was "300", what would the ID of this new process be?
A: 301

Q3:If we wanted to cleanly kill a process, what signal would we send it?
A: sigterm

Q4:Locate the process that is running on the deployed instance (10.10.194.93). What flag is given?
A: THM{PROCESSES}
# use ps aux to find this

Q5: What command would we use to stop the service "myservice"?
A: systemctl stop myservice

Q6: What command would we use to start the same service on the boot-up of the system?
A: systemctl enable myservice

Q7:What command would we use to bring a previously backgrounded process back to the foreground?
A: fg

Task 6: Maintaining Your System: Automation

Q2: How frequently will the crontab on the deployed instance run?
A: @reboot

Task 7: Maintaining Your System: Package Management

Task 8: Maintaining Your System: Logs

Q2:What is the IP address of the user who visited the site?
A: 10.9.232.111
# go to /var/log/apache2 directory there you find access.log.1
Q3:What file did they access?
A: /catsanddogs.jpg
