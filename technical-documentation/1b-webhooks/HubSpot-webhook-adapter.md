[**HOME**](Home) > [**SNOWPLOW TECHNICAL DOCUMENTATION**](Snowplow-technical-documentation) > [**Webhooks**](Webhooks) > HubSpot webhook adapter

## Contents

- 1 [Overview](#overview)
- 2 [Implementation](#implementation)
  - 2.1 [Event source](#source)
  - 2.2 [Snowplow adapter](#adapter)
- 3 [Events](#events)
- 4 [See also](#see-also)

<a name="overview" />

## 1. Overview

This webhook adapter lets you track events logged by [HubSpot][hubspot].

<a name="implementation" />

## 2. Implementation

<a name="source" />

### 2.1 Event source

The [HubSpot webhook API][hubspot-webhooks] sends events as a JSON body in a `POST` request.

<a name="adapter" />

### 2.2 Snowplow adapter

Implementation: [HubSpotAdapter][hubspot-adapter]

HubSpot webhook support was implemented in [Snowplow R113][snowplow-r113].

<a name="events" />

## 3. Events

All resources for this webhook's events:

| **Event** | **JSON Schema** | **JSON Paths** | **Redshift Table** |
|:-|:-|:-|:-|
| Company change | [company_change 1-0-0][company-change-json-schema] | [company_change_1.json][company-change-json-paths] | [com_hubspot_company_change_1.sql][company-change-sql] |
| Company creation | [company_creation 1-0-0][company-creation-json-schema] | [company_creation_1.json][company-creation-json-paths] | [com_hubspot_company_creation_1.sql][company-creation-sql] |
| Company deletion | [company_deletion 1-0-0][company-deletion-json-schema] | [company_deletion_1.json][company-deletion-json-paths] | [com_hubspot_company_deletion_1.sql][company-deletion-sql] |
| Contact change | [contact_change 1-0-0][contact-change-json-schema] | [contact_change_1.json][contact-change-json-paths] | [com_hubspot_contact_change_1.sql][contact-change-sql] |
| Contact creation | [contact_creation 1-0-0][contact-creation-json-schema] | [contact_creation_1.json][contact-creation-json-paths] | [com_hubspot_contact_creation_1.sql][contact-creation-sql] |
| Contact deletion | [contact_deletion 1-0-0][contact-deletion-json-schema] | [contact_deletion_1.json][contact-deletion-json-paths] | [com_hubspot_contact_deletion_1.sql][contact-deletion-sql] |
| Deal change | [deal_change 1-0-0][deal-change-json-schema] | [deal_change_1.json][deal-change-json-paths] | [com_hubspot_deal_change_1.sql][deal-change-sql] |
| Deal creation | [deal_creation 1-0-0][deal-creation-json-schema] | [deal_creation_1.json][deal-creation-json-paths] | [com_hubspot_deal_creation_1.sql][deal-creation-sql] |
| Deal deletion | [deal_deletion 1-0-0][deal-deletion-json-schema] | [deal_deletion_1.json][deal-deletion-json-paths] | [com_hubspot_deal_deletion_1.sql][deal-deletion-sql] |

<a name="see-also" />

## 4. See also

[[HubSpot webhook setup]]

[company-change-json-schema]: https://github.com/snowplow/iglu-central/blob/master/schemas/com.hubspot/company_change/jsonschema/1-0-0
[company-change-json-paths]: https://github.com/snowplow/iglu-central/blob/master/jsonpaths/com.hubspot/company_change_1.json
[company-change-json-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.hubspot/company_change_1.sql
[company-creation-json-schema]: https://github.com/snowplow/iglu-central/blob/master/schemas/com.hubspot/company_creation/jsonschema/1-0-0
[company-creation-json-paths]: https://github.com/snowplow/iglu-central/blob/master/jsonpaths/com.hubspot/company_creation_1.json
[company-creation-json-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.hubspot/company_creation_1.sql
[company-deletion-json-schema]: https://github.com/snowplow/iglu-central/blob/master/schemas/com.hubspot/company_deletion/jsonschema/1-0-0
[company-deletion-json-paths]: https://github.com/snowplow/iglu-central/blob/master/jsonpaths/com.hubspot/company_deletion_1.json
[company-deletion-json-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.hubspot/company_deletion_1.sql
[contact-change-json-schema]: https://github.com/snowplow/iglu-central/blob/master/schemas/com.hubspot/contact_change/jsonschema/1-0-0
[contact-change-json-paths]: https://github.com/snowplow/iglu-central/blob/master/jsonpaths/com.hubspot/contact_change_1.json
[contact-change-json-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.hubspot/contact_change_1.sql
[contact-creation-json-schema]: https://github.com/snowplow/iglu-central/blob/master/schemas/com.hubspot/contact_creation/jsonschema/1-0-0
[contact-creation-json-paths]: https://github.com/snowplow/iglu-central/blob/master/jsonpaths/com.hubspot/contact_creation_1.json
[contact-creation-json-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.hubspot/contact_creation_1.sql
[contact-deletion-json-schema]: https://github.com/snowplow/iglu-central/blob/master/schemas/com.hubspot/contact_deletion/jsonschema/1-0-0
[contact-deletion-json-paths]: https://github.com/snowplow/iglu-central/blob/master/jsonpaths/com.hubspot/contact_deletion_1.json
[contact-deletion-json-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.hubspot/contact_deletion_1.sql
[deal-change-json-schema]: https://github.com/snowplow/iglu-central/blob/master/schemas/com.hubspot/deal_change/jsonschema/1-0-0
[deal-change-json-paths]: https://github.com/snowplow/iglu-central/blob/master/jsonpaths/com.hubspot/deal_change_1.json
[deal-change-json-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.hubspot/deal_change_1.sql
[deal-creation-json-schema]: https://github.com/snowplow/iglu-central/blob/master/schemas/com.hubspot/deal_creation/jsonschema/1-0-0
[deal-creation-json-paths]: https://github.com/snowplow/iglu-central/blob/master/jsonpaths/com.hubspot/deal_creation_1.json
[deal-creation-json-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.hubspot/deal_creation_1.sql
[deal-deletion-json-schema]: https://github.com/snowplow/iglu-central/blob/master/schemas/com.hubspot/deal_deletion/jsonschema/1-0-0
[deal-deletion-json-paths]: https://github.com/snowplow/iglu-central/blob/master/jsonpaths/com.hubspot/deal_deletion_1.json
[deal-deletion-json-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.hubspot/deal_deletion_1.sql

[hubspot]: https://www.hubspot.com/
[hubspot-webhooks]: https://developers.hubspot.com/docs/methods/webhooks/webhooks-overview
[hubspot-adapter]: https://github.com/snowplow/snowplow/blob/master/3-enrich/scala-common-enrich/src/main/scala/com.snowplowanalytics.snowplow.enrich/common/adapters/registry/HubSpotAdapter.scala
[snowplow-r113]: https://github.com/snowplow/snowplow/releases/tag/r113-filitosa
