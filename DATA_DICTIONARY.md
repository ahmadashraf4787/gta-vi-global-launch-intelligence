# Data Dictionary

## metric_snapshots.csv

| Field | Type | Meaning |
|---|---|---|
| snapshot_id | string | Unique observation row ID |
| collected_at_utc | ISO datetime | When this project collected the value |
| snapshot_date_utc | date | UTC collection date |
| source | string | API/source identifier |
| entity_type | string | Type of entity (video, Wikipedia page, etc.) |
| entity_id | string | Stable source-native identifier |
| entity_name | string | Display name/title |
| metric_name | string | Metric name |
| metric_value | numeric | Value observed in the API response |
| metric_period_start | string | Start of metric period, if provided |
| metric_period_end | string | End of metric period, if provided |
| country_code | string | Country code, if source provides it |
| url | string | Public canonical URL |
| query | string | Search/query used during collection |
| metadata_json | JSON string | Additional source-specific fields |

## news_articles.csv

| Field | Type | Meaning |
|---|---|---|
| article_id | string | SHA-256 of article URL (project-side deduplication key) |
| collected_at_utc | ISO datetime | Collection timestamp |
| source | string | Source API |
| title | string | Article title |
| url | string | Article URL |
| domain | string | Publisher domain |
| published_at | string | Source-reported publication/seen timestamp |
| language | string | Source-reported language |
| query | string | Search query |
| metadata_json | JSON string | Additional source fields |

## Interpretation cautions

- A row is an observation returned by a source, not necessarily a unique user action.
- YouTube metrics are cumulative counters. Calculate interval growth by matching `source + entity_id + metric_name` across snapshots.
- Wikimedia pageviews are daily totals. Repeated collection of the same historical period can create duplicate observations; deduplicate using source, entity, metric, and metric period.
- GDELT article records can repeat across collection runs. Deduplicate using `article_id`, while retaining first/last-seen timestamps in a later enrichment step.
