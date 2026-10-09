---
name: smart-routing
description: Looks at where a LeadTap.me Smart Object (NFC or QR) sends people, proposes a weekly schedule by link, checks it for overlaps and applies it with the LeadTap.me MCP tools. Use when the user asks to change an object's destination, route a QR or NFC by hours or days (lunch menu by day, dinner at night, weekend promo), review or optimise smart routing, or asks why an object sends people somewhere.
---

# Smart routing for LeadTap.me objects

A Smart Object is a physical NFC thing (keychain, sign holder, touchpoint) or a QR code. Each tap or scan goes to one link: a single destination, or a weekly schedule of links with a fallback. The printed object never changes; only where it sends people.

## Before touching anything

1. `get_account`: smart routing is a paid feature (`features.smartRouting`). The portal's time zone is in `timezone`.
2. `list_objects` to find the object (by name, `objectId`), and `list_links` plus `list_forms` for the links it can point at (a lead page has a `linkId`). `create_link` makes a new external link.
3. `get_object_routing` with the customer's IANA `timezone` to see the current week as the portal paints it, and `overlaps` if any.
4. For "is it working": `get_objects_metrics` with `objectIds` for that object. `byDestination` shows taps per link served, which is how each rule performs against the fallback. The range is cut to the plan's analytics window (`clampedToPlan`): say so.

## Writing a schedule

`set_smart_routing` replaces the object's whole schedule. Send everything, not a diff.

- `timezone`: the customer's local time (ask, or `get_account.timezone`). Days are the customer's calendar days: 1 = Monday … 7 = Sunday.
- `slots`: `{ linkId, days: [..], start: "HH:MM", end: "HH:MM" }`. A slot stays inside one day; a night window is two slots (day 5 22:00 to 24:00 and day 6 00:00 to 02:00). `24:00` is the end of the day.
- Slots of different links must not overlap. The tool refuses with `OVERLAPPING_SLOTS` and lists the pairs: fix and resend.
- `fallbackLinkId`: where people go when no slot matches. Usually the main page or the menu.
- One link at a time: for a single destination use `set_object_destination` instead; it also clears any schedule (the response says `removedRules`).

Confirm with the user before applying: it changes where real people land, from the next tap.

## Proposing, not just applying

When the user asks to "optimise" or "set it up for my restaurant": ask for the opening hours and which link serves which moment (lunch menu, dinner menu, the booking page when closed, a weekend promo). Draw the week in words, one line per day, with the fallback stated. Then apply. After saving, call `get_object_routing` with the same time zone and show the week back.

Do not propose routing by browser language: it is not offered. Do not change an object nobody asked about.

## Getting more objects

NFC objects are bought in the store and connected by tapping them when they arrive; QR codes come in packs bought once and `create_qr_code` makes one when a bought code is left. `list_store_products` has the prices and the link to buy each; give the link, never buy for the user.
