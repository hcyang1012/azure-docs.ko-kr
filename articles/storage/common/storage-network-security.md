---
title: Azure Storage 방화벽 및 가상 네트워크 구성 | Microsoft Docs
description: 스토리지 계정에 대한 계층화된 네트워크 보안을 구성합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 7120ba2cf71c9af5373b830d04d0b67952922887
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113519"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Azure Storage 방화벽 및 가상 네트워크 구성

Azure Storage는 계층화된 보안 모델을 제공합니다. 이 모델을 사용하면 사용되는 네트워크의 유형과 하위 집합에 따라 응용 프로그램 및 엔터프라이즈 환경이 요구하는 저장소 계정에 대한 액세스 수준을 보호하고 제어할 수 있습니다. 네트워크 규칙이 구성되면 지정된 네트워크 집합을 통해 데이터를 요청하는 응용 프로그램만 저장소 계정에 액세스할 수 있습니다. 저장소 계정에 대한 액세스를 지정된 IP 주소, IP 범위 또는 Azure 가상 네트워크(VNet)의 서브넷 목록에서 발생하는 요청으로 제한할 수 있습니다.

저장소 계정에는 인터넷을 통해 액세스할 수 있는 공용 끝점이 있습니다. 또한 VNet에서 저장소 계정에 개인 IP 주소를 할당하고 개인 링크를 통해 VNet과 저장소 계정 간의 모든 트래픽을 보호하는 저장소 계정에 대한 비공개 [엔드포인트를](storage-private-endpoints.md)만들 수도 있습니다. Azure 저장소 방화벽은 저장소 계정의 공용 끝점에 대한 액세스 제어 액세스를 제공합니다. 개인 끝점을 사용할 때 방화벽을 사용하여 공용 끝점을 통해 모든 액세스를 차단할 수도 있습니다. 또한 스토리지 방화벽 구성을 사용하면 신뢰할 수 있는 Azure 플랫폼 서비스를 선택하여 저장소 계정에 안전하게 액세스할 수 있습니다.

네트워크 규칙이 적용된 경우 저장소 계정에 액세스하는 응용 프로그램은 여전히 요청에 대한 적절한 권한 부여가 필요합니다. 권한 부여는 Blob 및 큐에 대한 Azure Active Directory(Azure AD) 자격 증명, 유효한 계정 액세스 키 또는 SAS 토큰을 통해 지원됩니다.

> [!IMPORTANT]
> Azure 가상 네트워크(VNet) 내에서 운영되는 서비스 또는 허용된 공용 IP 주소에서 요청이 발생하는 경우가 아니면 저장소 계정에 대한 방화벽 규칙을 설정하면 기본적으로 들어오는 데이터 요청이 차단됩니다. 차단되는 요청에는 다른 Azure 서비스, Azure Portal, 로깅 및 메트릭 서비스 등이 포함됩니다.
>
> 서비스 인스턴스를 호스팅하는 서브넷의 트래픽을 허용하여 VNet 내에서 작동하는 Azure 서비스에 대한 액세스 권한을 부여할 수 있습니다. 아래에 설명된 [예외 메커니즘을](#exceptions) 통해 제한된 수의 시나리오를 활성화할 수도 있습니다. Azure 포털을 통해 저장소 계정의 데이터에 액세스하려면 설정한 신뢰할 수 있는 경계(IP 또는 VNet) 내에 있는 컴퓨터에 있어야 합니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>시나리오

저장소 계정을 보호하려면 기본적으로 공용 끝점의 모든 네트워크(인터넷 트래픽 포함)의 트래픽 에 대한 액세스를 거부하는 규칙을 구성해야 합니다. 그런 다음 특정 VNet의 트래픽에 대한 액세스 권한을 부여하는 규칙을 구성해야 합니다. 또한 일부 공용 인터넷 IP 주소 범위의 트래픽에 대한 액세스 권한을 부여하도록 규칙을 구성하여 특정 인터넷 또는 온-프레미스 클라이언트의 연결을 활성화할 수도 있습니다. 이 구성을 사용하면 애플리케이션에 대한 보안 네트워크 경계를 구축할 수 있습니다.

특정 가상 네트워크 및 동일한 저장소 계정의 공용 IP 주소 범위에서 액세스를 허용하는 방화벽 규칙을 결합할 수 있습니다. 저장소 방화벽 규칙은 기존 저장소 계정에 적용하거나 새 저장소 계정을 만들 때 적용할 수 있습니다.

저장소 방화벽 규칙은 저장소 계정의 공용 끝점에 적용됩니다. 저장소 계정의 개인 끝점에 대한 트래픽을 허용하기 위해 방화벽 액세스 규칙이 필요하지 않습니다. 개인 끝점 만들기를 승인하는 프로세스는 개인 끝점을 호스팅하는 서브넷의 트래픽에 대한 암시적 액세스를 부여합니다.

네트워크 규칙은 REST 및 SMB를 포함하여 Azure Storage에 대한 모든 네트워크 프로토콜에 적용됩니다. Azure 포털, 저장소 탐색기 및 AZCopy와 같은 도구를 사용하여 데이터에 액세스하려면 명시적 네트워크 규칙을 구성해야 합니다.

네트워크 규칙이 적용되면 모든 요청에 적용됩니다. 특정 IP 주소에 대한 액세스 권한을 부여하는 SAS 토큰은 토큰 소유자의 액세스를 제한하지만, 구성된 네트워크 규칙 이외의 새 액세스 권한은 부여하지 않습니다.

가상 머신 디스크 트래픽(탑재 및 분리 작업 및 디스크 IO 포함)은 네트워크 규칙의 영향을 받지 않습니다. 페이지 Blob에 대한 REST 액세스는 네트워크 규칙에 의해 보호됩니다.

클래식 스토리지 계정은 방화벽 및 가상 네트워크를 지원하지 않습니다.

예외를 만들어 VM 백업 및 복원에 적용되는 네트워크 규칙이 있는 스토리지 계정에서 관리되지 않는 디스크를 사용할 수 있습니다. 이 프로세스는 이 문서의 [예외](#exceptions) 섹션에서 설명하고 있습니다. 방화벽 예외는 Azure에서 이미 관리되고 있으므로 관리 디스크에는 적용되지 않습니다.

## <a name="change-the-default-network-access-rule"></a>기본 네트워크 액세스 규칙 변경

기본적으로 스토리지 계정은 네트워크에 있는 모든 클라이언트로부터의 연결을 허용합니다. 선택한 네트워크에 대한 액세스를 제한하려면 먼저 기본 동작을 변경해야 합니다.

> [!WARNING]
> 네트워크 규칙을 변경하면 Azure Storage에 연결하는 애플리케이션의 기능에 영향을 미칠 수 있습니다. 기본 네트워크 규칙을 **거부하도록** 설정하면 액세스 **권한을 부여하는** 특정 네트워크 규칙도 적용되지 않는 한 데이터에 대한 모든 액세스가 차단됩니다. 액세스를 거부하도록 기본 규칙을 변경하기 전에 네트워크 규칙을 사용하여 허용된 모든 네트워크에 대한 액세스를 허가해야 합니다.

### <a name="managing-default-network-access-rules"></a>기본 네트워크 액세스 규칙 관리

스토리지 계정에 대한 기본 네트워크 액세스 규칙은 Azure Portal, PowerShell 또는 CLIv2를 통해 관리할 수 있습니다.

#### <a name="azure-portal"></a>Azure portal

1. 보호하려는 스토리지 계정으로 이동합니다.

1. **Firewall 및 Virtual Network**이라는 설정 메뉴를 클릭합니다.

1. 기본적으로 액세스를 거부하려면 **선택한 네트워크**에서 액세스를 허용하도록 선택합니다. 모든 네트워크의 트래픽을 허용하려면 **모든 네트워크**에서 액세스를 허용하도록 선택합니다.

1. **저장**을 클릭하여 변경 내용을 적용합니다.

#### <a name="powershell"></a>PowerShell

1. [Azure PowerShell](/powershell/azure/install-Az-ps)을 설치하고 [로그인](/powershell/azure/authenticate-azureps)합니다.

1. 스토리지 계정에 대한 기본 규칙의 상태를 표시합니다.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. 기본적으로 네트워크 액세스를 거부하도록 기본 규칙을 설정합니다.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. 기본적으로 네트워크 액세스를 허용하도록 기본 규칙을 설정합니다.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="cliv2"></a>CLIv2

1. [Azure CLI](/cli/azure/install-azure-cli)를 설치하고 [로그인](/cli/azure/authenticate-azure-cli)합니다.

1. 스토리지 계정에 대한 기본 규칙의 상태를 표시합니다.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

1. 기본적으로 네트워크 액세스를 거부하도록 기본 규칙을 설정합니다.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

1. 기본적으로 네트워크 액세스를 허용하도록 기본 규칙을 설정합니다.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```

## <a name="grant-access-from-a-virtual-network"></a>가상 네트워크의 액세스 허가

특정 서브넷에서만 액세스를 허용하도록 저장소 계정을 구성할 수 있습니다. 허용되는 서브넷은 동일한 구독의 VNet또는 다른 Azure Active Directory 테넌트에 속한 구독을 포함하여 다른 구독의 서브넷에 속할 수 있습니다.

VNet 내의 Azure Storage에 대해 [서비스 엔드포인트](/azure/virtual-network/virtual-network-service-endpoints-overview)를 사용하도록 설정합니다. 서비스 끝점은 VNet에서 Azure Storage 서비스에 대한 최적의 경로를 통해 트래픽을 라우팅합니다. 서브넷과 가상 네트워크의 ID도 각 요청과 함께 전송됩니다. 그런 다음 관리자는 VNet의 특정 서브넷에서 요청을 받을 수 있도록 하는 저장소 계정에 대한 네트워크 규칙을 구성할 수 있습니다. 이러한 네트워크 규칙을 통해 액세스가 허용되는 클라이언트에서 데이터에 액세스하려면 스토리지 계정의 권한 부여 요구 사항을 계속 충족해야 합니다.

각 스토리지 계정은 [IP 네트워크 규칙](#grant-access-from-an-internet-ip-range)과 결합될 수 있는 최대 100개의 가상 네트워크 규칙을 지원합니다.

### <a name="available-virtual-network-regions"></a>사용 가능한 가상 네트워크 지역

일반적으로 서비스 엔드포인트는 동일한 Azure 지역의 가상 네트워크와 서비스 인스턴스 간에 작동합니다. Azure Storage에서 서비스 엔드포인트를 사용하는 경우 이 범위에는 [쌍을 이루는 지역](/azure/best-practices-availability-paired-regions)이 포함됩니다. 서비스 엔드포인트를 사용하면 지역별 장애 조치 및 읽기 전용 RA-GRS(지역 중복 스토리지) 인스턴스에 대한 액세스 중에 연속성을 유지할 수 있습니다. 가상 네트워크에서 스토리지 계정으로의 액세스를 허가하는 네트워크 규칙은 모든 RA-GRS 인스턴스에 대한 액세스도 허가합니다.

지역 가동 중단 시 재해 복구를 계획하는 경우 쌍을 이루는 지역에 VNet을 미리 만들어야 합니다. 이러한 대체 가상 네트워크에서 액세스할 수 있도록 허용하는 네트워크 규칙을 사용하여 Azure Storage에 대한 서비스 엔드포인트를 사용하도록 설정합니다. 그런 다음, 이러한 규칙을 지역 중복 스토리지 계정에 적용합니다.

> [!NOTE]
> 서비스 엔드포인트는 가상 네트워크 지역 및 지정된 지역 쌍 외부의 트래픽에는 적용되지 않습니다. 가상 네트워크에서 액세스할 수 있도록 허용하는 네트워크 규칙만 스토리지 계정의 주 지역 또는 쌍을 이루는 지정된 지역의 스토리지 계정에 적용할 수 있습니다.

### <a name="required-permissions"></a>필요한 사용 권한

가상 네트워크 규칙을 스토리지 계정에 적용하려면 추가되는 서브넷에 대한 적절한 권한이 사용자에게 있어야 합니다. 필요한 권한은 *서브넷에 서비스 조인*이며, *스토리지 계정 기여자* 기본 제공 역할에 포함됩니다. 사용자 지정 역할 정의에 추가할 수도 있습니다.

저장소 계정 및 액세스 권한을 부여한 가상 네트워크는 다른 Azure AD 테넌트의 일부인 구독을 포함하여 다른 구독에 있을 수 있습니다.

> [!NOTE]
> 다른 Azure Active Directory 테넌트의 일부인 가상 네트워크의 서브넷에 대한 액세스 권한을 부여하는 규칙의 구성은 현재 Powershell, CLI 및 REST API를 통해서만 지원됩니다. 이러한 규칙은 Azure 포털을 통해 구성할 수 없지만 포털에서 볼 수 있습니다.

### <a name="managing-virtual-network-rules"></a>가상 네트워크 규칙 관리

Azure Portal, PowerShell 또는 CLIv2를 통해 스토리지 계정에 대한 가상 네트워크 규칙을 관리할 수 있습니다.

#### <a name="azure-portal"></a>Azure portal

1. 보호하려는 스토리지 계정으로 이동합니다.

1. **Firewall 및 Virtual Network**이라는 설정 메뉴를 클릭합니다.

1. **선택한 네트워크**에서 액세스를 허용하도록 선택했는지 확인합니다.

1. 새 네트워크 규칙을 사용하여 가상 네트워크에 대한 액세스 권한을 부여하려면 **가상 네트워크** 아래에서 **기존 가상 네트워크 추가**를 클릭하고, **가상 네트워크** 및 **서브넷** 옵션을 선택한 다음, **추가**를 클릭합니다. 새 가상 네트워크를 만들고 액세스 권한을 부여하려면 **새 가상 네트워크 추가**를 클릭합니다. 새 가상 네트워크를 만드는 데 필요한 정보를 제공한 다음, **만들기**를 클릭합니다.

    > [!NOTE]
    > 이전에 Azure Storage에 대한 서비스 엔드포인트가 선택한 가상 네트워크 및 서브넷에 구성되지 않은 경우 이 작업의 일환으로 구성할 수 있습니다.
    >
    > 현재 는 규칙을 만드는 동안 선택을 위해 동일한 Azure Active Directory 테넌트에 속한 가상 네트워크만 표시됩니다. 다른 테넌트에 속한 가상 네트워크의 서브넷에 대한 액세스 권한을 부여하려면 Powershell, CLI 또는 REST API를 사용하십시오.

1. 가상 네트워크 또는 서브넷 규칙을 제거하려면 **...** 를 클릭하여 가상 네트워크 또는 서브넷에 대한 상황에 맞는 메뉴를 열고 **제거**를 클릭합니다.

1. **저장**을 클릭하여 변경 내용을 적용합니다.

#### <a name="powershell"></a>PowerShell

1. [Azure PowerShell](/powershell/azure/install-Az-ps)을 설치하고 [로그인](/powershell/azure/authenticate-azureps)합니다.

1. 가상 네트워크 규칙을 나열합니다.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. 기존 가상 네트워크 및 서브넷에서 Azure Storage에 대한 서비스 엔드포인트를 사용하도록 설정합니다.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

1. 가상 네트워크 및 서브넷에 대한 네트워크 규칙을 추가합니다.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > 다른 Azure AD 테넌트에 속한 VNet의 서브넷에 대한 네트워크 규칙을 추가하려면 "/구독/구독-ID/리소스 그룹/리소스 그룹/공급자/Microsoft.Network/vNet-name/서브넷 이름" 양식에 정규화된 **VirtualNetworkResourceId** 매개 변수를 사용합니다.

1. 가상 네트워크 및 서브넷에 대한 네트워크 규칙을 제거합니다.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 **거부**로 설정해야 합니다. 그렇지 않으면 네트워크 규칙이 적용되지 않습니다.

#### <a name="cliv2"></a>CLIv2

1. [Azure CLI](/cli/azure/install-azure-cli)를 설치하고 [로그인](/cli/azure/authenticate-azure-cli)합니다.

1. 가상 네트워크 규칙을 나열합니다.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

1. 기존 가상 네트워크 및 서브넷에서 Azure Storage에 대한 서비스 엔드포인트를 사용하도록 설정합니다.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

1. 가상 네트워크 및 서브넷에 대한 네트워크 규칙을 추가합니다.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > 다른 Azure AD 테넌트에 속하는 VNet의 서브넷에 대한 규칙을\<추가하려면 "/구독/구독-ID/리소스\>그룹/리소스\<\>\<그룹/공급자/Microsoft.Network/vNet 이름/서브넷/서브넷\>\<이름"이라는\>형식으로 정규화된 서브넷 ID를 사용합니다.
    >
    > **구독** 매개 변수를 사용하여 다른 Azure AD 테넌트에 속한 VNet에 대한 서브넷 ID를 검색할 수 있습니다.

1. 가상 네트워크 및 서브넷에 대한 네트워크 규칙을 제거합니다.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 **거부**로 설정해야 합니다. 그렇지 않으면 네트워크 규칙이 적용되지 않습니다.

## <a name="grant-access-from-an-internet-ip-range"></a>인터넷 IP 범위의 액세스 허가

특정 공용 인터넷 IP 주소 범위에서 액세스할 수 있도록 스토리지 계정을 구성할 수 있습니다. 이 구성은 특정 인터넷 기반 서비스와 온-프레미스 네트워크에 대한 액세스 권한을 부여하고 일반 인터넷 트래픽을 차단합니다.

*16.17.18.0/24* 형식의 [CIDR 표기법](https://tools.ietf.org/html/rfc4632)을 사용하거나 개별 IP 주소(예: *16.17.18.19*)를 사용하여 허용된 인터넷 주소 범위를 제공합니다.

   > [!NOTE]
   > "/31" 또는 "/32" 접두사 크기를 사용하는 작은 주소 범위는 지원되지 않습니다. 이러한 범위는 개별 IP 주소 규칙을 사용하여 구성해야 합니다.

IP 네트워크 규칙은 **공용 인터넷** IP 주소에 대해서만 허용됩니다. 사설망에 예약된 IP 주소 범위([RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)에 정의된 대로)는 IP 규칙에서 허용되지 않습니다. 사설망에는 _10.*_, _172.16.*_ - _172.31.*_ 및 _192.168.*_ 로 시작하는 주소가 포함됩니다.

   > [!NOTE]
   > IP 네트워크 규칙은 스토리지 계정과 동일한 Azure 지역에서 시작된 요청에 영향을 주지 않습니다. 동일한 지역 요청을 허용하려면 [가상 네트워크 규칙](#grant-access-from-a-virtual-network)을 사용하세요.

  > [!NOTE]
  > 저장소 계정과 동일한 지역에 배포된 서비스는 통신을 위해 개인 Azure IP 주소를 사용합니다. 따라서 공용 아웃바운드 IP 주소 범위에 따라 특정 Azure 서비스에 대한 액세스를 제한할 수 없습니다.

저장소 방화벽 규칙의 구성에는 IPV4 주소만 지원됩니다.

각 저장소 계정은 최대 100개의 IP 네트워크 규칙을 지원합니다.

### <a name="configuring-access-from-on-premises-networks"></a>온-프레미스 네트워크에서의 액세스 구성

IP 네트워크 규칙을 사용하여 온-프레미스 네트워크에서 스토리지 계정으로의 액세스 권한을 부여하려면 네트워크에서 사용되는 인터넷 연결 IP 주소를 식별해야 합니다. 네트워크 관리자에게 도움을 요청합니다.

공용 피어링 또는 Microsoft 피어링을 위해 온-프레미스에서 [ExpressRoute](/azure/expressroute/expressroute-introduction)를 사용하는 경우 사용되는 NAT IP 주소를 식별해야 합니다. 공용 피어링의 경우 기본적으로 각 ExpressRoute 회로는 트래픽이 Microsoft Azure 네트워크 백본으로 들어갈 때 Azure 서비스 트래픽에 적용되는 두 개의 NAT IP 주소를 사용합니다. Microsoft 피어링의 경우 사용되는 NAT IP 주소는 고객이 제공하거나 서비스 공급자가 제공합니다. 서비스 리소스에 대한 액세스를 허용하려면 리소스 IP 방화벽 설정에서 이러한 공용 IP 주소를 허용해야 합니다. 공용 피어링 ExpressRoute 회로 IP 주소를 찾으려면 Azure Portal을 통해 [ExpressRoute에서 지원 티켓을 엽니다](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). [ExpressRoute 공용 및 Microsoft 피어링을 위한 NAT](/azure/expressroute/expressroute-nat#nat-requirements-for-azure-public-peering)에 대해 자세히 알아보세요.

### <a name="managing-ip-network-rules"></a>IP 네트워크 규칙 관리

Azure Portal, PowerShell 또는 CLIv2를 통해 스토리지 계정에 대한 IP 네트워크 규칙을 관리할 수 있습니다.

#### <a name="azure-portal"></a>Azure portal

1. 보호하려는 스토리지 계정으로 이동합니다.

1. **Firewall 및 Virtual Network**이라는 설정 메뉴를 클릭합니다.

1. **선택한 네트워크**에서 액세스를 허용하도록 선택했는지 확인합니다.

1. 인터넷 IP 범위에 대한 액세스 권한을 부여하려면 **방화벽** > 주소 범위 아래에 IP 주소 또는 주소 범위(CIDR 형식)를**입력합니다.**

1. IP 네트워크 규칙을 제거하려면 주소 범위 옆에 있는 휴지통 아이콘을 클릭합니다.

1. **저장**을 클릭하여 변경 내용을 적용합니다.

#### <a name="powershell"></a>PowerShell

1. [Azure PowerShell](/powershell/azure/install-Az-ps)을 설치하고 [로그인](/powershell/azure/authenticate-azureps)합니다.

1. IP 네트워크 규칙을 나열합니다.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. 개별 IP 주소에 대한 네트워크 규칙을 추가합니다.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. IP 주소 범위에 대한 네트워크 규칙을 추가합니다.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. 개별 IP 주소에 대한 네트워크 규칙을 제거합니다.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. IP 주소 범위에 대한 네트워크 규칙을 제거합니다.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 **거부**로 설정해야 합니다. 그렇지 않으면 네트워크 규칙이 적용되지 않습니다.

#### <a name="cliv2"></a>CLIv2

1. [Azure CLI](/cli/azure/install-azure-cli)를 설치하고 [로그인](/cli/azure/authenticate-azure-cli)합니다.

1. IP 네트워크 규칙을 나열합니다.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

1. 개별 IP 주소에 대한 네트워크 규칙을 추가합니다.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. IP 주소 범위에 대한 네트워크 규칙을 추가합니다.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

1. 개별 IP 주소에 대한 네트워크 규칙을 제거합니다.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. IP 주소 범위에 대한 네트워크 규칙을 제거합니다.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 **거부**로 설정해야 합니다. 그렇지 않으면 네트워크 규칙이 적용되지 않습니다.

## <a name="exceptions"></a>예외

네트워크 규칙은 대부분의 시나리오에 대해 응용 프로그램과 데이터 간의 연결을 위한 안전한 환경을 만드는 데 도움이 됩니다. 그러나 일부 응용 프로그램은 가상 네트워크 또는 IP 주소 규칙을 통해 고유하게 격리할 수 없는 Azure 서비스에 의존합니다. 그러나 이러한 서비스는 전체 응용 프로그램 기능을 활성화하기 위해 저장소에 부여되어야 합니다. 이러한 경우 ***신뢰할 수 있는 Microsoft 서비스 허용 설정을*** 사용하여 이러한 서비스가 데이터, 로그 또는 분석에 액세스할 수 있도록 할 수 있습니다.

### <a name="trusted-microsoft-services"></a>신뢰할 수 있는 Microsoft 서비스

일부 Microsoft 서비스는 네트워크 규칙에 포함될 수 없는 네트워크에서 작동합니다. 다른 앱에 대한 네트워크 규칙을 유지하면서 이러한 신뢰할 수 있는 Microsoft 서비스 액세스의 하위 집합을 저장소 계정에 부여할 수 있습니다. 그러면 이러한 신뢰할 수 있는 서비스는 강력한 인증을 사용하여 저장소 계정에 안전하게 연결합니다. Microsoft 서비스에 대한 신뢰할 수 있는 액세스 모드가 두 가지로 설정되었습니다.

- 일부 서비스의 리소스는 **구독에 등록할 때**로그 작성 또는 백업과 같은 선택 작업에 대해 **동일한 구독에서** 저장소 계정에 액세스할 수 있습니다.
- 일부 서비스의 리소스는 시스템에서 할당된 관리되는 ID에 **RBAC 역할을 할당하여** 저장소 계정에 대한 명시적 액세스 권한을 부여할 수 있습니다.


신뢰할 수 **있는 Microsoft 서비스 허용 을** 사용하도록 설정하면 저장소 계정과 동일한 구독에 등록된 다음 서비스의 리소스에 설명된 대로 제한된 작업 집합에 대한 액세스 권한이 부여됩니다.

| 서비스                  | 리소스 공급자 이름     | 허용되는 연산                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure Backup             | Microsoft.RecoveryServices | IAAS 가상 머신에서 관리되지 않는 디스크의 백업 및 복원을 실행합니다. (관리되는 디스크에 필요 없음). [자세히 알아보기](/azure/backup/backup-introduction-to-azure-backup). |
| Azure Data Box           | Microsoft.DataBox          | 데이터 상자를 사용하여 Azure로 데이터를 가져올 수 있습니다. [자세히 알아보기](/azure/databox/data-box-overview). |
| Azure DevTest Labs       | Microsoft.DevTestLab       | 사용자 지정 이미지 만들기 및 아티팩트 설치. [자세히 알아보기](/azure/devtest-lab/devtest-lab-overview). |
| Azure Event Grid         | Microsoft.EventGrid        | Blob Storage 이벤트 게시를 사용하도록 설정하고 Event Grid가 스토리지 큐에 게시하도록 허용합니다. [Blob Storage 이벤트](/azure/event-grid/event-sources)와 [큐에 게시](/azure/event-grid/event-handlers)에 대해 알아봅니다. |
| Azure Event Hubs         | Microsoft.EventHub         | Event Hubs 캡처로 데이터를 보관합니다. [자세한 내용을 알아보십시오.](/azure/event-hubs/event-hubs-capture-overview) |
| Azure 파일 동기화          | Microsoft.StorageSync      | 온-프레임 파일 서버를 Azure File 공유에 대한 캐시로 변환할 수 있습니다. 다중 사이트 동기화, 빠른 재해 복구 및 클라우드 측 백업을 허용합니다. [자세히](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft.HDInsight        | 새 HDInsight 클러스터에 대한 기본 파일 시스템의 초기 내용을 프로비전합니다. [자세히 알아보기](/azure/hdinsight/hdinsight-hadoop-use-blob-storage). |
| Azure 가져오기 내보내기      | Microsoft.ImportExport     | 가져오기/내보내기 서비스를 사용하여 Azure로 데이터를 가져오고 Azure에서 데이터를 내보낼 수 있습니다. [자세히 알아보기](/azure/storage/common/storage-import-export-service).  |
| Azure Monitor            | Microsoft.Insights         | 리소스 진단 로그, Azure Active Directory 로그인 및 감사 로그 및 Microsoft Intune 로그를 포함하여 모니터링 데이터를 보안 저장소 계정에 기록할 수 있습니다. [자세히 알아보기](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security). |
| Azure 네트워킹         | Microsoft.Network          | 네트워크 트래픽 로그를 저장 및 분석합니다. [자세히 알아보기](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview). |
| Azure Site Recovery      | Microsoft.SiteRecovery     | 방화벽 지원 캐시, 소스 또는 대상 저장소 계정을 사용할 때 Azure IaaS 가상 시스템의 재해 복구를 위해 복제를 사용하도록 설정합니다.  [자세히 알아보기](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication). |

**신뢰할 수 있는 Microsoft 서비스 허용...** 설정은 해당 리소스 인스턴스에 대해 시스템에 [할당된 관리되는 ID에](../../active-directory/managed-identities-azure-resources/overview.md) [RBAC 역할을](storage-auth-aad.md#assign-rbac-roles-for-access-rights) 명시적으로 할당하는 경우 아래 서비스의 특정 인스턴스가 저장소 계정에 액세스할 수 있도록 합니다. 이 경우 인스턴스에 대한 액세스 범위는 관리되는 ID에 할당된 RBAC 역할에 해당합니다.

| 서비스                        | 리소스 공급자 이름                 | 용도            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Azure Cognitive Search         | Microsoft.Search/searchServices        | 코그너티브 검색 서비스가 인덱싱, 처리 및 쿼리를 위한 저장소 계정에 액세스할 수 있도록 합니다. |
| Azure Container Registry 작업 | Microsoft.ContainerRegistry/registries | ACR 작업은 컨테이너 이미지를 빌드할 때 저장소 계정에 액세스할 수 있습니다. |
| Azure 데이터 팩터리             | Microsoft.DataFactory/factories        | ADF 런타임을 통해 저장소 계정에 액세스할 수 있습니다. |
| Azure Data Share               | 마이크로소프트.데이터 쉐어/계정           | 데이터 공유를 통해 저장소 계정에 액세스할 수 있습니다. |
| Azure Logic Apps               | Microsoft.Logic/workflows              | 논리 앱이 저장소 계정에 액세스할 수 있도록 합니다. [자세히 알아보기](/azure/logic-apps/create-managed-service-identity#authenticate-access-with-managed-identity). |
| Azure Machine Learning 서비스 | Microsoft.MachineLearningServices      | 권한이 부여된 Azure 기계 학습 작업 영역은 실험 출력, 모델 및 로그를 Blob 저장소에 기록하고 데이터를 읽습니다. [자세히 알아보기](/azure/machine-learning/service/how-to-enable-virtual-network#use-a-storage-account-for-your-workspace). | 
| Azure SQL Data Warehouse       | Microsoft.Sql                          | PolyBase를 사용하여 특정 SQL Database 인스턴스에서 데이터를 가져오고 내보낼 수 있습니다. [자세히 알아보기](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview). |
| Azure Stream Analytics         | Microsoft.StreamAnalytics             | 스트리밍 작업의 데이터를 Blob 저장소에 기록할 수 있습니다. 이 기능은 현재 미리 보기로 제공됩니다. [자세히 알아보기](/azure/stream-analytics/blob-output-managed-identity). |
| Azure Synapse Analytics        | 마이크로소프트.시냅스/작업 공간          | 시냅스 애널리틱스에서 Azure 저장소의 데이터에 액세스할 수 있습니다. |


### <a name="storage-analytics-data-access"></a>스토리지 분석 데이터 액세스

경우에 따라 네트워크 경계 밖에서 진단 로그 및 메트릭을 읽을 수 있는 권한이 필요합니다. 저장소 계정에 대한 신뢰할 수 있는 서비스 액세스를 구성할 때 로그 파일, 메트릭 테이블 또는 둘 다에 대한 읽기 액세스를 허용할 수 있습니다. [스토리지 분석 작업에 대한 자세한 정보](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>예외 관리

Azure Portal, PowerShell 또는 Azure CLI v2를 통해 네트워크 규칙 예외를 관리할 수 있습니다.

#### <a name="azure-portal"></a>Azure portal

1. 보호하려는 스토리지 계정으로 이동합니다.

1. **Firewall 및 Virtual Network**이라는 설정 메뉴를 클릭합니다.

1. **선택한 네트워크**에서 액세스를 허용하도록 선택했는지 확인합니다.

1. **예외** 아래에서 허용하려는 예외를 선택합니다.

1. **저장**을 클릭하여 변경 내용을 적용합니다.

#### <a name="powershell"></a>PowerShell

1. [Azure PowerShell](/powershell/azure/install-Az-ps)을 설치하고 [로그인](/powershell/azure/authenticate-azureps)합니다.

1. 스토리지 계정 네트워크 규칙에 대한 예외를 표시합니다.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. 스토리지 계정 네트워크 규칙에 대한 예외를 구성합니다.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. 스토리지 계정 네트워크 규칙에 대한 예외를 제거합니다.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 **거부**로 설정해야 합니다. 그렇지 않으면 예외를 제거해도 효과가 없습니다.

#### <a name="cliv2"></a>CLIv2

1. [Azure CLI](/cli/azure/install-azure-cli)를 설치하고 [로그인](/cli/azure/authenticate-azure-cli)합니다.

1. 스토리지 계정 네트워크 규칙에 대한 예외를 표시합니다.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

1. 스토리지 계정 네트워크 규칙에 대한 예외를 구성합니다.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

1. 스토리지 계정 네트워크 규칙에 대한 예외를 제거합니다.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 **거부**로 설정해야 합니다. 그렇지 않으면 예외를 제거해도 효과가 없습니다.

## <a name="next-steps"></a>다음 단계

[서비스 엔드포인트](/azure/virtual-network/virtual-network-service-endpoints-overview)에서 Azure 네트워크 서비스 엔드포인트에 대해 자세히 알아봅니다.

[Azure Storage 보안 가이드](../blobs/security-recommendations.md)에서 Azure Storage 보안에 대해 자세히 살펴봅니다.
