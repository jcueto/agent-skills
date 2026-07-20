---
name: stripe-checkout-sessions-management
description: Retrieve line items, list sessions, and expire active Stripe Checkout Sessions.
---

# Stripe Checkout Sessions Management

This skill enables an agent to interact with Stripe's Checkout Sessions API. It provides capabilities to list existing checkout sessions, retrieve the specific line items (purchased products/services) associated with a session, and programmatically expire active ("open") checkout sessions.

## When to Use

### Trigger Scenarios
* **Auditing Purchases**: When you need to verify what items a customer purchased or intended to purchase in a specific checkout session.
* **Session Lifecycle Management**: When an order is canceled, times out, or needs to be invalidated, requiring an active checkout session to be expired immediately.
* **Customer Support**: When listing recent checkout sessions for a specific customer, payment intent, or subscription to troubleshoot payment issues.

### Keywords & Phrases
* "list stripe checkout sessions"
* "get line items for session [id]"
* "expire checkout session [id]"
* "cancel checkout link [id]"
* "what did the customer buy in session [id]?"

### What NOT to Use For
* **Creating Checkout Sessions**: This skill does not cover the creation of new checkout sessions.
* **Direct Refund Processing**: Use the Refunds API skill instead of expiring checkout sessions for completed payments.
* **Product/Price Catalog Management**: Do not use this to create or update products and prices.

## Usage Instructions

### Prerequisites
* A valid Stripe Secret API Key (`sk_test_...` or `sk_live_...`).
* Authentication must be passed via Basic Auth (using the API key as the username with an empty password) or as a Bearer token in the `Authorization` header.

### Workflow
1. **List Sessions**: Retrieve a list of checkout sessions, optionally filtering by `customer`, `payment_intent`, `subscription`, or `status`.
2. **Retrieve Line Items**: Use the Checkout Session ID (`cs_...`) to fetch the paginated list of line items (including description, quantity, unit amount, and currency).
3. **Expire Session**: Invalidate an active session by sending a POST request to the expire endpoint.

### Constraints
* Only sessions with a status of `open` can be expired. Attempting to expire a session that is already `expired` or `complete` will result in an API error.
* Line items are paginated. If the response contains `has_more: true`, use `starting_after` with the last item ID to fetch the next page.

## Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `id` | string | Yes (for Retrieve/Expire) | The unique identifier of the Checkout Session (starts with `cs_`). |
| `limit` | integer | No | A limit on the number of objects to be returned (default is 10, max is 100). |
| `starting_after` | string | No | A cursor for pagination. An object ID defining your place in the list. |
| `ending_before` | string | No | A cursor for pagination. An object ID defining your place in the list. |
| `payment_intent` | string | No | Filter sessions by a specific PaymentIntent ID. |
| `subscription` | string | No | Filter sessions by a specific Subscription ID. |
| `customer` | string | No | Filter sessions by a specific Customer ID. |
| `status` | string | No | Filter sessions by status: `open`, `complete`, or `expired`. |

## Examples

### Example 1: Retrieve Line Items for a Checkout Session
**Trigger**: "Show me the items purchased in checkout session cs_test_a1enSAC01IA3Ps2vL32mNoWKMCNmmfUGTeEeHXI5tLCvyFNGsdG2UNA7mr"

**API Request**:
```bash
curl https://api.stripe.com/v1/checkout/sessions/cs_test_a1enSAC01IA3Ps2vL32mNoWKMCNmmfUGTeEeHXI5tLCvyFNGsdG2UNA7mr/line_items \
  -u "sk_test_REDACTED_SECRET_KEY_PLACEHOLDER:"
```

**Expected Response**: