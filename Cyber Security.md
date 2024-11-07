# Cybersecurity

[[Home]]

# Linux Fundamentals

'''bash

ednj

'''

## Basic Commands

```bash
**whoami**: displays usernmae
**id**: displays user identity
**hostname**: sets or prints the name of current host system
**uname**: prints basic info about operating system and hardware
**pwd**: returns current directory name
**ifconfig**: view network address and/or configure network interface parameters
**ip**: similar to ifconfig, shows or manipulates routing, network devices,
		interfaces, and tunnels
**netstat**: shows network status
**ss**: used to investigate sockets
**ps**: shows process status
**who**: displays who is logged in
**env**: prints environment
**lsblk**: lists block devices
**lsusb**: lists usb devices
**lsof**: lists opened files
**useradd**: creates a new user 
userdel: deletes a user account and their files
passwd: changes password
jobs: shows background processes, linux task manager
curl: transfer data to or from a server
python 3 -m http.server: starts a python3 web server on port 8000
ls: list current directory contents
cd: change directories
mkdir: creates a directory
```

## File System

```bash
/: the top level directory, root of file system
/usr: contains executables, libraries, man files, etc
/var: contains variable data files
/home: subdirectory used for each user on the machine for storage
/etc: local system config files
```

# Web Fuzzing

## Directory Fuzzing

Many web apps have pages or files or directories that aren’t linked anywhere on the website and are only referenced by programmers at the company or in databases and stuff.

Possible information to be found:

- Sensitive Data
- Outdated Content
- Development Resources that can be leveraged for future attacks
- Hidden Functionalities that could expose future vulnerabilities

### How?

Wordlists - text files containing hundreds to thousands of common names, specifically curated for efficiency - paired with tools like GoBuster or [FFUF](https://www.notion.so/FFUF-1073eb459f0a80c7b44afa4388c0ef64?pvs=21) (Most common wordlists from the SecLists package

- Most common SecLists:
  - Discovery/Web-Content/common.txt
  - Discovery/Web-Content/directory-list-2.3-medium.txt
  - Discovery/Web-Content/raft-large-directories.txt
  - Discovery/Web-Content/big.txt

## File Fuzzing

More specifically targets certain files - each file type serves different uses so it’s important to know what type of file you should be looking for.

- File types
  - .php: files containing php code, a server-side scripting language
  - .html: files that define the structure and content of a web page
  - .txt: text files, usually contain simple yet large amounts of information such as logs
  - .bak: backup files created to preserve old versions of webapps
  - .js: Javascript code that adds functionality to a website

## Recursive Fuzzing

Rather than fuzzing for a directory and finding out you have to go deeper because a webapp is complex and has nested directories, recursive fuzzing finds directories and then automatically searches that directory for nested directories within. _Work smarter not Harder_

1. Initial Fuzzing - finding the first directory (example.com/FUZZ)
2. Directory Discovery and Expansion - creates new branch for directory and searches that (example.com/root/FUZZ)
3. Iterative Depth - repeat this process until desired depth is reached

<aside>
💡

Make sure to add “-recursion” to end of ffuf command to use recursion

</aside>

```bash
ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -ic -u http://IP:PORT/FUZZ -e .html -recursion
																																	(ignore case and comments)		(append this to each word)
```

- Other Recursion Commands

  \-recursion-depth: Sets maximum depth

  \-rate: controls the rate at which ffuf sends requests/second

# Basic Stuff

- PenTesting and other things are primary done in **Linux** operating systems, the most common being parrot OS / **Kali** Linux
- PenTesting (penetration testing) can be helpful in both offensive and defensive cybersecurity.
  - Defensive: Stop possible compromising points before they get taken advantage of
  - Offensive: Take advantage of those points
- Enumeration: A good first step with any box/target, gathering information in many ways to be well-situated with your challenge.
- Sub-Domain: An additional piece of information added to a domain name, used to organize a website.
- Nano - basic command line level text editor (syntax: sudo nano <file.txt>)
- 

# Go Buster

Helps brute-force find stuff.

Commands:

```bash
dir: Specify that we wish to do web directory enumeration.
--url (-u): Specify the web address of the target machine that 
             runs the HTTP server.
--wordlist (-w): S pecify the wordlist that we want to use
								 Example: ~$ gobuster dir -u (https://<TARGET_IP> -w 
						<FILE_PATH>
-x: Specify Filetype/File Extensions to search for
		Example: -x php,html (specifies we are looking for ONLY php or html pages
		
--append-domain: Makes GoBuster add the wordlist words as a subdomain onto the
							    main domain while brute forcing
```

This will check that URL for hidden pages (not accessible via button on website) with common page names found in different lists, like the one provided by SecLists (common.txt)

# Nmap

## Syntax:

```bash
$ sudo nmap [scan type] {TARGET_IP}
```

## Commands

```bash
Basic Scan: nmap <TARGET_IP>
Scan Specific Ports: nmap -p [ports] <TARGET_IP>
Version of Services: nmap -sV <TARGET_IP>
```

## Usage

Nmap is used to scan a network and is usually where you would start in looking for vulnerabilities. There are different types of scans for different situations, such as looking for active devices on a network, finding open ports, detect the different services running on a host network, and finding which operating system a certain machine is running.

# Finding Files

## SMB (Server Message Block)

### About

- Mostly used on **Windows** devices

- provides shared access to files, printers, and serial ports between endpoints on a network (client+server computers, printers, serial ports)

  Port 445 on the target is reserved for SMB, usually open for this reason

  SMB uses NetBIOS (inner computer stuff) to communicate, so SMB and TCP/IP (or NetBIOS) are usually running together.

  **Authentication** needed so that only certain people can read, create, and update files using an application on the remote server using SMB protocol, e.g. a username and password are required for users to access the Share

  <aside>
  <img src="https://www.notion.so/icons/exclamation-mark_gray.svg" alt="https://www.notion.so/icons/exclamation-mark_gray.svg" width="40px" /> **Share**: An SMB-enabled storage on a network. Like a folder that can be accessed over the internet

  </aside>

  Some network admins will mess up and allow unsecure log-ins through guest accounts

  If a share is found through the SMB port being open, we can access it using a script called SMBClient (must be installed on local machine).

### Commands

```bash
sudo nmap -sV {TARGET_IP}
```

```bash
**smbclient -L {TARGET_IP}**

Password for [WORKGROUP\gavinworkman]:

Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        WorkShares      Disk
```

<aside>
<img src="https://www.notion.so/icons/exclamation-mark_gray.svg" alt="https://www.notion.so/icons/exclamation-mark_gray.svg" width="40px" /> You can type the name of your script then “-h” or “-?” to see the possible commands you can perform

</aside>

```bash
ls: lists contents of the directory we are in
cd: allows you to navigate through directories
get: downloads stuff you find in a directory
exit: self-explanatory
cat: get contents of a file (and more)
```

## FTP (File Transfer Protocol)

### About

- Mostly used on Unix devices

- Used to transfer files between computers

  Port 21 is reserved for FTP and is usually open

  Able to check if anonymous logins are possible using nmap -sC -sV scan (FTP code 230)

### Commands

```jsx
ftp <TARGET_IP>
```

```bash
ls: List files in currect directory
cd: Change directory within server
get: Download a file from the server
put: Upload a file to the server
mget: Download multiple files from the server
delete: Delete a file on the server
quit: Close the FTP session
```

# SQL Injection

Many web applications use SQL (Structured Query Language) databases to store sensitive information on the back end. SQL is a language that is most widely used for querying, updating, and managing data in large databases. Websites can store user’s emails, billing info, and addresses in an SQL database, information that the public shouldn’t be seeing.

SQL Injection works by putting special characters in the username entry on the web page and modifying the search query code as this website doesn’t have input validation (it doesn’t check for bad characters it just sends it off to be queried). For example, in PHP, a hashtag makes the rest of the line of code into a comment and ignored. Therefore, putting a hastag (#) in a username cancels the search for a password and just checks if there is a matching username. It is much easier to guess that a single username exists than it is to guess the username and a password that match.

Each language is different and some sites are more secure than others, but SQL Injection is a very prevalent, easy to do, yet crucial vulnerability to web applications.

# AWS (Amazon Web Services)

- Bucket: container used to store stuff on the cloud