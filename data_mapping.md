# Data mapping for the 6 Excel sheets

This mapping is based on the sheet headers shown in the screenshots.
Some headers are truncated in the images; fields that need confirmation
are called out in the "Open questions" section.

## Sheet 1: Client Type

| Source column          | Target field             | Type   | Notes |
|------------------------|--------------------------|--------|-------|
| Client Type            | client_type_id           | int    | Primary key for client types. |
| client type D...       | client_type_description  | string | Full description (e.g., Strategic/Con..., Private Equity). |

## Sheet 2: Client Contact

Column numbers are left-to-right from the screenshot because the header
text is truncated and repeats as "Client Conta...".

| Source column (image)    | Target field             | Type   | Notes |
|--------------------------|--------------------------|--------|-------|
| Client Contact (col 1)   | contact_company          | string | Company or location name (e.g., Siris Capital). |
| Client Contact (col 2)   | contact_address_1        | string | Address line 1 or building (e.g., 601 Lexington, Suite 600). |
| Client Contact (col 3)   | contact_city             | string | City. |
| Client Contact (col 4)   | contact_state_province   | string | State/Province or region. |
| Client Partne...         | client_partner_id        | int    | Partner/owner id; needs source system confirmation. |
| client_id                | client_id                | int    | FK to Client Billing client_id. |
| salutation               | salutation_code          | int    | -1 indicates unknown. |
| client_entry_...         | contact_id               | int    | Unique contact record id. |
| Client Contact (col 9)   | contact_email            | string | Email (e.g., jane.goss@...). |
| Client Contact (col 10)  | contact_first_name       | string | First name. |
| Client Contact (col 11)  | contact_last_name        | string | Last name. |
| Client Contact (col 12)  | contact_role             | string | Appears as "Gen" in sample; confirm meaning. |
| Client Contact (col 13)  | contact_country          | string | Country (e.g., US, UK). |
| Client Contact (col 14)  | contact_state_province_2 | string | Region/state when country present; may duplicate above. |
| Client Contact (col 15)  | contact_postal_code      | string | Postal code (e.g., EC2V7JD). |
| Client Contact (col 16)  | contact_zip_code         | string | ZIP code (e.g., 10022, 75308). |

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

## Sheet 5: Employee Status

| Source column     | Target field          | Type   | Notes |
|-------------------|-----------------------|--------|-------|
| Status            | status_flag           | int    | 0/1 indicator; meaning needs confirmation. |
| Name              | employee_name         | string | Employee name. |
| EmpID             | employee_id           | int    | Employee identifier. |
| office_floor      | office_floor          | string | Floor value; NULL in sample. |
| office_area       | office_area           | string | Area value; NULL in sample. |
| office_cs         | office_cs             | string | Unknown meaning; likely office cost center. |

## Sheet 6: Industry

| Source column     | Target field          | Type   | Notes |
|-------------------|-----------------------|--------|-------|
| industry_id       | industry_id           | int    | Primary key for industry lookup. |
| industry          | industry_name         | string | Industry name. |
| industry_cd       | industry_code         | int    | Industry code used in other tables. |

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
7. Client Index.industry_code likely joins to Industry.industry_code
   (confirm if the join should use industry_id instead).
8. Employee Status sheet has EmpID only; confirm if it joins to a
   user/employee table elsewhere.

## Open questions (need confirmation)

- Exact header names for the Client Contact sheet (multiple truncated
  "Client Conta..." columns). Please confirm the real header names and
  their order.
- Meaning of client_partner_id and client_group_id.
- Whether Client Index is a lookup table for public company data or the
  primary client master list.
- Whether "Gen" in the Client Contact sheet represents a contact type,
  department, or role.
- Whether Client Contact (col 1) is company name or address line.
- Whether Client Index.industry_code joins to Industry.industry_code or
  Industry.industry_id.
- What the Employee Status sheet represents and the meaning of office_cs.
