---
title: PCI DSS를 위한 안전한 웹 앱 | 마이크로 소프트 문서
description: 이 샘플 앱은 Azure에서 개발할 때 응용 프로그램 및 조직의 보안 상태를 개선하는 보안 모범 사례를 구현합니다.
keywords: na
services: security
documentationcenter: na
author: fehase
manager: rkarlin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: terrylan
ms.openlocfilehash: 4fe612db65d985be2f1f1c81d03c3ee735c03889
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "69992615"
---
# <a name="develop-a-secure-infrastructure-for-a-pci-app"></a>PCI 앱을 위한 보안 인프라 개발

## <a name="overview"></a>개요

PCI(결제 카드 산업) 앱용 이 보안 인프라는 카드 소지자 데이터의 수집, 저장 및 검색에 적합한 서비스(PaaS) 환경으로 결제 카드 산업 플랫폼을 배포하기 위한 지침을 제공합니다. 이 솔루션은 공통 참조 아키텍처를 위한 Azure 리소스 배포 및 구성을 자동화하여 고객이 특정 보안 및 규정 준수 요구 사항에 부합할 수 있는 방법을 보여 주며 고객이 Azure에서 자체 솔루션을 구축 및 구성하기 위한 기초 역할을 합니다. 이 솔루션은 결제 카드 산업 데이터 보안 표준(PCI DSS 3.2)과 유사한 요구 사항의 하위 집합을 구현합니다.

이 샘플에서는 미리 구성된 보안 컨트롤을 갖춘 PaaS 웹 응용 프로그램 참조 아키텍처를 자동으로 배포하여 고객이 PCI DSS 3.2 요구 사항과 유사한 규정 준수를 달성할 수 있도록 지원합니다. 이 솔루션은 리소스 배포 및 구성을 안내하는 Azure Resource Manager 템플릿과 PowerShell 스크립트로 구성되었습니다.

응용 프로그램 구성 요소가 제대로 구성되었는지 확인하려면 이 문서에서 설명하는 단계를 순차적으로 수행해야 합니다. 데이터베이스, Azure 앱 서비스, Azure 키 볼트 인스턴스 및 Azure 응용 프로그램 게이트웨이 인스턴스는 서로 종속됩니다.

배포 스크립트는 인프라를 설정합니다. 배포 스크립트를 실행한 후 Azure Portal에서 구성 요소와 서비스를 함께 연결하기 위해 몇 가지 수동 구성을 수행해야 합니다.

이 샘플은 소매 업계에서 작업하고 안전한 Azure 인프라를 사용하여 소매 앱을 빌드하려는 Azure의 숙련된 개발자를 대상으로 합니다.

> [!NOTE]
> 이 아키텍처는 고객이 특정 요구 사항에 맞게 조정할 수 있는 기반이며 있는 프로덕션 환경에 그대로 사용해서는 안됩니다.

애플리케이션을 수정 없이 이러한 환경에 배포하는 것은 배포 PCI DSS 3.2의 요구 사항을 온전히 충족하기에는 부족합니다. 다음 사항에 유의하세요.

- 이 아키텍처는 고객이 PCI DSS 3.2 규격에 맞게 Azure를 사용하기 위한 기준을 제공합니다.
- 요구 사항은 각 고객의 구현에서 특정 상황에 따라 달라질 수 있으므로 이 아키텍처를 사용하여 구축된 솔루션의 적합한 보안 및 준수 평가의 수행은 고객의 책임입니다.

이 응용 프로그램을 개발하고 배포할 때 다음 방법을 배웁니다.

- Azure Key Vault 인스턴스를 만들고 비밀을 저장하고 검색합니다.
- Azure SQL용 Azure 데이터베이스 배포, 보안 데이터 설정 및 액세스 권한 부여
- 프런트 엔드 방화벽 aEcess를 사용하는 전용 격리된 앱 서비스 환경으로 Azure 웹 앱을 배포합니다.
- [OWASP 상위 10개 규칙 집합을](https://coreruleset.org/)사용하는 방화벽을 사용하여 Azure 응용 프로그램 게이트웨이 인스턴스를 만들고 구성합니다.
- Azure 서비스를 사용하여 전송 중 및 미사용 데이터의 암호화를 사용하도록 설정합니다.
- Azure 정책 및 블루 프린트를 설정하여 규정 준수 를 평가합니다.

이 앱을 개발하고 배포한 후에는 설명된 구성 및 보안 조치와 함께 다음 샘플 웹 앱을 설정하게 됩니다.

## <a name="architecture-diagram-and-components"></a>아키텍처 다이어그램 및 구성 요소
응용 프로그램은 세 계층과 일반적인 n 계층 응용 프로그램입니다. 모니터링 및 비밀 관리 구성 요소가 통합된 프런트 엔드, 백 엔드 및 데이터베이스 계층은 다음과 같습니다.

![앱 아키텍처](./media/secure-pci-web-app/architecture.png)

아키텍처는 다음과 같은 구성 요소로 구성됩니다.

- [앱 서비스 환경 v2](https://docs.microsoft.com/azure/app-service/environment/intro/). 응용 프로그램 아키텍처에 대한 앱 서비스 환경 및 로드 밸러버를 제공합니다.
- [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/). 응용 프로그램 아키텍처에 대한 게이트웨이 및 방화벽을 제공합니다.
- [응용 프로그램 인사이트](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). 여러 플랫폼에서 확장 가능한 응용 프로그램 성능 관리(APM) 서비스를 제공합니다.
- [Azure 키 볼트](https://docs.microsoft.com/azure/key-vault/). 앱의 비밀을 저장하고 암호화하고 앱 주변의 액세스 정책 생성을 관리합니다.
- [Azure 활성 디렉터리](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis/). 클라우드 기반 ID 및 액세스 관리 서비스, 로그인 및 리소스 액세스 서비스를 제공합니다.
- [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview). 도메인을 호스트하는 서비스를 제공합니다.
- [Azure 로드 밸러터](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview/). 애플리케이션을 확장하고 서비스를 위한 고가용성 을 창출합니다.
- [Azure 저장소](https://docs.microsoft.com/azure/storage/common/storage-introduction/) 형식 연결된 서비스의 JSON 속성에 대해 설명합니다. 최신 데이터 저장소 시나리오에 대한 솔루션을 제공합니다.
- [Azure 웹 앱](https://docs.microsoft.com/azure/app-service/overview/).  웹 응용 프로그램을 호스팅하기 위한 HTTP 기반 서비스를 제공합니다.
- [AzureSQL에 대한 Azure 데이터베이스](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview/). 앱의 데이터를 안전하게 저장합니다.
- [Azure Blueprint.](https://docs.microsoft.com/azure/governance/blueprints/overview/) 특정 표준 및 요구 사항을 사양 및 준수합니다.
- [Azure 보안 센터](https://docs.microsoft.com/azure/security-center/)
- [Azure 정책](https://docs.microsoft.com/azure/governance/policy/overview). 리소스가 할당된 정책을 준수하지 않는지 평가합니다.

## <a name="threat-model"></a>위협 모델
위협 모델링은 비즈니스 및 응용 프로그램에 대한 잠재적보안 위협을 식별한 다음 적절한 완화 계획을 수립하는 프로세스입니다.

이 샘플에서는 [Microsoft 위협 모델링 도구를](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) 사용하여 보안 샘플 앱에 대한 위협 모델링을 구현했습니다. 구성 요소와 데이터 흐름을 다이어그램으로 만들어 개발 프로세스 초기에 문제와 위협을 식별할 수 있습니다. 이렇게 하면 나중에 시간과 비용을 절약할 수 있습니다.

샘플 앱의 위협 모델입니다.

![위협 모델](./media/secure-pci-web-app/threat-model.png)

위협 모델링 도구가 생성하는 일부 샘플 위협 및 잠재적 취약점은 다음 스크린샷에 나와 있습니다. 위협 모델은 노출된 공격 노출 에 대한 개요를 제공하고 개발자가 문제를 완화하는 방법에 대해 생각하라는 메시지를 표시합니다.

![위협 모델 출력](./media/secure-web-app/threat-model-output.png)

예를 들어, 이전 위협 모델 출력의 SQL 주입은 사용자 입력을 삭제하고 PostgreSQL용 Azure 데이터베이스에 저장된 함수를 사용하여 완화됩니다. 이 완화는 데이터 읽기 및 쓰기 중에 쿼리를 임의로 실행하지 못하게 합니다.

개발자는 위협 모델 출력의 각 위협을 완화하여 시스템의 전반적인 보안을 향상시킵니다.

## <a name="deployment"></a>배포
### <a name="prerequisites"></a>사전 요구 사항
응용 프로그램을 실행하려면 다음 도구를 설치해야 합니다.

- 응용 프로그램 코드를 수정하고 볼 수 있는 코드 편집기입니다. [비주얼 스튜디오 코드는](https://code.visualstudio.com/) 오픈 소스 옵션입니다.
- 개발 컴퓨터에서 [Azure CLI를](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest) 제공합니다.
- 시스템에 [Git.](https://git-scm.com/) Git은 소스 코드를 로컬로 복제하는 데 사용됩니다.
- [jq](https://stedolan.github.io/jq/), 사용자 친화적 인 방법으로 JSON을 쿼리하기위한 UNIX 도구입니다.

이 샘플은 Azure 리소스 관리자의 API 서비스에서 Azure 내에서 리소스를 배포하기 위해 처리하는 JSON 구성 파일 및 PowerShell 스크립트로 구성됩니다. 자세한 배포 지침은 [여기](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM)에 있습니다.

#### <a name="quickstart"></a>빠른 시작

1.  [이](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM.git) GitHub 리포지토리를 로컬 워크스테이션에 복제하거나 다운로드합니다.
2.  0-Setup-AdministrativeAccountAndPermission.md를 검토하고 제공된 명령을 실행합니다.
3.  Contoso 샘플 데이터를 사용하여 테스트 솔루션을 배포하거나, 초기 프로덕션 환경을 시험해 봅니다.

    이 스크립트는 Contoso 샘플 데이터를 사용하여 웹 저장소데모를 위해 Azure 리소스를 배포합니다.

이 예제에서는 앱 서비스에 웹 앱을 배포하고 리소스를 만드는 배포 스크립트를 실행합니다.

Azure에서 앱을 배포하는 방법에는 다음과 같은 여러 가지 방법이 있습니다.

- Azure 리소스 관리자 템플릿
- PowerShell
- Azure CLI
- Azure portal
- Azure DevOps

## <a name="guidance-and-recommendations"></a>지침 및 권장 사항

### <a name="network"></a>네트워크
아키텍처는 10.200.0.0/16 주소 공간으로 프라이빗 가상 네트워크를 정의합니다.
 ![Virtual_Network](./media/secure-pci-web-app/virtual-network.png)

### <a name="network-security-groups"></a>네트워크 보안 그룹
네트워크 보안https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) 그룹(가상 네트워크 내의 트래픽을 허용하거나 거부하는 ACL(액세스 제어 목록)을 포함합니다. 네트워크 보안 그룹은 서브넷 또는 개별 VM 수준에서 트래픽을 보호하는 데 사용될 수 있습니다. 다음과 같은 네트워크 보안 그룹이 있습니다.

- Application Gateway용 네트워크 보안 그룹 1개
- App Service Environment용 네트워크 보안 그룹 1개
- Azure SQL Database용 네트워크 보안 그룹 1개
- 요새 호스트용 네트워크 보안 그룹 1개

각 네트워크 보안 그룹에는 솔루션이 안전하고 올바르게 작동할 수 있도록 특정 포트와 프로토콜이 열려 있습니다. 또한 각 네트워크 보안 그룹에 대해 다음과 같은 구성을 사용합니다.

- 진단 로그 및https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) 이벤트(저장소 계정에 활성화되고 저장됨)
- Azure 모니터 로그가 네트워크 보안 그룹의 진단 에 연결됩니다(https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

### <a name="nsg-config"></a>NSG 구성
앱 서비스 환경에 대한 NSG 구성은 아래 이미지와 같이 구성해야 합니다.

 ![NSG_Config](./media/secure-pci-web-app/nsg-config.png)

각 서브넷이 해당 네트워크 보안 그룹에 연결됩니다.

### <a name="config"></a>Config
서브넷은 아래 이미지와 같이 구성됩니다.
 ![Config](./media/secure-pci-web-app/config.png)

### <a name="azure-dns"></a>Azure DNS
도메인 이름 시스템(DNS)은 웹 사이트 또는 서비스 이름을 IP 주소로 변환(또는 해결)할 책임이 있습니다. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview)는 Azure 인프라를 사용하여 이름 확인을 제공하는 DNS 도메인에 대한 호스팅 서비스입니다. Azure에서 도메인을 호스팅하면 사용자가 다른 Azure 서비스와 동일한 자격 증명, API, 도구 및 대금 청구를 사용하여 DNS 레코드를 관리할 수 있습니다. Azure DNS는 프라이빗 DNS 도메인도 지원합니다.

### <a name="protect-data"></a>데이터 보호
클라우드에서 데이터를 보호하려면 데이터에서 발생 가능한 상태 및 해당 상태에 사용 가능한 컨트롤을 고려해야 합니다. Azure 데이터 보안 및 암호화의 모범 사례는 다음과 같은 데이터 상태와 관련이 있습니다.

- 미사용: 물리적 미디어(자기 또는 광 디스크)에 정적으로 존재하는 모든 정보 스토리지 개체, 컨테이너 및 형식이 포함됩니다.
- 전송 중: 구성 요소, 위치 또는 프로그램 간에 데이터를 전송하는 경우 전송 중입니다. 네트워크를 통하거나, 서비스 버스 간에(ExpressRoute와 같은 하이브리드 연결을 비롯한 온-프레미스와 클라우드 간) 또는 입출력 프로세스 중에 전송을 예로 들 수 있습니다.

### <a name="azure-storage"></a>Azure Storage
미사용 요구 사항에서 암호화된 데이터를 충족하기 위해 모든 [Azure Storage는](https://azure.microsoft.com/services/storage/) Azure Key Vault를 사용하여 데이터에 액세스하고 암호화하는 키제어를 유지합니다. 이렇게 하면 PCI DSS 3.2에서 정의한 조직의 보안 약정 및 준수 요구 사항을 지원하기 위해 카드 소유자 데이터를 보호할 수 있습니다.

### <a name="azure-disk-encryption"></a>Azure 디스크 암호화
Azure Disk Encryption은 Windows의 BitLocker 기능을 활용하여 데이터 디스크에 대한 볼륨 암호화를 제공합니다. 이 솔루션은 Azure Key Vault와 통합되어 디스크 암호화 키를 제어하고 관리하는 데 유용합니다.

### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database 인스턴스는 다음 데이터베이스 보안 조치를 사용합니다.

- [Active Directory 인증 및 권한 부여](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)를 사용하면 데이터베이스 사용자 및 기타 Microsoft 서비스의 ID를 중앙의 한 위치에서 집중적으로 관리할 수 있습니다.
- [SQL 데이터베이스 감사는](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started/) 데이터베이스 이벤트를 추적하고 Azure 저장소 계정의 감사 로그에 씁니다.
- Azure SQL Database는 [투명한 데이터 암호화](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)를 사용하여 데이터베이스, 관련 백업 및 트랜잭션 로그 파일의 실시간 암호화 및 암호 해독을 수행하고 미사용 정보를 보호하도록 구성됩니다. 투명한 데이터 암호화는 저장된 데이터가 무단으로 액세스되지 못하게 합니다.
- [방화벽 규칙](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)은 적절한 권한이 부여될 때까지 데이터베이스 서버에 대한 모든 액세스를 차단합니다. 방화벽은 각 요청이 시작된 IP 주소의 데이터베이스에 대한 액세스를 허용합니다.
- [SQL 위협 요소 탐지](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started)를 사용하면 의심스러운 데이터베이스 활동, 잠재적 취약성, SQL 삽입 공격 및 비정상 데이터베이스 액세스 패턴에 대한 보안 경고를 제공하여 발생할 수 있는 잠재적 위협을 탐지하고 대응할 수 있습니다.
- [암호화된 열](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)은 중요한 데이터가 데이터베이스 시스템 내에서 일반 텍스트로 나타나지 않게 합니다. 데이터 암호화를 사용하도록 설정하면 키에 액세스할 수 있는 클라이언트 애플리케이션 또는 애플리케이션 서버만 일반 텍스트 데이터에 액세스할 수 있습니다.
- [SQL Database 동적 데이터 마스킹](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)은 권한이 없는 사용자 또는 애플리케이션에 데이터를 마스킹하여 중요한 데이터의 노출을 제한합니다. 동적 데이터 마스킹은 잠재적으로 중요한 데이터를 자동으로 검색하고 적용할 적절한 마스크를 제안할 수 있습니다. 이렇게 하면 무단 액세스를 통해 데이터베이스가 종료되지 않도록 데이터에 대한 액세스를 식별하고 줄일 수 있습니다. 고객은 데이터베이스 스키마를 준수하도록 동적 데이터 마스킹 설정을 조정할 책임이 있습니다.

### <a name="identity-management"></a>ID 관리
Azure 환경에서 카드 소유자 데이터에 대한 액세스를 관리하는 기능을 제공하는 기술은 다음과 같습니다.

- Azure Active Directory는 Microsoft의 다중 테넌트 클라우드 기반 디렉터리 및 ID 관리 서비스입니다. Azure SQL Database에 액세스하는 사용자를 포함하여 이 솔루션의 모든 사용자는 Azure Active Directory에서 만들어졌습니다.
- 애플리케이션에 대한 인증은 Azure Active Directory를 사용하여 수행됩니다. 자세한 내용은 [응용 프로그램을 Azure Active Directory와 통합을](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-an-application-integration)참조하십시오. 또한 데이터베이스 열 암호화는 Azure Active Directory를 사용하여 Azure SQL Database에 애플리케이션을 인증합니다. 자세한 내용은 [Azure SQL Database 에서 중요한 데이터를 보호하는 방법을](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview)참조하세요.
- Azure 역할 기반 액세스 제어를 사용하면 관리자가 세분화된 액세스 권한을 정의하여 사용자가 자신의 작업을 수행하는 데 필요한 액세스 권한만 부여할 수 있습니다. 관리자는 모든 사용자에게 Azure 리소스에 대한 무제한 권한을 부여하는 대신 카드 소유자 데이터에 액세스하기 위한 특정 작업만 허용할 수 있습니다. 구독 액세스는 구독 관리자로 제한됩니다.
- Azure Active Directory Privileged Identity Management를 사용하면 고객이 특정 정보(예: 카드 소유자 데이터)에 액세스할 수 있는 사용자 수를 최소화할 수 있습니다. 관리자는 Azure Active Directory Privileged Identity Management를 사용하여 권한 있는 ID와 리소스에 대한 액세스를 검색, 제한 및 모니터링할 수 있습니다. 필요한 경우 이 기능을 사용하여 요청 시 JIT(Just-In-Time) 관리 액세스를 적용할 수도 있습니다.
- Azure Active Directory ID 보호는 조직의 ID에 영향을 미치는 잠재적 취약점을 감지하고, 조직의 ID와 관련된 의심스러운 작업을 검색하기 위해 자동화된 응답을 구성하고, 의심스러운 작업을 조사합니다. 적절한 조치를 취할 수 있습니다.

### <a name="secrets-management"></a>비밀 관리
이 솔루션은 Azure Key Vault를 사용하여 키와 비밀을 관리합니다. Azure Key Vault는 클라우드 애플리케이션 및 서비스에서 사용되는 암호화 키 및 비밀을 보호하는데 도움이 됩니다. Azure Key Vault 기능을 통해 고객이 이러한 데이터를 보호하고 액세스할 수 있습니다.

- 필요에 따라 고급 액세스 정책이 구성됩니다.
- Key Vault 액세스 정책은 키와 비밀에 대한 최소 필수 권한으로 정의됩니다.
- Key Vault의 모든 키와 비밀에는 만료 날짜가 있습니다.
- Key Vault의 모든 키는 특별한 하드웨어 보안 모듈을 통해 보호됩니다. 키 유형은 HSM 보호 2048비트 RSA 키입니다.
- Key Vault를 사용하면 키와 비밀(예: 인증 키, 저장소 계정 키, 데이터 암호화 키) 을 암호화할 수 있습니다. 하드웨어 보안 모듈(HSM)에 의해 보호되는 키를 사용하여 PFX 파일 및 암호)
- RBAC를 사용하여 특정 범위에서 사용자, 그룹 및 응용 프로그램에 권한을 할당합니다.
- 키 볼트를 사용하여 자동 갱신을 통해 TLS 인증서를 관리합니다.
- Key Vault에 대한 진단 로그는 365일 이상의 보존 기간 동안 사용하도록 설정됩니다.
- 키에 허용되는 암호화 작업은 필요한 것으로 제한됩니다

### <a name="azure-security-center"></a>Azure Security Center
고객은 Azure Security Center를 통해 여러 워크로드에 걸쳐 보안 정책을 중앙에서 적용 및 관리하고, 위협에 대한 노출을 제한하고, 공격을 검색하여 대응할 수 있습니다. 또한 Azure Security Center는 Azure 서비스의 기존 구성에 액세스하여 보안 상태를 개선하고 데이터를 보호하는 데 유용한 구성과 서비스 권장 사항을 제공합니다.

Azure Security Center는 다양한 검색 기능을 사용하여 고객에게 환경을 대상으로 하는 잠재적인 공격에 대해 경고합니다. 이러한 경고는 트리거된 경고, 대상으로 지정된 리소스 및 공격의 출처에 대한 중요한 정보를 포함합니다. Azure Security Center에는 위협 또는 의심스러운 활동이 발생할 때 트리거되는 미리 정의된 보안 경고 집합이 있습니다. Azure Security Center에서 사용자 지정 경고 규칙을 사용하면 고객은 자신의 환경에서 이미 수집된 데이터를 기반으로 하는 새 보안 경고를 정의할 수 있습니다.

Azure Security Center에서는 우선 순위가 지정된 보안 경고 및 인시던트를 제공하여 고객이 잠재적인 보안 문제를 더 쉽게 검색하고 해결하게 합니다. 위협 인텔리전스 보고서는 인시던트 대응 팀이 위협을 조사하고 수정하도록 지원하기 위해 탐지된 각 위협에 대해 생성됩니다.

### <a name="azure-application-gateway"></a>Azure Application Gateway
이 아키텍처는 웹 애플리케이션 방화벽이 구성되어 있고 OWASP 규칙 집합을 사용할 수 있는 Azure Application Gateway를 통해 보안 취약성의 위험을 줄입니다. 추가적인 기능은 다음과 같습니다.

- 엔드 투 엔드 SSL
- TLS v1.0 및 v1.1 사용 안 함
- TLSv1.2 사용
- 웹 애플리케이션 방화벽(방지 모드)
- 방지 모드, OWASP 3.0 규칙 집합 사용
- 진단 로깅 사용
- 사용자 지정 상태 프로브
- Azure Security Center 및 Azure Advisor는 추가적인 보호 및 알림을 제공합니다. Azure Security Center는 평판 시스템도 제공합니다.

### <a name="logging-and-auditing"></a>로깅 및 감사
Azure 서비스는 시스템 및 사용자 활동, 시스템 상태를 광범위하게 기록합니다.

- [활동 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)는 구독에 있는 리소스에서 수행된 작업에 대한 인사이트를 제공합니다. 활동 로그는 작업의 개시 장치, 발생 시간 및 상태를 결정하는 데 도움이 될 수 있습니다.
- [진단 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)는 모든 리소스에서 내보낸 모든 로그를 포함합니다. 이러한 로그에는 Windows 이벤트 시스템 로그, Azure 저장소 로그, 키 볼트 감사 로그, 응용 프로그램 게이트웨이 액세스 및 방화벽 로그가 포함됩니다. 모든 진단 로그는 보관을 위해 암호화된 중앙 집중식 Azure Storage 계정에 기록됩니다. 보존은 조직 특정 보존 요구 사항에 맞게 최대 730일까지 사용자가 구성할 수 있습니다.

### <a name="azure-monitor-logs"></a>Azure Monitor 로그
이러한 로그는 처리, 저장 및 대시보드 보고를 위해 [Azure Monitor 로그에](https://azure.microsoft.com/services/log-analytics/) 통합됩니다. 수집이 완료되면 데이터는 Log Analytics 작업 영역 내의 각 데이터 형식에 대해 별도 테이블로 구성되어 원본에 관계없이 모든 데이터가 함께 분석되도록 합니다. 또한 Azure Security Center는 Azure Monitor 로그와 통합되어 고객이 Kusto 쿼리를 사용하여 보안 이벤트 데이터에 액세스하고 다른 서비스의 데이터와 결합할 수 있습니다.

다음 Azure [모니터링 솔루션은](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) 이 아키텍처의 일부로 포함됩니다.

- [활성 디렉터리 평가](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Active Directory 상태 검사 솔루션은 정기적으로 서버 환경의 위험과 상태를 평가하고 배포된 서버 인프라와 관련된 우선 순위 목록을 제공합니다.
- [SQL 평가](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): SQL 상태 확인 솔루션은 일정한 간격으로 서버 환경의 위험과 상태를 평가하고, 배포된 서버 인프라에 관련된 권장 사항의 우선 순위 목록을 고객에게 제공합니다.
- [에이전트 상태:](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)에이전트 상태 솔루션은 배포된 에이전트 수와 해당 지리적 분포, 응답하지 않는 에이전트 수 및 운영 데이터를 제출하는 에이전트 수를 보고합니다.
- [활동 로그 분석](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): 활동 로그 분석 솔루션은 고객에 대한 모든 Azure 구독에서 Azure 활동 로그를 분석하는 데 도움을 줍니다.

### <a name="azure-monitor"></a>Azure Monitor
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/)를 사용하면 사용자의 Azure 리소스에서 API 호출을 추적하는 것을 포함하여 조직에서 감사, 경고 만들기 및 데이터 보관을 수행할 수 있도록 하여 사용자가 성능을 추적하고, 보안을 유지하고, 추세를 식별할 수 있습니다.

### <a name="application-insights"></a>애플리케이션 정보
[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview)는 여러 플랫폼의 웹 개발자를 위한 확장 가능한 애플리케이션 성능 관리 서비스입니다. Application Insights는 성능 이상을 감지하며, 고객이 라이브 웹 애플리케이션을 모니터링하는 데 사용할 수 있습니다. 고객이 문제를 진단하고 실제로 앱을 사용하여 수행하는 작업을 파악할 수 있는 강력한 분석 도구를 포함하고 있습니다. 고객이 성능 및 가용성을 지속적으로 향상시킬 수 있도록 설계되었습니다.

### <a name="azure-key-vault"></a>Azure Key Vault
키를 저장할 조직에 대한 자격 증명 모음을 만들고 다음과 같은 운영 작업에 대한 책임을 유지합니다.

- Key Vault에 저장된 데이터에는 다음 항목이 포함됩니다.

   - Application Insight 키
   - 데이터 스토리지 액세스 키
   - 연결 문자열
   - 데이터 테이블 이름
   - 사용자 자격 증명

- 필요에 따라 고급 액세스 정책이 구성됩니다.
- Key Vault 액세스 정책은 키와 비밀에 대한 최소 필수 권한으로 정의됩니다.
- Key Vault의 모든 키와 비밀에는 만료 날짜가 있습니다.
- 키 볼트의 모든 키는 HSM [키 유형 = HSM 보호 2048비트 RSA 키]로 보호됩니다.
- 모든 사용자/ID에는 RBAC(역할 기반 액세스 제어)를 사용하여 최소 필수 권한이 부여됩니다.
- 애플리케이션이 서로 신뢰하고 런타임 시 동일한 비밀에 액세스할 필요가 없으면 애플리케이션에서 Key Vault를 공유하지 않습니다.
- Key Vault에 대한 진단 로그는 365일 이상의 보존 기간 동안 사용하도록 설정됩니다.
- 키에 허용되는 암호화 작업은 필요한 것으로 제한됩니다

### <a name="vpn-and-expressroute"></a>VPN 및 ExpressRoute
이 PaaS 웹 애플리케이션 참조 아키텍처의 일부로 배포된 리소스에 대한 연결을 안전하게 설정하도록 보안 VPN 터널 또는 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)를 구성해야 합니다. VPN 또는 ExpressRoute를 적절히 설정하면 고객이 전송 중인 데이터에 대한 보호 계층을 추가할 수 있습니다.

Azure를 통해 보안 VPN 터널을 구현하면 온-프레미스 네트워크와 Azure Virtual Network 간의 가상 프라이빗 연결을 만들 수 있습니다. 이 연결은 인터넷을 통해 이루어지며 고객이 고객의 네트워크와 Azure 간의 암호화된 링크 내에서 정보를 안전하게 "터널링"할 수 있도록 합니다. 사이트 간 VPN은 수십 년 동안 모든 규모의 기업에서 배포한 안전하고 완성도가 높은 기술입니다. IPsec 터널 모드는 이 옵션에서 암호화 메커니즘으로 사용됩니다.

VPN 터널 내의 트래픽은 사이트 간 VPN을 사용하여 인터넷을 통과하므로 Microsoft는 훨씬 더 안전한 또 다른 연결 옵션을 제공합니다. Azure ExpressRoute는 Azure 및 온-프레미스 위치 또는 Exchange 호스팅 공급자 간의 전용 WAN 링크입니다. ExpressRoute 연결은 인터넷을 통해 수행되지 않으므로 인터넷을 통한 일반적인 연결보다 높은 안정성, 빠른 속도, 짧은 대기 시간 및 높은 보안을 제공합니다. 또한 이 연결은 고객의 통신 공급자의 직접 연결이므로 데이터는 인터넷을 통해 이동하지 않으며, 따라서 노출되지 않습니다.

온-프레미스 네트워크를 Azure로 확장하는 보안 하이브리드 네트워크를 구현하는 모범 사례를 [사용할 수 있습니다](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="cost-considerations"></a>비용 고려 사항
Azure 계정이 아직 없는 경우 무료 계정을 만들 수 있습니다. [무료 계정 페이지로](https://azure.microsoft.com/free/) 이동하여 시작하고, 무료 Azure 계정으로 수행할 수 있는 작업을 확인하고, 12개월 동안 어떤 제품이 무료인지 알아보세요.

보안 기능을 사용하여 샘플 앱에 리소스를 배포하려면 일부 프리미엄 기능에 대한 비용을 지불해야 합니다. 응용 프로그램 확장 및 Azure에서 제공하는 프리 계층 및 평가판을 응용 프로그램 요구 사항을 충족하도록 업그레이드해야 하므로 비용이 증가할 수 있습니다. Azure [가격 계산기를](https://azure.microsoft.com/pricing/calculator/) 사용하여 비용을 예측합니다.

## <a name="next-steps"></a>다음 단계
다음 문서에서는 보안 응용 프로그램을 설계, 개발 및 배포하는 데 도움이 될 수 있습니다.

- [디자인](secure-design.md)
- [개발](secure-develop.md)
- [배포](secure-deploy.md)
