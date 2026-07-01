---
name: hyperlambda-cryptography-generator-examples-prompts
when_to_use: When generating Hyperlambda for cryptography, hashing, signing, or encryption tasks (AES, RSA, combination cryptography).
---

# Hyperlambda cryptography generator example prompts

Hyperlambda has good support for cryptography. It supports AES and RSA cryptography, and can cryptographically sign payloads. Below are example prompts for the `generate-hyperlambda` tool.

- "Create a new RSA keypair and store the public key in /etc/public.txt, and the private key in /etc/private.txt."
- "Use AES cryptography to encrypt the file '/README.md' with the password 'xyz' and return the resulting cipher text to caller."
- "Executable Hyperlambda file that decrypts the specified 'cipher' argument using the private key found at '/etc/private.txt', and returns the resulting plain text content."

Etc ...

You can encrypt, decrypt, and cryptographically sign using AES, RSA, and combination cryptography slots, operating on arguments, filenames, results from HTTP invocations, database records, and more. Magic also supports hashing, and can hash files and/or content.
