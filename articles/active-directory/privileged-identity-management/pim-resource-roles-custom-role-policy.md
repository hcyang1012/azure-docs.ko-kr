---
title: PIM - Azure AD에서 Azure 리소스에 대한 사용자 지정 역할 사용 | 마이크로 소프트 문서
description: Azure AD PIM(Privileged Identity Management)에서 Azure 리소스에 사용자 지정 역할을 사용하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbe08cff2b57155f8f3315f5d3454abfbdad47a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847056"
---
# <a name="use-custom-roles-for-azure-resources-in-privileged-identity-management"></a>권한 있는 ID 관리에서 Azure 리소스에 대한 사용자 지정 역할 사용

Azure Active Directory(Azure AD) 조직의 권한 있는 역할의 일부 사용자에게 엄격한 PIM(권한 있는 ID 관리) 설정을 적용하는 동시에 다른 사용자에게 더 큰 자율성을 제공해야 할 수 있습니다. 예를 들어 조직에서 Azure 구독에서 실행되는 응용 프로그램의 개발을 지원하기 위해 여러 계약 동료를 고용하는 시나리오를 생각해 보십시오.

리소스 관리자로서 직원이 승인 없이 액세스할 수 있도록 하려고 합니다. 그러나 조직의 리소스에 대한 액세스를 요청할 때 모든 계약 당사자는 승인을 받아야 합니다.

다음 섹션에 설명된 단계를 수행하여 Azure 리소스 역할에 대한 대상 권한 있는 ID 관리 설정을 설정합니다.

## <a name="create-the-custom-role"></a>사용자 지정 역할 만들기

리소스에 대한 사용자 지정 역할을 만들려면 [Azure 역할 기반 액세스 제어의 사용자 지정 역할 만들기](../role-based-access-control-custom-roles.md)에 설명된 단계를 따릅니다.

사용자 지정 역할을 만들 때 복제하려는 기본 제공 역할을 쉽게 기억할 수 있도록 설명이 포함된 이름을 사용합니다.

> [!NOTE]
> 사용자 지정 역할이 복제하려는 기본 제공 역할의 복제본이 되고, 해당 범위는 기본 제공 역할과 일치하도록 합니다.

## <a name="apply-pim-settings"></a>PIM 설정 적용

Azure AD 조직에서 역할이 만들어진 후 Azure 포털의 **권한 있는 ID 관리 - Azure 리소스** 페이지로 이동합니다. 역할을 적용하는 리소스를 선택합니다.

![“Privileged Identity Management - Azure 리소스” 창](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

역할의 이러한 구성원에 적용해야 하는 [권한 있는 ID 관리 역할 설정을 구성합니다.](pim-resource-roles-configure-role-settings.md)

마지막으로 이러한 설정으로 대상으로 할 멤버의 고유 그룹에 [역할을 할당](pim-resource-roles-assign-roles.md)합니다.

## <a name="next-steps"></a>다음 단계

- [권한 있는 ID 관리에서 Azure 리소스 역할 설정 구성](pim-resource-roles-configure-role-settings.md)
- [Azure의 사용자 지정 역할](../../role-based-access-control/custom-roles.md)
