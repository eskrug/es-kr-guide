---
description: >-
  이 문서의 허가되지 않은 무단 복제나 배포 및 출판을 금지합니다. 본 문서의 내용 및 도표 등을 인용하고자 하는 경우 출처를 명시하고
  김종민(kimjmin@gmail.com)에게 사용 내용을 알려주시기 바랍니다.
---

# 8. 집계 \(Aggregations\)

  Elasticsearch 는 검색엔진으로 개발되었지만 지금은 로그분석을 비롯해 다양한 목적의데이터 시스템으로 사용되고 있습니다. Elasticsearch가 이렇게 다양한 용도로 활용이 될 수 있는 이유는 데이터를 단순히 검색만 하는 것이 아니라 여러가지 연산을 할 수 있는 **Aggregation** 기능이 있기 때문입니다. Kibana 에서는 다음과 같이 바 차트, 파이 차트 등으로 데이터를 시각화 할 수 있는데 여기서 사용하는 것이 이 기능입니다.

![Kibana &#xD654;&#xBA74; - &#xCD9C;&#xCC98;: https://www.elastic.co/products/kibana](../.gitbook/assets/08-01.png)

  Aggregation 은 번역하면 "집계" 라는 뜻 이지만 Elasticsearch 의 기능명 이기 때문에 보통 Elastic Stack 관련 세미나 또는 블로그 포스트에서는 원문대로 **aggregation** 혹은 **애그리게이션** 으로 많이 표현합니다. 이 책에서도 **aggregation** 또는 **aggs** 로 표현하도록 하겠습니다.

  Aggregation 의 사용 방법은 다음과 같습니다. **\_search API** 에서 query 문과 같은 수준에 지정자 `aggregations` 또는 `aggs`를 명시하고 그 아래 임의의 aggregation 이름을 입력한 뒤 사용할 aggregation 종류와 옵션들을 명시합니다. 한번의 쿼리로 aggregation 여러 개를 입력할 수도 있습니다. 아래는 aggregation을 입력하는 예제입니다.

{% tabs %}
{% tab title="request" %}
{% code-tabs %}
{% code-tabs-item title="aggregations 입력" %}
```javascript
GET <인덱스명>/_search
{
  "query": {
    … <쿼리 구문> …
  },
  "aggs": {
    "<임의의 aggregation 1>": {
      "<aggregation 종류>": {
        … <aggreagation 구문> …
      }
    },
    "<임의의 aggregation 2>": {
      "<aggregation 종류>": {
        … <aggreagation 구문> …
      }
    }
  }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}
{% endtab %}

{% tab title="response" %}
{% code-tabs %}
{% code-tabs-item title="aggregations 입력 결과" %}
```javascript
{
  "hits": {
… 쿼리 결과 (hit 된 도큐먼트 내용) …
  },
  "aggregations": {
    "<임의의 aggregation 1>": {
… aggregation 결과 …
    },
    "<임의의 aggregation 2>": {
… aggregation 결과 …
    }
  }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}
{% endtab %}
{% endtabs %}

  Aggregation 에는 크게 **Metrics** 그리고 **Bucket** 두 종류가 있습니다. Aggregations 구문이나 옵션에 metrics 이거나 bucket 이라고 따로 명시를 하지는 않습니다. Aggregation 종류들 중 숫자 또는 날짜 필드의 값을 가지고 계산을 하는 aggregation 들을 metrics aggregation 이라고 분류하고, 범위나 keyword 값 등을 가지고 도큐먼트들을 그룹화 하는 aggregation 들을 bucket aggregation 이라고 분류 합니다.

  다음에 주로 사용되는 **Metrics** 와 **Bucket** Aggregations 들을 설명하기 위해 아래의 데이터를 먼저 **my\_stations** 인덱스에 입력하도록 하겠습니다. 처음 한 줄을 카피 해서 필드명은 그대로 두고 필드값만 수정하면 약간 더 수월하게 입력할 수 있습니다.

{% code-tabs %}
{% code-tabs-item title="테스트를 위한 my\_stations 인덱스에 데이터 입력" %}
```javascript
PUT my_stations/_bulk
{"index": {"_id": "1"}}
{"date": "2019-06-01", "line": "1호선", "station": "종각", "passangers": 2314}
{"index": {"_id": "2"}}
{"date": "2019-06-01", "line": "2호선", "station": "강남", "passangers": 5412}
{"index": {"_id": "3"}}
{"date": "2019-07-10", "line": "2호선", "station": "강남", "passangers": 6221}
{"index": {"_id": "4"}}
{"date": "2019-07-15", "line": "2호선", "station": "강남", "passangers": 6478}
{"index": {"_id": "5"}}
{"date": "2019-08-07", "line": "2호선", "station": "강남", "passangers": 5821}
{"index": {"_id": "6"}}
{"date": "2019-08-18", "line": "2호선", "station": "강남", "passangers": 5724}
{"index": {"_id": "7"}}
{"date": "2019-09-02", "line": "2호선", "station": "신촌", "passangers": 3912}
{"index": {"_id": "8"}}
{"date": "2019-09-11", "line": "3호선", "station": "양재", "passangers": 4121}
{"index": {"_id": "9"}}
{"date": "2019-09-20", "line": "3호선", "station": "홍제", "passangers": 1021}
{"index": {"_id": "10"}}
{"date": "2019-10-01", "line": "3호선", "station": "불광", "passangers": 971}

```
{% endcode-tabs-item %}
{% endcode-tabs %}

