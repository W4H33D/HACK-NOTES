# [HTB Academy](https://academy.hackthebox.eu/)

Module: [Setting Up](https://academy.hackthebox.eu/module/details/87)

##### Table of Contents

###### Introduction

1. Introduction

2. Organization

3. Virtualization

4. Containers

###### Operating Systems

1. Linux

2. Windows

###### Virtual Private Server

1. VPS Providers

2. VPS Setup

3. VPS Hardening


## Introduction

Our company was commissioned by a new customer (Inlanefreight) to perform an external and internal penetration test. As already mentioned, proper Operating System preparation is required before conducting any penetration test. Our customer provides us with internal systems that we should prepare before the engagement so that the penetration testing activities commence without delays. For this, we have to prepare the necessary operating systems accordingly and efficiently.

### Penetration Testing Stages & Situations

Every penetration test is different in terms of scope, expected results, and environment, depending on the customer's service line and infrastructure. Apart from the different penetration testing stages we usually go through; our activities can vary depending on the type of penetration test, which can either extend or limit our working environment and capabilities.

For example, if we are performing an internal penetration test, in most cases, we are provided with an internal host from which we can work. Suppose this host has internet access (which is usually the case). In that case, we need a corresponding Virtual Private Server (VPS) with our tools to access and download the related penetration testing resources quickly.

Testing may be performed remotely or on-site, depending on the client's preference. If remote, we will typically ship them a device with our penetration testing distro of choice pre-installed or provide them with a custom VM that will call back to our infrastructure via OpenVPN. The client will elect to either host an image (that we must log into and customize a bit on day one) and give us SSH access via IP whitelisting or VPN access directly into their network. Some clients will prefer not to host any image and provide VPN access, in which case we are free to test from our own local Linux and Windows VMs.

When traveling on-site to a client, it is essential to have both a customized and fully up-to-date Linux and Windows VM. Certain tools work best (or only) on Linux, and having a Windows VM makes specific tasks (such as enumerating Active Directory) much easier and more efficient. Regardless of the setup chosen, we must guide our clients on the pros and cons and help guide them towards the best possible solution based on their network and requirements.

This is yet another area of penetration testing in which we must be versatile and adaptable as subject matter experts. We must make sure we are fully prepared on day 1 of the assessment with the proper tools to provide the client with the best possible value and in-depth assessment. Every environment is different, and we never know what we will encounter once we start enumerating the network and uncovering issues. We have to compile/install tools or download specific scripts to our attack VM during almost every assessment we perform. Having our tools set up in the best way possible will ensure that we don't waste time in the early days of the assessment but instead only have to make changes to our assessment VMs for specific scenarios we encounter during the assessment.

### Setup & Efficiency

Over time, we all gather different experiences and collections of tools that we are most familiar with. Being structured is of paramount importance, as it increases our efficiency in penetration testing. Searching for individual resources and even needing additional tools to make these resources work by the time an engagement starts can be eliminated by having access to a prebaked, organized, and structured environment. Doing so requires some preparation and knowledge of different operating systems.

## Organization

As we have already seen in the `Learning Process` module, organization plays a significant role in our penetration tests. It does not matter what type of penetration test it is. Having a working environment that we can navigate almost blindly saves a tremendous amount of time researching resources that we are already familiar with and have invested our time learning. These sources can be found within a few minutes, but once we have an extensive list of resources required for each assessment and include installation, this results in a few hours of pure preparation.

Corporate environments usually consist of heterogeneous networks (hosts/servers having different Operating Systems). Therefore, it makes sense to organize hosts and servers based on their Operating System. If we organize our structure according to penetration testing stages and the targets’ Operating System, then a sample folder structure could look as follows.

```shell-session
W4H33D@htb[/htb]$ tree .

.
└── Penetration-Testing
	│
	├── Pre-Engagement
	│       └── ...
    ├── Linux
    │   ├── Information-Gathering
    │   │   └── ...
    │   ├── Vulnerability-Assessment
    │   │   └── ...
    │   ├── Exploitation
    │   │   └── ...
    │   ├── Post-Exploitation
    │   │   └── ...
    │   └── Lateral-Movement
    │       └── ...
    ├── Windows
    │   ├── Information-Gathering
    │   │   └── ...
    │   ├── Vulnerability-Assessment
    │   │   └── ...
    │   ├── Exploitation
    │   │   └── ...
    │   ├── Post-Exploitation
    │   │   └── ...
    │   └── Lateral-Movement
    │       └── ...
    ├── Reporting
    │   └── ...
	└── Results
	    └── ...
```

If we are specialized in specific penetration testing fields, we can, of course, reorganize the structure according to these fields. We are all free to develop a system with which we are familiar, and in fact, it is recommended that we do so. Everyone works differently and has their strengths and weaknesses. If we work in a team, we should develop a structure that each team member is familiar with. Take this example as a starting point for creating your system.

```shell-session
W4H33D@htb[/htb]$ tree .

.
└── Penetration-Testing
	│
	├── Pre-Engagement
	│       └── ...
    ├── Network-Pentesting
	│       ├── Linux
	│       │   ├── Information-Gathering
	│		│   │   └── ...
	│       │   ├── Vulnerability-Assessment
    │       │   │	└── ...
    │       │	└── ...
    │       │    	└── ...
    │		├── Windows
    │ 		│   ├── Information-Gathering
    │		│   │   └── ...
    │		│   └── ...
    │       └── ...
    ├── WebApp-Pentesting
	│       └── ...
    ├── Social-Engineering
	│       └── ...
    ├── .......
	│       └── ...
    ├── Reporting
    │   └── ...
	└── Results
	    └── ...
```

Proper organization helps us in both keeping track of everything and finding errors in our processes. During our studies here, we will come across many different fields that we can use to expand and enhance our understanding of the cybersecurity domain. Not only can we save the cheatsheets or scripts provided within Modules in Academy, but we can also keep notes regarding all phases of a penetration test we will come across in HTB Academy to ensure that no critical steps are missed in future engagements. We recommend starting with small structures, especially when entering the penetration testing field. Organizing based on the Operating System is, therefore, more suitable for newcomers.

While organizing things for ourselves or an entire team, we should make sure that we all work according to a specific procedure. Everyone should know how where they fit in and where each member fits in throughout the entire penetration testing process. There should also be a common understanding regarding the activities of each member. Otherwise, things may end up in the wrong subdirectory, or evidence necessary for reporting could be lost or corrupted.

### Bookmarks

Numerous browser add-ons exist that can significantly enhance both our penetration testing activities and efficiency. Having to reinstall them over and over again takes time and slows us down unnecessarily. Thankfully Firefox offers add-on and bookmark synchronization after creating and using a Firefox account. All add-ons installed using this account are automatically installed and synchronized when we log in again. The same applies to any saved bookmarks. Therefore, logging in with a Firefox account will be enough to transfer everything from a prefabricated environment to a new one.

We should be cautious not to store any resources containing potentially sensitive information or private resources. We should always keep in mind that third parties could view these stored resources. Therefore, customer-related bookmarks should never be saved. A list of bookmarks should always be created with a single principle:

**`This list will be seen by third parties sooner or later.`**

For this reason, we should create an account for penetration testing purposes only. If our bookmark list must be edited and extended, then the safest route is to store the list locally and import it to the pentesting account. Once we have done this, we should change our private one (the non-pentesting one).

### Password Manager

One other essential component for us is password managers. Password managers can prove helpful not only for personal purposes but also for penetration tests. One of the most common vulnerabilities or attack methods within a network is "password reuse". We try to use found or decrypted passwords and usernames to log in to multiple systems and services within the company network through this attack. It is still quite common to come across credentials that can be used to access several services or servers, making our work easier and offering us more opportunities for attack. There are only three problems with passwords:

1.  `Complexity`
2.  `Re-usage`
3.  `Remembering`

#### 1. Complexity

The first problem with passwords is complexity and remembering it. It is already a challenge for standard users to create a complex password, as they are often associated with content that users know and can remember. NordPass has created a list of the most commonly used passwords that we can view [here](https://nordpass.com/most-common-passwords-list/). We can see here that these passwords can be guessed within seconds without any special preparations, such as password mutation.

#### 2. Re-usage

Once the user has created and memorized a complex password, only two problems remain. Remembering a complex and hard-to-guess password is still within the realm of possibility for a standard user. The second problem is that this password is then used for all services, which allows us to work across several components of the company's infrastructure. To prevent this, the user would have to create and remember complex passwords `for all` services used.

#### 3. Remembering

This brings us to the third problem with passwords. If a standard user uses several dozen services, human nature forces them to become lazy. Very few will make an effort actually to `remember` all passwords. If a user creates multiple passwords, the risk of forgetting them or mixing them up with other password components is high if a secure password-keeping solution is not used.

`Password Managers` solve all problems mentioned above not only for standard users but also for penetration testers. We work with dozens, if not hundreds, of different services and servers for which we need a new, strong, and complex password every time. Some providers include the following, but are not limited to:

**[1Password](https://1password.com/)**

**[LastPass](https://www.lastpass.com/)**

**[Keeper](https://www.keepersecurity.com/)**

**[Bitwarden](https://bitwarden.com/)**

Another advantage to this is that we only have to remember one password to access all of our other passwords. One of the most recommended is `1Password` password manager, which offers `1GB` (personal) or `5GB`/person (teams) of storage and a [2FA function](https://support.1password.com/two-factor-authentication/) for entire teams. This storage space can be used to store important notes and results collected during the penetration test. Furthermore, a security key can be installed on a physical device that can serve as an additional 2FA. All included features can be found [here](https://1password.com/teams/pricing/).

Let us assume that we need to perform an internal penetration test.

1.  Before we go to the company, we regenerate a new `Secret Key` and `Master Password` and make sure that the `2FA function is enabled`.
    
2.  Once we are at the company's internal host, we can install the `1Password add-on` for Firefox and log in with the newly generated keys (which we can view from our smartphone or laptop). Even if this host is compromised and a keylogger runs on it, our vault cannot be used by third parties without our 2FA code.
    
3.  Once we are logged in, we can log in with the `Firefox account` credentials stored in 1Password, and all our add-ons and bookmarks will be available in a few seconds.

### Updates & Automation

We should continually update the components we have organized before starting a new penetration test. This applies to the operating system we use and all the Github collections we will collect and use over time. It is highly recommended to record all the resources and their sources in a file to more easily automate them later. Any automation scripts can also be saved in 1Password to download them directly when needed.

When we create automated scripts, they are operating system-dependent. For example, we can work with Bash, Python, and PowerShell. Creating automation scripts is a good exercise for learning and practicing scripting and can also help us prepare and even reinstall a system more efficiently. We will find more tools, practical explanations, and cheat sheets when learning new methods and technologies. It is recommended that we keep those in a record and keep the entries up to date.

### Note Taking

Note-taking is another essential part of our penetration testing because we accumulate a lot of different information, results, and ideas that are difficult to remember all at once. There are five different main types of information that need to be noted down:

1.  Newly discovered information
    
2.  Ideas for further tests and processing
    
3.  Scan results
    
4.  Logging
    
5.  Screenshots

#### 1. Discovered Information

By discovered information we mean, general information, such as new IP addresses, usernames, passwords, source code, etc., that we identified and are related to the penetration testing engagement and process. This is information that we can use against our target company. We often obtain such information through OSINT, active scans, and manual analysis of the given information resources and services.

#### 2. Processing

We will receive a lot of different information during our penetration testing that will require us to adapt our approach. These results may give us ideas for subsequent steps we can take, and other vulnerabilities or misconfigurations may be forgotten or overlooked. Therefore, we should get in the habit of noting down everything we see that should be investigated as part of the assessment. Notion.so, Obsidian and Xmind are very suitable for this.

[Notion.so](https://notion.so/) is a fancy online markdown editor that offers many different functions and gives us the ability to shape our ideas and thoughts according to our preferences.

![](https://academy.hackthebox.eu/storage/modules/87/notion.png)

[Xmind](https://www.xmind.net/) is an excellent mind map editor that can visualize relevant information components and processes very well.

![](https://academy.hackthebox.eu/storage/modules/87/xmind.png)

[Obsidian](https://obsidian.md/) is a powerful knowledge base that works on top of a local folder of plain text Markdown files.

![](https://academy.hackthebox.eu/storage/modules/87/obsidian.png)

#### 3. Results

The results we get after our scans and penetration testing steps are significant. With such a large amount of information in a short time, one can quickly feel overwhelmed. It is not easy at first to filter out the most critical pieces of information. This is something that will come with experience and practice. Only through practice, our eyes can be trained to recognize the essential small fragments of information. Nevertheless, we should keep all information and results not to miss something meaningful and because a piece of information may prove helpful later in the engagement. Besides, these results are also often used for documentation. For this, we can use [GhostWriter](https://github.com/GhostManager/Ghostwriter) or [Pwndoc](https://github.com/pwndoc/pwndoc). These allow us to generate our documentation and have a clear overview of the steps we have taken.

#### GhostWriter

![](https://academy.hackthebox.eu/storage/modules/87/ghostwriter.png)

#### Pwndoc

![](https://academy.hackthebox.eu/storage/modules/87/pwndoc.png)

#### 4. Logging

Logging is essential for both documentation and our protection. If third parties attack the company during our penetration test and damage occurs, we can prove that the damage did not result from our activities. For this, we can use the tools `script` and `date`. `Date` can be used to display the exact date and time of each command in our command line. With the help of `script`, every command and the subsequent result is saved in a background file. To display the date and time, we can replace the `PS1` variable in our `.bashrc` file with the following content.

#### PS1

Code: bash

```bash
PS1="\[\033[1;32m\]\342\224\200\$([[ \$(/opt/vpnbash.sh) == *\"10.\"* ]] && echo \"[\[\033[1;34m\]\$(/opt/vpnserver.sh)\[\033[1;32m\]]\342\224\200[\[\033[1;37m\]\$(/opt/vpnbash.sh)\[\033[1;32m\]]\342\224\200\")[\[\033[1;37m\]\u\[\033[01;32m\]@\[\033[01;34m\]\h\[\033[1;32m\]]\342\224\200[\[\033[1;37m\]\w\[\033[1;32m\]]\n\[\033[1;32m\]\342\224\224\342\224\200\342\224\200\342\225\274 [\[\e[01;33m\]$(date +%D-%r)\[\e[01;32m\]]\\$ \[\e[0m\]"
```

#### Date

  Date

```shell-session
─[eu-academy-1]─[10.10.14.2]─[Cry0l1t3@htb]─[~]
└──╼ [03/21/21-01:45:04 PM]$
```

To start logging with `script` (for Linux) and [Start-Transcript](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.host/start-transcript?view=powershell-7.1) (for Windows), we can use the following command and rename it according to our needs. It is recommended to define a certain format in advance after saving the individual logs. One option is using the format `<date>-<start time>-<name>.log`.

#### Script

  Script

```shell-session
W4H33D@htb[/htb]$ script 03-21-2021-0200pm-exploitation.log

W4H33D@htb[/htb]$ ...SNIP...
W4H33D@htb[/htb]$ exit
```

#### Start-Transcript

  Start-Transcript

```powershell-session
C:\> Start-Transcript -Path "C:\Pentesting\03-21-2021-0200pm-exploitation.log"

Transcript started, output file is C:\Pentesting\03-21-2021-0200pm-exploitation.log

C:\> ...SNIP...
C:\> Stop-Transcript
```

This will automatically sort our logs in the correct order, and we will no longer have to examine them manually. This also makes it more straightforward for our team members to understand what steps have been taken and when.

Another significant advantage is that we can later analyze our approach to optimize our process. If we repeat one or two steps repeatedly and use them in combination, it may be worthwhile to examine these steps with the help of a simple script to save time.

In addition, most tools offer the possibility to save the results in separate files. It is highly recommended always to use these functions because the results can also change. Therefore, if specific results seem to have changed, we can compare the current results with the previous ones. There are also terminal emulators, such as [Tmux](https://github.com/tmux/tmux/wiki) and [Terminator](https://terminator-gtk3.readthedocs.io/en/latest/), which allow, among other things, to log all commands and output automatically. If we come across a tool that does not allow us to log the output, we can work with redirections and the program tee. This would look like this:

#### Linux Output Redirection

  Linux Output Redirection

```shell-session
W4H33D@htb[/htb]$ ./custom-tool.py 10.129.28.119 > logs.custom-tool
```

  Linux Output Redirection

```shell-session
W4H33D@htb[/htb]$ ./custom-tool.py 10.129.28.119 | tee -a logs.custom-tool
```

#### Windows Output Redirection

  Windows Output Redirection

```powershell-session
C:\> .\custom-tool.ps1 10.129.28.119 > logs.custom-tool
```

  Windows Output Redirection

```powershell-session
C:\> .\custom-tool.ps1 10.129.28.119 | Out-File -Append logs.custom-tool
```

#### 5. Screenshots

Screenshots serve as a momentary record and represent proof of results obtained, necessary for the Proof-Of-Concept and our documentation. One of the best tools for this is [Flameshot](https://github.com/flameshot-org/flameshot). It has all the essential functions that we need to quickly edit our screenshots without using an additional editing program. We can install it using our APT package manager or via download from Github.

#### Flameshot

![](https://raw.githubusercontent.com/flameshot-org/flameshot-org.github.io/master/docs/media/animatedUsage.gif)

Sometimes, however, we cannot show all the necessary steps in one or more screenshots. We can use an application called [Peek](https://github.com/phw/peek) and create GIFs that record all the required actions for us.

#### Peek

![](https://raw.githubusercontent.com/phw/peek/master/data/screenshots/peek-recording-itself.gif)


## Virtualization

`Virtualization` is an abstraction of physical computing resources. Both hardware and software components can be abstracted. A computer component created as part of virtualization is referred to as a virtual or logical component and can be used precisely as its physical counterpart. The main advantage of virtualization is the abstraction layer between the physical resource and the virtual image. This is the basis of various cloud services, which are becoming increasingly important in everyday business. Virtualization must be distinguished from the concepts of simulation and emulation.

Virtualization involves the abstraction of physical computing resources such as hardware, software, storage, and network components. The aim is to make these resources available on a virtual level and to distribute them to different customers in a manner that is as flexible as it is demand-driven. This should ensure improved utilization of computer resources. The aim is to run applications on a system that is not supported by it. In virtualization, we distinguish between:

-   Hardware virtualization
    
-   Application virtualization
    
-   Storage virtualization
    
-   Data virtualization
    
-   Network virtualization
    

Hardware virtualization is about technologies that enable hardware components to be made available independently of their physical basis using [hypervisor](https://en.wikipedia.org/wiki/Hypervisor) software. The best-known example of this is the `virtual machine (VM)`. A VM is a virtual computer that behaves like a physical computer, including hardware and operating system. Virtual machines run as virtual guest systems on one or more physical systems referred to as `hosts`.

![](https://www.tech-hounds.com/wp-content/uploads/2020/12/0115_hardware_virtualization_stack_showing_layers_ppt_slide_Slide01.jpg)

## Virtual Machines

A `virtual machine (VM)` is a virtual operating system that runs on a host system (an actual physical computer system). Several VMs isolated from each other can be operated in parallel. The physical hardware resources of the host system are allocated via `hypervisors`. This is a sealed-off, virtualized environment with which several guest systems can be operated, independent of the operating system, in parallel, on one physical computer. The VMs act independently of each other and do not influence each other. A hypervisor manages the hardware resources, and from the virtual machine's point of view, allocated computing power, RAM, hard disk capacity, and network connections are exclusively available.

From the application perspective, an operating system installed within the VM behaves as if installed directly on the hardware. It is not apparent to the applications or the operating system that they are running in a virtual environment. Virtualization is usually associated with performance losses for the VM because the intermediate virtualization layer itself requires resources. VMs offer numerous advantages over running an operating system or application directly on a physical system. The most important benefits are:

1. Applications and services of a VM do not interfere with each other

2. Complete independence of the guest system from the host system's operating system and the underlying physical hardware

3. VMs can be moved or cloned to other systems by simple copying

4. Hardware resources can be dynamically allocated via the hypervisor

5. Better and more efficient utilization of existing hardware resources

6. Shorter provisioning times for systems and applications

7. Simplified management of virtual systems

8. Higher availability of VMs due to independence from physical resources

## Introduction to VMware

VMware produces software products for the virtualization of computer systems. `VMware Workstation Pro` and `VMware Workstation Player` are the most interesting. The difference between the two is that, unlike Workstation Pro, Workstation Player can only run one VM at a time.

![](https://blogs.vmware.com/workstation/files/2020/09/WS-Blog-Header.png) Resource: [https://blogs.vmware.com/workstation/files/2020/09/WS-Blog-Header.png](https://blogs.vmware.com/workstation/files/2020/09/WS-Blog-Header.png)

For both operating systems, Windows and Linux, the installation files can be downloaded [here](https://www.vmware.com/products/workstation-player/workstation-player-evaluation.html). Since most people are more familiar with Windows, we will install it on a Windows operating system.

 

![](https://academy.hackthebox.eu/storage/modules/87/vmware.png)

#### VMware Installation on Windows

The installation itself is done by downloading the installation file and running it. There we are asked for some additional features that we can install if necessary. We should read through these features and then decide if we want to use them. After completing the installation, the operating system must be restarted.

![](https://academy.hackthebox.eu/storage/modules/87/vmware_install0.png)

After the installation is complete and we restart the operating system, we will be able to see an icon on our desktop, and when running, `VMware Workstation Player` will look like this:

![](https://academy.hackthebox.eu/storage/modules/87/vmware_installed.png)

Here we now have the option to create a new VM or to include an existing one. Existing VMs from VMware are deployed in `Open Virtualization Format` (`OVF`). OVF is an open standard to package and distribute virtual appliances or, more generally, software running in virtual machines. The `OVF` standard is not limited to specific hypervisors or processor architectures and can be used by many different platforms like VMware, VirtualBox, XenServer, Oracle VM, and others. The entity involved in packaging and distribution is called an OVF Package containing one or more virtual systems, including a virtual machine. More about this format can be found [here](https://en.wikipedia.org/wiki/Open_Virtualization_Format).

#### VMware Installation on Linux

The installation of VMware Workstation Player on a Linux-based operating system looks a little different. However, we need to download the installation file first. Once we have downloaded it, we can run the installation with the following commands:

  VMware Installation on Linux

```shell-session
username@Linux:~$ sudo apt install build-essential -y
username@Linux:~$ sudo bash ~/Downloads/VMware*.bundle

[sudo] password for user: **************

Extracting VMware Installer...done.
Installing VMware Installer 3.0.0
Copying files...
...SNIP...
Configuring...
Installation was successful.
```

![](https://academy.hackthebox.eu/storage/modules/87/vmware_installed_linux.png)

![](https://academy.hackthebox.eu/storage/modules/87/vmware_install0_linux.png)

## Introduction to VirtualBox

An excellent and free alternative to VMware Workstation is [VirtualBox](https://www.virtualbox.org/). With VirtualBox, hard disks are emulated in container files, called Virtual Disk Images (`VDI`). Aside from VDI format, VirtualBox can also handle hard disk files from VMware virtualization products (`.vmdk`), the `Virtual Hard Disk` format (`.vhd`), and others. We can also convert these external formats using the VBoxManager command-line tool that is part of VirtualBox. We can install VirtualBox from the command line or download the installation file from the [official website](https://www.virtualbox.org/wiki/Downloads) and install it manually.

#### VirtualBox Installation

  VirtualBox Installation

```shell-session
W4H33D@htb[/htb]$ sudo apt install virtualbox virtualbox-ext-pack -y
```

![](https://academy.hackthebox.eu/storage/modules/87/vbox.png)

From the example of a created VM shown above, we can see what configuration options are available for the VDI within VirtualBox. Also, we have the possibility and function to `encrypt` the VM, which we should always use. We will use this option as soon as we have prepared our VM accordingly and ready for use.

1.  Containers

## Containers

A `container` cannot be defined as a virtual machine but as an isolated group of `processes` running on a single host that corresponds to a complete application, including its configuration and dependencies. This application is packaged in a precisely defined and reusable format. Unlike a usual VM on VMware Workstation, however, a container does not contain its operating system or kernel. It is, therefore, not a virtualized operating system. For this reason, containers are significantly slimmer than conventional virtual machines. Precisely because they are not real virtual machines, they are also referred to as application virtualization in this context.

![](https://wiki.aquasec.com/download/attachments/2854029/docker-birthday-3-intro-to-docker-slides-18-638.jpg?version=1&modificationDate=1515522843003&api=v2) Resource: [https://wiki.aquasec.com/download/attachments/2854029/docker-birthday-3-intro-to-docker-slides-18-638.jpg?version=1&modificationDate=1515522843003&api=v2](https://wiki.aquasec.com/download/attachments/2854029/docker-birthday-3-intro-to-docker-slides-18-638.jpg?version=1&modificationDate=1515522843003&api=v2)

A significant issue when rolling out new applications or new releases is that each application depends on some aspects of its environment. These include, for example, local settings or function libraries. Often, the settings in the development environment differ from those in the test environment and production. It can then quickly happen that, contrary to expectations, an application works differently or not at all in production.

**Virtual Machine**

   1. Contain applications and the complete operating system
   2. A hypervisor such as VMware ESXi provides virtualization
   3. Multiple VMs run in isolation from each other on a physical server

**`Container`**
 
 `1. Contain applications and only the necessary operating system components such as libraries and binaries`
 `2. The operating system with the container engine provides its own virtualization`
 `3. Several containers run isolated from each other on one operating system`
 
 Application containers are technically based on functions that have been available under the Linux operating system for some time. The kernel uses these functions to `isolate` applications. Thus, applications run isolated from each other as a process in different user accounts. However, they belong at the same time to a familiar Linux environment. The cooperation of various applications is also possible, and if the containers run on the same system, a container daemon is used, for example, the `Linux Container Daemon` (`LXD`). LXD is a similar technology to `Linux Containers` (`LXC`). LXC is a container-based virtualization technology at the operating system level. Technically, LXC combines isolated namespaces and the Linux kernel "cgroups" to implement isolated environments for code execution. Historically, LXC was also the basis for the widely used Docker virtualization technology. Using LXD, Linux operating system containers can be configured and controlled via a defined set of commands. It is therefore suitable for automating mass container management and is used in cloud computing and data centers.

An image of the file system forms the basis of each container. We can choose whether to use an image that has already been created or to create one ourselves. Containers are also characterized by outstanding scalability. Improved scalability is ideally suited to the requirements of the now highly dynamic IT infrastructure in companies. Indeed, the high scalability of containers makes it possible to ideally adapt the capacities for the users' provision of applications. Meanwhile, even large container setups can be managed without any problems because of orchestration systems such as `Apache Mesos` or `Google Kubernetes`. These systems distribute the containers over the existing hardware based on predefined rules and monitor them.

## Introduction to Docker

[Docker](https://www.docker.com/get-started) is open-source software that can isolate applications in containers, similar to operating system virtualization. This approach significantly simplifies the deployment of applications. The application data stored in the containers can be transported and installed easily. The use of containers ensures that computer resources are strictly separated from each other. Docker stores programs together with their dependencies in images. These form the basis for virtualized containers that can run on almost any operating system. This makes applications portable and uncomplicated, whether during development or when scaling [SaaS](https://en.wikipedia.org/wiki/Software_as_a_service) clusters.

[Docker Engine](https://docs.docker.com/engine/) is the main component of container virtualization. The software provides the interface between host resources and running containers. Any system that has Docker Engine installed can use Docker containers. Originally, Docker was designed to be used on Linux systems. However, with virtualization via VMware or Hyper-V, the engine also works on Windows or Mac OS devices. Docker can therefore be used in virtually all common scenarios.

#### Docker Installation

  Docker Installation

```shell-session
W4H33D@htb[/htb]$ sudo apt update -y 
W4H33D@htb[/htb]$ sudo apt install docker.io -y
```

 #### Docker Installation

```powershell-session
C:\> IEX((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))
C:\> choco upgrade chocolatey
C:\> choco install docker-desktop
```
 
 ## Introduction to Vagrant

[Vagrant](https://www.vagrantup.com/) is a tool that can create, configure and manage virtual machines or virtual machine environments. The VMs are not created and configured manually but are described in code in a `Vagrantfile`. To better structure the program code, the Vagrant file can include additional code files. The code can then be processed using the Vagrant CLI. In this way, we can create, provision, and start our own VMs. Moreover, if the VMs are no longer needed, they can be destroyed just as quickly and easily. Out of the box, Vagrant offers providers for VMware and Docker.

![](https://stefanscherer.github.io/content/images/2016/03/windows_swarm_demo.png) Resource: [https://stefanscherer.github.io/content/images/2016/03/windows_swarm_demo.png](https://stefanscherer.github.io/content/images/2016/03/windows_swarm_demo.png)

#### Vagrant Installation

  Vagrant Installation

```shell-session
W4H33D@htb[/htb]$ sudo apt update -y 
W4H33D@htb[/htb]$ sudo apt install virtualbox virtualbox-dkms vagrant
```

  Vagrant Installation

```powershell-session
C:\> IEX((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))
C:\> choco upgrade chocolatey
C:\> cinst virtualbox cyg-get vagrant
```


It is highly recommended to play around with different containers and experiment to get a feel for them. We should look at the documentation and read through it to understand how the containers work and what they depend on. It will also help us understand what advantages and disadvantages they bring.

1.  Linux

# Linux

---

Linux is the most widely used Operating System for penetration testing purposes. Therefore, we must be very familiar with it. If we have to prepare an operating system, it is best to develop a certain standard for it that always leads to the same setup we are used to.

Suppose we are asked to perform a penetration test to test both the network's internal and external security. Therefore, if we have not prepared a system in advance, we should do it now at the latest. Otherwise, we will lose valuable time during the penetration test that we could have used to test the different components of the network instead of configuring and installing various tools. However, before we prepare a system, we need to look at the available penetration testing distributions.

---

## Penetration Testing Distributions

There are many different distributions that we can use, all of which have different advantages and disadvantages. Many people ask themselves which system is the best. Nevertheless, what many do not understand is that it is a personal decision. This means that it depends a lot on our own needs and desires, which is the best for us. The tools installed on these operating systems can be installed on pretty much any distribution, so we should instead ask ourselves what we should expect from this Linux distribution for penetration testing. The best penetration testing distributions are characterized by their large and active community and comprehensible and detailed documentation. Among the most popular include, but not limited to:

[ParrotOS](https://www.parrotsec.org/) (`Pwnbox`)

[Kali Linux](https://kali.org/)

[BlackArch](https://blackarch.org/)

[BackBox](https://linux.backbox.org/)

In this case, we will deal with [ParrotOS Security](https://www.parrotsec.org/download/) as our penetration testing distribution of choice.

---

## VM Setup

Before installing our ParrotOS Security operating system, we need to `create a VM` (in VMware Workstation in this example). Here we also specify which installation file will be used for the operating system (`.iso` file).

#### ParrotOS ISO

![](https://academy.hackthebox.eu/storage/modules/87/linux_vmware1.png)

Since VMware does not know all operating systems, it may be that the system will not be recognized. Therefore, we have to specify which distribution the system we want to install is based upon in the next step. In the case of ParrotOS, this is a `Debian`-based operating system.

#### OS Base

![](https://academy.hackthebox.eu/storage/modules/87/linux_vmware2.png)

After that, we assign a `name` for the VM with the label we want to have for it and then set the `path` where it will be stored.

#### OS Name & Location Path

![](https://academy.hackthebox.eu/storage/modules/87/linux_vmware3.png)

After that, we can set the `maximum size` of the VM. Here it is recommended to set the size larger than 20 GB because the VM will not be equal to the size we set but will grow with the packages and applications we install during setup and regular usage. It is also recommended to save the VM in a `single file` and not split it into several to make moving the VM easier in the future if necessary.

#### OS Size

![](https://academy.hackthebox.eu/storage/modules/87/linux_vmware4.png)

---

## ParrotOS Installation

Once we have created our VM, we can start it and get to the GRUB menu to select our options. Since we want to `install ParrotOS`, we should select this option. Once we click on the VM window, our mouse will be trapped there. This means that our mouse cannot leave this window. To move the mouse freely again, we have to press `[CTRL] + [ALT]` (in most cases). However, we should verify this key combination in the VMware Workstation window under `Edit > Preferences > Hot Keys`.

Note: We can design all the steps according to our needs. However, we should stick to the given selection for uniformity in the steps shown to get the same result.

![](https://academy.hackthebox.eu/storage/modules/87/linux_vmware_install1.png)

Once the installation procedure starts, we are asked how we want to partition it. Since we want to encrypt our data and information on the VM using [Logical Volume Manager](https://en.wikipedia.org/wiki/Logical_volume_management) (`LVM`), we should select the penultimate option "`Guided - use entire disk and set up encrypted LVM`".

![](https://academy.hackthebox.eu/storage/modules/87/linux_vmware_install2.png)

`LVM` is a partitioning scheme mainly used in Unix and Linux environments, which provides a level of abstraction between disks, partitions, and file systems. Using LVM, it is possible to form dynamically changeable partitions, which can also extend over several disks. In this case, we will install `all files in one partition`.

![](https://academy.hackthebox.eu/storage/modules/87/linux_vmware_install3.png)

Once we have made all our required entries, we can confirm them and start configuring `LVM`.

![](https://academy.hackthebox.eu/storage/modules/87/linux_vmware_install4.png)

---

## LUKS Encryption

`LVM` is an additional abstraction layer between physical data storage and the computer's operating system with its logical data storage area and the file system. LVM supports the organization of logical volumes in a RAID array to protect computers from individual hard disk failure. Unlike RAID, however, the LVM concept does not provide redundancy. It has been present in almost all Unix and Linux distributions but also for other operating systems. Windows or macOS also have the concept of LVM but use different names for it like [Storage Spaces](https://docs.microsoft.com/en-us/windows-server/storage/storage-spaces/overview) (Windows) or [CoreStorage](https://en.wikipedia.org/wiki/Core_Storage) (macOS).

Once we get to the partition disk step, we will be asked for an `encryption passphrase` for encryption and decryption. We should keep in mind that this passphrase should be very strong, and we should use a password manager of our choice to store it. We will then have to enter this passphrase again to make sure that we did not make a mistake when we first entered it.

#### LVM Passphrase

![](https://academy.hackthebox.eu/storage/modules/87/linux_vmware_install5.png)

After selecting the passphrase and confirming it, we will get an overview of all the partitions that have been created and configured. We will have other options available to us, as listed above. If we do not want to make any further configurations, we can now `finish partitioning` and let the changes be written accordingly.

#### LVM Finish Partitioning

![](https://academy.hackthebox.eu/storage/modules/87/linux_vmware_install6.png)

Now the operating system's installation takes place, and as soon as this is finished, the VM is restarted. After the restart, we get a window that asks us for our passphrase to unlock the system.

#### LVM Unlock Partition

![](https://academy.hackthebox.eu/storage/modules/87/linux_vmware_installed1.png)

If we have entered the passphrase correctly, then the operating system will boot up completely, and we will be able to log in. Here we enter the password for the username we have created.

#### First Login

![](https://academy.hackthebox.eu/storage/modules/87/linux_vmware_installed2.png)

---

## Updates & APT Package Manager

Now that we have installed the operating system, we need to bring it up to date. For this, we will use the `APT` package management tool. The `Advanced Packaging Tool` (`APT`) is a package management system that originated in the Debian operating system that uses `dpkg` for actual package management. The package manager is used for package management. This means that we can search, update, and install program packages. APT uses repositories (thus package sources), which are deposited in the directory `/etc/apt/sources.list` (in our case for ParrotOS: `/etc/apt.sources.list.d/parrot.list`).

#### ParrotOS Sources List

  ParrotOS Sources List

```shell-session
┌─[cry0l1t3@parrot]─[~]
└──╼ $ cat /etc/apt/sources.list.d/parrot.list

# parrot repository
# this file was automatically generated by parrot-mirror-selector
deb https://deb.parrot.sh/parrot/ rolling main contrib non-free
#deb-src https://deb.parrot.sh/parrot/ rolling main contrib non-free
deb https://deb.parrot.sh/parrot/ rolling-security main contrib non-free
#deb-src https://deb.parrot.sh/parrot/ rolling-security main contrib non-free
```

Here the package manager can access a list of HTTP and FTP servers and obtain and install the corresponding packages from there. If packages are searched for, they are automatically loaded from the list of available repositories. Since program versions can be compared quickly under APT and can be loaded automatically from the repositories list, updating existing program packages under APT is relatively easy and comfortable.

#### Updating ParrotOS

  Updating ParrotOS

```shell-session
┌─[cry0l1t3@parrot]─[~]
└──╼ $ sudo apt update -y && sudo apt full-upgrade -y && sudo apt autoremove -y && sudo apt autoclean -y
[sudo] password for cry0l1t3:                                                 

Hit:1 https://deb.parrot.sh/parrot rolling InRelease
Hit:2 https://deb.parrot.sh/parrot rolling-security InRelease
Reading package lists... Done
Building dependency tree
Reading state information... Done
2310 packages can be upgraded. Run 'apt list --upgradable' to see them.       
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Calculating upgrade... Done
The following packages were automatically installed and are no longer required:
  cryptsetup-nuke-password dwarfdump
  ...SNIP...
```

Then we can install the most necessary tools we need for our penetration tests. Here it is recommended to create a list of tools to simplify our automation process (note that many of these come pre-installed in distros such as Parrot).

#### Tools List

  Tools List

```shell-session
┌─[cry0l1t3@parrot]─[~]
└──╼ $ cat tools.list

netcat
ncat
nmap
wireshark
tcpdump
hashcat
ffuf
gobuster
hydra
zaproxy
proxychains
sqlmap
radare2
metasploit-framework
python2.7
python3
spiderfoot
theharvester
remmina
xfreerdp
rdesktop
crackmapexec
exiftool
curl
seclists
testssl.sh
git
vim
tmux
```

If there are only a few packages that we want to install, we can enter them manually in the following command.

#### Installing Additional Tools

  Installing Additional Tools

```shell-session
┌─[cry0l1t3@parrot]─[~]
└──╼ $ sudo apt install netcat ncat nmap wireshark tcpdump ...SNIP... git vim tmux -y
[sudo] password for cry0l1t3:       

Reading package lists... Done
Building dependency tree
Reading state information... Done
The following packages were automatically installed and are no longer required: 
  libarmadillo9 libboost-locale1.71.0 libcfitsio8 libdap25 libgdal27 libgfapi0
  ...SNIP...
```

However, if the list contains more than just five packages, we should always create a list and keep it updated. With the following command, we will install all the tools from the list at once using APT.

#### Installing Additional Tools from a List

  Installing Additional Tools from a List

```shell-session
┌─[cry0l1t3@parrot]─[~]
└──╼ $ sudo apt install $(cat tools.list | tr "\n" " ") -y
[sudo] password for cry0l1t3:       

Reading package lists... Done
Building dependency tree
Reading state information... Done
The following packages were automatically installed and are no longer required: 
  libarmadillo9 libboost-locale1.71.0 libcfitsio8 libdap25 libgdal27 libgfapi0
  ...SNIP...
```

---

## Using Github

We will also come across tools that are not found in the repositories and therefore have to download them manually from Github. For example, we are still missing specific tools for Privilege Escalation and want to download the [Privilege-Escalation-Awesome-Scripts-Suite](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite). We can do that using the following command:

#### Clone Github Repository

  Clone Github Repository

```shell-session
┌─[cry0l1t3@parrot]─[~]
└──╼ $ git clone https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite.git

Cloning into 'privilege-escalation-awesome-scripts-suite'...
remote: Enumerating objects: 29, done.
remote: Counting objects: 100% (29/29), done.
remote: Compressing objects: 100% (17/17), done.
remote: Total 5242 (delta 18), reused 22 (delta 11), pack-reused 5213
Receiving objects: 100% (5242/5242), 18.65 MiB | 5.11 MiB/s, done.
Resolving deltas: 100% (3129/3129), done.
```

#### List Contents

  List Contents

```shell-session
┌─[cry0l1t3@parrot]─[~]
└──╼ $ ls -l privilege-escalation-awesome-scripts-suite/

total 16
-rwxrwxr-x 1 cry0l1t3 cry0l1t3 1069 Mar 23 16:41 LICENSE
drwxrwxr-x 3 cry0l1t3 cry0l1t3 4096 Mar 23 16:41 linPEAS
-rwxrwxr-x 1 cry0l1t3 cry0l1t3 2506 Mar 23 16:41 README.md
drwxrwxr-x 4 cry0l1t3 cry0l1t3 4096 Mar 23 16:41 winPEAS
```

---

## Snapshot

After installing all known and relevant packages and repositories, it is highly recommended to take a `VM snapshot`. In the following steps, we will make changes to specific configuration files. If we are not careful, this can make parts of the system or even the entire system unusable. We do not have to repeat all our previous steps, and we should now create a snapshot and name it "`Initial Setup`".

Nevertheless, `before` we create this snapshot, we should shut down the OS. This will significantly reduce the time required to create the snapshot. Otherwise, the snapshot will be taken from a running system that we will return to when we return to it.

If we break the system in some way while performing subsequent configuration steps we can revert back to a good, known, working copy. A snapshot (powered off) should be taken after every major configuration stage. It is also a good idea to periodically take a VM snapshot during a penetration test in case something goes wrong.

#### Create a Snapshot

![](https://academy.hackthebox.eu/storage/modules/87/vm_snapshot.png)

#### Completed Tasks

![](https://academy.hackthebox.eu/storage/modules/87/vm_snapshot3.png)

---

## Terminal Adjustment

Now that we have created a snapshot and can work with our configurations, let us look at our terminal environment. Many different terminal emulators emulate the actual command line input we use to enter and execute the system's commands. The one that is the best for us depends on many personal desires and expectations. Here is a small list of very popular terminal emulators:

[Terminator](https://terminator-gtk3.readthedocs.io/en/latest/)

[Guake](http://guake-project.org/)

[iTerm2](https://iterm2.com/)

[Terminology](https://www.enlightenment.org/docs/apps/terminology.md)

A very efficient alternative, which can also be used as an extension, is [Tmux](https://github.com/tmux/tmux/wiki). `Tmux` is a terminal multiplexer that allows creating a whole shell session with multiple windows and subwindows from a single shell window. As we know, started processes abort when the terminal session or SSH connection disappears. Tmux's console keeps the process alive by working with sessions. For example, if we are connected to a constantly running server in this way, we can close the terminal or shut down the computer on the local client without terminating the Tmux session. If we log back into the remote server via SSH, we can view the existing sessions and rejoin the desired session.

[Ippsec](https://www.youtube.com/channel/UCa6eh7gCkpPo5XXUDfygQQA) has also created a short video where he introduced Tmux. There he explains some advantages of Tmux and shows with examples how he works with it. We will see his approach in all his videos.

#### Ippsec - Tmux

Another handy component that we should adapt to our needs is the Bash prompt. The [bashrcgenerator](http://bashrcgenerator.com/) makes it very easy for us to design our bash prompt the way we want it to be displayed. For our penetration tests, it is crucial to have the order of the given commands to configure our Bash prompt to display timestamps.

#### Customize Bash Prompt

  Customize Bash Prompt

```shell-session
┌─[cry0l1t3@parrot]─[~]
└──╼ $ cp .bashrc .bashrc.bak
┌─[cry0l1t3@parrot]─[~]
└──╼ $ echo 'export PS1="-[\[$(tput sgr0)\]\[\033[38;5;10m\]\d\[$(tput sgr0)\]-\[$(tput sgr0)\]\[\033[38;5;10m\]\t\[$(tput sgr0)\]]-[\[$(tput sgr0)\]\[\033[38;5;214m\]\u\[$(tput sgr0)\]@\[$(tput sgr0)\]\[\033[38;5;196m\]\h\[$(tput sgr0)\]]-\n-[\[$(tput sgr0)\]\[\033[38;5;33m\]\w\[$(tput sgr0)\]]\\$ \[$(tput sgr0)\]"' >> .bashrc
```

#### Customized Bash Prompt

  Customized Bash Prompt

```shell-session
-[Tue Mar 23-00:39:51]-[cry0l1t3@parrot]-
-[~]$ 
```

Another advantage of this is that we can filter out our commands by the `minus` (`-`) at the beginning later in our logs and thus see a list with only the date, time, and command specified. There are countless variations on how we can also design the look and feel of our Linux distro. Apart from the terminal, we can also customize our desktop manager. There is even a [community](https://www.reddit.com/r/unixporn/) on Reddit which designs the GUIs in many different ways.

---

## Automation

The automation process is also an essential part of our preparation for penetration testing. Especially when it comes to internal penetration tests, where we have internet access and can adapt the workstation we are working on to our needs. This should be fast and efficient. For this, we have to create (in the best case) Bash scripts that automatically adjust our settings to the new system. Let us take the configuration and adjustment of our Bash prompt as an example. An example script can consist of the same commands we already configured.

#### Bash Prompt Customization Script - Prompt.sh

Code: bash

```bash
#!/bin/bash

#### Make a backup of the .bashrc file
cp ~/.bashrc ~/.bashrc.bak

#### Customize bash prompt
echo 'export PS1="-[\[$(tput sgr0)\]\[\033[38;5;10m\]\d\[$(tput sgr0)\]-\[$(tput sgr0)\]\[\033[38;5;10m\]\t\[$(tput sgr0)\]]-[\[$(tput sgr0)\]\[\033[38;5;214m\]\u\[$(tput sgr0)\]@\[$(tput sgr0)\]\[\033[38;5;196m\]\h\[$(tput sgr0)\]]-\n-[\[$(tput sgr0)\]\[\033[38;5;33m\]\w\[$(tput sgr0)\]]\\$ \[$(tput sgr0)\]"' >> ~/.bashrc
```

If we then host this script on our VPS, we can retrieve it from our customer's Linux workstation and apply it.

#### Request Prompt.sh

  Request Prompt.sh

```shell-session
user@workstation:~$ curl -s http://myvps.vps-provider.net/Prompt.sh | bash
```

#### Customized Bash Prompt

  Customized Bash Prompt

```shell-session
-[Wed Mar 24-11:27:15]-[user@workstation]-
-[~]$ 
```

A simple designation of these scripts is also of great use. For example, suppose we assume that we want to configure our Bash prompt and other operating system components. In that case, we need to name the scripts for this as `understandably` as possible. If we have created several scripts like this, we can write a simple Bash script from memory on the working station, which then does all the configuration. Let us assume we have created the following list of scripts, and we are hosting these on our `Virtual Private Server` (`VPS`):

#### Customization Scripts

  Customization Scripts

```shell-session
user@workstation:~$ cat customization-scripts.txt

Prompt.sh
Tools.sh
GUI.sh
Tmux.sh
Vim.sh
```

Now we could write a Bash script that takes care of all these settings for us or even combines them into a single command:

#### Customization Scripts Execution

  Customization Scripts Execution

```shell-session
user@workstation:~$ for script in $(cat customization-scripts.txt); do curl -s http://myvps.vps-provider.net/$script | bash; done
```

With this command, each customization script is retrieved and executed one by one from our VPS. This allows us to make any changes to the workstation or our new VM quickly from memory.

---

## Final Snapshot

Let us get back to our VM. Once we have adjusted all our configurations and settings, we should create a `Final snapshot` again to save our settings. We will want to add all the changes and tasks to the description of the new snapshot.

If we want to test our installation scripts to see if they work the way we want them to, we can copy them to our host system, revert our VM to the `Initial Setup` snapshot and run those scripts there. When we are satisfied with our scripts, we can then switch back to our `Final snapshot` and continue with VM encryption.

---

## VM Encryption

In addition to LVM encryption, we can encrypt the entire VM with another strong password. This gives us an extra layer of protection that will protect our results and any customer data residing on the system. This means that no one will be able to start the VM without the password we set.

Now that we have shut down and powered off the VM, we go to `Edit virtual machine settings` and select the `Options` tab.

#### VMware Encryption Settings

![](https://academy.hackthebox.eu/storage/modules/87/vm_encrypt0.png)

There we will find more additional functions and settings that we can use later. Relevant for us now is the `Access Control` settings. Once we have encrypted it, we will not be able to create a clone of it without first decrypting it. More about this can be found in the VMware [documentation](https://docs.vmware.com/en/VMware-Workstation-Pro/16.0/com.vmware.ws.using.doc/GUID-8A64D0EF-CB0E-4C50-A034-3FD5C0A0F905.html).

#### VMware Access Control

![](https://academy.hackthebox.eu/storage/modules/87/vm_encrypt1.png)

When we close VMware and open it again, we are asked for the password for this VM. Once this has been entered correctly, we can then boot and work with it accordingly.

#### VM Decryption

![](https://academy.hackthebox.eu/storage/modules/87/linux_vmware_encrypted.png)

## Windows


Windows computers serve an essential role as testbeds and victims for aspiring penetration testers like ourselves. However, it can make for a great penetration testing platform as well. There are some advantages to using Windows as our daily driver. It will blend in most enterprise environments so that we will appear physically and virtually less suspicious. It is easier to navigate and communicate with other hosts on an Active Directory domain if we use Windows versus Linux and some Python tooling. Traversing SMB and utilizing shares is much easier this way. With this in mind, it can be beneficial to familiarize ourselves with Windows and set a standard that ensures we have a stable and effective platform to perform our actions.

Building our penetration testing platform can help us in multiple ways:

1.  Since we built it and installed only the tools necessary, we should have a better understanding of what is happening under the hood. This also allows us to ensure we do not have any unnecessary services running that could potentially be a risk to ourselves and the customer when on an engagement.
    
2.  It provides us the flexibility of having multiple operating system types at our disposal if needed. These same systems used for our engagements can also serve as a testbed for payloads and exploits before launching them at the customer.
    
3.  By building and testing the systems ourselves, we know they will function as intended during the penetration test and save ourselves time troubleshooting during the engagement.
    

With all this in mind, where do we start? Fortunately for us, there are many new features with Windows that were not available just a few years ago. `Windows Subsystem for Linux (WSL)` is an excellent example of this. It allows for Linux operating systems to run alongside our Windows install. This can help us by giving us a space to run tools developed for Linux right inside our Windows host without the need for a hypervisor program or installation of a third-party application such as VirtualBox or Docker.

This section will examine and install the core components we will need to get our systems in fighting shape, such as `WSL, Visual Studio Code, Python, Git, and the Chocolatey Package Manager`. Since we are utilizing this platform to perform penetration test functions, it will also require us to make changes to our host's security settings. Keep in mind, most exploitation tools and code are just that, `USED for EXPLOITATION` and can be harmful to your host if not careful. Be mindful of what we install and run. If we do not isolate these tools off, Windows Defender will almost certainly delete any detected files and applications it deems harmful, breaking our setup. OK, let us dive in.


## Installation Requirements

The installation of the Windows VM is done in the same way as the Linux VM. We can do this on a bare-metal host or in a hypervisor. With either option, we have some requirements to think about when installing Windows 10.

#### Hardware Requirements

Processor that runs at 1GHz or greater. Dual-core or better is ideal.

2G of RAM minimum, 4G or more is ideal.

60G of Hard Drive space. This ensures there is room for the OS and some tools. Size can vary based on the number of tools we install on the host.

Network connectivity, if possible, two network adapters.

Ideally, we have a moderate processor that can handle intensive loads at times. If we are attempting to run Windows virtualized, our host will need at least four cores to give two to the VM. Windows can get a bit beefy with updates and tool installs, so 80G of storage or more is ideal. When it comes to RAM, 4G would be a minimum to ensure we do not have any latency or issues while performing our penetration tests.

#### Software Requirements

Unlike most Linux distributions, Windows is a licensed product. To stay in good standing, ensure we are adhering to the terms of use. For now, a great place to start is to grab a copy of a Developer VM [here](https://developer.microsoft.com/en-us/windows/downloads/virtual-machines/). We can use this to begin building out our platform. The Developer Evaluation platform comes pre-configured with:

Windows 10 Version 2004

Windows 10 SDK Version 2004

Visual Studio 2019 with the UWP, .NET desktop, and Azure workflows enabled and also includes the Windows Template Studio extension

Visual Studio Code

Windows Subsystem for Linux with Ubuntu installed

Developer mode enabled

The VM comes pre-configured with a user: `IEUser` and Password `Passw0rd!`. It is a trial virtual machine, so it has an expiration date of 90 days. Keep this in mind when configuring it. Once we have a baseline VM, take a snapshot.


## Core Changes

To prepare our Windows host, we have to make a few changes before installing our fun tools:

1.  We will need to update our host to ensure it is working at the required level and keep our security posture as strong as possible.
    
2.  We will want to install the Windows Subsystem for Linux and the [Chocolatey Package manager](https://chocolatey.org/). Once these tasks are completed, we can make our exclusions to Windows Defender scanning policies to ensure they will not quarantine our newly installed tools and scripts. From this point, it is now time to install our tools and scripts of choice.
    
3.  We will finish our buildout by taking a backup or snapshot of the host to have a fallback point if something happens to it.
    


## Updates

To keep with our command-line use, we will work at utilizing the command-line whenever possible. To start installing updates on our host, we will need the PSWindowsUpdate module. To acquire it, we will open an administrator Powershell window and issue the following commands:

#### Updates

  Updates

```powershell-session
PS C:\htb> Get-ExecutionPolicy -List

Scope ExecutionPolicy
----- ---------------
MachinePolicy Undefined
UserPolicy Undefined
Process Undefined
CurrentUser Undefined
LocalMachine Undefined 
```

We must first check our systems Execution Policy to ensure we can download, load, and run modules and scripts. The above command will show us a list output with the policy set for each scope. In our case, we do not want this change to be permanent, so we will only change the ExecutionPolicy for the scope of `Process`.

#### Execution Policy

  Execution Policy

```powershell-session
PS C:\htb> Set-ExecutionPolicy Unrestricted -Scope Process

Execution Policy Change
The execution policy helps protect you from scripts that you do not trust. 
Changing the execution policy might expose you to the security risks described in the about_Execution_Policies help topic at https:/go.microsoft.com/fwlink/?LinkID=135170. Do you want to change the execution policy?
[Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "N"): A

PS C:\htb> Get-ExecutionPolicy -List

Scope ExecutionPolicy
----- ---------------
MachinePolicy Undefined
UserPolicy Undefined
Process Unrestricted
CurrentUser Undefined
LocalMachine Undefined
```

Once we set our ExecutionPolicy, recheck it to make sure our change took effect. By changing the Process scope policy, we ensure our change is temporary and only applies to the current Powershell process. Changing it for any other scope will modify a registry setting and persist until we change it again.

Now that we have our ExecutionPolicy set, let us install the PSWindowsUpdate module and apply our updates. We can do so by:

#### PSWindowsUpdate

  PSWindowsUpdate

```powershell-session
PS C:\htb> Install-Module PSWindowsUpdate 

Untrusted repository 
You are installing the modules from an untrusted repository. If you trust this repository, 
change its InstallationPolicy value by running the Set-PSRepository cmdlet. 
Are you sure you want to install the modules from 'PSGallery'?
[Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "N"): A 
```

Once the module installation completes, we can import it and run our updates.

  PSWindowsUpdate

```powershell-session
PS C:\htb> Import-Module PSWindowsUpdate 

PS C:\htb> Install-WindowsUpdate -AcceptAll

X ComputerName Result KB Size Title
- ------------ ------ -- ---- -----
1 DESKTOP-3... Accepted KB2267602 510MB Security Intelligence Update for Microsoft Defender Antivirus - KB2267602...
1 DESKTOP-3... Accepted 17MB VMware, Inc. - Display - 8.17.2.14
2 DESKTOP-3... Downloaded KB2267602 510MB Security Intelligence Update for Microsoft Defender Antivirus - KB2267602...
2 DESKTOP-3... Downloaded 17MB VMware, Inc. - Display - 8.17.2.14 3 DESKTOP-3... Installed KB2267602 510MB Security Intelligence Update for Microsoft Defender Antivirus - KB2267602... 3 DESKTOP-3... Installed 17MB VMware, Inc. - Display - 8.17.2.14 

PS C:\htb> Restart-Computer -Force

```

The above Powershell example will import the `PSWindowsUpdate` module, run the update installer, and then reboot the PC to apply changes. Be sure to run updates regularly, especially if we plan to use this host frequently and not destroy it at the end of each engagement. Now that we have our updates installed let us get our package manager and other essential core tools.


## Chocolatey Package Manager

Chocolatey is a free and open software package management solution that can manage the installation and dependencies for our software packages and scripts. It also allows for automation with Powershell, Ansible, and several other management solutions. Chocolatey will enable us to install the tools we need from one source instead of downloading and installing each tool individually from the internet. Follow the Powershell windows below to learn how to install Chocolatey and use it to gather and install our tools.

#### Chocolatey

  Chocolatey

```powershell-session
PS C:\htb> Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))

Forcing web requests to allow TLS v1.2 (Required for requests to Chocolatey.org)
Getting latest version of the Chocolatey package for download.
Not using proxy.
Getting Chocolatey from https://community.chocolatey.org/api/v2/package/chocolatey/0.10.15.
Downloading https://community.chocolatey.org/api/v2/package/chocolatey/0.10.15 to C:\Users\DEMONS~1\AppData\Local\Temp\chocolatey\chocoInstall\chocolatey.zip
Not using proxy.
Extracting C:\Users\DEMONS~1\AppData\Local\Temp\chocolatey\chocoInstall\chocolatey.zip to C:\Users\DEMONS~1\AppData\Local\Temp\chocolatey\chocoInstall
Installing Chocolatey on the local machine
Creating ChocolateyInstall as an environment variable (targeting 'Machine')
Setting ChocolateyInstall to 'C:\ProgramData\chocolatey'

...SNIP...

Chocolatey (choco.exe) is now ready.
You can call choco from the command-line or PowerShell by typing choco.
Run choco /? for a list of functions.
You may need to shut down and restart powershell and/or consoles
first prior to using choco.
Ensuring Chocolatey commands are on the path
Ensuring chocolatey.nupkg is in the lib folder

```

We have now installed chocolatey. The Powershell string we issued sets our ExecutionPolicy for the session and then downloads the installer from chocolatey.org and runs the script. Next, we will update chocolatey then start installing packages. To ensure no issues arise, it is recommended that we periodically restart our host.

  Chocolatey

```powershell-session
PS C:\htb> choco upgrade chocolatey -y 

Chocolatey v0.10.15
Upgrading the following packages:
chocolatey
By upgrading, you accept licenses for the packages.
chocolatey v0.10.15 is the latest version available based on your source(s).

Chocolatey upgraded 0/1 packages.
See the log for details (C:\ProgramData\chocolatey\logs\chocolatey.log).
```

Now that we are sure chocolatey is up-to-date let us run our packages. We can use `choco` to install packages by issuing the `choco install pkg1 pkg2 pkg3` command listing out the package you need one by one separated by spaces. Alternatively, we can use a `packages.config` file for the installation. This is an XML file formatted so that chocolatey can install a list of packages. One helpful command to use is `choco info pkg`. It will show us various information about a package if it is available in the choco repository. See the [install page](https://docs.chocolatey.org/en-us/choco/commands/install) for more info on how to utilize chocolatey.

  Chocolatey

```powershell-session
PS C:\htb> choco info vscode 

Chocolatey v0.10.15
vscode 1.55.1 [Approved]
Title: Visual Studio Code | Published: 4/9/2021
Package approved as a trusted package on Apr 09 2021 01:34:23.
Package testing status: Passing on Apr 09 2021 00:49:32.
Number of Downloads: 1999367 | Downloads for this version: 19751
Package url
Chocolatey Package Source: https://github.com/chocolatey-community/chocolatey-coreteampackages/tree/master/automatic/vscode
Package Checksum: 'fTzzpEG+cspu7FUdqMbj8EqaD8cRIQ/cXtAUv7JGVB9uc23vuGNiuceqM94irt+nx8MGM0xAcBwdwBH+iE+tgQ==' (SHA512)
Tags: microsoft visualstudiocode vscode development editor ide javascript typescript admin foss cross-platform
Software Site: https://code.visualstudio.com/
Software License: https://code.visualstudio.com/License
Software Source: https://github.com/Microsoft/vscode
Documentation: https://code.visualstudio.com/docs
Issues: https://github.com/Microsoft/vscode/issues
Summary: Visual Studio Code
Description: Build and debug modern web and cloud applications. Code is free and available on your favorite platform - Linux, Mac OSX, and Windows.
...SNIP...
```

Above is an example of using the info option with chocolatey.

  Chocolatey

```powershell-session
PS C:\htb> choco install python vscode git wsl2 openssh openvpn 

Chocolatey v0.10.15
Installing the following packages:
python;vscode;git;wsl2;openssh;openvpn 
...SNIP... 

Chocolatey installed 20/20 packages.
See the log for details (C:\ProgramData\chocolatey\logs\chocolatey.log).

Installed:
- kb2919355 v1.0.20160915
- python v3.9.4
- kb3033929 v1.0.5
- chocolatey-core.extension v1.3.5.1
- kb2999226 v1.0.20181019
- python3 v3.9.4
- openssh v8.0.0.1
- vcredist2015 v14.0.24215.20170201
- gpg4win-vanilla v2.3.4.20191021
- vscode.install v1.55.1
- wsl2 v2.0.0.20210122
- kb2919442 v1.0.20160915
- openvpn v2.4.7
- git.install v2.31.1
- vscode v1.55.1
- vcredist140 v14.28.29913
- kb3035131 v1.0.3
- dotnet4.5.2 v4.5.2.20140902
- git v2.31.1
- chocolatey-windowsupdate.extension v1.0.4

PS C:\htb> refresh env 
```

We can see in the terminal above that `choco` installed the packages we requested and pulled any dependencies required. Issuing the `refreshenv` command will update Powershell and any environment variables that were applied. Up to this point, we have our core tools installed. These tools will enable our operations. To install other packages, use the `choco install pkg` command to pull any operational tools we need. We have included a list of helpful packages that can aid us in completing a penetration test below. See the automation section further down to begin automating installing the tools and packages we commonly need and use.


## Windows Terminal

Windows Terminal is Microsoft's updated release for a GUI terminal emulator. It supports using many different command-line tools to include Command Prompt, PowerShell, and Windows Subsystem for Linux. The terminal allows for the use of customizable themes, configurations, command-line arguments, and custom actions. A terminal is a versatile tool for managing multiple shell types and will quickly become a staple for most.

![image](https://academy.hackthebox.eu/storage/modules/87/terminal-example.png)

To install Terminal with Chocolatey:

  Chocolatey

```powershell-session
PS C:\htb> choco install microsoft-windows-terminal

Chocolatey v0.10.15
2 validations performed. 1 success(es), 1 warning(s), and 0 error(s).

Validation Warnings:
- A pending system reboot request has been detected, however, this is
being ignored due to the current Chocolatey configuration. If you
want to halt when this occurs, then either set the global feature
using:
choco feature enable -name=exitOnRebootDetected
or pass the option --exit-when-reboot-detected.

Installing the following packages:
microsoft-windows-terminal
By installing you accept licenses for the packages.
Progress: Downloading microsoft-windows-terminal 1.6.10571.0... 100%

microsoft-windows-terminal v1.6.10571.0 [Approved]
microsoft-windows-terminal package files install completed. Performing other installation steps.
Progress: 100% - Processing The install of microsoft-windows-terminal was successful.
Software install location not explicitly set, could be in package or
default install location if installer.

Chocolatey installed 1/1 packages.
See the log for details (C:\ProgramData\chocolatey\logs\chocolatey.log). 
```


## Windows Subsystem for Linux 2

`Windows Subsystem for Linux 2` (`WSL2`) is the second iteration of Microsoft's architecture that allows users to run Linux instances, provides the ability to run Bash scripts and other apps like Vim, Python, etc. WSL also us to interact with the Windows operating system and file structure from a Unix instance. Best of all, it is done without the use of a hypervisor like VirtualBox or Hyper-V.

What does this mean for us? Having the ability to interact and utilize Linux native tools and applications from our Windows host provides us with a hybrid environment and the flexibility that comes with it. To install the subsystem, the quickest route is to utilize chocolatey.

#### Chocolatey - WSL2

  Chocolatey - WSL2

```powershell-session
PS C:\htb> choco install WSL2

Chocolatey v0.10.15
2 validations performed. 1 success(es), 1 warning(s), and 0 error(s).
Installing the following packages:
wsl2
By installing you accept licenses for the packages.
Progress: Downloading wsl2 2.0.0.20210122... 100%

wsl2 v2.0.0.20210122 [Approved]
wsl2 package files install completed. Performing other installation steps.
...SNIP...
wsl2 may be able to be automatically uninstalled.
The install of wsl2 was successful.
Software installed as 'msi', install location is likely default.

Chocolatey installed 1/1 packages.
See the log for details (C:\ProgramData\chocolatey\logs\chocolatey.log).
```

Once WSL is installed, we can add the Linux platform of our choice. The most common one to find is Ubuntu on the Microsoft store. Current Linux distributions supported for WSL are:

-   [Ubuntu 16.04 LTS](https://www.microsoft.com/store/apps/9pjn388hp8c9), [18.04 LTS](https://www.microsoft.com/store/apps/9N9TNGVNDL3Q), and [20.04 LTS](https://www.microsoft.com/store/apps/9n6svws3rx71)
-   [openSUSE Leap 15.1](https://www.microsoft.com/store/apps/9NJFZK00FGKV)
-   [SUSE Linux Enterprise Server 12 SP5](https://www.microsoft.com/store/apps/9MZ3D1TRP8T1) and [15 SP1](https://www.microsoft.com/store/apps/9PN498VPMF3Z)
-   [Kali Linux](https://www.microsoft.com/store/apps/9PKR34TNCV07)
-   [Debian GNU/Linux](https://www.microsoft.com/store/apps/9MSVKQC78PK6)
-   [Fedora Remix for WSL](https://www.microsoft.com/store/apps/9n6gdm4k2hnc)
-   [Pengwin](https://www.microsoft.com/store/apps/9NV1GV1PXZ6P) and [Pengwin Enterprise](https://www.microsoft.com/store/apps/9N8LP0X93VCP)
-   [Alpine WSL](https://www.microsoft.com/store/apps/9p804crf0395)

To install the distribution of our choice, just click the link above, and it will take us to the Microsoft Store page for the distro. Once we have it installed, we need to open a PowerShell prompt and type `bash`.

![image](https://academy.hackthebox.eu/storage/modules/87/windows-bash.png)

From this point, we can use it as a regular OS, alongside our Windows install.


## Security Configurations and Defender Modifications

Since we will be using this platform as a penetration testing host, we may run into some issues with Windows Defender finding our tools unsavory. Windows Defender will scan and quarantine or remove anything it deems potentially harmful. To make sure Defender does not mess up our plans, we will add some exclusion rules to ensure our tools stay in place.

#### Windows Defender Exemptions for the Tools' Folders.

-   `C:\Users\your user here\AppData\Local\Temp\chocolatey\`
    
-   `C:\Users\your user here\Documents\git-repos\`
    
-   `C:\Users\your user here\Documents\scripts\`
    

These three folders are just a start. As we add more tools and scripts, we may need to add more exclusions. To exclude these files, we will we a PowerShell command.

#### Adding Exclusions

  Adding Exclusions

```powershell-session
PS C:\htb> Add-MpPreference -ExclusionPath "C:\Users\your user here\AppData\Local\Temp\chocolatey\"
```

Repeat the same steps for each folder we wish to exclude.


## Tool Install Automation

Utilizing Chocolatey for package management makes it super easy to automate the initial install of core tools and applications. We can use a simple PowerShell script to pull everything for us in one run. Here is an example of a simple script to install some of our requirements. As usual, before executing any scripts, we need to change the execution policy. Once we have our initial script built, we can modify it as our toolkit changes and reuse it to speed up our setup process.

#### Choco Build Script

Code: powershell

```powershell
# Choco build script

write-host "*** Initial app install for core tools and packages. ***"

write-host "*** Configuring chocolatey ***"
choco feature enable -n allowGlobalConfirmation

write-host "*** Beginning install, go grab a coffee. ***"
choco upgrade wsl2 python git vscode openssh openvpn netcat nmap wireshark burp-suite-free-edition heidisql sysinternals putty golang neo4j-community openjdk

write-host "*** Build complete, restoring GlobalConfirmation policy. ***"
choco feature disable -n allowGlobalCOnfirmation
```

When scripting with Chocolatey, the developers recommend a few rules to follow:

-   always use `choco` or `choco.exe` as the command in your scripts. `cup` or `cinst` tends to misbehave when used in a script.
    
-   when utilizing options like `-n` it is recommended that we use the extended option like `--name`.
    
-   Do not use `--force` in scripts. It overrides Chocolatey's behavior.
    

Not all of our packages can be acquired from Chocolatey. Fortunately for us, a majority of what is left resides in Github. We can set up a script for this and download the repositories and binaries we need, then extract them to our scripts folder. Below we will build out a quick example of a Git script. First, let us see what it looks like to clone a repository to our local host.

#### Git Clone

  Git Clone

```powershell-session
PS C:\htb> git clone https://github.com/dafthack/DomainPasswordSpray.git

Cloning into 'DomainPasswordSpray'...
remote: Enumerating objects: 149, done.
remote: Counting objects: 100% (6/6), done.
remote: Compressing objects: 100% (5/5), done.
Receiving objects:  94% (141/149)(delta 1), reused 5 (delta 1), pack-reused 143
Receiving objects: 100% (149/149), 51.70 KiB | 3.69 MiB/s, done.
Resolving deltas: 100% (52/52), done.
PS C:\Users\demonstrator\Documents\scripts> ls


    Directory: C:\Users\demonstrator\Documents\scripts


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----         4/16/2021   4:58 PM                DomainPasswordSpray


PS C:\Users\demonstrator\Documents\scripts> cd .\DomainPasswordSpray\
PS C:\Users\demonstrator\Documents\scripts\DomainPasswordSpray> ls


    Directory: C:\Users\demonstrator\Documents\scripts\DomainPasswordSpray


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         4/16/2021   4:58 PM          19419 DomainPasswordSpray.ps1
-a----         4/16/2021   4:58 PM           1086 LICENSE
-a----         4/16/2021   4:58 PM           2678 README.md
```

We issued the `git clone` command with the URL to the repository we needed. From the output, we can tell it created a new folder in our scripts folder then populated it with the files from the GitHub repository.


## Testing VMs

It is common and very relevant to prepare our penetration testing VMs and VMs for the most common operating systems and their patch levels. This is especially necessary if we want to mirror our target machines and test our exploits before applying them to real machines. For example, we can install a Windows 10 VM that is built on different [patches](https://support.microsoft.com/en-us/topic/windows-10-update-history-24ea91f4-36e7-d8fd-0ddb-d79d9d0cdbda) and [releases](https://docs.microsoft.com/en-us/windows/release-health/release-information). This will save us considerable time in the course of our penetration tests to configure them again. These VMs will help us test our approach and exploits to understand better how the interconnected system might react to them because it may be that we will only have one attempt to execute the exploit.

The good thing here is that we do not have to set up 20 VMs for this but can work with snapshots. For example, we can start with Windows 10 version 1607 (OS build 14393) and update our system step by step and create a snapshot of the clean system from each of these updates and patches. Updates and patches can be downloaded from the [Microsoft Update Catalog](https://www.catalog.update.microsoft.com/Search.aspx?q=KB4550994). We just need to use the `Kb article` designation, and there we will find the appropriate files to download and patch our systems.

Tools that can be used to install older versions of Windows:

-   [Chocolatey](https://community.chocolatey.org/)
    
-   [MediaCreationTool.bat](https://gist.github.com/AveYo/c74dc774a8fb81a332b5d65613187b15)
    
-   [Microsoft Windows and Office ISO Download Tool](https://www.heidoc.net/joomla/technology-science/microsoft/67-microsoft-windows-and-office-iso-download-tool%EF%BB%BF)
    
-   [Rufus](https://rufus.ie/en_US/)
    



## Next Steps

Now that we have our Linux and Windows VMs configured, let's dive into Virtual Private Server (VPS) setup and usage.

1.  VPS Providers

# VPS Providers


A `Virtual Private Server` (`VPS`) is an isolated environment created on a physical server using virtualization technology. A VPS is fundamentally part of `Infrastructure-as-a-Service` (`IaaS`) solutions. This solution offers all the advantages of an ordinary server, with specially allocated resources and complete management freedom. We can freely choose the operating system and applications we want to use, with no configuration restrictions. This VM uses the resources of a physical server and provides users with various server functionalities comparable to those of a dedicated server. It is therefore also referred to as a `Virtual Dedicated Server` (`VDS`).

A VPS positions itself as a compromise between inexpensive shared hosting and the usually costly rental of dedicated server technology. This hosting model's idea is to offer users the most comprehensive possible range of functions at manageable prices. The virtual replication of individual computer systems on a standard host system involves significantly less effort for a web hoster than the provision of separate hardware components for each customer. Extensive independence of the individual guest systems is achieved through encapsulation. Each VPS on the shared hardware base acts isolated from other, parallel operating systems. In most cases, VPS is used for the following purposes, but not limited to:


`Webserver`   `LAMP/XAMPP stack`   `Mail server`   `DNS server`   `Development server`   

`Proxy server` `Test server`   `Code repository`   `Pentesting` `VPN` `Gaming server` `C2 server`


We can choose from a range of Windows and Linux operating systems to provide the required operating environment for our desired application or software during installation.

`Provider`   `Price`   `RAM`    `CPU Cores`   `Storage`   `Transfer/Bandwidth`

[Vultr](https://www.vultr.com/products/cloud-compute/)   `$10/mo`   `2GB`   `1 CPU`   `55 GB`   `2 TB`

[Linode](https://www.linode.com/pricing/)   `$10/mo`   `2GB`   `1 CPU`   `50 GB`   `2 TB`

[DigitalOcean](https://www.digitalocean.com/pricing)   `$10/mo`   `2GB`   `1 CPU`   `50 GB`   `2 TB`

[OneHostCloud](https://onehostcloud.hosting/)   `$14.99/mo` `2GB` `1 CPU` `50 GB` `2 TB`

1.  VPS Setup

## VPS Setup


Whether we are on an internal or external penetration test, a VPS that we can use is of great importance to us in many different cases. We can store all our resources on it and access them from almost any point with internet access. Apart from the fact that we first have to set up the VPS, we also have to prepare the corresponding folder structure and its services. In this example, we will deal with the provider called [Vultr](https://www.vultr.com/) and set up our VPS there. For the other providers, the configuration options look almost identical.

We should still read the individual information about their components carefully and understand what kind of VPS we will be working with in the future. In Vultr, we can choose one of the four different servers. For our purposes, the `Cloud Computer` server is sufficient for now.

![](https://academy.hackthebox.eu/storage/modules/87/vps1.png)

Next, we need to select the location closest to us. This will ensure an excellent connection to our server. If we need to perform a penetration test on another continent, we can also set up a VPS there using our automation scripts and perform the penetration test from there.

![](https://academy.hackthebox.eu/storage/modules/87/vps2.png)

For the server type, we select which operating system should be installed on the VPS. ParrotOS is based on `Debian`, just like Ubuntu. Here we can choose one of these two or go to advanced options and upload our ISO.

![](https://academy.hackthebox.eu/storage/modules/87/vps2_2.png)

If we want to upload our ISO, we have to do it via a public link to that ISO. We can go to the [ParrotOS website](https://www.parrotsec.org/security-edition/), copy the link to the `mirror server` for the appropriate version, and paste it into Vultr.

![](https://academy.hackthebox.eu/storage/modules/87/vps3.png)

Next, we need to choose the performance level for our VPS. This is one of the points where the cost can change a lot. For our purposes, however, we can choose one of the two options on the left. However, here we need to select the performance for which we want to use the VPS. If the VPS is to be used for advanced purposes and provides many requests and services, 1024MB memory will not be enough. Therefore, it is always advisable to first set up the installation of our OS locally in a VM and then check the services' load.

![](https://academy.hackthebox.eu/storage/modules/87/vps4.png)

Next, we have the choice if we want to use [IPv6](https://en.wikipedia.org/wiki/IPv6) for our VPS. This is highly recommended because many firewalls are only protected against IPv4, and `IPv6` is forgotten. We can also allow `automatic backups`, `private networking`, and `DDOS protection`, but at a higher cost.

![](https://academy.hackthebox.eu/storage/modules/87/vps5.png)

After that, we can generate our SSH keys, which we can use to log in to the VPS via SSH later. We can also generate these keys later on our VPS or our VM or own host operating system. Let's use our VM to generate the key pair.

#### Generate SSH Keys

  Generate SSH Keys

```shell-session
┌─[cry0l1t3@parrot]─[~]
└──╼ $ ssh-keygen -t rsa -b 4096 -f vps-ssh

Generating public/private rsa key pair.
Enter passphrase (empty for no passphrase): ******************
Enter same passphrase again: ******************
Your identification has been saved in vps-ssh
Your public key has been saved in vps-ssh.pub
The key fingerprint is:
SHA256:zXyVAWK00000000000000000000VS4a/f0000+ag cry0l1t3@parrot
The key's randomart image is:
...SNIP...
```

With the command shown above, we generate two different keys. The `vps-ssh` is the `private key` and must not be shared anywhere or with anyone. The second `vps-ssh.pub` is the `public key` which we can now insert in the Vultr control panel.

#### SSH Keys

  SSH Keys

```shell-session
┌─[cry0l1t3@parrot]─[~]
└──╼ $ ls -l vps*

-rw------- 1 cry0l1t3 cry0l1t3 3434 Mar 30 12:23 vps-ssh
-rw-r--r-- 1 cry0l1t3 cry0l1t3  741 Mar 30 12:23 vps-ssh.pub
```

![](https://academy.hackthebox.eu/storage/modules/87/vps6.png)

Finally, we choose a hostname and the server label with which we want to name our VPS.

![](https://academy.hackthebox.eu/storage/modules/87/vps7.png)

Once the VPS is installed, we can access it via SSH.

#### SSH Using Password

  SSH Using Password

```shell-session
W4H33D@htb[/htb]$ ssh root@<vps-ip-address>

root@<vps-ip-address>'s password: 

[root@VPS ~]# 
```

After that, we should add a new user for the VPS to not run our services with root or administrator privileges. For this, we can then generate another SSH key and insert it for this user.

#### Adding a New Sudo User

  Adding a New Sudo User

```shell-session
[root@VPS ~]# adduser cry0l1t3
[root@VPS ~]# usermod -aG sudo cry0l1t3
[root@VPS ~]# su - cry0l1t3
Password: 

[cry0l1t3@VPS ~]$
```

#### Adding Public SSH Key to VPS

  Adding Public SSH Key to VPS

```shell-session
[cry0l1t3@VPS ~]$ mkdir ~/.ssh
[cry0l1t3@VPS ~]$ echo '<vps-ssh.pub>' > ~/.ssh/authorized_keys
[cry0l1t3@VPS ~]$ chmod 600 ~/.ssh/authorized_keys
```

Once we have added this to the `authorized_keys` file, we can use the `private key` to log in to the system via SSH.

#### Using SSH Keys

  Using SSH Keys

```shell-session
W4H33D@htb[/htb]$ ssh root@<vps-ip-address> -i vps-ssh-cry0l1t3

[cry0l1t3@VPS ~]$ 
```

# VPS Hardening

---

Another necessary step in our configuration and setup of our VPS is the hardening of the system and access. We should limit our access to the VPS to SSH and disable all other services on the VPS. Finally, we will reduce the attack vectors to a minimum and provide only one possible access to our VPS, which we will secure in the best possible way. We should keep in mind that, if possible, we should not store any sensitive data on the VPS, or at least only for a short period when we perform an internal penetration test. In doing so, we should follow the principle that someone could gain access to the system sooner or later.

However, since in this case, the VPS is only used as a source for our organization and tools and we can access these resources via SSH, we should secure and harden the SSH server accordingly so that no one else (or at least no one other than the team members) can access it. There are many ways to harden it, and this includes the following precautions, but not limited to:

-   Install Fail2ban
-   Working only with SSH keys
-   Reduce Idle timeout interval
-   Disable passwords
-   Disable x11 forwarding
-   Use a different port
-   Limit users' SSH access
-   Disable root logins
-   Use SSH proto 2
-   Enable 2FA Authentication for SSH

`It is highly recommended to try these settings and precautions first in a local VM we have created before making these settings on a VPS.`

One of the first steps in hardening our system is updating and bringing the system `up-to-date`. We can do this with the following commands:

#### Update the System

  Update the System

```shell-session
[cry0l1t3@VPS ~]$ sudo apt update -y && sudo apt full-upgrade -y && sudo apt autoremove -y && sudo apt autoclean -y
```


## SSH Hardening

SSH is always installed on the VPS, giving us guaranteed access to the server in advance. Now we can change some of the settings in the configuration file `/etc/ssh/sshd_config` to enforce these security measures for our SSH server. In this file, we will comment out, change or add some lines. The entire list of possible settings that can be made for the SSH daemon can be found on the [man page](https://man.openbsd.org/sshd_config).

#### Install Fail2Ban

  Install Fail2Ban

```shell-session
[cry0l1t3@VPS ~]$ sudo apt install fail2ban -y
```

Once we have installed it, we can find the configuration file at `/etc/fail2ban/jail.conf`. We should make a backup if we make a mistake somewhere and it does not work as it should.

#### Fail2Ban Config Backup

  Fail2Ban Config Backup

```shell-session
[cry0l1t3@VPS ~]$ sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.conf.bak
```

In this file, we will find the field commented out with `# [sshd]`. In most cases, we will find the first line commented out there. Otherwise, we add this and two more, as shown in the following example:

#### /etc/fail2ban/jail.conf

  /etc/fail2ban/jail.conf

```shell-session
...SNIP...

# [sshd]
enabled = true
bantime = 4w
maxretry = 3
```

With this, we enable monitoring for the SSH server, set the `ban time` to four weeks, and allow a maximum of 3 `attempts`. The advantage of this is that once we have configured our `2FA` feature for SSH, fail2ban will ban the IP address that has entered the `verification code` incorrectly three times too. We should make the following configurations in the `/etc/ssh/sshd_config` file:

#### Editing OpenSSH Config

  Editing OpenSSH Config

```shell-session
[cry0l1t3@VPS ~]$ sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
[cry0l1t3@VPS ~]$ sudo vim /etc/ssh/sshd_config
```


**`Settings`**  & **`Description`**

1. `LogLevel VERBOSE`: Gives the verbosity level that is used when logging messages from SSH daemon.

2. `PermitRootLogin no`: Specifies whether root can log in using SSH.

3. `MaxAuthTries 3`: Specifies the maximum number of authentication attempts permitted per connection.

4. `MaxSessions 5`: Specifies the maximum number of open shell, login, or subsystem (e.g., SFTP) sessions allowed per network connection.

5. `HostbasedAuthentication no`: Specifies whether rhosts or /etc/hosts.equiv authentication together with successful public key client host authentication is allowed (host-based authentication).

6.` PermitEmptyPasswords no`: When password authentication is allowed, it specifies whether the server allows login to accounts with empty password strings.

7. `ChallengeResponseAuthentication yes`: Specifies whether challenge-response authentication is allowed.

8. `UsePAM yes`: Specifies if PAM modules should be used for authentification.

9. `X11Forwarding no`: Specifies whether X11 forwarding is permitted.

10. `PrintMotd no`: Specifies whether SSH daemon should print /etc/motd when a user logs in interactively.

11. `ClientAliveInterval 600`: Sets a timeout interval in seconds, after which if no data has been received from the client, the SSH daemon will send a message through the encrypted channel to request a response from the client.

12. `ClientAliveCountMax 0`: Sets the number of client alive messages which may be sent without SSH daemon receiving any message back from the client.

13. `AllowUsers <username>`: This keyword can be followed by a list of user namepatterns, separated by spaces. If specified, login is allowed only for user names that match one of the patterns.

14. `Protocol 2`: Specifies the usage of the newer protocol with is more secure.

15. `AuthenticationMethods publickey,keyboard-interactive`: Specifies the authentication methods that must be successfully completed for a user to be granted access.

16. `PasswordAuthentication no`: Specifies whether password authentication is allowed.


## 2FA Authentication

With the configuration shown above, we have already taken essential steps to harden our SSH. Therefore, we can now go one step further and configure `2-factor authentication` (`2FA`). With this, we use a third-party software called Google Authenticator, which generates a six-digit code every 30 seconds that is needed to authenticate our identity. These six-digit codes represent a so-called `One-Time-Password` (OTP). `2FA` has proven itself an authentication method, not least because of its relatively high-security standard compared to the time required for implementation. Two different and independent authentication factors verify the identity of a person requesting access. We can find more information about `2FA` [here](https://en.wikipedia.org/wiki/Multi-factor_authentication).

We will use Google Authenticator as our authentication application on our Android or iOS smartphone. For this, we need to download and install the application from the Google/Apple Store. A guide on setting up Google Authenticator on our smartphone can be found [here](https://support.google.com/accounts/answer/185839?co=GENIE.Platform%3DDesktop&hl=en). To configure 2FA with Google Authenticator on our VPS, we need the [Google-Authenticator PAM module](https://github.com/google/google-authenticator-libpam). We can then install it and execute it to start configuring it as follows:

#### Installing Google-Authenticator PAM Module

  Installing Google-Authenticator PAM Module

```shell-session
[cry0l1t3@VPS ~]$ sudo apt install libpam-google-authenticator -y
[cry0l1t3@VPS ~]$ google-authenticator

Do you want authentication tokens to be time-based (y/n) y

Warning: pasting the following URL into your browser exposes the OTP secret to Google:
  https://www.google.com/chart?chs=200x200&chld=M|0&cht=qr&chl=otpauth://totp/cry0l1t3@parrot%3Fsecret%...SNIP...%26issuer%3Dparrot

   [ ---- QR Code ---- ]

Your new secret key is: ***************
Enter code from app (-1 to skip):
```

If we follow these steps, then a `QR code` and a `secret key` will appear in our terminal, which we can then scan with the Google Authenticator app or enter the secret key there. Once we have scanned the QR code or entered the secret key, we will see the first `OTP` (six-digit number) on our smartphone. We enter this in our terminal to synchronize and authorize Google Authenticator on our smartphone and our VPS with Google.

The module will then generate several `emergency scratch codes` (`backup codes`), which we should save safely. These will be used in case we lose our smartphone. Should this happen, we can then log in with the `backup codes`.

#### Google-Authenticator Configuration

  Google-Authenticator Configuration

```shell-session
Enter code from app (-1 to skip): <Google-Auth Code>

Code confirmed
Your emergency scratch codes are:
  21323478
  43822347
  60232018
  73234726
  45456791
  
Do you want me to update your "/home/cry0l1t3/.google_authenticator" file? (y/n) y

Do you want to disallow multiple uses of the same authentication
token? This restricts you to one login about every 30s, but it increases
your chances to notice or even prevent man-in-the-middle attacks (y/n) y

By default, a new token is generated every 30 seconds by the mobile app.
In order to compensate for possible time-skew between the client and the server,
we allow an extra token before and after the current time. This allows for a
time skew of up to 30 seconds between authentication server and client. If you
experience problems with poor time synchronization, you can increase the window
from its default size of 3 permitted codes (one previous code, the current
code, the next code) to 17 permitted codes (the 8 previous codes, the current
code, and the 8 next codes). This will permit for a time skew of up to 4 minutes
between client and server.
Do you want to do so? (y/n) n

If the computer that you are logging into isn't hardened against brute-force
login attempts, you can enable rate-limiting for the authentication module.
By default, this limits attackers to no more than 3 login attempts every 30s.
Do you want to enable rate-limiting? (y/n) y
```

Next, we need to configure the [PAM](https://en.wikipedia.org/wiki/Linux_PAM) module for the SSH daemon. To do this, we first create a backup of the file and open the file with a text editor such as Vim.

#### 2FA PAM Configuration

  2FA PAM Configuration

```shell-session
[cry0l1t3@VPS ~]$ sudo cp /etc/pam.d/sshd /etc/pam.d/sshd.bak
[cry0l1t3@VPS ~]$ sudo vim /etc/pam.d/sshd
```

We comment out the "`@include common-auth`" line by putting a "`#`" in front of it. Besides, we add two new lines at the end of the file, as follows:

#### /etc/pam.d/sshd

  /etc/pam.d/sshd

```shell-session
#@include common-auth

...SNIP...

auth required pam_google_authenticator.so
auth required pam_permit.so
```

Next, we need to adjust our settings in our SSH daemon to allow this authentication method. In this configuration file (`/etc/ssh/sshd_config`), we need to add two new lines at the end of the file as follows:

#### /etc/ssh/sshd_config

  /etc/ssh/sshd_config

```shell-session
...SNIP...

AuthenticationMethods publickey,keyboard-interactive
PasswordAuthentication no
```

Finally, we have to restart the SSH server to apply the new configurations and settings.

#### Restart SSH Server

  Restart SSH Server

```shell-session
[cry0l1t3@VPS ~]$ sudo service ssh restart
```

Now we can test this and try to login to the SSH server with our SSH key and check if everything works as intended.

#### 2FA SSH Login

  2FA SSH Login

```shell-session
W4H33D@htb[/htb]$ ssh cry0l1t3@VPS -i cry0l1t3

Enter passphrase for key 'cry0l1t3': *************
Verification code: <Google-Auth Code>
```

Finally, we can transfer all our resources, scripts, notes, and other components to the VPS using [SCP](https://en.wikipedia.org/wiki/Secure_copy_protocol).

#### SCP Syntax

  SCP Syntax

```shell-session
W4H33D@htb[/htb]$ scp -i <ssh-private-key> -r <directory to transfer> <username>@<IP/FQDN>:<path>
```

#### Resources Transfer

  Resources Transfer

```shell-session
W4H33D@htb[/htb]$ scp -i ~/.ssh/cry0l1t3 -r ~/Pentesting cry0l1t3@VPS:~/

Enter passphrase for key 'cry0l1t3': *************
Verification code: <Google-Auth Code>
```

---

## Closing Thoughts

We should now have an understanding of using common virtualization platforms such as VMWare Workstation/Player and VirtualBox and be comfortable with setting up VMs from scratch. After practicing the examples in this Module, we should also be comfortable setting up and hardening both a Windows and Linux attack machine for our penetration testing purposes. Finally, it is worth replicating the steps for standing up a VPS using a provider such as Vultr and practicing hardening it based on the steps in this section. It is important for us to be able to configure, harden, and maintain the systems that we use during our assessments.

Q:  What does the acronym PAM stand for?
A: Pluggable Authentication Modules
```
You can find it by visit the wiki of PAM from the following link 

PAM wiki Link: https://en.wikipedia.org/wiki/Linux_PAM
```