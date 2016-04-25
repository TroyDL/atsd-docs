## Command: `property`

Insert property of specified type with keys and values for a given entity.

```
property e:{entity} s:{unix_seconds} t:{type} k:{key}={value} k:{key}={value} v:{name}={value} v:{name}={value}
```

> Example

```
property e:abc001 t:disk k:name=sda v:size=203459 v:fs_type=nfs
```

> UDP command

```
echo property e:DL1866 t:disk k:name=sda v:size=203459 v:fs_type=nfs | nc -u atsd_server.com 8082
```

| **Field** | **Required** |
|-----------|--------------|
| e         | yes          |
| s         | no           |
| t         | yes          |
| k         | no           |
| v         | yes          |