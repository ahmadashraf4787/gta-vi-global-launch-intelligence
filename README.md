# GTA VI Global Launch Intelligence Tracker

A reproducible, multi-source data collection project tracking the public digital footprint of Grand Theft Auto VI over time.

**Project start:** 2026-11-19  
**Initial scope:** YouTube video/channel snapshots, Wikimedia pageviews, GDELT news articles.  
**Planned extensions:** Twitch, Reddit, Google Trends, regional trend snapshots, sentiment/topic analysis.

> This project measures public attention and engagement. It does **not** measure official game sales, active players, or revenue unless those figures are explicitly published by an authoritative source.

## 1. Data principles

1. Preserve timestamped snapshots. Never overwrite historical observations.
2. Keep source-native IDs and URLs so records can be traced.
3. Separate cumulative counters (views, likes, comments) from event records (articles).
4. Store UTC timestamps and retain source publication timestamps where available.
5. Keep collection metadata: script version, query, source, collection time, and status.
6. Respect API terms, rate limits, robots policies, privacy requirements, and redistribution licenses.

## 2. Initial data sources

| Source | Initial data | Authentication |
|---|---|---|
| YouTube Data API v3 | Video search results and public video statistics | API key |
| Wikimedia Pageviews API | Daily pageviews for GTA VI-related Wikipedia pages | No key |
| GDELT DOC 2.0 | Public news article metadata | No key |

The first version intentionally starts with sources that can be collected reproducibly. Twitch and Reddit integrations should be added after their developer credentials and access permissions are configured. Google Trends collection may be added as an experimental module because unofficial clients can change or be rate-limited.

## 3. Quick start

```bash
git clone <your-repository-url>
cd gta_vi_global_launch_intelligence

python -m venv .venv
# Windows:
.venv\Scripts\activate
# macOS/Linux:
source .venv/bin/activate

pip install -r requirements.txt
cp .env.example .env
```

Add a YouTube API key to `.env`:

```env
YOUTUBE_API_KEY_AHMED=your_existing_api_key_here
```

Run collectors:

```bash
python -m src.collectors.wikimedia
python -m src.collectors.gdelt
python -m src.collectors.youtube
```

YouTube collection is skipped with a clear message if no API key is configured. The other two collectors do not require a key.

## 4. Output layout

```text
data/
  raw/
    wikimedia/
    gdelt/
    youtube/
  processed/
    snapshots/
  logs/
```

Raw API responses are retained as JSONL. A normalized snapshot table is also appended to:

`data/processed/snapshots/metric_snapshots.csv`

News article metadata is stored separately in:

`data/processed/news_articles.csv`

Do not commit `.env`, API keys, or private credentials.

## 5. Snapshot schema

`metric_snapshots.csv` contains:

- `snapshot_id`: unique row ID
- `collected_at_utc`: UTC timestamp when the API was queried
- `snapshot_date_utc`: UTC collection date
- `source`: source platform/API
- `entity_type`: video, page, channel, etc.
- `entity_id`: source-native identifier
- `entity_name`: human-readable title/page name
- `metric_name`: views, likes, comments, pageviews, etc.
- `metric_value`: numeric metric value
- `metric_period_start`: period start if supplied by source
- `metric_period_end`: period end if supplied by source
- `country_code`: country code when available
- `url`: canonical public URL
- `query`: collection query
- `metadata_json`: additional source-specific fields

`news_articles.csv` contains source URL, title, domain, publication date, language, query and collection timestamp. GDELT coverage and language detection are source-provided and should be treated as metadata, not ground truth.

## 6. Research questions

- How does global public attention evolve after launch?
- Do search-interest spikes precede video engagement spikes?
- Which content formats and creator segments receive the strongest engagement?
- How does news coverage change around patches, controversies and major announcements?
- How do public discussions and sentiment evolve over time?

## 7. Important limitations

- YouTube search results are query- and ranking-dependent; this is not a census of all GTA VI videos.
- YouTube counters are cumulative snapshots, not per-hour view counts. Differences between snapshots estimate growth during the interval.
- Wikimedia pageviews are page-level attention indicators, not unique people or game players.
- GDELT is a news-monitoring source, not a complete archive of all media coverage.
- Country-level data is only recorded when the source provides it.
- Do not label third-party estimates as official sales or revenue.
- API availability, quotas and terms may change. Check the relevant provider documentation before commercial redistribution.

## 8. Recommended publication strategy

1. Run the collectors and validate records for several days.
2. Publish the initial dataset with a clear changelog and data dictionary.
3. Keep daily versions and a stable schema.
4. Publish separate Kaggle notebooks for EDA, time-series analysis, creator analysis and NLP.
5. Add a dashboard only after the data pipeline is stable.

## 9. Citation

If you use this dataset, cite the Kaggle dataset page and the relevant original data providers. Add a DOI after a citable release is created through the repository/platform workflow; do not invent a DOI in advance.


## Reusing the existing Cristiano Ronaldo tracker API key

This project is configured to reuse the existing YouTube Data API key under the name `YOUTUBE_API_KEY_AHMED`.

- Local `.env`: set `YOUTUBE_API_KEY_AHMED=...`
- GitHub: add a repository Actions secret named `YOUTUBE_API_KEY_AHMED`
- Kaggle: retrieve it with `UserSecretsClient().get_secret("YOUTUBE_API_KEY_AHMED")` when running inside a Kaggle notebook.

Both trackers consume quota from the same Google Cloud project/key. Monitor quota usage. Never commit the key or paste it into source code.
