# Vault Secure

Hide one file inside up to three pieces of media (song, picture, meme, video, document), locked with a 256-bit key that travels as a QR code and expires on a timer.

## Run it

Open `index.html` in Chrome, Edge, Firefox or Safari. It needs internet the first time to load three small libraries (QR maker, QR reader, ZIP). Files never leave your device.

For local use, serve the folder over https or `localhost` (for example `npx http-server`), because browsers only allow WebCrypto there.

## Hide

1. Drop the secret file.
2. Drop 1–3 carriers:
   - **Picture or meme:** saved as PNG. A meme gets top and bottom captions.
   - **Song:** saved as 16-bit WAV.
   - **Video:** MP4, MOV or WebM.
   - **Document:** PDF, DOCX, XLSX or PPTX.
3. Choose **Split** (all carriers needed) or **Copy** (any one opens it).
4. Pick an expiry: 24 hours, 7 days, 31 days, 3 months, 6 months, 366 days, or long-term storage of 2 to 17 years. Optionally add a nickname and a passphrase.
5. Press **Lock and hide**, then save the carriers and the QR key.

## Long-term vaults (2 to 17 years)

- From year 2, every 6 months, Vault Secure beeps and shows a check-in notice with three choices: **Renew 6 months**, **Auto-renew from now on**, or **Terminate**.
- You have 30 days to answer. After that, the rule you picked when locking applies: renew automatically, or terminate.
- **My vaults** lists every vault made on this device with its countdown, check-in dates (✓ answered, ✕ missed) and a Terminate button.
- **Calendar reminders** saves an .ics file with every check-in date and an alarm, so your phone or computer beeps even when Vault Secure is closed.
- Check-ins are recorded on the device and browser that made the vault. No keys are stored there.

## Reveal

Drop the QR image (or paste the key text), type the passphrase if one was set, drop the carriers, and press **Unlock**.

## Security design

| Part | What it uses |
|---|---|
| Encryption | AES-256-GCM, fresh random key per vault |
| Key mixing | HKDF-SHA-256; passphrase stretched with PBKDF2-SHA-256 at 600,000 rounds |
| Tokens | Encrypted file cut into numbered tokens, each sealed with HMAC-SHA-256 |
| Pictures and songs | Adaptive least-significant-bit placement: busiest pixels and loudest samples first, in a key-derived order |
| Videos and documents | Whitened data block appended (MP4 `free` box, Office package part, or file tail) |
| Expiry | Sealed into the encryption as authenticated data; the app refuses expired keys |
| Long-term check-ins | Term length and no-answer rule sealed in the key; answers kept in a device-only vault list |
| QR | Opens with “This Vault QR code is locked for a specific period.” so ordinary scanners show only that notice |

**Quantum resistance:** Vault Secure uses no public-key cryptography, the part quantum computers threaten. AES-256 keeps roughly 128-bit strength against Grover's algorithm.

**Limits:**
- Expiry is enforced by Vault Secure itself. Someone who keeps the key, the carriers and writes their own program could still decrypt after expiry, so destroy the QR when the period ends.
- Send carriers as files: chat apps and social networks re-compress media and destroy the hidden tokens.
