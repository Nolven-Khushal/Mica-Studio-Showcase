<p align="center"><img src="screenshots/logo.png" alt="Mica Studio" width="380"></p>

<h3 align="center">An offline ERP for a plywood and laminate gallery, built end to end.</h3>

<p align="center">
Purchases and sales · stock as a passbook · order-first selling · dispatch · receipts with advances · party ledgers · audited real data · encrypted backups<br>
<b>Desktop · fully offline · one installer · no cloud server</b>
</p>

> **Showcase repository.** This page tells the story and shows the product. The source code is private by design and is not published here.
> All screens and the video use made-up demo data.

---

## Watch it

**[▶ The full tour (video, about 9 minutes)](video/mica-studio-tour.mp4)** — every feature, driven live in the real app, with the story behind each chapter.

[![The dashboard](screenshots/01-dashboard.png)](video/mica-studio-tour.mp4)

## The story

The whole journey, decision by decision (the brief, stock as a passbook, two people on one set of books with no internet, the audit of the real data, the corrections file, the stock reset, receipts with advances) is in **[docs/JOURNEY.md](docs/JOURNEY.md)**.

Everything the product does, screen by screen, is in **[docs/FEATURES.md](docs/FEATURES.md)**.

## A look inside

| | |
|---|---|
| ![Sales](screenshots/03-sales.png) | ![New sales invoice](screenshots/04-sales-new.png) |
| **Sales**: profit, dispatch and payment status on one line | **A new invoice**: the exact batch behind every line |
| ![Dispatch board](screenshots/05-dispatch-board.png) | ![Inventory](screenshots/06-inventory.png) |
| **Dispatch board**: late, on the road, delivered | **Stock**: on hand, yet to dispatch, free to sell |
| ![Receipts](screenshots/07-receipts.png) | ![Reminders](screenshots/08-reminders.png) |
| **Receipts & payments**: one lump sum over many bills, the rest on account | **Reminders** in English, Hindi and Gujarati |
| ![Profit](screenshots/09-profit.png) | ![Reports](screenshots/10-reports.png) |
| **Profit and costing**: exact, because every line knows its cost | **Reports**: Excel and PDF |
| ![Purchase](screenshots/02-purchase.png) | ![Products and rates](screenshots/11-products.png) |
| **Purchases**: every supplier invoice with its stock and cost | **Products and rates**: the rate sheet as rules |

## How it is put together

```mermaid
flowchart LR
  subgraph Main PC
    UI[Desktop app] --> API[One back-end entry]
    API --> SVC[Services: purchase, sales, stock, money, notes, reports]
    SVC --> DB[(One local database)]
    DB --> BK[Encrypted backups + recovery key]
    API --> CHK[Automatic data check]
  end
  Other[Owner's laptop] -- private HTTPS, pairing code --> API
```

- **One live database** on one PC; other computers open the same program from it. No sync, no merge conflicts, no cloud.
- **Stock is a ledger** of movements; profit is the sale minus the cost of the exact batch sold.
- **Every correction is a previewed, repeatable step** that can be applied twice without doing anything twice.

## Facts

| | |
|---|---|
| Delivery | A single Windows installer |
| Data | Local, encrypted backups, automatic restore |
| Verticals | Plywood, WPC, Mica laminate (different line layouts) |
| Testing | Hundreds of automated checks, plus end-to-end tests that drive two real programs against each other |
| Languages | English, Hindi and Gujarati reminders |

---

<sub>© Khushal Vashistha. All rights reserved. No part of the source code is published or licensed.</sub>
