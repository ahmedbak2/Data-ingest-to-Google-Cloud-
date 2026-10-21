# Data-ingest-to-Google-Cloud-
Data ingest task to GCP Big query. 

New York Times Bestseller Data Pipeline
A data engineering pipeline designed to systematically extract, warehouse, and transform weekly bestseller data from the New York Times API using Google Cloud BigQuery.

Overview
This project builds an automated pipeline that connects to the New York Times Books API, extracts raw bestseller list data, stages it into a cloud data warehouse, and executes in-warehouse transformations for analytical reporting.

The focus of this project is demonstrating programmatic API interaction, raw data parsing, and executing ELT (Extract, Load, Transform) patterns on GCP.

API Integration & Usage
The pipeline connects to the NYT Books API v3 to collect structured data across multiple bestseller lists.

Endpoint Targeted: GET /svc/books/v3/lists/overview.json

Authentication: Query parameter API Key authentication (api-key).

Payload Handling: The raw JSON payload returns nested lists containing details about lists, books, rank history, and metadata.

Response Normalization: Python’s pandas.json_normalize() is used to flatten the nested JSON structure:

Extracting individual book objects from the results.lists.books path.

Preserving parent metadata fields (such as list_name and updated) across child rows.

Dropping redundant external links (Amazon URLs, Buy Links, ISBN arrays) to optimize payload size and data storage.

Architecture & Pipeline Flow
[ NYT Books API ] ---> [ Python / Pandas ] ---> [ Staged CSV ] ---> [ BigQuery Raw Table ] ---> [ BigQuery Cleaned Table ]




Extraction & Parsing: Python requests execute an HTTP GET request to the NYT API. The response payload is filtered and flattened into a structured DataFrame, then saved to a local CSV staging file.

Cloud Data Warehousing: The BigQuery Python Client SDK ingests the CSV file into a raw table (data-eng-proj-478713.raw_data.book3) with automatic schema detection enabled.

SQL Transformation: In-warehouse BigQuery SQL models process the raw table to produce a refined analytical dataset (cleaned_books).

Data Transformations
The transformation layer applies the following business logic inside BigQuery SQL:

Ranking Movement Analysis: Calculates week-over-week performance by comparing rank against rank_last_week. Assigns dynamic indicators like +2 for rank increases or "New Rank" for new entries.

String Cleanup: Strips redundant prefix text (such as "by ") from contributor strings using SQL regex/replacement functions.

Date Standardisation: Converts ISO-formatted timestamp strings into standard DD/MM/YYYY date representations.

Tech Stack
Language: Python

API Ingestion: Requests, Pandas (json_normalize)

Cloud Platform: Google Cloud Platform (GCP)

Data Warehouse: Google Cloud BigQuery

Transformation: BigQuery SQL
