---
title : "BigQuery Intro"



excerpt: "GCP Bigquery"

categories:
  - Bigquery
tags:
  - [Google Cloud,GCP,Bigquery ]
# classes : wide
toc: true
toc_sticky: true
---
## Bigquery

Google Cloud Platform 에서 제공해주는 ***<u>Data Warehouse</u>*** service중 하나이면서 , Big data analytics tool입니다.  Big data analytics를 위해 쓸모있는 structured data , semi-structured data가 Bigquery로 loading 됩니다.



### query petabyte in seconds

Bigquery는 ***<u>petabyte 단위의 데이터를 초단의 latency</u>***로 query할 수 있습니다.



### Ecosystem

#### ETL and Data processing

다양한 ***<u>Sass(Software as a service)로 부터</u>*** data를 Bigquery로 loading하거나 federated query를 할 수 있습니다. 

> AmazonS3
>
> Youtube
>
> Cloud Storage
>
> on-premise ..etc

#### visualization and analytics tools

단순히 , 플랫폼을 넘어서 ***<u>다른 GCP 서비스 및  coworker</u>***에서 dashboard를 볼 수 있습니다.

> - Google Spread Sheet
> - DataStudio 360
> - G-Suite
> - Datalab
> - co-workes..etc



### serverless and no-ops

Serverless라함은 GCP에서는 ***<u>instance를 작업하는 동안 생성하고 , 작업이 끝나면 자동으로 turn off하는 과정을 user가 할 필요가 없는것</u>*** 이라고 생각합니다.(표현에 틀린 부분이 있으면 지적해주세요.)

그저 ***<u>Query 문을 작성하고 실행</u>***하면 됩니다.

### ad-hoc queries. 

Ad-hoc query는 ***<u>not-predetrmined query</u>***입니다. 실행하기 전까지 query의 결과값이 결정되지 않는 쿼리입니다. 

자세히는 알지 못해서 이 이상의 설명은 어려울 거 같습니다.

### Machine Learning

Bigquery는 Bigquery ML을 제공합니다. ***<u>SQL 쿼리 문법으로 ML model</u>***을 만들 수 있게 해줍니다.