+++
title = "Bandit Soluciones"
author = "Fcch"
date = "2021-07-07"
description = "Un solucionario a los retos Bandit de Over The Wire"
featured = false
tags = [
    "bash"
]
categories = [
    "apuntes",
]
series = ["Linux"]
thumbnail = "images/bandit/otw-bandit.jpg"
+++

Son juegos de guerra ofrecidos por la comunidad OverTheWire que nos ayudan a aprender y practicar conceptos de seguridad en forma de juegos divertidos.

El enlace para poder jugar un momento: [**Over the wire, Bandit**](https://overthewire.org/wargames/bandit/)

<!--more-->

![](/images/bandit/otw-bandit.jpg)

**Iniciamos!!!**

* Usuario: bandit0 
* Puerto: 2220
* Contraseña: bandit0
* URL: banditlabs.overthewire.org

## Bandit Level 0 → Level 1

* SSH : ssh -o PubkeyAuthentication=no -p2220 bandit0@bandit.labs.overthewire.org
* Solución:

```bash
$ cat readme
boJ9jbbUNNfktd78OOpsqOltutMc3MY1
```

## Bandit Level 1 → Level 2

* SSH:  ssh -o PubkeyAuthentication=no -p2220 bandit1@bandit.labs.overthewire.org

* Solución: 

```bash
$ cat /home/bandit1/-
CV1DtqXWVFXTvM2F0k09SHz0YwRINYA9
```

## Bandit Level 2 → Level 3

* SSH: ssh -o PubkeyAuthentication=no -p2220 bandit2@bandit.labs.overthewire.org
* Solución:

```bash
$ cat spaces\ in\ this\ filename
UmHadQclWmgdLOKQ3YNgjWxGoRMb5luK
```

## Bandit Level 3 → Level 4

* SSH: ssh -o PubkeyAuthentication=no -p2220 bandit3@bandit.labs.overthewire.org

* Solución: 

```bash
$ cd inhere/
$ ls -la
$ cat .hidden
pIwrPrtPN36QITSp3EQaw936yaFoFgAB
```

## Bandit Level 4 → Level 5

* SSH:  ssh -o PubkeyAuthentication=no -p2220 bandit4@bandit.labs.overthewire.org
* Solución: 

```bash
$ cd inhere/
$ ls -la
$ file /home/bandit4/inhere/-*
$ cat /home/bandit4/inhere/-file07
koReBOKuIDDepwhWk7jZC0RTdopnAYKh
```

## Bandit Level 5 → Level 6

* SSH: ssh -o PubkeyAuthentication=no -p2220 bandit5@bandit.labs.overthewire.org
* Solución: 

```bash
$ find . -size 1033c ! -executable
$ cat ./inhere/maybehere07/.file2
DXjZPULLxYr17uwoI01bNLQbtFemEgo7
```

## Bandit Level 6 → Level 7

* SSH: ssh -o PubkeyAuthentication=no -p2220 bandit6@bandit.labs.overthewire.org
* Solución: 

```bash
$ find / -size 33c -group bandit6 -user bandit7 2>/dev/null
$ ls -l /var/lib/dpkg/info/bandit7.password
$ cat /var/lib/dpkg/info/bandit7.password
HKBPTKQnIay4Fw76bEy8PVxKEDQRKTzs
```

## Bandit Level 7 → Level 8

* SSH: ssh -o PubkeyAuthentication=no -p2220 bandit7@bandit.labs.overthewire.org
* Solución: 

```bash
$ grep 'millionth' data.txt
millionth	cvX2JJa4CFALtqS87jk27qwqGhBM9plV
```

## Bandit Level 8 → Level 9

* SSH: ssh -o PubkeyAuthentication=no -p2220 bandit8@bandit.labs.overthewire.org
* Solución: 

```bash
$ cat data.txt | sort | uniq -u
UsvVyFSfZZWbi6wgC7dAFyFuR6jQQUhR
```

## Bandit Level 9 → Level 10

* SSH: ssh -o PubkeyAuthentication=no -p2220 bandit9@bandit.labs.overthewire.org
* Solución: 

```bash
$ strings data.txt | grep '='
========== truKLdjsbJ5g7yyJ2X2R0o3a5HQJFuLk
```

## Bandit Level 10 → Level 11

* SSH: ssh -o PubkeyAuthentication=no -p2220 bandit10@bandit.labs.overthewire.org
* Solución:

```bash
$ cat data.txt
$ base64 -d data.txt 
The password is IFukwKGsFW8MOq3IRFqrxE1hxTNEbUPR
```

## Bandit Level 11 → Level 12

* SSH: ssh -o PubkeyAuthentication=no -p2220 bandit11@bandit.labs.overthewire.org
* Solución: 

```bash
$ cat data.txt | tr a-zA-Z n-za-mN-ZA-M
The password is 5Te8Y4drgCRfCx8ugdwuEX8KFC6k2EUu
```

## Bandit Level 12 → Level 13

* SSH: ssh -o PubkeyAuthentication=no -p2220 bandit12@bandit.labs.overthewire.org
* Solución: 

```bash
$ mkdir /tmp/fcch
$ cp data.txt /tmp/fcch/data.raw
$ cd /tmp/fcch/
$ xxd -r data.raw > data1
## $ file nombre_datos
## $ gzip -cd data1 > data2
## $ bzip2 -d data2 output_file: data2.out
## $ tar -xvf data*
The password is 8ZjyCRiBWFYkneahHwxCv3wb2a1ORpYL
```

## Bandit Level 13 → Level 14

* SSH: ssh -o PubkeyAuthentication=no -p2220 bandit13@bandit.labs.overthewire.org
* Solución:

```bash
$ cat sshkey.private
$ ssh -i sshkey.private bandit14@localhost 
$ cat /etc/bandit_pass/bandit14
4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e
```

## Bandit Level 14 → Level 15

* SSH: ssh -i sshkey.private bandit14@localhost
* Solución: 

```bash
$ cat /etc/bandit_pass/bandit14
$ telnet localhost 30000
BfMYroe26WYalil77FoDi9qh59eK5xNr
```

## Bandit Level 15 → Level 16

* SSH: ssh -o PubkeyAuthentication=no -p2220 bandit15@bandit.labs.overthewire.org
* Solución:

```bash
$ echo 'BfMYroe26WYalil77FoDi9qh59eK5xNr' | openssl s_client -quiet -connect localhost:30001
cluFn7wTiGryunymYOu4RcffSxQluehd
```

## Bandit Level 16 → Level 17

* SSH: ssh -o PubkeyAuthentication=no -p2220 bandit16@bandit.labs.overthewire.org
* Solución:

```bash
$ nmap -sT localhost -p 31000-32000
$ echo fcch | nc localhost 31046
## Lo mismo
$ echo fcch | nc localhost 31518
## Nada
$ echo fcch | nc localhost 31691
## Lo mismo
$ echo fcch | nc localhost 31790
## Nada
$ echo fcch | nc localhost 31960
## Lo mismo
$ echo cluFn7wTiGryunymYOu4RcffSxQluehd | openssl s_client -quiet -connect localhost:31518
cluFn7wTiGryunymYOu4RcffSxQluehd ## Se descarta
$ echo cluFn7wTiGryunymYOu4RcffSxQluehd | openssl s_client -quiet -connect localhost:31790
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
imZzeyGC0gtZPGujUSxiJSWI/oTqexh+cAMTSMlOJf7+BrJObArnxd9Y7YT2bRPQ
Ja6Lzb558YW3FZl87ORiO+rW4LCDCNd2lUvLE/GL2GWyuKN0K5iCd5TbtJzEkQTu
DSt2mcNn4rhAL+JFr56o4T6z8WWAW18BR6yGrMq7Q/kALHYW3OekePQAzL0VUYbW
JGTi65CxbCnzc/w4+mqQyvmzpWtMAzJTzAzQxNbkR2MBGySxDLrjg0LWN6sK7wNX
x0YVztz/zbIkPjfkU1jHS+9EbVNj+D1XFOJuaQIDAQABAoIBABagpxpM1aoLWfvD
KHcj10nqcoBc4oE11aFYQwik7xfW+24pRNuDE6SFthOar69jp5RlLwD1NhPx3iBl
J9nOM8OJ0VToum43UOS8YxF8WwhXriYGnc1sskbwpXOUDc9uX4+UESzH22P29ovd
d8WErY0gPxun8pbJLmxkAtWNhpMvfe0050vk9TL5wqbu9AlbssgTcCXkMQnPw9nC
YNN6DDP2lbcBrvgT9YCNL6C+ZKufD52yOQ9qOkwFTEQpjtF4uNtJom+asvlpmS8A
vLY9r60wYSvmZhNqBUrj7lyCtXMIu1kkd4w7F77k+DjHoAXyxcUp1DGL51sOmama
+TOWWgECgYEA8JtPxP0GRJ+IQkX262jM3dEIkza8ky5moIwUqYdsx0NxHgRRhORT
8c8hAuRBb2G82so8vUHk/fur85OEfc9TncnCY2crpoqsghifKLxrLgtT+qDpfZnx
SatLdt8GfQ85yA7hnWWJ2MxF3NaeSDm75Lsm+tBbAiyc9P2jGRNtMSkCgYEAypHd
HCctNi/FwjulhttFx/rHYKhLidZDFYeiE/v45bN4yFm8x7R/b0iE7KaszX+Exdvt
SghaTdcG0Knyw1bpJVyusavPzpaJMjdJ6tcFhVAbAjm7enCIvGCSx+X3l5SiWg0A
R57hJglezIiVjv3aGwHwvlZvtszK6zV6oXFAu0ECgYAbjo46T4hyP5tJi93V5HDi
Ttiek7xRVxUl+iU7rWkGAXFpMLFteQEsRr7PJ/lemmEY5eTDAFMLy9FL2m9oQWCg
R8VdwSk8r9FGLS+9aKcV5PI/WEKlwgXinB3OhYimtiG2Cg5JCqIZFHxD6MjEGOiu
L8ktHMPvodBwNsSBULpG0QKBgBAplTfC1HOnWiMGOU3KPwYWt0O6CdTkmJOmL8Ni
blh9elyZ9FsGxsgtRBXRsqXuz7wtsQAgLHxbdLq/ZJQ7YfzOKU4ZxEnabvXnvWkU
YOdjHdSOoKvDQNWu6ucyLRAWFuISeXw9a/9p7ftpxm0TSgyvmfLF2MIAEwyzRqaM
77pBAoGAMmjmIJdjp+Ez8duyn3ieo36yrttF5NSsJLAbxFpdlc1gvtGCWW+9Cq0b
dxviW8+TFVEBl1O4f7HVm6EpTscdDxU+bCXWkfjuRb7Dy9GOtt9JPsX8MBTakzh3
vBgsyi/sN3RqRBcGU40fOoZyfAMT8s1m/uYv52O6IgeuZ/ujbjY=
-----END RSA PRIVATE KEY-----
$ mkdir /tmp/fcch-16
$ cd /tmp/fcch-16
$ vim sshkey.private ## Paste ssh key
$ chmod 600 sshkey.private
$ ssh -i sshkey.private bandit17@localhost
## Como se obtuvo la clave ssh privada para el nivel 17 entonces se pasa al reto 18
```

## Bandit Level 17 → Level 18

* SSH: ssh -i sshkey.private bandit17@localhost
* Solución:

```bash
$ diff passwords.old passwords.new
$ grep kfBf3eYk5BPBRzwjqutbbfE887SVc5Yd passwords.new
kfBf3eYk5BPBRzwjqutbbfE887SVc5Yd
```

## Bandit Level 18 → Level 19

* SSH: ssh -o PubkeyAuthentication=no -p2220 bandit18@bandit.labs.overthewire.org "cat /home/bandit18/readme"
* Solución: 

```bash
$ ssh -o PubkeyAuthentication=no -p2220 bandit18@bandit.labs.overthewire.org "cat /home/bandit18/readme"
IueksS7Ubh8G3DCwVzrTd8rAVOwq3M5x
```

## Bandit Level 19 → Level 20

* SSH: ssh -o PubkeyAuthentication=no -p2220 bandit19@bandit.labs.overthewire.org
* Solución:

```bash
$ ls -l 
$ file bandit20-do
$ ./bandit20-do
$ ./bandit20-do --help
$ find / -user bandit20 2>/dev/null
$ cat /etc/dpkg/.info20.txt
$ ./bandit20-do cat /etc/dpkg//.info20.txt
$ cat /etc/bandit_pass/bandit20
$ ./bandit20-do cat /etc/bandit_pass/bandit20
GbKksEFF4yrVs6il55v6gwY5aVje5f0j
```

## Bandit Level 20 → Level 21

* SSH: ssh -o PubkeyAuthentication=no -p2220 bandit20@bandit.labs.overthewire.org
* Solución: 

```bash
$ nc -l -p 4321
$ nc localhost 4321
$ echo "GbKksEFF4yrVs6il55v6gwY5aVje5f0j" | nc -l -p 4321 &
$ ./suconnect 4321
gE269g2h3mw3pwgrj0Ha9Uoqen1c9DGr
```

## Bandit Level 21 → Level 22

* SSH: ssh -o PubkeyAuthentication=no -p2220 bandit21@bandit.labs.overthewire.org
* Solución: 

```bash
$ cd /etc/cron.d/
$ ls -l
$ cat cronjob_bandit22
$ /usr/bin/cronjob_bandit22.sh
$ cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
Yk7owGAcWjwMVRwrTesJEwB7WVOiILLI
```

## Bandit Level 22 → Level 23

* SSH: ssh -o PubkeyAuthentication=no -p2220 bandit22@bandit.labs.overthewire.org
* Solución:

```bash
$ cd /etc/cron.d/
$ ls -l 
$ cat cronjob_bandit23
$ cat /usr/bin/cronjob_bandit23.sh
-- #!/bin/bash
-- myname=$(whoami)
-- mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)
-- echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"
-- cat /etc/bandit_pass/$myname > /tmp/$mytarget
$ /usr/bin/cronjob_bandit23.sh
## whoami = bandit22
$ myname=bandit23
$ mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)
$ echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"
$ cat /etc/bandit_pass/$myname > /tmp/$mytarget
$ cat /tmp/8ca319486bfbbc3663ea0fbe81326349
jc1udXuA1tiHqjIsL8yaapX5XIAI6i0n
```

## Bandit Level 23 → Level 24

* SSH: ssh -o PubkeyAuthentication=no -p2220 bandit23@bandit.labs.overthewire.org
* Solución:

```bash
$ cd /etc/cron.d/
$ ls -l 
$ cat cronjob_bandit24
$ cat /usr/bin/cronjob_bandit24.sh
$ mkdir /tmp/fcchx
$ cd /tmp/fcchx
$ touch getx.sh
$ chmod 777 getx.sh
$ ls -la getx.sh
$ vim getx.sh
-- #!/bin/bash
-- cat /etc/bandit_pass/bandit24 > /tmp/fcchx/password
$ touch password
$ chmod 666 password
$ ls -la password
$ cp getx.sh /var/spool/bandit24/
## Wait 5 sec.
$ cat password
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ
```

## Bandit Level 24 → Level 25

* SSH: ssh -o PubkeyAuthentication=no -p2220 bandit24@bandit.labs.overthewire.org
* Solución: 

```bash
$ cd /tmp
$ mkdir fcch-pass25; cd fcch-pass25; touch genpass.sh
$ vim genpass.sh
-- #!/bin/bash
-- pass=UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ
-- for i in {0000..9999} 
-- do
--    echo $pass $i >> pass25.txt
-- done
$ cat pass25,txt | nc localhost 30001
uNG9O58gUE7snukf3bvZ0rxhtnjzSGzG
```

## Bandit Level 25 → Level 26

* SSH: ssh -o PubkeyAuthentication=no -p2220 bandit25@bandit.labs.overthewire.org
* Solución: 

```bash
$ file bandit26.sshkey
$ ssh -i bandit26.sshkey bandit26@localhost
$ cat /etc/passwd
$ cat /etc/passwd | grep bandit26
$ cat /usr/bin/showtext
-- #!/bin/sh
-- export TERM=linux
-- more ~/text.txt
-- exit 0
# Por el comando "more" en el script se debe redimensionar la ventana de la terminal a un tamaño pequeño.
$ ssh -i bandit26.sshkey bandit26@localhost
# -- More -- con esta pantalla podemos ingresar a la pantalla de ayuda del comando "More" tecleando "h", luego podemos iniciar el editor vi tecleando la tecla "v" (/usr/bin/vi), dentro del editor tecleamos ":set shell=/bin/bash" y aun dentro de vi ":shell".
$ cat /etc/bandit_pass/bandit26
5czgV9L3Xx8JPOyRbXh6lQbmIOWvPT6Z
```

## Bandit Level 26 → Level 27

* SSH: ssh -i bandit26.sshkey bandit26@localhost
* Solución:

```bash
$ ls -la
$ file bandit27-do
$ cat text.txt
$ ./bandit27-do id
$ ./bandit27-do whoami
$ echo 'cat /etc/bandit_pass/bandit27' > /tmp/getpass27.sh
$ chmod a+x /tmp/getpass27.sh
$ ./bandit27-do /tmp/getpass27.sh
3ba3118a22e93127a4ed485be72ef5ea
```

## Bandit Level 27 → Level 28

* SSH: ssh -o PubkeyAuthentication=no -p2220 bandit27@bandit.labs.overthewire.org
* Solución: 

```bash
$ mkdir /tmp/fcch-git
$ cd /tmp/fcch-git
$ git clone ssh://bandit27-git@localhost/home/bandit27-git/repo
$ cat repo/README
The password to the next level is: 0ef186ac70e04ea33b4c1853d2526fa2
```

## Bandit Level 28 → Level 29

* SSH: ssh -o PubkeyAuthentication=no -p2220 bandit28@bandit.labs.overthewire.org
* Solución: 

```bash
$ mkdir /tmp/fcch28git
$ cd /tmp/fcch28git
$ git clone ssh://bandit28-git@localhost/home/bandit28-git/repo
$ cd repo
$ ls -la 
$ ls -la .git
$ git log
$ git diff b67405defc6ef44210c53345fc953e6a21338cc7 186a1038cc54d1358d42d468cdc8e3cc28a93fcb
$ git diff b67405defc6ef44210c53345fc953e6a21338cc7 073c27c130e6ee407e12faad1dd3848a110c4f95
$ git diff 186a1038cc54d1358d42d468cdc8e3cc28a93fcb 073c27c130e6ee407e12faad1dd3848a110c4f95
bbc96594b4e001778eee9975372716b2
```

## Bandit Level 29 → Level 30

* SSH: ssh -o PubkeyAuthentication=no -p2220 bandit29@bandit.labs.overthewire.org
* Solución: 

```bash
$ mkdir /tmp/fcch29git
$ cd /tmp/fcch29git
$ cat README.md
$ git branch
$ git branch -a
$ git checkout dev
$ git log 
$ git diff 33ce2e95d9c5d6fb0a40e5ee9a2926903646b4e3 a8af722fccd4206fc3780bd3ede35b2c03886d9b
5b90576bedb2cc04c86a9e924ce42faf
```

## Bandit Level 30 → Level 31

* SSH: ssh -o PubkeyAuthentication=no -p2220 bandit30@bandit.labs.overthewire.org
* Solución: 

```bash
$ git clone ssh://bandit30-git@localhost/home/bandit30-git/repo
$ cd repo
$ cat README.md
$ git branch -a
$ git show-branch --all
$ git log
$ cat .git/packed-refs
$ git show-ref --tags -d
$ git show --name-only secret
47e603bb428404d265f59c42920d81e5
```

## Bandit Level 31 → Level 32

* SSH: ssh -o PubkeyAuthentication=no -p2220 bandit31@bandit.labs.overthewire.org
* Solución: 

```bash
$ mkdir /tmp/fcch31git
$ cd /tmp/fcch31git
$ git clone ssh://bandit31-git@localhost/home/bandit31-git/repo
$ cd repo
$ ls -la
$ cat README.md
$ touch key.txt
$ vim key.txt
-- May I come in?
$ cat .gitignore
-- *.txt
$ git add	-f key.txt
$ git commit -m 'add key'
$ git push origin master
-- remote: Well done! Here is the password for the next level:
-- remote: 56a9bf19c63d650ce78e6ec0354ee45e
```

## Bandit Level 32 → Level 33

* SSH: ssh -o PubkeyAuthentication=no -p2220 bandit32@bandit.labs.overthewire.org
* Solución: 

```bash
$ ls
$ clear
>> $0
$ pwd
$ ls -la 
$ file uppershell
$ cat uppershell
$ cat /etc/bandit_pass/bandit33
c9c3199ddf4121b10cf581a98d51caee
```

## Bandit Level 33 → Level 34

* At this moment, level 34 does not exist yet.

## Referencias

- [**Over the wire, Bandit**](https://overthewire.org/wargames/bandit/)