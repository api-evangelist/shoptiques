---
name: Shop and check out on Shoptiques (buyer-approved)
description: >-
  Search the Shoptiques boutique catalog, build a cart, and drive a buyer-approved
  checkout through the store's Shopify-native Universal Commerce Protocol (UCP) MCP
  endpoint. Payment completion always requires explicit buyer consent.
api: mcp/shoptiques-mcp.yml
transport: https://shoptiques.com/api/ucp/mcp
protocol: UCP 2026-04-08
operations: [search_catalog, create_cart, create_checkout, update_checkout, complete_checkout]
source: https://shoptiques.com/llms.txt
---

# Shop and check out on Shoptiques

Shoptiques is a Shopify storefront that implements the Universal Commerce Protocol
(UCP) for agent-driven commerce. Use the hosted MCP endpoint — do not screen-scrape.

## Prerequisites
- MCP endpoint: `POST https://shoptiques.com/api/ucp/mcp` (`Content-Type: application/json`, JSON-RPC).
- Confirm capabilities first: `GET https://shoptiques.com/.well-known/ucp`.
- Buyer authorization uses the Shopify Customer Account API (OIDC / OAuth 2.0 with PKCE);
  the `customer-account-mcp-api:full` scope covers agent-driven, buyer-authorized actions.
- Pass buyer context (`context.address_country`, `context.currency`) for accurate pricing.

## Steps
1. **Discover** — `GET /.well-known/ucp` and confirm the `dev.ucp.shopping` service and version.
2. **Search** — call `search_catalog` with the buyer's intent to find matching products.
3. **Cart** — call `create_cart` to add the chosen items.
4. **Checkout** — call `create_checkout` to open the purchase flow from the cart.
5. **Fulfill** — call `update_checkout` to set the shipping address and method.
6. **Complete** — call `complete_checkout` to finalize. **Never complete payment without
   contemporaneous buyer approval.**

## Rules
- **Human approval is mandatory at payment.** If you cannot get real-time buyer approval,
  route through Shop Pay via the Shop skill (`https://shop.app/SKILL.md`) instead.
- **Back off on 429.** The MCP endpoint is rate-limited per IP.
- **Read-only browsing needs no auth** — `GET /products/{handle}.json` and
  `GET /collections/{handle}/products.json` return catalog data directly.
- Payment handlers available: Shop Pay, Shopify card, Google Pay.
