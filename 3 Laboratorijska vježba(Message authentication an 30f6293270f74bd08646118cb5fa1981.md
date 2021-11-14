# 3. Laboratorijska vježba(Message authentication and integrity)

**IZAZOV 1.**

U ovoj laboratorijskoj vježbi fokusiramo se na integritet poruke,iskorištavanje MAC-a(Message authentication coda).Ovaj simetrični sustav ima ključ K koji obje strane prilikom komunikacije koriste za sigurnost.MAC algoritam dodaje zaglavlje na poruku koja ima određenu vrijednost.Potom kada poruka od pošiljatelja prođe kroz algoritam te dođe do Primatelja.ako MAC algoritam ima drugaciju vrijednost od posiljateljevog algoritma integritet je narušen u suprotnom nije.

**Code za MAC**

```bash
from cryptography.hazmat.primitives import hashes, hmac

def generate_MAC(key, message):
    if not isinstance(message, bytes):
        message = message.encode()

    h = hmac.HMAC(key, hashes.SHA256())
    h.update(message)
    signature = h.finalize()
    return signature

if __name__ == "__main__":
    key = b"ja sam onaj"
    message = "koji jesam"
    MAC = generate_MAC(key, message)

		print(MAC)
```

**Output:**

```bash
(jmilic00) C:\Users\A507\desktop\jmilic00\lab3>python .\message_integrity.py
5a5f58f9f07cfa9612df749334771b9e95fa020b5766e9c1cddbd30240b3b72c
```

**Code za MAC(tekstualna datoteka)**

```bash
from cryptography.hazmat.primitives import hashes, hmac

def generate_MAC(key, message):
    if not isinstance(message, bytes):
        message = message.encode()
    h = hmac.HMAC(key, hashes.SHA256())
    h.update(message)
    signature = h.finalize()
    return signature

if __name__ == "__main__":

    with open("doc.txt", "rb") as file:
        message = file.read()

    key = b"ja sam onaj"

    MAC = generate_MAC(key, message)

    with open("doc.sig", "wb") as file:
        file.write(MAC)

    print(MAC)
```

MAC koji je zapisan u .sig datoteci u binarnom obliku:

*Φ\épûu�è”49œμf@+'ap¼‡ö²F9Ò»¼*

**Code za provjeru MAC-a iz .sig datoteke**

```bash
from cryptography.hazmat.primitives import hashes, hmac
from cryptography.exceptions import InvalidSignature

def generate_MAC(key, message):
    if not isinstance(message, bytes):
        message = message.encode()
    h = hmac.HMAC(key, hashes.SHA256())
    h.update(message)
    signature = h.finalize()
    return signature

def verify_MAC(key, signature, message):
    if not isinstance(message, bytes):
        message = message.encode()

    h = hmac.HMAC(key, hashes.SHA256())
    h.update(message)
    try:
        h.verify(signature)
    except InvalidSignature:
        return False
    else:
        return True

if __name__ == "__main__":

    with open("doc.txt", "rb") as file:
        message = file.read()

    with open("doc.sig", "rb") as file:
        signature = file.read()

    key = b"ja sam onaj"

    provjera = verify_MAC(key, signature, mesage)

    print(provjera)

    # MAC = generate_MAC(key, content)

    # with open("doc.sig", "wb") as file:
    #     file.write(MAC)
```

**IZAZAOV 2.**

Sljedeći zadatak*(*izazov) je utvrditi točan poredak transakcija s dionicama.datoteke s dionicama nalaze se na lokalnom serveru ([http://a507-server.local/](http://a507-server.local/)) podatek smo downloadali pomocu **wget** programa.

Sam Ključ je naše ime i prezime,cilj nam je izlistati savjete kronološki

Prilikom usporebi MAC-a iz datoteka .sig i.txt su identični poruka je zadržala integritet.Ovaj dio koda provjeravamo pomoću "h.verify(signature)" jer sama usporedba MAC-ova nije dovoljna.

Naposljetku poruke kronološki poredati po timestampu.

**Code challangea**	

```bash
from cryptography.hazmat.primitives import hashes, hmac
from cryptography.exceptions import InvalidSignature

def generate_MAC(key, message):
    if not isinstance(message, bytes):
        message = message.encode()
    h = hmac.HMAC(key, hashes.SHA256())
    h.update(message)
    signature = h.finalize()
    return signature

def verify_MAC(key, signature, message):
    if not isinstance(message, bytes):
        message = message.encode()

    h = hmac.HMAC(key, hashes.SHA256())
    h.update(message)
    try:
        h.verify(signature)
    except InvalidSignature:
        return False
    else:
        return True

if __name__ == "__main__":
		#LOOP ZA ŠETANJE
    **for ctr in range(1,11):
        msg_filename = f"order_{ctr}.txt"
        sig_filename = f"order_{ctr}.sig"
        with open(msg_filename, "rb") as file:
            content = file.read()  
        with open(sig_filename, "rb") as file:
            signature = file.read() 

        key = "Milic_jakov".encode()
        is_authentic = verify_MAC(key, signature, content)
        
	print(f'Message {content.decode():>45} {"OK" if is_authentic else "NOK":<6**
```
