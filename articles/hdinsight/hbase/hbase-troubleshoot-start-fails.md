---
title: 아파치 HBase 마스터 는 Azure HDInsight에서 시작 하지 못합니다.
description: 아파치 HBase 마스터 (HMaster) Azure HDInsight에서 시작 하는 데 실패
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: 290b541d9b5e86616373d2e426241fca07e780ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887209"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>아파치 HBase 마스터 (HMaster) Azure HDInsight에서 시작 하는 데 실패

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="scenario-atomic-renaming-failure"></a>시나리오: 원자성 이름 바꾸기 실패

### <a name="issue"></a>문제

시작 프로세스 중에 식별된 예기치 않은 파일입니다.

### <a name="cause"></a>원인

시작 프로세스 중에 HMaster는 데이터를 처음부터(.tmp) 폴더에서 데이터 폴더로 이동하는 등 많은 초기화 단계를 수행합니다. 또한 HMaster는 미리 쓰기 로그(WAL) 폴더를 확인하여 응답하지 않는 지역 서버가 있는지 확인합니다.

HMaster는 WAL 폴더에서 기본 목록 명령을 수행합니다. 언제든지 이러한 폴더에 예기치 않은 파일이 있을 경우 예외가 발생하고 시작되지 않습니다.

### <a name="resolution"></a>해결 방법

호출 스택을 확인하고 문제의 원인이 될 수 있는 폴더를 확인합니다(예: WAL 폴더 또는 .tmp 폴더일 수 있음). 그런 다음 클라우드 탐색기 또는 HDFS 명령을 사용하여 문제가 있는 파일을 찾습니다. 일반적으로 이 파일은 파일입니다. `*-renamePending.json` 이 `*-renamePending.json` 파일은 WASB 드라이버에서 원자성 이름 바꾸기 작업을 구현하는 데 사용되는 저널 파일입니다. 이 구현의 버그로 인해 이러한 파일은 프로세스 충돌 후 남아 있을 수 있습니다. 클라우드 탐색기에서 또는 HDFS 명령을 사용하여 이 파일을 강제 삭제합니다.

경우에 따라 이 위치에 같은 `$$$.$$$` 이름의 임시 파일이 있을 수도 있습니다. 이 파일을 보기 위해 HDFS `ls` 명령을 사용해야 합니다. 클라우드 탐색기에서는 이 파일을 볼 수 없습니다. 이 파일을 삭제하려면 HDFS 명령 `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`을 사용합니다.

이러한 명령을 실행한 후 HMaster는 즉시 시작됩니다.

---

## <a name="scenario-no-server-address-listed"></a>시나리오: 나열된 서버 주소가 없습니다.

### <a name="issue"></a>문제

테이블이 온라인 상태가 아님을 `hbase: meta` 나타내는 메시지가 표시될 수 있습니다. 실행 `hbck` 하면 `hbase: meta table replicaId 0 is not found on any region.` HMaster 로그에서 메시지가 `No server address listed in hbase: meta for region hbase: backup <region name>`표시 될 수 있습니다.  

### <a name="cause"></a>원인

HMaster는 HBase를 다시 시작한 후 초기화할 수 없습니다.

### <a name="resolution"></a>해결 방법

1. HBase 셸에서 다음 명령을 입력합니다(해당되는 경우 실제 값 변경).

    ```hbase
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>'
    ```

1. 항목을 `hbase: namespace` 삭제합니다. 이 항목은 `hbase: namespace` 테이블을 검색할 때 보고되는 것과 동일한 오류일 수 있습니다.

1. Ambari UI에서 활성 HMaster를 다시 시작하여 HBase를 실행 상태로 만듭니다.

1. HBase 셸에서 모든 오프라인 테이블을 가져오려면 다음 명령을 실행합니다.

    ```hbase
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>시나리오: java.io.IOException: 시간 정

### <a name="issue"></a>문제

HMaster는 다음과 유사한 치명적인 `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned`예외를 사용하여 시간 이동합니다.

### <a name="cause"></a>원인

HMaster 서비스를 다시 시작할 때 플러시되지 않은 많은 테이블 및 영역이 있는 경우 이 문제가 발생할 수 있습니다. 시간 시간은 HMaster의 알려진 결함입니다. 일반 클러스터 시작 작업이 오래 걸릴 수 있습니다. 네임스페이스 테이블이 아직 할당되지 않은 경우 HMaster가 종료됩니다. 시간이 오래 걸리는 시작 작업은 플러시되지 않은 많은 양의 데이터가 존재하고 5분의 시간 초과가 충분하지 않은 곳에서 발생합니다.

### <a name="resolution"></a>해결 방법

1. 아파치 암바리 UI에서 **HBase** > **구성으로 이동합니다.** 사용자 지정 `hbase-site.xml` 파일에서 다음 설정을 추가합니다.

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. 필요한 서비스(HMaster 및 가능한 경우 다른 HBase 서비스)를 다시 시작합니다.

---

## <a name="scenario-frequent-region-server-restarts"></a>시나리오: 빈번한 리전 서버 다시 시작

### <a name="issue"></a>문제

노드는 주기적으로 재부팅됩니다. 지역 서버 로그에서 다음과 유사한 항목이 표시될 수 있습니다.

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>원인

긴 `regionserver` JVM GC 일시 중지. 일시 중지로 `regionserver` 인해 응답이 없고 zk 세션 시간 정 40대 내에 HMaster에 하트 비트를 보낼 수 없습니다. HMaster는 `regionserver` 죽었다고 믿고 중단하고 다시 `regionserver` 시작할 것입니다.

### <a name="resolution"></a>해결 방법

설정뿐만 `hbase-site` `zookeeper.session.timeout` 아니라 사육사 `zoo.cfg` 설정을 `maxSessionTimeout` 변경해야합니다, 사육사 세션 시간 시간을 변경합니다.

1. 액세스 Ambari UI, **HBase -> 구성 -> 설정으로**이동, 시간 시간 단축 섹션에서, 사육사 세션 시간 단축의 값을 변경합니다.

1. 액세스 Ambari UI, **사육사로 이동 -> 구성 -> 사용자 정의,** `zoo.cfg`추가 / 다음 설정을 변경합니다. 값이 HBase와 `zookeeper.session.timeout`동일한지 확인합니다.

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. 필수 서비스를 다시 시작합니다.

---

## <a name="scenario-log-splitting-failure"></a>시나리오: 로그 분할 실패

### <a name="issue"></a>문제

HMasters는 HBase 클러스터에 나타나지 못했습니다.

### <a name="cause"></a>원인

보조 저장소 계정에 대한 HDFS 및 HBase 설정이 잘못 구성되었습니다.

### <a name="resolution"></a>해결 방법

hbase.rootdir 를 wasb://@.blob.core.windows.net/hbase 설정합니다: Ambari에서 서비스를 다시 시작합니다.

---

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 올바른 리소스( 답변, 지원 및 전문가)에 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 Azure [지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토합니다. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.
