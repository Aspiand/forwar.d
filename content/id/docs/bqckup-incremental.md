+++
title = 'Bqckup Incremental'
draft = true
date = '2025-07-20T20:35:54+08:00'
+++

Pada versi [1.6.0](https://github.com/bqckup/bqckup/releases/tag/v1.6.0), bqckup memperkenalkan fitur baru, yaitu backup incremental. Bqckup menggunakan rustic untuk implementasi fitur ini. Rustic dipilih karena performa yang jauh lebih baik dari pada restic, walaupun begitu hal tersebut juga mengakibatkan penggunaan ram yang banyak. Untuk sekarang, bqckup hanya mendukung incremental backup untuk S3 (compatible storage).

Agar backup dilakukan dengan incremental, perlu dilakukan konfigurasi pada konfigurasi situs.
Contoh: `domain.com.yml`

```diff
bqckup:
  name: domain.com
  enabled: yes
+ incremental:
+   enable: yes
+   password: admin#1234 # required if incremental
```

Bqckup akan membaca file `domain.com.yml` dan `storages.yml` lalu membuat konfigurasi rustic yang terletak pada `/etc/rustic/domain.com.toml`.

## Backup

Backup dapat dilakukan menggunakan command yang sama dengan sebelumnya.

```sh
bqckup run
```

Bqckup akan menjalankan `rustic` sebagai *subprocess*, kemudian rustic memecah file menjadi chunk-chunk kecil. Chunk akan dikompress dan dienkripsi sebelum disimpan ke repository restic yang ada di cloud. Ketika backup dijalankan, rustic akan membuat snapshot baru yang berisi kondisi file dan directory yang dibackup.

## Restore

Untuk melakukan restore, terdapat perintah baru yang dibuat khusus untuk hal ini.

```sh
bqckup restore {site_name} --snapshot {snapshot_id|default:latest} --target {target_dir|default:directory_source}
```

Snapshot id dapat dilihat pada history backup

```sh
bqckup history --site domain.com
```

Snapshot id berada pada field file name.
