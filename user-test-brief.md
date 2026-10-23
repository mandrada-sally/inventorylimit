# User Test Brief: Inventory Limit Experience (Cosmo Prof)

> **How to use this file:** paste it into a Claude chat. It holds everything needed to plan a moderated usability test of the prototype described below.

---

## What I need from you, Claude

Please create a complete **moderated usability test session** for the prototype described below. Include:

1. **Research goals and questions.** What we're trying to learn, written as testable questions.
2. **Participant profile and screener.** Who to recruit (professional salon and beauty customers who buy on Cosmo Prof / Beauty Pro), how many, and 6–8 screener questions with qualifying answers.
3. **Session outline.** A 45–60 minute agenda with timings: intro, warm-up, tasks, wrap-up.
4. **Moderator script.** Word-for-word intro, consent and think-aloud instructions, then the closing.
5. **Task scenarios.** One realistic, non-leading scenario per prototype screen listed below, written the way a moderator would read it aloud. Each one needs:
   - the starting page (file name)
   - the success criteria
   - what to observe
   - 2–3 follow-up probes
   - a post-task rating (Single Ease Question, 1–7)
6. **Comprehension checks.** For each alert message, a question that tests whether the participant understood it without hinting at the answer. For example: "In your own words, what is this message telling you?"
7. **Metrics and note-taking template.** A table a note-taker can fill in per participant and per task: success / partial / fail, time, errors, SEQ, quotes.
8. **Post-test questionnaire.** SUS or a short custom set, plus 3–4 open-ended questions comparing the scenarios.
9. **Analysis plan.** How to synthesize findings across participants, plus a severity scale for issues.
10. **Risks and biases.** What could skew results with this prototype (see "Known prototype limitations") and how to mitigate them.

Keep tasks free of leading language. Don't use words that appear in the UI, such as "limit", "available" or "switch", in the task prompts.

---

## Product context

- **Business:** Cosmo Prof / Beauty Pro, a professional beauty supply retailer. Customers are mostly licensed stylists and salon owners buying in volume.
- **Problem:** inventory is constrained per fulfillment method (Shipping, In-Store Pickup, 2-Hour Delivery) and per store. Customers often want more units than one method can fulfill, or an item goes out of stock after it's already in their bag.
- **Design goal:** communicate purchase constraints clearly and early on the product page, and recover gracefully in the Shopping Bag by offering fallbacks (adjust quantity, switch fulfillment method, switch store, remove the item) instead of dead ends.
- **Fulfillment methods:**
  - **Shipping:** $9.99, "Free on a $150+ purchase".
  - **In-Store Pickup:** free, ready within 2 hours.
  - **2-Hour Delivery:** $5.00 plus an optional driver tip ($4 / $7 / $9 / $11 or custom).

---

## Prototype

The prototype is a static, clickable HTML prototype that works on desktop, tablet and mobile. **`index.html`** is the hub page linking to every scenario.

### Product page (PDP) scenarios

The product is **ColorLast Shampoo for Color-Treated Hair**, priced at **$6.33**. The page has an image gallery with full-screen zoom and a video, delivery options (Shipping / Pickup / 2-hr Delivery), a quantity selector and **Add to Bag**.

| ID | File | Constraint | What the user sees |
|---|---|---|---|
| **P1** | `index-p1.html` | **Availability limit.** Only 24 units can ship. | The quantity starts at 1. With Shipping selected it stops at 24, and a blue info notice, "Only **24** available for shipping", appears only at 24 and disappears as soon as the quantity drops to 23. With Pickup or 2-hr Delivery selected there is no cap and no notice. Switching back to Shipping with more than 24 drops the quantity to 24 and shows the notice. |
| **P2** | `index-p2.html` | **Purchase limit.** Stock is fine, but a policy caps the order at 10 per customer. | The quantity starts at 1 and stops at 10. A blue info notice, "You can add up to **10** items to your Bag", appears only at 10 and disappears as soon as the quantity drops to 9. |
| **P3** | `index-p3.html` | **Limit of 1, Pickup only.** Only 1 unit can be reserved for In-Store Pickup; Shipping and 2-hr Delivery are unlimited. | Pickup is preselected and the notice reads "Only **1** available item for Pickup". There's no quantity selector, so Add to Bag spans the full width. Tapping Add to Bag switches to the P4 state ("already in your Bag" + **Go to Bag**, which keeps the user on the product page). Switching to Shipping or 2-hr Delivery hides the notice and brings back an unlimited quantity selector and Add to Bag. |
| **P4** | `index-p4.html` | **Limit reached.** The 1 allowed unit is already in the bag. | The notice reads "**This product is already in your Bag.** You can add 1 to your Bag." The main button becomes an outlined **Go to Bag**, which links to the bag. |

### Shopping Bag scenarios

Each bag holds two products:
- **BlondMe 12%/40 Volume Premium Developer:** 10 units at $8.50 each ($85.00, was $100.00). This is the item with the problem.
- **ChromaSilk VIVIDS Semi-Permanent Color:** 1 unit at $9.00 ($9.00, was $10.00). This item has no problem.

A red banner at the top reads "Availability changed for (1) items since your last visit", with a **Review** link that scrolls to the item.

**Checkout** is greyed out while any item still has an availability issue. Tapping it anyway scrolls to the problem item and pulses its red outline. Checkout becomes active as soon as the issue is resolved, whether by adjusting the quantity, switching method or store, or removing the item.

| ID | File | Situation | Alert on the item | Fallback offered | Result of the main action |
|---|---|---|---|---|---|
| **B1** | `bag-b1.html` | Shipping can't cover the full quantity. | Red outline. "Only 6 of 10 available for Shipping". | **Adjust quantity to 6** link | Quantity becomes 6, the warning clears and the totals update. |
| **B2** | `bag-b2.html` | Same shipping shortfall, but pickup has stock. | Same red warning as B1. | Green box: "All 10 available for In-Store Pickup within 2 hours / From 1125 Catalina Dr., 5 mi. away" with **Switch to Pickup**. | The item moves to a new Pickup group and shows "**Updated.** All 10 will be ready for pickup." |
| **B3** | `bag-b3.html` | The bag uses 2-Hour Delivery from store #4212, which is short. | "Only 6 of 10 available from this store". | Green box: "All 10 available from a different store / From 890 Elm St., 3.2 mi. Arrives today" with **Switch store**. | "Deliver from" changes to 890 Elm St. and the item shows "**Updated.** All 10 will be delivered from 890 Elm St." |
| **R4** | `bag-r4.html` | Out of stock for every method and store. | Red outline. Red inline alert: "**Out of stock** / Not available for shipping, pickup, or 2-hr Delivery right now." | A full-width **Remove** button is the main action. | The item is removed, the bag goes from (2) to (1), and the totals update. |

### Order summary (all bags)

- The summary lists:
  - Subtotal
  - Shipping, or 2-Hour Delivery in B3
  - 2-Hour Delivery Tip (B3 only)
  - Estimated Tax (GST + PST + HST/SAVECASH)
  - Discount (February Mystery Sale 5%, MISSU 10%, COSMO7 7%)
  - Order Total
- Totals recalculate live when quantities, tips, items or fulfillment change.
- Reference totals when a page first loads:

| Bag | Order Total when loaded | Totals after the main action |
|---|---|---|
| B1 | $89.54 | $60.76 after Adjust to 6 |
| B2 | $89.54 | unchanged after Switch to Pickup (ChromaSilk still ships) |
| B3 | $91.55 (includes the $7 tip) | — |
| R4 | $89.54 | $17.62 after Remove |

---

## Research questions to cover

1. Do participants **notice** the constraint messages on the product page before they try to exceed them?
2. Do they **understand the difference** between an *availability* limit (P1: stock) and a *purchase* limit (P2: policy)? Does the difference matter to them?
3. In P3/P4, is it clear why the quantity selector disappears and why the button turns into **Go to Bag**?
4. In the bag, do participants **find** the affected item from the top banner? Do they understand what changed and why?
5. When offered a fallback (adjust quantity, switch to pickup, switch store), do they **pick the option that fits their goal**? Do they trust it?
6. In R4, is "Out of stock" plus **Remove** acceptable, or do they expect alternatives such as notify me, save for later or substitutes?
7. Is the **visual severity** right? Red is used for blocking issues, green for fallbacks and blue for informational limits.
8. Does the **order summary** stay understandable as totals change after each resolution?

---

## Suggested task framing (starting points; please refine)

- **PDP:** "You need to restock this shampoo for your salon and want enough for the next month (about 30 bottles). Go ahead and add what you need."
- **P3 → P4:** "You're near the store and want to pick this shampoo up on your way in today." Then ask them to try to get a second bottle, and observe whether they discover that Shipping or 2-hr Delivery allows more.
- **B1/B2/B3:** "You come back to finish an order you started yesterday. Review your bag and get it ready to check out." Each bag gets a different variant.
- **R4:** same framing as the bag tasks. Observe what they do with the item that can't be fulfilled.

---

## Known prototype limitations (tell participants up front or work around them)

- **Static prototype:** search, navigation menus, Change links, promo codes, checkout, Apple Pay and PayPal don't do anything.
- **Data:** prices, stores and addresses are placeholders. The PDP shows one product (ColorLast); the bags show different products (BlondMe, ChromaSilk).
- **Separate pages:** each scenario is its own page. The PDP doesn't carry over to the bag, except that **Go to Bag** in P4 opens B1.
- **Delivery options and the notice:** on P1 the 24-unit cap applies only to Shipping. On P2, switching the delivery option doesn't change the limit message.
- **Hub page:** `index.html` describes each scenario. Don't show it to participants, because it gives away the answers. Open scenario files directly.
- **Device:** test on both desktop and mobile if possible, since the layouts differ. On mobile the delivery address truncates with "…".

---

## Logistics (fill in before sending)

- Number of participants: _[e.g., 6–8]_
- Remote or in person: _[ ]_
- Tool (Zoom / Lookback / Maze / UserTesting): _[ ]_
- Prototype hosting URL: _[ ]_
- Session length: _[45–60 min]_
- Incentive: _[ ]_
- Test dates: _[ ]_
