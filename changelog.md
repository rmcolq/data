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
* `Foel` -- second generation CLIMB-COVID ingest system

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

***
# 2022-02-23
## Majora Magenta
* Effective immediately, "Test Majora", lovingly known as "Majora Magenta" has moved URL to `https://majora-test.covid19.climb.ac.uk/`.
    * The test database has not been migrated and users will need to register new accounts and new OAuth applications.

***
# 2022-02-18
## Elan
### Changed
* The location of individual BAM and BAI files are being migrated over the weekend. While all care is being taken to minimise impact to users, attempts to access individual BAM and BAI files may fail over this weekend period.

***
# 2022-02-04
## Foel
### Changed
* Foel will now reject BAM submissions if the BAM file is larger than 2 GB (defined as 2e9 bytes) 

***
# 2022-02-01
## Elan
### Removed
* The "individual FASTA" directory at `/bham/artifacts/published/fasta` has been removed
* The "individual BAM" directory at `/bham/artifacts/published/alignment` has been removed

***
# 2022-01-28
## Elan
### Removed
* New FASTA files are no longer linked in `/cephfs/covid/bham/artifacts/published/fasta`
    * Users should extract their sequences directly from the daily consensus FASTA (`elan.consensus.fasta`), leveraging its index. A sequence extraction utility (`seq_extract`) is available via [our utilities repository](https://github.com/CLIMB-COVID/utilities).
* New BAM files are no longer linked in `/cephfs/covid/bham/artifacts/published/alignment`
    * Users must now resolve the location of BAM files using `/cephfs/covid/artifacts/elan/latest/majora.pag_lookup.tsv`
* As a side effect of an update to the publishing pipeline, the `/cephfs/covid/bham/artifacts/published/20220128` directory was accidentally removed. As we are working towards removing these directories anyway, the change will remain and these "dated artifact" directories will no longer be published.
    * Users can continue to use `/cephfs/covid/bham/artifacts/published/latest` which will contain the latest artifacts to maintain compatibility

### Changed
* The files detailing which samples were missing and removed by Elan have moved from `/cephfs/covid/bham/artifacts/published/latest/summary` to `/cephfs/covid/artifacts/elan/latest/`:
    * Use `elan.missing.ls` for determining why samples were not ingested by Elan (missing metadata or missing files)
    * Use `elan.quickcheck.ls` for samples rejected by Elan screening (invalid FASTA or BAM)

***
# 2022-01-25
## Datapipe
### Changed
* Created a separate nextflow for geography cleaning steps, moving them earlier in the pipeline. Results should be unaffected.

## Elan
### Changed
* `/cephfs/covid/artifacts` is the new top-level home for artifacts
    * Moving the location of the artifacts is necessary for longer term maintenance of the CLIMB-COVID project and additionally allows us to work toward solving the infamous "big dir" problem.
* The FASTA header for the daily `elan.consensus.fasta` will no longer contain the pipe delimited "row number" and will now only contain the Published Artifact Group (PAG) name. The FAI index will therefore only contain the PAG names, making it easier to maintain random access to sequences. Pipe delimited metadata may be added again in future as a sequence comment, rather than as part of the sequence header.
  * Python code using `seq_name.split('|')[0]` to parse this header will be unaffected, as the split will succeed, but developers can now just use `seq_name`.

### Added
* `/cephfs/covid/artifacts/elan/latest/majora.pag_lookup.tsv` now allows users to write scripts to look up (`central_sample_id` AND `run_name`) tuples, OR `pag_name` to resolve the locations of published BAMs

### Deprecated
* The individual FASTA and BAM directories at `/bham/artifacts/published/fasta` and `/bham/artifacts/published/alignment` will be removed without exception on 2022-01-31. These directories contain hundreds of thousands of symlinks and need to be removed as part of our solution to the "big dir" problem.
    * FASTA users: As per previous guidance, users should extract their sequences directly from the daily consensus FASTA (`elan.consensus.fasta`), leveraging its index. For users unsure how to do this effectively, we have now made a sequence extraction utility (`seq_extract`) available via [our utilities repository](https://github.com/CLIMB-COVID/utilities).
    * BAM users: Users must now resolve the location of BAM files using the new `majora.pag_lookup.tsv`, first published today

## Majora, Ocarina
### Changed
* The minimum Ocarina client version for Majora to accept requests has been bumped to v`0.44.0`. Requests from clients below this version number will be rejected immediately.

***
# 2022-01-24
## Elan
### Deprecated
* After consultation, the "individual QC" outputs are now considered deprecated and will be removed without warning in the near future. A new API service will allow users to fetch QC information as an alternative.
* The "individual FASTA" outputs are now considered deprecated and will be removed without warning in the immediate future. Users should follow our recommendation and ensure they are using the daily consensus FASTA and the corresponding FAI index to query for sequences as an alternative.

***
# 2022-01-13
## Elan
### Changed
* Elan will now add files, metrics and QC reports to Majora as `service-elan`, not `nicholsz`

## Foel
### Changed
* Foel will now add empty biosamples, libraries and sequencing runs to Majora as `service-foel`, not `nicholsz`

***
# 2022-01-06
## Elan
### Changed
* Elan will refuse to process FASTA files containing one or more non-IUPAC characters or `-`

***
# 2021-12-08
## Majora
### Changed
* `api.artifact.biosample.addempty` supports using an optional `metadata` parameter to add key value metadata to empty biosamples

***
# 2021-12-03
## Foel
### Added
* `sample_route` is now a required field in the metadata CSV

### Changed
* The minimal `csv_template_version` has been increased to `2` to coincide with the new `sample_route` field. **Submissions using `csv_template_version` 1 will be rejected**.

## CLIMB-COVID
### Changed
* Automated GISAID submissions are now made every day, with a seven day lag

***
# 2021-11-03
## Majora
### Changed
* `INVITROGEN` added to `test_kit` ct validator

## datapipe
### Changed
* Substitute 'N' for '?' in input FASTA. (continue otherwise to reject all FASTA input sequences with non-IUPAC characters)

***
# 2021-10-29
## datapipe
### Changed
* Add flag `--score-N=0` to minimap2 command as part of MSA building step
* Reject all FASTA input sequences with non-IUPAC characters

***
# 2021-10-23
## datapipe
### Changed
* Temporary fix of replacing ? characters in incoming FASTA with Ns
* Add further parallelization and line count checks to handle file corruptions

***
# 2021-10-14
## phylopipe
### Changed
* Attempted switch back to big tree - one off tree published, minus the microreact outputs

***
# 2021-09-21
## phylopipe
### Changed
* Ran with refreshed base tree (~475k tips down from 1.4m tips)

***
# 2021-09-23
## Majora
### Fixed
* Majora will now correctly reject biosamples with a `collection_date` after the `received_date` with an error message: "Sample cannot be collected after it was received. Perhaps they have been swapped?"

***

# 2021-09-21
## datapipe
### Changed
* Added gzipped output files to s3 bucket 

***

# 2021-09-01
## datapipe
### Changed
* Updated memory requirements for publish steps
* Parallelized minimap2 and alignment steps

***

# 2021-08-18
## CLIMB-COVID
### Changed
* The head node has moved to different hardware. SSH users and sequence uploaders should be aware that the ESDCA key has been changed.
    * Users should not be alarmed by the rather alarming message (below) that will be issued on the command line the first time they attempt to log in after this change:
    ```
    @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
    @    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
    @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
    IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
    ```
    * Follow the instructions in the error message to remove the offending keys from your `known_hosts` file. The line numbers to remove will be listed in the message, **for example** this message would indicate you must remove line 8 from your `known_hosts` file:
    ```
    Offending key for IP in /home/user/.ssh/known_hosts:8 <= number after the colon is the line number, this is just an example
    ```

***

# 2021-08-11
## Ocarina
### Changed
* Ocarina 0.43.0 now allows OAuth authentication for `pag suppress`, users will need to update to 0.43.0 for the new configuration to work.
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
