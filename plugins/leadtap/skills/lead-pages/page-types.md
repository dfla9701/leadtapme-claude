# Pieces, shapes and worked examples of a LeadTap.me page

A LeadTap.me page is one mobile page made of **steps**, in any order and any mix. A step either **asks** (name, email, phone, choice, text, slider, star rating) or **presents** (contact card, about us, products & services, testimonials). The page ends with a thank-you that can differ by score, redirect somewhere, or hand a coupon or a file.

This file is not a taxonomy. The shapes below are common starting points, and the worked examples at the end mix them freely: an open house sign-in is a property presentation, a capture form, a qualifier and the agent's card, all in one page. Build whatever the business needs from the pieces.

**The deal: free mix, strict truth, strict length.** What never bends: the checker's rules (unique keys, titles, scoring ranges without gaps, photos from known sources), the length rules below, and truth: the business's own work is shown with their own photos, never stock; contact data and quotes are theirs, never invented; review flows never gate; URLs are theirs, never guessed. Every recipe here passes the checker as written, and a test in the repository keeps it so.

## What "optimized" means

Freedom in the mix, discipline in the length. A page opens on a phone, often standing in a venue, so:

- **3 to 7 steps including presentation**, and one screen per idea. A presentation step costs no typing but it costs scroll: put it where it earns its place (the property, today's experiences, who you are when the visitor does not know you).
- **One email step, required**, unless the page is pure presentation. Content first, questions after: someone who just saw the menu or kept your card leaves an email more willingly.
- **Choose before type.** Cards with an emoji or a photo beat a text box. `textarea` only when they truly have to tell something.
- **Partial submit right after the email** (`partialSubmitAfterStep`), so a visitor who leaves during the qualifying questions is still a lead. Paid feature: check `get_account.features.partialSubmit`.
- **Scoring only when it changes the ending.** Points are worth it when hot leads go somewhere and the rest get something else; otherwise `scoring.enabled: false` and one thank-you.
- **Every ending gives something:** a next step, a coupon, a link, a promise. A bare "thanks" wastes the moment.
- **The visitors' language**, not the user's, and the business's tone.

## The pieces

| Step `type` | Asks or presents | Notes |
|---|---|---|
| `name`, `email`, `phone`, `url` | asks | `email` validated; `phone` with country |
| `text`, `textarea` | asks | one line / a paragraph |
| `multiple_choice` | asks | `options[]` with `label`, `value`, optional `icon` (emoji or a requested photo) and `points`; `optionLayout: "cards" \| "list"`; `selectionMode: "single" \| "multiple"` |
| `slider` | asks | `min`, `max`, `step`, `default`; `sliderScoring` ranges give points |
| `rating` | asks | 1 to 5 stars; `sliderScoring` ranges give points |
| `vcard` | presents | a contact card the visitor saves to their phone; no title; `required: false` |
| `about` | presents | `question` is the title, `helper` the text, one photo; `about.layout`: `classic`, `cover`, `split` |
| `products` | presents | `question` is the title; `products.items[]` with `id`, `title`, `description` (prices go here) and a photo each; one step per section |
| `testimonials` | presents | `question` is the title; `testimonials.items[]` with `quote` and `author`, written by the customer, never by you |

Logic on any step: `showWhen` / `hideWhen` (`{ field, values }` for choices; `{ field, op: "lt" | "gt" | "eq" | "between", value | min, max }` for numbers) and `goto` jumps forward (`{ values, target }`, `target: null` ends the page). Endings, scoring and rewards are in [endings.md](endings.md).

## Rules for presentation steps

- `about`, `products` and `testimonials` need a `question` (their title) and may carry `helper`. `vcard` needs no title. All four take `required: false`.
- They ask nothing, so they are not the email step: a page meant to capture still needs an `email` step.
- Photos: `about` takes one (`imageSearches["<key>.imagen"]`), each product card takes one (`imageSearches["<key>.<itemId>"]`). Stock photos: say so and give `editorUrl`. Testimonials never get stock photos, and their quotes are never written by you: send the items with `quote` and `author` null and tell the customer to write the real ones in the editor before publishing. The checker warns until they do.
- **Own work, own photos.** Where the photo is itself the claim (completed projects, before and after, the team, the premises, the things they make), never ask for stock: leave `imageUrl` null, say so, and give `editorUrl` for the real ones. Stock is a placeholder only where the photo illustrates without claiming anything (a dish, a tour, a keychain, a coffee). A stock kitchen in "our projects" shows someone else's work as theirs.
- Contact card data is the customer's: ask for it, and leave null what you were not given. Never invent a phone, an email or a street. `photoUrl` stays null; they upload it in the editor.

## Common shapes

| Shape | What the visitor gets | Steps | Captures |
|---|---|---|---|
| Lead capture form | A short form with a thank-you | questions + email | yes |
| Digital contact card | Your card, saved to their phone | `vcard` (+ email) | optional |
| Business presentation | Who you are, what you sell, what clients say | `about`, `products`, `testimonials` (+ `vcard` or email) | optional |
| Products & services / menu | A carousel of cards, by section | one `products` per section (+ `about`) | optional |
| Testimonials page | Quotes with five stars | `testimonials` (+ `about`) | no |
| Review or feedback flow | Stars, then what went wrong | `rating` + conditional follow-ups | yes, optional email |
| Lead qualifier | Questions that score and end differently | scored questions + email | yes |
| Event RSVP | Attendance, headcount, notes | name, choice with jumps, notes | yes |

The review flow and the lead qualifier are in [endings.md](endings.md). Recipes for the others follow, then the mixes.

### Digital contact card ("contact us" page)

The page of a keychain, a badge, a sign holder on a counter. The visitor taps, keeps the card on their phone, and may leave an email.

```json
{
  "steps": [
    { "key": "card", "type": "vcard", "question": "", "required": false,
      "vcard": { "layout": "card", "fullName": "Ana Pérez", "title": "Owner", "company": "Café Aurora", "phoneMobile": "+34 600 000 000", "email": "ana@cafeaurora.es", "website": "https://cafeaurora.es", "address": "Calle Mayor 12, Madrid",
        "links": [ { "label": "Instagram", "url": "https://instagram.com/cafeaurora" }, { "label": "Book a table", "url": "https://cafeaurora.es/book" } ],
        "saveLabel": "Save contact" } },
    { "key": "email", "type": "email", "question": "Want our news? Leave your email", "helper": "No spam.", "required": false }
  ],
  "scoring": { "enabled": false },
  "outcomes": [],
  "ending": { "headline": "Thanks!", "body": "Our card is in your phone. See you soon." }
}
```

`layout`: `card` (a card), `poster` (full-screen with the photo), `list` (one line per item). Up to 8 `links`. Replace every value above with the customer's real data; drop the email step if they only want the card.

### Business presentation page

For a QR on a flyer, a link in a bio, a sign in a fair: the visitor may not know the business yet, so the page tells them who you are before asking anything.

```json
{
  "steps": [
    { "key": "about", "type": "about", "question": "Café Aurora", "helper": "Specialty coffee roasted in Madrid since 2015. Breakfast, brunch and the best cinnamon rolls in Lavapiés.", "required": false,
      "about": { "media": "image", "layout": "cover" } },
    { "key": "menu", "type": "products", "question": "What we serve", "required": false,
      "products": { "items": [
        { "id": "flat_white", "title": "Flat white", "description": "Double shot, silky milk." },
        { "id": "cinnamon_roll", "title": "Cinnamon roll", "description": "Baked every morning." },
        { "id": "brunch", "title": "Weekend brunch", "description": "Saturdays and Sundays, 10 to 14." }
      ] } },
    { "key": "reviews", "type": "testimonials", "question": "What our regulars say", "required": false,
      "testimonials": { "items": [ { "id": "t1", "quote": null, "author": null }, { "id": "t2", "quote": null, "author": null }, { "id": "t3", "quote": null, "author": null } ] } },
    { "key": "email", "type": "email", "question": "Get 10% off your first coffee", "helper": "We will send the code to your inbox.", "required": true }
  ],
  "scoring": { "enabled": false },
  "outcomes": [],
  "ending": { "headline": "Thank you!", "body": "Your code is on its way." }
}
```

Photos for this one, sent alongside the config:

```jsonc
"imageSearches": {
  "about.imagen": "specialty coffee shop interior",
  "menu.flat_white": "flat white coffee cup",
  "menu.cinnamon_roll": "cinnamon roll pastry",
  "menu.brunch": "brunch table eggs avocado"
}
```

`about.layout`: `classic` (image above the text), `cover` (text over the image), `split` (side by side). `media: "video"` with `videoUrl` is possible but the customer uploads the video in the editor: leave it to them.

### Products & services, or a menu

One `products` step per section, each with its title. Between 2 and 6 cards per section reads well; the limit is 12. Add an `about` first when the visitor may not know the place, and an email at the end only if there is a reason for it (a promo, a reservation list); a menu that only shows the menu is fine.

```json
{
  "steps": [
    { "key": "starters", "type": "products", "question": "Starters", "required": false,
      "products": { "items": [
        { "id": "bravas", "title": "Patatas bravas", "description": "8 €" },
        { "id": "croquetas", "title": "Croquetas de jamón", "description": "6 units, 9 €" }
      ] } },
    { "key": "mains", "type": "products", "question": "Mains", "required": false,
      "products": { "items": [
        { "id": "paella", "title": "Paella valenciana", "description": "Minimum 2 people, 18 € each." },
        { "id": "lubina", "title": "Grilled sea bass", "description": "With roasted vegetables, 22 €" },
        { "id": "veggie", "title": "Vegetable lasagna", "description": "Vegetarian, 15 €" }
      ] } }
  ],
  "scoring": { "enabled": false },
  "outcomes": [],
  "ending": { "headline": "Buen provecho", "body": "Ask our staff for today's specials." }
}
```

Prices go in `description`: there is no price field. Photos: `imageSearches["starters.bravas"]` and so on, one per card, describing the dish.

### Testimonials page

Quotes with five stars, optionally after an about page. The items are placeholders: the customer writes the real quotes in the editor, and the page should not be published before that.

```json
{
  "steps": [
    { "key": "about", "type": "about", "question": "Clínica Dental Sol", "helper": "Family dentistry in Sevilla since 2008.", "required": false, "about": { "media": "image", "layout": "classic" } },
    { "key": "reviews", "type": "testimonials", "question": "What our patients say", "required": false,
      "testimonials": { "items": [ { "id": "t1", "quote": null, "author": null }, { "id": "t2", "quote": null, "author": null }, { "id": "t3", "quote": null, "author": null } ] } }
  ],
  "scoring": { "enabled": false },
  "outcomes": [],
  "ending": { "headline": "Thank you for reading", "body": "Call us or book online whenever you are ready." }
}
```

If the customer gives you real quotes in the conversation, write them with their author. Never make one up, not even as an example.

### Event RSVP

```json
{
  "steps": [
    { "key": "name", "type": "name", "question": "Who's attending?", "required": true },
    { "key": "email", "type": "email", "question": "Your email", "helper": "For the confirmation.", "required": true },
    { "key": "attending", "type": "multiple_choice", "question": "Will you join us?", "required": true, "optionLayout": "cards",
      "options": [ { "label": "Yes, I'll be there", "value": "yes", "icon": "🎉" }, { "label": "Sorry, can't make it", "value": "no", "icon": "😔" } ],
      "goto": [ { "values": ["no"], "target": null } ] },
    { "key": "guests", "type": "multiple_choice", "question": "How many guests are you bringing?", "required": true, "optionLayout": "cards",
      "options": [ { "label": "Just me", "value": "0", "icon": "🙋" }, { "label": "1 guest", "value": "1", "icon": "👥" }, { "label": "2 or more", "value": "2_plus", "icon": "👨‍👩‍👧" } ] },
    { "key": "dietary", "type": "textarea", "question": "Any dietary notes?", "placeholder": "Optional: allergies, preferences…", "required": false }
  ],
  "scoring": { "enabled": false },
  "outcomes": [],
  "ending": { "headline": "See you there!", "body": "We have saved your spot." }
}
```

`goto` with `target: null` jumps to the end: someone who cannot come is not asked about guests. Jumps are a paid feature (conditional logic); check `get_account.features.conditionalLogic`.

## Mixing shapes: worked examples

These are the point of this file. Each one presents, captures, qualifies and ends differently in a single short page. Use them as patterns for anything that looks alike (a car dealership test drive, a gym trial, a wedding venue visit, a clinic's first appointment) and invent your own mix when nothing fits; keep the length rules.

### Open house sign-in (real estate)

A sign holder at the door. The property first, then the visitor, then three questions that tell the agent whom to call, then the agent's card. Hot leads jump to the agent's calendar.

```json
{
  "steps": [
    { "key": "property", "type": "about", "question": "123 Maple Street · Open House", "helper": "3 bed · 2 bath · 1,850 sq ft · $745,000. Thanks for visiting today.", "required": false, "about": { "media": "image", "layout": "cover" } },
    { "key": "name", "type": "name", "question": "Your name", "required": true },
    { "key": "email", "type": "email", "question": "Your email", "helper": "For the listing details and the disclosure packet.", "required": true },
    { "key": "phone", "type": "phone", "question": "Your phone", "helper": "Optional.", "required": false },
    { "key": "agent", "type": "multiple_choice", "question": "Are you working with an agent?", "required": true, "optionLayout": "cards",
      "options": [ { "label": "Yes", "value": "yes", "icon": "🤝", "points": 0 }, { "label": "Not yet", "value": "no", "icon": "🔎", "points": 5 } ] },
    { "key": "timeline", "type": "multiple_choice", "question": "When are you looking to buy?", "required": true, "optionLayout": "cards",
      "options": [ { "label": "In the next 3 months", "value": "soon", "icon": "🔥", "points": 10 }, { "label": "3 to 6 months", "value": "mid", "icon": "📅", "points": 5 }, { "label": "Just looking", "value": "looking", "icon": "👀", "points": 0 } ] },
    { "key": "financing", "type": "multiple_choice", "question": "Financing", "required": false, "optionLayout": "cards",
      "options": [ { "label": "Pre-approved", "value": "preapproved", "icon": "✅", "points": 5 }, { "label": "Cash", "value": "cash", "icon": "💵", "points": 5 }, { "label": "Not yet", "value": "notyet", "icon": "⏳", "points": 0 } ] },
    { "key": "card", "type": "vcard", "question": "", "required": false,
      "vcard": { "layout": "card", "fullName": "Laura Gómez", "title": "Realtor", "company": "Maple Realty", "phoneMobile": "+1 555 010 0000", "email": "laura@maplerealty.com", "links": [ { "label": "More listings", "url": "https://maplerealty.com/listings" } ], "saveLabel": "Save my contact" } }
  ],
  "partialSubmitAfterStep": 3,
  "scoring": { "enabled": true },
  "outcomes": [
    { "id": "visitor", "label": "Thanks for stopping by!", "minScore": 0, "maxScore": 9, "message": "I will email you the listing details. Enjoy the rest of your visit." },
    { "id": "warm", "label": "Thanks! I will send you similar homes", "minScore": 10, "maxScore": 19, "message": "You will get this listing and a few like it this week." },
    { "id": "hot", "label": "Let's talk this week", "minScore": 20, "message": "I will call you tomorrow to answer any question about the house.", "redirectUrl": "https://calendly.com/maplerealty/visit", "redirectDelayMs": 5000 }
  ],
  "ending": {}
}
```

Photo: `imageSearches["property.imagen"]` with the kind of house ("modern two story house exterior"); the agent replaces it with the real listing photo in the editor. Ask for the real calendar link and the agent's card data.

### Hotel: today's experiences (QR in the room or the lobby)

Welcome, show what the hotel sells today, ask what they fancy, capture name, email and room. Guests who pick an experience go to the booking page; the rest get a welcome drink coupon, delivered on screen.

```json
{
  "steps": [
    { "key": "welcome", "type": "about", "question": "Welcome to Hotel Aurora", "helper": "Everything you can do today, in one place. Tell us what you fancy and the concierge takes it from there.", "required": false, "about": { "media": "image", "layout": "cover" } },
    { "key": "experiences", "type": "products", "question": "Today's experiences", "required": false,
      "products": { "items": [
        { "id": "catamaran", "title": "Sunset catamaran", "description": "6 pm, 2 hours, 45 € per person." },
        { "id": "wine", "title": "Wine tasting", "description": "8 pm at the cellar, 30 €." },
        { "id": "bikes", "title": "City bike tour", "description": "10 am, 3 hours, 25 €." }
      ] } },
    { "key": "interest", "type": "multiple_choice", "question": "What would you like to do?", "required": true, "optionLayout": "cards", "selectionMode": "multiple",
      "options": [ { "label": "Sunset catamaran", "value": "catamaran", "icon": "⛵", "points": 10 }, { "label": "Wine tasting", "value": "wine", "icon": "🍷", "points": 10 }, { "label": "City bike tour", "value": "bikes", "icon": "🚲", "points": 10 }, { "label": "Just relax at the hotel", "value": "relax", "icon": "🧘", "points": 0 } ] },
    { "key": "name", "type": "name", "question": "Your name", "required": true },
    { "key": "email", "type": "email", "question": "Your email", "helper": "We will send the details and your welcome drink.", "required": true },
    { "key": "room", "type": "text", "question": "Room number", "helper": "So the concierge can confirm with you.", "required": false }
  ],
  "partialSubmitAfterStep": 5,
  "scoring": { "enabled": true },
  "outcomes": [
    { "id": "relax", "label": "Enjoy your stay", "minScore": 0, "maxScore": 9, "message": "Your welcome drink is on us: show this screen at the bar." },
    { "id": "book", "label": "Great choice!", "minScore": 10, "message": "The concierge will confirm your spot. Taking you to the booking page in a moment…", "redirectUrl": "https://hotelaurora.example/experiences/book", "redirectDelayMs": 5000 }
  ],
  "ending": {}
}
```

The coupon lives outside the config, under the outcome id, delivered on screen without email confirmation:

```jsonc
"rewards": { "relax": { "type": "coupon_code", "value": "A free welcome drink at the bar", "requireConfirmation": false } }
```

### Tour agency (QR on a brochure or at a hotel desk)

Who the agency is, the tours, which one and when, how many, then name, email and WhatsApp. Travellers leaving in days go straight to WhatsApp; the ones still planning get an early-bird coupon by email, after confirming it.

```json
{
  "steps": [
    { "key": "agency", "type": "about", "question": "Andes Explorer", "helper": "Small-group tours from Cusco since 2012. Licensed guides, hotel pick-up, no hidden fees.", "required": false, "about": { "media": "image", "layout": "classic" } },
    { "key": "tours", "type": "products", "question": "Our tours", "required": false,
      "products": { "items": [
        { "id": "machu", "title": "Machu Picchu by train", "description": "Full day, from $190" },
        { "id": "rainbow", "title": "Rainbow Mountain", "description": "Full day, $45" },
        { "id": "valley", "title": "Sacred Valley", "description": "Full day, $55" }
      ] } },
    { "key": "tour", "type": "multiple_choice", "question": "Which tour are you interested in?", "required": true, "optionLayout": "cards",
      "options": [ { "label": "Machu Picchu", "value": "machu", "icon": "🏔️" }, { "label": "Rainbow Mountain", "value": "rainbow", "icon": "🌈" }, { "label": "Sacred Valley", "value": "valley", "icon": "🌄" }, { "label": "Not sure yet", "value": "unsure", "icon": "🤔" } ] },
    { "key": "when", "type": "multiple_choice", "question": "When would you go?", "required": true, "optionLayout": "cards",
      "options": [ { "label": "In the next 3 days", "value": "now", "icon": "🔥", "points": 10 }, { "label": "This month", "value": "month", "icon": "📅", "points": 5 }, { "label": "Still planning", "value": "planning", "icon": "🗺️", "points": 0 } ] },
    { "key": "people", "type": "slider", "question": "How many people?", "min": 1, "max": 12, "step": 1, "default": 2, "required": true },
    { "key": "name", "type": "name", "question": "Your name", "required": true },
    { "key": "email", "type": "email", "question": "Your email", "helper": "For the itinerary and your discount.", "required": true },
    { "key": "whatsapp", "type": "phone", "question": "WhatsApp", "helper": "Optional, for a faster reply.", "required": false }
  ],
  "partialSubmitAfterStep": 7,
  "scoring": { "enabled": true },
  "outcomes": [
    { "id": "planning", "label": "Your 10% early-bird discount", "minScore": 0, "maxScore": 4, "message": "Confirm your email and the code is yours, valid any date this year." },
    { "id": "soon", "label": "Let's lock your date", "minScore": 5, "message": "We reply within the hour. Taking you to WhatsApp to confirm…", "redirectUrl": "https://wa.me/51999999999?text=Hi%2C%20I%20want%20to%20book%20a%20tour", "redirectDelayMs": 5000 }
  ],
  "ending": {}
}
```

```jsonc
"rewards": { "planning": { "type": "coupon_code", "value": "10% off any tour, early-bird", "requireConfirmation": true } }
```

`requireConfirmation: true` sends the coupon only after the email is confirmed, which is why the email step is required. Photos: `agency.imagen` and one per tour card.

### Remodeling company: portfolio and quote request (QR on the van, a flyer, the showroom)

Their finished work first, then what the prospect has in mind, when and with what budget, then contact. Small or distant projects get a price range by email; near and sizeable ones get a call and a visit. The portfolio cards go without photos on purpose: the customer uploads the real ones.

```json
{
  "steps": [
    { "key": "portfolio", "type": "products", "question": "Reformas Castillo · Recent projects", "helper": "Kitchens, bathrooms and full renovations in Valencia since 2009. Licensed, insured, fixed-price quotes.", "required": false,
      "products": { "items": [
        { "id": "kitchen_ruzafa", "title": "Kitchen in Ruzafa", "description": "Full remodel, 6 weeks." },
        { "id": "bath_carmen", "title": "Bathroom in El Carmen", "description": "Walk-in shower, 3 weeks." },
        { "id": "loft_benimaclet", "title": "Loft in Benimaclet", "description": "Open plan, 10 weeks." }
      ] } },
    { "key": "project", "type": "multiple_choice", "question": "What do you have in mind?", "required": true, "optionLayout": "cards",
      "options": [ { "label": "Kitchen", "value": "kitchen", "icon": "🍳" }, { "label": "Bathroom", "value": "bathroom", "icon": "🛁" }, { "label": "Whole home", "value": "home", "icon": "🏠" }, { "label": "Something else", "value": "other", "icon": "🔨" } ] },
    { "key": "timeline", "type": "multiple_choice", "question": "When would you like to start?", "required": true, "optionLayout": "cards",
      "options": [ { "label": "As soon as possible", "value": "asap", "icon": "🔥", "points": 10 }, { "label": "In 1 to 3 months", "value": "soon", "icon": "📅", "points": 5 }, { "label": "Just gathering ideas", "value": "ideas", "icon": "💭", "points": 0 } ] },
    { "key": "budget", "type": "multiple_choice", "question": "Rough budget", "required": true, "optionLayout": "cards",
      "options": [ { "label": "Under 5,000 €", "value": "low", "icon": "💶", "points": 0 }, { "label": "5,000 to 20,000 €", "value": "mid", "icon": "💶💶", "points": 5 }, { "label": "Over 20,000 €", "value": "high", "icon": "💶💶💶", "points": 10 } ] },
    { "key": "name", "type": "name", "question": "Your name", "required": true },
    { "key": "email", "type": "email", "question": "Your email", "helper": "Where we send the quote.", "required": true },
    { "key": "phone", "type": "phone", "question": "Your phone", "helper": "If you prefer a call.", "required": false }
  ],
  "partialSubmitAfterStep": 6,
  "scoring": { "enabled": true },
  "outcomes": [
    { "id": "quote", "label": "Thanks! Your estimate is on its way", "minScore": 0, "maxScore": 9, "message": "We will email you a price range for your project within two working days, plus a guide on what a remodel like yours involves." },
    { "id": "call", "label": "Let's visit your place", "minScore": 10, "message": "We will call you within 24 hours to arrange a free visit and a fixed-price quote." }
  ],
  "ending": {}
}
```

No `imageSearches` for this one. Tell the customer: "the three project cards are waiting for your real photos; upload them in the editor before publishing". The same holds for a before-and-after page, a team page, a workshop's premises.

### Other mixes, in one line each

- **Counter loyalty (café, bakery):** `products` with today's specials, email, coupon on the thank-you.
- **Fair booth:** `about`, one qualifying question, name and email, the rep's `vcard`, hot leads to a calendar.
- **Waitlist (a launch, a class):** `about`, name, email, one choice (which date or plan), thank-you with the expected date.
- **Appointment request (clinic, salon, workshop):** `about`, choice of service in cards, preferred time in cards, name, phone, email; thank-you says when they will be called. The scheduler step is not offered; a booking link in the ending is.
- **Job application:** `about` the role, name, email, phone, two choice questions (availability, experience), one `textarea`, thank-you.
- **Menu with feedback:** `products` sections, then a `rating` with a conditional "what went wrong", endings by stars ([endings.md](endings.md)).
