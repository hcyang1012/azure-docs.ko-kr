---
title: '자습서: Azure CDN에 대한 WAF 정책 만들기 - Azure Portal'
description: 이 자습서에서는 Azure Portal을 사용하여 Azure CDN에 WAF(웹 애플리케이션 방화벽) 정책을 만드는 방법을 알아봅니다.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 03/18/2020
ms.author: victorh
ms.openlocfilehash: 738be1361bfbd944575abceb08781b241336f6e8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79472545"
---
# <a name="tutorial-create-a-waf-policy-on-azure-cdn-using-the-azure-portal"></a>자습서: Azure Portal을 사용하여 Azure CDN에 WAF 정책 만들기

이 자습서는 기본 Azure WAF(웹 애플리케이션 방화벽) 정책을 만들어서 Azure CDN(콘텐츠 배달 네트워크)의 엔드포인트에 적용하는 방법을 보여줍니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * WAF 정책 만들기
> * CDN 엔드포인트와 연결하기
> * WAF 규칙 구성

## <a name="prerequisites"></a>사전 요구 사항

다음 지침에 따라 Azure CDN 프로필 및 엔드포인트를 만듭니다. [빠른 시작: Azure CDN 프로필 및 엔드포인트 만들기](../../cdn/cdn-create-new-endpoint.md) 

## <a name="create-a-web-application-firewall-policy"></a>웹 애플리케이션 방화벽 정책 만들기

먼저, 포털을 사용하여 관리형 DRS(기본 규칙 집합)로 기본 WAF 정책을 만듭니다.

1. 화면 왼쪽 상단에서 **리소스 만들기**를 선택하고, **WAF**를 검색하고, **웹 애플리케이션 방화벽**을 선택한 다음, **만들기**를 선택합니다.
2. **WAF 정책 만들기** 페이지의 **기본** 탭에서 다음 정보를 입력하거나 선택하고, 나머지 설정은 기본값을 그대로 유지한 다음, **검토 + 만들기**를 선택합니다.

    | 설정                 | 값                                              |
    | ---                     | ---                                                |
    | 정책            |Azure CDN(미리 보기)을 선택합니다.|
    | Subscription            |Front Door 구독 이름을 선택합니다.|
    | Resource group          |Front Door 리소스 그룹 이름을 선택합니다.|
    | 정책 이름             |WAF 정책의 고유한 이름을 입력합니다.|

   ![WAF 정책 만들기](../media/waf-cdn-create-portal/basic.png)

3. **WAF 정책 만들기** 페이지의 **연결** 탭에서 **CDN 엔드포인트 추가**를 선택하고, 다음 설정을 입력한 다음, **추가**를 선택합니다.

    | 설정                 | 값                                              |
    | ---                     | ---                                                |
    | CDN 프로필              | CDN 프로필 이름을 선택합니다.|
    | 엔드포인트           | 엔드포인트의 이름을 선택한 다음, **추가**를 선택합니다.|
    
    > [!NOTE]
    > 엔드포인트가 WAF 정책과 연결되어 있으면 회색으로 표시됩니다. 먼저, 연결된 정책에서 엔드포인트를 제거한 다음, 엔드포인트를 새 WAF 정책에 다시 연결해야 합니다.
1. **검토 + 만들기**를 선택한 다음, **만들기**를 선택합니다.

## <a name="configure-web-application-firewall-policy-optional"></a>웹 애플리케이션 방화벽 정책 구성(선택 사항)

### <a name="change-mode"></a>모드 변경

WAF 정책을 만드는 경우 기본적으로 WAF 정책은 검색 모드입니다  . 검색 모드에서는 WAF가 요청을 차단하지 않습니다  . 대신 WAF 규칙과 일치하는 요청이 WAF 로그에 기록됩니다.

동작 중인 WAF를 확인하기 위해 모드 설정을 *검색*에서 *방지*로 변경할 수 있습니다. *방지* 모드에서는 DRS(기본 규칙 집합)에 정의된 규칙과 일치하는 요청이 차단되고 WAF 로그에 기록됩니다.

 ![WAF 정책 모드 변경](../media/waf-cdn-create-portal/policy.png)

### <a name="custom-rules"></a>사용자 지정 규칙

사용자 지정 규칙을 만들려면 **사용자 지정 규칙** 섹션에서 **사용자 지정 규칙 추가**를 선택합니다. 그러면 사용자 지정 규칙 구성 페이지가 열립니다. 사용자 지정 규칙의 유형은 두 가지 즉, **일치 규칙**과 **속도 제한**입니다.

다음 스크린샷은 쿼리 문자열에 **blockme** 값이 포함된 경우 요청을 차단하는 사용자 지정 일치 규칙을 보여줍니다.

![WAF 정책 모드 변경](../media/waf-cdn-create-portal/custommatch.png)

속도 제한 규칙에는 두 가지 추가 필드 즉, **속도 제한 기간**과 **속도 제한 임계값(요청)** 이 필요합니다. 다음 예를 참조하세요.

![WAF 정책 모드 변경](../media/waf-cdn-create-portal/customrate.png)

### <a name="default-rule-set-drs"></a>DRS(기본 규칙 집합)

Azure 관리형 기본 규칙 집합은 기본적으로 사용하도록 설정되어 있습니다. 규칙 그룹 내에 있는 개별 규칙을 사용하지 않도록 설정하려면 해당 규칙 그룹 내에서 규칙을 확장하고 규칙 번호 앞의 확인란을 선택한 다음, 위의 탭에서 **사용 안 함**을 선택합니다. 규칙 집합 내에 있는 개별 규칙의 동작 유형을 변경하려면 규칙 번호 앞의 확인란을 선택한 다음, 위에 있는 **동작 변경** 탭을 선택하세요.

 ![WAF 규칙 집합 변경](../media/waf-cdn-create-portal/managed2.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure 웹 애플리케이션 방화벽에 대해 알아보기](../overview.md)
