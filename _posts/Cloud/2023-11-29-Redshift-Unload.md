---
title:  "[Redshift] Unload"
date:   2023-11-29 00:00:00 +0900
categories:
  - AWS
tags:
  - Cloud
  - AWS
  - Redshift
toc: true
toc_sticky: true
toc_label: "Redshift Unload"
header-img: https://github.com/magaeTube/magaeTube.github.io/assets/78892113/6e5c4a52-08fd-4048-ace8-18cba336c4e7
header-mask: true

---

&nbsp;안녕하세요 마개입니다.  
AWS Redshift의 여러 기능 중에 UNLOAD에 대해 살펴봅니다. <a href="https://docs.aws.amazon.com/ko_kr/redshift/latest/dg/r_UNLOAD.html">공식 문서</a>를 참고하여 정리합니다.
{: .notice--info}

<br>

![redshift](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/6e5c4a52-08fd-4048-ace8-18cba336c4e7){: .align-center}{: width="70%" height="70%"} 

<br><br>

## Unload 기능이란

Redshift상에 있는 대량의 데이터를 파일로 내려주고자 할 때 사용하는 기능입니다. 많은 데이터를 S3에 저장하고 분석을 하기 위해 사용하면 됩니다. 일반적인 SELECT로 검색하여 분석하는 것보다 속도가 더 빠릅니다.

<br>

## 필수 권한

UNLOAD를 사용하려면 S3에 쓸 수 있는 (Write) 권한과 Redshift에 SELECT 권한이 필요합니다.

<br>

## 구문

```sql
UNLOAD (
  'SELECT ...'
)
TO 'S3 Path'
IAM_ROLE
[Options]
```

기본적으로는 UNLOAD 명령어를 작성하면서 () 안에 SELECT 구문을 작성합니다. 해당 SELECT 구문은 작은 따옴표(') 안에서 작성을 하면됩니다. 이후에 TO로 데이터를 unload하고자 하는 S3 경로를 지정하면 됩니다. 경로는 Bucket명과 최종 목적지의 경로까지 작성하면 됩니다. 그러면 파일은 "0000_part_00.gz", "0001_part_00.gz"와 같은 형태로 저장됩니다. 만약에 경로에 Key까지 작성하게 되면 Key 뒤에 "0000_part_00.gz"이 붙게 됩니다. IAM_ROLE에는 위에 필수 권한에서 다룬 IAM을 지정해주면 됩니다. Options에는 각자의 여건에 따라 적용하도록 합니다. 몇가지 Option을 보겠습니다.

<br>

### Options

#### FORMAT 

```sql
UNLOAD (
  'SELECT ...'
)
TO 'S3 Path'
IAM_ROLE
[FORMAT [AS]] CSV | PARQUET | JSON
```

기본 형식을 정의하는 키워드입니다. `[FORMAT [AS]]`는 옵션으로 사용할 수 있습니다  (있어도 되고 없어도 됩니다). PARQUET는 Apache Parquet 버전 1.0 형식으로 파일에 언로드합니다. 기본적으로 압축은 SNAPPY 압축을 이용합니다. JSON의 경우 유효한 JSON읆 만들기 위해 각 열 이름이 고유해야 합니다. 각 포맷은 같이 사용할 수 없는 옵션들이 있습니다.

* CSV: FIXEDWIDTH, ADDQUOTES
* PARQUET: DELIMITER, FIXEDWIDTH, ADDQUOTES, ESCAPE, NULL AS, HEADER, GZIP, BZIP2, ZSTD
* JSON: DELIMITER, HEADER, FIXEDWIDTH, ADDQUOTES, ESCAPE, NULL AS

<br>

#### PARTITION BY

```sql
UNLOAD (
  'SELECT ...'
)
TO 'S3 Path'
IAM_ROLE
PARTITION BY (column_name) [INCLUDE]
```

파티션 키를 지정하는 옵션입니다. UNLOAD는 Apace Hive 규칙을 따르기 때문에 파티션 키 값을 기반으로 폴더가 자동 분할됩니다. `column_name`은 파티션하고자 하는 컬럼명이고 이는 unload하는 쿼리에 있는 컬럼을 지정해야 합니다. INCLUDE 옵션을 사용하면 파티션 컬럼이 unload된 파일에서 제거되지 않습니다.

<br>

#### HEADER

```sql
UNLOAD (
  'SELECT ...'
)
TO 'S3 Path'
IAM_ROLE
HEADER
```

각 출력 파일의 맨 위에 컬럼명을 포함하는 Header 줄을 추가합니다. HEADER는 FIXEDWIDTH와 함께 사용할 수 없습니다.

<br>

#### DELIMITER

```sql
UNLOAD (
  'SELECT ...'
)
TO 'S3 Path'
IAM_ROLE
DELIMITER [AS] 'delimiter-char'
```

출력 파일에서 컬럼을 구분할 문자를 지정합니다. 이때 단일 ASCII 문자를 지정해야하고 default는 텍스트 파일에서는 `|` (파이프) 문자, CSVsms `,` (쉼표) 문자입니다. DELIMITER는 FIXEDWIDTH와 함께 사용할 수 없습니다.

<br>

#### FIXEDWIDTH 

```sql
UNLOAD (
  'SELECT ...'
)
TO 'S3 Path'
IAM_ROLE
FIXEDWIDTH [AS] 'fixedwidth-spec'
```

각 컬럼의 너비를 고정된 길이인 파일로 데이터를 unload합니다. `fixedwidth-spec`에는 컬럼와 컬럼 너비를 지정해야 하는데 아래와 같이 지정합니다.

```sql
'col1:colWidth1,col2:colWidth2,...'
```

<br>

#### ENCRYPTED

```sql
UNLOAD (
  'SELECT ...'
)
TO 'S3 Path'
IAM_ROLE
ENCRYPTED [AUTO]
```

출력 파일을 서버측 암호화 또는 클라이언트 암호화를 사용하여 암호화할지에 대한 옵션입니다. MANIFEST가 지정되어 있는 경우에는 해당 파일도 암호화됩니다. ENCRYPTED 옵션이 없다면 S3 서버측 암호화(SSE-S3)를 사용하여 암호화를 진행합니다. ENCRYPTED의 경우 AWS KMS 키를 사용한 서버 측 암호화(SSE-KMS)를 사용하여 unload됩니다. 이 경우 `KMS_KEY_ID` 옵션을 이용하여 ID를 제공합니다.  
고객이 제공하는 대칭 키를 사용한 클라이언트 측 암호화를 사용하려면 `MASTER_SYMMETRIC_KEY` 또는 `CREDENTIALS` 옵션을 이용하면 됩니다.

<br>

#### KMS_KEY_ID

```sql
UNLOAD (
  'SELECT ...'
)
TO 'S3 Path'
IAM_ROLE
ENCRYPTED
KMS_KEY_ID 'key-id'
```

데이터를 암호화하기 위해 사용할 AWS Key Management Service (AWS KMS)의 키 ID를 지정합니다. `KMS_KEY_ID`를 사용할 경우 `CREDENTIALS` 파라미터를 같이 사용할 수 없습니다.

<br>

#### BZIP2, GZIP, ZSTD

```sql
UNLOAD (
  'SELECT ...'
)
TO 'S3 Path'
IAM_ROLE
BZIP2 or GZIP or ZSTD
```

파일을 압축하는 방식을 지정합니다. bzip2, gzip, Zstandard 방식으로 압축이 진행되고 파일의 확장명은 각각 `.bz2`, `.gz`, `.zst`이 따릅니다.

<br>

#### ADDQUOTES

```sql
UNLOAD (
  'SELECT ...'
)
TO 'S3 Path'
IAM_ROLE
ADDQUOTES
```

각 컬럼 데이터를 큰 따옴표(")로 감싸줍니다. DELIMITER에서 지정한 구분 기호가 데이터 자체에 있게 되면 문제가 생기기 때문에 이를 방지하기 위해 사용합니다. 예를 들어 구분 기호가 쉼표(,)인 경우 다음 데이터를 성공적으로 unload할 수 있습니다.

```sql
"1","Hello, World"
```

만약 큰 따옴표를 추가하지 않는다면 위 데이터에서 "Hello"와 "World"는 각 컬럼으로 구분되어 분석됩니다. ADDQUOTES를 사용한 경우 COPY에서 `REMOVEQUOTES`를 지정해야 합니다.

<br>

#### NULL AS

```sql
UNLOAD (
  'SELECT ...'
)
TO 'S3 Path'
IAM_ROLE
NULL AS 'null-string'
```

데이터에 있는 NULL을 대체해줄 문자열을 지정합니다. 이 옵션을 지정하지 않으면 NULL 값은 다음과 같이 대체됩니다.
* 제로 길이의 문자열
* 고정 너비 출력을 위한 공백 문자열

<br>

#### ESCAPE

```sql
UNLOAD (
  'SELECT ...'
)
TO 'S3 Path'
IAM_ROLE
ESCAPE
```

구분 기호로 분리된 파일에 있는 CHAR 및 VARCHAR 열의 경우, 다음 문자가 나올 때마다 이스케이프 문자(`\`)가 배치됩니다.
* 라인 피드: `\n`
* 캐리지 리턴: `\r`
* 구분 기호 문자
* 이스케이프 문자 : `\`
* 따옴표: `"` 또는 `'` (ESCAPE와 ADDQUOTES가 모두 지정된 경우)

UNLOAD에서 ESCAPE를 이용했다면 COPY에서도 ESCAPE를 무조건 사용해야 합니다.

<br>

#### ALLOWOVERWRITE

```sql
UNLOAD (
  'SELECT ...'
)
TO 'S3 Path'
IAM_ROLE
ALLOWOVERWRITE
```

기본적으로 S3에 파일이 있다면 에러를 발생합니다. ALLOWOVERWRITE를 지정하면 이러한 파일들이 있더라도 덮어씁니다.

<br>

#### CLEANPATH

```sql
UNLOAD (
  'SELECT ...'
)
TO 'S3 Path'
IAM_ROLE
CLEANPATH
```

CLEANPATH는 파일을 unload하기 전에 S3 경로에 있는 파일들을 제거합니다. 만약에 PARTITION BY 옵션도 포함되어 있다면 파티션 폴더에서만 파일들이 제거됩니다. 이를 위해서는 S3 버킷에 대한 `s3:DeleteObject` 권한이 있어야 합니다. ALLOWOVERWRITE 옵션과 같이 지정할 수는 없습니다.

<br>

#### PARALLEL

```sql
UNLOAD (
  'SELECT ...'
)
TO 'S3 Path'
IAM_ROLE
PARALLEL [ { ON | TRUE } | { OFF | FALSE } ]
```

UNLOAD 기능은 기본적으로 병렬 처리를 이용해 데이터를 작성하도록 설계되어 있습니다. 그러므로 기본 값은 ON 또는 TRUE인데 이를 OFF 또는 FALSE로 설정하면 데이터 파일에 직렬 방식으로 작성하게 됩니다.

<br>

#### MAXFILESIZE

```sql
UNLOAD (
  'SELECT ...'
)
TO 'S3 Path'
IAM_ROLE
MAXFILESIZE [AS] max-size [ MB | GB ]
```

S3에 생성하는 파일의 최대 크기를 지정할 수 있습니다. 5MB ~ 6.2GB 사이에 지정할 수 있고 소수 값을 지정하면 됩니다. 기본 단위는 MB이고 이 옵션을 지정하지 않을 경우 기본 값은 최대값인 6.2GB 입니다.

<br>

#### ROWGROUPSIZE

```sql
UNLOAD (
  'SELECT ...'
)
TO 'S3 Path'
IAM_ROLE
ROWGROUPSIZE [AS] size [ MB | GB ]
```

행 그룹의 크기를 지정합니다. 기본 값은 32MB이고 32 ~ 128MB의 값을 지정할 수 있습니다. 이 옵션을 사용하려면 스토리지 형식은 Parquet를 사용해야 합니다.

<br>

#### EXTENSION

```sql
UNLOAD (
  'SELECT ...'
)
TO 'S3 Path'
IAM_ROLE
EXTENSION 'extension-name'
```

확장자를 지정하는 옵션입니다. Redshift는 검증을 하지 않기 떄문에 지정된 확장자가 올바른지 직접 확인해야 합니다.