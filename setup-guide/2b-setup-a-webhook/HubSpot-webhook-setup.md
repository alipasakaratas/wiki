<a name="top" />

[**HOME**](Home) > [**SNOWPLOW SETUP GUIDE**](Setting-up-Snowplow) > [**Step 2b: setup a Webhook**](Setting-up-a-webhook) > [[HubSpot webhook setup]]

## Contents

- 1 [Overview](#overview)
  - 1.1 [Compatibility](#compat)
- 2 [Setup](#setup)
  - 2.1 [HubSpot](#setup-hubspot)
  - 2.2 [Redshift](#setup-redshift)

<a name="overview" />

## 1. Overview

This webhook integration lets you track the following events logged by [HubSpot][hubspot]:

- Deal creation
- Deal change
- Deal deletion
- Contact creation
- Contact change
- Contact deletion
- Company creation
- Company change
- Company deletion

For the technical implementation, see [[HubSpot webhook adapter]].

<a name="compat" />

### 1.1 Compatibility

* [Snowplow R113][snowplow-r113]+
* [HubSpot webhook API][hubspot-webhooks]

<a name="setup" />

## 2. Setup

Integrating HubSpot's webhooks into Snowplow is a two-stage process:

1. Configure HubSpot to send events to Snowplow
2. (Optional) Create the HubSpot event tables into Amazon Redshift

<a name="setup-hubspot" />

## 2.1 HubSpot

Documentation for the HubSpot webhook can be found here: https://developers.hubspot.com/docs/methods/webhooks/webhooks-overview.

The webhook address can be found here: https://developers.hubspot.com/docs/methods/webhooks/webhooks-overview.

The endpoint should be:

```
http://<collector host>/com.hubspot/v1
```

<a name="redshift" />

## 2.2 Redshift

If you are running the Snowplow with Amazon Redshift, then you should deploy the following tables
into your Amazon Redshift:

* [com_hubspot_company_change_1.sql][company-change-sql]
* [com_hubspot_company_creation_1.sql][company-creation-sql]
* [com_hubspot_company_deletion_1.sql][company-deletion-sql]
* [com_hubspot_contact_change_1.sql][contact-change-sql]
* [com_hubspot_contact_creation_1.sql][contact-creation-sql]
* [com_hubspot_contact_deletion_1.sql][contact-deletion-sql]
* [com_hubspot_deal_change_1.sql][deal-change-sql]
* [com_hubspot_deal_creation_1.sql][deal-creation-sql]
* [com_hubspot_deal_deletion_1.sql][deal-deletion-sql]

Make sure to deploy this table into the same schema as your `events` table.

That's it - with these tables deployed, your HubSpot events should automatically flow through into
Redshift.

[company-change-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.hubspot/company_change_1.sql
[company-creation-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.hubspot/company_creation_1.sql
[company-deletion-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.hubspot/company_deletion_1.sql
[contact-change-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.hubspot/contact_change_1.sql
[contact-creation-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.hubspot/contact_creation_1.sql
[contact-deletion-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.hubspot/contact_deletion_1.sql
[deal-change-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.hubspot/deal_change_1.sql
[deal-creation-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.hubspot/deal_creation_1.sql
[deal-deletion-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.hubspot/deal_deletion_1.sql

[hubspot]: https://www.hubspot.com/
[hubspot-webhooks]: https://developers.hubspot.com/docs/methods/webhooks/webhooks-overview
[snowplow-r113]: https://github.com/snowplow/snowplow/releases/tag/r113-filitosa
