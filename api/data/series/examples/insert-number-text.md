# Series Insert Not A Number 

## Description

Insert text value along with numeric value. 

## Request

### URI

```elm
POST https://atsd_host:8443/api/v1/series/insert
```

### Payload
```json
[
    {
        "entity": "sensor-01",
        "metric": "temperature",
        "data": [
            {
                "d": "2016-10-14T08:15:00Z",
                "v": 12.4,
				"x": "Provisional"
            }
        ]
    }
]
```

## Response 

```
```
