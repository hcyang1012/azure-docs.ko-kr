---
title: Azure 응용 프로그램 제공 전제 조건 | Azure 마켓플레이스
description: Azure Marketplace에서 Azure 애플리케이션 제품을 게시하기 위한 필수 구성 요소입니다.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: dsindona
ms.openlocfilehash: b927404758dc59419fb3c5e638ac32758d534681
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281751"
---
# <a name="azure-application-prerequisites"></a>Azure 애플리케이션 필수 구성 요소

이 문서에서는 Azure Marketplace에 관리되는 애플리케이션 제품을 게시하기 위한 기술 및 비즈니스 필수 구성 요소를 설명합니다.  아직 수행하지 않은 경우 다음 정보 소스를 검토하십시오.
- SKU 유형에 따라 [Azure 응용 프로그램: 솔루션 템플릿 제공 게시 가이드](../../marketplace-solution-templates.md) 또는 Azure 응용 [프로그램: 관리되는 응용 프로그램 제안 게시 가이드](../../marketplace-managed-apps.md)
- [Azure 마켓플레이스](https://channel9.msdn.com/Events/Build/2018/BRK3603) 비디오에 대한 솔루션 템플릿 및 관리되는 응용 프로그램 빌드


## <a name="technical-requirements"></a>기술적인 요구 사항

기술 요구 사항에는 Azure Resource Manager 템플릿이 포함됩니다.

*   자세한 내용은 [Azure Resource Manager 템플릿의 구조 및 구문 이해](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)를 참조하세요. 이 문서에서는 Azure Resource Manager 템플릿의 구조에 대해 설명합니다. 여기서는 템플릿의 다른 섹션 및 해당 섹션에서 사용할 수 있는 속성을 보여 줍니다. 템플릿은 배포에 대한 값을 생성하는 데 사용할 수 있는 식과 JSON으로 구성됩니다. 
* Azure 빠른 시작 템플릿<br> 자세한 내용은 다음을 참조하세요.

  * [Azure 빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/). 커뮤니티가 참여한 템플릿을 사용하여 Azure Resource Manager를 통해 Azure 리소스를 배포하여 생산성을 높일 수 있습니다. Azure Resource Manager를 사용하면 선언적 템플릿을 통해 애플리케이션을 프로비전할 수 있습니다. 단일 템플릿에서 여러 서비스를 해당 종속성과 함께 배포할 수 있습니다. 동일한 템플릿을 사용하여 애플리케이션 수명 주기의 각 단계 중에 애플리케이션을 반복해서 배포합니다.
  * [GitHub: Azure 리소스 관리자 빠른 시작 템플릿.](https://github.com/azure/azure-quickstart-templates) 이 리포지토리는 커뮤니티가 참여한 현재 사용 가능한 모든 Azure Resource Manager 템플릿을 포함합니다. 검색 가능 템플릿 인덱스는 https://azure.microsoft.com/documentation/templates/에서 유지 관리됩니다.
* UI 정의 만들기<br>
자세한 내용은 [관리되는 응용 프로그램에 대한 Azure 포털 사용자 인터페이스 만들기](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)를 참조하십시오. 이 문서에서는 createUiDefinition.json 파일의 핵심 개념을 소개합니다. Azure Portal에서 이 파일을 사용하여 관리되는 애플리케이션을 만들기 위한 사용자 인터페이스를 생성합니다.


## <a name="business-requirements"></a>비즈니스 요구 사항

비즈니스 요구 사항에는 다음과 같은 절차, 계약 및 법적 의무가 포함됩니다.

* 등록된 Cloud Marketplace 게시자여야 합니다. 등록하지 않은 경우 [클라우드 마켓플레이스 게시자되기](https://docs.microsoft.com/azure/marketplace/become-publisher
)문서의 단계를 따르십시오.

>[!NOTE]
>동일한 Microsoft 개발자 센터 등록 계정을 사용하여 Cloud 파트너 포털에 로그온해야 합니다. Azure Marketplace 제품에 대해 Microsoft 계정이 하나만 있으면 됩니다. 이 계정은 개별 서비스 또는 혜택에 만국해서는 안 됩니다.

* 회사(또는 자회사)는 Azure 마켓플레이스에서 지원하는 국가/지역 판매에 있어야 합니다. 이러한 국가/지역의 현재 목록은 [Microsoft Azure 마켓플레이스 참여 정책을](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)참조하십시오.
* Azure Marketplace에서 지원하는 청구 모델과 호환되는 방식으로 제품에 라이선스가 부여되어야 합니다. 자세한 내용은 Azure 마켓플레이스의 [청구 옵션을](https://docs.microsoft.com/azure/marketplace/marketplace-commercial-transaction-capabilities-and-considerations) 참조하세요.
* 상업적이고 합리적인 방식으로 고객이 이용할 수 있는 기술 지원을 담당합니다. 이러한 지원은 체험, 유료 또는 커뮤니티 접근 방식을 통해 제공될 수 있습니다.
* 소프트웨어 및 타사 소프트웨어 종속성에 대해 사용 허가를 받을 책임이 있습니다.
* Azure Marketplace 및 Azure Portal에 등록할 제안에 대한 조건을 충족하는 콘텐츠를 제공해야 합니다.
* Microsoft Azure Marketplace 참가 정책 및 게시자 계약의 약관에 동의해야 합니다.
* Microsoft Azure 웹 사이트 사용 약관, Microsoft 개인정보처리방침 및 Microsoft Azure Certified 프로그램 계약을 준수해야 합니다.


## <a name="publishing-requirements"></a>게시 요구 사항

새 Azure 애플리케이션 제품을 게시하려면 다음 필수 구성 요소를 충족해야 합니다.

* 메타데이터를 사용할 수 있도록 준비합니다. 이 메타데이터의 예를 보여 주는 목록은 다음과 같습니다(완전하지 않음).
  * 제목
  * 설명(HTML 형식)
  * 로고 이미지(PNG 형식) 및 고정 이미지 크기: 40 x 40 픽셀, 90 x 90 픽셀, 115 x 115 픽셀 및 255 x 115 픽셀.
* *이용 약관* 및 *개인 정보 보호 정책* 문서
* 응용 프로그램 문서
* 지원 연락처


## <a name="next-steps"></a>다음 단계

모든 요구 사항이 충족되면 [Azure 애플리케이션 제안을 만들](./cpp-create-offer.md) 준비가 됩니다. 
 
