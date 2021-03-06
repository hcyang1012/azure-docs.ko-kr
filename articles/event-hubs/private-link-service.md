---
title: Azure 이벤트 허브를 Azure 개인 링크 서비스와 통합
description: Azure 이벤트 허브를 Azure 개인 링크 서비스와 통합하는 방법에 대해 알아봅니다.
services: event-hubs
author: spelluru
ms.author: spelluru
ms.date: 03/12/2020
ms.service: event-hubs
ms.topic: article
ms.openlocfilehash: bcc360bbe4dd58200993b9377317ccb608b3529d
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383639"
---
# <a name="integrate-azure-event-hubs-with-azure-private-link-preview"></a>Azure 이벤트 허브를 Azure 개인 링크와 통합(미리 보기)
Azure 개인 링크 서비스를 사용하면 가상 네트워크의 **개인 끝점을** 통해 Azure 서비스(예: Azure 이벤트 허브, Azure Storage 및 Azure Cosmos DB) 및 Azure 호스팅 고객/파트너 서비스에 액세스할 수 있습니다.

개인 끝점은 Azure 개인 링크에서 제공하는 서비스에 개인적으로 안전하게 연결하는 네트워크 인터페이스입니다. 프라이빗 엔드포인트는 VNet의 개인 IP 주소를 사용하여 서비스를 VNet으로 효과적으로 가져옵니다. 서비스에 대한 모든 트래픽은 프라이빗 엔드포인트를 통해 라우팅할 수 있으므로 게이트웨이, NAT 디바이스, ExpressRoute 또는 VPN 연결 또는 공용 IP 주소가 필요하지 않습니다. 가상 네트워크와 서비스 간의 트래픽은 Microsoft 백본 네트워크를 통해 이동하여 공용 인터넷에서 노출을 제거합니다. Azure 리소스의 인스턴스에 연결하여 액세스 제어에서 가장 높은 수준의 세분성을 제공할 수 있습니다.

자세한 내용은 [Azure Private Link란?](../private-link/private-link-overview.md)을 참조하세요.

> [!NOTE]
> 이 기능은 **전용** 계층에서만 지원됩니다. 전용 계층에 대한 자세한 내용은 [이벤트 허브 전용 개요를](event-hubs-dedicated-overview.md)참조하십시오. 
>
> 이 기능은 현재 **미리 보기**상태입니다. 


## <a name="add-a-private-endpoint-using-azure-portal"></a>Azure 포털을 사용하여 개인 끝점 추가

### <a name="prerequisites"></a>사전 요구 사항

이벤트 허브 네임스페이스를 Azure 개인 링크와 통합하려면 다음 엔터티 또는 사용 권한이 필요합니다.

- Event Hubs 네임스페이스
- Azure 가상 네트워크
- 가상 네트워크의 서브넷
- 네임스페이스와 가상 네트워크 모두에 대한 소유자 또는 기여자 권한입니다.

프라이빗 엔드포인트와 가상 네트워크는 동일한 지역에 있어야 합니다. 포털을 사용하여 프라이빗 엔드포인트에 대한 영역을 선택하면 해당 지역에 있는 가상 네트워크만 자동으로 필터링됩니다. 네임스페이스는 다른 지역에 있을 수 있습니다.

프라이빗 엔드포인트는 가상 네트워크에서 개인 IP 주소를 사용합니다.

### <a name="steps"></a>단계
이벤트 허브 네임스페이스가 이미 있는 경우 다음 단계를 수행하여 개인 링크 연결을 만들 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. 검색 표시줄에서 **이벤트 허브를 입력합니다.**
3. 개인 끝점을 추가할 목록에서 **네임스페이스를** 선택합니다.
4. **설정**에서 **네트워킹** 탭을 선택합니다.
5. 페이지 상단의 **비공개 끝점 연결(미리 보기)** 탭을 선택합니다. 이벤트 허브의 전용 계층을 사용하지 않는 경우 **이벤트 허브의 개인 끝점 연결은 전용 클러스터에서 만든 네임스페이스에서만 지원됩니다.**
6. 페이지 상단의 **+ 비공개 끝점** 버튼을 선택합니다.

    ![이미지](./media/private-link-service/private-link-service-3.png)
7. 기본 사항 페이지에서 다음 단계를 **수행합니다.** 
    1. 개인 끝점을 만들 려는 **Azure 구독을** 선택합니다. 
    2. 개인 끝점 리소스에 대한 **리소스 그룹을** 선택합니다.
    3. 전용 끝점의 **이름을** 입력합니다. 
    5. 전용 끝점에 대한 **영역을** 선택합니다. 개인 끝점은 가상 네트워크와 동일한 지역에 있어야 하지만 연결하는 개인 링크 리소스가 다른 지역에 있을 수 있습니다. 
    6. 다음: 페이지 하단의 리소스 >단추를 **선택합니다.**

        ![비공개 끝점 만들기 - 기본 페이지](./media/private-link-service/create-private-endpoint-basics-page.png)
8. **리소스** 페이지에서 다음 단계를 수행합니다.
    1. 연결 방법의 경우 **내 디렉터리에서 Azure 리소스에 대한 연결을**선택하면 다음 단계를 따르십시오. 
        1. **이벤트 허브 네임스페이스가** 있는 Azure **구독을** 선택합니다. 
        2. **리소스 유형의**경우 리소스 유형에 대한 **Microsoft.EventHub/네임스페이스를** **선택합니다.**
        3. **리소스의**경우 드롭다운 목록에서 이벤트 허브 네임스페이스를 선택합니다. 
        4. **대상 하위 리소스가** **네임스페이스로**설정되어 있는지 확인합니다.
        5. 다음: 페이지 하단의 구성 >단추를 **선택합니다.** 
        
            ![개인 끝점 만들기 - 리소스 페이지](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. **리소스 ID 또는 별칭으로 Azure 리소스에 연결(연결)을**선택한 경우 다음 단계를 따르십시오.
        1. 리소스 **ID** 또는 **별칭을**입력합니다. 일부 사용자와 공유한 리소스 ID 또는 별칭일 수 있습니다.
        2. **대상 하위 리소스의**경우 **네임스페이스를**입력합니다. 개인 끝점이 액세스할 수 있는 하위 리소스의 유형입니다.
        3. (선택 사항) 요청 **메시지를**입력합니다. 리소스 소유자는 개인 끝점 연결을 관리하는 동안 이 메시지를 봅니다.
        4. 그런 다음 페이지 하단의 구성 >단추를 **선택합니다.**

            ![개인 엔드포인트 만들기 - 리소스 ID를 사용하여 연결](./media/private-link-service/connect-resource-id.png)
9. **구성** 페이지에서 가상 네트워크에서 개인 끝점을 배포할 서브넷을 선택합니다. 
    1. 가상 **네트워크를**선택합니다. 현재 선택한 구독 및 위치의 가상 네트워크만 드롭다운 목록에 나열됩니다. 
    2. 선택한 가상 네트워크에서 **서브넷을** 선택합니다. 
    3. **다음:** 페이지 하단에 >버튼에 태그를 붙입니다. 

        ![개인 끝점 만들기 - 구성 페이지](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. **태그** 페이지에서 개인 끝점 리소스와 연결할 태그(이름 및 값)를 만듭니다. 그런 다음 페이지 하단에 **있는 검토 + 만들기** 버튼을 선택합니다. 
11. 검토 **+ 만들기**, 모든 설정을 검토하고 **만들기를** 선택하여 비공개 끝점을 만듭니다.
    
    ![비공개 엔드포인트 만들기 - 검토 및 만들기 페이지](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. 만든 개인 끝점 연결이 끝점 목록에 표시되는지 확인합니다. 이 예제에서는 디렉터리에서 Azure 리소스에 연결하고 충분한 권한이 있기 때문에 개인 끝점이 자동으로 승인됩니다. 

    ![만든 전용 끝점](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>PowerShell을 사용하여 개인 끝점 추가
다음 예제에서는 Azure PowerShell을 사용하여 개인 끝점 연결을 만드는 방법을 보여 주며 있습니다. 전용 클러스터를 만들지 않습니다. [이 문서의](event-hubs-dedicated-cluster-create-portal.md) 단계를 수행하여 전용 이벤트 허브 클러스터를 만듭니다. 

```azurepowershell-interactive
# create resource group

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VIRTUAL NETWORK LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName $rgName `
                    -Location $vnetlocation `
                    -Name $vnetName `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name $subnetName `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork

# create an event hubs namespace in a dedicated cluster
$namespaceResource = New-AzResource -Location $namespaceLocation `
                                    -ResourceName $namespaceName `
                                    -ResourceGroupName $rgName `
                                    -Sku @{name = "Standard"; capacity = 1} `
                                    -Properties @{clusterArmId = "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/clusters/<EVENT HUBS CLUSTER NAME>"} `
                                    -ResourceType "Microsoft.EventHub/namespaces" -ApiVersion "2018-01-01-preview"

# create private endpoint connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use later
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# create a private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


```

### <a name="configure-the-private-dns-zone"></a>개인 DNS 영역 구성
이벤트 허브 도메인에 대한 개인 DNS 영역을 만들고 가상 네트워크와의 연결 링크를 만듭니다.

```azurepowershell-interactive
$zone = New-AzPrivateDnsZone -ResourceGroupName $rgName `
                            -Name "privatelink.servicebus.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $rgName `
                                            -ZoneName "privatelink.servicebus.windows.net" `
                                            -Name "mylink" `
                                            -VirtualNetworkId $virtualNetwork.Id  
 
$networkInterface = Get-AzResource -ResourceId $privateEndpoint.NetworkInterfaces[0].Id -ApiVersion "2019-04-01" 
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
        Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
        $recordName = $fqdn.split('.',2)[0] 
        $dnsZone = $fqdn.split('.',2)[1] 
        New-AzPrivateDnsRecordSet -Name $recordName -RecordType A -ZoneName "privatelink.servicebus.windows.net"  `
                                -ResourceGroupName $rgName -Ttl 600 `
                                -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $ipconfig.properties.privateIPAddress)  
    } 
}
```

## <a name="manage-private-endpoints-using-azure-portal"></a>Azure 포털을 사용하여 개인 끝점 관리

프라이빗 엔드포인트를 만들 때 연결이 승인되어야 합니다. 개인 끝점을 만드는 리소스가 디렉터리에 있는 경우 충분한 권한이 있는 경우 연결 요청을 승인할 수 있습니다. 다른 디렉터리의 Azure 리소스에 연결하는 경우 해당 리소스의 소유자가 연결 요청을 승인할 때까지 기다려야 합니다.

다음 네 가지 프로비저닝 상태가 있습니다.

| 서비스 작업 | 서비스 소비자 프라이빗 엔드포인트 상태 | 설명 |
|--|--|--|
| 없음 | Pending | 연결이 수동으로 만들어지고, Private Link 리소스 소유자의 승인이 보류 중입니다. |
| 승인 | 승인됨 | 연결이 자동 또는 수동으로 승인되었으며, 사용할 준비가 되었습니다. |
| 거부 | 거부됨 | Private Link 리소스 소유자가 연결을 거부했습니다. |
| 제거 | 연결 끊김 | Private Link 리소스 소유자가 연결을 제거했습니다. 프라이빗 엔드포인트는 정보를 제공하므로 정리를 위해 삭제해야 합니다. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>개인 끝점 연결 승인, 거부 또는 제거

1. Azure Portal에 로그인합니다.
2. 검색 표시줄에서 **이벤트 허브를 입력합니다.**
3. 관리할 **네임스페이스를** 선택합니다.
4. 네트워킹 탭을 **선택합니다.**
5. 승인, 거부 또는 제거하려는 작업에 따라 아래 해당 섹션으로 이동합니다.

### <a name="approve-a-private-endpoint-connection"></a>개인 끝점 연결 승인
1. 보류 중인 연결이 있는 경우 프로비저닝 상태에서 **보류 중으로** 나열된 연결이 표시됩니다. 
2. 승인할 **전용 끝점을** 선택합니다.
3. **승인** 단추를 선택합니다.

    ![이미지](./media/private-link-service/approve-private-endpoint.png)
4. 연결 **승인** 페이지에서 주석(선택 사항)을 추가하고 **예**를 선택합니다. **아니요를**선택하면 아무 일도 발생하지 않습니다. 
5. 목록에서 개인 끝점 연결의 상태가 **승인됨으로**변경된 것을 볼 수 있습니다. 

### <a name="reject-a-private-endpoint-connection"></a>개인 끝점 연결 거부

1. 거부할 개인 끝점 연결이 있는 경우 보류 중인 요청이든 기존 연결이든 관계없이 연결을 선택하고 **거부** 단추를 클릭합니다.

    ![이미지](./media/private-link-service/private-endpoint-reject-button.png)
2. 연결 **거부** 페이지에서 주석(선택 사항)을 입력하고 **예**. **아니요를**선택하면 아무 일도 발생하지 않습니다. 
3. 목록에서 개인 끝점 연결의 상태가 **거부됨으로**변경된 것을 볼 수 있습니다. 

### <a name="remove-a-private-endpoint-connection"></a>개인 끝점 연결 제거

1. 개인 끝점 연결을 제거하려면 목록에서 선택하고 도구 모음에서 **제거를** 선택합니다.
2. 연결 **삭제** 페이지에서 **예를** 선택하여 개인 끝점의 삭제를 확인합니다. **아니요를**선택하면 아무 일도 발생하지 않습니다.
3. **상태가 연결이 끊긴**것으로 변경된 것을 볼 수 있습니다. 그런 다음 목록에서 끝점이 사라지는 것을 볼 수 있습니다.

## <a name="validate-that-the-private-link-connection-works"></a>프라이빗 링크 연결이 작동하는지 확인

개인 끝점 리소스의 동일한 서브넷 내의 리소스가 개인 IP 주소를 통해 이벤트 허브 네임스페이스에 연결되고 올바른 개인 DNS 영역 통합이 있는지 확인해야 합니다.

먼저 [Azure Portal에서 Windows 가상 머신 만들기](../virtual-machines/windows/quick-create-portal.md)의 단계에 따라 가상 머신을 만듭니다.

**네트워킹** 탭에서:

1. **가상 네트워크** 및 **서브넷을**지정합니다. 새 가상 네트워크를 만들거나 기존 가상 네트워크를 선택할 수 있습니다. 기존 가상 네트워크를 선택하는 경우 지역이 일치하는지 확인합니다.
1. 공용 **IP** 리소스를 지정합니다.
1. NIC **네트워크 보안 그룹에서** **없음을**선택합니다.
1. 로드 **균형**조정에서 **아니요를**선택합니다.

명령줄을 열고 다음 명령을 실행합니다.

```console
nslookup <your-event-hubs-namespace-name>.servicebus.windows.net
```

ns 조회 명령을 실행하여 공용 끝점을 통해 Event Hubs 네임스페이스의 IP 주소를 해결하면 다음과 같은 결과가 표시됩니다.

```console
c:\ >nslookup <your-event-hubs-namespae-name>.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-event-hubs-namespace-name>.servicebus.windows.net
```

ns 조회 명령을 실행하여 개인 끝점을 통해 Event Hubs 네임스페이스의 IP 주소를 해결하면 다음과 같은 결과가 표시됩니다.

```console
c:\ >nslookup your_event-hubs-namespace-name.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-event-hub-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>제한 사항 및 설계 고려 사항

**가격 책정**: 가격 정보는 [Azure 개인 링크 가격 책정을](https://azure.microsoft.com/pricing/details/private-link/)참조하십시오.

**제한 사항**: Azure 이벤트 허브에 대한 개인 끝점이 공개 미리 보기입니다. 이 기능은 모든 Azure 퍼블릭 지역에서 사용할 수 있습니다.

**이벤트 허브 네임스페이스당 최대 개인 끝점 수**: 120개.

자세한 내용은 [Azure 개인 링크 서비스: 제한 사항](../private-link/private-link-service-overview.md#limitations) 참조

## <a name="next-steps"></a>다음 단계

- [Azure Private Link](../private-link/private-link-service-overview.md)에 대해 자세히 알아봅니다.
- [Azure 이벤트 허브에](event-hubs-about.md) 대해 자세히 알아보기
