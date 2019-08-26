---
description: >-
  이 문서의 허가되지 않은 무단 복제나 배포 및 출판을 금지합니다. 본 문서의 내용 및 도표 등을 인용하고자 하는 경우 출처를 명시하고
  김종민(kimjmin@gmail.com)에게 사용 내용을 알려주시기 바랍니다.
---

# 2.3 elasticsearch 환경 설정

  Elasticsearch 는 각 노드들 별로 실행될 설정들을 적용함으로써 [노드들의 역할을 나누](2.3.3-master-data-ingest-ml.md)거나 클러스터의 속성을 결정하게 됩니다. Elasticsearch 의 실행 환경을 설정하는 방법은 크게 2가지가 있습니다. 홈 디렉토리의 config 경로 아래 있는 파일들을 변경하거나 시작 명령으로 설정하는 방법입니다. config 경로 아래의 파일들에서는 다음과 같은 설정들이 가능합니다.

* [jvm.options](2.3.1-jvm.options.md)
* [elasticsearch.yml](2.3.2-elasticsearch.yml.md)

  그 외에도 Elasticsearch 를 처음 실행할 때 [-E 커맨드](2.3.4.md) 라인 명령을 통해서도 가능합니다.

