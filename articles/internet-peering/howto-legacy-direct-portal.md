---
title: 포털을 사용하여 레거시 직접 피어링을 Azure 리소스로 변환
titleSuffix: Azure
description: 포털을 사용하여 레거시 직접 피어링을 Azure 리소스로 변환
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9900414d38bd597d08a80d15e908228c06ce06ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775057"
---
# <a name="convert-a-legacy-direct-peering-to-azure-resource-using-the-portal"></a>포털을 사용하여 레거시 직접 피어링을 Azure 리소스로 변환

이 문서에서는 포털을 사용하여 기존 레거시 직접 피어링을 Azure 리소스로 변환하는 방법을 설명합니다.

원하는 경우 [PowerShell](howto-legacy-direct-powershell.md)을 사용하여 이 가이드를 완료할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에
* 구성을 시작하기 전에 [필수 구성 조건](prerequisites.md) 및 직접 [피어링 연습을](walkthrough-direct-all.md) 검토합니다.


## <a name="convert-legacy-direct-peering-to-azure-resource"></a>레거시 직접 피어링을 Azure 리소스로 변환

### <a name="sign-in-to-portal-and-select-your-subscription"></a>포털에 로그인하고 구독을 선택합니다.
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-direct-peering"></a><a name=create></a>레거시 직접 피어링 변환

**피어링** 리소스를 사용하여 레거시 피어링 연결을 변환할 수 있습니다.

#### <a name="launch-resource-and-configure-basic-settings"></a>리소스 시작 및 기본 설정 구성
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>연결 구성 및 제출
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>직접 피어링 확인
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>추가 리소스

자세한 내용은 [인터넷 피어링 자주 묻는 질문(FAQ)을 방문하십시오.](faqs.md)

## <a name="next-steps"></a>다음 단계

* [포털을 사용하여 직접 피어링을 만들거나 수정합니다.](howto-direct-portal.md)
