# 6.Laboratorijska vježba(Linux permissions and ACLs)

Kontrola pristupa je sustav koji omogućava kontorlu kome dajemo dopuštenja ili zabranu za obavljanje određenih radnji nekom korisniku.

Kontrolu pristupa možemo realizirati na više načina.

Matrica kontorle pristupa kao i kod svih kodova ima jako veliku efektivnost ali imamo problem ako ne iskoristimo cijelu matricu te stoga postoji neiskorištena memorija.

Ovaj problem se riješava listama vezanim listama te se štedi memorija(Access Control Lists).Svaki korisnik pokazuje jedan na drugog.

**1.dio**

Dodati korisnika u sustav  te napraviti datoteku security.txt.

```bash
student@DESKTOP-7Q0BASR:/mnt/c/Users/A507$ id
uid=1000(student) gid=1000(student) groups=1000(student),4(adm),20(dialout),24(cdrom),25(floppy),27(sudo),29(audio),30(dip),44(video),46(plugdev),114(netdev),1001(docker)

student@DESKTOP-7Q0BASR:/mnt/c/Users/A507$ adduser alice
adduser: Only root may add a user or group to the system.

student@DESKTOP-7Q0BASR:/mnt/c/Users/A507$ sudo adduser alice
[sudo] password for student:
Adding user `alice' ...
Adding new group `alice' (1002) ...
Adding new user `alice' (1001) with group `alice' ...
Creating home directory `/home/alice' ...
Copying files from `/etc/skel' ...
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
Changing the user information for alice
Enter the new value, or press ENTER for the default
        Full Name []:
        Room Number []:
        Work Phone []:
        Home Phone []:
        Other []:
Is the information correct? [Y/n] y

student@DESKTOP-7Q0BASR:/mnt/c/Users/A507$ su - alice
Password:
alice@DESKTOP-7Q0BASR:~$ id
uid=1003(alice) gid=1003(alice) groups=1003(alice)
alice@DESKTOP-7Q0BASR:~$
```

Zatim smo dodali Boba(na isti način kao i alice) kojeg ćemo koristiti u sljedećim dijelovima vježbe.

Zatim smo kreirali security.txt datoteku.

```bash
alice@DESKTOP-7Q0BASR:~$ mkdir SRP
alice@DESKTOP-7Q0BASR:~$ cd SRP
alice@DESKTOP-7Q0BASR:~/SRP$  echo "hello world" > security.txt
alice@DESKTOP-7Q0BASR:~/SRP$ ll
total 12
drwxrwxr-x 2 alice alice 4096 Jan 10 13:18 ./
drwxr-xr-x 5 alice alice 4096 Jan 10 13:17 ../
-rw-rw-r-- 1 alice alice   12 Jan 10 13:18 security.txt
alice@DESKTOP-7Q0BASR:~/SRP$ ls -l
total 4
-rw-rw-r-- 1 alice alice 12 Jan 10 13:18 security.txt
alice@DESKTOP-7Q0BASR:~/SRP$ cd ..
alice@DESKTOP-7Q0BASR:~$ ls -l
total 4
drwxrwxr-x 2 alice alice 4096 Jan 10 13:18 SRP
alice@DESKTOP-7Q0BASR:~$
```

provjeravamo pristup te se uvjeravamo da alice ne moze vidjeti jer nema dopuštenje “read” tj. r.

```bash
alice@DESKTOP-7Q0BASR:~/SRP$ chmod u-r security.txt
alice@DESKTOP-7Q0BASR:~/SRP$ cat security.txt
cat: security.txt: Permission denied
```

a sljedećim kodom joj dajemo dopuštenje za čitanje.

```bash
alice@DESKTOP-7Q0BASR:~/SRP$ chmod u+r security.txt
alice@DESKTOP-7Q0BASR:~/SRP$ cat security.txt
hello world
```

da bi imali mogućnost čitanja nekog direktorija moramo napraviti to pomoću naredbe chmod -x.

![Untitled](6%20Laboratorijska%20vjez%CC%8Cba(Linux%20permissions%20and%20ACL%20af3849a2e627497c803dc6c431c509d5/Untitled.png)

Naredbom usermode mozemo dati ista prava 

![Untitled](6%20Laboratorijska%20vjez%CC%8Cba(Linux%20permissions%20and%20ACL%20af3849a2e627497c803dc6c431c509d5/Untitled%201.png)

![Untitled](6%20Laboratorijska%20vjez%CC%8Cba(Linux%20permissions%20and%20ACL%20af3849a2e627497c803dc6c431c509d5/Untitled%202.png)

**2.dio**

Sljedeći dio vježbe odnosi se na kontrolu pristupa korištenjem naredbi getfacl i setfalc. Tim naredbama pokušavamo dati prava Bob-u kojeg naknadno grupe kako bi imali lakši pristup datotekama.

```bash
bob@DESKTOP-7Q0BASR:~$ cat /etc/shadow
cat: /etc/shadow: Permission denied
bob@DESKTOP-7Q0BASR:~$ ls -l
total 0
bob@DESKTOP-7Q0BASR:~$ cd etc
-su: cd: etc: No such file or directory
bob@DESKTOP-7Q0BASR:~$ getfacl /etc/shadow
getfacl: Removing leading '/' from absolute path names
# file: etc/shadow
# owner: root
# group: shadow
user::rw-
group::r--
other::---

student@DESKTOP-7Q0BASR:~$ python3 lab6.py
Real (R), effective (E) and saved (S) UIDs: (1000, 1000, 1000)

Traceback (most recent call last):
  File "lab6.py", line 5, in <module>
    with open('/home/alice/SRP/security.txt', 'r') as f:
PermissionError: [Errno 13] Permission denied: '/home/alice/SRP/security.txt'
```

grupa korisnika. To je uloga korisnika u sustavu koja se jednostavno može maknuti ili staviti bilo kojem korisniku. Prava i kontrola koju taj korisnik tada dobije vezana je specifično uz njegovu ulogu, a ne njega samoga.

naposlijetku boba mičemo iz svih grupa.

![Untitled](6%20Laboratorijska%20vjez%CC%8Cba(Linux%20permissions%20and%20ACL%20af3849a2e627497c803dc6c431c509d5/Untitled%203.png)

**3.dio**

Posljednji dio vježbe odnosi se na promjenu zaporke korisnika pomoću naredbe pawssd.Passwd ima na sebi veću težinu prava,passwd svoju težinu prenese na proces korisnika pa onda sam korisnik čak i ako nije administrator sustava, može promijeniti vlastitu šifru.

![Untitled](6%20Laboratorijska%20vjez%CC%8Cba(Linux%20permissions%20and%20ACL%20af3849a2e627497c803dc6c431c509d5/Untitled%204.png)

![Untitled](6%20Laboratorijska%20vjez%CC%8Cba(Linux%20permissions%20and%20ACL%20af3849a2e627497c803dc6c431c509d5/Untitled%205.png)

**The End**