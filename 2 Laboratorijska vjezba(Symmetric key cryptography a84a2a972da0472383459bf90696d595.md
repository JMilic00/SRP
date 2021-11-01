# 2. Laboratorijska vjezba(Symmetric key cryptography - a crypto challenge)

U ovoj vježbi odrađivali smo kriptiranje i dekriptiranje poruka s enkripcijom simetričnog ključa.

**Alat**

Za pripremu crypto izazova korištena je python biblioteka *`cryptogtaphy`.`Plaintext`* koji student treba otkriti enkriptiran je korištenjem *high-level* sustava za simetričnu enkripciju iz navedene biblioteke - Fernet.

**Fernet**

Fernet koristi sljedeće *low-level* kriptografske mehanizme:

- AES šifru sa 128 bitnim ključem
- CBC enkripcijski način rada
- HMAC sa 256 bitnim ključem za zaštitu integriteta poruka
- Timestamp za osiguravanje svježine (*freshness*) poruka

---

**Fernet code**

*Pokretanje virtual python okruzenja te instalacija* 

```bash
python -m venv jmilic00

pip install cryptography

from cryptography.fernet import Fernet
```

*funkcija generate_key generira ključ koji može dekriptirati nama željene podatke*

```bash
key = Fernet.generate_key()
F = Fernet(key)
```

*enkriptiranu poruku moramo spremit u neku varijablu u ovom slucaju zove se `ciphertext` te pomocu kljuca koji se nalazi u objektu F mozemo dekriptirati neku poruku tj. podatak*

```bash
//komanda s kojom enkriptiramo poruku
ciphertext = F.encrypt(b"some word")
//komanda s kojom dekriptiramo poruku
F.decrypt(ciphertext)
```
*Izlazak iz python shella*

```bash
exit()
```

---

**Generiranje hasha**

Hash generiramo pomoću Secure Hash Algorithm 256.

```bash
from cryptography.hazmat.primitives import hashes

def hash(input):
    if not isinstance(input, bytes):
        input = input.encode()

    digest = hashes.Hash(hashes.SHA256())
    digest.update(input)
    hash = digest.finalize()

    return hash.hex()

if __name__=="__main__":
    h = hash('milic_jakov')
    print(h)
```

**Brute-force attack code 20 bita(nedovrsen)**

Entropija od 20 bita probijena je za otprilike minutu,dok za 22 puno duže.

```bash
import base64
from cryptography.fernet import Fernet

def brute_force():
				ctr = 0

				filename = "ime datoteke"
				    with open(filename, "rb") as file:
				        ciphertext = file.read()
				
				while True:
				    key_bytes = ctr.to_bytes(32, "big")
				    key = base64.urlsafe_b64encode(key_bytes)
						
				    try:
				         plaintext = Fernet(key).decrypt(ciphertext)
				         print(key, plaintext)
				         break
				
				    except Exception:
				          pass
				
				    ctr += 1

if __name__=="__main__":
    brute_force()
```

**Brute-force attack code 22 bita(nedovrsen)**

```bash
from multiprocessing import Pool

def brute_force(filename, chunk_start_index, chunk_size):
    ctr = 0

    filename = "ime datoteke"
				    with open(filename, "rb") as file:
				        ciphertext = file.read()

				while True:
				    key_bytes = ctr.to_bytes(32, "big")
				    key = base64.urlsafe_b64encode(key_bytes)

				    try:
				         plaintext = Fernet(key).decrypt(ciphertext)
				         print(key, plaintext)
				         break

				    except Exception:
				          pass

				    ctr += 1

def parallelize_attack(filename, key_entropy):

    total_keys = 2**key_entropy
    chunk_size = int(total_keys/os.cpu_count())

    with Pool() as pool:
        def key_found_event(event):
            print("Terminating the pool ...")
            pool.terminate()

        for chunk_start_index in range(0, total_keys, chunk_size):
            pool.apply_async(
                brute_force,
                (
                    filename,
                    chunk_start_index,
                    chunk_size,
                ),
                callback=key_found_event
            )

        pool.close()
        pool.join()
```
