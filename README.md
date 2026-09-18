# Encryption Packs
You can download all the official Encryption packs for **Encryption++** here

### Encrypter++
Encryption++ is a software that allows people to create new encryption methods and share them.
The goal is not necessarily to create the safest encipher, but to educate people on how these mathmatics work
or maybe encrypt a document your mom or a friend is not allowed to view.
[Visit Encryption++](https://github.com/Morgan-Kot/Encrypt-plus)

## How to add Encryption Packs to Encrypter++
Download your prefered Encryption Pack
Then a .zip will get downloaded
From here you have a few choices:
1. If the Encryption Pack is a standalone file (single.epac) you can extract it and place it anywhere on your PC
2. You don't need to unpack the .zip you can simply place it inside the **epac-methods** folder. 
(If downloaded with the installer: C:\Users\USERNAME\AppData\Local\Encryption++)
3. Normal folder are also supported, so you can safely unpack the .zip

Then when the Encryption Pack has been added you can launch Encrypter++
When Encrypter++ has launched your Encryption Packs should be in the top-right corner.
Now you can encrypt messages, passwords etc. [Download the official Encryption-Packs](https://github.com/Morgan-Kot/Encryption-Packages/releases)

## How to edit .epac files
You can create either standalone epac or Dual epac
### Standalone .epac
Start by creating a folder (you can call it what you want)
Inside that folder create a file called single.epac
Then write your method

## Single.epac example:
```python
# Example of how a Byte Inverter looks inside .epac
NAME = "Method_Name"
DESCRIPTION = "a description about the encryption method"
CREATOR = "Your Name"
YEAR = "year it was created"
COPYRIGHT = "Copyrights?"
GITHUB_URL = "Github Url is not required, but recommended"


def encrypt(plaintext: str) -> str:
    if not plaintext:
        return ""
    data = plaintext.encode('utf-8')
    tokens = [f"{(byte ^ 0xFF):03d}" for byte in data]
    return "_".join(tokens)


def decrypt(ciphertext: str) -> str:
    if not ciphertext.strip():
        return ""
    tokens = [int(t) for t in ciphertext.strip().split("_") if t]
    out_bytes = bytearray(t ^ 0xFF for t in tokens)
    return out_bytes.decode('utf-8', errors='replace')
```
( .epac features similar syntax to python ) 
When you have written your methods, save it and exit your text editor
Then you can either drag the folder into **epac-methods** or just place it anywhere (if not placed in epac-methods you must find single.epac with the direct tool)
Now you can open Encrypter++ and find your own method.

### Dual .epac
It's the same concept as single.epac,
but here it's just seperated into 2 .epacs: encrypter.epac and decrypter.epac

## Encrypter.epac example:
```python
# Encrypter Example - This method in called STWE01
NAME = "Method Name"
DESCRIPTION = "A description about your encipher method"
CREATOR = "Your name"
YEAR = "Year Created"
COPYRIGHT = "Copyright?"
GITHUB_URL = "Github Url is not required, but recommended"


def encrypt(plaintext: str) -> str:
    if not plaintext:
        return ""
    data = plaintext.encode('utf-8')
    state = 0x8F3C
    tokens = []
    
    for i, byte in enumerate(data):
        k1 = (state + i * 13) % 256
        k2 = (state * 7 + i * 31) % 256
        k3 = (state ^ (i * 101)) % 256
        k4 = ((state >> 3) + i * 17) % 256
        
        t1 = (byte + k1) % 256
        t2 = (byte ^ k2) % 256
        t3 = (byte + k3 + i) % 256
        t4 = (byte ^ k4 ^ (i * 3)) % 256
        
        tokens.extend([f"{t1:03d}", f"{t2:03d}", f"{t3:03d}", f"{t4:03d}"])
        state = (state * 251 + byte + i * 9) % 65536

    return "_".join(tokens)
```
## Decrypter.epac example:
```python
# decryption.epac example
def decrypt(ciphertext: str) -> str:
    if not ciphertext.strip():
        return ""
    tokens = [int(t) for t in ciphertext.strip().split("_") if t]
    if len(tokens) % 4 != 0:
        raise ValueError("Invalid ciphertext structure.")

    state = 0x8F3C
    out_bytes = bytearray()
    
    for i in range(0, len(tokens), 4):
        idx = i // 4
        t1 = tokens[i]
        
        k1 = (state + idx * 13) % 256
        b1 = (t1 - k1) % 256
        
        out_bytes.append(b1)
        state = (state * 251 + b1 + idx * 9) % 65536

    return out_bytes.decode('utf-8', errors='replace')
```
These .epac have to be placed in the same folder. 
And the parent folder has to placed inside epac-methods
