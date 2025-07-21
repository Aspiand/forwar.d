+++
title = 'Bqckup Remote Storage'
draft = true
date = '2025-07-21T11:35:14+08:00'
+++

## Remote Storage

For better security, storage credentials can be stored separately and retrieved via a RESTful API. To enable this feature, add the following configuration:

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

Ensure that the target URL follows the specifications below.

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

After completing a backup and `after_backup_completed` configuration is set, bqckup will send relevant backup data to url.

Ensure that the target URL follows the specifications below.

```text
POST {base_url}
```

**Body:**

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

## General API Error

`400`: Bad Request

`401`: Unauthorized

```json
{
    "error": "string",
    "message": "string"
}
```
