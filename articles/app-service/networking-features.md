---
title: 네트워킹 기능
description: Azure App Service의 네트워크 기능 및 보안 또는 기능에 필요한 네트워크에 필요한 기능에 대해 알아봅니다.
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 03/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 79f85261115dbddcb0b04cd2863a90912de2ab87
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474913"
---
# <a name="app-service-networking-features"></a>앱 서비스 네트워킹 기능

Azure 앱 서비스의 응용 프로그램은 여러 가지 방법으로 배포할 수 있습니다. 기본적으로 App Service 호스팅 앱은 인터넷에 직접 액세스할 수 있으며 인터넷에서 호스팅되는 끝점에만 도달할 수 있습니다. 그러나 많은 고객 응용 프로그램은 인바운드 및 아웃바운드 네트워크 트래픽을 제어해야 합니다. 이러한 요구를 충족하기 위해 앱 서비스에 사용할 수 있는 몇 가지 기능이 있습니다. 문제는 주어진 문제를 해결하기 위해 어떤 기능을 사용해야 하는지 아는 것입니다. 이 문서는 고객이 몇 가지 예제 사용 사례를 기반으로 사용해야 하는 기능을 결정하는 데 도움을 주기 위한 것입니다.

Azure 앱 서비스에는 두 가지 기본 배포 유형이 있습니다. 무료, 공유, 기본, 표준, 프리미엄 및 Premiumv2 가격 SCO에서 앱 서비스 계획을 호스팅하는 다중 테넌트 공용 서비스가 있습니다. 그런 다음 Azure 가상 네트워크(VNet)에서 격리된 SKU 앱 서비스 계획을 직접 호스팅하는 단일 테넌트 앱 서비스 환경(ASE)이 있습니다. 사용하는 기능은 다중 테넌트 서비스 또는 ASE에 있는 경우에 따라 달라집니다. 

## <a name="multi-tenant-app-service-networking-features"></a>다중 테넌트 앱 서비스 네트워킹 기능 

Azure 앱 서비스는 분산 시스템입니다. 들어오는 HTTP/HTTPS 요청을 처리하는 역할을 프런트 엔드라고 합니다. 고객 워크로드를 호스트하는 역할을 작업자라고 합니다. 앱 서비스 배포의 모든 역할은 다중 테넌트 네트워크에 존재합니다. 동일한 앱 서비스 규모 단위에 여러 고객이 있기 때문에 앱 서비스 네트워크를 네트워크에 직접 연결할 수 없습니다. 네트워크를 연결하는 대신 응용 프로그램 통신의 다양한 측면을 처리하는 기능이 필요합니다. 앱에 대한 요청을 처리하는 기능은 앱에서 전화를 걸 때 문제를 해결하는 데 사용할 수 없습니다. 마찬가지로 앱에서 걸려온 호출문제를 해결하는 기능은 앱에 대한 문제를 해결하는 데 사용할 수 없습니다.  

| 인바운드 기능 | 아웃바운드 기능 |
|---------------------|-------------------|
| 앱 할당 주소 | 하이브리드 연결 |
| 액세스 제한 | 게이트웨이 필수 VNet 통합 |
| 서비스 엔드포인트 | VNet 통합 |

달리 명시되지 않는 한 모든 기능을 함께 사용할 수 있습니다. 다양한 문제를 해결하기 위해 기능을 혼합 할 수 있습니다.

## <a name="use-case-and-features"></a>사용 사례 및 기능

지정된 사용 사례의 경우 문제를 해결하는 몇 가지 방법이 있을 수 있습니다.  사용하려는 올바른 기능은 사용 사례 자체 이상의 이유로 인해 발생하기도 합니다. 다음 인바운드 사용 사례는 앱 서비스 네트워킹 기능을 사용하여 앱으로 가는 트래픽 제어와 관련된 문제를 해결하는 방법을 제안합니다. 
 
| 인바운드 사용 사례 | 기능 |
|---------------------|-------------------|
| 앱에 대한 IP 기반 SSL 요구 사항 지원 | 앱 할당된 주소 |
| 앱에 대한 공유되지 않는 전용 인바운드 주소 | 앱 할당된 주소 |
| 잘 정의된 주소 집합에서 앱에 대한 액세스 제한 | 액세스 제한 |
| VNet의 리소스에서 내 앱에 대한 액세스 제한 | 서비스 엔드포인트 </br> ILB ASE </br> 프라이빗 엔드포인트(미리 보기) |
| 내 VNet에서 개인 IP에 내 응용 프로그램을 노출 | ILB ASE </br> 서비스 끝점이 있는 애플리케이션 게이트웨이에서 인바운드를 위한 프라이빗 IP </br> 서비스 끝점(미리 보기) |
| WAF로 내 앱 보호 | 애플리케이션 게이트웨이 + ILB ASE </br> 서비스 엔드포인트가 있는 애플리케이션 게이트웨이 </br> 액세스 제한이 있는 Azure 정문 |
| 다른 지역의 내 앱에 대한 트래픽 로드 균형 | 액세스 제한이 있는 Azure 정문 | 
| 동일한 리전의 로드 밸런스 트래픽 | [서비스 엔드포인트가 있는 애플리케이션 게이트웨이][appgwserviceendpoints] | 

다음 아웃바운드 사용 사례는 앱 서비스 네트워킹 기능을 사용하여 앱의 아웃바운드 액세스 요구 사항을 해결하는 방법을 제안합니다. 

| 아웃바운드 사용 사례 | 기능 |
|---------------------|-------------------|
| 동일한 리전의 Azure 가상 네트워크의 리소스에 액세스 | VNet 통합 </br> ASE |
| 다른 지역의 Azure 가상 네트워크의 리소스에 액세스 | 게이트웨이 필수 VNet 통합 </br> ASE 및 VNet 피어링 |
| 서비스 끝점으로 보호된 리소스 에 액세스 | VNet 통합 </br> ASE |
| Azure에 연결되지 않은 개인 네트워크의 리소스에 액세스 | 하이브리드 연결 |
| 익스프레스루트 회로 전반에 걸쳐 리소스 액세스 | VNet 통합 </br> ASE | 
| 웹 앱에서 아웃바운드 트래픽 보호 | VNet 통합 및 네트워크 보안 그룹 </br> ASE | 
| 웹 앱에서 아웃바운드 트래픽 라우팅 | VNet 통합 및 경로 테이블 </br> ASE | 


### <a name="default-networking-behavior"></a>기본 네트워킹 동작

Azure 앱 서비스 규모 단위는 각 배포의 많은 고객을 지원합니다. 무료 및 공유 SKU 계획은 다중 테넌트 작업자에 대한 고객 워크로드를 호스팅합니다. 기본 및 위의 계획은 하나의 앱 서비스 계획(ASP)에만 전용인 고객 워크로드를 호스트합니다. 표준 앱 서비스 계획이 있는 경우 해당 계획의 모든 앱이 동일한 작업자에서 실행됩니다. 작업자를 확장하면 해당 ASP의 모든 앱이 ASP의 각 인스턴스에 대해 새 작업자에 복제됩니다. Premiumv2에 사용되는 작업자는 다른 계획에 사용되는 작업자와 다릅니다. 각 앱 서비스 배포에는 해당 앱 서비스 배포의 앱에 대한 모든 인바운드 트래픽에 사용되는 하나의 IP 주소가 있습니다. 그러나 아웃바운드 통화를 하는 데 사용되는 주소는 4~11개에 이릅니다. 이러한 주소는 해당 앱 서비스 배포의 모든 앱에서 공유됩니다. 아웃바운드 주소는 작업자 유형에 따라 다릅니다. 즉, 무료, 공유, 기본, 표준 및 프리미엄 ASP에서 사용하는 주소가 Premiumv2 ASP의 아웃바운드 호출에 사용되는 주소와 다릅니다. 앱의 속성을 보면 앱에서 사용되는 인바운드 및 아웃바운드 주소를 볼 수 있습니다. IP ACL을 사용하여 종속성을 잠그어야 하는 경우 가능한 Outbound주소(Outbound주소)를 사용합니다. 

![앱 속성](media/networking-features/app-properties.png)

앱 서비스에는 서비스를 관리하는 데 사용되는 여러 끝점이 있습니다.  이러한 주소는 별도의 문서에 게시되며 AppServiceManagement IP 서비스 태그에도 게시됩니다. AppServiceManagement 태그는 이러한 트래픽을 허용해야 하는 ASE(앱 서비스 환경)에서만 사용됩니다. 앱 서비스 인바운드 주소는 AppService IP 서비스 태그에서 추적됩니다. 앱 서비스에서 사용하는 아웃바운드 주소를 포함하는 IP 서비스 태그가 없습니다. 

![앱 서비스 인바운드 및 아웃바운드 다이어그램](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>앱 할당 주소 

앱에 할당된 주소 기능은 IP 기반 SSL 기능의 오프슈트이며 앱에 SSL을 설정하여 액세스합니다. 이 기능은 IP 기반 SSL 호출에 사용할 수 있지만 앱에 주소만 있는 주소를 제공하는 데도 사용할 수 있습니다. 

![앱 할당 주소 다이어그램](media/networking-features/app-assigned-address.png)

앱할당주소를 사용하는 경우에도 트래픽은 앱 서비스 규모 단위로 들어오는 모든 트래픽을 처리하는 동일한 프런트 엔드 역할을 거칩니다. 그러나 앱에 할당된 주소는 앱에서만 사용됩니다. 이 기능의 사용 사례는 다음과 같습니다.

* 앱에 대한 IP 기반 SSL 요구 사항 지원
* 다른 사람과 공유되지 않는 앱전용 주소 설정

[Azure 앱 서비스에서 TLS/SSL 인증서 추가에][appassignedaddress]대한 자습서를 사용하여 앱에서 주소를 설정하는 방법을 알아볼 수 있습니다. 

### <a name="access-restrictions"></a>액세스 제한 

액세스 제한 기능을 사용하면 원본 IP 주소를 기반으로 **인바운드** 요청을 필터링할 수 있습니다. 필터링 작업은 앱이 실행 중인 작업자 역할에서 업스트림인 프런트 엔드 역할에서 수행됩니다. 프런트 엔드 역할은 작업자의 업스트림이므로 액세스 제한 기능은 앱에 대한 네트워크 수준 보호기능으로 간주될 수 있습니다. 이 기능을 사용하면 우선 순위순서로 평가되는 허용 및 거부 주소 블록 목록을 작성할 수 있습니다. Azure 네트워킹에 있는 NSG(네트워크 보안 그룹) 기능과 유사합니다.  ASE 또는 다중 테넌트 서비스에서 이 기능을 사용할 수 있습니다. ILB ASE와 함께 사용하면 개인 주소 블록에서 액세스를 제한할 수 있습니다.

![액세스 제한](media/networking-features/access-restrictions.png)

액세스 제한 기능은 앱에 도달하는 데 사용할 수 있는 IP 주소를 제한하려는 시나리오에서 유용합니다. 이 기능의 사용 사례는 다음과 같습니다.

* 잘 정의된 주소 집합에서 앱에 대한 액세스 제한 
* Azure 정문과 같은 로드 밸런싱 서비스를 통해 들어오는 액세스를 제한합니다. Azure Front Door로 의인 인바운드 트래픽을 잠그려면 147.243.0.0/16 및 2a01:111:2050:/44의 트래픽을 허용하는 규칙을 만듭니다. 

![현관 문이 있는 액세스 제한](media/networking-features/access-restrictions-afd.png)

Azure 가상 네트워크(VNet)의 리소스에서만 액세스할 수 있도록 앱에 대한 액세스를 잠그려면 원본이 무엇이든 간에 정적 공용 주소가 필요합니다. 리소스에 공용 주소가 없는 경우 서비스 끝점 기능을 대신 사용해야 합니다. [액세스 제한 구성에][iprestrictions]대한 자습서에서 이 기능을 활성화하는 방법에 대해 알아봅니다.

### <a name="service-endpoints"></a>서비스 엔드포인트

서비스 끝점을 사용하면 원본 주소가 선택한 서브넷 집합에서 오도록 앱에 대한 **인바운드** 액세스를 잠글 수 있습니다. 이 기능은 IP 액세스 제한과 함께 작동합니다. 서비스 끝점은 IP 액세스 제한과 동일한 사용자 환경에서 설정됩니다. 공용 주소와 VNet의 서브넷이 포함된 액세스 규칙의 허용/거부 목록을 작성할 수 있습니다. 이 기능은 다음과 같은 시나리오를 지원합니다.

![서비스 엔드포인트](media/networking-features/service-endpoints.png)

* 앱에 대한 인바운드 트래픽을 잠그기 위해 앱으로 응용 프로그램 게이트웨이 설정
* 앱에 대한 액세스를 VNet의 리소스로 제한합니다. 여기에는 VM, ASA또는 VNet 통합을 사용하는 다른 앱도 포함될 수 있습니다. 

![애플리케이션 게이트웨이를 통한 서비스 엔드포인트](media/networking-features/service-endpoints-appgw.png)

[서비스 엔드포인트 액세스 제한][serviceendpoints] 구성 에 대한 자습서에서 앱으로 서비스 끝점 구성에 대해 자세히 알아볼 수 있습니다.

### <a name="private-endpoint-preview"></a>프라이빗 엔드포인트(미리 보기)

프라이빗 엔드포인트는 Azure Private Link를 통해 개인적으로 안전하게 웹 앱에 연결하는 네트워크 인터페이스입니다. 프라이빗 엔드포인트는 VNet의 개인 IP 주소를 사용하여 웹 앱을 VNet으로 효과적으로 가져올 수 있습니다. 이 기능은 웹 앱으로의 **인바운드** 흐름에만 해당됩니다.
[Azure 웹 앱에 개인 끝점 사용(미리 보기)][privateendpoints]
 
### <a name="hybrid-connections"></a>하이브리드 연결

앱 서비스 하이브리드 연결을 사용하면 앱에서 지정된 TCP 끝점에 **아웃바운드** 호출을 수행할 수 있습니다. 끝점은 온-프레미스, VNet 또는 포트 443에서 Azure로의 아웃바운드 트래픽을 허용하는 모든 위치에 있을 수 있습니다. 이 기능을 사용하려면 Windows Server 2012 또는 최신 호스트에 HCM(하이브리드 연결 관리자)이라는 릴레이 에이전트를 설치해야 합니다. HCM은 포트 443에서 Azure 릴레이에 도달할 수 있어야 합니다. HCM은 포털의 앱 서비스 하이브리드 연결 UI에서 다운로드할 수 있습니다. 

![하이브리드 연결 네트워크 흐름](media/networking-features/hybrid-connections.png)

앱 서비스 하이브리드 연결 기능은 Azure 릴레이 하이브리드 연결 기능을 기반으로 합니다. App Service는 앱에서 TCP 호스트 및 포트로 아웃바운드 호출만 수행하는 기능을 특수한 형태로 사용합니다. 이 호스트 및 포트는 HCM이 설치된 호스트에서만 해결해야 합니다. 앱 서비스에서 앱이 하이브리드 연결에 정의된 호스트 및 포트에 대한 DNS 조회를 수행하면 트래픽이 자동으로 리디렉션되어 하이브리드 연결을 통과하고 하이브리드 연결 관리자밖으로 나갑니다. 하이브리드 연결에 대해 자세히 알아보려면 [앱 서비스 하이브리드 연결에][hybridconn] 대한 설명서를 참조하십시오.

이 기능은 일반적으로 다음과 같은 데 사용됩니다.

* VPN 또는 ExpressRoute를 사용하여 Azure에 연결되지 않은 개인 네트워크의 리소스에 액세스
* 지원 데이터베이스를 이동할 필요 없이 온-프레미스 앱을 앱 서비스로 리프트 및 이동 지원  
* 하이브리드 연결당 단일 호스트 및 포트에 안전하게 액세스할 수 있습니다. 대부분의 네트워킹 기능은 네트워크에 대한 개방형 액세스 기능을 갖추고 있으며 하이브리드 연결을 사용하면 도달할 수 있는 단일 호스트 및 포트만 사용할 수 있습니다.
* 다른 아웃바운드 연결 방법으로 다루지 않는 시나리오 적용
* 앱이 온-프레미스 리소스를 쉽게 활용할 수 있는 앱 서비스 개발 수행 

이 기능을 사용하면 인바운드 방화벽 구멍 없이 온-프레미스 리소스에 액세스할 수 있으므로 개발자에게 인기가 있습니다. 다른 아웃바운드 앱 서비스 네트워킹 기능은 매우 Azure 가상 네트워킹과 관련이 있습니다. 하이브리드 연결은 VNet을 통과하는 데 종속성이 없으며 보다 다양한 네트워킹 요구에 사용할 수 있습니다. 앱 서비스 하이브리드 연결 기능은 위에서 무엇을 하고 있는지 신경 쓰지 않거나 알지 못한다는 점에 유의해야 합니다. 즉, 메인프레임에서 데이터베이스, 웹 서비스 또는 임의의 TCP 소켓에 액세스하는 데 사용할 수 있습니다. 이 기능은 기본적으로 TCP 패킷을 터널합니다. 

하이브리드 연결은 개발에 대 한 인기 있는 동안, 그것은 또한 수많은 생산 응용 프로그램에서 사용. 웹 서비스 나 데이터베이스에 액세스하는 데 적합하지만 많은 연결을 만드는 데 적합하지 않은 경우. 

### <a name="gateway-required-vnet-integration"></a>게이트웨이 필수 VNet 통합 

게이트웨이 필수 앱 서비스 VNet 통합 기능을 사용하면 앱에서 Azure 가상 네트워크에 **아웃바운드** 요청을 할 수 있습니다. 이 기능은 앱이 실행 중인 호스트를 지점 간 VPN을 사용하여 VNet의 가상 네트워크 게이트웨이에 연결하여 작동합니다. 기능을 구성하면 앱은 각 인스턴스에 할당된 지점 간 주소 중 하나를 가져옵니다. 이 기능을 사용하면 모든 리전에서 클래식 또는 리소스 관리자 VNet의 리소스에 액세스할 수 있습니다. 

![게이트웨이 필수 VNet 통합](media/networking-features/gw-vnet-integration.png)

이 기능은 다른 VNet의 리소스에 액세스하는 문제를 해결하며 VNet을 통해 다른 VNet 또는 온-프레미스에 연결하는 데사용할 수도 있습니다. ExpressRoute 연결 VNet에서 작동하지 않지만 사이트 간 VPN 연결 네트워크에서도 작동합니다. ASE가 이미 VNet에 있으므로 일반적으로 ASE(앱 서비스 환경)의 앱에서 이 기능을 사용하는 것은 부적절합니다. 이 기능이 해결하는 사용 사례는 다음과 같습니다.

* Azure 가상 네트워크의 개인 IP에서 리소스 액세스 
* 사이트 간 VPN이 있는 경우 온-프레미스 리소스 액세스 
* 피어링된 VNet에서 리소스 액세스 

이 기능을 사용하도록 설정하면 앱에서 대상 VNet이 구성된 DNS 서버를 사용합니다. 이 기능에 대한 자세한 내용은 [앱 서비스 VNet 통합에][vnetintegrationp2s]대한 설명서에서 확인할 수 있습니다. 

### <a name="vnet-integration"></a>VNet 통합

게이트웨이 필수 VNet 통합 기능은 매우 유용하지만 ExpressRoute에서 리소스에 액세스하는 데 는 여전히 해결되지 않습니다. ExpressRoute 연결을 통해 도달해야 하는 것 외에도 앱이 서비스 엔드포인트 보안 서비스에 전화를 걸 수 있어야 합니다. 이러한 추가 요구 사항을 모두 해결하기 위해 다른 VNet 통합 기능이 추가되었습니다. 새로운 VNet 통합 기능을 사용하면 앱의 백엔드를 동일한 리전의 리소스 관리자 VNet의 서브넷에 배치할 수 있습니다. 이 기능은 이미 VNet에 있는 앱 서비스 환경에서 사용할 수 없습니다. 이 기능을 사용하면 다음을 수행할 수 있습니다.

* 동일한 리전의 리소스 관리자 VNet의 리소스 액세스
* 서비스 끝점으로 보호되는 리소스 액세스 
* ExpressRoute 또는 VPN 연결을 통해 액세스할 수 있는 리소스 액세스
* 모든 아웃바운드 트래픽 보안 
* 모든 아웃바운드 트래픽을 강제로 터널링합니다. 

![VNet 통합](media/networking-features/vnet-integration.png)

이 기능에 대한 자세한 내용은 앱 [서비스 VNet 통합의][vnetintegration]문서를 참조하십시오.

## <a name="app-service-environment"></a>App Service 환경 

ASE(앱 서비스 환경)는 VNet에서 실행되는 Azure 앱 서비스의 단일 테넌트 배포입니다. ASE를 사용하면 다음과 같은 사용 사례를 사용할 수 있습니다.

* VNet의 리소스에 액세스
* 익스프레스루트 에서 리소스에 액세스
* VNet에 개인 주소로 앱 노출 
* 서비스 엔드포인트 전반의 리소스 액세스 

ASE를 사용하면 ASE가 이미 VNet에 있으므로 VNet 통합 또는 서비스 끝점과 같은 기능을 사용할 필요가 없습니다. 서비스 끝점을 통해 SQL 또는 Storage와 같은 리소스에 액세스하려면 ASE 서브넷에서 서비스 끝점을 사용하도록 설정합니다. VNet의 리소스에 액세스하려면 추가 구성이 필요하지 않습니다.  ExpressRoute에서 리소스에 액세스하려는 경우 이미 VNet에 있으며 ASE 또는 그 안에 있는 앱에 대해 아무것도 구성할 필요가 없습니다. 

ILB ASE의 앱은 개인 IP 주소에 노출될 수 있으므로 WAF 장치를 쉽게 추가하여 인터넷에 원하는 앱만 노출하고 나머지는 안전하게 유지할 수 있습니다. 다중 계층 응용 프로그램을 쉽게 개발할 수 있습니다. 

ASE에서 온 다중 테넌트 서비스에서 아직 불가능한 몇 가지 사항이 있습니다. 여기에는 다음과 같은 것들이 포함됩니다.

* 개인 IP 주소에 앱 노출
* 앱의 일부가 아닌 네트워크 컨트롤로 모든 아웃바운드 트래픽 보호 
* 단일 테넌트 서비스에서 앱 호스트 
* 다중 테넌트 서비스에서 가능한 것보다 더 많은 인스턴스로 확장 
* 개인 CA 보안 끝점을 사용하여 앱에서 사용할 개인 CA 클라이언트 인증서 로드 
* 앱 수준에서 비활성화할 수 없는 기능 없이 시스템에서 호스팅되는 모든 앱에서 TLS 1.1 강제 적용 
* 고객과 공유되지 않는 ASE의 모든 앱에 대해 전용 아웃바운드 주소를 제공합니다. 

![VNet의 ASE](media/networking-features/app-service-environment.png)

ASE는 격리되고 전용된 앱 호스팅에 대한 최고의 스토리를 제공하지만 몇 가지 관리 과제가 있습니다. 운영 ASE를 사용하기 전에 고려해야 할 몇 가지 사항은 다음과 같습니다.
 
 * ASE는 VNet 내에서 실행되지만 VNet 외부에 종속성이 있습니다. 이러한 종속성은 허용되어야 합니다. [앱 서비스 환경에 대한 네트워킹 고려 사항에 대해][networkinfo] 자세히 알아보기
 * ASE는 다중 테넌트 서비스처럼 즉시 확장되지 않습니다. 적극적인 스케일링보다는 확장 요구 사항을 예측해야 합니다. 
 * ASE는 연관된 선행 비용이 더 높습니다. ASE를 최대한 활용하려면 작은 노력에 사용하기보다는 많은 워크로드를 하나의 ASE에 넣을 계획이어야 합니다.
 * ASE의 앱은 ASE의 일부 앱에 대한 액세스를 제한할 수 없으며 다른 앱은 제한할 수 없습니다.
 * ASE는 서브넷에 있으며 모든 네트워킹 규칙은 해당 ASE를 오가는 모든 트래픽에 적용됩니다. 하나의 앱에 만 인바운드 트래픽 규칙을 할당하려면 액세스 제한을 사용합니다. 

## <a name="combining-features"></a>기능 결합 

다중 테넌트 서비스에 대해 언급된 기능을 함께 사용하여 보다 정교한 사용 사례를 해결할 수 있습니다. 일반적인 사용 사례 중 두 가지는 여기에 설명되어 있지만 예에 불과합니다. 다양한 기능이 수행하는 작업을 이해하면 거의 모든 시스템 아키텍처 요구 사항을 해결할 수 있습니다.

### <a name="inject-app-into-a-vnet"></a>VNet에 앱 삽입

일반적인 요청은 앱을 VNet에 넣는 방법에 대한 것입니다. 앱을 VNet에 넣으면 앱의 인바운드 및 아웃바운드 끝점이 VNet 내에 있음을 의미합니다. ASE는 이 문제를 해결하는 최상의 솔루션을 제공하지만 기능을 결합하여 다중 테넌트 서비스에서 필요한 대부분의 작업을 얻을 수 있습니다. 예를 들어 다음과 같은 방법으로 개인 인바운드 및 아웃바운드 주소가 있는 인트라넷 응용 프로그램만 호스트할 수 있습니다.

* 개인 인바운드 및 아웃바운드 주소가 있는 응용 프로그램 게이트웨이 만들기
* 서비스 끝점을 사용하여 앱으로의 인바운드 트래픽 보호 
* 앱의 백엔드가 VNet에 있도록 새 VNet 통합을 사용하십시오. 

이 배포 스타일은 인터넷으로의 아웃바운드 트래픽에 대한 전용 주소를 제공하거나 앱의 모든 아웃바운드 트래픽을 잠글 수 있는 기능을 제공하지 않습니다.  이 배포 스타일은 ASE를 사용하면 얻을 수 있는 많은 것을 제공합니다. 

### <a name="create-multi-tier-applications"></a>다중 계층 응용 프로그램 만들기

다중 계층 응용 프로그램은 API 백 엔드 앱이 프런트 엔드 계층에서만 액세스할 수 있는 응용 프로그램입니다. 다중 계층 응용 프로그램을 만들려면 다음을 수행할 수 있습니다.

* VNet 통합을 사용하여 프런트 엔드 웹 앱의 백엔드를 VNet의 서브넷과 연결
* 서비스 끝점을 사용하여 API 앱으로의 인바운드 트래픽을 프런트 엔드 웹 앱에서 사용하는 서브넷에서만 보호합니다.

![다중 계층 앱](media/networking-features/multi-tier-app.png)

서브넷을 사용하여 API 앱의 다른 프런트 엔드 앱 및 서비스 끝점에서 VNet 통합을 사용하여 여러 프런트 엔드 앱이 동일한 API 앱을 사용하도록 할 수 있습니다.  

<!--Links-->
[appassignedaddress]: https://docs.microsoft.com/azure/app-service/configure-ssl-certificate
[iprestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[serviceendpoints]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[hybridconn]: https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections
[vnetintegrationp2s]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[vnetintegration]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[networkinfo]: https://docs.microsoft.com/azure/app-service/environment/network-info
[appgwserviceendpoints]: https://docs.microsoft.com/azure/app-service/networking/app-gateway-with-service-endpoints
[privateendpoints]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
