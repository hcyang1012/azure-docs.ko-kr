---
title: Azure 익스프레스루트 다이렉트 소개
description: 이 페이지에서는 익스프레스루트 다이렉트에 대한 개요를 제공합니다.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jaredro
ms.openlocfilehash: f26fdd75e0a6c6228d329a8b9be4743072ae54c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083595"
---
# <a name="about-expressroute-direct"></a>ExpressRoute Direct 정보

ExpressRoute Direct는 전 세계에 전략적으로 분산된 피어링 위치에서 Microsoft의 글로벌 네트워크에 직접 연결하는 기능을 제공합니다. ExpressRoute Direct는 이중 100Gbps 또는 10Gbps 연결을 제공하며, 이는 규모에 따라 활성/활성 연결을 지원합니다.

ExpressRoute Direct가 제공하는 주요 기능은 다음을 포함하지만 제한되지 않습니다.

* 스토리지 및 Cosmos DB와 같은 서비스로 대규모 데이터 수집
* 은행, 정부, 소매업체 등 규제가 적용되며 전용 격리 연결을 사용해야 하는 업계의 물리적 격리
* 비즈니스 단위에 따라 세부적으로 회로 배포 제어

## <a name="onboard-to-expressroute-direct"></a>익스프레스루트 다이렉트온보드

ExpressRoute Direct를 사용하기 전에 먼저 구독을 등록해야 합니다. 등록하려면 다음 세부 사항을 포함하여 구독 ID로 <ExpressRouteDirect@microsoft.com>에 이메일을 전송합니다.

* **ExpressRoute Direct**를 사용하여 수행하려는 시나리오
* 위치 기본 설정 - 모든 위치의 전체 목록은 [파트너 및 피어링 위치](expressroute-locations-providers.md) 참조
* 구현을 위한 타임라인
* 기타 궁금한 점이 있는 경우

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>서비스 공급자 및 ExpressRoute Direct를 사용하는 ExpressRoute

| **서비스 공급자를 사용하는 ExpressRoute** | **ExpressRoute Direct** | 
| --- | --- |
| 서비스 공급자를 활용하여 기존 인프라로 빠른 온보딩 및 연결 활성화 | 100Gbps/10Gbps 인프라 및 모든 계층의 전체 관리 필요
| 이더넷 및 MPLS를 포함하는 수백 개의 공급자와 통합 | 규제 산업 및 대규모 데이터 수집에 대한 직접/전용 용량 |
| 50Mbps - 10Gbps의 회로 SKU | 고객은 100Gbps 익스프레스루트 다이렉트에서 다음 회로 SUS의 조합을 선택할 수 있습니다. <ul><li>5Gbps</li><li>10Gbps</li><li>40Gbps</li><li>100Gbps</li></ul> 고객은 10Gbps 익스프레스루트 다이렉트에서 다음 회로 SUS의 조합을 선택할 수 있습니다.<ul><li>1Gbps</li><li>2Gbps</li><li>5Gbps</li><li>10Gbps</li></ul>
| 단일 테넌트에 최적화됨 | 여러 비즈니스 단위 및 여러 작업 환경을 갖춘 단일 테넌트에 최적화

## <a name="expressroute-direct-circuits"></a>ExpressRoute Direct 회로

Microsoft Azure ExpressRoute를 사용하면 연결 공급자에서 쉽게 처리된 프라이빗 연결을 통해 온-프레미스 네트워크를 Microsoft 클라우드로 확장할 수 있습니다. ExpressRoute를 사용하면 Microsoft Azure 및 Office 365와 같은 Microsoft 클라우드 서비스에 대한 연결을 설정할 수 있습니다.

각 피어링 위치는 Microsoft의 글로벌 네트워크에 대한 액세스 권한을 가지며 기본적으로 지리적 영역에 있는 모든 지역에 액세스할 수 있으며 프리미엄 회로를 사용하여 전 세계 모든 지역에 액세스할 수 있습니다.  

대부분의 시나리오에서 기능은 작동하기 위해 ExpressRoute 서비스 공급자를 활용하는 회로와 동일합니다. 추가 세분성 및 ExpressRoute Direct를 사용하여 제공되는 새로운 기능을 지원하기 위해 ExpressRoute Direct 회로에 존재하는 특정 키 기능이 있습니다.

## <a name="circuit-skus"></a>회로 SKU

ExpressRoute Direct는 Azure Storage 및 기타 빅 데이터 서비스에 대규모 데이터 수집 시나리오를 지원합니다. 100Gbps 익스프레스루트 다이렉트의 익스프레스루트 회로도 **40Gbps** 및 **100Gbps** 회로 SCO를 지원합니다. 실제 포트 쌍은 **100Gbps 또는 10Gbps에** 불과하며 여러 가상 회로를 가질 수 있습니다. 회로 크기:

| **100Gbps 익스프레스루트 다이렉트** | **10Gbps 익스프레스루트 다이렉트** | 
| --- | --- |
| **구독 대역폭**: 200Gbps | **구독 대역폭**: 20Gbps |
| <ul><li>5Gbps</li><li>10Gbps</li><li>40Gbps</li><li>100Gbps</li></ul> | <ul><li>1Gbps</li><li>2Gbps</li><li>5Gbps</li><li>10Gbps</li></ul>

## <a name="technical-requirements"></a>기술적인 요구 사항

* 마이크로 소프트 엔터프라이즈 에지 라우터 (MSEE) 인터페이스 :
    * 라우터 쌍에서만 듀얼 10 또는 100 기가비트 이더넷 포트
    * 단일 모드 LR 광섬유 연결
    * IPv4 및 IPv6
    * IP MTU 1500 바이트

* 스위치/라우터 계층 2/계층 3 연결:
    * 1 802.1Q(Dot1Q) 태그 또는 두 개의 태그 802.1Q(QinQ) 태그 캡슐화를 지원해야 합니다.
    * 이더타입 = 0x8100
    * 마이크로 소프트가 지정한 VLAN ID를 기반으로 외부 VLAN 태그 (STAG)를 추가해야합니다 - *QinQ에만 적용*
    * 포트 및 장치당 여러 BGP 세션(VLAN)을 지원해야 합니다.
    * IPv4 및 IPv6 연결. *IPv6의 경우 추가 하위 인터페이스가 생성되지 않습니다. IPv6 주소는 기존 하위 인터페이스에 추가됩니다.* 
    * 옵션: ExpressRoute 회로의 모든 프라이빗 피어링에서 기본적으로 구성된 [BFD(양방향 포워딩 감지)](https://docs.microsoft.com/azure/expressroute/expressroute-bfd) 지원

## <a name="vlan-tagging"></a>VLAN 태그 지정

ExpressRoute Direct는 QinQ 및 Dot1Q VLAN 태그 지정을 지원합니다.

* **QinQ VLAN 태그 지정**은 ExpressRoute 회로 기준당 격리된 라우팅 도메인을 허용합니다. Azure는 회로 생성 시 동적으로 S 태그를 할당하고 이는 변경할 수 없습니다. 회로의 각 피어링(프라이빗 및 Microsoft)은 VLAN으로 고유한 C 태그를 활용합니다. C 태그는 ExpressRoute Direct 포트의 회로에서 고유할 필요가 없습니다.

* **Dot1Q VLAN 태그 지정**은 각 ExpressRoute Direct 포트 쌍 기준당 단일 태그가 지정된 VLAN을 허용합니다. 피어링에 사용되는 C 태그는 ExpressRoute Direct 포트 쌍의 모든 회로 및 피어링에서 고유해야 합니다.

## <a name="workflow"></a>워크플로

[![워크플로](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

ExpressRoute Direct는 Microsoft 글로벌 네트워크에 대한 활성/활성 중복 연결과 동일한 엔터프라이즈급 SLA를 제공합니다. ExpressRoute 인프라는 중복되며, Microsoft 글로벌 네트워크로 연결은 중복되고 다양하며 고객 요구 사항에 적절하게 크기를 조정합니다. 

## <a name="next-steps"></a>다음 단계

[ExpressRoute Direct 구성](expressroute-howto-erdirect.md)
