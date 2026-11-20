# 🔐 PDF Password Cracking Lab: John the Ripper vs. Web-Based Tools

## 📌 Project Overview
This repository documents two hands-on cybersecurity lab modules focused on auditing PDF encryption strength and performing offline dictionary attacks. Using **Kali Linux** and browser-based security utilities, I extracted cryptographic hashes from password-protected PDF documents, recovered the plaintext passwords, and verified access to the target files.

> **Disclaimer:** All exercises were conducted strictly for educational and authorized training purposes using lab files provided by **Networkwalks Academy**.

---

## 🛠️ Tools & Environment
* **Operating System:** Kali Linux (Virtual Machine)
* **CLI Tools:** `pdf2john`, `John the Ripper (JTR)`
* **Web Tools:** Networkwalks Hash Calculator & Password Cracker
* **Target Files:** Encrypted PDF documents (`My Locked PDF1.pdf`, `PDF2`, `PDF3`)

---

## 🚀 Module 1: Command-Line Cracking with John the Ripper (Kali Linux)

### 1. Hash Extraction (`pdf2john`)
John the Ripper cannot crack raw `.pdf` files directly; it requires the cryptographic password hash stored in the file's metadata. Using `pdf2john`, I extracted the `$pdf$...` hash locally:

```bash
pdf2john "My Locked PDF1.pdf" > hash1.txt
cat hash1.txt
```

### 2. Offline Dictionary Attack (`john`)
Next, I ran John the Ripper against the extracted hash file using its built-in wordlist (`/usr/share/john/password.lst`):

```bash
john hash1.txt
```

* **Result:** Cracked in under 1 second (`good-luck`).
* **Verification:** Confirmed the recovered credential using:
  ```bash
  john --show hash1.txt
  ```

### 3. Batch Testing Additional Targets
Repeated the extraction and cracking pipeline across `PDF2` and `PDF3`, successfully recovering all target passwords and unlocking the flag documents.
<img width="1585" height="887" alt="My Locked PDF1" src="https://github.com/user-attachments/assets/40cbc632-8ef3-4f36-aa54-5fd49d5e757d" />

---

## 🌐 Module 2: Browser-Based Hash Extraction & Dictionary Attack

To compare local CLI workflows with lightweight browser utilities, I repeated the audit using web-based tools:

1. **Hash Extraction:** Uploaded the encrypted PDF files into the **Networkwalks Hash Calculator** to parse the file locally and extract the `$pdf$...` signature.
2. **Dictionary Attack:** Loaded the extracted hashes into the **Networkwalks Password Cracker** and executed a dictionary attack against the built-in wordlist.
3. **Verification:** Unlocked each PDF with the recovered plaintext passwords to verify document access.

<img width="1418" height="889" alt="My Locked PDF2" src="https://github.com/user-attachments/assets/71f05c1d-e2c5-4ce8-96db-a6fea72aa9bc" />

---

## 🛡️ Key Security Takeaways & Remediation
* **Strong Encryption ≠ Strong Security:** Even 128-bit PDF encryption fails immediately if protected by a predictable dictionary word.
* **Offline Attack Risk:** Once an attacker obtains an encrypted file, password hashes can be extracted and tested offline at thousands of guesses per second without triggering account lockouts.
* **Defense / Remediation:** Enforce high-entropy passwords (12+ characters combining uppercase, lowercase, numbers, and symbols) or randomly generated passphrases to defeat dictionary and rule-based attacks.
