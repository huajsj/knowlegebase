# By pass firewall to connect to ssh server

### 1. Motivation

Due to some firewall or network setting some SSH server is not reachable but we still need to access it


### 2. Solution

using reverse ssh to connect out from the SSH server and let another machine can have the capabliltiy to
login the ssh server


### 3. Tech solution

reverser SSH 

https://unix.stackexchange.com/questions/46235/how-does-reverse-ssh-tunneling-work


### 4. Create a reverse channel from SSH server to client

 We have A box IP 192.168.1.10  as the SSH server, B box is 192.168.1.11 as the client

in A box do following to create the reverse ssh connection

```
ssh -f -N -T -R:5500:localhost:22 <username>@192.168.1.11
```

### 5. connect in B box

```
ssh <username>@127.0.0.1 -p 5500
```

### 6. Trouble shooting and pre-requirment

#### 1. B box which target to send out a SSH request need to install a ssh server or enable ssh server

if B is mac, then go to share --> (enable) remote login

#### 2. "kex_exchange_identification: read: Connection reset by peer"

if this issue happpen, beside of firewall issue, most possible is that the A box not enable ssh server

why the A box need ssh server is that reserver ssh logic is that

"*:5500:localhost:22" means, if B box try to connect it local 5500, such packet will get forward to A box

locahost 22

so A box need to enable a ssh server

```
sudo service ssh start
```

#### 3. when ssh to local, not ask password, and authen keep failed

it is because in the A box , the ssh server not enabled the password authentication do following to change

```
sudo vim /etc/ssh/sshd_config
```
change following to yes

![image](https://github.com/huajsj/knowlegebase/assets/2281489/d64c6931-45eb-4360-9402-a5c679a79ea5)


resart the sshd

```
sudo service sshd restart
```

restart in mac

```
sudo launchctl unload /System/Library/LaunchDaemons/ssh.plist
sudo launchctl load -w /System/Library/LaunchDaemons/ssh.plist
```



