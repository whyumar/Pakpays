# Contributing to Pakpays

Thank you for your interest in improving Pakpays.

## Before opening an issue

- Search existing issues to avoid duplicates.
- Use the appropriate issue form: bug report, general request, or bank/wallet integration request.
- Do not post passwords, API keys, OTPs, customer records, full account numbers, bank statements, or unredacted payment notification emails.
- For a bank or wallet integration request, first forward a relevant, redacted payment notification email to **info@umar.is-a.dev**, then submit the bank integration form.

## Pull requests

1. Fork the repository and create a focused branch.
2. Keep changes small and explain the problem they solve.
3. Run relevant checks and tests before opening a pull request.
4. Update documentation when behavior or configuration changes.
5. Never commit .env files, credentials, private customer data, or real payment emails.
6. Describe security or payment-verification implications clearly.

## Payment-related changes

Payment status and settlement handling are security-sensitive. Do not weaken provider authentication, amount checks, timestamp checks, deduplication, signature verification, or manual-review safeguards merely to make a test pass. Email notifications alone must not be presented as authoritative settlement confirmation.

## Review

A maintainer may ask for additional context, tests, or revisions. Integration requests are evaluated based on feasibility, provider behavior, security, and available test data; submission does not guarantee implementation.
