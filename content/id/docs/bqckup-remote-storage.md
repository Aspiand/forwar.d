+++
title = 'Bqckup Remote Storage'
draft = false
date = '2025-07-21T10:43:37+08:00'
+++

## Remote Storage

Untuk keamanan yang lebih baik, kredensial storage dapat disimpan ditempat lain dan diambil dengan RESTful API. Untuk menggunakan fitur ini tambahkan konfigurasi berikut:

`/etc/bqckup/config/storages.yml`

```yaml
storages:
  dummy:
+   remote_url: https://domain.com/api/get?token=894211610151115239
```

Passtikan url yang dituju mengikuti spesifikasi berikut.

Method: `GET`

`404`: Not Found

`200`: OK

```json
{
    "bucket": "string",
    "access_key_id": "string",
    "secret_access_key": "string",
    "endpoint": "string|url",
    "region": "string"
}
```

## Backup Notification

Setelah melakukan backup dan `after_backup_completed` di set, bqckup akan mengirim data terkait backup.

Passtikan url yang dituju mengikuti spesifikasi berikut:

Method: `POST`

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

Konfigurasi Webhook:

`/etc/bqckup/bqckup.cnf`

```toml
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
