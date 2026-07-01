---
name: ecommerce-integration-workflow
when_to_use: When the user wants to integrate an e-commerce platform (Stripe, WooCommerce, Shopify, etc.) for order management, payments, invoicing, and sales reporting.
---

# Integrate an e-commerce platform

## Purpose

Integrate e-commerce platforms such as Stripe, WooCommerce, or Shopify for order management and payments.

## Steps

1. Ask the user which platform to integrate.
2. Connect using API keys or OAuth credentials. Calls to the platform's HTTP API can be made with the `invoke-http` tool, or wrapped in saved endpoints with the `generate-hyperlambda` tool (pass a `filename` so they are automatically exposed as MCP tools).
3. Retrieve product, order, and customer data from the platform's API.
4. Generate PDF invoices with the `create-pdf-file-from-html` tool and send confirmation emails with the `send-email` tool.
5. Add analytics for sales performance and revenue tracking — store data with the database tools (`create-sqlite-database`, `execute-sql`, `select-sql`) and report on it.

## Outcome

A unified e-commerce integration workflow that automates order processing, invoicing, and reporting.
