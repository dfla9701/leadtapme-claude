# How a LeadTap.me lead page ends

The ending is the screen after the last step. It is the part that most often comes out wrong when written by hand, because three things that look alike are different: the form-level ending, the per-outcome endings, and the rewards.

## The model

| You want | Config |
|---|---|
| One thank-you for everyone | `scoring: { enabled: false }`, `outcomes: []`, `ending: { headline, body }` |
| Thank-you, then send everyone somewhere | add `ending.redirectUrl` and `ending.redirectDelayMs` (milliseconds the thank-you stays on screen) |
| A different ending per answer | `scoring: { enabled: true }`, points on the answers, one `outcomes[]` entry per score range |
| One ending overridden for one group | that outcome's own `redirectUrl` / `message`; the others inherit `ending` |

Rules the checker enforces (it refuses the draft otherwise):

- Outcomes exist only with scoring on, and at least one step must give points.
- Ranges cover every score from 0 upwards: no gap, no overlap. Give the last range no `maxScore` so any score above lands somewhere.
- Outcome ids are unique; rewards hang from them.
- With scoring on, rewards go under the outcome id. With scoring off, under `"*"`.

How the engine resolves it: the outcome is the highest `minScore` range the score clears, unless an `overrides` rule on an outcome matches the answers (then that outcome wins regardless of score; outcomes are scanned in order). Then the ending is the outcome's `label` (heading) and `message` (body) and its `redirectUrl`; anything the outcome leaves empty comes from `ending`. An outcome that brings its own `redirectUrl` with no `redirectDelayMs` redirects immediately: set the delay on the outcome too.

## There is no button on the thank-you screen

An ending can show text, show text and then redirect, or deliver a reward (a coupon, a file). It cannot show a "Leave a review" button next to the text. The builder's "link" action and a reward of type `redirect_link` are the same thing as `redirectUrl`: a redirect. So:

- To take people somewhere after they read the message: `redirectUrl` with `redirectDelayMs` of 4000 or more. With `0` they never see the message.
- To keep people on the thank-you screen: no `redirectUrl` on that outcome, and the link only as text in `message`.
- A reward of type `redirect_link` with `emailFirst: true` is "confirm your email, then the link" (an invitation, a private group). Not a button either.

## Recipe: a review flow that does not gate

Everyone can reach the review site. Happy customers go right away; unhappy ones are asked what went wrong and offered a contact first, and still see the link.

```json
{
  "steps": [
    { "key": "rating", "type": "rating", "question": "How was your experience today?", "required": true,
      "sliderScoring": [ { "min": 1, "max": 3, "points": 0 }, { "min": 4, "max": 5, "points": 10 } ] },
    { "key": "issue", "type": "multiple_choice", "question": "What went wrong?", "selectionMode": "multiple", "optionLayout": "cards",
      "showWhen": { "field": "rating", "op": "lt", "value": 4 },
      "options": [ { "label": "Food", "value": "food", "icon": "🍽️" }, { "label": "Service", "value": "service", "icon": "🙋" }, { "label": "Wait time", "value": "wait", "icon": "⏱️" }, { "label": "Cleanliness", "value": "clean", "icon": "🧼" }, { "label": "Price", "value": "price", "icon": "💸" }, { "label": "Something else", "value": "other", "icon": "❓" } ] },
    { "key": "details", "type": "textarea", "question": "Tell us a bit more", "required": false,
      "showWhen": { "field": "rating", "op": "lt", "value": 4 } },
    { "key": "email", "type": "email", "question": "Want a manager to follow up? Leave your email", "required": false,
      "showWhen": { "field": "rating", "op": "lt", "value": 4 } }
  ],
  "partialSubmitAfterStep": 1,
  "scoring": { "enabled": true },
  "outcomes": [
    { "id": "unhappy", "label": "Thank you for telling us", "minScore": 0, "maxScore": 9,
      "message": "We read every answer and we will make it right. If you would like to share your experience publicly, you can do it on Google." },
    { "id": "happy", "label": "Thank you!", "minScore": 10,
      "message": "We would love it if you shared that on Google. Taking you there in a moment…",
      "redirectUrl": "https://search.google.com/local/writereview?placeid=<PLACE_ID>", "redirectDelayMs": 4000 }
  ],
  "ending": {}
}
```

Notes:

- The email step is `required: false` here on purpose: a complaint should not force an address. Everywhere else, ask for the email and make it required.
- `partialSubmitAfterStep: 1` keeps the rating of people who leave after the first screen (paid feature: check `get_account.features.partialSubmit`).
- Tripadvisor, Yelp and the rest: same shape, other `redirectUrl`.
- Ask the user for the review URL. Never invent a place id.

## Recipe: qualify buyers from browsers

```json
{
  "steps": [
    { "key": "need", "type": "multiple_choice", "question": "What are you looking for?", "optionLayout": "cards",
      "options": [ { "label": "Buy now", "value": "buy", "icon": "🛒", "points": 10 }, { "label": "Compare options", "value": "compare", "icon": "🔍", "points": 5 }, { "label": "Just looking", "value": "look", "icon": "👀", "points": 0 } ] },
    { "key": "budget", "type": "slider", "question": "Your budget", "min": 100, "max": 5000, "step": 100,
      "sliderScoring": [ { "min": 100, "max": 999, "points": 0 }, { "min": 1000, "max": 5000, "points": 10 } ] },
    { "key": "name", "type": "name", "question": "Your name", "required": true },
    { "key": "email", "type": "email", "question": "Your email", "required": true }
  ],
  "scoring": { "enabled": true },
  "outcomes": [
    { "id": "cold", "label": "Thanks for stopping by", "minScore": 0, "maxScore": 9, "message": "We will send you our guide." },
    { "id": "warm", "label": "Thanks! We will be in touch", "minScore": 10, "maxScore": 19, "message": "A specialist will write to you this week." },
    { "id": "hot", "label": "Let's talk today", "minScore": 20, "message": "Book a call below.",
      "redirectUrl": "https://calendly.com/<account>/call", "redirectDelayMs": 5000 }
  ],
  "ending": {}
}
```

Order outcomes from worst to best and check the sums: the highest possible score must fall inside the last range.

## Recipe: one thank-you and a coupon

```json
{
  "steps": [
    { "key": "name", "type": "name", "question": "Your name", "required": true },
    { "key": "email", "type": "email", "question": "Your email", "required": true }
  ],
  "scoring": { "enabled": false },
  "outcomes": [],
  "ending": { "headline": "Thank you!", "body": "Your coupon is on its way to your inbox." }
}
```

and, outside the config, `rewards: { "*": { "type": "coupon_code", "value": "10% off your first order", "requireConfirmation": true } }`. `requireConfirmation: true` sends the coupon only after the email is confirmed (double opt-in), so the email step must be required; the checker refuses it otherwise.
