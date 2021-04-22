---
layout: docpost
title: CLIMB-COVID Data Changelog
date_published: 2021-04-22 14:00:00 +0000
date_modified:  2021-04-22 14:00:00 +0000
author: samstudio8
maintainer: samstudio8
logo: assets/dipi-patch.png
logo_alt: CLIMB-COVID DIPI Mission Patch
---

All notable changes to CLIMB-COVID data or interchange formats that have impact to users or other pipelines should be documented in this file.
Changes described here may only be a subset of all changes to a project as this log concerns itself only with changes that impact how data is provided or consumed by users or other pipelines.
The following DIPI projects are routinely using this CHANGELOG.

* `Majora API` -- metadata APIs
* `Ocarina` -- Majora command line client ([Full changelog](https://github.com/SamStudio8/ocarina/blob/master/CHANGELOG.md))
* `Elan` -- inbound data pipeline
* `Tael` -- MQTT messaging tools
* `Asklepian` -- Outbound PHE pipeline

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

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
## Elan
### Changed
* [Elan will now start at 6am each day and finish in the afternoon](https://github.com/COG-UK/dipi-group/issues/31).
