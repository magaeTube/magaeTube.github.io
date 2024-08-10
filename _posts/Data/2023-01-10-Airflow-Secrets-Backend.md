---
title:  "[Airflow] Secrets Backend"
date:   2023-01-10 00:00:00 +0900
categories:
  - Data
  - Airflow
tags:
  - Data
  - Airflow
toc: true
toc_sticky: true
toc_label: "Secrets Backend"
header-img: https://user-images.githubusercontent.com/78892113/209359696-bdb63ef2-8e14-41f2-8e04-2c29049aeabf.png
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
일반적으로 connections와 variables는 환경변수 또는 메타스토어 데이터베이스에서 조회가 가능하지만 대체안으로 `AWS SSM Parameter Store`, `Hashicorp Vault Secrets` 또는 직접 구성하여 사용할 수 있습니다. 이번에는 이 Secrets Backend에 대해 알아봅니다. 여기서는 Vault를 기준으로 파악해봅니다. 다른 것들은 아래의 링크를 통해 확인해보면 됩니다.  
{: .notice--info}

<br>

![image](https://user-images.githubusercontent.com/78892113/209359696-bdb63ef2-8e14-41f2-8e04-2c29049aeabf.png){: .align-center}{: width="70%" height="70%"} 

<br><br>

* 참고 URL : <a href="https://airflow.apache.org/docs/apache-airflow/1.10.10/howto/use-alternative-secrets-backend.html">공식 홈페이지</a>

<br><br>

## 조회 경로

`connection`이나 `variable`을 찾을 때 Airflow는 환경 변수에서 먼저 찾고 그 다음으로 메타스토어 데이터베이스에서 찾습니다. 하지만 대체안으로 `secrets backend`을 사용한다면 이것이 첫 번째가 되고 그 다음으로 환경변수, 메타스토어 데이터베이스 순서대로 찾습니다. 이러한 순서는 변경할 수 없는 부분입니다. 

<br><br>

## Configuration

Secrets Backend를 이용하기 위해서는 configuration에서 `[secrets]` 세션에 지정해야 합니다.

<br>

```conf
[secrets]
backend = 
backend_kwargs = 
```

* `backend` : 적용을 하고 싶은 backend의 전체 클래스명을 작성합니다.
* `backend_kwargs` : JSON의 형태로 작성하여 위에서 지정한 클래스의 `__init__`에 넘겨주는 변수들을 지정합니다.

<br><br>

## Hashicorp Vault Secrets Backend

Hashicorp Vault를 적용하기 위해서는 설정값을 아래와 같이 변경합니다.

<br>

### Configuration

```conf
[secrets]
backend = airflow.contrib.secrets.hashicorp_vault.VaultBackend
backend_kwargs = {"connections_path": "connections", "variables_path": "variables", "mount_point": "airflow", "url": "http://127.0.0.1:8200"}
```

위에서 KV 버전에 대한 값은 없는데 기본값은 2입니다. 만약에 KV Secrets 엔진 버전이 1이면 `kv_engine_version: 1`을 작성해서 적용하면 됩니다. 

만약에 URL을 위에 작성하는 것이 싫다면 환경변수로 지정하면 됩니다. 환경변수는 아래와 같이 지정합니다.

```sh
$ export VAULT_ADDR="http://127.0.0.1:8200"
```

<br>

### Connections 저장 및 조회

위에서 지정한 `backend_kwargs`에서 `connections_path`를 `connections`, `mount_point`를 `airflow` 라고 지정하고 connection id가 `smtp_default`라면 아래와 같이 저장하려고 할 수 있습니다.

```sh
$ vault kv put airflow/connections/smtp_default conn_uri=smtps://user:host@relay.example.com:465
```

정보를 얻을 때는 아래와 같이 명령어를 입력하면 됩니다.

```sh
❯ vault kv get airflow/connections/smtp_default
====== Metadata ======
Key              Value
---              -----
created_time     2020-03-19T19:17:51.281721Z
deletion_time    n/a
destroyed        false
version          1

====== Data ======
Key         Value
---         -----
conn_uri    smtps://user:host@relay.example.com:465
```

<br>

### Variables 저장 및 조회

Connections를 진행할 때와 비슷합니다. 위에 지정한 `backend_kwargs`에서 `variables_path`를 `variables`, `mount_point`를 `airflow`라고 지정하고 variable의 key가 `hello`라면 아래와 같이 지정해서 저장할 수 있습니다.

```sh
$ vault kv put airflow/variables/hello value=world
```

저장을 했다면 마찬가지로 조회해서 값을 확인할 수 있습니다.

```sh
❯ vault kv get airflow/variables/hello
====== Metadata ======
Key              Value
---              -----
created_time     2020-03-28T02:10:54.301784Z
deletion_time    n/a
destroyed        false
version          1

==== Data ====
Key      Value
---      -----
value    world
```