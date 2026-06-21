[README_en.md](https://github.com/user-attachments/files/29169078/README_en.md)
# 🐾 WhatsApp Chatbot for Pet Shop — Automation with n8n

End-to-end WhatsApp customer service automation for a pet shop, built in **n8n**, integrating **Evolution API** (WhatsApp), **AI (Groq)**, and a **per-customer state management system** to handle multi-step conversations.

## 🎯 The problem this project solves

A pet shop receives repetitive WhatsApp messages (business hours, address, services, prices) that don't need AI to be answered — alongside specific questions that do. The challenge was building a flow that:

- Instantly answers common questions **without spending AI tokens**
- Uses AI **only** when the question falls outside the standard menu
- Lets the customer go through a **multi-step booking process** without losing conversation context along the way
- Doesn't loop by processing the bot's own outgoing messages

## 🧠 Architecture decisions

**State per customer, not per message.** Each customer has a "stage" saved in a table (`estados_clientes`). Every incoming message first checks this stage before deciding what to do — this prevents the bot from "forgetting" that a customer was mid-booking and sending them back to the main menu.

**Controlled cost.** Static information (address, hours, service list, prices) is answered with fixed text, without triggering AI. AI (Groq) is only called when the customer's message doesn't match any menu option.

**Echo filtering.** Since the WhatsApp webhook via Evolution API fires for both incoming and outgoing messages, the flow filters by `fromMe: false` right at the entry point — preventing the bot from processing and responding to its own messages.

## 🗺️ Flow overview

![Flow overview](./Fluxon8n_petshop_geral.png)

## 🔍 Section breakdown

### 1. Intake, filtering, and state-based routing
Webhook receives the message → filters duplicate/echo events → looks up the customer's saved stage in the table → decides whether to go to the main menu or resume an in-progress flow (such as booking).

![Intake and routing](./Fluxon8n_petshop_1.png)

### 2. Services menu (level 1)
Submenu with the main service options — each option triggers a specific fixed-text response, no AI involved.

![Services menu](./Fluxon8n_petshop_2.png)

### 3. Additional services menu (level 2)
A deeper submenu for complementary services (ozone treatment, teeth brushing, grooming treatments), following the same direct-response logic.

![Additional services menu](./Fluxon8n_petshop_3.png)

## 🛠️ Tech stack

- **n8n** — flow orchestration (self-hosted via Docker)
- **Evolution API** — WhatsApp integration
- **Groq** — AI model for questions outside the standard menu
- **Data Table (n8n)** — conversation state persistence per customer
- **Redis** — context memory for the AI agent

## 📌 Status

Functional project, tested in a local environment with a test WhatsApp number. Next steps include optional Google Calendar integration for automatic availability confirmation.

---

*Project built as a study in WhatsApp customer service automation with n8n.*
