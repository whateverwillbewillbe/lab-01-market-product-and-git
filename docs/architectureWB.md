## Wildberries.ru
- Wildberries.ru;
- https://www.wildberries.ru/;
- Wildberries is the largest online retailer and marketplace in Russia.
---

![Wildberries.ru Architecture diagram](./diagrams/out/wildberries/architecture-component/Component%20Diagram.svg)

---

![Wildberries.ru Architecture diagram](./diagrams/src/wildberries/architecture-component.puml)

## Selected components:
### Cart & Checkout service
- Manages the user’s shopping cart and orchestrates the checkout flow, including price calculation, taxes, discounts, and order placement. It’s responsible for turning “items in cart” into a confirmed order.

---

### Auth & ID service
- Handles user authentication, authorization, and identity management (login, tokens, roles, permissions). It ensures the right user is accessing the right resources securely.

---

### Catalog & Search service
- Stores and serves product data (items, categories, attributes) and provides search, filtering, and ranking capabilities. It helps users discover products quickly and accurately.

---

### User profile and loyalty
- Manages user-specific data such as personal details, preferences, addresses, and loyalty points or tiers. It supports personalization and reward programs across the platform.

---

### Fintech & Payment service
- Processes payments, refunds, and financial transactions, integrating with external payment providers and banks. It ensures secure, compliant handling of money flows and transaction records.


# Data Flow


[!Wildberries.ru Sequence Diagram](./diagrams/out/wildberries/architecture-sequence/Sequence%20Diagram.svg)



[!Wildberries.ru Sequence Diagram](./diagrams/src/wildberries/architecture-sequence.puml)


I’ll pick **Group 2: Checkout (Reservation & Payment)** — the big light-green block in the middle.

---

## Group 2 — Checkout (Reservation & Payment)

### What happens overall

In this group, the system turns a **cart** into a **pending order**, temporarily **reserves inventory**, and **initiates payment** (including 3-D Secure if required).
The key idea: *hold stock first, charge money second*, while keeping everything reversible if payment fails.

---

## Step-by-step narrative

### 1. User initiates checkout

* **Component interaction**

  * **WB Mobile App → Storefront Gateway**
* **Action**

  * User clicks **“Pay Now”**
* **Data sent**

  * `cart_id`
  * `payment_method`
* **Result**

  * Gateway calls `createOrder(...)`

---

### 2. Order initialization

* **Component interaction**

  * **Storefront Gateway → Order Service (OMS)**
* **Action**

  * OMS creates a new order record
* **Data**

  * Cart contents
  * User ID
  * Payment method
* **Order state**

  * `CREATED` (not paid yet)

---

### 3. Inventory reservation (critical consistency step)

* **Component interaction**

  * **Order Service → Inventory Service**
* **Action**

  * Reserve stock for all SKUs in the order
* **Data exchanged**

  * Request: list of `sku_ids` + quantities
  * Response: `reservation_id`
* **Inventory behavior**

  * Decrements *available* stock
  * Places items on **temporary hold (e.g. 15 minutes)**
* **Why this matters**

  * Prevents overselling while the user is paying

---

### 4. Order marked as awaiting payment

* **Component interaction**

  * **Order Service → Shared DB**
* **Action**

  * Save order with status:

    * `PENDING_PAYMENT`
  * Store `reservation_id`
* **Data persisted**

  * Order metadata
  * Inventory reservation reference

---

### 5. Payment initiation

* **Component interaction**

  * **Order Service → Payment Service**
* **Action**

  * Start payment transaction
* **Data**

  * Order ID
  * Amount
  * Payment method
* **Payment Service behavior**

  * Requests a charge token
  * Negotiates with the bank/acquirer
  * Determines if **3-D Secure (3DS)** is required

---

### 6. 3-D Secure redirect (if needed)

* **Component interaction**

  * **Payment Service → Storefront Gateway → WB Mobile App**
* **Data**

  * `payment_url` (3DS challenge page)
* **User action**

  * App opens a WebView
  * User confirms payment in their bank’s UI

---

### 7. Temporary state after this group

At the end of Group 2:

* Inventory is reserved
* Order is pending payment
* Payment is in progress
* No warehouse work has started yet

If payment fails or times out → reservation expires and stock is released.

---

## Components involved (summary)

| Component           | Role                              |
| ------------------- | --------------------------------- |
| WB Mobile App       | User interaction, opens 3DS       |
| Storefront Gateway  | API entry point, orchestration    |
| Order Service (OMS) | Order lifecycle, coordination     |
| Inventory Service   | Stock reservation & holds         |
| Payment Service     | Payment initiation & 3DS handling |
| Shared DB           | Order persistence                 |

---

## Key data exchanged

* `cart_id`, `order_id`
* `sku_ids`, quantities
* `reservation_id`
* `payment_url`
* Order status (`PENDING_PAYMENT`)

---

If you want, next we can:

* zoom into **Inventory reservation edge cases**
* compare this flow to **event-driven checkout**
* or rewrite this as **PlantUML sequence commentary** for interviews or design docs

# Deployment

[!Wildberries.ru Deployment Diagram](./diagrams/out/wildberries/architecture-deployment/Deployment%20Diagram.svg)

[!Wildberries.ru Deployment Diagram](./diagrams/src/wildberries/architecture-deployment.puml)


Clients run on user and warehouse devices → traffic enters via edge gateways → microservices run in Kubernetes → data lives in internal DB, cache, and Kafka → payments, logistics, and notifications integrate with external providers.


# Assumptions

* I assume the Inventory Service implements time-bound reservations (e.g. 10–15 minutes) that automatically expire if payment is not completed.

        This is inferred from the “Stock Reserved (Hold 15m)” note and is typical for high-traffic marketplaces to prevent dead stock.

        Order, payment, and inventory consistency is eventual, not strongly transactional

* I assume there is no distributed transaction (2PC) across Order, Inventory, and Payment services.

        Instead, the system relies on:

        state machines in OMS,

        compensating actions (release reservation, refund),

        and Kafka events for recovery.

        This fits the microservices + Kafka architecture shown.

# Open questions
* How exactly are race conditions handled during high-load sales?

        If thousands of users try to reserve the same SKU:

        Is reservation implemented via atomic DB operations?

        Redis + Lua?

        Optimistic locking in PostgreSQL?

        This is critical for flash sales but not visible from the diagram.

* What is the exact failure and retry strategy for payment callbacks?

        If the payment webhook:

        arrives late,

        arrives twice,

        or is temporarily lost,

        how does OMS ensure idempotency and correct final order state?







