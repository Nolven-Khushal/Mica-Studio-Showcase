# The journey of Mica Studio

How a plywood and laminate gallery got an offline ERP-lite: what we were asked for, what we argued about, what broke, and how it reached today.
Names of customers, suppliers and amounts are left out on purpose; the decisions and the numbers that explain them are real.

---

## Chapter 1 - "It is a bit different from a standard ERP"

The brief arrived in parts, one explanation at a time, and each part was written down before the next one started:

- A **desktop app**, **fully offline**, all data on the client's PC, delivered as **one `.exe`**. Login with an ID and password, so nobody can wander in.
- The gallery's invoices are raised through a separate billing company, so this is **not** a billing engine and not a textbook ERP.
  It tracks **purchase and sales movement, stock and costing, dispatch and delivery, expenses**, and shows them on a dashboard and in Excel.
- *Simple and easy for anyone, but very precise on the data.* Depth in the numbers, not in the number of screens.

A name was brainstormed from four angles (gallery, material, Sanskrit, modern product). The shortlist had *Lamina*, *Vitrine*, *Plinth*; the owner supplied a logo, and **Mica Studio** won: orange, white and black from then on.

## Chapter 2 - Three verticals, three kinds of invoice

Plywood, WPC boards and decorative Mica laminate do not look alike on paper. Real sample invoices were studied one by one, and each vertical got its own line columns:
laminate by design and finish, plywood by thickness and **square metre**, door frames by **running foot**. Stock is counted in pieces; price can be per piece, SQM or RFT,
with the priced quantity and the basic value typed, exactly like the paper bill. A purchase invoice holds one vertical; a sales bill may hold several.

## Chapter 3 - Stock as a passbook

The first big design decision: **stock is not a number, it is a list of movements**, like a bank passbook. Purchase in, sale out, credit note, adjustment.
Stock on hand is simply the total of the list. Each purchase line is a **batch**, a sale picks a batch, and profit is the sale minus the cost of *that* batch.
Nothing is overwritten, so any figure can be explained by pointing at the lines behind it. This is also why a late fix never has to touch a sale or a purchase.

## Chapter 4 - Two people, one set of books, no internet

The hardest conversation. The owner and a data-entry colleague must work on the same data, offline, privately.
Two copies of a database can never be identical without a network, so we laid out the options:

- **A**: one master PC, others send drafts.
- **B**: full copies everywhere with **sync by file** (carry an encrypted pack by pen drive and merge it).

The owner rejected file sync as one step too many, and allowed "a little online presence" only if the data stayed private. The answer was to **remove the sync problem instead of solving it**:
keep **one live database on one PC** (the host) and make every other computer a client that opens the same program from it. No merging, no conflicts, invoice numbers handled in one place.

What it took to make that trustworthy:

- **No entry without the main PC.** The client watches a heartbeat; when it stops, the screen locks and saves are refused in code, so nothing is ever kept on the client.
- **Safe retries.** Every save carries a request id, written in the same transaction as the document. Pressing Save twice after a lost reply creates one invoice. Tested with three parallel identical sends and 14 power cuts in a row.
- **Private by construction.** HTTPS with a pinned certificate, only private addresses allowed, one-time 8-digit pairing codes tied to the certificate so an impostor host cannot finish pairing.
- **A near miss:** a phone version was started, and then a check found a developer-machine tunnel that published a service to the open internet and would have looked "local" to the server. The phone idea was dropped by the owner, and the server now refuses anything that arrives that way.

## Chapter 5 - Never lose the books

Encrypted backups (AES-256-GCM) taken shortly after every change, verified after writing, and a **recovery key** shown once at setup. A first design that locked the data key with the operating system's safe store *lost the key in a test* when the app was killed at the wrong moment,
so it was replaced by a wrapped key plus the printed recovery key. At every start the database is checked; a damaged file is set aside and the newest good backup is restored, and the app never silently starts empty.
A built-in **data check** compares the books against themselves (stock, totals, ledgers against due dates, counters). The test suite grew to a few hundred checks, plus 24 end-to-end checks that drive two real hidden Electron programs against each other.

## Chapter 6 - "I sold it before it arrived"

The first live use produced the next requirement: customers pay for goods that are not yet bought. So a sales line can be **"order first"**: priced, numbered and printable, with no stock behind it. It shows as awaiting purchase,
and fills **by itself, oldest sale first**, when the supplier invoice is entered, taking the real cost then. Until it is filled, profit shows as unknown instead of lying. The business model, it turned out, *is* order-first:
the gallery holds almost no stock and buys only what customers have ordered.

## Chapter 7 - Real data, the hard way

Purchases came from the suppliers' own Excel files (with an importer that learned to apply the supplier's discount columns). Sales existed only as **scanned paper bills**, about 70 to 80 a month.
Building an in-app photo reader would have needed the internet and risked a misread going straight into live sales, so the decision was a **one-time careful extraction** with a no-mistakes rule:
a bill is accepted only if quantity times rate equals each line, lines equal the taxable value, the tax matches, and taxable plus tax plus round-off plus freight equals the printed total to the paisa, read twice. Anything else goes to a "needs a human look" list.
Hundreds of bills were loaded through a previewed, duplicate-guarded importer.

## Chapter 8 - The audit

With real data in, a **read-only audit** of the live books against the suppliers' files and the scans found what hand-typing and bulk loading had produced:
sales and purchases entered twice, duplicate customer cards, a discounted invoice overstated, supplier credit notes that implied customer returns nobody had recorded,
and a cheap lot of goods costed against the wrong same-day bill. The clean parts were just as important: nearly every bill matched the paper to the paise, and the stock ledger reconciled exactly.

## Chapter 9 - The corrections file

Fixing a dozen different problems by hand is confusing, so the fix became a feature: a **corrections file**, an ordered list of exact steps (merge two customer cards, enter a credit note, cancel and re-enter a debit note,
swap two batches, spread a supplier's value credit over older invoices...). It is **previewed** (will do / already done / cannot do, with the reason), takes a **backup first**, runs each step in its **own transaction**
so a refused step changes nothing while the others carry on, and can be applied twice without doing anything twice. The owner applied it in one click, and then reapplied later versions as the audit continued.

## Chapter 10 - Stock that should not exist

At hand-over the stock screen showed a large value for a business that keeps no stock. The cause was clear once looked at honestly: purchases from the opening month were loaded, but the sales of that month were not,
so those goods looked unsold. The tempting fixes (a debit note, a fake sale) would have changed what is owed and the tax. The right tool was the **stock adjustment**: one extra line per batch that brings the total to zero,
dated, with the reason written on it, visible in batch history and in the movement report, owner only, and touching **no** purchase, sale, payable, receivable or profit. A single "stock reset" step does it for all batches at once and leaves untouched
whatever is already invoiced and waiting to be sent, anything waiting for stock, and anything bought after the reset date.

## Chapter 11 - Money received that does not fit a bill

The last big one: a customer pays a lump sum while his bills are scattered over months, with an old balance from before the system and sometimes an advance. The first design could only attach a receipt to **one bill**.
Now **Receipts & Payments** is a page of its own: choose the party, the amount, cash or bank, and put it on one or several bills (part payments included). What does not fit a bill stays **on account**, shows on the ledger as an advance,
and can be put on a bill later. If it was really for old bills from before the system, the owner books it as an **earlier balance**: an opening entry of the same amount is posted and the receipt settles it, so the ledger reads nil,
the receipt reads "against earlier balance", and due dates and overdue figures do not move.

## Where it stands

Offline, one installer, one live database shared privately between PCs, stock as a ledger, order-first selling, audited real data, previewed corrections, receipts and payments with advances, encrypted backups, and a test suite that drives real programs.
The full engineering detail is in the code and the tests; this file is the story of how we got there.
