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
	  # file.write(MAC)
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

Završni izazov bio je dovršiti kod za provjeru digitalng potpisa.

Koristimo javne i privatne kljuceve,s javnim kljucem denkriptiramo poruku koja je enkripitara od strane osobe s privatnim ključem.Potpis mora biti identičan potpisu sa slike.

K**od za verifikaciju i učitavanje public keya** 

```bash
from cryptography.hazmat.primitives import serialization
from cryptography.hazmat.backends import default_backend
from cryptography.hazmat.primitives.asymmetric import padding
from cryptography.hazmat.primitives import hashes
from cryptography.exceptions import InvalidSignature

def load_public_key():
    with open("public.pem", "rb") as f:
        PUBLIC_KEY = serialization.load_pem_public_key(
            f.read(),
            backend=default_backend()
        )
    return PUBLIC_KEY

def verify_signature_rsa(signature, message):
    PUBLIC_KEY = load_public_key()
    try:
        PUBLIC_KEY.verify(
            signature,
            message,
            padding.PSS(
                mgf=padding.MGF1(hashes.SHA256()),
                salt_length=padding.PSS.MAX_LENGTH
            ),
            hashes.SHA256()
        )
    except InvalidSignature:
        return False
    else:
        return True

if __name__ == "__main__":

    with open("image_1.png", "rb") as file:
        image = file.read()

    with open("image_1.sig", "rb") as file:
        sig = file.read()

    with open("image_2.png", "rb") as file:
        image2 = file.read()

    with open("image_2.sig", "rb") as file:
        sig2 = file.read()

    print(verify_signature_rsa(sig,image))
    print(verify_signature_rsa(sig2,image2))
```

Rezultat u command promptu je 'false' pa 'true'