## Product Choice
Telegram;
https://web.telegram.org
Telegram — это популярный, безопасный и быстрый кроссплатформенный мессенджер, основанный в 2013 году.
## Main components
![Telegram Component Diagram](./diagrams/src/telegram/component-diagram/Component%20Diagram.svg)

Rendered image (click to open)
![Telegram Component Diagram](./diagrams/out/telegram/component-diagram/Component%20Diagram.svg)

Select at least 5 components of the product from the component diagram.
1. Bot API Server
The Bot API is an HTTP-based interface created for developers keen on building bots for Telegram.

2. MTProto Gateway (DC Entry)
An MTProto Gateway or Data Center (DC) Entry in Telegram is the initial connection point, or proxy server, used to establish a secure link between a client application and Telegram's data center infrastructure.

3. Secret Chat Relay
Telegram Secret Chat is a highly secure, device-specific messaging mode featuring end-to-end encryption, self-destruct timers, and no server-side storage.

4. Media & File Service
The new version of Telegram allows you to play media from services like YouTube, Vimeo or SoundCloud directly from Telegram

5. Auth & Session Service
Telegram's Authorization and Session Service is based on the proprietary MTProto mobile protocol

# Data Flow


[!Telegram Sequence Diagram](./diagrams/out/telegram/sequence-diagram/Sequence%20Diagram.svg)



[!Telegram Sequence Diagram](./diagrams/src/telegram/sequence-diagram.puml)


I’ll pick **Group 2: Checkout (Reservation & Payment)** — the big light-green block in the middle.

---
## Decryption algorithm
Principles of encryption in Telegram
MTProto uses several cryptographic methods to provide security:
AES-256 is a symmetric data encryption algorithm using 256-bit keys.
RSA-2048 is an asymmetric encryption algorithm for protection of data during transmission.
Diffie-Hellman is an encryption method where two parties receive a shared secret key which is then used to encrypt messages.
## Assumptions
I assume that MTProto supports protection against man-in-the-middle (MITM) attacks with the use of authentication and verification of keys.
I assume the cloud storage system implements deduplication to optimize storage costs for shared media files.
## Open questions
With what TON connected?
What does "the open network" means?
