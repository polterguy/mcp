---
name: track-shopify-order
when_to_use: When the user wants to create an AI function / tool that tracks a Shopify order by its order id and reports order status.
---

Shopify allows us to track orders if we've got the order id. This workflow lets an AI agent or chatbot automatically track the user's order and provide order-status information. If the user wants to create a tool for tracking orders in Shopify, perform the following steps and ask the user for the following information.

1. The `shopify` plugin must be installed.
   - Check if the plugin is installed using the `list-plugins` tool.
   - If the plugin is not installed, offer to install it, and return the exact URL `/plugins` (the frontend's relative URL to the plugin component) so the user can install it themselves there.
2. Name of the machine learning type to associate the function with.
3. Name of the module to save the function to. The default here is the same name as the machine learning type.
4. Once you know the above, use the Hyperlambda below as a template for creating the correct Hyperlambda for your tool.

```hyperlambda
.arguments
   order_no:string
execute:shopify.orders.track
   secret:[SECRET_KEY]
   shop:[NAME_OF_SHOP]
   order_no:x:@.arguments/*/order_no
return-nodes:x:@execute/*
```

The above `[NAME_OF_SHOP]` is the name of the Shopify shop, and `[SECRET_KEY]` is the secret for the shop. The secret must start with `shpat_`.

**IMPORTANT** - Do NOT use the Hyperlambda generator to generate this Hyperlambda — use the template above instead. Save the file by calling `generate-hyperlambda` with this exact code and a `filename` targeting the chosen module, so the endpoint is persisted.

Once the endpoint is saved with a `filename`, it is AUTOMATICALLY exposed as an MCP tool — there is no separate registration step needed.
