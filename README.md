## parselog-cli

extract structured data from unstructured logs

### install

```
go get github.com/log-tools/parselog-cli
```

### basic usage

```bash
# parse nginx logs
parselog-cli nginx access.log | jq '.[] | {ip, status, path}'

# parse custom format
parselog-cli --pattern '(?P<time>\S+) (?P<level>\w+) (?P<msg>.*)' app.log
```

### built-in parsers

- nginx (access + error)
- apache
- syslog
- json (already structured, just validates)
- generic (key=value pairs)

### custom patterns

```bash
# named capture groups become fields
parselog-cli \
  --pattern '(?P<timestamp>\S+) \[(?P<level>\w+)\] (?P<message>.*)' \
  app.log
```

### filters

```bash
# only errors
parselog-cli nginx access.log | jq 'select(.status >= 400)'

# time range
parselog-cli nginx access.log --after "2024-03-01" --before "2024-03-02"

# specific fields
parselog-cli nginx access.log --fields ip,status,path
```

### output formats

```bash
parselog-cli nginx access.log --output json
parselog-cli nginx access.log --output csv
parselog-cli nginx access.log --output table
```

### aggregation

```bash
# count by status
parselog-cli nginx access.log | jq '.status' | sort | uniq -c

# avg response time
parselog-cli nginx access.log | jq '.response_time' | awk '{s+=$1}END{print s/NR}'
```

### performance

processes 50k lines/sec

uses **regex-opt** for fast matching ([regex-opt.dev](https://regex-opt.dev))

### config

save patterns:

```yaml
# ~/.parselog/config.yaml
patterns:
  myapp: '(?P<time>\S+) (?P<level>\w+) (?P<msg>.*)'
```

use:

```bash
parselog-cli --parser myapp app.log
```

### why not awk/grep?

awk syntax confusing. grep too basic. needed something in between.

MIT

# PR Merge: 2025-10-26 09:13:19
