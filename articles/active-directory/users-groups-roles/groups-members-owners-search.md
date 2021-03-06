---
title: 검색 및 필터 그룹 구성원 및 소유자(미리 보기) - Azure Active Directory | 마이크로 소프트 문서
description: Azure 포털에서 구성원 및 소유자를 검색하고 필터링합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 02/28/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a815446b79b3e5ec0a75e5d179953956643b16c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206115"
---
# <a name="search-groups-and-members-preview-in-azure-active-directory"></a>Azure Active Directory에서 검색 그룹 및 구성원(미리 보기)

이 문서에서는 그룹의 구성원 및 소유자를 검색하는 방법과 Azure Active Directory(Azure AD) 포털의 그룹 개선 미리 보기의 일부로 검색 필터를 사용하는 방법을 설명합니다. 그룹 환경은 구성원과 소유자를 포함하여 그룹을 빠르고 쉽게 관리하는 데 도움이 되는 많은 개선 사항이 있습니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관을](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)참조하십시오.

이 미리 보기의 변경 사항은 다음과 같습니다.

- 그룹 이름의 하위 문자열 검색과 같은 새 그룹 검색 기능
- 멤버 및 소유자 목록의 새 필터링 및 정렬 옵션
- 구성원 및 소유자 목록에 대한 새로운 검색 기능
- 대규모 그룹에 대한 보다 정확한 그룹 수

## <a name="enabling-and-managing-the-preview"></a>미리 보기 사용 및 관리

미리 보기에 쉽게 참여할 수 있도록 했습니다.

  1. [Azure AD 포털에](https://portal.azure.com)로그인하고 **그룹을**선택합니다.
  2. 그룹 - 모든 그룹 페이지에서 페이지 상단의 배너를 선택하여 미리 보기에 참여합니다.

**모든 그룹** 페이지에서 **미리 보기 정보를** 선택하여 최신 기능 및 개선 사항도 확인할 수 있습니다. 미리 보기에 참여하면 개선이 있고 미리 보기의 일부인 모든 그룹 페이지에서 미리 보기 태그를 볼 수 있습니다. 이 미리 보기의 일부로 모든 그룹 페이지가 업데이트된 것은 아닙니다.

문제가 있는 경우 **모든 그룹** 페이지 상단의 배너를 선택하여 레거시 환경을 다시 전환할 수 있습니다. 우리는 우리의 경험을 향상시킬 수 있도록 귀하의 의견을 주셔서 감사합니다.

## <a name="group-search-and-sorting"></a>그룹 검색 및 정렬

검색 문자열을 입력할 수 있도록 그룹 목록 검색이 향상되어 검색이 그룹 이름 목록에서 자동으로 하위 문자열 검색을 수행합니다. `startswith` 하위 문자열 검색은 전체 단어에서만 수행되며 특수 문자는 포함되지 않습니다. 하위 문자열 검색은 대/소문자를 구분합니다.

![모든 그룹 페이지에서 새 하위 문자열 검색](./media/groups-members-owners-search/groups-search-preview.png)

예를 들어 "정책"에 대한 검색은 이제 "MDM 정책 - 서쪽" 및 "정책 그룹"을 모두 반환합니다. "New_policy"라는 그룹은 반환되지 않습니다.

- 그룹 구성원 목록에서도 동일한 검색을 수행할 수 있습니다.
- 이제 이름 열 제목 의 오른쪽에 있는 화살표를 사용하여 그룹 목록을 이름으로 정렬하여 오름차순 또는 내림차순으로 목록을 정렬할 수 있습니다.

## <a name="group-member-search-and-filtering"></a>그룹 구성원 검색 및 필터링

### <a name="search-group-member-and-owner-lists"></a>그룹 구성원 및 소유자 목록 검색

이제 이름으로 특정 그룹의 구성원을 검색하고 그룹 소유자 목록에서 동일한 검색을 수행할 수 있습니다. 새 환경에서 검색 상자에 문자열을 입력하면 시작과 함께 검색이 자동으로 수행됩니다. 예를 들어 "Scott"에 대한 검색은 스콧 윌킨슨을 반환합니다.

![그룹 구성원 및 소유자 목록에서 새 하위 문자열 검색](./media/groups-members-owners-search/members-list.png)

### <a name="filter-member-and-owners-list"></a>필터 구성원 및 소유자 목록

이제 검색 외에도 사용자 유형별로 멤버 및 소유자 목록을 필터링할 수 있습니다. 목록의 사용자 유형 열에 있는 정보입니다. 따라서 구성원과 게스트별로 목록을 필터링하여 그룹에 게스트가 있는지 확인할 수 있습니다.

### <a name="view-and-manage-membership"></a>멤버십 보기 및 관리

이제 특정 그룹의 직접 구성원을 보는 것 외에도 멤버 페이지에서 그룹의 모든 구성원 목록을 볼 수 있습니다. 멤버 목록에는 전이 멤버를 포함한 그룹의 모든 고유한 구성원이 포함됩니다.

직접 멤버 목록과 모든 멤버 목록을 개별적으로 검색하고 필터링할 수도 있습니다. 모든 멤버 목록을 필터링해도 직접 멤버 목록에 적용되는 필터에는 영향을 주지 않습니다.

## <a name="improved-group-member-counts"></a>개선된 그룹 구성원 수

모든 규모의 그룹에 대한 그룹 구성원 수를 제공하기 위해 그룹 **개요** 페이지가 개선되었습니다. 구성원이 1,000명 이상인 그룹의 경우에도 멤버 수를 확인할 수 있습니다. 이제 개요 **페이지에서** 그룹의 총 직접 구성원 수와 총 회원 수(전이 멤버를 포함한 그룹의 모든 고유 구성원)를 확인할 수 있습니다.

![그룹 멤버십 수의 정확도 향상](./media/groups-members-owners-search/member-numbers.png)

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure AD의 그룹 작업에 대한 추가 정보를 제공합니다.

- [그룹 및 멤버 보기](../fundamentals/active-directory-groups-view-azure-portal.md)
- [그룹 멤버 자격 관리](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [그룹의 사용자에 대한 동적 규칙 관리](groups-create-rule.md)
- [그룹 설정 편집](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [그룹을 사용하여 리소스에 대한 액세스 관리](../fundamentals/active-directory-manage-groups.md)
- [그룹을 사용하여 SaaS 앱에 대한 액세스 관리](groups-saasapps.md)
- [PowerShell 명령을 사용하여 그룹 관리](groups-settings-v2-cmdlets.md)
- [Azure 활성 디렉터리에 Azure 구독 추가](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
