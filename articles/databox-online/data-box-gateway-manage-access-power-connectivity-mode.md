---
title: Azure 데이터 박스 게이트웨이 장치 액세스, 전원 및 연결 모드
description: Azure로 데이터를 전송하는 데 도움이 되는 Azure Data Box Gateway 디바이스에 대한 액세스, 전원 및 연결 모드를 관리하는 방법에 대해 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: e4d85bd460c39964c9f42ac946e3522f5f129c1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474444"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway"></a>Azure 데이터 박스 게이트웨이의 액세스, 전원 및 연결 모드 관리

이 문서에서는 Azure Data Box Gateway에 대한 액세스, 전원 및 연결 모드를 관리하는 방법에 대해 설명합니다. 이러한 작업은 로컬 웹 UI 또는 Azure Portal을 통해 수행합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 디바이스 액세스 관리
> * 연결 모드 관리
> * 전원 관리

## <a name="manage-device-access"></a>디바이스 액세스 관리

데이터 박스 게이트웨이 장치에 대한 액세스는 장치 암호를 사용하여 제어됩니다. 로컬 웹 UI를 통해 암호를 변경할 수 있습니다. Azure 포털에서 장치 암호를 재설정할 수도 있습니다.

### <a name="change-device-password"></a>디바이스 암호 변경

로컬 UI의 다음 단계를 수행하여 장치 암호를 변경합니다.

1. 로컬 웹 UI에서 **유지 관리 > 암호 변경**으로 이동합니다.
2. 현재 암호를 입력한 다음, 새 암호를 입력합니다. 제공된 암호는 8 ~ 16자 사이여야 합니다. 암호에 대문자, 소문자, 숫자, 특수 문자 중 3가지가 포함되어야 합니다. 새 암호를 확인합니다.

    ![암호 변경](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. **암호 변경**을 클릭합니다.
 
### <a name="reset-device-password"></a>장치 암호 재설정

재설정 워크플로에서는 사용자가 이전 암호를 기억할 필요가 없으며 암호를 분실했을 때 유용합니다. 이 워크플로는 Azure Portal에서 수행합니다.

1. Azure Portal에서 **개요 > 관리자 암호 재설정**으로 이동합니다.

    ![암호 재설정](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. 새 암호를 입력하고 확인합니다. 제공된 암호는 8 ~ 16자 사이여야 합니다. 암호에 대문자, 소문자, 숫자, 특수 문자 중 3가지가 포함되어야 합니다. **재설정**을 클릭합니다.

    ![암호 재설정](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>리소스 액세스 관리

데이터 상자 가장자리/데이터 상자 게이트웨이, IoT 허브 및 Azure 저장소 리소스를 만들려면 리소스 그룹 수준에서 기여자 이상으로 권한이 필요합니다. 또한 해당 리소스 공급자를 등록해야 합니다. 정품 인증 키 및 자격 증명을 포함하는 모든 작업의 경우 Azure Active Directory Graph API에 대한 사용 권한도 필요합니다. 이 것들은 다음 섹션에서 설명합니다.

### <a name="manage-microsoft-graph-api-permissions"></a>마이크로소프트 그래프 API 권한 관리

Data Box Edge 장치에 대한 활성화 키를 생성하거나 자격 증명이 필요한 작업을 수행할 때 Microsoft Graph API에 대한 권한이 필요합니다. 자격 증명이 필요한 작업은 다음과 같은 것입니다.

-  연결된 저장소 계정으로 공유 만들기
-  장치에서 공유에 액세스할 수 있는 사용자 만들기

을 수행할 `User` 수 있어야 하므로 Active Directory 테넌트에 액세스할 수 `Read all directory objects`있어야 합니다. `Read all directory objects`에 대한 사용 권한이 없기 때문에 게스트 사용자가 될 수 없습니다. 게스트인 경우 활성화 키 생성, Data Box Edge 디바이스에 대한 공유 만들기, 사용자 만들기 등의 작업이 모두 실패합니다.

사용자에게 Microsoft 그래프 API에 대한 액세스를 제공하는 방법에 대한 자세한 내용은 [Microsoft 그래프 사용 권한 참조를](https://docs.microsoft.com/graph/permissions-reference)참조하십시오.

### <a name="register-resource-providers"></a>리소스 공급자 등록

Azure에서 리소스를 프로비전하려면 Azure 리소스 관리자 모델에서 해당 리소스 만들기를 지원하는 리소스 공급자가 필요합니다. 예를 들어 가상 컴퓨터를 프로비전하려면 구독에서 사용할 수 있는 'Microsoft.Compute' 리소스 공급자가 있어야 합니다.
 
리소스 공급자는 구독 수준에서 등록됩니다. 기본적으로 새 Azure 구독은 주로 사용되는 리소스 공급자 목록에 미리 등록됩니다. 'Microsoft.DataBoxEdge'에 대한 리소스 공급자는 이 목록에 포함되지 않습니다.

이러한 리소스에 대한 리소스 공급자가 이미 있는 경우 사용자가 리소스 그룹 내에서 소유자 권한이 있는 'Microsoft.DataBoxEdge'와 같은 리소스를 만들 수 있도록 구독 수준에 대한 액세스 권한을 부여할 필요가 없습니다. 등록.

리소스를 만들기 전에 리소스 공급자가 구독에 등록되어 있는지 확인합니다. 리소스 공급자가 등록되지 않은 경우 새 리소스를 만드는 사용자에게 구독 수준에서 필요한 리소스 공급자를 등록할 수 있는 충분한 권한이 있는지 확인해야 합니다. 이 작업을 수행하지 않은 경우 다음과 같은 오류가 표시됩니다.

*> \<구독 구독 이름 리소스 공급자를 등록할 수 있는 권한이 없습니다: Microsoft.DataBoxEdge.*


현재 구독에서 등록된 리소스 공급자 목록을 얻으려면 다음 명령을 실행합니다.

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

데이터 박스 에지 `Microsoft.DataBoxEdge` 장치의 경우 등록해야 합니다. 등록하려면 `Microsoft.DataBoxEdge`구독 관리자는 다음 명령을 실행해야 합니다.

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

리소스 공급자를 등록하는 방법에 대한 자세한 내용은 [리소스 공급자 등록에 대한 오류 해결을](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors)참조하십시오.

## <a name="manage-connectivity-mode"></a>연결 모드 관리

디바이스는 기본 표준 모드 외에도, 부분적으로 연결이 끊어져 있거나 연결이 끊긴 모드에서도 실행할 수 있습니다. 이러한 각 모드는 아래에 설명되어 있습니다.

- **부분적으로 연결이 끊어짐** - 이 모드에서 디바이스는 일부 데이터를 공유에 업로드할 수 없지만 Azure Portal을 통해 관리할 수 있습니다.

    이 모드는 일반적으로 요금제 위성 네트워크에서 사용되며 목표는 네트워크 대역폭 소비를 최소화하는 것입니다. 디바이스 모니터링 작업을 위해 최소한의 네트워크 사용량이 여전히 발생할 수 있습니다.

- **연결 끊김** – 이 모드에서는 디바이스가 클라우드에서 연결이 완전히 끊어지고 클라우드 업로드와 다운로드를 모두 사용할 수 없습니다. 디바이스는 로컬 웹 UI를 통해서만 관리할 수 있습니다.

    이 모드는 일반적으로 디바이스를 오프라인 상태로 만들 때 사용합니다.

디바이스 모드를 변경하려면 다음 단계를 수행합니다.

1. 디바이스의 로컬 웹 UI에서 **구성 > 클라우드 설정**으로 이동합니다.
2. **클라우드 업로드 및 다운로드**를 사용하지 않도록 설정합니다.
3. 부분적으로 연결이 끊긴 모드에서 디바이스를 실행하려면 **Azure Portal 관리**를 사용하도록 설정합니다.

    ![연결 모드](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. 연결이 끊긴 모드에서 디바이스를 실행하려면 **Azure Portal 관리**를 사용하지 않도록 설정합니다. 이제 디바이스는 로컬 웹 UI를 통해서만 관리할 수 있습니다.

    ![연결 모드](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>전원 관리

로컬 웹 UI를 사용하여 가상 디바이스를 종료하거나 다시 시작할 수 있습니다. 다시 시작하기 전에 호스트에서 공유를 오프라인으로 전환한 후 디바이스를 다시 시작하는 것이 좋습니다. 이 작업을 수행하면 데이터 손상 가능성이 최소화됩니다.

1. 로컬 웹 UI에서 **유지 관리 > 전원 설정**으로 이동합니다.
2. 수행하려는 작업에 따라 **종료** 또는 **다시 시작**을 클릭합니다.

    ![전원 설정](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. 확인 메시지가 표시되면 **예**를 클릭하여 계속 진행합니다.

> [!NOTE]
> 가상 디바이스를 종료하는 경우 하이퍼바이저 관리를 통해 디바이스를 시작해야 합니다.
