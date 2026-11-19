# Pakpays

<p align="center">
  <strong>A clearer way for Pakistani businesses to accept and reconcile digital payments.</strong><br />
  Public project · Community hub · Pakistan-focused
</p>

<p align="center">
  <a href="https://github.com/whyumar/Pakpays/issues">Issues</a> ·
  <a href="https://github.com/whyumar/Pakpays/issues/new/choose">Request an improvement</a> .
  <a href="https://github.com/whyumar/Pakpays/issues/new/choose">Request a Bank</a> .
  <a href="mailto:info@umar.is-a.dev">Contact</a>
</p>

---

## What is Pakpays?

Pakpays is a Pakistan-focused merchant checkout and payment-reconciliation project. It is designed to help merchants create payment orders, present a hosted checkout experience, and reconcile incoming bank or wallet payment notifications against those orders.

The project brings together a merchant-facing API, hosted checkout, bank-alert parsing, exception review, and signed webhook notifications.

> **Important:** Pakpays is software for payment workflow coordination. It does not hold customer funds or move money. Payments are made directly to the merchant's own bank account or wallet. An email notification is not, by itself, authoritative proof that funds have settled.

## At a glance

- **Hosted checkout** — create an order and direct a customer to a checkout experience.
- **Merchant API** — create and check payment orders using API credentials.
- **Payment reconciliation** — match incoming payment notifications against pending orders.
- **Human review** — keep uncertain or unsupported matches visible for merchant review.
- **Signed webhooks** — notify merchant systems about order events using HMAC signatures.
- **Pakistan-focused integrations** — provider-specific parsers normalize bank and wallet notification formats.

## How it works

1. A merchant creates a payment order through the API.
2. Pakpays returns an order reference and checkout destination.
3. The customer pays directly to the merchant's configured bank account or wallet.
4. A supported payment notification may be ingested and checked against the order.
5. The order is reconciled only according to configured provider-specific verification rules; uncertain cases remain for review.
6. Pakpays sends a signed webhook to the merchant's configured endpoint when applicable.

## Using Pakpays

This repository is the **public project page and community hub** for Pakpays. The application source code and deployment configuration are maintained separately in a private repository, so cloning this public repository will not install or run the Pakpays application.

If you are a merchant interested in using Pakpays, contact [info@umar.is-a.dev](mailto:info@umar.is-a.dev) to ask about access and onboarding. API access, credentials, and the correct service URL are provided to authorized integrations directly; they are not published in this repository.

If you are here to follow development, suggest an improvement, report a problem, or request support for a bank or wallet, use the issue forms linked below.

## Merchant API

All production API requests should use HTTPS. Authenticate with the x-api-key request header.

### Create an order

~~~http
POST /api/v1/create
Content-Type: application/json
x-api-key: YOUR_API_KEY
~~~

Example request:

~~~json
{
  "amount": 1500,
  "currency": "PKR",
  "order_id": "ORDER-10001",
  "callback_url": "https://merchant.example.com/payment/callback"
}
~~~

Use the exact request fields and validation rules exposed by the deployed API version. This example illustrates a merchant order request and is not a substitute for server-side validation.

### Check an order

~~~http
GET /api/v1/check?order_id=ORDER-10001
x-api-key: YOUR_API_KEY
~~~

The check endpoint returns the current status available for the supplied order reference. Merchants should not infer settlement from a browser redirect or an email alone.

## Technical details

<details>
<summary><strong>API endpoints and integration notes</strong></summary>

| Method | Endpoint | Purpose |
| --- | --- | --- |
| POST | /api/v1/create | Create a merchant payment order |
| GET | /api/v1/check?order_id=... | Check an order's current status |

**Authentication:** send the merchant API key in the x-api-key header.

**Integration recommendations:**
- Generate a unique order ID for every payment attempt.
- Keep API keys on your server; never expose them in browser JavaScript or mobile app bundles.
- Validate amounts and order references on your server.
- Make webhook processing idempotent.
- Do not treat a customer redirect as proof of payment.
- Reconcile the final amount and reference with your own merchant records.

</details>

<details>
<summary><strong>Webhooks and signature verification</strong></summary>

Pakpays webhook deliveries are signed using HMAC-SHA256. Merchants should verify the signature using the shared webhook secret and the exact raw request body before trusting the payload.

Webhook delivery should be treated as **at-least-once**: the same event may be delivered more than once. Store processed event identifiers (or another stable deduplication key) and make handlers idempotent.

Recommended receiver behavior:

1. Read the raw request body.
2. Recompute the HMAC-SHA256 signature with your webhook secret.
3. Compare signatures using a constant-time comparison.
4. Reject invalid signatures.
5. Deduplicate the event.
6. Process the event and return a successful response promptly.

The exact signature header and payload schema should be taken from the implementation version you deploy. Do not guess or hard-code a header name from this overview.

</details>

<details>
<summary><strong>Architecture and technology</strong></summary>

The project uses these core technologies and patterns:

- **Next.js App Router** for the application and API routes.
- **Vercel** as the deployment platform.
- **Supabase Auth and PostgreSQL** for authentication and application data.
- **AES-256-GCM** for encryption of sensitive stored configuration.
- **HMAC-based credentials and webhook signatures** for request authentication and message integrity.
- **Gmail IMAP over TLS** for the configured email-notification ingestion workflow.
- **Provider-specific parser registry** to normalize bank and wallet notification formats.

The architecture separates order creation, notification ingestion, provider-specific parsing, reconciliation, exception handling, and merchant notification.

</details>

<details>
<summary><strong>Payment verification and provider limitations</strong></summary>

Payment notification emails can be delayed, duplicated, incomplete, or spoofed. A matching email is not equivalent to confirmed bank settlement.

Broad automatic verification across all banks is not enabled. The current implementation has a narrow NayaPay running-order exception with sender authentication and additional amount, timestamp, and unique-order checks. Other cases should remain subject to merchant review.

Provider support and verification behavior can change as integrations are tested. A bank integration request is not a promise that a provider is supported or that automatic verification will be enabled.

**No direct Raast or payment-switch integration is implied by this repository overview.** Confirm the capabilities of the exact deployment before relying on any payment flow.

</details>

<details>
<summary><strong>Deployment and configuration</strong></summary>

The application source code and production configuration are maintained in a separate private repository. This public repository intentionally does not provide clone instructions, a local development setup, environment files, or deployment secrets.

For implementation or deployment access, contact [info@umar.is-a.dev](mailto:info@umar.is-a.dev). Never request or share credentials, private configuration, or customer payment data in public issues.

</details>

<details>
<summary><strong>Production readiness checklist</strong></summary>

Before using a deployment with real customers:

- [ ] Set all required production environment variables in the deployment platform.
- [ ] Confirm database policies, access controls, and backups.
- [ ] Rotate any credentials that may have been exposed during development.
- [ ] Configure and test webhook signature verification and event deduplication.
- [ ] Test each provider parser with representative, redacted messages.
- [ ] Keep unsupported or ambiguous payment notifications in manual review.
- [ ] Configure and monitor scheduled jobs; do not assume they are active by default.
- [ ] Complete applicable legal, compliance, KYC/AML, and provider approvals independently.
- [ ] Perform security and load testing before production use.

</details>

## Roadmap and current limitations

Pakpays is under active development. Do not assume the following are production-ready unless verified in the deployed version:

- Scheduled reconciliation/cron workflows may require additional setup.
- Provider coverage is limited to implemented and configured parsers.
- Some payment notifications require manual merchant review.
- Direct bank, Raast, or PSP connectivity is not provided merely by parsing notification emails.
- KYC/AML processes, regulatory approvals, and provider commercial agreements are outside the scope of this codebase.
- Paid-plan billing is not part of the current feature set.

## Contributing

Issues, ideas, and integration requests are welcome. Please use the issue forms:

- [Report a bug](https://github.com/whyumar/Pakpays/issues/new?template=bug_report.yml)
- [Request a feature](https://github.com/whyumar/Pakpays/issues/new?template=feature_request.yml)
- [Request a bank or wallet integration](https://github.com/whyumar/Pakpays/issues/new?template=bank_integration.yml)

For bank integration requests, **first forward a relevant payment notification email to info@umar.is-a.dev**, then submit the form. Redact sensitive personal or account information wherever possible. Never attach passwords, API keys, or login links.

Please read CONTRIBUTING.md before opening a pull request.

## Security

Please do not report exploitable vulnerabilities in public issues. Follow SECURITY.md for responsible disclosure instructions. Never publish secrets, access tokens, full bank statements, or unredacted payment emails.

## Disclaimer

Pakpays is provided for development and evaluation. It is not a bank, wallet, payment service provider, or financial institution. It does not custody funds. Merchants are responsible for independently confirming settlement, securing their integrations, meeting applicable legal and regulatory obligations, and obtaining required provider approvals.

## License

No license has been declared in this repository yet. Until a license is added, the code is not granted an open-source license; please do not assume permission to reuse, modify, or redistribute it.
