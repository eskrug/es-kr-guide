---
description: >-
  이 문서의 허가되지 않은 무단 복제나 배포 및 출판을 금지합니다. 본 문서의 내용 및 도표 등을 인용하고자 하는 경우 출처를 명시하고
  김종민(kimjmin@gmail.com)에게 사용 내용을 알려주시기 바랍니다.
---

# 7.2 매핑 - Mappings

### 동적\(Dynamic\) 매핑

  Elasticsearch 를 활용하면서 가장 손이 많이 가는 작업이 매핑 설정입니다. Elasticsearch 는 동적 매핑을 지원하기 때문에 미리 정의하지 않아도 인덱스에 도큐먼트를 새로 추가하면 자동으로 매핑이 생성됩니다. 인덱스가 없는 상태에서 다음의 도큐먼트를 **books** 인덱스에 입력 해 보겠습니다.

{% code title="books 인덱스가 없는 상태에서 도큐먼트 입력" %}
```javascript
PUT books/_doc/1
{
  "title": "Romeo and Juliet",
  "author": "William Shakespeare",
  "category": "Tragedies",
  "publish_date": "1562-12-01T00:00:00",
  "pages": 125
}
```
{% endcode %}

  books 인덱스의 매핑을 확인 해 보면 각 필드의 매핑이 자동으로 생성된 것을 확인할 수 있습니다.

{% tabs %}
{% tab title="request" %}
{% code title="books 인덱스의 매핑 확인" %}
```javascript
GET books/_mapping
```
{% endcode %}
{% endtab %}

{% tab title="response" %}
{% code title="books 인덱스의 매핑 확인 결과" %}
```javascript
{
  "books" : {
    "mappings" : {
      "properties" : {
        "author" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "category" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "pages" : {
          "type" : "long"
        },
        "publish_date" : {
          "type" : "date"
        },
        "title" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        }
      }
    }
  }
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

  인덱스의 매핑에서 필드들은 **mappings** 아래 **properties** 항목의 아래에 지정됩니다. 위 예제에서 보면 데이터 형식에 맞게 **title**, **author**, **category** 필드들은 **text**와 **keyword**타입으로, **pages** 필드는 **long** 타입으로, **publish\_date** 필드는 **date** 타입으로 자동 지정 된 것을 확인할 수 있습니다.

  Elasticsearch 의 매핑이 동적으로 생성 될 때는 필드의 값을 보고 타입을 예상하는데, 항상 그 필드가 포함될 수 있는 가장 넓은 범위 형태의 데이터 타입을 선택합니다. pages 필드의 값은 125로 값이 작지만 자연수를 저장하는 데이터 타입 중 가장 큰 **long** 으로 지정이 됩니다. publish\_date 필드는 값이 **"1562-12-01T00:00:00"** 로 JSON 도큐먼트에서 사용하는 [ISO8601 표준 날짜 형식](https://www.iso.org/iso-8601-date-and-time-format.html)의 데이터를 준수하였기 때문에 **date** 타입으로 인식이 되었습니다. 하지만 날짜가 **"1 Dec 1562 00:00:00"** 같이 다른 포맷으로 입력이 되면 보통은 **text** 타입으로 인식이 됩니다.

### 매핑 정의

  데이터가 입력되어 자동으로 매핑이 생성되기 전에 미리 먼저 인덱스의 매핑을 정의 해 놓으면 정의 해 놓은 매핑에 맞추어 데이터가 입력됩니다. 매핑은 다음과 같이 선언합니다.

{% code title="인덱스의 매핑 정의" %}
```javascript
PUT <인덱스명>
{
  "mappings": {
    "properties": {
      "<필드명>":{
        "type": "<필드 타입>"
        … <필드 설정>
      }
      …
    }
  }
}
```
{% endcode %}

  이미 만들어진 매핑에 필드를 추가하는것은 가능합니다. 하지만 이미 만들어진 필드를 삭제하거나 필드의 타입 및 설정값을 변경하는 것은 불가능합니다. 필드의 변경이 필요한 경우 인덱스를 새로 정의하고 기존 인덱스의 값을 새 인덱스에 모두 재색인 해야 합니다. 이미 생성된 인덱스에 새로운 필드를 추가 할 때는 다음과 같이 합니다.

{% code title="기존 매핑에 필드 추가" %}
```javascript
PUT <인덱스명>/_mapping
{
  "properties": {
    "<추가할 필드명>": { 
      "type": "<필드 타입>"
      … <필드 설정>
    }
  }
}
```
{% endcode %}

{% hint style="danger" %}
이 때 추가할 필드명이 기존 필드와 중복되는 이름이면 오류가 발생합니다.
{% endhint %}

  필드 추가는 최상위 필드와 object 타입의 내부 필드, 그리고 다중 필드\(multi-field\) 역시 추가가 가능합니다. 

  인덱스에 데이터가 입력될 때 기존 매핑에 정의되지 않은 필드가 도큐먼트에 있으면 필드가 자동으로 추가됩니다. **books** 인덱스에서 **page** 필드는 **byte**, **title** 필드는 **text**, **category** 필드는 **keyword** 로 선언하고 위의 첫 예제와 동일한 도큐먼트를 입력한 뒤 필드 내용을 확인해 보겠습니다.

{% code title="books 인덱스 매핑에 category, pages, title 필드 정의" %}
```javascript
PUT books
{
  "mappings": {
    "properties": {
      "category": {
        "type": "keyword"
      },
      "pages": {
        "type": "byte"
      },
      "title": {
        "type": "text"
      }
    }
  }
}
```
{% endcode %}

{% code title="books 인덱스에 도큐먼트 입력" %}
```javascript
PUT books/_doc/1
{
  "title": "Romeo and Juliet",
  "author": "William Shakespeare",
  "category": "Tragedies",
  "publish_date": "1562-12-01T00:00:00",
  "pages": 125
}
```
{% endcode %}

{% tabs %}
{% tab title="request" %}
{% code title="books 인덱스의 매핑 확인" %}
```javascript
GET books/_mapping
```
{% endcode %}
{% endtab %}

{% tab title="response" %}
{% code title="books 인덱스의 매핑 확인 결과" %}
```javascript
{
  "books" : {
    "mappings" : {
      "properties" : {
        "author" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "category" : {
          "type" : "keyword"
        },
        "pages" : {
          "type" : "byte"
        },
        "publish_date" : {
          "type" : "date"
        },
        "title" : {
          "type" : "text"
        }
      }
    }
  }
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

  도큐먼트 입력 후 미리 정의 해 둔 **title**, **pages**, **category** 필드들은 선언된 타입 대로 유지가 되었고, **publish\_date**, **author** 필드는 디폴트 형식대로 정의되어 추가 된 것을 확인할 수 있습니다.

이제 Elasticsearch 필드에 설정 가능한 타입들을 살펴보겠습니다. 일반적으로 자바 언어 레벨에서 지원하는 **기본 타입**들과 Elasticsearch 또는 루씬 레벨에서 추상화된 **확장 타입**들이 있습니다.

