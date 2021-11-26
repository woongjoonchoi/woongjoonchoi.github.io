---
title : "BigQuery Access Control"



excerpt: "GCP Bigquery"

categories:
  - Bigquery
tags:
  - [Google Cloud,GCP,Bigquery ]
# classes : wide
toc: true
toc_sticky: true
---
이 내용은 Courser GCP Professional Data Enigneering 강의및 Google Cloud Blog 및 Google CLoud Docs 기반으로 작성한 글입니다.

## Query and Data in Bigquery

Bigquery는 ***<u>data</u>***를 가지고 ***<u>Query</u>***문을 실행하면 result가 나오는 구조라고 개인적으로 생각합니다.이 글에서는 , Data 와 Query에 대한 ***<u>Access Control</u>***을 어떻게  하는지 알아 보겠습니다.

### Data Access Control

#### Data Organization

![image](https://user-images.githubusercontent.com/50165842/143584736-04953293-4638-4373-98a1-c6c1a92377f7.png)

Bigquery의 Data는 계층형 구조(hierarchy )입니다. Project level 안에는 Dataset Level이고 그다음 level은 Table level입니다.  Bigquery에서 특정 table을 access 할 때는 project.dataset.table 이렇게 access합니다.

```sql
SELECT
  MIN(start_station_name) AS start_station_name,
  MIN(end_station_name) AS end_station_name,
  APPROX_QUANTILES(tripduration, 10)[OFFSET (5)] AS typical_duration,
  COUNT(tripduration) AS num_trips
FROM
  `bigquery-public-data.new_york_citibike.citibike_trips`
WHERE
  start_station_id != end_station_id
GROUP BY
  start_station_id,
  end_station_id
ORDER BY
  num_trips DESC
LIMIT
  10
```

예를들어, 위 쿼리에서는 bigquery-public-data 라는 project의 new_york_citibkie라는 dataset의 citibike_trips라는 table에 access하는query문입니다. 이러한 다수의 project,dataset,table level은 우리가 정보를 논리적인 계층구조로 관리하는데 도움을 줍니다.

사업적 제약사항에 따라 혹은 domain에 따라 분리(seperate) 할 수 있습니다.

#### Cloud IAM

![image](https://user-images.githubusercontent.com/50165842/143585643-dde9487d-3a67-47dc-8722-69ad839ac46f.png)

Bigquery는 ***<u>Cloud IAM</u>***(Identity Access Manager)로 user에게 권한을 부여합니다.

Access Control은 R***<u>esource level(Organization , Project , Dataset , Table, View)</u>***에 각각 access control을 할 수 있습니다. 계층형 구조(hierarchy)이기 때문에 상위 level에 권한을 가지고 있으면 하위 level에 전부 access 할 수 있습니다.

Bigquery의 IAM 으로 Resource에 부여할 수  있는 Role은 3가지 유형이 있습니다.

- Predefined Role
- Basic Role
- Custom Role

Predefined Role은 Google에서 관리해주는 granular access manage입니다. 이는 다양한 common case를 포함하고 있다고 Docs에 나와 있습니다. 여기에는 ,***<u>Admin,user,data editor, data owner</u>*** 등이 포함됩니다.

Basic Role은 ***<u>Owner,Writer , Reader</u>*** 이 3가지 role을 의미합니다.

Custom Role은 user-specified role입니다.

자세한 사항은 아래 reference에 링크를 달아놨습니다.

#### Regional vs Multi-regional

![image](https://user-images.githubusercontent.com/50165842/143586897-1b3e8ea7-066d-42b2-b7fe-9b0cf5a6a7ae.png)

Bigquery의 Datasets는 ***<u>Multi-Regional</u>*** 이 될 수도 있고 , ***<u>regional(단일 region)</u>***이 될 수도 있습니다. 두 개의 차이점은 ***<u>replica</u>***가 multi-region에 있는가 아니면 single region안의 zone안에 있는가 차이입니다. 비용은 multi-regional이 비싼 것으로 알고 있습니다.

Multi-regional의 장점은 특정 region에 있는 user가 다른 region에 있는 dataset에 access 할 때 egress traffic이 발생하지 않는다는 장점이 있다고 개인적으로 생각합니다. 

어떤 시나리오를 예로 들어보겠습니다.

어떤 회사의 본사가 아시아 리전에 있고 , 이 회사는 글로벌 회사입니다. 이 회사의 Data Science 팀은 유럽, 아프리카,아메리카 , 아시아에 흩어져 있습니다. Data Science team은 본사에서 발생하는 traffic data에 access 해야하는데 , 이 data는 본사 아시아 리전 의 bigquery datasets에 저장되어 있습니다. 만약, 이 Bigquery Project가 regional이라면 DS team이 access할 때 마다 , egress traffic이 발생할 것입니다.

#### Security

![image](https://user-images.githubusercontent.com/50165842/143588406-6caf22d7-d9f4-4ddf-bdc3-01a4f53c1f12.png)

Bigquery Storage는 ***<u>Google Managed encryption key</u>***로 암호화 거나 , ***<u>Customer-managed encryption keys</u>***로 암호화합니다. 이 Storage에 Access 할 때는 ***<u>Service accounts 나 User accounts</u>***로 authentication을 한 후 access하게 됩니다.  Access 할 때마다 Bigquery log가 발생합니다. 이 ***<u>Bigquery log</u>*** 들은 immutable하며 ***<u>Stackdriver에 export</u>***가 가능합니다.

#### view access control

![image](https://user-images.githubusercontent.com/50165842/143592390-85855e64-ccb1-4a8b-ad49-efbc5bb027d5.png)

tables에 access 할 수있도록 role을 부여하면, 모든 table의 데이터를 볼 수 있게 됩니다. 하지만, view를 만들어 table 안의 특정 data만을 보도록 access control을 할 수 있습니다. 이 view로 query result가 나오면 영원히 지속되지 않고 ***<u>24시간에 expired</u>*** 됩니다.

view는 standardized view, materialized view 2가지가 존재합니다.

Standardized view는 위의 이유 뿐만 아니라 user에게 query의 복잡한 logic을 기억하지 않고도 result를 얻을 수 있도록 하게 할때 사용합니다.

![image](https://user-images.githubusercontent.com/50165842/143593157-b24892f4-2a0d-468d-b129-2781da402a49.png)

Materialized View를 query result가 지속되어 매번 query결과가 필요할 때 query를 할 필요가 없습니다

자동적으로 refresh되는 특징도 있습니다.



### Query Acess Control

#### Query text Access

#### query history

![image](https://user-images.githubusercontent.com/50165842/143589884-731e3fce-6563-427a-92b2-2a379675556a.png)

Bigquery는 Query history를 통해서 Query문을 확인 할 수 있습니다.

#### saved queries

![image](https://user-images.githubusercontent.com/50165842/143590074-c1f39ff6-7153-429d-8b12-95fe87733338.png)

![image](https://user-images.githubusercontent.com/50165842/143590255-c9f2e8a8-cac6-4ea9-899d-d749b154c96a.png)

Query를 저장하게 되면 saved queries에서 확인 할 수있습니다.

#### share query

![image](https://user-images.githubusercontent.com/50165842/143590511-34b98828-a3a7-4e8d-bbdc-d90a8be19e5b.png)



위 의 두개는 edit 권한을 수정함으로써 팀원들이 edit 할 수있게 할 수 있습니다.

#### Query reuslt Acess

#### temporary table

![image](https://user-images.githubusercontent.com/50165842/143590663-fa75ddaa-fd29-4019-921b-9a75d076b86b.png)

Query를 한번 수행하면 Bigquery는 이를 자동적으로 peramanent table로 저장합니다. 이의 수명은 24시간이며 , 다음 번에 query를 수행할 때 결과를 불러오기만 합니다. 하지만, 이는 오직 query 실행자만이 볼 수 있습니다.

#### permanent table

만약에, Query 결과를 share해야한다면 이를 permanent table로 저장해야합니다.

![image](https://user-images.githubusercontent.com/50165842/143591097-a5fe281b-6157-4180-9441-2fd31fc1dc23.png)

![image](https://user-images.githubusercontent.com/50165842/143591139-21ae1f36-3263-453a-bd45-2393083c5527.png)

Cloud IAM으로 destination table에 대한 access control을 할 수 있습니다. 주의점은 , 이는 table에 저장을 하므로 temporary table과 달리 추가적인 cost가 발생합니다. 

#### storage access

![image](https://user-images.githubusercontent.com/50165842/143591743-44cc6798-20be-45e3-a686-3434401c69fa.png)

Bigquery는 query문으로 cloud storage,External storage, Bigquery storage로부터 query 할 수 있습니다. Cloud Storage같은 몇몇 특정 storage로 부터 query를 할 때는 , data를 loading하지 않고 바로 query할 수 있습니다. 이를 ***<u>federated query</u>***라 합니다. Performance 에서 더 이점을 누리고 싶다면, Data를 Bigquery storage로 직접 loading해서 query하는것이 좋습니다. 추가적으로, Bigquery는 terabytes 나 petabytes 같은 거대한 dataset에서 query를 할 때 효율적으로 작동하도록 설계되었습니다.

### cost of stroage and cost of queries

![image](https://user-images.githubusercontent.com/50165842/143593303-d3a91fa1-0176-441d-b429-45b6a791a837.png)

Access Control을 통해 ***<u>Query cost랑 Storage Cost를 분리</u>***할 수 있습니다.

Bigquery에서 User2가 User1에게 Dataset access role을 주었습니다. User1은 Dataset D에 대해 Query를 하지만 query cost는 User1이 지불합니다.

Bigquery의 Public Dataset은 모든 authenticated 된 user들에게 access 할 수 있고 query 할 수 있도록 합니다. 하지만, query에 대한 비용은 user가 지불합니다, 이 dataset은 Bigquery public project안에 있음에도 말입니다.

### Reference

[Access control with IAM  | BigQuery  | Google Cloud](https://cloud.google.com/bigquery/docs/access-control#role_types)

[Creating a table definition file for an external data source (google.com)](https://cloud.google.com/bigquery/external-table-definition#permanent_versus_temporary_external_tables)

[How to query your data in BigQuery | Google Cloud Blog](https://cloud.google.com/blog/topics/developers-practitioners/bigquery-explained-querying-your-data)

