---
title: 위협 보호 및 Azure Security Center
description: 이 항목에서는 Azure Security Center의 위협 보호 기능으로 보호되는 리소스에 대해 설명합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: fdf22e4d981549b876a14aed2b0a1d7e0c76e40e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263457"
---
# <a name="threat-protection-in-azure-security-center"></a>위협 보호 및 Azure Security Center

보안 센터에서 사용자 환경의 모든 영역에서 위협을 감지하면 경고를 생성합니다. 이러한 경고는 영향을 받는 리소스의 세부 정보, 제안된 수정 단계 및 경우에 따라 응답으로 논리 앱을 트리거하는 옵션을 설명합니다.

Azure 보안 센터의 위협 보호는 사용자 환경에 대한 포괄적인 방어를 제공합니다.

* **Azure 계산 리소스에 대한 위협 보호**: Windows 컴퓨터, Linux 컴퓨터, Azure 앱 서비스 및 Azure 컨테이너

* **Azure 데이터 리소스에 대한 위협 보호**: SQL 데이터베이스 및 SQL 데이터 웨어하우스, Azure 저장소 및 Azure Cosmos DB

* **Azure 서비스 계층에 대한 위협 보호**: Azure 네트워크 계층, Azure 관리 계층(Azure 리소스 관리자) (미리 보기) 및 Azure 키 자격 증명 모음(미리 보기)

보안 센터에서 경고를 생성하든 다른 보안 제품에서 보안 센터에서 수신하든 내보낼 수 있습니다. Azure Sentinel(또는 타사 SIEM) 또는 기타 외부 도구로 경고를 내보내려면 [SIEM으로 경고 내보내기의](continuous-export.md)지침을 따르십시오. 




## <a name="threat-protection-for-windows-machines"></a>Windows 컴퓨터에 대한 위협 보호<a name="windows-machines"></a>

Azure 보안 센터는 Azure 서비스와 통합하여 Windows 기반 컴퓨터를 모니터링하고 보호합니다. 보안 센터는 이러한 모든 서비스의 경고 및 수정 제안을 사용하기 쉬운 형식으로 제공합니다.

* **Microsoft Defender ATP** <a name="windows-atp"></a> - 보안 센터는 Microsoft Defender 고급 위협 보호(ATP)와 통합하여 클라우드 워크로드 보호 플랫폼을 확장합니다. 또한 포괄적인 엔드포인트 감지 및 응답(EDR) 기능을 제공합니다.

    > [!IMPORTANT]
    > Microsoft Defender ATP 센서는 보안 센터를 사용하는 Windows 서버에서 자동으로 활성화됩니다.

    Microsoft Defender ATP가 위협을 감지하면 경고를 트리거합니다. 경고는 보안 센터 대시보드에 표시됩니다. 대시보드에서 Microsoft Defender ATP 콘솔로 피벗하고 자세한 조사를 수행하여 공격 범위를 파악할 수 있습니다. Microsoft Defender ATP에 대한 자세한 내용은 [Microsoft Defender ATP 서비스의 온보드 서버를](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)참조하십시오.

* **크래시 덤프 분석** <a name="windows-dump"></a> - 소프트웨어가 충돌하면 크래시 덤프가 충돌 시 메모리의 일부를 캡처합니다.

    맬웨어로 인해 충돌이 발생했거나 맬웨어가 포함되었을 수 있습니다. 보안 제품에 의해 감지되지 않도록 다양한 형태의 맬웨어는 파일 없는 공격을 사용하여 디스크에 쓰거나 디스크에 기록된 소프트웨어 구성 요소를 암호화하지 않습니다. 이러한 유형의 공격은 기존 디스크 기반 접근 방식을 사용하여 감지하기가 어렵습니다.

    그러나 메모리 분석을 사용하면 이러한 종류의 공격을 감지할 수 있습니다. 보안 센터는 크래시 덤프의 메모리를 분석하여 공격이 사용하는 기술을 검색할 수 있습니다. 예를 들어, 공격은 소프트웨어의 취약점을 악용하고, 기밀 데이터에 액세스하고, 손상된 컴퓨터 내에서 은밀하게 유지하려고 시도할 수 있습니다. 보안 센터는 호스트에 미치는 성능에 미치는 영향을 최소화하면서 이 작업을 수행합니다.

    크래시 덤프 분석 경고에 대한 자세한 내용은 [경고 참조 테이블을](alerts-reference.md#alerts-windows)참조하십시오.

* **파일없는 공격 탐지** <a name="windows-fileless"></a> - 엔드포인트를 대상으로 하는 파일 없는 공격이 일반적입니다. 검색을 방지하기 위해 파일없는 공격은 악성 페이로드를 메모리에 주입합니다. 공격자 페이로드는 손상된 프로세스의 메모리 내에서 유지되며 광범위한 악의적인 작업을 수행합니다.

    파일 없는 공격 탐지를 통해 자동화된 메모리 포렌식 기술은 파일 없는 공격 도구 키트, 기술 및 동작을 식별합니다. 이 솔루션은 런타임시 컴퓨터를 주기적으로 검사하고 보안에 중요한 프로세스의 메모리에서 직접 통찰력을 추출합니다.

    악용, 코드 주입 및 악의적인 페이로드 실행의 증거를 찾습니다. Fileless 공격 탐지는 경고 심사, 상관 관계 및 다운스트림 응답 시간을 가속화하기 위해 자세한 보안 경고를 생성합니다. 이 접근 방식은 이벤트 기반 EDR 솔루션을 보완하여 더 큰 탐지 범위를 제공합니다.

    파일 없는 공격 탐지 경고에 대한 자세한 내용은 [경고 참조 테이블을](alerts-reference.md#alerts-windows)참조하십시오.

> [!TIP]
> [Azure 보안 센터 플레이북: 보안 경고를](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)다운로드하여 Windows 경고를 시뮬레이션할 수 있습니다.






## <a name="threat-protection-for-linux-machines"></a>Linux 시스템에 대한 위협 보호<a name="linux-machines"></a>

보안 센터는 가장 일반적인 Linux 감사 프레임워크 중 하나인 **감사를**사용하여 Linux 시스템에서 감사 레코드를 수집합니다. 메인 라인 커널에 살고 있습니다. 

* **Linux 감사 경고 및 로그 분석 에이전트 통합** <a name="linux-auditd"></a> - 감사된 시스템은 시스템 호출을 모니터링하는 커널 수준 하위 시스템으로 구성됩니다. 지정된 규칙 집합으로 필터링하고 소켓에 메시지를 씁니다. 보안 센터는 Log Analytics 에이전트 내에서 감사된 패키지의 기능을 통합합니다. 이 통합을 사용하면 전제 조건 없이 지원되는 모든 Linux 배포판에서 감사된 이벤트를 수집할 수 있습니다.

    감사된 레코드는 Linux 에이전트용 로그 분석 에이전트를 사용하여 이벤트로 수집, 보강 및 집계됩니다. 보안 센터는 지속적으로 클라우드 및 온-프레미스 리눅스 컴퓨터에서 악의적인 동작을 감지 하는 리눅스 신호를 사용 하 여 새로운 분석을 추가 합니다. Windows 기능과 마찬가지로 이러한 분석은 의심스러운 프로세스, 의심스러운 로그인 시도, 커널 모듈 로드 및 기타 활동에 걸쳐 있습니다. 이러한 활동은 컴퓨터가 공격을 받고 있거나 위반되었음을 나타낼 수 있습니다.  

    Linux 경고 목록은 [경고 참조 테이블을](alerts-reference.md#alerts-linux)참조하십시오.

> [!TIP]
> [Azure 보안 센터 플레이북: Linux 검색을](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef)다운로드하여 Linux 경고를 시뮬레이션할 수 있습니다.





## <a name="threat-protection-for-azure-app-service"></a>Azure 앱 서비스에 대한 위협 보호<a name="app-services"></a>

> [!NOTE]
> 이 서비스는 현재 Azure 정부 및 주권 클라우드 지역에서 사용할 수 없습니다.

보안 센터는 클라우드의 규모를 사용하여 앱 서비스를 통해 실행되는 응용 프로그램을 대상으로 하는 공격을 식별합니다. 공격자는 웹 응용 프로그램을 조사하여 약점을 찾아 악용합니다. 특정 환경으로 라우팅되기 전에 Azure에서 실행 중인 응용 프로그램에 대한 요청은 검사 및 기록되는 여러 게이트웨이를 거닐어 줍니다. 그런 다음 이 데이터를 사용하여 악용 및 공격자를 식별하고 나중에 사용할 새로운 패턴을 학습합니다.

보안 센터는 Azure가 클라우드 공급자로 가지고 있는 가시성을 사용하여 앱 서비스 내부 로그를 분석하여 여러 대상에 대한 공격 방법론을 식별합니다. 예를 들어, 방법론에는 광범위한 검색 및 분산 공격이 포함됩니다. 이러한 유형의 공격은 일반적으로 IP의 작은 하위 집합에서 발생하며 여러 호스트에서 유사한 끝점으로 크롤링하는 패턴을 보여 옵니다. 공격은 취약한 페이지 또는 플러그인을 검색하고 있으며 단일 호스트의 관점에서 식별할 수 없습니다.

Windows 기반 앱 서비스 계획을 실행하는 경우 보안 센터에서기본 샌드박스 및 VM에 액세스할 수도 있습니다. 위에서 언급한 로그 데이터와 함께 인프라는 야생에서 순환하는 새로운 공격부터 고객 컴퓨터의 손상에 이르기까지 스토리를 전달할 수 있습니다. 따라서 웹 앱이 악용된 후 보안 센터가 배포되더라도 지속적인 공격을 감지할 수 있습니다.

Azure 앱 서비스 경고 목록은 [경고 참조 테이블을](alerts-reference.md#alerts-azureappserv)참조하십시오.

앱 서비스 계획에 대한 자세한 내용은 [앱 서비스 계획을](https://azure.microsoft.com/pricing/details/app-service/plans/)참조하십시오.





## <a name="threat-protection-for-azure-containers"></a>Azure 컨테이너에 대한 위협 보호<a name="azure-containers"></a>

> [!NOTE]
> 이 서비스는 현재 Azure 정부 및 주권 클라우드 지역에서 사용할 수 없습니다.

보안 센터는 컨테이너화된 환경에 대한 실시간 위협 보호를 제공하고 의심스러운 활동에 대한 경고를 생성합니다. 이 정보를 사용하여 보안 문제를 신속하게 수정하고 컨테이너의 보안을 강화할 수 있습니다.

보안 센터는 다양한 수준에서 위협 보호를 제공합니다. 

* **호스트 수준** - 보안 센터의 에이전트(표준 계층에서 사용 가능, 자세한 내용은 [가격 책정](security-center-pricing.md) 참조)가 Linux에서 의심스러운 활동을 모니터링합니다. 에이전트는 노드에서 발생하는 의심스러운 활동 또는 노드에서 실행 중인 컨테이너에 대한 경고를 트리거합니다. 이러한 활동의 예로는 웹 셸 검색 및 알려진 의심스러운 IP 주소와의 연결이 있습니다.

    컨테이너화된 환경의 보안에 대한 심층적인 통찰력을 위해 에이전트는 컨테이너별 분석을 모니터링합니다. 권한 있는 컨테이너 만들기, API 서버에 대한 의심스러운 액세스 및 Docker 컨테이너 내에서 실행되는 SSH(보안 셸) 서버와 같은 이벤트에 대한 경고를 트리거합니다.

    >[!IMPORTANT]
    > 호스트에 에이전트를 설치하지 않도록 선택하면 위협 보호 혜택 및 보안 경고의 하위 집합만 받게 됩니다. 네트워크 분석 및 악의적인 서버와의 통신과 관련된 경고는 계속 수신됩니다.

    호스트 수준 경고 목록은 경고 참조 [테이블을](alerts-reference.md#alerts-containerhost)참조하십시오.


* **AKS 클러스터 수준에서**위협 보호는 Kubernetes의 감사 로그를 분석하는 것을 기반으로 합니다. 에이전트 **없는** 모니터링을 사용하려면 **가격 책정 & 설정** 페이지에서 구독에 Kubernetes 옵션을 추가합니다(가격 [책정](security-center-pricing.md)참조). 이 수준에서 경고를 생성하기 위해 보안 센터는 AKS에서 검색한 로그를 사용하여 AKS 관리 서비스를 모니터링합니다. 이 수준에서 이벤트의 예로는 노출된 Kubernetes 대시보드, 높은 권한 역할 만들기 및 중요한 마운트 생성이 있습니다.

    >[!NOTE]
    > 보안 센터는 구독 설정에서 Kubernetes 옵션을 사용하도록 설정한 후 발생하는 Azure Kubernetes 서비스 작업 및 배포에 대한 보안 경고를 생성합니다. 

    AKS 클러스터 수준 경고 목록은 [경고 참조 테이블을](alerts-reference.md#alerts-akscluster)참조하십시오.

또한 글로벌 보안 연구원 팀이 위협 환경을 지속적으로 모니터링합니다. 검색될 때 컨테이너별 경고 및 취약점을 추가합니다.

> [!TIP]
> [이 블로그 게시물의](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)지침에 따라 컨테이너 경고를 시뮬레이션할 수 있습니다.








## <a name="threat-protection-for-sql-database-and-sql-data-warehouse"></a>SQL 데이터베이스 및 SQL 데이터 웨어하우스에 대한 위협 보호<a name="data-sql"></a>

Azure SQL Database에 대한 고급 위협 보호는 데이터베이스에 액세스하거나 악용하려는 비정상적인 시도를 나타내는 비정상적인 활동을 검색합니다.

의심스러운 데이터베이스 활동, 잠재적 인 취약점 또는 SQL 주입 공격, 비정상적인 데이터베이스 액세스 및 쿼리 패턴이 있는 경우 경고가 표시됩니다.

Azure SQL 데이터베이스 및 SQL에 대한 고급 위협 보호는 Azure SQL 데이터베이스, Azure SQL Database 관리 인스턴스, Azure SQL 데이터 웨어하우스 데이터베이스 및 Azure 가상 컴퓨터의 SQL 서버를 포함하는 고급 SQL 보안 기능을 위한 [ADS(고급](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) 데이터 보안) 통합 패키지의 일부입니다.

자세한 내용은 다음을 참조하세요.

* [Azure SQL 데이터베이스에 대한 고급 위협 보호를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Azure 가상 컴퓨터에서 SQL 서버에 대한 고급 위협 보호를 사용하도록 설정하는 방법](security-center-iaas-advanced-data.md)
* [SQL 데이터베이스 및 SQL 데이터 웨어하우스에 대한 위협 보호 경고 목록](alerts-reference.md#alerts-sql-db-and-warehouse)



## <a name="threat-protection-for-azure-storage"></a>Azure 저장소에 대한 위협 보호<a name="azure-storage"></a>

스토리지에 대한 고급 위협 보호는 저장소 계정에 액세스하거나 악용하려는 비정상적이고 잠재적으로 유해한 시도를 감지합니다. 이 보호 계층을 사용하면 보안 전문가가 될 필요 없이 위협을 해결할 수 있으며 보안 모니터링 시스템을 관리할 수 있습니다.

Azure 저장소에 대한 고급 위협 보호는 현재 [Blob 저장소에서만](https://azure.microsoft.com/services/storage/blobs/)사용할 수 있습니다. 

이 서비스는 모든 퍼블릭 클라우드 및 미국 정부 클라우드에서 사용할 수 있지만 다른 주권 또는 Azure 정부 클라우드 지역에서는 사용할 수 없습니다.

무료 30일 평가판을 포함한 가격 세부 정보는 [Azure 보안 센터 가격 페이지를](https://azure.microsoft.com/pricing/details/security-center/)참조하십시오.

자세한 내용은 다음을 참조하세요.

* [Azure 저장소에 대한 고급 위협 보호를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Azure 저장소에 대한 위협 보호 경고 목록](alerts-reference.md#alerts-azurestorage)

> [!TIP]
> [이 블로그 게시물의](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131)지침에 따라 Azure Storage 경고를 시뮬레이션할 수 있습니다.




## <a name="threat-protection-for-azure-cosmos-db"></a>Azure 코스모스 DB에 대한 위협 보호<a name="cosmos-db"></a>

Azure Cosmos DB 경고는 Azure Cosmos DB 계정에 액세스하거나 악용하려는 특이하고 잠재적으로 유해한 시도에 의해 생성됩니다.

자세한 내용은 다음을 참조하세요.

* [Azure 코스모스 DB에 대한 고급 위협 보호(미리 보기)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Azure Cosmos DB에 대한 위협 보호 경고 목록(미리 보기)](alerts-reference.md#alerts-azurecosmos)




## <a name="threat-protection-for-azure-network-layer"></a>Azure 네트워크 계층에 대한 위협 보호<a name="network-layer"></a>

보안 센터 네트워크 계층 분석은 Azure 코어 라우터에서 수집한 패킷 헤더인 샘플 [IPFIX 데이터를](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)기반으로 합니다. 이 데이터 피드를 기반으로 Security Center는 기계 학습 모델을 사용하여 악의적인 트래픽 활동을 식별하고 플래그를 표시합니다. 또한 보안 센터는 Microsoft 위협 인텔리전스 데이터베이스를 사용하여 IP 주소를 보강합니다.

일부 네트워크 구성은 보안 센터가 의심스러운 네트워크 활동에 대한 경고를 생성하지 못하도록 제한할 수 있습니다. 보안 센터에서 네트워크 경고를 생성하려면 다음을 수행해야 합니다.

- 가상 시스템에 공용 IP 주소가 있거나 공용 IP 주소가 있는 로드 밸런서에 있습니다.

- 가상 컴퓨터의 네트워크 송신 트래픽은 외부 IDS 솔루션에 의해 차단되지 않습니다.

- 의심스러운 통신이 발생한 전체 시간 동안 가상 시스템에 동일한 IP 주소가 할당되었습니다. 이는 관리되는 서비스의 일부로 만든 VM(예: AKS, Databricks)에도 적용됩니다.

Azure 네트워크 계층 경고 목록은 [경고 참조 테이블을](alerts-reference.md#alerts-azurenetlayer)참조하십시오.

보안 센터에서 네트워크 관련 신호를 사용하여 위협 보호를 적용하는 방법에 대한 자세한 내용은 [보안 센터의 휴리스틱 DNS 검색을](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/)참조하십시오.



## <a name="threat-protection-for-azure-management-layer-azure-resource-manager-preview"></a>Azure 관리 계층에 대한 위협 보호(Azure 리소스 관리자) (미리 보기)<a name ="management-layer"></a>

Azure 리소스 관리자를 기반으로 하는 보안 센터의 보호 계층은 현재 미리 보기 상태입니다.

보안 센터는 Azure의 제어 평면으로 간주되는 Azure 리소스 관리자 이벤트를 사용하여 추가 보호 계층을 제공합니다. 보안 센터는 Azure 리소스 관리자 레코드를 분석하여 Azure 구독 환경에서 비정상적이거나 잠재적으로 유해한 작업을 검색합니다.

Azure 리소스 관리자(미리 보기) 경고 목록은 [경고 참조 테이블을](alerts-reference.md#alerts-azureresourceman)참조하십시오.



>[!NOTE]
> 앞의 분석 중 일부는 Microsoft 클라우드 앱 보안에 의해 구동됩니다. 이러한 분석을 활용하려면 클라우드 앱 보안 라이선스를 활성화해야 합니다. 클라우드 앱 보안 라이선스가 있는 경우 이러한 경고는 기본적으로 활성화됩니다. 경고를 사용하지 않도록 설정하려면 다음을 수행하십시오.
>
> 1. 보안 **센터** 블레이드에서 **보안 정책을**선택합니다. 변경하려는 구독의 경우 **설정 편집을**선택합니다.
> 2. **위협 탐지를 선택합니다.**
> 3. **통합 사용 에서**Clear 를 **선택하여 Microsoft 클라우드 앱 보안에서 내 데이터에 액세스하고** **저장을**선택합니다.

>[!NOTE]
>보안 센터는 보안 관련 고객 데이터를 리소스와 동일한 지역에 저장합니다. Microsoft가 리소스의 지리적 위치에 보안 센터를 아직 배포하지 않은 경우 미국에 데이터를 저장합니다. 클라우드 앱 보안을 사용하도록 설정하면 이 정보는 클라우드 앱 보안의 지리적 위치 규칙에 따라 저장됩니다. 자세한 내용은 [비지역 서비스에 대한 데이터 저장소를](https://azuredatacentermap.azurewebsites.net/)참조하십시오.








## <a name="threat-protection-for-azure-key-vault-preview"></a>Azure 키 자격 증명 모음에 대한 위협 보호(미리 보기)<a name="azure-keyvault"></a>

> [!NOTE]
> 이 서비스는 현재 Azure 정부 및 주권 클라우드 지역에서 사용할 수 없습니다.

Azure Key Vault는 암호화 키와 비밀(예: 인증서, 연결 문자열 및 암호)을 보호하는 클라우드 서비스입니다. 

Azure 보안 센터에는 Azure Key Vault에 대한 Azure 네이티브 고급 위협 보호기능이 포함되어 있으며, 추가 보안 인텔리전스 계층을 제공합니다. 보안 센터는 Key Vault 계정에 액세스하거나 악용하려는 비정상적이고 잠재적으로 유해한 시도를 감지합니다. 이 보호 계층을 사용하면 타사 보안 모니터링 시스템을 관리할 필요 없이 보안 전문가가 되지 않고도 위협을 해결할 수 있습니다.  

비정상적인 활동이 발생하면 보안 센터에서 경고를 표시하고 선택적으로 전자 메일을 통해 구독 관리자에게 보냅니다. 이러한 경고에는 의심스러운 활동의 세부 정보와 위협을 조사하고 해결하는 방법에 대한 권장 사항이 포함됩니다. 

Azure 키 자격 증명 모음 경고 목록은 [경고 참조 테이블을](alerts-reference.md#alerts-azurekv)참조하십시오.





## <a name="threat-protection-for-other-microsoft-services"></a>다른 Microsoft 서비스에 대한 위협 보호<a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Azure WAF에 대한 위협 보호<a name="azure-waf"></a>

Azure Application Gateway는 일반적인 악용 및 취약성으로부터 웹 애플리케이션을 중앙 집중식으로 보호하는 WAF(웹 애플리케이션 방화벽)를 제공합니다.

일반적으로 알려진 취약성을 악용하여 웹 애플리케이션을 공격하는 악의적인 사례가 점점 늘어나고 있습니다. 응용 프로그램 게이트웨이 WAF는 개방형 웹 응용 프로그램 보안 프로젝트의 핵심 규칙 집합 3.0 또는 2.2.9를 기반으로 합니다. WAF는 새로운 취약점으로부터 보호하기 위해 자동으로 업데이트됩니다. 

Azure WAF에 대한 라이선스가 있는 경우 추가 구성없이 WAF 경고가 보안 센터로 스트리밍됩니다. WAF에서 생성된 경고에 대한 자세한 내용은 [웹 응용 프로그램 방화벽 CRS 규칙 그룹 및 규칙을](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31)참조하십시오.


### <a name="threat-protection-for-azure-ddos-protection"></a>Azure DDoS 보호를 위한 위협 보호<a name="azure-ddos"></a>

분산 서비스 거부(DDoS) 공격은 실행하기 쉬운 것으로 알려져 있습니다. 특히 애플리케이션을 클라우드로 이동하는 경우 보안 문제가 크게 되고 있습니다. 

DDoS 공격은 애플리케이션의 리소스를 소진시켜서 정상적인 사용자가 애플리케이션을 사용할 수 없게 만듭니다. DDoS 공격은 인터넷을 통해 도달할 수 있는 모든 엔드포인트를 대상으로 할 수 있습니다.

DDoS 공격을 방어하려면 Azure DDoS 보호에 대한 라이선스를 구입하고 응용 프로그램 디자인 모범 사례를 따르고 있는지 확인합니다. DDoS 보호는 서로 다른 서비스 계층을 제공합니다. 자세한 내용은 [Azure DDoS 보호 개요를](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)참조하십시오.

Azure DDoS 보호 경고 목록은 [경고 참조 테이블을](alerts-reference.md#alerts-azureddos)참조하십시오.


## <a name="next-steps"></a>다음 단계
이러한 위협 보호 기능의 보안 경고에 대한 자세한 내용은 다음 문서를 참조하세요.

* [모든 Azure 보안 센터 경고에 대한 참조 테이블](alerts-reference.md)
* [Azure Security Center의 보안 경고](security-center-alerts-overview.md)
* [Azure Security Center에서 보안 경고 관리 및 응답](security-center-managing-and-responding-alerts.md)
* [내보내기 보안 경고 및 권장 사항(미리 보기)](continuous-export.md)