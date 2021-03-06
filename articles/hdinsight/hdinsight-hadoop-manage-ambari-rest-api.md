---
title: Ambari REST API를 사용하여 Hadoop 모니터링 및 관리 - Azure HDInsight
description: Ambari를 사용하여 Azure HDInsight에서 Hadoop 클러스터를 모니터링하고 관리하는 방법에 대해 알아봅니다. 이 문서에서는 HDInsight 클러스터에 포함된 Ambari REST API를 사용하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: 317d12f6d5dee92d998266d4e9b6d52e6ef9c7a5
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381392"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-rest-api"></a>Apache Ambari REST API를 사용하여 HDInsight 클러스터 관리

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari REST API를 사용하여 Azure HDInsight에서 Apache Hadoop 클러스터를 관리하고 모니터링하는 방법에 대해 알아봅니다.

## <a name="what-is-apache-ambari"></a>아파치 암바리란?

[아파치 암바리는](https://ambari.apache.org) [REST API를](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)통해 사용이 간편한 웹 UI를 제공하여 하둡 클러스터의 관리 및 모니터링을 간소화합니다.  Ambari는 Linux 기반 HDInsight 클러스터를 기본으로 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

* HDInsight의 하두프 클러스터. [리눅스에서 HDInsight로 시작하기를](hadoop/apache-hadoop-linux-tutorial-get-started.md)참조하십시오.

* 윈도우 에 우분투에 강타 10.  이 문서의 예제에서는 Windows 10의 Bash 셸을 사용합니다. [Windows 10을 위한 Linux용 Windows 하위 시스템 설치 가이드](https://docs.microsoft.com/windows/wsl/install-win10)에서 설치 단계를 참조하세요.  다른 [Unix 셸](https://www.gnu.org/software/bash/)도 작동합니다.  몇 가지 약간의 수정이 있는 예제는 Windows 명령 프롬프트에서 작동할 수 있습니다.  또는 Windows PowerShell을 사용할 수 있습니다.

* 간단한 jq 명령줄 JSON 프로세서.  을 [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/)참조하십시오.

* Windows PowerShell.  또는 [Bash](https://www.gnu.org/software/bash/).

## <a name="base-uniform-resource-identifier-for-ambari-rest-api"></a>Ambari 나머지 API에 대 한 기본 균일 한 리소스 식별자

 HDInsight의 Ambari REST API의 기본 균일 리소스 식별자(URI)는 클러스터 이름이 있는 `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME` `CLUSTERNAME` 위치입니다.  URI의 클러스터 이름은 **대/소문자를 구분합니다.**  URI(URI)의`CLUSTERNAME.azurehdinsight.net`정규화된 도메인 이름(FQDN) 부분의 클러스터 이름은 대/소문자를 구분하지 않지만 URI의 다른 발생은 대/소문자를 구분합니다.

## <a name="authentication"></a>인증

HTTPS를 요구하는 HDInsight에서 Ambari로 연결 클러스터 만들기 중 입력한 관리자 계정 이름(기본값은 **admin**) 및 암호를 사용합니다.

엔터프라이즈 보안 패키지 클러스터의 `admin`경우 에서와 같은 `username@domain.onmicrosoft.com`정규화된 사용자 이름을 사용합니다.

## <a name="examples"></a>예

### <a name="setup-preserve-credentials"></a>설정(자격 증명 보존)

자격 증명을 보존하여 각 예제에 대해 자격 증명을 다시 입력하지 않도록 합니다.  클러스터 이름은 별도의 단계로 유지됩니다.

**A. 배쉬**  
실제 암호로 대체하여 `PASSWORD` 아래 스크립트를 편집합니다.  그런 다음 명령을 입력합니다.

```bash
export password='PASSWORD'
```  

**B. 파워쉘**  

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>올바르게 대/소문자 클러스터 이름 식별

클러스터 이름의 실제 대/소문자는 예상과 다를 수 있습니다.  여기에 있는 단계는 실제 대/소문자와 함께 다음 모든 이후 예제에 대 한 변수에 저장 됩니다.

클러스터 이름으로 바꾸려면 `CLUSTERNAME` 아래 스크립트를 편집합니다. 그런 다음 명령을 입력합니다. (FQDN의 클러스터 이름은 대/소문자를 구분하지 않습니다.)

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

### <a name="parsing-json-data"></a>JSON 데이터 구문 분석

다음 예제에서는 [jq](https://stedolan.github.io/jq/) 또는 [ConvertFrom-Json을](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/convertfrom-json) 사용하여 JSON 응답 문서를 `health_report` 구문 분석하고 결과의 정보만 표시합니다.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" \
| jq '.Clusters.health_report'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```

### <a name="get-the-fqdn-of-cluster-nodes"></a>클러스터 노드의 FQDN 가져오기

클러스터 노드의 정규화된 도메인 이름(FQDN)을 알아야 할 수 있습니다. 다음 예제를 사용하여 클러스터의 다양한 노드에 대한 FQDN을 쉽게 검색할 수 있습니다.

**모든 노드**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq -r '.items[].Hosts.host_name'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```

**헤드 노드**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" \
| jq -r '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**작업자 노드**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" \
| jq -r '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Zookeeper 노드**

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
| jq -r ".host_components[].HostRoles.host_name"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

### <a name="get-the-internal-ip-address-of-cluster-nodes"></a>클러스터 노드의 내부 IP 주소 가져오기

이 섹션의 예제에서 반환되는 IP 주소는 인터넷을 통해 직접 액세스할 수 없습니다. HDInsight 클러스터가 포함된 Azure 가상 네트워크 내에서만 액세스할 수 있습니다.

HDInsight 및 가상 네트워크 작업에 대한 자세한 내용은 [HDInsight용 가상 네트워크 계획을](hdinsight-plan-virtual-network-deployment.md)참조하십시오.

IP 주소를 찾으려면 클러스터 노드의 내부 FQDN(정규화된 도메인 이름)을 알아야 합니다. FQDN을 알고 있으므로 호스트의 IP 주소를 얻을 수 있습니다. 다음 예제는 모든 호스트 노드의 FQDN에 대한 Ambari를 먼저 쿼리합니다. 그런 다음 각 호스트의 IP 주소에 대해 Ambari를 쿼리합니다.

```bash
for HOSTNAME in $(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```  

```powershell
$uri = "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" 
$resp = Invoke-WebRequest -Uri $uri -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
foreach($item in $respObj.items) {
    $hostName = [string]$item.Hosts.host_name
    $hostInfoResp = Invoke-WebRequest -Uri "$uri/$hostName" `
        -Credential $creds -UseBasicParsing
    $hostInfoObj = ConvertFrom-Json $hostInfoResp
    $hostIp = $hostInfoObj.Hosts.ip
    "$hostName <--> $hostIp"
}
```

### <a name="get-the-default-storage"></a>기본 스토리지 가져오기

HDInsight 클러스터는 Azure 저장소 계정 또는 데이터 레이크 저장소를 기본 저장소로 사용해야 합니다. 클러스터를 만든 후 Ambari를 사용하여 이 정보를 검색할 수 있습니다. 예를 들어 HDInsight 외부 컨테이너에 데이터를 읽고 쓰려는 경우가 여기에 해당합니다.

다음 예제에서는 클러스터에서 기본 스토리지 구성을 검색합니다.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq -r '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]  
> 이러한 예제는 서버(`service_config_version=1`)에 적용된 첫 번째 구성을 반환하며 이 정보를 포함합니다. 클러스터를 만든 후에 수정된 값을 검색하는 경우 구성 버전을 나열하고 최신 버전을 검색해야 합니다.

반환 값은 다음 예제 중 하나와 유사합니다.

* `wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net` - 이 값은 클러스터에서 기본 스토리지에 Azure Storage 계정을 사용하고 있음을 나타냅니다. `ACCOUNTNAME` 값은 스토리지 계정의 이름입니다. `CONTAINER` 부분은 스토리지 계정에서 blob 컨테이너의 이름입니다. 이 컨테이너는 클러스터에 대한 HDFS 호환 스토리지의 루트입니다.

* `abfs://CONTAINER@ACCOUNTNAME.dfs.core.windows.net` - 이 값은 클러스터가 기본 스토리지에 Azure Data Lake Storage Gen2를 사용하고 있음을 나타냅니다. `ACCOUNTNAME` 및 `CONTAINER` 값은 앞에서 언급한 Azure Storage에서 동일한 의미를 갖습니다.

* `adl://home` - 이 값은 클러스터가 기본 스토리지에 Azure Data Lake Storage Gen1을 사용하고 있음을 나타냅니다.

    Data Lake Storage 계정 이름을 찾으려면 다음 예제를 사용합니다.

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq -r '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    반환 값은 `ACCOUNTNAME.azuredatalakestore.net`와 비슷합니다. 여기서 `ACCOUNTNAME`은 Data Lake Storage 계정의 이름입니다.

    Data Lake Storage 내에서 클러스터에 대한 스토리지를 포함하는 디렉터리를 찾으려면 다음 예제를 사용합니다.

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq -r '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```  

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    반환 값은 `/clusters/CLUSTERNAME/`과 비슷합니다. 이 값은 Data Lake Storage 계정 내의 경로입니다. 이 경로는 클러스터에 대한 HDFS 호환 파일 시스템의 루트입니다.  

> [!NOTE]  
> [Azure PowerShell에서](/powershell/azure/overview) 제공하는 [Get-AzHDInsight클러스터](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) cmdlet도 클러스터에 대한 저장소 정보를 반환합니다.

### <a name="get-all-configurations"></a>모든 구성 받기

클러스터에 사용할 수 있는 구성을 가져옵니다.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_configs"
```

```powershell
$respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
    -Credential $creds -UseBasicParsing
$respObj.Content
```

이 예제는 설치된 구성 요소에 대한 현재 구성을 포함하는 JSON 문서를 반환합니다. *태그* 값을 참조하십시오. 다음 예제는 Spark 클러스터 형식에서 반환된 데이터에서 발췌한 것입니다.

```json
"jupyter-site" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-client-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
```

### <a name="get-configuration-for-specific-component"></a>특정 구성 요소에 대한 구성 받기

관심 있는 구성 요소에 대한 구성을 가져옵니다. 다음 예제에서는 이전 요청에서 반환된 태그 값으로 `INITIAL`을 바꿉니다.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
    -Credential $creds -UseBasicParsing
$resp.Content
```

이 예제는 `livy2-conf` 구성 요소에 대한 현재 구성을 포함하는 JSON 문서를 반환합니다.

### <a name="update-configuration"></a>구성 업데이트

1. `newconfig.json`를 만듭니다.  
   수정한 다음 아래 명령을 입력합니다.

   * 새 `livy2-conf` 구성 요소로 바꿉습니다.
   * 모든 `INITIAL` 구성 `tag` 받기에서 검색된 실제 [값으로 바꿉꿉입니다.](#get-all-configurations)

     **A. 배쉬**

     ```bash
     curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" \
     | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     **B. 파워쉘**  
     PowerShell 스크립트는 [jq를](https://stedolan.github.io/jq/)사용합니다.  `C:\HD\jq\jq-win64` [jq의](https://stedolan.github.io/jq/)실제 경로 및 버전을 반영하기 위해 아래편집 .

     ```powershell
     $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
     $now = Get-Date
     $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
     $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
       -Credential $creds -UseBasicParsing
     $resp.Content | C:\HD\jq\jq-win64 --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     Jq는 HDInsight에서 검색한 데이터를 새 구성 템플릿으로 반환하는 데 사용됩니다. 특히 이러한 예제에서는 다음과 같은 작업을 수행합니다.

   * 문자열 "version" 및 날짜를 포함하는 고유 값을 만듭니다. 이 값은 `newtag`에 저장됩니다.

   * 새 구성에 대한 루트 문서를 만듭니다.

   * `.items[]` 배열의 내용을 가져와서 **desired_config** 요소에 추가합니다.

   * `href`, `version`, and `Config` 요소는 새 구성을 제출하는 데 필요하지 않으므로 삭제합니다.

   * 값이 `version#################`인 `tag` 요소를 추가합니다. 숫자 부분은 현재 날짜를 기반으로 합니다. 각 구성에 고유한 태그가 있어야 합니다.

     마지막으로 데이터가 `newconfig.json` 문서에 저장됩니다. 문서 구조는 다음 예제와 유사하게 표시되어야 합니다.

     ```json
     {
       "Clusters": {
         "desired_config": {
           "tag": "version1552064778014",
           "type": "livy2-conf",
           "properties": {
             "livy.environment": "production",
             "livy.impersonation.enabled": "true",
             "livy.repl.enableHiveContext": "true",
             "livy.server.csrf_protection.enabled": "true",
               ....
           },
         },
       }
     }
     ```

2. `newconfig.json`을 편집합니다.  
   `newconfig.json` 문서를 열고 `properties` 개체의 값을 수정/추가합니다. 다음 예제는 `"livy.server.csrf_protection.enabled"` 값을 `"true"`에서 `"false"`로 변경합니다.

        "livy.server.csrf_protection.enabled": "false",

    수정작업이 완료되면 파일을 저장합니다.

3. 을 `newconfig.json`제출합니다.  
   다음 명령을 사용하여 업데이트된 구성을 Ambari에 제출합니다.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" -X PUT -d @newconfig.json "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName"
    ```

    ```powershell
    $newConfig = Get-Content .\newconfig.json
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body $newConfig
    $resp.Content
    ```  

    이러한 명령은 new 구성으로 클러스터에 **newconfig.json** 파일의 내용을 제출합니다. 이 요청은 JSON 문서를 반환합니다. 이 문서의 **versionTag** 요소는 제출한 버전과 일치해야 하며, **configs** 개체에는 요청한 구성 변경 내용이 포함됩니다.

### <a name="restart-a-service-component"></a>서비스 구성 요소 다시 시작

이 시점에서 Ambari 웹 UI는 새 구성을 적용하기 전에 Spark 서비스를 다시 시작해야 한다는 것을 나타냅니다. 다음 단계를 사용하여 서비스를 다시 시작합니다.

1. Spark2 서비스에 대한 유지 관리 모드를 활성화하려면 다음을 사용합니다.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}'
    ```

2. 유지 보수 모드 확인  

    이러한 명령은 서버에 JSON 문서를 보내 유지 관리 모드를 켭니다. 이제 다음 요청을 사용하여 서비스가 유지 관리 모드인지 확인할 수 있습니다.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" \
    | jq .ServiceInfo.maintenance_state
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.ServiceInfo.maintenance_state
    ```

    반환 값은 `ON`입니다.

3. 다음으로 다음을 사용하여 Spark2 서비스를 끕니다.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
    $resp.Content
    ```

    응답은 다음 예제와 유사합니다.

    ```json
    {
        "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
        "Requests" : {
            "id" : 29,
            "status" : "Accepted"
        }
    }
    ```

    > [!IMPORTANT]  
    > 이 URI에서 반환된 `href` 값은 클러스터 노드의 내부 IP 주소를 사용합니다. 클러스터 외부에서 사용하려면 `10.0.0.18:8080` 부분을 클러스터의 FQDN으로 바꿉습니다.  

4. 요청을 확인합니다.  
    이전 단계에서 `29` `id` 반환된 실제 값으로 대체하여 아래 명령을 편집합니다.  다음 명령은 요청의 상태를 검색합니다.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" \
    | jq .Requests.request_status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.Requests.request_status
    ```

    응답 `COMPLETED`는 요청이 완료되었음을 나타냅니다.

5. 이전 요청이 완료되면 다음을 사용하여 Spark2 서비스를 시작합니다.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    $resp.Content
    ```

    이제 서비스는 새 구성을 사용합니다.

6. 마지막으로, 다음을 사용하여 유지 관리 모드를 해제합니다.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}'
    ```

## <a name="next-steps"></a>다음 단계

REST API의 모든 참조 문서를 보려면 [Apache Ambari API 참조 V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)을 참조하세요.  또한, [아파치 암바리 보기에 대 한 사용자 권한 부여](./hdinsight-authorize-users-to-ambari.md)
