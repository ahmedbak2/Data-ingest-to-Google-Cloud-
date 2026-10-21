# Data Ingest to Google Cloud

Data engineering pipeline designed to systematically extract, warehouse, and transform weekly bestseller data from the New York Times API using Google Cloud BigQuery.

## Overview

This project builds an automated pipeline that connects to the New York Times Books API, extracts raw bestseller list data, stages it into a cloud data warehouse, and executes in-warehouse transformations for analytical reporting.

The focus of this project is demonstrating programmatic API interaction, raw data parsing, and executing ELT (Extract, Load, Transform) patterns on GCP.

## API Integration & Usage

The pipeline connects to the **NYT Books API v3** to collect structured data across multiple bestseller lists.

* **Endpoint Targeted:** `GET /svc/books/v3/lists/overview.json`
* **Authentication:** Query parameter API Key authentication (`api-key`).
* **Payload Handling:** The raw JSON payload returns nested lists containing details about lists, books, rank history, and metadata.
* **Response Normalization:** Python’s `pandas.json_normalize()` is used to flatten the nested JSON structure:
  * Extracting individual book objects from the `results.lists.books` path.
  * Preserving parent metadata fields (such as `list_name` and `updated`) across child rows.
  * Dropping redundant external links (Amazon URLs, Buy Links, ISBN arrays) to optimize payload size and data storage.

## Architecture & Pipeline Flow

```text
[ NYT Books API ] ---> [ Python / Pandas ] ---> [ Staged CSV ] ---> [ BigQuery Raw Table ] ---> [ BigQuery Cleaned Table ]
