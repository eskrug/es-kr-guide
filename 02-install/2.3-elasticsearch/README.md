---
description: >-
  이 문서의 허가되지 않은 무단 복제나 배포 및 출판을 금지합니다. 본 문서의 내용 및 도표 등을 인용하고자 하는 경우 출처를 명시하고
  김종민(kimjmin@gmail.com)에게 사용 내용을 알려주시기 바랍니다.
---

# 2.3 elasticsearch 환경 설정

  Elasticsearch 의 실행 환경을 설정하는 방법은 크게 2가지가 있습니다. 홈 디렉토리의 config 경로 아래 있는 파일들을 변경하거나 시작 명령으로 설정하는 방법입니다. config 경로 아래의 파일들에서는 다음과 같은 설정들이 가능합니다.



### 

### 노드 역할 지 : master / data / ingest / ml

  Elasticsearch의 노드는 수행하는 다양한 역할들이 있는데, 각자의 노드들이 서로 다른 역할들을 수행하도록 클러스터를 구성할 수 있습니다. 아래 설정들의 모든 디폴트 값은 **true** 이며 기본적으로 노드는 명시된 모든 역할들을 수행합니다. 특정 값 들을 false 로 설정 함으로서 노드의 역할들을 구분지어 클러스터를 구성할 수 있습니다.

#### node.master: true

  마스터 후보\(master eligible\) 노드 여부를 설정합니다. false인 경우 이 노드는 마스터 노드로 선출이 불가능합니다. 모든 클러스터는 1개의 마스터 노드가 존재하며 마스터 노드가 다운되거나 끊어진 경우 남은 마스터 후보 노드들 중에서 새로운 마스터 노드가 선출되게 됩니다.

#### node.data: true

   노드가 데이터를 저장하도록 합니다. false인 경우 이 노드는 데이터를 저장하지 않습니다.

#### node.ingest: true

  데이터 색인시 전처리 작업인 ingest pipleline 작업의 수행을 할 수 있는지 여부를 지정합니다. false인 경우 이 노드에서는 ingest pipeline 작업의 실행이 불가능합니다.

#### node.ml: true

  이 노드가 머신러닝 작업 수행을 할 수 있는지 여부를 지정합니다. false 인 경우 이 노드애서는 머신러닝 작업이 수행되지 않습니다.

  예를 들어 클러스터에서 어떤 노드를 데이터는 저장하거나 색인하지 않고 오직 클러스터 상태를 관리하는 마스터 노드의 역할만 수행하도록 설정하려면 아래와 같이 설정합니다.

{% code-tabs %}
{% code-tabs-item title="config/elasticsearch.yml - 전용 마스터 노드 설정" %}
```yaml
node.master: true
node.data: false
node.ingest: false
node.ml: false
```
{% endcode-tabs-item %}
{% endcode-tabs %}

  이런 방법으로 클러스터 안의 노드들을 마스터 전용 노드, 데이터 전용 노드 등으로 분리하여 유연한 구성을 할 수 있습니다.

{% hint style="info" %}
앞의 모든 설정을 **false** 로 하게 되면 해당 노드는 데이터를 저장하거나 색인하지 않고 클러스터 상태를 업데이트도 하지 않으며 오직 클라이언트와 통신만 하는 역할로 사용이 가능합니다. 이런 노드를 코디네이트 온리 노드 \(coordinate only node\) 라고 부릅니다.
{% endhint %}

### 커맨드 라인 설

  elasticsearch.yml 파일에 설정하는 것 외에도 Elasticsearch 실행 시 커맨드 명령에 `-E <옵션>=<값>` 을 이용해서 환경 설정이 가능합니다. 예를 들어 클러스터명은 **my-cluster** 노드명은 **node-1**로 노드를 실행하기 위해서는 다음과 같이 실행합니.

{% code-tabs %}
{% code-tabs-item title="클러스터명: my-cluster / 노드명:  node-1 로 노드 실행" %}
```bash
$ bin/elasticsearch -E cluster.name=my-cluster -E node.name="node-1"
[2019-08-26T14:23:51,399][INFO ][o.e.e.NodeEnvironment    ] [node-1] using [1] data paths, mounts [[/ (/dev/disk1s1)]], net usable_space [88.9gb], net total_space [465.6gb], types [apfs]
[2019-08-26T14:23:51,401][INFO ][o.e.e.NodeEnvironment    ] [node-1] heap size [989.8mb], compressed ordinary object pointers [true]
[2019-08-26T14:23:51,455][INFO ][o.e.n.Node               ] [node-1] node name [node-1], node ID [RDBLYDInSxmMV1PEVit_pQ], cluster name [my-cluster]
[2019-08-26T14:23:51,455][INFO ][o.e.n.Node               ] [node-1] version[7.3.0], pid[50389], build[default/tar/de777fa/2019-07-24T18:30:11.767338Z], OS[Mac OS X/10.14.6/x86_64], JVM[Oracle Corporation/Java HotSpot(TM) 64-Bit Server VM/1.8.0_151/25.151-b12]
[2019-08-26T14:23:51,456][INFO ][o.e.n.Node               ] [node-1] JVM home [/Library/Java/JavaVirtualMachines/jdk1.8.0_151.jdk/Contents/Home/jre]
[2019-08-26T14:23:51,456][INFO ][o.e.n.Node               ] [node-1] JVM arguments [-Xms1g, -Xmx1g, -XX:+UseConcMarkSweepGC, -XX:CMSInitiatingOccupancyFraction=75, -XX:+UseCMSInitiatingOccupancyOnly, -Des.networkaddress.cache.ttl=60, -Des.networkaddress.cache.negative.ttl=10, -XX:+AlwaysPreTouch, -Xss1m, -Djava.awt.headless=true, -Dfile.encoding=UTF-8, -Djna.nosys=true, -XX:-OmitStackTraceInFastThrow, -Dio.netty.noUnsafe=true, -Dio.netty.noKeySetOptimization=true, -Dio.netty.recycler.maxCapacityPerThread=0, -Dlog4j.shutdownHookEnabled=false, -Dlog4j2.disable.jmx=true, -Djava.io.tmpdir=/var/folders/0d/m7m670h13pz3lvr9xjz07zk80000gn/T/elasticsearch-5549928559955731670, -XX:+HeapDumpOnOutOfMemoryError, -XX:HeapDumpPath=data, -XX:ErrorFile=logs/hs_err_pid%p.log, -XX:+PrintGCDetails, -XX:+PrintGCDateStamps, -XX:+PrintTenuringDistribution, -XX:+PrintGCApplicationStoppedTime, -Xloggc:logs/gc.log, -XX:+UseGCLogFileRotation, -XX:NumberOfGCLogFiles=32, -XX:GCLogFileSize=64m, -Dio.netty.allocator.type=unpooled, -XX:MaxDirectMemorySize=536870912, -Des.path.home=/Users/kimjmin/elastic/getStart/elasticsearch-7.3.0, -Des.path.conf=/Users/kimjmin/elastic/getStart/elasticsearch-7.3.0/config, -Des.distribution.flavor=default, -Des.distribution.type=tar, -Des.bundled_jdk=true]
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% hint style="warning" %}
환경 설정이 elasticsearch.yml 과 커맨드 명령 -E 에 모두 있는 경우에는 -E 커맨드 명령에서 한 설정이 더 우선해서 적용이 됩니다.
{% endhint %}

  지금까지 기본적인 설치 방법과 설정 정보들을 살펴보았습니다. 지금까지 다룬 설정들 외에도 추가적으로 다양한 설정들이 있습니다. 설치와 설정에 대한 더 많은 내용들은 아키텍쳐 구성과 예제 부분에서 더 자세히 다루도록 하겠습니다.

