---
title: Azure Load Balancer 배포 모드 구성
titleSuffix: Azure Load Balancer
description: 이 문서에서는 원본 IP 선호도를 지원하기 위해 Azure Load Balancer에 대한 배포 모드 구성을 시작하십시오.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: 5c50186692438be5d0922cd329c28e665310e5c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023534"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Azure Load Balancer의 배포 모드 구성

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="hash-based-distribution-mode"></a>해시 기반 배포 모드

Azure Load Balancer의 기본 배포 모드는 5 튜플 해시입니다. 

튜플은 다음으로 구성됩니다.
* **소스 IP**
* **원본 포트**
* **대상 IP**
* **대상 포트**
* **프로토콜 유형**

해시는 사용 가능한 서버에 트래픽을 매핑하는 데 사용됩니다. 알고리즘은 전송 세션 내에서만 끈적거림을 제공합니다. 동일한 세션에 있는 패킷은 로드 균형 끝점 뒤에 있는 동일한 데이터 센터 IP로 전달됩니다. 클라이언트가 동일한 원본 IP에서 새 세션을 시작하면 원본 포트가 변경되고 트래픽이 다른 데이터 센터 끝점으로 이동하게 됩니다.

![5튜플 해시 기반 배포 모드](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>소스 IP 선호도 모드

로드 밸랜더는 소스 IP 선호도 분포 모드를 사용하여 구성할 수도 있습니다. 이 배포 모드는 세션 선호도 또는 클라이언트 IP 선호도라고도 합니다. 이 모드는 투플(소스 IP 및 대상 IP) 또는 쓰리 튜플(소스 IP, 대상 IP 및 프로토콜 유형) 해시를 사용하여 사용 가능한 서버에 트래픽을 매핑합니다. 원본 IP 선호도를 사용하면 동일한 클라이언트 컴퓨터에서 시작된 연결이 동일한 데이터 센터 끝점으로 이동합니다.

다음 그림은 투튜드 구성을 보여 줍니다. 투플이 로드 밸런서를 통해 가상 머신 1(VM1)으로 어떻게 실행되는지 확인합니다. 그런 다음 VM1이 VM2와 VM3에 의해 백업됩니다.

![투플 세션 선호도 분포 모드](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

원본 IP 선호도 모드는 Azure Load Balancer와 RD 게이트웨이(원격 데스크톱 게이트웨이)간의 비호환성을 해결합니다. 이 모드를 사용하여 단일 클라우드 서비스에서 RD 게이트웨이 팜을 빌드할 수 있습니다.

또 다른 사용 사례 시나리오는 미디어 업로드입니다. 데이터 업로드는 UDP를 통해 이루어 지지만 제어 평면은 TCP를 통해 이루어집니다.

* 클라이언트는 로드 균형 공용 주소로 TCP 세션을 시작하고 특정 DIP로 이동합니다. 채널은 활성 상태로 유지되어 연결 상태를 모니터링합니다.
* 동일한 클라이언트 컴퓨터의 새 UDP 세션이 로드 균형 이블런드 공용 끝점으로 시작됩니다. 연결은 이전 TCP 연결과 동일한 DIP 엔드포인트로 지정됩니다. TCP를 통해 제어 채널을 유지하면서 미디어 업로드를 높은 처리량으로 실행할 수 있습니다.

> [!NOTE]
> 가상 컴퓨터를 추가 또는 제거하여 부하 분산 집합이 변경되면 클라이언트 요청 배포가 다시 계산됩니다. 기존 클라이언트의 새 연결이 동일한 서버로 수행된다고 확신할 수 없습니다. 또한 원본 IP 선호도 배포 모드를 사용하는 경우 트래픽이 불규칙하게 배포될 수 있습니다. 프록시 뒤에서 실행되는 클라이언트는 하나의 고유한 클라이언트 애플리케이션으로 보일 수 있습니다.

## <a name="configure-source-ip-affinity-settings"></a>원본 IP 선호도 설정 구성

### <a name="azure-portal"></a>Azure portal

포털에서 부하 분산 규칙을 수정하여 배포 모드의 구성을 변경할 수 있습니다.

1. Azure 포털에 로그인하고 리소스 그룹을 클릭하여 변경하려는 로드 밸러서가 포함된 리소스 **그룹을**찾습니다.
2. 로드 밸런서 개요 화면에서 **설정**에서 **로드 밸런싱 규칙을** 클릭합니다.
3. 로드 균형 규칙 화면에서 배포 모드를 변경하려는 부하 분산 규칙을 클릭합니다.
4. 규칙에 따라 **세션 지속성** 드롭다운 상자를 변경하여 배포 모드가 변경됩니다.  다음 옵션을 사용할 수 있습니다.
    
    * **없음(해시 기반)** - 동일한 클라이언트의 연속된 요청이 모든 가상 컴퓨터에서 처리될 수 있음을 지정합니다.
    * **클라이언트 IP(소스 IP 선호도 2-튜플)** - 동일한 클라이언트 IP 주소의 연속적인 요청이 동일한 가상 시스템에 의해 처리되도록 지정합니다.
    * **클라이언트 IP 및 프로토콜(소스 IP 선호도 3-튜플)** - 동일한 클라이언트 IP 주소및 프로토콜 조합의 연속적인 요청이 동일한 가상 머신에서 처리되도록 지정합니다.

5. 배포 모드를 선택한 다음 **저장을**클릭합니다.

### <a name="azure-powershell"></a>Azure PowerShell

리소스 관리자와 함께 배포된 가상 시스템의 경우 PowerShell을 사용하여 기존 부하 분산 규칙에서 로드 밸런서 배포 설정을 변경합니다. 다음 명령은 배포 모드를 업데이트합니다. 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLb -ResourceGroupName MyLbRg
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

클래식 가상 머신의 경우 Azure PowerShell을 사용하여 배포 설정을 변경합니다. Azure 엔드포인트를 가상 컴퓨터에 추가하고 부하 분산 장치 배포 모드를 구성합니다.

```azurepowershell-interactive
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

필요한 부하 분산 `LoadBalancerDistribution` 양에 대한 요소 값을 설정합니다. 투플(소스 IP 및 대상 IP) 부하 분산에 대한 소스 IP를 지정합니다. 3튜플(소스 IP, 대상 IP 및 프로토콜 유형) 부하 분산에 대한 sourceIPProtocol을 지정합니다. 5튜플 하중 분산의 기본 동작에 대해 없음을 지정합니다.

다음 설정을 사용하여 엔드포인트 부하 분산 장치 배포 모드 구성을 검색합니다.

    PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

    VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
    LBSetName : MyLoadBalancedSet
    LocalPort : 80
    Name : HTTP
    Port : 80
    Protocol : tcp
    Vip : 65.52.xxx.xxx
    ProbePath :
    ProbePort : 80
    ProbeProtocol : tcp
    ProbeIntervalInSeconds : 15
    ProbeTimeoutInSeconds : 31
    EnableDirectServerReturn : False
    Acl : {}
    InternalLoadBalancerName :
    IdleTimeoutInMinutes : 15
    LoadBalancerDistribution : sourceIP

`LoadBalancerDistribution` 요소가 없는 경우 Azure Load Balancer는 기본 5튜플 알고리즘을 사용합니다.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>부하 분산 엔드포인트 집합에 대한 배포 모드 구성

부하 분산 엔드포인트 집합에 엔드포인트가 포함되어 있으면 부하 분산 엔드포인트 집합에 대해 배포 모드를 구성해야 합니다.

```azurepowershell-interactive
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>Cloud Services 엔드포인트에 대한 배포 모드 구성

Azure SDK for .NET 2.5를 사용하여 클라우드 서비스를 업데이트합니다. .csdef 파일에서 Cloud Services용 엔드포인트 설정을 지정합니다. Cloud Services 배포의 부하 분산 장치 배포 모드를 업데이트하려면 배포를 업그레이드해야 합니다.

아래에 .csdef에서 엔드포인트 설정을 변경하는 예제가 나와 있습니다.

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
    </Endpoints>
</WorkerRole>
<NetworkConfiguration>
    <VirtualNetworkSite name="VNet"/>
    <AddressAssignments>
<InstanceAddress roleName="VMRolePersisted">
    <PublicIPs>
    <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
    </PublicIPs>
</InstanceAddress>
    </AddressAssignments>
</NetworkConfiguration>
```

## <a name="api-example"></a>API 예제

다음 예에서는 배포에 지정된 부하 분산 집합에 대한 부하 분산 장치 배포 모드를 다시 구성하는 방법을 보여줍니다. 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>배포된 부하 분산 집합에 대한 배포 모드 변경

Azure 클래식 배포 모델을 사용하여 기존 배포 구성을 변경하십시오. `x-ms-version` 헤더를 추가하고 값을 버전 2014-09-01 이상으로 설정합니다.

#### <a name="request"></a>요청

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
    Content-Type: application/xml

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName>
        <LocalPort> local-port-number </LocalPort>
        <Port> external-port-number </Port>
        <LoadBalancerProbe>
          <Port> port-assigned-to-probe </Port>
          <Protocol> probe-protocol </Protocol>
          <IntervalInSeconds> interval-of-probe </IntervalInSeconds>
          <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds>
        </LoadBalancerProbe>
        <Protocol> endpoint-protocol </Protocol>
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn>
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes>
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution>
      </InputEndpoint>
    </LoadBalancedEndpointList>

앞에서 설명한 것처럼 `LoadBalancerDistribution` 요소를 투투 선호도에 대한 sourceIP, 3튜플 선호도에 대한 sourceIPProtocol, 또는 선호도 없음(5튜플 선호도)에 대해 없음으로 설정합니다.

#### <a name="response"></a>응답

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>다음 단계

* [Azure Internal Load Balancer 개요](load-balancer-internal-overview.md)
* [인터넷 연결 부하 분산 장치 구성 시작](quickstart-create-standard-load-balancer-powershell.md)
* [부하 분산 장치에 대한 유휴 TCP 시간 제한 설정 구성](load-balancer-tcp-idle-timeout.md)
