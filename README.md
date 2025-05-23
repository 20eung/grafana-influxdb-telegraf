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

```
version: '3.6'
services:
  telegraf:
    image: nuntz/telegraf-snmp
    container_name: telegraf
    hostname: telegraf
    restart: always
    environment:
      - TZ=Asia/Seoul
    volumes:
      - ./etc_telegraf:/etc/telegraf
      - ./etc_telegraf/.cache:/root/.cache
      - ./var_lib_telegraf:/var/lib/telegraf
      - ./var_log_telegraf:/var/log/telegraf
      - /etc/hosts:/etc/hosts
    command: "--config /etc/telegraf/telegraf.conf --config-directory /etc/telegraf/telegraf.d"
    ports:
      - '8125:8125'

# Use same docker network with Portainer
networks:
  default:
    external:
      name: portainer-network
```

# InfluxDB raw data로 쿼리하기

## 쿼리 최적화 방법
```sql
SELECT non_negative_derivative("ifHCInOctets", 1s) *8 FROM "snmp" WHERE ("hostname" = 'SKNet_PDC3F_MMR_7020SR_leaf_1' AND "ifName" = 'Ethernet26') AND $timeFilter GROUP BY "hostname", "ifName"
```

# Grafana Panel Query 샘플
```sql
SELECT non_negative_derivative("ifHCOutOctets", 1s) *8 FROM "snmp" 
WHERE ("hostname" = '7750SR-a8' AND "ifDescr"::tag != '')  AND $timeFilter 
GROUP BY "ifDescr"::tag
```
## Transformations

### 1. Rename fields by regex

Match: `/, 10.*Gig Ethernet(?: SFP)?, "(.*)"/`

Replace: `: $1`
