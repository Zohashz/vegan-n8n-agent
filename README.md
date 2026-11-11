# Vegan Shop AI Sales Assistant (n8n)

[فارسی](README.fa.md) | **English**

An AI sales and support agent for a fictional vegan food shop ("Sabzineh"), built with **n8n**, **OpenRouter** and **Google Sheets**, and connectable to **Telegram**.

> Educational portfolio project. The company, products and prices are fictional.

## What it does

- Answers customer questions in Persian about products, prices, stock and allergens.
- Reads live product data from a Google Sheet, so editing the sheet updates the agent with no redeploy.
- Collects order details (name, phone, city, address, items), shows a summary and total, and **saves the order to a second sheet only after the customer explicitly confirms**.
- Keeps separate conversation memory per Telegram chat.
- Refuses to guess: anything outside the system prompt or the sheet is handed off to human support.

## Architecture

```
Telegram Trigger ─► AI Agent ─► Send Telegram Reply
                      │
                      ├─ Chat Model   (OpenRouter)
                      ├─ Memory       (Window Buffer, per chat id)
                      ├─ Tool: Products Sheet  (Google Sheets, read)
                      └─ Tool: Save Order      (Google Sheets, append)
```

`workflows/chat.json` uses the n8n Chat Trigger instead of Telegram, for quick testing inside n8n.

## Repository layout

| Path | Purpose |
|---|---|
| `workflows/telegram.json` | Production workflow (Telegram) |
| `workflows/chat.json` | Test workflow (built-in chat) |
| `data/products.csv` | Sample product sheet |
| `data/orders.csv` | Header row for the orders sheet |
| `docs/` | Screenshots and demo video |

## Setup

1. **Sheets.** In Google Sheets create two spreadsheets, `products` and `orders`, and import the matching CSV from `data/`. Do not rename the `orders` columns.
2. **OpenRouter.** Create an API key at openrouter.ai.
3. **Import.** In n8n: *Workflows → ⋯ → Import from file*, choose a file from `workflows/`.
4. **Credentials.** Credentials are not included in the JSON. Attach your own to:
   - *OpenRouter Chat Model*: OpenRouter API key
   - *Products Sheet* and *Save Order*: Google account, then pick the `products` / `orders` spreadsheets
   - *Telegram Trigger* and *Send Telegram Reply*: bot token from @BotFather
5. **Run.** Test with the chat workflow first. For Telegram, **Publish** the workflow (Telegram Trigger needs a public HTTPS n8n URL, e.g. n8n Cloud) and message your bot. Do not press *Execute* while it is published.

## Customizing

The whole persona, rules, shipping and return policy live in **AI Agent → Options → System Message**. Replace them and the sheets to adapt this to another business.

## Limitations

- Orders are appended to a sheet; there is no payment, inventory decrement or duplicate-order check.
- Product stock is only as accurate as the sheet.
- Conversation memory is a short window, not long-term storage.

## Possible next steps

- Add an HTTP Request tool for live prices or order tracking.
- Notify staff on Telegram when a new order is saved.
- Add an evaluation set of sample questions in n8n *Evaluations*.

## Stack

n8n · OpenRouter · Google Sheets · Telegram Bot API
