# 🎙️ Ruby — AI Voice Receptionist | Gold Coast Kitchen
### Built with VAPI + n8n + Google Sheets + Gmail

![Status](https://img.shields.io/badge/Status-Live%20%E2%9C%85-brightgreen?style=for-the-badge)
![VAPI](https://img.shields.io/badge/VAPI-Voice%20AI-blueviolet?style=for-the-badge)
![n8n](https://img.shields.io/badge/n8n-Workflow%20Automation-EA4B71?style=for-the-badge)
![Google Sheets](https://img.shields.io/badge/Google%20Sheets-CRM%20Database-34A853?style=for-the-badge)

---

## 📌 Project Overview

**Ruby** is a fully functional AI Voice Receptionist built for **Gold Coast Kitchen** — a Nigerian restaurant — as a capstone project completing an n8n automation module.

Ruby handles **inbound customer calls 24/7** — taking food orders, booking private events, looking up orders, registering new clients, and checking event availability — all without a single human needing to pick up the phone.

Every interaction is:
- ✅ Logged in real time to a **Google Sheets CRM database**
- ✅ Confirmed via a **branded HTML email** with embedded payment links
- ✅ Routed intelligently through **7 live n8n workflows** via an MCP server

> *"What if your restaurant never missed another call? I built that."*

---

## 🏗️ System Architecture

```
Inbound Call (Customer)
        │
        ▼
   VAPI (Ruby)
   AI Voice Agent
        │
        ▼
  n8n MCP Server ──── Central routing hub
        │
        ├──► 1. Present Menu
        ├──► 2. New Client CRM
        ├──► 3. Client Lookup
        ├──► 4. Place Order ──────► Google Sheets + Gmail (Order Confirmation + PAY NOW)
        ├──► 5. Lookup Order
        ├──► 6. Check Availability
        └──► 7. Book Event ────────► Google Sheets + Gmail (Booking Confirmation + PAY DEPOSIT)
```

---

## 🔧 7 Live Workflows

### 1. 🗂️ Present Menu
Ruby describes the full Gold Coast Kitchen menu with prices on demand when a caller asks what's available.

**Flow:** VAPI tool call → n8n → returns menu data → Ruby reads to customer

---

### 2. 👤 New Client CRM
First-time callers are automatically registered and saved to the Google Sheets CRM database — no manual data entry required.

**Data captured:** Customer name · Phone number · Email · Date registered

**Flow:** VAPI → n8n Code node → Google Sheets (append row) → confirmation to Ruby

---

### 3. 🔍 Client Lookup
Ruby pulls up any existing customer record instantly by name or phone number.

**Flow:** VAPI → n8n → Google Sheets (filter by name/phone) → returns record → Ruby reads back to customer

---

### 4. 🛒 Place Order
Customers place food orders entirely by voice. Ruby confirms the order, logs it to Google Sheets, and automatically sends a branded HTML email confirmation with a **PAY NOW** button.

**Data captured:** Order ID · Customer name · Items · Total · Delivery address · Order type · Status

**Flow:** VAPI → n8n Code node → Google Sheets (log order) → Gmail (send HTML email) → confirmation to Ruby

---

### 5. 📦 Lookup Order
Customers call back and ask Ruby to check their order status using their Order ID.

**Flow:** VAPI → n8n → Google Sheets (filter by Order ID) → returns order details → Ruby reads status to customer

---

### 6. 📅 Check Availability
Ruby checks event availability in real time before committing to a booking — preventing double bookings.

**Flow:** VAPI → n8n → Google Sheets (check date/time slot) → confirms or declines availability → Ruby responds

---

### 7. 🎊 Book Event
Customers book private dining, birthdays, corporate events and weddings entirely by voice. Ruby confirms the booking, logs it, and sends an automated **PAY DEPOSIT** email.

**Data captured:** Booking ID · Customer name · Event type · Date · Time · Guest count · Venue · Special requests · Deposit amount

**Flow:** VAPI → n8n Code node → Google Sheets (log booking) → Gmail (send HTML email) → confirmation to Ruby

---

## 📧 Automated Email Notifications

Two custom-designed HTML email templates fire automatically post-call:

| Email | Trigger | Key Feature |
|---|---|---|
| **Order Confirmation** | After Place Order workflow | Branded HTML · Order summary · PAY NOW button |
| **Event Booking Confirmation** | After Book Event workflow | Branded HTML · Booking summary · PAY DEPOSIT button |

Both emails use dynamic `{{$json.variables}}` pulled directly from the n8n workflow output.

---

## 🛠️ Tech Stack

| Layer | Tool |
|---|---|
| Voice AI / Phone | VAPI |
| Workflow Automation | n8n (self-hosted) |
| MCP Server | n8n MCP Node |
| CRM Database | Google Sheets |
| Email Notifications | Gmail via n8n |
| Scripting | JavaScript (n8n Code nodes) |
| Payment Integration | Paystack (payment link) |

---

## 📁 Repository Contents

```
ruby-ai-voice-receptionist/
│
├── workflows/
│   ├── GCK_MCP_Server.json           # Central routing hub
│   ├── GCK_New_Client_CRM.json       # New client registration
│   ├── GCK_Client_Lookup.json        # Client record lookup
│   ├── GCK_Place_Order.json          # Order placement + email
│   ├── GCK_Lookup_Order.json         # Order status lookup
│   ├── GCK_Check_Availability.json   # Event availability check
│   └── GCK_Book_Event.json           # Event booking + email
│
├── email-templates/
│   ├── order_confirmation.html        # Order confirmation email
│   └── event_booking_confirmation.html # Event booking email
│
├── docs/
│   ├── ruby_system_architecture.png   # System architecture diagram
│   └── GCK_Ruby_Presentation.pptx    # Project slide deck
│
└── README.md
```

---

## 🚀 Setup Guide

### Prerequisites
- [VAPI account](https://vapi.ai) with assistant configured
- [n8n instance](https://n8n.io) (cloud or self-hosted)
- Google Sheets with correct column headers (see below)
- Gmail account connected to n8n
- Paystack or Flutterwave account (for payment links)

### Google Sheets — Required Column Headers

**Orders Sheet:**
`OrderID | CustomerName | OrderType | Items | Total | Address | Status | DeliveryTime | Email`

**Bookings Sheet:**
`BookingID | CustomerName | EventType | EventDate | EventTime | GuestCount | Venue | SpecialRequests | DepositAmount | PaymentLink | Email | Phone`

**Clients Sheet:**
`CustomerName | Phone | Email | DateRegistered`

### Installation

1. **Import n8n workflows** — import each `.json` file from the `/workflows` folder into your n8n instance
2. **Connect credentials** — link your Google Sheets and Gmail credentials in n8n
3. **Configure VAPI** — set each n8n webhook URL as the corresponding tool endpoint in your VAPI assistant
4. **Assign phone number** — link your phone number to the Ruby assistant in VAPI
5. **Test each workflow** — use the test payloads below before going live

### Sample Test Payload

```json
{
  "CustomerName": "Chioma Okafor",
  "OrderID": "GCK-TEST-001",
  "OrderType": "Delivery",
  "Items": "1x Jollof Rice (Large), 2x Chicken Wings, 1x Chapman",
  "Total": "₦18,500",
  "Address": "15 Admiralty Way, Lekki Phase 1, Lagos",
  "Status": "Order Received",
  "PaymentLink": "https://paystack.com/pay/gck-test-001"
}
```

---

## 💼 Business Impact

Gold Coast Kitchen now has a receptionist that works **24 hours a day, 7 days a week** — no sick days, no missed calls, no forgotten orders.

| Before Ruby | After Ruby |
|---|---|
| Missed calls during busy hours | Every call answered instantly |
| Manual order logging | Auto-logged to Google Sheets CRM |
| No after-hours bookings | 24/7 event booking available |
| Staff time spent on phone | Staff freed for kitchen & service |
| No automated follow-up | Instant branded email sent post-call |

---

## 🔮 Ruby V2.0 — Coming Next

| Feature | Description |
|---|---|
| 💳 Live Payment Links | Ruby generates real Paystack/Flutterwave payment links mid-call |
| 📞 Call Transfer | Ruby escalates to a human staff member when needed |
| ❓ FAQ Lookup | Ruby answers common questions from a live knowledge base |
| ✏️ Update Appointment | Customers reschedule bookings by voice |
| 🗑️ Delete Appointment | Customers cancel bookings and trigger automated refund workflows |

---

## 👩🏾‍💻 About the Builder

**Gloria Njorteah** — Strategic Business Analyst & AI Automation Specialist

Ruby was built as a capstone project completing an n8n automation module — two weeks of sleepless nights, endless debugging, and the deeply satisfying moment when she answered her first live call.

- 🌍 [LinkedIn](https://www.linkedin.com/in/gloria-njorteah)
- 💼 [Upwork](https://www.upwork.com)
- 🐙 [GitHub](https://github.com/Glorious75)

> *Available to build AI voice agents and automation systems for your business. Let's talk.*

---

## 📄 Licence

This project is shared for portfolio and educational purposes. Workflow logic, email templates and system architecture are original work by Gloria Njorteah.

---

*Built with 💛 using VAPI + n8n | Gold Coast Kitchen · Lagos, Nigeria*
