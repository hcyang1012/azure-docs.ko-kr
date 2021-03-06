---
title: Azure 키 볼트 보안 | 마이크로 소프트 문서
description: Azure Key Vault, 키 및 비밀에 대한 액세스 권한을 관리합니다. Key Vault의 인증 및 권한 부여 모델과 키 자격 증명 모음을 보호하는 방법을 설명합니다.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mbaldwin
Customer intent: As a key vault administrator, I want to learn the options available to secure my vaults
ms.openlocfilehash: dbe13b2d1291f212af7da9d1176bc3d90997978b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428967"
---
# <a name="azure-key-vault-security"></a>Azure 키 볼트 보안

Azure Key Vault를 사용하려면 클라우드에서 인증서, 연결 문자열 및 암호와 같은 암호화 키 및 암호를 보호해야 합니다. 중요한 데이터와 비즈니스에 중요한 데이터를 저장하기 때문에 볼트및 보안에 저장된 데이터의 보안을 최대화하기 위한 조치를 취해야 합니다. 이 문서에서는 Azure Key Vault 보안을 디자인할 때 고려해야 할 몇 가지 개념을 다룹니다.

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

Azure 구독에 Key Vault을 만들 때 해당 구독의 Azure AD 테넌트에 자동으로 연결됩니다. 볼트에서 콘텐츠를 관리하거나 검색하려는 모든 사람은 Azure AD에서 인증해야 합니다.

- 인증은 호출자의 ID를 설정합니다.
- 권한 부여는 호출자에서 수행할 수 있는 작업을 결정합니다. Key Vault의 권한 부여는 [RBAC(역할 기반 액세스 제어)](../../role-based-access-control/overview.md) 및 Azure Key Vault 액세스 정책의 조합을 사용합니다.

### <a name="access-model-overview"></a>액세스 모델 개요

볼트에 대한 액세스는 두 개의 인터페이스 또는 평면을 통해 이루어집니다. 이러한 평면은 관리 평면과 데이터 평면입니다.

- *관리 평면은* Key Vault 자체를 관리하는 위치이며 볼트를 만들고 삭제하는 데 사용되는 인터페이스입니다. 키 자격 증명 모음 속성을 읽고 액세스 정책을 관리할 수도 있습니다.
- *데이터 평면을* 사용하면 키 자격 증명 모음에 저장된 데이터로 작업할 수 있습니다. 키, 비밀 및 인증서를 추가, 삭제 및 수정할 수 있습니다.

두 평면에서 키 자격 증명 모음에 액세스하려면 모든 호출자(사용자 또는 응용 프로그램)를 인증하고 승인해야 합니다. 두 평면은 인증을 위해 Azure AD(Azure Active Directory)를 사용합니다. 권한 부여를 위해 관리 평면에서는 RBAC(역할 기반 액세스 제어)를 사용하고, 데이터 평면에서는 Key Vault 액세스 정책을 사용합니다.

두 평면에 대한 인증에 단일 메커니즘을 사용할 경우 다음과 같은 몇 가지 이점이 있습니다.

- 조직에서는 조직의 모든 Key Vault에 대한 액세스를 중앙에서 제어할 수 있습니다.
- 사용자가 떠나는 경우 곧바로 조직의 모든 Key Vault에 대한 액세스 권한을 잃게 됩니다.
- 조직은 Azure AD의 옵션을 사용하여 인증을 사용자 지정할 수 있습니다.이 옵션을 사용 하 여 추가 된 보안에 대 한 다단계 인증을 사용 하 여

### <a name="managing-administrative-access-to-key-vault"></a>키 볼트에 대한 관리 액세스 관리

리소스 그룹에서 키 자격 증명 모음을 만들 때 Azure AD를 사용하여 액세스를 관리합니다. 사용자 또는 그룹에 리소스 그룹에서 key vault를 관리하는 기능을 부여합니다. 적절한 RBAC 역할을 할당하여 특정 범위 수준에서 액세스 권한을 부여할 수 있습니다. key vault를 관리하기 위해 사용자에게 액세스 권한을 부여하려면 특정 범위에 속한 사용자에게 미리 정의된 `key vault Contributor` 역할을 할당합니다. 다음 범위 수준을 RBAC 역할에 할당할 수 있습니다.

- **구독**: 구독 수준에서 할당된 RBAC 역할은 해당 구독 내의 모든 리소스 그룹 및 리소스에 적용됩니다.
- **리소스 그룹**: 리소스 그룹 수준에서 할당된 RBAC 역할은 해당 리소스 그룹의 모든 리소스에 적용됩니다.
- **특정 리소스**: 특정 리소스에 할당된 RBAC 역할이 해당 리소스에 적용됩니다. 이 경우 리소스는 특정 Key Vault입니다.

미리 정의된 몇 가지 역할이 있습니다. 미리 정의된 역할이 요구에 맞지 않는 경우 고유한 역할을 정의할 수 있습니다. 자세한 내용은 [RBAC: 기본 제공 역할을](../../role-based-access-control/built-in-roles.md)참조하십시오.

> [!IMPORTANT]
> 사용자에게 Key Vault 관리 평면에 대한 `Contributor` 사용 권한이 있는 경우 이 사용자는 Key Vault 액세스 정책을 설정하여 스스로 데이터 평면에 대한 액세스 권한을 부여할 수 있습니다. Key Vault에 대한 `Contributor` 역할 액세스 권한이 있는 사용자를 긴밀하게 제어해야 합니다. 권한이 있는 사람만 키 자격 증명 모음, 키, 비밀 및 인증서에 액세스하고 관리할 수 있는지 확인합니다.

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>키 볼트 데이터에 대한 액세스 제어

키 Vault 액세스 정책은 키, 보안 암호 또는 인증서에 별도로 권한을 부여합니다. 비밀이 아닌 키에 대해서만 사용자 액세스 권한을 부여할 수 있습니다. 키, 암호 및 인증서에 대한 액세스 권한은 볼트 수준에서 관리됩니다.

> [!IMPORTANT]
> Key Vault 액세스 정책은 특정 키, 비밀 또는 인증서와 같은 세분화된 개체 수준 권한을 지원하지 않습니다. 사용자에게 키를 만들고 삭제하는 권한이 부여되면 해당 사용자는 Key Vault의 모든 키에 대해 이러한 작업을 수행할 수 있습니다.

Key Vault에 대한 액세스 정책을 설정하려면 [Azure Portal](https://portal.azure.com/), [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest), [PowerShell](/powershell/azureps-cmdlets-docs) 또는 [Key Vault 관리 REST API](/rest/api/keyvault/)를 사용합니다.

[Azure Key Vault에 대한 가상 네트워크 서비스 끝점을](overview-vnet-service-endpoints.md)사용하여 데이터 평면 액세스를 제한할 수 있습니다. 추가 보안 계층에 대한 [방화벽 및 가상 네트워크 규칙](network-security.md)을 구성할 수 있습니다.

## <a name="network-access"></a>네트워크 액세스

볼트에 액세스할 수 있는 IP 주소를 지정하여 볼트 노출을 줄일 수 있습니다. Azure Key Vault의 가상 네트워크 서비스 엔드포인트를 사용하면 지정된 가상 네트워크에 대한 액세스를 제한할 수 있습니다. 엔드포인트를 사용하면 IPv4(인터넷 프로토콜 버전 4) 주소 범위 목록에 대한 액세스를 제한할 수도 있습니다. 해당 소스 외부에서 키 자격 증명 모음에 연결하는 모든 사용자는 액세스가 거부됩니다.

방화벽 규칙이 적용되면 사용자는 요청이 허용된 가상 네트워크 또는 IPv4 주소 범위에서 시작된 경우에만 Key Vault의 데이터를 읽을 수 있습니다. Azure Portal에서 Key Vault에 액세스하는 경우도 마찬가지입니다. 사용자가 Azure Portal에서 키 자격 증명 모음으로 이동할 수 있다고 해도 해당 클라이언트 머신이 허용 목록에 없는 경우 키, 비밀 또는 인증서를 나열하지 못할 수 있습니다. 다른 Azure 서비스의 Key Vault 선택기도 마찬가지입니다. 방화벽 규칙이 사용자의 클라이언트 머신을 금지하는 경우 해당 사용자는 키 자격 증명 모음 목록을 확인할 수 있지만 키를 나열하지는 못합니다.

Azure Key Vault 네트워크 주소에 대한 자세한 내용은 [Azure 키 볼트에 대한 가상 네트워크 서비스 끝점을](overview-vnet-service-endpoints.md)검토합니다.

## <a name="monitoring"></a>모니터링

키 볼트 로깅은 볼트에서 수행된 활동에 대한 정보를 저장합니다. 키 볼트 로그:

- 실패한 요청을 포함하여 인증된 모든 REST API 요청
  - 키 자격 증명 모음 자체에 대한 작업입니다. 이러한 작업에는 생성, 삭제, 액세스 정책 설정 및 태그와 같은 주요 볼트 특성 업데이트가 포함됩니다.
  - 다음을 포함하여 키 자격 증명 모음의 키 및 비밀에 대한 작업
    - 이러한 키 또는 비밀 만들기, 수정 또는 삭제
    - 서명, 확인, 암호화, 암호 해독, 키 래핑 및 래핑 해제, 비밀 가져오기, 키 및 비밀(및 해당 버전) 나열
- 401 응답이 발생하는 인증되지 않은 요청. 예를 들어 전달자 토큰이 없거나, 형식이 잘못되었거나 만료되었거나, 잘못된 토큰이 있는 요청입니다.

키 자격 증명 모음 작업 후 10분 이내에 로깅 정보에 액세스할 수 있습니다. 저장소 계정에서 로그를 관리하는 것은 사용자의 책임입니다.

- 표준 Azure 액세스 제어 메서드를 사용하여 액세스할 수 있는 사용자를 제한하여 로그를 보호합니다.
- 더 이상 스토리지 계정에 유지하지 않으려는 로그를 삭제합니다.

저장소 계정을 안전하게 관리하는 방법에 대한 권장 사항은 [Azure Storage 보안 가이드를](../../storage/blobs/security-recommendations.md) 참조하십시오.

## <a name="next-steps"></a>다음 단계

- [Azure 키 볼트에 대한 가상 네트워크 서비스 끝점)](overview-vnet-service-endpoints.md)
- [RBAC: 기본 제공 역할](../../role-based-access-control/built-in-roles.md)
- [Azure 키 볼트에 대한 가상 네트워크 서비스 끝점)](overview-vnet-service-endpoints.md)
