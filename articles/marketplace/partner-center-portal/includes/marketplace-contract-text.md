---
title: 포함 파일
description: Microsoft 상용 마켓플레이스 표준 계약에 대한 텍스트 파일 포함
documentationcenter: partner-center-commercial-marketplace
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
ms.date: 01/22/2020
ms.author: dsindona
ms.custom: include file
ms.openlocfilehash: 9b99876b66826cc5a66cba686a7d6a5243b47b98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277225"
---
Microsoft는 상용 마켓플레이스에 대한 표준 계약 템플릿을 제공합니다.

- **Microsoft 상용 시장에 대한 표준 계약을 사용합니까?**

고객을 위한 조달 프로세스를 간소화하고 소프트웨어 공급업체의 법적 복잡성을 줄이기 위해 Microsoft는 시장에서 트랜잭션을 용이하게 하기 위해 Microsoft 상용 시장에 대한 표준 계약을 제공합니다. 상업용 마켓플레이스 퍼블리셔는 사용자 지정 이용 약관을 만드는 대신 고객이 한 번만 수의사와 수락을 할 수 있는 표준 계약에 따라 소프트웨어를 제공하도록 선택할 수 있습니다. 표준 계약은 여기에서 찾을 https://go.microsoft.com/fwlink/?linkid=2041178수 있습니다.

"상용 마켓플레이스에 표준 계약 사용" 확인란을 선택하여 사용자 지정 이용 약관을 제공하는 대신 표준 계약을 사용하도록 선택할 수 있습니다.

![표준 계약 확인란 사용](./media/use-standard-contract.png)

> [!NOTE]
> Microsoft 상용 마켓플레이스의 표준 계약을 사용하여 오퍼를 게시하면 사용자 지정 이용 약관을 사용할 수 없습니다. "또는" 시나리오입니다. 귀하는 표준 계약 **또는** 사용자 고유의 이용 약관에 따라 솔루션을 제공합니다. 표준 계약 약관을 수정하려면 표준 계약 개정을 통해 수정할 수 있습니다.

**표준 계약 개정**

표준 계약 개정을 통해 게시자는 단순함을 위해 표준 계약 조건을 선택하고 제품 또는 비즈니스에 대한 조건을 사용자 지정할 수 있습니다. 고객은 Microsoft 표준 계약을 이미 검토하고 수락한 경우에만 계약 수정을 검토하면 됩니다.

상업용 마켓플레이스 퍼블리셔가 사용할 수 있는 두 가지 종류의 수정안이 있습니다.

- 보편적 개정: 이러한 개정안은 모든 고객을 위한 표준 계약에 보편적으로 적용됩니다. 보편적 인 개정은 구매 흐름에서 제안의 모든 고객에게 표시됩니다. 고객은 제품을 사용하기 전에 표준 계약 약관 및 개정안에 동의해야 합니다.
- 사용자 지정 개정: 이러한 개정은 Azure 테넌트 아이디를 통해서만 특정 고객을 대상으로 하는 표준 계약에 대한 특별 개정입니다. 게시자는 타겟팅할 테넌트를 선택할 수 있습니다. 테넌트의 고객만 오퍼의 구매 흐름에서 사용자 지정 수정 약관이 제공됩니다.  고객은 귀하의 제안을 사용하기 전에 표준 계약 및 개정안의 약관에 동의해야 합니다.

>[!NOTE]
> 이 두 가지 유형의 개정은 서로 겹치게 됩니다. 사용자 지정 개정 대상 고객은 구매 시 표준 계약에 대한 보편적 인 개정을 받게됩니다.

**Microsoft의 상용 시장에 대한 표준 계약에 대한 보편적 인 개정 조건**: 이 상자에 범용 개정 조건을 입력합니다. 오퍼당 단일 보편적 수정을 제공할 수 있습니다. 이 상자에 문자를 무제한으로 입력할 수 있습니다. 이러한 용어는 검색 및 구매 흐름 중에 AppSource, Azure 마켓플레이스 및/또는 Azure 포털의 고객에게 표시됩니다.

**Microsoft 의 상용 시장에 대한 표준 계약에 대한 사용자 지정 개정 조건**: 사용자 지정 개정 조건 **추가를**선택하여 시작합니다. 오퍼당 최대 10개의 맞춤 수정 조건을 제공할 수 있습니다.

- **사용자 지정 수정 약관**: 사용자 지정 수정 약관 상자에 사용자 지정 수정 용어를 입력합니다. 이 상자에 문자를 무제한으로 입력할 수 있습니다. 이러한 사용자 지정 용어에 대해 지정한 테넌트 아이디의 고객만 Azure Portal에서 오퍼의 구매 흐름에 사용자 지정 수정 조건이 표시됩니다.  
- **테넌트 아이디(필수):** 각 사용자 지정 수정안은 최대 20개의 테넌트 아이디를 대상으로 지정할 수 있습니다. 사용자 지정 수정 본인을 추가하는 경우 하나 이상의 테넌트 ID를 제공해야 합니다. 테넌트 ID는 Azure에서 고객을 식별합니다. 고객에게 이 ID를 요청할 수 있으며 Azure Active Directory > 속성에 > portal.azure.com 탐색하여 이 ID를 찾을 수 있습니다. 디렉터리 ID 값은 테넌트 ID(예: 50c464d3-4930-493c-963c-963c-1e951d15360e)입니다. 또한 [내 Microsoft Azure 및 Office 365 테넌트 ID는 무엇입니까에서](https://www.whatismytenantid.com)도메인 이름 URL을 사용하여 고객의 조직의 테넌트 ID를 조회할 수 있습니다.
- **설명(선택** 사항): 선택적으로 테넌트 ID에 대한 친숙한 설명을 제공하여 개정을 통해 타겟팅하는 고객을 식별하는 데 도움이 됩니다.

**사용 약관**

사용자 지정 이용 약관을 제공하려는 경우 이용 약관 필드에 입력하도록 선택할 수 있습니다. 이 필드에는 최대 10,000자의 텍스트를 입력할 수 있습니다. 이용 약관에 더 긴 설명이 필요한 경우 이용 약관을 찾을 수 있는 이 필드에 단일 URL 링크를 입력합니다. 고객에게 활성 링크로 표시됩니다.

고객은 제품을 사용해 받기 전에 이 약관에 동의해야 합니다.

다음 섹션으로 이동하기 전에 **저장해야** 합니다.
다음 섹션으로 이동하기 전에 **저장해야** 합니다.
