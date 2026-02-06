# Data mapping for the 4 Excel sheets

This mapping is based on the sheet headers shown in the screenshots.
Some headers are truncated in the images; fields that need confirmation
are called out in the "Open questions" section.

## Sheet 1: Client Type

| Source column          | Target field             | Type   | Notes |
|------------------------|--------------------------|--------|-------|
| Client Type            | client_type_id           | int    | Primary key for client types. |
| client type D...       | client_type_description  | string | Full description (e.g., Strategic/Con..., Private Equity). |

## Sheet 2: Client Contact

| Source column (image)  | Target field             | Type   | Notes |
|------------------------|--------------------------|--------|-------|
| Client Conta...        | contact_address_1        | string | Address line 1. |
| Client Conta...        | contact_address_2        | string | Address line 2 (e.g., Suite/Floor). |
| Client Conta...        | contact_city             | string | City. |
| Client Conta...        | contact_state_province   | string | State/Province (or region). |
| Client Partne...       | client_partner_id        | int    | Partner/owner id; needs source system confirmation. |
| client_id              | client_id                | int    | FK to Client Billing client_id. |
| salutation             | salutation_code          | int    | -1 indicates unknown. |
| client_entry_...       | contact_id               | int    | Unique contact record id. |
| Client Conta...        | contact_email            | string | Email (e.g., jane.goss@...). |
| Client Conta...        | contact_first_name       | string | First name. |
| Client Conta...        | contact_last_name        | string | Last name. |
| Client Conta...        | contact_role             | string | Appears as "Gen" in sample; confirm meaning. |
| Client Conta...        | contact_country          | string | Country (e.g., US, UK). |
| Client Conta...        | contact_state_province_2 | string | Region/state when country present; may duplicate above. |
| Client Conta...        | contact_postal_code      | string | Postal/ZIP code. |

## Sheet 3: Client Billing

| Source column     | Target field          | Type   | Notes |
|-------------------|-----------------------|--------|-------|
| Client Billing    | billing_name          | string | Client billing name. |
| Client id         | client_id             | int    | Primary key for client. |
| Client Type       | client_type_id        | int    | FK to Client Type.client_type_id. |
| Opened Date       | opened_date           | date   | Client opened date. |
| Public Comp?      | public_company_flag   | bool   | NULL or 0/1. |
| Client Group      | client_group_id       | int    | Group id; confirm relationship. |
| Status            | client_status         | string | e.g., STOPMATTE..., INACTIVE. |

## Sheet 4: Client Index

| Source column     | Target field          | Type   | Notes |
|-------------------|-----------------------|--------|-------|
| Number            | record_number         | int    | Source record number. |
| ClientIndex       | client_index          | int    | Unique index; possible join key. |
| DisplayName       | display_name          | string | Company name. |
| MaxWorkda...      | max_workday_time      | time   | Appears as 00:00.0. |
| Last Time         | last_time             | date   | Last activity date. |
| MaxPAYDate        | max_pay_date          | date   | Last pay date. |
| PublicCompa...    | public_company_flag   | bool   | 0/1 indicator. |
| Industry          | industry_code         | int    | Industry code. |

## Relationships and join logic

1. Client Billing.client_id is the primary client key.
2. Client Contact.client_id should join to Client Billing.client_id.
3. Client Billing.client_type_id should join to Client Type.client_type_id.
4. Client Contact.client_partner_id may reference a client or partner
   table; confirm if it joins to Client Billing.client_id or Client Index.client_index.
5. Client Billing.client_group_id may reference a client group or
   another client; confirm intended join target.
6. If no numeric join exists between Client Billing and Client Index,
   use a controlled match on billing_name <-> display_name with
   normalization and manual review.

## Open questions (need confirmation)

- Exact header names for the Client Contact sheet (multiple truncated
  "Client Conta..." columns). Please confirm the real header names and
  their order.
- Meaning of client_partner_id and client_group_id.
- Whether Client Index is a lookup table for public company data or the
  primary client master list.
- Whether "Gen" in the Client Contact sheet represents a contact type,
  department, or role.
