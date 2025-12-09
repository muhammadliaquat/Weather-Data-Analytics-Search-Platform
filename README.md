******Weather Data Analytics & Search Platform******
-----------------------------------------------------------------------------------------------
****Milestone 1 – Project Description****
-----------------------------------------------------------------------------------------------

Goal:

Collect and analyze historical weather data (temperature, rainfall, humidity, wind speed, etc.).

Use Elasticsearch for fast analytics and aggregation queries.

Provide REST APIs for querying analytics.

Visualize trends over time or per city.

Inputs:

Dataset with fields: city, date, temperature_max, temperature_min, humidity, rainfall, wind_speed, etc.

Outputs / Reports:

Top 10 hottest/coldest cities in a given period

Rainfall distribution across cities

Seasonal temperature trends

Monthly average temperature per city

Anomaly detection (extreme temperatures, unusual rainfall)
Optionally: JSON/CSV export of analytics
Actors:
Admin or professor (requests analytics)
Success Criteria:

Elasticsearch index created and data ingested successfully
REST APIs return analytics correctly

Postman tests pass

Basic dashboard  visuals available

----------------------------------------------------------------------------------------------------
****Milestone 2 –Use Cases****
----------------------------------------------------------------------------------------------------

Use Case 1 — Ingest Weather Dataset

Actor: Admin / Developer
Goal: Load historical weather data (CSV/JSON) into Elasticsearch for analytics.
Trigger: Admin uploads a dataset via REST API or runs the ingestion script.

Main Flow:

Admin selects CSV/JSON file from local system.

Server parses the dataset into JSON documents.

Server sends bulk index requests to Elasticsearch (weather_data index).

Elasticsearch indexes documents and returns acknowledgment for each batch.

API responds with total number of records ingested and any errors.

Alternate Flow / Error Handling:

If the dataset contains invalid data formats, the API returns a validation error with line numbers.

If Elasticsearch is unreachable, the API responds with a “cluster not reachable” error.

Use Case 2 — Retrieve Top Cities by Temperature

Actor: Admin / User
Goal: Identify hottest or coldest cities over a specific date range.
Trigger: GET request to /api/weather/top-cities with parameters: start, end, type (hot/cold), size.

Main Flow:

Server validates query parameters (dates, type, size).

Server sends an aggregation query to Elasticsearch:

Filters by date range

Aggregates by city

Calculates average temperature_max for hot or temperature_min for cold cities

Sorts results descending (hot) or ascending (cold)

Elasticsearch returns aggregation results.

API responds with JSON containing top N cities with their average temperatures.

Alternate Flow / Error Handling:

If no data exists in the date range, API responds with empty array and a message.

If query parameters are invalid, API returns HTTP 400 with validation error.

Use Case 3 — Rainfall Distribution Analytics

Actor: Admin / User
Goal: Aggregate rainfall data per city or month to analyze precipitation patterns.
Trigger: GET request to /api/weather/rainfall-distribution with start and end dates.

Main Flow:

Server validates date range.

Server sends aggregation query to Elasticsearch:

Filters documents by date range

Aggregates rainfall sum per city or per month (based on request)

Elasticsearch returns aggregation buckets.

API responds with JSON: each city/month and total rainfall.

Alternate Flow / Error Handling:

If no rainfall data is found, API responds with empty buckets.

If Elasticsearch is down, API returns HTTP 503 with error message.

Use Case 4 — Seasonal Temperature Trends

Actor: Admin / User
Goal: Identify temperature patterns over months or seasons.
Trigger: GET request to /api/weather/temperature-trends with start, end, and optional interval (day/week/month).

Main Flow:

Server validates input parameters.

Server sends date_histogram aggregation query to Elasticsearch on date field.

Aggregates average temperature_max and temperature_min per interval.

Elasticsearch returns buckets with interval and aggregated temperature.

API responds with JSON showing trends over the requested period.

Alternate Flow / Error Handling:

If no records exist in the date range, API responds with empty trends array.

If interval parameter is invalid, API defaults to month and returns a warning.

Use Case 5 — Anomaly Detection

Actor: Admin / User
Goal: Detect extreme weather events (temperature spikes, heavy rainfall).
Trigger: GET request to /api/weather/anomalies with optional threshold parameters (temp_max_threshold, rainfall_threshold).

Main Flow:

Server validates thresholds.

Server sends Elasticsearch query with filters:

temperature_max >= temp_max_threshold

OR rainfall >= rainfall_threshold

Elasticsearch returns matching records.

API responds with JSON listing anomalies: date, city, metric, value.

Alternate Flow / Error Handling:

If no anomalies are detected, API returns empty array with message “No anomalies found.”

If thresholds are missing, defaults are applied (e.g., top 1% values).

Use Case 6 — Export Analytics Data

Actor: Admin / User
Goal: Export queried analytics data to JSON or CSV for reporting.
Trigger: GET request to /api/weather/export with format=json or format=csv and query parameters (start, end, metric).

Main Flow:

Server validates input parameters and format.

Server queries Elasticsearch using same aggregation queries as analytics endpoints.

Server formats response in requested format (JSON/CSV).

API sends downloadable file or stream with content-type headers.

Alternate Flow / Error Handling:

If Elasticsearch returns no data, server sends empty file with a warning.

If requested format is unsupported, server returns HTTP 400 error.

Use Case 7 — REST API Access & Monitoring

Actor: Admin / Developer
Goal: Ensure API and Elasticsearch cluster are running and healthy.
Trigger: GET request to /api/health

Main Flow:

Server sends _cluster/health request to Elasticsearch.

Server collects cluster status and response time.

API responds with JSON: status: green/yellow/red, number of nodes, active shards.

Alternate Flow / Error Handling:

If Elasticsearch cluster is unreachable, API returns HTTP 503 with message “Cluster unreachable.”

Logs errors for debugging.
