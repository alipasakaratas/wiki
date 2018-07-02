<a name="top" />

[**HOME**](Home) > [**SNOWPLOW TECHNICAL DOCUMENTATION**](Snowplow-technical-documentation) > [**Webhooks**](Webhooks) > Marketo webhook adapter

## Contents

- 1 [Overview](#overview)
- 2 [Implementation](#implementation)
  - 2.1 [Event source](#source)
  - 2.2 [Snowplow adapter](#adapter)
- 3 [Events](#events)
- 4 [See also](#see-also)

<a name="overview" />

## 1. Overview

This webhook adapter lets you track webhook events logged by [Marketo][marketo-website].

<a name="implementation" />

## 2. Implementation

<a name="source" />

### 2.1 Event source

Details of the Marketo webhook format as of 2nd July 2018.

Marketo sends events as a `POST` request with all information in the body, with `application/json` as the content type.

<a name="adapter" />

### 2.2 Snowplow adapter

Implementation: [Marketo][marketo-adapter]

Marketo webhook support was implemented in [Snowplow R107 Trypillia][snowplow-release].

<a name="events" />

## 3. Events

All resources for this webhook's events:

| **Event**         | **JSON Schema**                                            | **JSON Paths**                                             | **Redshift Table**                                            |
|:------------------|:-----------------------------------------------------------|:-----------------------------------------------------------|:--------------------------------------------------------------|
| Webhook event     | [event 1-0-0][event-json-schema]                           | [event_1.json][event-json-paths]                           | [com_marketo_event_1.sql][event-sql]                          |

<a name="see-also" />

## 4. See also

[[Marketo webhook setup]]

[marketo-website]: https://www.marketo.com/

[marketo-adapter]: https://github.com/snowplow/snowplow/blob/master/3-enrich/scala-common-enrich/src/main/scala/com.snowplowanalytics.snowplow.enrich/common/adapters/registry/MarketoAdapter.scala
[snowplow-release]: https://github.com/snowplow/snowplow/releases/tag/r107-trypillia

[event-json-schema]: https://github.com/snowplow/iglu-central/tree/master/schemas/com.marketo/event/jsonschema/1-0-0
[event-json-paths]: https://github.com/snowplow/iglu-central/blob/master/jsonpaths/com.marketo/event_1.json
[event-sql]: https://github.com/snowplow/iglu-central/blob/master/sql/com.marketo/event_1.sql
