+++
title = 'Incremental Bqckup'
draft = false
date = '2025-07-21T07:28:02+08:00'
+++

As of version 1.6.0, bqckup introduces a new feature: incremental backups. This functionality is implemented using Rustic, which was chosen due to its significantly better performance compared to Restic. However, this performance improvement comes at the cost of higher RAM usage.

Currently, bqckup supports incremental backups only for S3-compatible storage.

To enable incremental backups, you need to update the site configuration file accordingly.
Example `domain.com.yml`:

```diff
bqckup:
  name: domain.com
  enabled: yes
+ incremental:
+   enable: yes
+   password: admin#1234 # required if incremental
```

bqckup reads both the `domain.com.yml` and `storages.yml` configuration files, then generates a Rustic configuration file located at `/etc/rustic/domain.com.toml`.

## Backup

Backups can be performed using the same command as before:

```sh
bqckup run
```

bqckup executes rustic as a subprocess. Rustic then splits files into small chunks. These chunks are compressed and encrypted before being uploaded to the cloud repository.

Each time a backup is run, Rustic creates a new snapshot that captures the current state of the files and directories being backed up.

## Restore

To perform a restore, a new dedicated command has been introduced:

```sh
bqckup restore {site_name} --snapshot {snapshot_id|default:latest} --target {target_dir|default:directory_source}
```

`site_name`: the name of the site configuration (e.g. domain.com)

`--snapshot`: optional; specify the snapshot ID to restore. If not provided, the latest snapshot will be used.

`--target`: optional; specify the target directory to restore files to. By default, files will be restored to their original source directory.

You can view available snapshot IDs by running:

```sh
bqckup history --site domain.com
```

The snapshot ID is shown under the file name field in the history output.
