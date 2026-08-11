---
name: stripe-customer-management
description: Create, retrieve, and update customer profiles in Stripe.
---

# Stripe Customer Management

This skill enables an AI agent to manage customer profiles within Stripe. It supports creating new customer records, retrieving existing customer details, and updating customer profiles (such as changing contact information, adding metadata, or updating payment configurations).

## When to Use

### Trigger Scenarios
* **Onboarding New Users:** When a new user signs up and needs to be registered in the billing system.
* **Updating Contact/Billing Info:** When a customer changes their email, name, phone number, or billing/shipping address.
* **Attaching Payment Methods:** When linking a payment method or source to a customer profile.
* **Adding Metadata:** When syncing internal database IDs or order IDs to a Stripe customer record for tracking.
* **Retrieving Customer Details:** Before generating invoices, processing payments, or checking subscription statuses.

### Keywords & Phrases
* "Create a Stripe customer"
* "Update customer email in Stripe"
* "Get Stripe customer details"
* "Add billing address to customer"
* "Link payment method to customer `cus_...`"

### When NOT to Use
* **Processing Charges Directly:** Do not use this skill to charge a card directly. Use the Charges or PaymentIntents API instead.
* **Managing Subscriptions:** Do not use this skill to subscribe a customer to a plan. Use the Subscriptions API.
* **Handling Refunds:** Do not use this skill to refund payments.

## Usage Instructions

### Prerequisites
* **Stripe Secret API Key:** You must authenticate requests using your Stripe secret API key (`sk_test_...` or `sk_live_...`) passed via Basic Authentication or as a Bearer token.

### Typical Workflow
1. **Create:** Initiate a `POST` request to `/v1/customers` with the customer's name, email, and optional metadata.
2. **Retrieve:** Use the returned customer ID (e.g., `cus_NffrFeUfNV2Hib`) with a `GET` request to `/v1/customers/:id` to verify details.
3. **Update:** Modify existing customer records using a `POST` request to `/v1/customers/:id` to append metadata, update addresses, or change default payment sources.

### Constraints & Limits
* **Email Length:** Maximum of 512 characters.
* **Name Length:** Maximum of 256 characters.
* **Phone Length:** Maximum of 20 characters.
* **Metadata:** Key-value pairs where keys can be unset by posting an empty value.

---

## Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `id` | string | Yes (for Retrieve/Update) | The unique identifier of the customer (starts with `cus_`). |
| `email` | string | No | Customer’s email address. Max 512 characters. |
| `name` | string | No | The customer’s full name or business name. Max 256 characters. |
| `phone` | string | No | The customer’s phone number. Max 20 characters. |
| `description` | string | No | An arbitrary string displayed alongside the customer in the dashboard. |
| `metadata` | map/object | No | Set of key-value pairs for storing custom structured information. |
| `address` | object | No | The customer’s address. Required if calculating taxes. |
| `shipping` | object | No | The customer’s shipping information. Appears on invoices. |
| `payment_method` | string | No | The ID of the PaymentMethod to attach to the customer (Create only). |
| `source` | string | No | The ID of a payment source (e.g., card) to attach as the active source. |
| `default_source` | string | No | The ID of the default payment source (Update only). |
| `tax` | object | No | Tax details about the customer. Recommended if calculating taxes. |

---

## Expected Response

### Success Response Structure
A successful request returns a **Customer Object** (JSON) containing:
* `id` (string): Unique identifier starting with `cus_`.
* `object` (string): Value is always `"customer"`.
* `address` (object/null): Customer's billing address.
* `balance` (integer): Current balance in minor units (e.g., cents).
* `created` (integer): Unix timestamp of creation.
* `email` (string/null): Customer's email address.
* `invoice_prefix` (string): Unique prefix for the customer's invoices.
* `metadata` (object): Custom key-value pairs.
* `name` (string/null): Customer's name.

### Example Successful Response
```json
{
  "id": "cus_NffrFeUfNV2Hib",
  "object": "customer",
  "address": null,
  "balance": 0,
  "created": 1680893993,
  "currency": null,
  "default_source": null,
  "delinquent": false,
  "description": null,
  "email": "jennyrosen@example.com",
  "invoice_prefix": "0759376C",
  "invoice_settings": {
    "custom_fields": null,
    "default_payment_method": null,
    "footer": null,
    "rendering_options": null
  },
  "livemode": false,
  "metadata": {},
  "name": "Jenny Rosen",
  "next_invoice_sequence": 1,
  "phone": null,
  "preferred_locales": [],
  "shipping": null,
  "tax_exempt": "none",
  "test_clock": null
}
```

### Error Responses
* **400 Bad Request:** Raised if parameters are invalid (e.g., specifying an invalid payment source or exceeding character limits).
* **401 Unauthorized:** Raised if the API key is missing or invalid.
* **404 Not Found:** Raised if attempting to retrieve or update a customer ID that does not exist.

---

## Examples

### Example 1: Create a new customer
**Trigger:** "Create a new customer profile for Jenny Rosen with email jennyrosen@example.com"

**API Call:**
```bash
curl https://api.stripe.com/v1/customers \
  -u "sk_test_REDACTED_SECRET_KEY_PLACEHOLDER:" \
  -d "name=Jenny Rosen" \
  --data-urlencode "email=jennyrosen@example.com"
```

**Expected Response:**
```json
{
  "id": "cus_NffrFeUfNV2Hib",
  "object": "customer",
  "address": null,
  "balance": 0,
  "created": 1680893993,
  "currency": null,
  "default_source": null,
  "delinquent": false,
  "description": null,
  "email": "jennyrosen@example.com",
  "invoice_prefix": "0759376C",
  "invoice_settings": {
    "custom_fields": null,
    "default_payment_method": null,
    "footer": null,
    "rendering_options": null
  },
  "livemode": false,
  "metadata": {},
  "name": "Jenny Rosen",
  "next_invoice_sequence": 1,
  "phone": null,
  "preferred_locales": [],
  "shipping": null,
  "tax_exempt": "none",
  "test_clock": null
}
```

### Example 2: Update a customer's metadata
**Trigger:** "Add order ID 6735 to customer cus_NffrFeUfNV2Hib's metadata"

**API Call:**
```bash
curl https://api.stripe.com/v1/customers/cus_NffrFeUfNV2Hib \
  -u "sk_test_REDACTED_SECRET_KEY_PLACEHOLDER:" \
  -d "metadata[order_id]=6735"
```

**Expected Response:**
```json
{
  "id": "cus_NffrFeUfNV2Hib",
  "object": "customer",
  "address": null,
  "balance": 0,
  "created": 1680893993,
  "currency": null,
  "default_source": null,
  "delinquent": false,
  "description": null,
  "email": "jennyrosen@example.com",
  "invoice_prefix": "0759376C",
  "invoice_settings": {
    "custom_fields": null,
    "default_payment_method": null,
    "footer": null,
    "rendering_options": null
  },
  "livemode": false,
  "metadata": {
    "order_id": "6735"
  },
  "name": "Jenny Rosen",
  "next_invoice_sequence": 1,
  "phone": null,
  "preferred_locales": [],
  "shipping": null,
  "tax_exempt": "none",
  "test_clock": null
}
```

### Example 3: Retrieve a customer profile
**Trigger:** "Get customer details for ID cus_NffrFeUfNV2Hib"

**API Call:**
```bash
curl https://api.stripe.com/v1/customers/cus_NffrFeUfNV2Hib \
  -u "sk_test_REDACTED_SECRET_KEY_PLACEHOLDER:"
```

**Expected Response:**
```json
{
  "id": "cus_NffrFeUfNV2Hib",
  "object": "customer",
  "address": null,
  "balance": 0,
  "created": 1680893993,
  "currency": null,
  "default_source": null,
  "delinquent": false,
  "description": null,
  "email": "jennyrosen@example.com",
  "invoice_prefix": "0759376C",
  "invoice_settings": {
    "custom_fields": null,
    "default_payment_method": null,
    "footer": null,
    "rendering_options": null
  },
  "livemode": false,
  "metadata": {},
  "name": "Jenny Rosen",
  "next_invoice_sequence": 1,
  "phone": null,
  "preferred_locales": [],
  "shipping": null,
  "tax_exempt": "none",
  "test_clock": null
}
```

---

## Guidelines

* **Authentication:** Always include the Stripe Secret Key in the request header. Never expose this key in client-side code.
* **Updating Metadata:** To delete or unset a metadata key, pass an empty string as its value. To clear all metadata, pass an empty value directly to the `metadata` parameter.
* **Tax Calculations:** If you plan to use Stripe Tax to calculate taxes automatically, ensure you provide a valid `address` object.
* **Subscription Retry Behavior:** When updating a customer with a new payment `source`, Stripe will automatically retry payment for any open, past-due invoices belonging to active subscriptions. This behavior does not trigger when updating `default_source`.