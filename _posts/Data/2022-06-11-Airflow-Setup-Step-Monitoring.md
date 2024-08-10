---
title:  "Airflow 구축 및 변천사 - 모니터링"
date:   2022-06-11 09:00:00 +0900
categories:
  - Data
  - Airflow
tags:
  - Data
  - Airflow
  - Workflow
toc: true
toc_sticky: true
toc_label: "Airflow 모니터링"
header-img: https://user-images.githubusercontent.com/78892113/172186672-025cb8a1-cddf-4b90-b08c-a8c3634cbb90.png

header-mask: true

---



&nbsp;안녕하세요 마개입니다.  
Airflow를 처음 도입하게 된 배경부터 구축을 넘어 마지막으로는 모니터링 부분에 대해 정리하고자 합니다. 
{: .notice--info}

<br>

관련 글들은 아래 링크로 확인하실 수 있습니다.

* <a href="https://magaetube.github.io/data/airflow/Airflow-Setup-Step-Intro/">Airflow 구축 및 변천사 - 도입 배경 및 선정</a>
* <a href="https://magaetube.github.io/data/airflow/Airflow-Setup-Step-Setup/">Airflow 구축 및 변천사 - 구축</a>
* <a href="https://magaetube.github.io/data/airflow/Airflow-Setup-Step-Improvement/">Airflow 구축 및 변천사 - 개선</a>
* <a href="https://magaetube.github.io/data/airflow/Airflow-Setup-Step-Monitoring/">Airflow 구축 및 변천사 - 모니터링</a>

<br>

![airflow](https://user-images.githubusercontent.com/78892113/172186672-025cb8a1-cddf-4b90-b08c-a8c3634cbb90.png){: .align-center}{: width="80%" height="80%"} 

<br><br><br>

&nbsp;모니터링은 `Airflow`를 운영하는 데 있어 중요한 요소가 됩니다. 관리 서버가 늘어남에 따라 서버에 문제가 없는지 `Airflow` 컨테이너들은 살아있는지 `DAG`는 실행이 되는지 등 여러 측면에서 중요한 요소입니다. 모니터링 영역은 크게는 메트릭 영역, 서비스 영역 두 가지로 생각했습니다.  

모니터링을 구축하는 기술 부분에서도 변화가 있었습니다.

* 초기 : StatsD, Node Exporter + Prometheus + Grafana
* 개선 : Telegraf + InfluxDB + Grafana

<br><br>

## 초기 : StatsD + Node Exporter + Prometheus
&nbsp;처음에는 Airflow 공식 문서에서 제공하는 방법인 `StatsD`를 이용했습니다.  
&nbsp;StatsD는 NodeJS에서 실행되는 데몬 프로그램입니다. 이를 통해 <u>Airflow의 Counters, Gauges, Timers 통계 정보</u>를 얻을 수 있습니다. 각 정보에 대해서는 <a href="https://airflow.apache.org/docs/apache-airflow/stable/logging-monitoring/metrics.html">공식 문서</a>를 참고하시길 바랍니다. 서비스 측면에서는 `StatsD`를 이용했다면 시스템 측면에서는 `Node Exporter`를 이용했습니다. Node Exporter는 <u>OS 정보나 하드웨어 정보 등 시스템 메트릭을 수집하는 Exporter</u>입니다. 이 Node Exporter로 CPU나 Memory 정보 등을 수집하여 대시보드로 보여주었습니다. `Prometheus`는 모니터링 시스템과 Timeseries 데이터용 데이터베이스입니다. 오픈소스로 메트릭 수집 및 대시보드를 구축하는데 Grafana와 함께 많이 사용합니다. 마지막으로 `Grafana`는 대시보드 툴입니다. 시계열 데이터에 대한 대시보드에 특화되어 있는 대시보드 툴로 Graphite, InfluxDB, Prometheus 등을 지원합니다. 

<br><br>

### 설치
#### StatsD
&nbsp;Airflow 서비스들을 모두 Docker로 구성했기 때문에 StatsD 또한 Docker로 구성했습니다. 이미지는 `prom/statsd-exporter`를 이용했습니다.

```yaml
# docker-compose.yaml
version: '3'
...

services:
...
  statsd-exporter:
    image: prom/statsd-exporter
    container_name: airflow_statsd_exporter
    command: "--statsd.listen-udp=:8125 --web.listen-address=:9102 --statsd.mapping-config=/tmp/statsd_mapping.conf"
    ports:
      - 9102:9102
      - 8125:8125/udp
    volumes:
      - ./statsd_mapping.conf:/tmp/statsd_mapping.conf
    networks:
      - airflow

...
```

&nbsp;Docker의 서비스는 위와 같이 진행했습니다. 하나 특이한 점은 `statsd_mapping.conf`를 추가해서 적용시켰다는 점입니다. 해당 부분을 따로 지정하지 않으면 Airflow에서 기본적으로 제공하는 방식을 이용할 수 있습니다. 하지만 몇 번 사용하다 보니 조금 더 보기 쉬운 방법이 필요해서 다른 매핑 방법을 찾아서 적용했습니다. 매핑 정보는 <a href="https://github.com/databand-ai/airflow-dashboards/blob/main/statsd/statsd.conf">Databand-ai</a>에서 가져왔습니다.

​StatsD 서비스를 올렸으니 Airflow에서 설정을 변경해 줘야 합니다

```
# .env
AIRFLOW__METRICS__STATSD_ON: 'true'
AIRFLOW__METRICS__STATSD_HOST: 'statsd-exporter'
AIRFLOW__METRICS__STATSD_PORT: 8125
AIRFLOW__METRICS__STATSD_PREFIX: 'airflow'
```

&nbsp;이렇게 세팅을 하고 실행을 해서 제대로 Metrics 정보가 추출되는지 확인합니다. (HOST에서 'statsd-exporter'는 Docker 서비스명입니다.) StatsD가 설치된 서버의 `9102 포트`에 접속해서 데이터를 확인하면 됩니다.

<br><br>

#### Node Exporter

Node Exporter의 경우도 마찬가지로 Docker로 구성을 했습니다. 이 이미지 또한 `prom/node-exporter`로 구성했습니다. 

```yaml
# docker-compose.yaml
version: '3'
...

services:
...
  node-exporter:
    image: prom/node-exporter
    container_name: airflow_node_exporter
    ports:
      - 9100:9100
    restarts: always
    networks:
      - airflow

...
```

&nbsp;Node Exporter의 경우 시스템 메트릭이기 때문에 다른 세팅을 할 필요는 없습니다. 메트릭 정보는 해당 서버의 `9100포트`로 접속하면 확인하실 수 있습니다.

<br><br>

#### Prometheus & Grafana

메트릭 정보를 저장할 데이터베이스와 대시보드 역할을 할 `Prometheus`와 `Grafana`는 또 다른 서버에 Docker로 구성했습니다. 

```yaml
# docker-compose.yaml
version: '3'
...

services:
  prometheus:
    image: prom/prometheus
    container_name: prometheus
    ports:
      - 9090:9090
    volumes:
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
      - ./prometheus/data:/prometheus
    networks:
      - monitoring

  grafana:
    image: grafana/grafana:8.3.3
    container_name: grafana
    depends_on:
      - prometheus
    environment:
      GF_SECURITY_ADMIN_USER: Grafana아이디
      GF_SECURITY_ADMIN_PASSWORD: Grafana비밀번호
      GF_PATHS_PROVISIONG: /etc/grafana/provisioning
    ports:
      - 3000:3000
    volumes:
      - ./grafana/data:/var/lib/grafana
    networks:
      - monitoring
...
```

Docker의 경우 위와 같이 서비스를 구성했습니다. 

`Prometheus`의 경우 <u>다른 Exporter들로부터 데이터를 Pull 해오는 방식이기 때문에 Exporter 들과의 연결 설정이 필요</u>합니다. 

```yml
# prometheus.yml
global:
  scrape_interval: 30s
  evaluation_interval: 30s
  scrape_timeout: 10s

scrape_configs:
  ...
  - job_name: 'statsd-exporter'
    static_configs:
      - targets: ['StatsD서버1:포트(9102)', 'StatsD서버2:포트(9102)', ...]

  - job_name: 'node-exporter'
    static_configs:
      - targets: ['NodeExporter서버1:포트(9100)', 'NodeExporter서버2:포트(9100)']
```

&nbsp;위와 같이 Prometheus를 설정하고 Docker를 실행해서 컨테이너 생성하고 `9090포트`로 접속해서 Exporter 연결 여부와 데이터가 정상적으로 수집되는지 확인합니다. `Grafana` 대시보드의 경우 이후에 다시 보도록 하겠습니다. 

<br><br>

## 개선 : Telegraf + InfluxDB + Grafana
&nbsp;Exporter들과 Prometheus을 이용하다가 `Telegraf`와 `InfluxDB`로 변경하기로 했습니다. 이유는 다음과 같습니다. 

* StatsD와 Node 각 Exporter를 무겁게 쓰는 것보단 **하나의 Agent**로 관리하자
* 사내에서 **통합으로 사용**하고 있는 모니터링 시스템에 적용하기로 위해 InfluxDB로 변경하자

<br><br>

### 설치
&nbsp;변경하는 내용은 각 서버에 설치해놓은 StatsD와 Node Exporter를 빼고 `Telegraf`로 교체한다는 것, 모니터링 서버에서는 Prometheus를 빼고 `InfluxDB`로 대체한다는 점입니다. 

<br><br>

#### InfluxDB
&nbsp;`InfluxDB`는 1.x 버전과 2.x 버전이 많이 다른 모습을 보입니다. InfluxDB도 Docker로 구성했는데 2.x 버전으로 구성을 했습니다.

```yaml
version: '3'
...
services:
  ...
  influxdb:
    image: influxdb:2.1.1
    container_name: influxdb
    restart: always
    volumes:
      - ./influxdb2:/var/lib/influxdb2:rw
    ports:
      - 8086:8086
    networks:
      - monitoring
...
```

&nbsp;모니터링 서비스들을 작성했던 docker-compose(Prometheus, Grafana)에서 Prometheus를 빼고 `InfluxDB`를 작성했습니다. InfluxDB를 2.x 버전으로 새로 구축한다면 `Organization`과 `token` 등을 설정해야 합니다. 해당 정보를 설정하는 방법에 대해서는 다루지 않겠습니다. 설정한 정보들 중에서 이후에 필요한 정보들은 `Organization`, `token`, `Bucket` 명입니다.

<br><br>

#### Telegraf
`Telegraf`도 StatsD와 마찬가지로 각 Airflow 서버에 설치할 것이기 때문에 Airflow의 설정을 변경해야 합니다. 

```
AIRFLOW__METRICS__STATSD_HOST: 'telegraf'
```

다른 부분은 같은데 HOST만 Telegraf의 Docker 서비스명으로 변경합니다. 

```yaml
# docker-compose.yaml
version: '3'
...

services:
  ...
  telegraf:
    image: telegraf
    container_name: airflow_telegraf
    restart: always
    volumes:
      - ./telegraf.conf:/etc/telegraf/telegraf.conf
      - /var/run/docker.sock:/var/run/docker.sock
    environment:
      - DOCKER_INFLUXDB_INIT_ORG=InfluxDB Organization명
      - DOCKER_INFLUXDB_INIT_BUCKET=InfluxDB 버킷명
      - DOCKER_INFLUXDB_INIT_ADMIN_TOKEN=InfluxDB 토큰정보
      - HOST_NAME=airflow_main
    networks:
      - airflow
    
    ...
```


여기서 봐야 할 부분은 다음과 같습니다.

* telegraf.conf
* docker.sock
* InfluxDB 정보
* HOST_NAME

<br>

​**(1) telegraf.conf**

`telegraf.conf` 파일을 통해 어떠한 정보들을 **수집(input)** 하고 해당 정보들을 어디로 **보낼지(output)**에 대해 정의합니다.

```
# telegraf.conf
[agent]
  ...
  hostname = "$HOST_NAME"     # 내가 원하는 host명으로 보냅니다.
  omit_hostname = false       # true이면 agent에 "host" 태그를 세팅하지 않습니다.

...

[[inputs.statsd]]
  protocol = "udp"
  ...
  service_address = ":8125"
  templates = [
    "*.dag.*.*.duration measurement.measurement.dag_id.task_id.measurement",
    "*.dagrun.dependency-check.* measurement.measurement.measurement.dag_id",
    "*.dag_processing.last_duration.* measurement.measurement.measurement.dag_id",
    "*.dagrun.duration.*.* measurement.measurement.measurement.status.dag_id",
    "*.dagrun.schedule_delay.* measurement.measurement.measurement.dag_id",
    "*.dag_processing.last_runtime.* measurement.measurement.measurement.dag_id",
    "*.dag.loading-duration.* measurement.measurement.measurement.dag_id",
    "*.operator.*.* measurement.measurement.status.operator"
  ]
  ...

[[inputs.docker]]
  container_names = []
  ...

[[outputs.influxdb_v2]]
  urls = ["InfluxDB호스트:8086"]
  token = "$DOCKER_INFLUXDB_INIT_ADMIN_TOKEN"
  organization = "$DOCKER_INFLUXDB_INIT_ORG"
  bucket = "$DOCKER_INFLUXDB_INIT_BUCKET"
  ...

```

Telegraf 설정을 하는데 중요한 부분만 몇 가지 보겠습니다. Telegraf를 통해서 수집하고자 하는 내용은 크게 3가지입니다. CPU, Memory 등 기본 시스템 메트릭 (위에는 표기하지 않았습니다.), Airflow 메트릭, Docker 메트릭입니다. 

* `[agent]` 부분은 hostname을 Webserver/Scheduler 서버, Worker서버 등 각 서버별로 다르게 설정했습니다. 이 정보는 docker-compose에서 지정한 변수명을 따라갑니다. 
* `[[inputs.statsd]]` 부분은 Airflow 설정에서 지정한 정보들을 입력했는데 여기서는 templates로 Airflow에서 제공하는 기본 형태를 변경했습니다. (StatsD에서는 mapping.conf 부분)
* `[[inputs.docker]]` 부분은 Docker의 Container 정보를 수집하고자 설정했습니다. 
* `[[outputs.influxdb_v2]]` 은 데이터를 보내기 위한 InfluxDB 정보를 입력했습니다. token이나 organization은 전부 docker-compose에서 지정한 정보를 가져와서 입력하게 됩니다.

<br>

**(2) docker.sock**

&nbsp;`docker.sock`은 Docker 컨테이너 내부에서 데몬과 상호 작용을 할 수 있게 해주는 Unix 소켓인데 <u>Docker 정보를 수집</u>하기 위해서는 이 파일이 있어야 합니다. 그렇기 때문에 volume으로 마운트 했습니다. 

<br>

* Trouble shooting

 docker.sock를 마운트 했지만 Docker 정보가 제대로 수집되지 않는 경우가 생길 수 있습니다.

```sh
2021-11-03T12:40:00Z E! [inputs.docker] Error in plugin: Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get "http://%2Fvar%2Frun%2Fdocker.sock/v1.21/info": dial unix /var/run/docker.sock: connect: permission denied,
```

Telegraf 로그를 보면 위와 같은 메시지가 발생할 수 있는데 이는 `docker.sock의 권한`을 봐야 합니다.

```sh
$ ls -rtl /var/run/docker.sock
srw-rw---- 1 root docker 0  2월 24 14:35 docker.sock
```

위와 같이 권한이 660(rw-rw----)으로 되어있다면 `666`으로 변경합니다.

```sh
$ sudo chmod 666 /var/run/docker.sock
srw-rw-rw- 1 root docker 0 Mar 10 04:21 /var/run/docker.sock
```

<br>

**(3) InfluxDB 정보**

&nbsp;`Telegraf.conf`에서 잘 보면 InfluxDB로 보내는 것은 두 가지가 있습니다. 1.x 버전과 2.x 버전 (`[[outputs.influxdb_v2]]`은 2.x 버전입니다.) 각자 사용하시는 InfluxDB의 버전에 맞게 작성하시면 됩니다. docker-compose의 InfluxDB 서비스에 아까 위에서 만들었던 InfluxDB의 정보들을 작성하시면 됩니다. 

<br>

**(4) HOST_NAME**

&nbsp;`HOST_NAME`의 경우 각 서버에 맞게 이름을 정했습니다. 저의 경우 Webserver와 Scheduler가 있는 서버는 airflow_main, Worker는 airflow_worker1, 2 등 구분할 수 있는 HOST 명을 지정하면 됩니다. 

<br><br>

#### Grafana
&nbsp;이렇게 `Telegraf`와 `InfluxDB`를 통해 수집된 정보들을 `Grafana`로 대시보드를 구성했습니다. Grafana에서는 기본적으로 다양한 대시보드 템플릿을 제공하고 있습니다. ​위의 사이트에서 각자의 상황에 맞는 `Data Source`, `Collector Types`을 지정하고 검색하면 다양한 대시보드가 나옵니다. 여기서 각자의 버전에 맞고 본인이 생각하기에 이쁜(?) 대시보드를 찾아서 적용하면 됩니다. InfluxDB의 경우 1.x나 2.x의 Query 방식 자체가 다르기 때문에 반드시 본인의 InfluxDB에 맞는 것을 찾아서 적용해야 합니다. 
&nbsp;시스템 메트릭의 경우 마음에 드는 대시보드를 이용해서 적용하고 그 외에는 Docker와 Airflow에 대한 정보를 따로 적용했습니다.

![image](https://user-images.githubusercontent.com/78892113/175764654-b9ad90d9-a512-4f59-8835-6ce57ff8a488.png){: .align-center}


대시보드의 경우 위와 같은 형태로 만들었습니다. (자세한 정보는 보여드리지 못하는 점 양해 부탁드립니다.)

<br><br>

## 결론
&nbsp;총 4편으로 Airflow를 처음에 구축하고 개선, 모니터링까지 했던 경험을 정리해 보았습니다. `Elasticsearch`에 데이터 로그 축적하기, 모니터링을 더 구체적으로 하기 등 더 개선해야 할 부분들은 있다고 생각합니다. 이러한 개선 작업들에 대해서는 추후에 하나씩 개선하고 포스팅하도록 하겠습니다.