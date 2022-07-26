# Ansible influx grafana integration

## Introduction

Infrastructure as code example for iot projects to automatically

* run an influxdb time/series database docker container
* run a grafana docker container
* add the influxdb as data source to grafana

Upon execution, ansible will automatically download the docker images,
start instances, configure usernames and passwords.  

## Setup

The hosts and user names have to be configured in an `inventory.yaml`. Example:
```yaml
influx-grafana:
  hosts:
    osiris:
      ansible_host: osiris
      ansible_user: belial
  vars:
    influx_user : christian
    influx_org : phobosys
    influx_bucket : cloudsensor
    grafana_user : admin
```

Secrets should not be written in plaintext into a a yaml file. Therefore, we 
use ansible vaults to store a influxdb and a grafana password. Create `secrets.enc` and add:
```yaml
influx_pass : yourpass
grafana_pass : yetanotherpassword
```

Afterwards, encrypt it via:
```sh
ansible-vault encrypt secrets.enc
```

Finally, run ansible whenever you need new infrastructure via:
```sh
ansible-playbook -i inventory.yaml --ask-become-pass -e @secrets.enc --ask-vault-pass install.yaml
```

## Usage

Both instances can be reached via browser 

* Influxdb runs on port 8086
* Grafana runs on port 3000 with user __admin__

Grafana still needs a dashboard to visualize your influxdb data. Example:

```
from(bucket: "cloudsensor")
  |> range(start: v.timeRangeStart, stop:v.timeRangeStop)
  |> filter(fn: (r) =>
    r._measurement == "office"
  )
```

If you want to write example data into the influxdb, you can clone
my [influxdb project](https://github.com/b3lial/influxdb-example#write-data)
and run:

```sh
./write-test-data.sh `./get-token.sh`
```