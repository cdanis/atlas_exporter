# atlas_exporter 
[![Build Status](https://travis-ci.org/czerwonk/atlas_exporter.svg)](https://travis-ci.org/czerwonk/atlas_exporter)
[![Docker Build Statu](https://img.shields.io/docker/build/czerwonk/atlas_exporter.svg)](https://hub.docker.com/r/czerwonk/atlas_exporter/builds)
[![Go Report Card](https://goreportcard.com/badge/github.com/czerwonk/atlas_exporter)](https://goreportcard.com/report/github.com/czerwonk/atlas_exporter)

Metric exporter for RIPE Atlas measurement results

## Remarks
* this is an early version, more features will be added step by step
* at the moment only the last result of an measurement is used
* the required Go version is 1.9+.

## Install
```
go get -u github.com/czerwonk/atlas_exporter
```

## Docker
```
docker run -d --restart unless-stopped -p 9400:9400 czerwonk/atlas_exporter
```

## Usage
### Start server
```
./atlas_exporter
```
or using config file mode:
```
./atlas_exporter -config.file config.yml
```

### Config file
for this example we want to retrieve results for measurement 8772164
```YAML
measurements:
  - 8772164
 ```

### Call metrics URI
when using config file mode:
```
curl http://127.0.0.1:9400/metrics
```
or ad hoc for measuremnt 8772164:
```
curl http://127.0.0.1:9400/metrics?measurement_id=8772164
```
in both cases the result should look similar to this one:
``` 
# HELP atlas_traceroute_hops Number of hops
# TYPE atlas_traceroute_hops gauge
atlas_traceroute_hops{asn="1101",dst_addr="8.8.8.8",dst_name="8.8.8.8",ip_version="4",measurement="8772164",probe="6031"} 9
atlas_traceroute_hops{asn="11051",dst_addr="8.8.8.8",dst_name="8.8.8.8",ip_version="4",measurement="8772164",probe="17833"} 8
atlas_traceroute_hops{asn="111",dst_addr="8.8.8.8",dst_name="8.8.8.8",ip_version="4",measurement="8772164",probe="6231"} 9
atlas_traceroute_hops{asn="11427",dst_addr="8.8.8.8",dst_name="8.8.8.8",ip_version="4",measurement="8772164",probe="1121"} 13
atlas_traceroute_hops{asn="12337",dst_addr="8.8.8.8",dst_name="8.8.8.8",ip_version="4",measurement="8772164",probe="267"} 13
atlas_traceroute_hops{asn="1257",dst_addr="8.8.8.8",dst_name="8.8.8.8",ip_version="4",measurement="8772164",probe="140"} 11
atlas_traceroute_hops{asn="12586",dst_addr="8.8.8.8",dst_name="8.8.8.8",ip_version="4",measurement="8772164",probe="2088"} 13
atlas_traceroute_hops{asn="12597",dst_addr="8.8.8.8",dst_name="8.8.8.8",ip_version="4",measurement="8772164",probe="2619"} 10
atlas_traceroute_hops{asn="12714",dst_addr="8.8.8.8",dst_name="8.8.8.8",ip_version="4",measurement="8772164",probe="2684"} 9
atlas_traceroute_hops{asn="133752",dst_addr="8.8.8.8",dst_name="8.8.8.8",ip_version="4",measurement="8772164",probe="6191"} 14

...
```

## Features
* ping measurements (success, min/max/avg latency, dups, size)
* traceroute measurements (success, hop count, rtt)
* ntp (delay, derivation, ntp version)
* dns (succress, rtt)
* http (return code, rtt, http version, header size, body size)  
* sslcert (alert, rtt)

## Configuration (Prometheus)
```
  - job_name: 'atlas_exporter'
    scrape_interval: 5m
    static_configs:
      - targets:
        - 7924888
        - 7924886
    relabel_configs:
      - source_labels: [__address__]
        regex: (.*)(:80)?
        target_label: __param_measurement_id
        replacement: ${1}
      - source_labels: [__param_measurement_id]
        regex: (.*)
        target_label: instance
        replacement: ${1}
      - source_labels: []
        regex: .*
        target_label: __address__
        replacement: atlas-exporter.mytld:9400

```

## Third Party Components
This software uses components of the following projects
* Go bindings for RIPE Atlas API (https://github.com/DNS-OARC/ripeatlas)
* Prometheus Go client library (https://github.com/prometheus/client_golang)

## License
(c) Daniel Czerwonk, 2017. Licensed under [LGPL3](LICENSE) license.

## Prometheus
see https://prometheus.io/

## The RIPE Atlas Project
see http://atlas.ripe.net

## Further reading
I wrote an article about atlas_exporter for RIPE Labs. It covers version 0.5.
https://labs.ripe.net/Members/daniel_czerwonk/using-ripe-atlas-measurement-results-in-prometheus-with-atlas_exporter
