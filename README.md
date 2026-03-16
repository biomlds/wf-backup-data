# wf-backup

Robust backup workflow for ONT MinKNOW and EPI2ME data with verification and optional source deletion.


## Introduction

This workflow performs robust backup of ONT MinKNOW data and EPI2ME instance data to a destination folder. It includes verification steps to ensure data integrity and optionally deletes source files after successful backup.

**Features:**
- Backup ONT MinKNOW data (excluding `pod5` folder)
- Backup EPI2ME instance data
- Verification using rsync with checksum comparison
- JSON manifest generation for audit trail
- Optional source deletion after verified backup


## Compute requirements

Recommended requirements:

+ CPUs = 2
+ Memory = 2GB

Minimum requirements:

+ CPUs = 1
+ Memory = 1GB

Approximate run time: Varies by data size

ARM processor support: True



## Install and run

These are instructions to install and run the workflow on command line.
You can also access the workflow via the
[EPI2ME Desktop application](https://labs.epi2me.io/downloads/).

The workflow uses [Nextflow](https://www.nextflow.io/) to manage
compute and software resources,
therefore Nextflow will need to be
installed before attempting to run the workflow.

The workflow can currently be run using either
[Docker](https://docs.docker.com/get-started/)
or [Singularity](https://docs.sylabs.io/guides/3.0/user-guide/index.html)
to provide isolation of the required software.
Both methods are automated out-of-the-box provided
either Docker or Singularity is installed.
This is controlled by the
[`-profile`](https://www.nextflow.io/docs/latest/config.html#config-profiles)
parameter as exemplified below.

It is not required to clone or download the git repository
in order to run the workflow.
More information on running EPI2ME workflows can
be found on our [website](https://labs.epi2me.io/wfindex).

The following command can be used to obtain the workflow.
This will pull the repository in to the assets folder of
Nextflow and provide a list of all parameters
available for the workflow as well as an example command:

```
nextflow run epi2me-labs/wf-backup --help
```
To update a workflow to the latest version on the command line use
the following command:
```
nextflow pull epi2me-labs/wf-backup
```

For further information about running a workflow on
the command line see https://labs.epi2me.io/wfquickstart/



## Input parameters

### ONT Data

| Nextflow parameter name  | Type | Description | Help | Default |
|-------------------------|------|-------------|------|---------|
| ont_data | string | Path to ONT MinKNOW data folder. | Path to ONT MinKNOW data folder (e.g., /home/minit/Desktop/MinKnowData/Run5_20260303_YH/*/*_*_*-*_*_*/). The pod5 folder will be excluded from backup. |  |
| ont_data_dest | string | Destination folder for ONT data backup. |  |  |

### EPI2ME Data

| Nextflow parameter name  | Type | Description | Help | Default |
|-------------------------|------|-------------|------|---------|
| epi2me_data | string | Path to EPI2ME instance folder. | Path to EPI2ME instance folder. |  |
| epi2me_data_dest | string | Destination folder for EPI2ME data backup. |  |  |

### Backup Options

| Nextflow parameter name  | Type | Description | Help | Default |
|-------------------------|------|-------------|------|---------|
| backup_options.delete_source | boolean | Delete source files after successful backup | Delete source files only after rsync with checksum verification passes. The source folder is deleted and recreated as an empty directory. | False |

### Output Options

| Nextflow parameter name  | Type | Description | Help | Default |
|-------------------------|------|-------------|------|---------|
| out_dir | string | Directory for output of all workflow results. |  | output |



## Outputs

| Title | File path | Description |
|-------|-----------|-------------|
| Workflow report | wf-backup-report.html | Report for backup workflow. |
| ONT data backup manifest | manifest_ont_data.json | JSON manifest containing checksums of backed up ONT data files. |
| EPI2ME data backup manifest | manifest_epi2me_data.json | JSON manifest containing checksums of backed up EPI2ME data files. |
| ONT backup log | backup_ont.log | Log file for ONT data backup process. |
| EPI2ME backup log | backup_epi2me.log | Log file for EPI2ME data backup process. |

Output directory structure:
```
output/
├── ont_data/           # ONT backup (minus pod5 folder)
├── epi2me_data/        # EPI2ME backup (first-level files)
├── manifest_ont_data.json
├── manifest_epi2me_data.json
├── backup_ont.log
├── backup_epi2me.log
└── wf-backup-report.html
```


## Backup Process

The workflow performs the following steps for each data source:

1. **rsync with checksum verification**: Uses `rsync -avc --checksum` to copy and verify files in a single step
   - ONT: Excludes `pod5` folder
   - EPI2ME: Full recursive copy

2. **Manifest generation**: Creates JSON manifest with MD5 checksums for each backed up file

3. **Optional source deletion**: If `--backup_options.delete_source` is enabled and backup succeeded, the source folder is deleted and recreated as an empty directory



## Troubleshooting

+ If the workflow fails please check the log files (`backup_ont.log` and `backup_epi2me.log`) for detailed error messages.
+ Ensure source and destination paths are accessible and have appropriate permissions.
+ The workflow requires at least one source data path to be specified.



## FAQs

If your question is not answered here, please report any issues or suggestions on the [github issues](https://github.com/biomlds/wf-template/issues) page or start a discussion on the [community](https://community.nanoporetech.com/).

