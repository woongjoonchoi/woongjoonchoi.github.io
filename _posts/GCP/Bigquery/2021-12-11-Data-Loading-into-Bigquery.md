---
title : "Data Loading into Bigquery"



excerpt: "GCP Bigquery"

categories:
  - Bigquery
tags:
  - [Google Cloud,GCP,Bigquery ]
# classes : wide
toc: true
toc_sticky: true
---
이 내용은 Course [Preparing for Google Cloud Certification: Cloud Data Engineer 특화 과정](https://www.coursera.org/specializations/gcp-data-engineering) 강의 Part2의 Data Warehouse를 듣고 제 표현대로 강의 내용을 정리한 것입니다. Closed Book으로 적고 부족한 내용은 다시 검토하면서 채워넣습니다.

## Data Loading

Bigquery에 Query를 요청하려면 Data에 access 할 수 있어야 합니다. 여러 방법이 있는데 , 그 중 Bigquery에 Data를 Loading을 하는방법이 있는데 이에 대해 알아보도록 하겠습니다. 

## EL ,ELT , ETL

![image](https://user-images.githubusercontent.com/50165842/145668839-a3d8344f-6835-4ae6-8b7f-17d2edbdbced.png)

Data Loading 방법은 3가지가 있습니다. ***<u>EL, ELT, ETL</u>***이 있습니다. EL은 별도의 transformation 없이 Loading 하는 것입니다. Transformation을 거치지 않는다는 것은 ***<u>이미 데이터가 쓸모있는 형태</u>***라는 것입니다. ELT, ETL은 transformation이 필요할 경우 사용하는 방법입니다. Transformation이 을 Loading을 하고 하는가, Loading 하기 전에 하는 가의 차이입니다. Bigquery 의 경우 Read에 특화된 Data Warehouse입니다. 즉, UPDATE에 특화되지 않았다고 할 수 있습니다. 현재는 어떤지 모르지만, 제가 아는 한, Bigquery는 table마다 update를 1500번 이상 허용하지 않습니다. *<u>Loading 하기 전에 Transformation이 필요한 거 같으면 ETL을 사용하면 됩니다</u>*. 

## EL in Bigquery

![image](https://user-images.githubusercontent.com/50165842/145669017-055de43f-204b-4969-a4a5-f8dcd25e294a.png)

Bigquery에서의 EL은 앞에서 말했듯이 data가 Transformation이 필요하지 않고 usable하면 그냥 loading하면 됩니다. 





### supported format

![image](https://user-images.githubusercontent.com/50165842/145669064-c14bc602-606d-481b-996a-418bce1f5bf9.png)

지원되는 format은 CSV, JSON, PARQUET, etc 등등 다양한 format을 지원합니다. ***<u>CSV, JSON은 autodetect schema option</u>***으로 자동으로 schema를 정해줍니다. 하지만, ***<u>manually verification을 할 것을 권장</u>***한다고 합니다. ***<u>AVR,ORC, PARQUET은 self-describing format입니다.  auto-detection을 사용할 필요가 없습니다. inline-schema를 제공해줄 필요가 없습니다</u>***. Bigquery가 schema를 결정할 수 있습니다. 이 외에도, ***<u>User가 manually 하게 schema를 define 할 수 있습니다. 하지만, 이는 권장되는 방법은 아닙니다</u>***. 

Bigquery는 gzip format을 지원합니다. 하지만, Bigquery의 성능은 ***<u>uncompressed file을 loading 할 때 performance가 좋습니다.</u>*** 마찬가지로, Cloud Storage도 파일의 크기가 작은 것들을 여러 개 upload하는것보다 큰 파일을 여러 개 upload하는 것이 더 좋은 performance를 보여줍니다. time-sensitive 한 scenario와 bandwidth 제약과 시간 제약이 있고 uncompressed file을 loading 하는 scenario 둘 중에서 선택을 해야하는 경우가 있습니다. ***<u>빠른 loading test를 통해서 어느 대안이 잘 작동하는지 확인해야 합니다</u>***.

### DTS(data transfer system)

![image](https://user-images.githubusercontent.com/50165842/145670070-629fca04-5d9e-4632-aa5a-15a22f703793.png)

Big query는 ***<u>Data transfer system</u>***을 지원합니다. 다양한 format의 파일을 load 하는 것은 위에서 확인했습니다. Big query는 ***<u>API</u>***를 통해서 Data를 Loading 할 수 있습니다. ***<u>code를 실행할 수 있는 어디에서든지</u>*** Bigquery로  data를 loading 할 수 있습니다. 예를 들어, GCP에는 ***<u>compute engine, container on kubernetess engine, app engine, cloud function</u>***이 있습니다. 이 service들의 API를 활용해서 loading 할 수 있습니다. 하지만, 이러면 여러분이 ***<u>custom data preprocess function을 만들어야 합니다</u>***. 따라서, 실제적으로는, Bigquery는 GCP의 ***<u>dataproc, datflow</u>*** 에서 API를 사용해서 data를 loading 하게 됩니다. DataProc, Dataflow 는 data를 processing 해주는 service입니다.  제가 아는바에 따르면  , OpenSource인 Hadoop, Spark가 Dataproc에 대응되고 , Apache Airflow가 Dataflow 에 대응됩니다. DataProc, Dataflow 는 process function을 제공함으로 저희가 process function을 다시 만들지 않아도 됩니다. (설명이 부정확 할 수 있습니다. 부정확한 부분이 있으면 피드백 부탁드립니다) ***<u>다른 Saas(Software as a service)를 통해  loading을 할 경우 pre-built 된 transformation function이 loading 하기 전에 transformation을 수행합니다</u>***. (ETL이라 생각이 들었는데, 강의에서는 EL 카테고리로 분류합니다.)

#### From Cloud Storage

![image](https://user-images.githubusercontent.com/50165842/145670137-56392e2a-880a-495f-b83b-9f89a96eeedc.png)

***<u>다른 on-premise로부터 Cloud Stroage로 upload를 합니다 .그런 다음에 , Cloud Storage부터 Bigquery로 loading 할 수 있습니다.</u>*** 

```sql
gsutil -m cp *.csv gs:://mybucket 
bq load ...
```



![image](https://user-images.githubusercontent.com/50165842/145670226-69d071ad-af5e-453a-83d1-f7068ec0e1df.png)

Bigquery로 loading 하는 과정은 ***<u>scheduled query로 자동화</u>*** 할 수 있습니다. query는 standard SQL로 작성이 되어야 합니다. 이 SQL은 DDL(data definition langue) 와 DML(data manipulation language)을 포함합니다.  Query string과 destination table은 parameterized 되어 매일매일 정기적으로 수행됩니다



![image](https://user-images.githubusercontent.com/50165842/145670572-7fae669e-2b1a-46a6-ae90-aea5ab58e11d.png)



. B***<u>igquery는 7일 동안 query의 history를 cache에 보관합니다</u>***. 따라서, ***<u>특정 시간대의 data의 snapshot을 query 할 수 있습니다</u>***. 즉, 쉽게 이전의 data로 revert 할 수 있습니다. Data는 Dataset 안에 같은 id를 가진 table이 없을 때만 recover 될 수 있습니다.  Streaming Pipeline의 경우, empty table을 만들 수 있기 때문에, recover를 할 수 없습니다. 





#### From Saas

![image](https://user-images.githubusercontent.com/50165842/145670641-61d4ca37-daf5-450c-b2f9-85ec0c5c5909.png)

DTS는 정기적으로 transforamtion, scheduling , 을 제공합니다.

### Backfill in DTS



예를 들어 , 은행원이 Data warehouse에 접수기록을 보내는 시나리오를 가정해보겠습니다. 은행에서는 Data를 주기적으로 전송을 하는데, 은행원이 그 시간까지 기록을 못 보내는 경우가 발생할 수 있습니다. 이러면 , Data에 기록이 전부 오지 않았음을 detect 할 수 있어야 합니다. , 이러한 missing data를 fill in 해달라고 request를 하는 것을 Data backfills라고 합니다.

### DTS in Bigquery 

![image](https://user-images.githubusercontent.com/50165842/145670681-cced6f0f-a4d0-4bef-9031-cc0f3f29f7a7.png)

***<u>DTS는 managed service이기 때문에 overhead maintaining, secure 등등을 신경 쓸 필요가 없습니다</u>***. 예전의 Data Transfer service에는 많은 코드가 필요햇지만, Bigquery에서의 DTS는 그렇지 않습니다. ***<u>DTS의 핵심은 automatic, scheduled transfer가 가능</u>***하다는 것입니다. 이를 이용해서, 다른 on-premise로부터 data를 transfer 할 수 있습니다.



### How DTS work?

![image](https://user-images.githubusercontent.com/50165842/145670962-0a10ba8d-6f39-4587-92bc-acaa33f2b577.png)



Bigquery는 100개가 넘는 다양한 Saas Applications로 부터 Data Transfer를 수행할 수 있습니다.

![image](https://user-images.githubusercontent.com/50165842/145671008-70f14c70-6fb6-4846-84bb-7ce1b19a512d.png)

Cloud Storage를 사용하지 않아도 되고 Saas로 부터 DTS를 이용해서 transfer를 수행하면 됩니다. 

## ELT in Bigquery



![image](https://user-images.githubusercontent.com/50165842/145671067-d1496979-58c2-4fca-a8ed-60a729322f30.png)

이번에는 Bigquery에서의 ELT를 알아보도록 하겠습니다.

### Loading data and Transforming in Bigquery

![image](https://user-images.githubusercontent.com/50165842/145671078-335bb8e5-1ea9-4c91-86d1-ff06cdb2b6ee.png)

EL 챕터에서 봤던  사진에서 Transformation 과정이 추가되었습니다 . 다른 on-premise 나 other cloud services에서 Cloud storage로 loading하는 것은 변함이 없습니다. 

![image](https://user-images.githubusercontent.com/50165842/145671173-3cf84e68-0115-450f-a6e6-5b38758927d9.png)

DML으로 data를 transformation 할 수 있습니다. 하지만, 위에서도 말했듯이 DML은 table당 1500의 daily quota가 있습니다 .



![image](https://user-images.githubusercontent.com/50165842/145671167-7fb961c0-5a90-45ed-86c0-56b8562a5301.png)

 

Bigquery는 또한 CREATE 나 REPLACE TABLE로 새로운 table을 만들어서 transformation을 수행할 수 있습니다. 

![image](https://user-images.githubusercontent.com/50165842/145671232-87c9a380-d07e-48bf-9cba-043096b181a7.png)

Bigquery는 fedearated query가 가능하지만 Bigquery로 load해서 query를 하는 것이 performance 측면에서 더 좋습니다. 하지만, 추가적인 storage cost가 발생한다는 단점이 있습니다.

GCP의 다른 Cloud service로 부터도 loading 할 수 있습니다. 하지만, export는 오직 cloud storage만 가능합니다. (GCP PDE시험기준으로는 그렇다고 하는데 지금은 잘 모르겠습니다.)





### Custom Transformation

![image](https://user-images.githubusercontent.com/50165842/145671303-51f49872-c2ac-4a16-933f-7fd8e6ec9329.png)

Transformation이 Bigquery에서 제공해주는것 이상의 Transformation이 필요하면 어떻게 해야 할까요? 저희가 Transformation function을 작성하면 됩니다 . Bigquery는 UDF(user defined function) 기능을 제공해줍니다. Bigquery는 standard sql을 빠르게 수행하도록 optimization 하기 때문에 standard sql로 작성하길 권장합니다. Sql 이외에는 Javascript만이  가능합니다.(저는 javascript를 잘 모르기에 pass하도록 하겠습니다. ) 

## ETL in Bigquery

강의에서 관련 예제를 따로 다루지는 않았습니다. 제 개인적인 생각으로는 ETL에서 T(Transformation) 의 과정을 제외하면 EL이랑 비슷하다고 생각을 합니다.
