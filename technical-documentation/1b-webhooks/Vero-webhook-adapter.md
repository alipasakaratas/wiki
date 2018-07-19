<a name="top" />

[**HOME**](Home) > [**SNOWPLOW TECHNICAL DOCUMENTATION**](Snowplow-technical-documentation) > [**Webhooks**](Webhooks) > Vero webhook adapter

## Contents

- 1 [Overview](#overview)
- 2 [Implementation](#implementation)
  - 2.1 [Event source](#source)
  - 2.2 [Snowplow adapter](#adapter)
- 3 [Events](#events)
- 4 [See also](#see-also)

<a name="overview" />

## 1. Overview

This webhook adapter lets you track a variety of events logged by [Vero][vero-website].

<a name="implementation" />

## 2. Implementation

<a name="source" />

### 2.1 Event source

Details of the Vero webhook format as of 2nd July 2018.

Vero sends events as a `POST` request with all information in the body, with `application/json` as the content type.

<a name="adapter" />

### 2.2 Snowplow adapter

Implementation: [Vero][vero-adapter]

Vero webhook support was implemented in [Snowplow R107 Trypillia][snowplow-release].

<a name="events" />

## 3. Events

All resources for this webhook's events:

| **Event**         | **JSON Schema**                                            | **JSON Paths**                                             | **Redshift Table**                                            |
|:------------------|:-----------------------------------------------------------|:-----------------------------------------------------------|:--------------------------------------------------------------|
| Bounced           | [bounced 1-0-0][bounced-json-schema]                       | [bounced_1.json][bounced-json-paths]                       | [com_getvero_bounced_1.sql][bounced-sql]                      |
| Clicked           | [clicked 1-0-0][clicked-json-schema]                       | [clicked_1.json][clicked-json-paths]                       | [com_getvero_clicked_1.sql][clicked-sql]                      |
| Created           | [created 1-0-0][created-json-schema]                       | [created_1.json][created-json-paths]                       | [com_getvero_created_1.sql][created-sql]                      |
| Delivered         | [delivered 1-0-0][delivered-json-schema]                   | [delivered_1.json][delivered-json-paths]                   | [com_getvero_delivered_1.sql][delivered-sql]                  |
| Opened            | [opened 1-0-0][opened-json-schema]                         | [opened_1.json][opened-json-paths]                         | [com_getvero_opened_1.sql][opened-sql]                        |
| Sent              | [sent 1-0-0][sent-json-schema]                             | [sent_1.json][sent-json-paths]                             | [com_getvero_sent_1.sql][sent-sql]                            |
| Unsubscribed      | [unsubscribed 1-0-0][unsubscribed-json-schema]             | [unsubscribed_1.json][unsubscribed-json-paths]             | [com_getvero_unsubscribed_1.sql][unsubscribed-sql]            |
| Updated           | [updated 1-0-0][updated-json-schema]                       | [updated_1.json][updated-json-paths]                       | [com_getvero_updated_1.sql][updated-sql]                      |


<a name="see-also" />

## 4. See also

[[Vero webhook setup]]

[vero-website]: https://www.getvero.com/

[vero-adapter]: https://github.com/snowplow/snowplow/blob/master/3-enrich/scala-common-enrich/src/main/scala/com.snowplowanalytics.snowplow.enrich/common/adapters/registry/VeroAdapter.scala
[snowplow-release]: https://github.com/snowplow/snowplow/releases/tag/r107-trypillia

[bounced-json-schema]: https://github.com/snowplow/iglu-central/tree/master/schemas/com.getvero/bounced/jsonschema/1-0-0
[bounced-json-paths]: https://github.com/snowplow/iglu-central/blob/master/jsonpaths/com.getvero/bounced_1.json
[bounced-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.getvero/bounced_1.sql

[clicked-json-schema]: https://github.com/snowplow/iglu-central/tree/master/schemas/com.getvero/clicked/jsonschema/1-0-0
[clicked-json-paths]: https://github.com/snowplow/iglu-central/blob/master/jsonpaths/com.getvero/clicked_1.json
[clicked-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.getvero/clicked_1.sql

[created-json-schema]: https://github.com/snowplow/iglu-central/tree/master/schemas/com.getvero/created/jsonschema/1-0-0
[created-json-paths]: https://github.com/snowplow/iglu-central/blob/master/jsonpaths/com.getvero/created_1.json
[created-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.getvero/created_1.sql

[delivered-json-schema]: https://github.com/snowplow/iglu-central/tree/master/schemas/com.getvero/delivered/jsonschema/1-0-0
[delivered-json-paths]: https://github.com/snowplow/iglu-central/blob/master/jsonpaths/com.getvero/delivered_1.json
[delivered-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.getvero/delivered_1.sql

[opened-json-schema]: https://github.com/snowplow/iglu-central/tree/master/schemas/com.getvero/opened/jsonschema/1-0-0
[opened-json-paths]: https://github.com/snowplow/iglu-central/blob/master/jsonpaths/com.getvero/opened_1.json
[opened-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.getvero/opened_1.sql

[sent-json-schema]: https://github.com/snowplow/iglu-central/tree/master/schemas/com.getvero/sent/jsonschema/1-0-0
[sent-json-paths]: https://github.com/snowplow/iglu-central/blob/master/jsonpaths/com.getvero/sent_1.json
[sent-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.getvero/sent_1.sql

[unsubscribed-json-schema]: https://github.com/snowplow/iglu-central/tree/master/schemas/com.getvero/unsubscribed/jsonschema/1-0-0
[unsubscribed-json-paths]: https://github.com/snowplow/iglu-central/blob/master/jsonpaths/com.getvero/unsubscribed_1.json
[unsubscribed-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.getvero/unsubscribed_1.sql

[updated-json-schema]: https://github.com/snowplow/iglu-central/tree/master/schemas/com.getvero/updated/jsonschema/1-0-0
[updated-json-paths]: https://github.com/snowplow/iglu-central/blob/master/jsonpaths/com.getvero/updated_1.json
[updated-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.getvero/updated_1.sql
