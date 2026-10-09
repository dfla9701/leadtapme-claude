# LeadTap.me for Claude

The Claude Code plugin for [LeadTap.me](https://leadtap.me): the LeadTap.me MCP server plus four skills that teach Claude the craft. Build lead pages that work, route NFC and QR Smart Objects by schedule, send your leads to your CRM or your AI workflows by webhook, and review a week of leads.

LeadTap.me sells Smart Objects (NFC keychains, sign holders, touchpoints) and QR codes that open lead pages: short mobile pages that capture leads into a CRM. The MCP server at `https://app.leadtap.me/api/mcp` gives Claude the same actions the portal has, on your own account, with OAuth and no keys to paste.

## Install in Claude Code

```bash
claude plugin marketplace add dfla9701/leadtapme-claude
claude plugin install leadtap@leadtap
```

The first tool call opens your browser so you can sign in to LeadTap.me and allow the connection. You can revoke it any time from *Account & Billing → Connected apps* in the portal.

Update later with `claude plugin update leadtap@leadtap`.

## Use it in claude.ai, Claude Desktop or Cowork

1. *Settings → Connectors → Add custom connector* with `https://app.leadtap.me/api/mcp`, then sign in and allow.
2. Optional, per user: *Settings → Skills → upload* any of the folders in `plugins/leadtap/skills/` (zip the folder; each one has its `SKILL.md`). Without them Claude still works; with them it builds pages and webhooks the way the portal expects.

## The skills

- **lead-pages**: building, editing and publishing pages of any mix: a lead capture form, a digital contact card, a business presentation, a menu, a review flow, a qualifier, an RSVP, and mixes like an open house sign-in, a hotel's experiences page, a tour agency or a remodeling company's portfolio with a quote request. How endings and redirects really behave, scoring without gaps, stock photos and where you replace them, review flows that do not gate, and never overwriting what you edited in the portal.
- **smart-routing**: reading where an object sends people, proposing a weekly schedule, checking overlaps, applying it and showing the week back.
- **webhooks**: sending leads out. The CRM webhook (every lead, every page, to an external CRM, an AI agent workflow, Zapier, Make, n8n or your sales team) and the page webhook (one page's submissions, for a sheet or statistics): which one to use, the flow (it starts off; you turn it on in the portal after an email), what your receiver gets, the signature, retries and the delivery log.
- **weekly-leads**: the weekly review, numbers first, then people, then one or two actions.

## Try it

- "Create a lead page for my taco place: name, email and favourite dish. Don't publish it yet."
- "An open house sign-in for Saturday: the property, the visitor, whether they have an agent, and my card at the end."
- "Send every lead in my account to my CRM through this Zapier URL."
- "Send the entrance QR to the lunch menu on weekdays from 12 to 4 and to the dinner menu the rest of the time."
- "How did my lead pages do this week, and who should I call?"

## What is inside

- `.claude-plugin/marketplace.json`: the marketplace, one plugin.
- `plugins/leadtap/.mcp.json`: the LeadTap.me MCP server.
- `plugins/leadtap/skills/*`: the four skills, plain Markdown.

## Documentation and support

- The MCP server, its tools, authentication and what it never does: [docs.leadtap.me/mcp](https://docs.leadtap.me/mcp/).
- These skills and the plugin: [docs.leadtap.me/mcp/claude-code-plugin](https://docs.leadtap.me/mcp/claude-code-plugin/).
- Webhooks, what the receiver gets and how to verify the signature: [docs.leadtap.me/concepts/webhooks](https://docs.leadtap.me/concepts/webhooks/).
- Plans and prices: [leadtap.me/en/pricing](https://www.leadtap.me/en/pricing).
- Support: [the contact page](https://www.leadtap.me/en/contact).
