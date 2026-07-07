# TryHackMe — Splunk: Basics

## Overview

**Platform:** TryHackMe
**Room:** Splunk: Basics
**Category:** SIEM / Log Analysis
**Analyst Focus:** SOC Tier 1 — Splunk Architecture, Log Ingestion & SPL Search Fundamentals

---

## Description

This room introduces Splunk as a SIEM (Security Information and Event Management) platform — one of the most widely deployed log analysis and threat detection tools in enterprise SOC environments. It covers how Splunk is structured, how logs are ingested and indexed, how to navigate the Splunk UI, and how to write and run SPL (Search Processing Language) queries to search and analyze event data. The room includes hands-on log ingestion and practical SPL searching against real data, reinforcing how SOC analysts use Splunk during alert triage and incident investigation.

---

## Key Concepts Covered

- **Splunk architecture** — understanding the three core components of Splunk and how data flows through the platform from collection to search
- **Log ingestion** — exploring the methods Splunk uses to receive, parse, and index log data from various sources
- **Splunk UI navigation** — becoming familiar with the Search & Reporting App, indexes, sourcetypes, the time range picker, and the field sidebar
- **SPL (Search Processing Language)** — learning the syntax and structure of Splunk's query language to filter, aggregate, and display log data
- **Practical log analysis** — ingesting provided log files into a live Splunk instance and running searches to explore and summarize the data
- **Field extraction** — understanding how Splunk automatically extracts key fields from raw log data and how analysts use those fields to build targeted searches

---

## Splunk Architecture

Splunk is made up of three core components that work together to collect, process, and present log data to analysts.

| Component | Role |
|---|---|
| **Forwarder** | Installed on endpoints or log sources; collects raw log data and sends it to the indexer |
| **Indexer** | Receives incoming data, parses it, assigns sourcetypes, and stores it in indexes for searching |
| **Search Head** | The analyst-facing interface where SPL searches are run, dashboards are built, and alerts are managed |

Data flows in one direction: **Forwarder → Indexer → Search Head**. Understanding this pipeline helps analysts know where data originates, how it is stored, and how to retrieve it efficiently during an investigation.

---

## Log Ingestion

Splunk supports multiple methods for receiving log data depending on the environment and use case:

- **Universal Forwarder** — a lightweight agent installed on endpoints that continuously ships logs to the indexer in real time
- **Monitor inputs** — Splunk watches specified files or directories on the server itself and ingests new data as it appears
- **Manual upload** — log files are directly uploaded through the UI, used for one-time analysis or lab environments (used in this room)

During ingestion, Splunk automatically:
- Assigns a **sourcetype** based on the format and content of the file
- Extracts **default fields** including `_time`, `host`, `source`, and `sourcetype`
- Breaks continuous raw text into individual searchable **events**
- Makes all field values immediately available for searching and filtering

---

## Splunk UI Navigation

| UI Element | Purpose |
|---|---|
| **Search & Reporting App** | Primary analyst workspace for running searches and investigating events |
| **Indexes** | Data storage buckets that separate log data by source or environment |
| **Sourcetypes** | Classifications that define how Splunk parses different log formats |
| **Time Range Picker** | Controls the time window of the search — critical for scoping investigations |
| **Field Sidebar** | Displays all auto-extracted fields available for filtering and grouping results |
| **Events Tab** | Shows raw log events returned by a search |
| **Statistics Tab** | Displays aggregated results from commands like `stats` and `timechart` |

---

## SPL (Search Processing Language)

SPL is Splunk's built-in query language. It uses a **pipe-based structure** where the output of one command is passed directly into the next — similar to chaining commands in a Linux terminal. This allows analysts to build powerful, layered searches from simple building blocks.

**Basic SPL structure:**
```spl
index=main sourcetype=WinEventLog EventCode=4625
| stats count by src_ip
| sort -count
```

- `index=main` — specifies which data bucket to search
- `sourcetype=WinEventLog` — filters to a specific log type
- `EventCode=4625` — filters for a specific event (failed Windows login)
- `| stats count by src_ip` — counts how many times each IP appears
- `| sort -count` — sorts results from highest to lowest

### Core SPL Commands Used

| Command | Purpose |
|---|---|
| `search` | Filter events by keyword or field value |
| `stats` | Aggregate data — count, sum, average by field |
| `table` | Display specific fields in a clean, readable column layout |
| `sort` | Order results ascending or descending by field value |
| `dedup` | Remove duplicate entries from results |
| `fields` | Include or exclude specific fields from results |
| `where` | Apply conditional filters to aggregated results |

### Searches Performed

**Explore all ingested data**
```spl
index=main
```
Used to confirm successful ingestion and view raw events across the index.

---

**Filter by sourcetype**
```spl
index=main sourcetype=<sourcetype>
```
Narrowed results to a specific log type to reduce noise and focus the investigation scope.

---

**Count events by sourcetype**
```spl
index=main
| stats count by sourcetype
```
Provided a high-level summary of how many events existed per log source — useful for data validation after ingestion.

---

**Display key metadata fields in table format**
```spl
index=main
| table _time, host, source, sourcetype
```
Reformatted results into a clean, readable layout showing the key metadata for each event.

---

**Count events by host, sorted highest to lowest**
```spl
index=main
| stats count by host
| sort -count
```
Identified which hosts generated the most log volume — a useful first step during baseline investigation.

---

**Filter by a specific field value**
```spl
index=main sourcetype=<sourcetype> <field>="<value>"
```
Applied targeted filters using auto-extracted fields to isolate specific events of interest.

---

## Hands-On Activity

- Manually uploaded provided log files into a live Splunk instance through the UI
- Confirmed successful ingestion by verifying event count and sourcetype assignment
- Navigated the Search & Reporting App to explore raw events and extracted fields
- Ran both pre-built and self-written SPL searches to filter, aggregate, and summarize log data
- Used `stats` and `sort` to identify high-volume hosts and surface event distribution patterns
- Practiced building searches from scratch using SPL syntax and pipe chaining

---

## SOC Analyst Takeaways

| Area | Insight |
|---|---|
| **Splunk Architecture** | Understanding the Forwarder → Indexer → Search Head pipeline helps analysts know how data is collected, stored, and retrieved |
| **Log Ingestion** | Correctly identifying sourcetypes during ingestion ensures fields are parsed accurately and searches return reliable results |
| **SPL Fundamentals** | SPL's pipe-based structure allows complex queries to be built step by step from simple, readable commands |
| **Time Scoping** | Always setting a time range before searching prevents slow, noisy queries and keeps investigations focused |
| **stats + sort** | Counting events by field and sorting by volume is a core triage technique for surfacing anomalies quickly |
| **Index Awareness** | In production SOC environments, knowing which index holds endpoint, network, or authentication logs is essential for efficient investigation |

---

## Skills Demonstrated

- Understanding of Splunk's three-tier architecture and data flow pipeline
- Ability to manually ingest log files and verify sourcetype assignment
- Navigation of the Splunk Search & Reporting App UI including indexes, fields, and time picker
- Writing and executing SPL searches using `search`, `stats`, `table`, `sort`, and `dedup`
- Applying field-based filters to isolate specific events within a dataset
- Aggregating and summarizing log data to identify patterns and high-volume sources
- Building searches from scratch using pipe-chained SPL syntax

---

## Real-World Application

As a Tier 1 SOC Analyst, Splunk will be the central platform for daily alert investigation and log analysis. Core workflows include:

- Searching Splunk in response to SIEM alerts triggered by detection rules
- Pivoting through logs to build a timeline of suspicious activity
- Using `stats`, `table`, and `timechart` to summarize findings for escalation notes
- Scoping searches by index and sourcetype to focus on relevant data sources
- Identifying high-frequency events that may indicate brute force attempts, scanning activity, or data exfiltration

---

## Next Steps

- [ ] Complete TryHackMe **Splunk 2** room — advanced SPL, alerts, and correlation searches
- [ ] Practice SPL drills: brute force detection, process execution hunting, network anomalies
- [ ] Build Splunk-based detection queries for the `soc-playbook-suite` GitHub repo
- [ ] Map SPL searches to MITRE ATT&CK techniques for portfolio documentation

---

*Part of the THM-Labs portfolio series — Michael King, SOC Analyst in Training*
