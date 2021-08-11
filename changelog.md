---
layout: docpost
title: CLIMB-COVID Data Changelog
date_published: 2021-04-22 14:00:00 +0000
date_modified:  2021-05-14 17:45:00 +0000
author: samstudio8
maintainer: samstudio8
logo: assets/dipi-patch.png
logo_alt: CLIMB-COVID DIPI Mission Patch
---

All notable changes to CLIMB-COVID APIs, data or interchange formats that have impact to users or other pipelines should be documented in this file.
Changes described here may only be a subset of all changes to a project as this log concerns itself only with changes that impact how data is provided or consumed by users or other pipelines.
The following DIPI projects are routinely using this CHANGELOG.

* `Majora API` -- metadata APIs
* `Ocarina` -- Majora command line client ([Full changelog](https://github.com/SamStudio8/ocarina/blob/master/CHANGELOG.md))
* `Elan` -- inbound data pipeline
* `Tael` -- MQTT messaging tools
* `Asklepian` -- Outbound PHE pipeline
* `CLIMB-COVID` -- metaprojects (eg. status page, data page)

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

***

# 2021-08-10
## Ocarina
### Changed
* Ocarina 0.42.1 uses exit codes as suggested by BSD `sysexits`. This may affect users catching particular non-zero exit codes from the Ocarina application.

***

# 2021-08-05
## Asklepian
### Changed
* To improve performance, the MSA generation algorithm will be swapped from `datafunk` to `gofasta`.
* Users should be aware (but not concerned) of a small impact to the integrity of 14 sequences in the MSA (and downstream Asklepian tables).
    * See the accompanying [change advisory notice](https://github.com/COG-UK/dipi-group/blob/official/notices/20210803_asklepian.md) for further information.

***

# 2021-07-17
## Majora
### Changed
* Majora will no longer accept a `collection_date` or `received_date` for a biosample where the year is not 2020 or higher, regardless of whether the biosample is being added or updated

## Tael
### Changed
* `mqtt-message` automatically adds `ts` key to payloads, containing the UNIX epoch time
    * Recommended that users no longer use the `date` and `time` fields for anything other than human readability

***

# 2021-07-13
## datapipe
### Changed
* Now runs `gofasta updown list` and creates output files for CIVET3

***

# 2021-07-07
## datapipe
### Changed
* Adds a metadata output to `cog/UTLA_genome_counts_<date>.csv`
* Excludes samples labelled to omit when the `why_excluded` column is not in published metadata output to avoid mysterious duplicate rows
    
***

# 2021-06-21
## CLIMB-COVID
### Changed
* Files and directories in user upload dirs (`climb-covid19-user/upload`) will be periodically scanned and deleted if they are more than two weeks old.
    * This first run on June 21st will remove data older than June 7th

***
# 2021-06-10
## Majora
### Changed
* Users with the `force_add_biosampleartifact` scope can now add `sender_sample_id` to blank biosamples created through the `biosample.addempty` endpoint ([Majora biosample.addempty docs](https://samstudio8.github.io/majora-docs/#add-one-or-more-empty-biosamples-to-majora)), regardless of whether the sample was previously added by `addempty` before
    * This does not change existing behaviour that prevents samples that have been "filled in" with full metadata via `biosample.add` from being modified by `biosample.addempty` 

***
# 2021-06-01
## Datapipe
### Changed
* Started performing geography cleaning of all adm1s (largely global cleaning)

## Majora
### Changed
* Users with the `force_add_biosampleartifact` scope can now add `sender_sample_id` to blank biosamples created through the `biosample.addempty` endpoint ([Majora biosample.addempty docs](https://samstudio8.github.io/majora-docs/#add-one-or-more-empty-biosamples-to-majora))
* Existing biosample artifacts that were collected and/or received more than a year ago can now be updated through the metadata uploader, or Ocarina (without `--partial`) as the past-date checks are now skipped for existing data
    * This does not change the behaviour of `collection_date` or `received_date` from over 365 days ago being rejected for new samples
    * `collection_date` and `received_date` set to a future date are still rejected regardless of whether the sample exists or not

## Ocarina
### Changed
* Ocarina 0.40.2 `ocarina empty biosample` now takes an additional `--sender-sample-id` option ([Ocarina Changelog](https://github.com/SamStudio8/ocarina/blob/master/CHANGELOG.md#0402-2021-05-25))

## Phylopipe2.0
### Changed
* Makes civet output directories identical to previous except for explicitly containing "public" or "private" in file names
* Adds additional masking before usher updates at sites which are known to cause usher problems placing on a tree
* Fixed phylotyping bug (change to `clusterfunk`)
* Will be published to `latest` and today's phylopipe1 output when it completes in 2 days time will be published to `old`

***
# 2021-05-31
## Datapipe
### Changed
* New columns added to metadata outputs in line with pangolin changes are now all populated
* The pillar_2 column has been renamed is_pillar_2 and contains Y/N for consistency with incoming pipeline
* Bug fixes adding cleanded geography to metadata
* Additional requested columns/metadata outputs published

***
# 2021-05-27
## Pangolin
### Changed
* Major release of pangolin v3.0 with downstream changes in datapipe, phylopipe, pathogenwatch expected in coming days
* Pangolin output now includes a new `version` column containing information about inference engine (pangoLEARN, usher or designation hash) and data release on which assignments were based, to be used instead of pangoLEARN_version column
* Pangolin outputs constellation calls made by scorpio for a number of VOCs/VUIs 

***
# 2021-05-18
## CLIMB-COVID
### Added
* Added [data site](https://data.covid19.climb.ac.uk/) link to accession table ([#63](https://github.com/COG-UK/dipi-group/issues/63))

***
# 2021-05-14
## Tael
### Fixed
* [Patched](https://github.com/SamStudio8/elan-nextflow/commit/1ae6b713284130eb1ae1304c8416153bd10d4e7f) a regression in `mqtt-client.py` that caused clients not requiring any environment variables (`--envreq`) to silently fail to start their specified command
    * **Clients not specifying `--envreq` should be restarted as soon as possible**

## Phylopipe
### Added
* Beta release of phylopipe2.0 published daily

***
# 2021-05-02
## Tael
### Added
* `mqtt-client.py` automatically subscribes clients to a "control topic" named `COGUK/infrastructure/pipelines/<who>/control`, where `who` is the name of the pipeline provided to `--who`
* Pipelines can now be manually raised by sending messages to the client control topic, specfying an `action` key with the value of `raise`

### Changed
* The `started` message emitted by `mqtt-client.py` now includes a `reason` key, explaining why the pipeline has started

***
# 2021-04-30
## Datapipe
### Changed
* Updated minimap2 sequence divergence threshold preset from 5% to 20% divergence as increasing numbers of (primarily GISAID) sequences were being lost from the MSA
* Passes `uk_lineage` metadata column through from previous datapipe run for use by phylopipe

***
# 2021-04-23
## Asklepian
### Changed
* [Genome table `Sequence` field is populated with MSA genome sequence rather than consensus sequence](https://github.com/COG-UK/dipi-group/issues/61)

***

# 2021-04-21
## Asklepian
### Added
* [New fields added to Genome table](https://github.com/COG-UK/dipi-group/issues/37)
    * Field `Adm1` added to Genome table
    * Field `Pillar` added to Genome table
    * Field `Published_date` added to Genome table

### Changed
* [Genome table is now delivered with `gzip` compression](https://github.com/COG-UK/dipi-group/issues/43)

***

# 2021-04-16
## CLIMB-COVID
### Added
* [CLIMB-COVID Status Page](https://status.covid19.climb.ac.uk/) is now automatically updated every day

***

# 2021-04-13
## Tael
### Added
* Payload keys targeted by `--envreq` are copied through to the output payload if `--envprefix` has not been provided. `--envprefix` still copies all payload keys to the output with the specified prefix.

### Changed
* Payload keys that will be passed through to the `finished` output payload will also be emitted in the `start` ouput payload

***

# 2021-04-12
## Majora API
### Changed
* [`ION_TORRENT` added to `instrument_make` validator](https://github.com/COG-UK/dipi-group/issues/46)
* [`103` added to `collection_pillar` validator](https://github.com/COG-UK/dipi-group/issues/48)

## Tael
### Added
* `--payload-passthrough` parameter allows keys from the input payload to be automatically copied to the `finished` output payload

***

# 2021-04-01
## Elan
### Changed
* [Elan will no longer sort uploaded BAMs](https://github.com/COG-UK/dipi-group/issues/39). User guidance has always been that BAMs should be sorted before uploaded so this step is a waste of compute resources and time. Unsorted BAMs will now be considered invalid and rejected by Elan as part of the quickcheck report.

***

# 2021-03-18
## Majora API
### Changed
* [`THERMO_AMPLITUDE` added to Ct `test_platform` validator](https://github.com/COG-UK/dipi-group/issues/36)
* [`TAQPATH_HT` added to Ct `test_kit` validator](https://github.com/COG-UK/dipi-group/issues/36)

***
# 2021-03-06
## DIPI
### Added
* Introduced this CHANGELOG to track notable changes to data and pipeline interchange formats. Significant changes before this date are out of the scope of this CHANGELOG.

## Elan
### Changed
* [Elan will now start at 6am each day and finish in the afternoon](https://github.com/COG-UK/dipi-group/issues/31).
