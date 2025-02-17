# Changelog
All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## Unreleased
### Added
- SDAP-461: Added 4 remaining Saildrone insitu datasets.
### Changed
- SDAP-453: Updated results storage and retrieval to support output JSON from `/cdmsresults` that matches output from `/match_spark`.
  - **NOTE:** Deploying these changes to an existing SDAP deployment will require modifying the Cassandra database with stored results. There is a script to do so at `/tools/update-doms-data-schema/update.py`
  - Additional changes:
    - Made the `output` parameter case-insensitive
    - Improved speed of results insert
    - Updated `id` field of insitu points to include depth. This solves an issue with NetCDF result rendering where different insitu observations at the same place & time but at different depths were being excluded for having the same `id`.
- SDAP-466: Matchup now defines secondary `platform` fields with `platform.id` if it is available and not blank. It then uses `platform.code` and `platform.type` as fallbacks, then just the value of `platform` if none work.
- SDAP-468: Updated matchup output filename
### Deprecated
### Removed
- SDAP-465: Removed `climatology` directory. 
### Fixed
### Security

## [1.1.0] - 2023-04-26
### Added
- Deletebyquery: Parameter to set the number of rows to fetch from Solr. Speeds up time to gather tiles to delete; especially when there is a lot of them.
- Added Saildrone's `baja_2018` insitu dataset.
- SDAP-454: Added new query parameter `prioritizeDistance` to matchup algorithm
- SDAP-459: Added explicit definitions of min/max lat/lon values in nexustiles Solr collection creation script
### Changed
- SDAP-443:
  - Replacing DOMS terminology with CDMS terminology:
    - Renaming endpoints:
      - `/domsresults` -> `/cdmsresults`
      - `/domslist` -> `/cdmslist`
    - Removed `/domsvalues` from Swagger UI
  - Swagger UI updates:
    - `platforms` parameter in `/match_spark` is now a multi-select list.
    - Added note to `/stats` endpoint to note it is limited to satellite datasets
- SDAP-450: Updated helm chart to reflect k8s 1.22 changes. Bumped RMQ dependency version & updated Bitnami dependency chart URLs. Ingress template is already up to date.
- SDAP-451: Updated quickstart documentation
  - Specified default Docker platform as `linux/amd64`
  - Added copy button extension for RDT pages
  - Corrected volume mount paths for Solr and Cassandra containers
  - Fixed (potential) bug in RMQ monitor script
  - Updated granule download section: Replaced with a script that also accounts for dataset migration
  - Bumped image tag versions (1.0.0 -> 1.1.0)
### Deprecated
### Removed
### Fixed
- Made `platforms` param optional in `/cdmssubset`, and removed int requirement
- Updated OpenAPI specification for `/cdmssubset` to accurately reflect `platforms` and `parameter` field options.
- SDAP-436: Added special case for handling Cassandra SwathMulti tiles with uniform time arrays
- SDAP-449: Fixed `/cdmsresults` NetCDF output displaying and downloading as .txt. 
- SDAP-449: Fixed 404 error when populating datasets; script was still using `/domslist`
- SDAP-415: Fixed bug where mask was incorrectly combined across all variables for multi-variable satellite to satellite matchup
- SDAP-434: Fix for webapp Docker image build failure
- SDAP-412: Explicit definition of `__eq__` and `__hash__` in matchup `DomsPoint` class. This ensures all primary-secondary pairs with the same primary point are merged in the `combineByKey` step.
- SDAP-438: Replace variable value NaN with None to fix error in result storage
- SDAP-444: Fixed `resultSizeLimit` param in `/match_spark` truncating the results that are stored for the results endpoint.
- Fixed minor webapp image build issue
### Security

## [1.0.0] - 2022-12-05
### Added
- SDAP-388: Enable SDAP to proxy/redirect to alternate SDAP
- SDAP-372: Updated matchup algorithm to point to AWS insitu API endpoint
- SDAP-372: Added new matchup endpoint `match_spark_doms` that points to DOMS insitu endpoint
- SDAP-372: Updated `match_spark_doms` to interface with samos_cdms endpoint 
- SDAP-393: Included `insitu` in ingress based on the value of `insituAPI.enabled` in `values.yaml`
- SDAP-371: Renamed `/domssubset` endpoint to `/cdmssubset`
- SDAP-390: Updated NetCDF reader tool for data matchup and added user functionality.
- SDAP-396: Added saildrone insitu api to matchup
- SDAP-398: Added script for regression tests.
- Matchup validates insitu parameter using insitu API schema endpoint
- Added domsresults endpoint to openapi spec
- Added markdown table to matchup `platform` param in openapi spec
- SDAP-400: Added NCAR insitu api to matchup
- SDAP-405: Added SPURS AWS insitu api to matchup and new platform values to OpenAPI matchup spec
- RabbitMQ monitor script used in Docker quickstart guide
- Added new option for NCAR so either NCAR or JPL Insitu API can be specified
- SDAP-407: Added depth to `/domsresults` endpoint
- Added documentation for building SDAP docker images
  - Prepared documentation for v1.0.0 release.
- Added missing ASF headers to all .py files in this repository.
- Added ASF `README` for release.
### Changed
- SDAP-390: Changed `/doms` to `/cdms` and `doms_reader.py` to `cdms_reader.py`
- domslist endpoint points to AWS insitu instead of doms insitu
- Matchup returns numSecondary and numPrimary counts rather than insitu/gridded
- SDAP-402: Changed matchup matchOnce logic to match multiple points if same time/space
- Bumped ingress timeout in Helm chart to reflect AWS gateway timeout
- SDAP-399: Updated quickstart guide for standalone docker deployment of SDAP.
- SDAP-399: Updated quickstart Jupyter notebook
- SDAP-411: Used global versioning for SDAP NEXUS artifacts
- SDAP-416: Using mamba to install nexus-analysis dependencies. (Also using conda as a fallback option if mamba install fails)
### Deprecated
### Removed
- removed dropdown from matchup doms endpoint secondary param
- Matchup no longer returns insituMatched and griddedMatched fields
### Fixed
- Fix failing test_matchup unit test
- Fixed bug in OpenAPI spec where both matchup endpoints shared the same id
- Fixed null value for count in matchup response
- SDAP-371: Fixed DOMS subset endpoint
- SDAP-397: Added depth to matchup insitu output
- SDAP-397: Pull ID from insitu api response for matchup
- SDAP-397: Added null unit field for later use in matchup
- Fixed issue where satellite satellite matchups failed
- Fixed issue where null results were returned if more than "resultSizeLimit" matches are found
- Preserve return value of `query_insitu_schema()` in matchup to avoid excessive API hits 
- Fixed issue where satellite to satellite matchups with the same dataset don't return the expected result
- Fixed CSV and NetCDF matchup output bug
- Fixed NetCDF output switching latitude and longitude
- Fixed import error causing `/timeSeriesSpark` queries to fail.
- Fixed bug where domsresults no longer worked after successful matchup
- Fixed certificate error in Dockerfile
- SDAP-403: Remote timeout fix and HofMoeller bug fix
- Fixed matchup insitu query loading on import; loads when needed instead
- SDAP-406: Fixed `/timeSeriesSpark`comparison stats bug
- Fixed excessive memory usage by `/cdmssubset`
### Security


