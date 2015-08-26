# tinyFTP
Implementation of a tiny FTP client and server

## Introduction to tinyFTP

I spent 21 days to finish this project from Aug 3 to Aug 24, 2015 , and very happy to share with you. You can fork from my Github repository [tinyFTP](https://github.com/Wenchy/tinyFTP). Also, you can pull requests if you have any questions. I will continue updating this interesting project, and start to spend some time to finish a more interesting project **tinyHttpd**, a tiny web server just for fun.

> **PDF Online**: [tinyFTP_online](https://mozilla.github.io/pdf.js/web/viewer.html?file=https://wenchy.github.io/misc/2015-08-26-tinyFTP.pdf)

> **PDF Download**: [tinyFTP_download](https://wenchy.github.io/misc/2015-08-26-tinyFTP.pdf)

## Platform

Ubuntu14.04 64bit

## Basics

### Commands

ftp [ip-address]

- **GET**: `get [remote-file] [local-file]`
- **PUT**: `put [local-file] [remote-file]`
- **RGET**: `get [remote-dir] [local-dir]`
- **RPUT**: `put [local-dir] [remote-dir]`
- **LS**: `ls [remote-dir]` local: **LLS**
- **CD**: `cd [DIR]` local: **LCD**
- **RM**: `rm [remote-file]` local: **LRM**
- **PWD**: `pwd [-a]` local: **LPWD**
- **MKDIR**: `mkdir [remote-dir]` local: **LMKDIR**
- **RMDIR**: `rmdir [remote-dir]`
- **SHELL**: `shell [shell-cmd-string]` local: **LSHELL**
- **USERADD**: `useradd -u [username] -p [password]`
- **USERDEL**: `userdel [username]`
- **QUIT**: `quit`
- **HELP**: `help [command]`

### Special attention
1. *anonymous* user can only `GET` a file, but not `PUT`, `MKDIR`, `RM`,  `RMDIR`, or `shell`.
2. *admin* user has permission to do  `USERADD` and `USERDEL` command.

##  Features

- Multithread concurrency Model for server
- User authentication
- Salting password
- User space isolation
- Breakpoint resume
- Flash transmission

##  FTP model

```
                                          -------------
                                          |/---------\|
                                          ||   User  ||    --------
                                          ||Interface|<--->| User |
                                          |\----^----/|    --------
                ----------                |     |     |
                |/------\|  FTP Commands  |/----V----\|
                ||Server|<---------------->|  Client ||
                ||  PI  ||  FTP Replies   ||    PI   ||
                |\--^---/|                |\----^----/|
                |   |    |                |     |     |
    --------    |/--V---\|      Data      |/----V----\|    --------
    | File |<--->|Server|<---------------->|  Client |<--->| File |
    |System|    || DTP  ||   Connection   ||   DTP   ||    |System|
    --------    |\------/|                |\---------/|    --------
                ----------                -------------

                Server-FTP                   Client-FTP

    NOTES: 1. The data connection may be used in either direction.
           2. The data connection need not exist all of the time.

                    Figure 1  Model for FTP Use

```

In the model described in Figure 1, the user-protocol interpreter
initiates the control connection.  The control connection follows
the Telnet protocol.  At the initiation of the user, standard FTP
commands are generated by the user-PI and transmitted to the
server process via the control connection.  (The user may
establish a direct control connection to the server-FTP, from a
TAC terminal for example, and generate standard FTP commands
independently, bypassing the user-FTP process.) Standard replies
are sent from the server-PI to the user-PI over the control
connection in response to the commands.

The FTP commands specify the parameters for the data connection
(data port, transfer mode, representation type, and structure) and
the nature of file system operation (store, retrieve, append,
delete, etc.).  The user-DTP or its designate should "listen" on
the specified data port, and the server initiate the data
connection and data transfer in accordance with the specified
parameters.

## TODO
1. Breakpoint resume for `GET` command.
2. Enhance admin's permission to do more management.
3. Reactor concurrency Model for server: `epoll` implementation.
4. **Deamon** server.
5. **syslogd**: format log messages.
6. `DEBUG` macro for easy debugging.
7. Robust error handling, especially for internet exception.
8. More elegant **class** design, make full use of powerful features of **C++**. 
