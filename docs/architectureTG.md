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


[!Telegram Sequence Diagram](./diagrams/out/telegram/architecture-sequence/Sequence%20Diagram.svg)



[!Telegram Sequence Diagram](./diagrams/src/telegram/architecture-sequence.puml)


I’ll pick **Group 2: Checkout (Reservation & Payment)** — the big light-green block in the middle.

---
## Deployment
Embed the product's Deployment Diagram.svg.
Provide a link to the PlantUML code for that deployment diagram.
Briefly describe where the components are deployed.
## Assumptions
List two or more assumptions you made while describing the architecture. Examples:

Yandex Go: "I assume the pricing service handles surge pricing calculations based on demand and supply in real-time."
Telegram: "I assume the cloud storage system implements deduplication to optimize storage costs for shared media files."
Wildberries: "I assume the Logistics & Routing service integrates with multiple delivery partners to optimize shipping costs and delivery times"
## Open questions
List two or more questions that you couldn't answer based on the openly available information. Examples:

Yandex Go: "How does the actual load balancing mechanism work between the microservices in production?"
Telegram: "How does the data flow look like in secret chats?"
Wildberries: "What specific caching strategies are used to handle high traffic during sales events?"
