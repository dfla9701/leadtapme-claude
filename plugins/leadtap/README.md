# LeadTap.me for Claude Code

Connects Claude to your LeadTap.me account through the LeadTap.me MCP server and teaches it the craft: how to build a lead page that works, how to route an NFC object or a QR code by schedule, how to send your leads to your CRM, a workflow or an AI agent, and how to read a week of leads.

LeadTap.me sells Smart Objects (NFC keychains, sign holders, touchpoints) and QR codes that open lead pages: short mobile forms that capture leads into a CRM. The MCP server gives Claude the same actions the portal has: lead pages, CRM, external links, Smart Objects with smart routing, analytics and the store.

## Install

```bash
claude plugin marketplace add dfla9701/leadtapme-claude
claude plugin install leadtap@leadtap
```

The first tool call opens your browser so you can sign in to LeadTap.me and allow the connection (OAuth, no keys to paste). You can revoke it any time from *Account & Billing → Connected apps* in the portal.

Without the plugin, the server alone: `claude mcp add --transport http leadtap https://app.leadtap.me/api/mcp`. In claude.ai, Claude Desktop and Cowork: *Settings → Connectors → Add custom connector* with the same URL.

## What is inside

- `.mcp.json`: the LeadTap.me MCP server, `https://app.leadtap.me/api/mcp`.
- `skills/lead-pages`: building, editing and publishing pages of any mix: a lead capture form, a digital contact card, a business presentation (about us, products & services, testimonials), a menu, a review flow, a qualifier, an RSVP, and mixes like an open house sign-in, a hotel's experiences page or a tour agency that qualifies and hands coupons. The steps that work on a phone, how endings and redirects really behave, scoring and outcomes without gaps, stock photos and where the customer replaces them, review flows that do not gate, and never overwriting what the customer edited in the portal.
- `skills/smart-routing`: reading where an object sends people, proposing a weekly schedule, checking overlaps, applying it and showing the week back.
- `skills/webhooks`: sending leads out. The CRM webhook (every lead, every page, to an external CRM, an AI agent workflow, Zapier, Make, n8n or the sales team) and the page webhook (one page's submissions, for a sheet or statistics): which one to offer, the flow (it starts off; the account owner turns it on after an email), what the receiver gets, the signature, retries, and reading the delivery log.
- `skills/weekly-leads`: the weekly review, numbers first, then people, then one or two actions.

The skills only use the server's tools, so they work with any account. In claude.ai, the same `SKILL.md` folders can be uploaded as custom skills.

## Try it

- "Create a lead page for my taco place: name, email and favourite dish. Don't publish it yet."
- "Send the entrance QR to the lunch menu on weekdays from 12 to 4 and to the dinner menu the rest of the time."
- "How did my lead pages do this week, and who should I call?"
- "An open house sign-in for Saturday: the property, the visitor, whether they have an agent, and my card at the end."
- "Send every lead in my account to my CRM through this Zapier URL." (The CRM webhook: every page, every person. It starts off; you turn it on in the portal after the email.)
- "Send the submissions of the fair page to this spreadsheet webhook." (A page webhook: that page's data.)

## Update

```bash
claude plugin update leadtap@leadtap
```

Support: the contact page at https://leadtap.me, where the MCP server (tools, authentication, what it never does) is documented.
