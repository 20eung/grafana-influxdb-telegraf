# InfluxDB 크기 조정

## Retention Policy (보존 정책)
- 자동으로 오래된 데이터부터 삭제시켜주는 정책.
- 설정된 시간이 지난 데이터들은 자동으로 만료(삭제) 시켜주는 정책.
- 데이터를 저장할 기간을 지정할 수 있음.
- 각 database마다 다르게 지정할 수 있고, 별도 설정을 하지 않으면 기본 정책이 만들어짐.

> $ vi /etc/influxdb/influxdb.conf
```
[meta]
  dir = "/var/lib/influxdb/meta"
  retention-autocreate = true


[retention]
  enabled = true
  check-interval = "30m"
```

# telegraf docker-compose 설정

cmd = '--config' '/etc/telegraf/telegraf.conf' '--config-directory' '/etc/telegraf/telegraf.d'
run = '/endpoint.sh'
