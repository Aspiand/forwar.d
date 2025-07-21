+++
title = 'Bqckup Remote Storage'
draft = true
date = '2025-07-21T10:43:37+08:00'
+++

## Remote Storage

Untuk keamanan yang lebih baik, kredensial storage dapat disimpan ditempat lain dan diambil dengan Restfull API. Untuk menggunakan fitur ini tambahkan konfigurasi berikut:

```toml
; /etc/bqckup/bqckup.cnf
[storage]
remote_storage_endpoint=
```

```yaml
# /etc/bqckup/config/storages.yml
storages:
  dummy:
    bucket: dummy
    primary: no
+   remote: yes
```

Passtikan url yang dituju mengikuti spesifikasi berikut.

```text
GET {base_url}/{bucket_name}
bucket_name: string|required
```

`404`: Not Found

`200`: OK

```json
{
    "access_key_id": "string",
    "secret_access_key": "string",
    "endpoint": "string|url",
    "region": "string"
}
```

## Backup Notification

Setelah melakukan backup dan `after_backup_completed` di set, bqckup akan mengirim data terkait backup.

Passtikan url yang dituju mengikuti spesifikasi berikut:

```text
POST {base_url}
```

Body

```json
{
    "domain": "string|required",
    "hostname": "string|required",
    "total_size": "integer64|required", // -1 for not changed
    "size": "integer64|required",
    "start_at": "integer64|required",
    "finish_at": "integer64|required",
    "status": "string|enum[failed, completed]|required",
    "backup_method": "string|enum[tar, incremental]|required"
}
```

`201`: Create Success

Konfigurasi URL:

```toml
; /etc/bqckup/bqckup.cnf
[webhooks]
after_backup_completed=
```

## Kesalahan Umum

`400`: Bad Request

`401`: Unauthorized

```json
{
    "error": "string",
    "message": "string"
}
```
