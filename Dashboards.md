# Dashboards

## Logs

Create the following variables and default values.
- `scope = .+`
- `min_severity = 0`
- `max_severity = 24`

`scope` is a textbox variable. `min_severity` and `max_severity` are custom variables with the values `0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24`.

### Volume

Plot the logs volume in a "time series" panel.
- Set the max data points to 100 in query options.
- Plot the data as bars with full opacity.
- Stack the series.

```
sum by(severity_bucket) (count_over_time({service_name="vector"}
    | scope_name=~"$scope"
    | severity_number >= $min_severity
    | severity_number <= $max_severity
    | label_format severity_bucket="{{ div (add .severity_number 3) 4 }}"
[$__interval]))
```

The query buckets severities for readability. Create "fields with name" overrides to assign better display names and colors to each bucket. I was unable to do this in the UI; I created the first override in the UI, and I edited the dashboard JSON for the remaining ones.

### Lines

Plot the logs lines in a "logs" panel.

```
{service_name="vector"}
| scope_name =~ "$scope"
| severity_number >= $min_severity
| severity_number <= $max_severity
| line_format "[{{.scope_name}}] {{__line__}}"
```
