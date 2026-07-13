# Efí (efi-bank)

Efí (formerly **Gerencianet**) is a Brazilian payment institution ("Instituição de Pagamento", CNPJ 09.089.356/0001-18) and digital bank. It publishes a set of well-documented public REST APIs for the Brazilian financial system: Pix (immediate and dated charges, send/receive, refunds, webhooks), Boletos and Charges (Cobranças), Carnê installment booklets, and Pix via Open Finance. The company rebranded from Gerencianet to Efí; older SDKs, host names, and OAuth scope prefixes (e.g. `gn.`) still reflect the Gerencianet lineage.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/efi-bank/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/efi-bank/refs/heads/main/apis.yml)

## Access Model (Honest Summary)

Efí's APIs are **live, public, and production-grade**, but the auth model differs by product host:

- **Pix API** — base `https://pix.api.efipay.com.br` (sandbox `https://pix-h.api.efipay.com.br`). Requires **OAuth2 client-credentials** (token at `POST /oauth/token`, HTTP Basic with `Client_Id`/`Client_Secret`) **PLUS a mandatory mutual TLS (mTLS) client certificate** (`.p12`/`.pem`) on *every* request — this is a Brazilian Central Bank (BACEN) requirement for the Pix arrangement, not an Efí choice.
- **Pix via Open Finance API** — base `https://openfinance.api.efipay.com.br` (sandbox `https://openfinance-h.api.efipay.com.br`). Also requires **OAuth2 client-credentials (token at `POST /v1/oauth/token`) PLUS mTLS**.
- **Cobranças (Charges) API** — base `https://cobrancas.api.efipay.com.br` (sandbox `https://cobrancas-h.api.efipay.com.br`). Uses **OAuth2 client-credentials** (token at `POST /v1/authorize`, HTTP Basic). **No mTLS certificate required.**

You obtain `Client_Id` / `Client_Secret` and generate the mTLS certificate inside the Efí account dashboard. SDKs are published for PHP, Node.js, Python, Java, Go, Ruby, Delphi, TypeScript, and Flutter, plus e-commerce modules (WooCommerce, WordPress, PrestaShop, Magento 2, OpenCart, WHMCS).

The **create/query charge**, **webhook**, **Cobranças charge**, and **Carnê** operations in this repository were confirmed directly against Efí's public documentation. The dated-charge (`cobv`), received-Pix (`pix`), refund (`devolução`), payload-location (`loc`), and Open Finance path operations are **modeled** on the BACEN Pix API v2 standard that Efí implements and on Efí's documented OAuth scopes; treat their exact paths as representative. See `review.yml` for the confirmed-vs-modeled breakdown.

## Tags

- Payments
- Pix
- Boleto
- Banking
- Brazil
- Latin America
- Charges
- Digital Account
- Financial Infrastructure
- Fintech

## Timestamps

- **Created:** 2026-07-12
- **Modified:** 2026-07-12

## APIs

### Efí Pix Charges API

Create and manage Pix charges — immediate charges (cob) and dated charges with due date (cobv) — plus payload locations (loc) that generate the dynamic QR Code and Pix Copia e Cola payload. Requires OAuth2 client-credentials and a mandatory mTLS certificate.

- **Human URL:** [https://dev.efipay.com.br/docs/api-pix/cobrancas-imediatas](https://dev.efipay.com.br/docs/api-pix/cobrancas-imediatas)
- **Base URL:** `https://pix.api.efipay.com.br`

#### Tags

- Pix
- Charges
- QR Code
- Brazil

#### Properties

- [Documentation](https://dev.efipay.com.br/docs/api-pix/credenciais)
- [API Reference](https://dev.efipay.com.br/docs/api-pix/cobrancas-imediatas)
- [OpenAPI](openapi/efi-bank-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/efi-bank.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/efi-bank.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Efí Pix Payments API

Consult received Pix (Cash-In), send Pix directly from the API (Cash-Out), and request or query refunds (devolução) tied to an end-to-end identifier. Requires OAuth2 client-credentials and a mandatory mTLS certificate.

- **Human URL:** [https://dev.efipay.com.br/docs/api-pix/pix](https://dev.efipay.com.br/docs/api-pix/pix)
- **Base URL:** `https://pix.api.efipay.com.br`

#### Tags

- Pix
- Payments
- Refunds
- Cash Out

#### Properties

- [Documentation](https://dev.efipay.com.br/docs/api-pix/credenciais)
- [OpenAPI](openapi/efi-bank-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/efi-bank.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/efi-bank.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Efí Pix Webhooks API

Register, retrieve, and cancel webhooks that notify your application about received Pix, keyed to a Pix key (chave). Server-to-endpoint HTTPS callbacks, not a WebSocket. Requires OAuth2 client-credentials and a mandatory mTLS certificate.

- **Human URL:** [https://dev.efipay.com.br/docs/api-pix/webhooks](https://dev.efipay.com.br/docs/api-pix/webhooks)
- **Base URL:** `https://pix.api.efipay.com.br`

#### Tags

- Pix
- Webhooks
- Notifications

#### Properties

- [API Reference](https://dev.efipay.com.br/docs/api-pix/webhooks)
- [OpenAPI](openapi/efi-bank-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/efi-bank.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/efi-bank.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Efí Cobranças (Charges) API

Issue and manage charges (transações) payable by boleto bancário, credit card, or Pix — one-step or two-step creation, due-date changes, manual settlement (baixa), cancellation, resend, and history. Uses OAuth2 client-credentials over HTTP Basic; no mTLS certificate required.

- **Human URL:** [https://dev.efipay.com.br/docs/api-cobrancas/boleto](https://dev.efipay.com.br/docs/api-cobrancas/boleto)
- **Base URL:** `https://cobrancas.api.efipay.com.br`

#### Tags

- Boleto
- Charges
- Checkout
- Brazil

#### Properties

- [Documentation](https://dev.efipay.com.br/docs/api-cobrancas/credenciais)
- [API Reference](https://dev.efipay.com.br/docs/api-cobrancas/boleto)
- [OpenAPI](openapi/efi-bank-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/efi-bank.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/efi-bank.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Efí Carnê API

Generate and manage carnês — a batch of installment charges (parcelas) issued together as a booklet. Create a carnê, change parcel due dates, cancel or settle the whole booklet or individual parcels, and resend by email. Uses OAuth2 client-credentials; no mTLS certificate required.

- **Human URL:** [https://dev.efipay.com.br/docs/api-cobrancas/carne](https://dev.efipay.com.br/docs/api-cobrancas/carne)
- **Base URL:** `https://cobrancas.api.efipay.com.br`

#### Tags

- Carnê
- Installments
- Boleto

#### Properties

- [API Reference](https://dev.efipay.com.br/docs/api-cobrancas/carne)
- [OpenAPI](openapi/efi-bank-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/efi-bank.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/efi-bank.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Efí Pix via Open Finance API

Initiate Pix payments as a payment initiator across banks and fintechs connected to Brazil's Open Finance ecosystem, query participants, and consult or reverse initiated payments. Requires OAuth2 client-credentials and a mandatory mTLS certificate.

- **Human URL:** [https://dev.efipay.com.br/docs/api-open-finance/credenciais](https://dev.efipay.com.br/docs/api-open-finance/credenciais)
- **Base URL:** `https://openfinance.api.efipay.com.br`

#### Tags

- Open Finance
- Pix
- Payment Initiation

#### Properties

- [Documentation](https://dev.efipay.com.br/docs/api-open-finance/credenciais)
- [OpenAPI](openapi/efi-bank-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/efi-bank.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/efi-bank.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

## Common Properties

- [Domain Security](security/efi-bank-domain-security.yml)
- [Authentication](authentication/efi-bank-authentication.yml)
- [Website](https://sejaefi.com.br)
- [Documentation](https://dev.efipay.com.br)
- [LinkedIn](https://www.linkedin.com/company/efibank)
- [Plans](plans/efi-bank-plans-pricing.yml)
- [Rate Limits](rate-limits/efi-bank-rate-limits.yml)
- [Fin Ops](finops/efi-bank-finops.yml)

## Maintainers

**FN:** Kin Lane
**Email:** kin@apievangelist.com
