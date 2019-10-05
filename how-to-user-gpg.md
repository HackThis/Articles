Title:      How to use GPG
Topic:      privacy
Author:     flabbyrabbit
Date:       01/05/2019
Course:     

## What is GPG?
GNU Privacy Guard (GnuPG), also known as GPG, is a tool for secure communication that was created by Werner Koch as Free Software under the GNU Project. GnuPG follows the OpenPGP protocol, which defines and standardizes all the necessary components involved in sending encrypted messages–signatures, private keys, and public key certificates. This piece of free software is notably used by journalists around the world to ensure that their sensitive email communication is kept secure and private.

GPG uses a combination of symmetric-key cryptography and public-key cryptography. Public key cryptography is likely already familiar to you since it is the recommended way to authenticate when SSHing in to your Linode. Public-key cryptography uses a key-pair system where any single user has a private and public key pair. The public key can be shared with anyone, while the private key should be protected and secret to maintain the integrity of the system.

This asymmetric cryptographic system is ideal for secure communication, because all it requires is that the sender of the message have a copy of the receiver’s public key before encrypting and sending the message. The recipient can then use their private key to decrypt the message. This means anyone can send you a secure message if they have a copy of your public key.

This guide shows how to create your own keypair, distribute the public key to a receiver, and encrypt and decrypt a message on Ubuntu 16.04 and 18.04.

## Getting started
First things first, you need to install GPG. We are going to be focused 

```lanuage-bash
sudo apt update
sudo apt install gnupg
```

```lanuage-bash
gpg --full-generate-key
```

Prompts will ask details about the key you are generating. Our recommendations are:
* Select (1) RSA and RSA (default) for the type of key
* Enter 4096 for the key size
* Enter 1y for the expiration date. You may choose a different expiration duration depending on your own situation
* Enter a relevent name, email and comment. These will be used later to identify the keypair
* Give your key a good passphrase. This passphrase is the only thing that protects your private key if someone else gets hold of it.

Congratulations you should have a smoking hot new GPG key. You can list all the keys in your public keyring by typing:

```language-bash
gpg --list-keys
```

You should be able to see your newly created user id in the list. This may be the only key in your keyring but as you add other peoples keys the list will grow.

## Encrypting and decrypting messages
```language-bash
gpg --armor --output file.asc --encrypt --recipient <email> <file>
```

```language-bash
gpg --output file.txt --decrypt file.asc
```

If you have multiple private keys on your machine it will automatically determine the correct key and prompt you for the passphrase. Enter the passphrase correctly and a new file `file.txt` will be created containing the original content.

## Message verification
GPG can digitally sign a document to verify its authenticity and integrity. A recipient can inspect the signature and confirm that it does come from the expected sender and that the content has been unmodified.

```language-bash
gpg --armor --output signedfile.asc --sign <file>
```

Once the recipient has recieved the file they can verify the signature and extra the content:

```language-bash
gpg --output file.txt --decrypt signedfile.asc
```

If the signature is valid it will output details about the signature including when and by whom it was signed.

Notice that we did not specify a recipient when signing the document therefore the content is not encrypted and anyone can view the content.

## Exchanging public keys
### Export your public key
The simplest way to share your key is to export it as an ASCII armoured file and send it to whomever you want to recieve messages from. This file can be emailed or shared online.

```language-bash
gpg --armor --output public.asc --export <email>
```

### Import and validate public key
You can then import the public key into your keyring by:

```language-bash
gpg --import public.asc
```

### Submit your public key to a key server
A more convenient way is to upload it to a public keyserver. Key servers are...

MIT hosts a popular keyserver: https://pgp.mit.edu

1. Find the key ID for the public key you want to upload

```language--bash
gpg --list-keys <email>
```
You will see a long HEX value in the pub row, this is the ID for this public key.

2. Upload your key to a keyserver:

```language--bash
gpg --send-keys <key id>
```

### Retrieve a public key
1. Find the key ID for the public key you want to download

```language--bash
gpg --search <name>
```

2. Download key

```language--bash
gpg --recv-key <key id>
```

When adding a new key to your public keystore it is of little use until you verify it is the correct key TODO


## Revoking keys
To delete a key from your keychain you can do:

```
gpg --delete-key <key id>
```

If this a key you hold the private key to you will first need to delete the screts for the key:

```
gpg --delete-secret-key <key id>
```