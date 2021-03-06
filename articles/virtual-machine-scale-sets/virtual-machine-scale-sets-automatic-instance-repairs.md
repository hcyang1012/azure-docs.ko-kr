---
title: Azure 가상 시스템 규모 집합을 통한 자동 인스턴스 복구
description: 스케일 세트에서 VM 인스턴스에 대한 자동 복구 정책을 구성하는 방법 알아보기
author: avirishuv
manager: vashan
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: avverma
ms.openlocfilehash: 8156c563573183e51e06650914117f8787922e93
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81603671"
---
# <a name="automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Azure 가상 시스템 규모 집합에 대한 자동 인스턴스 복구

Azure 가상 시스템 크기 집합에 대한 자동 인스턴스 복구를 사용하면 정상 인스턴스 집합을 유지 관리하여 응용 프로그램의 가용성을 높은 가용성으로 얻을 수 있습니다. 배율 집합의 인스턴스가 [응용 프로그램 상태 확장](./virtual-machine-scale-sets-health-extension.md) 또는 로드 [밸런서 상태 프로브에서](../load-balancer/load-balancer-custom-probe-overview.md)보고한 대로 비정상으로 판명되면 이 기능은 비정상 인스턴스를 삭제하고 이를 대체할 새 인스턴스를 만들어 인스턴스 복구를 자동으로 수행합니다.

## <a name="requirements-for-using-automatic-instance-repairs"></a>자동 인스턴스 복구 사용에 대한 요구 사항

**스케일 세트에 대한 애플리케이션 상태 모니터링 지원**

규모 집합에는 인스턴스에 대한 응용 프로그램 상태 모니터링이 활성화되어 있어야 합니다. 이 작업은 응용 [프로그램 상태 확장](./virtual-machine-scale-sets-health-extension.md) 또는 [로드 밸러서 상태 프로브를](../load-balancer/load-balancer-custom-probe-overview.md)사용하여 수행할 수 있습니다. 이 중 하나만 한 번에 활성화할 수 있습니다. 응용 프로그램 상태 확장 또는 로드 밸러버 프로브는 가상 시스템 인스턴스에서 구성된 응용 프로그램 끝점을 ping하여 응용 프로그램 상태 상태를 확인합니다. 이 상태는 규모 집합 오케스트레이터에서 인스턴스 상태를 모니터링하고 필요한 경우 복구를 수행하는 데 사용됩니다.

**상태 상태를 제공하도록 끝점 구성**

자동 인스턴스 복구 정책을 사용하도록 설정하기 전에 확장 집합 인스턴스에 응용 프로그램 상태 상태를 내보도록 구성된 응용 프로그램 끝점이 있는지 확인합니다. 인스턴스가 이 응용 프로그램 끝점에서 상태 200(확인)을 반환하면 인스턴스가 "정상"으로 표시됩니다. 다른 모든 경우 인스턴스는 다음 시나리오를 포함하여 "비정상"으로 표시됩니다.

- 가상 시스템 인스턴스 내에 응용 프로그램 상태 상태를 제공하도록 구성된 응용 프로그램 끝점이 없는 경우
- 응용 프로그램 끝점이 잘못 구성된 경우
- 응용 프로그램 끝점에 연결할 수 없는 경우

"비정상"으로 표시된 인스턴스의 경우 배율 집합에 의해 자동 복구가 트리거됩니다. 끝점이 구성되는 동안 의도하지 않은 인스턴스 복구를 방지하기 위해 자동 복구 정책을 사용하도록 설정하기 전에 응용 프로그램 끝점이 올바르게 구성되었는지 확인합니다.

**단일 배치 그룹 사용**

이 기능은 현재 단일 배치 그룹으로 배포된 축척 집합에만 사용할 수 있습니다. 속성 *singlePlacementGroup* 자동 인스턴스 복구 기능을 사용 하 여 축척 집합에 대 한 *true로* 설정 해야 합니다. [게재위치 그룹에](./virtual-machine-scale-sets-placement-groups.md#placement-groups)대해 자세히 알아보세요.

**API 버전**

자동 복구 정책은 2018-10-01 이상의 계산 API 버전에 대해 지원됩니다.

**리소스 또는 구독 이동 제한**

자동 복구 기능이 활성화되어 있는 경우 현재 확장 집합에는 리소스 또는 구독 이동이 지원되지 않습니다.

**서비스 패브릭 스케일 세트 제한**

이 기능은 현재 서비스 패브릭 스케일 집합에 대해 지원되지 않습니다.

## <a name="how-do-automatic-instance-repairs-work"></a>자동 인스턴스 복구는 어떻게 작동합니까?

자동 인스턴스 복구 기능은 축척 집합의 개별 인스턴스의 상태 모니터링에 의존합니다. 확장 집합의 VM 인스턴스는 응용 프로그램 [상태 확장](./virtual-machine-scale-sets-health-extension.md) 또는 [로드 밸러버 상태 프로브를](../load-balancer/load-balancer-custom-probe-overview.md)사용하여 응용 프로그램 상태 상태를 내보도록 구성할 수 있습니다. 인스턴스가 비정상으로 판명되면 배율 집합은 비정상 인스턴스를 삭제하고 이를 대체할 새 인스턴스를 만들어 복구 작업을 수행합니다. 최신 가상 시스템 규모 집합 모델은 새 인스턴스를 만드는 데 사용됩니다. 이 기능은 *자동RepairsPolicy 개체를* 사용하여 가상 시스템 스케일 집합 모델에서 사용할 수 있습니다.

### <a name="batching"></a>일괄 처리

자동 인스턴스 복구 작업은 일괄 처리로 수행됩니다. 지정된 시간에 스케일 집합의 인스턴스 중 5% 이하는 자동 수리 정책을 통해 복구됩니다. 이렇게 하면 동시에 비정상으로 발견되는 경우 많은 수의 인스턴스를 동시에 삭제하고 다시 만드는 것을 방지할 수 있습니다.

### <a name="grace-period"></a>유예 기간

인스턴스가 SCALE 집합에서 수행된 PUT, PATCH 또는 POST 작업(예: 이미지 다시 이미지, 재배포, 업데이트 등)으로 인해 상태 변경 작업을 거치면 해당 인스턴스의 모든 복구 작업은 유예 기간을 기다린 후에만 수행됩니다. 유예 기간은 인스턴스가 정상 상태로 돌아갈 수 있도록 하는 시간입니다. 유예 기간은 상태 변경이 완료된 후 시작됩니다. 이렇게 하면 조기 또는 우발적인 수리 작업을 방지할 수 있습니다. 유예 기간은 축척 집합에서 새로 생성된 인스턴스(수리 작업의 결과로 생성된 인스턴스 포함)에 대해 적용됩니다. 유예 기간은 ISO 8601 형식으로 분 단위로 지정되며 속성 *자동RepairsPolicy.gracePeriod를*사용하여 설정할 수 있습니다. 유예 기간은 30분에서 90분 사이이며 기본값은 30분입니다.

### <a name="suspension-of-repairs"></a>수리 정지 

가상 시스템 크기 집합은 필요한 경우 자동 인스턴스 복구를 일시적으로 일시 중단하는 기능을 제공합니다. *serviceState속성* *오케스트레이션서비스* 가상 시스템 크기 집합의 인스턴스 뷰에서 자동 수리에 대한 상태는 자동 복구의 현재 상태를 표시합니다. 배율 집합을 자동 복구로 선택하면 매개 변수 *serviceState* 값이 *실행*으로 설정됩니다. 축척 집합에 대해 자동 복구가 일시 중단되면 매개 변수 *serviceState가* *일시 중단됨으로*설정됩니다. *자동RepairsPolicy는* 축척 집합에 정의되어 있지만 자동 복구 기능을 사용할 수 없는 경우 매개 변수 *serviceState가* *실행되지 않음으로*설정됩니다.

새로 만든 인스턴스를 크기 집합에서 비정상 상태로 대체하는 경우 복구 작업을 반복적으로 수행한 후에도 비정상 상태로 계속 유지되는 경우 안전 측정으로 플랫폼은 일시 *중단된*자동 복구를 위해 *serviceState를* 업데이트합니다. *service값을* 설정하여 자동 복구를 다시 다시 시작할 수 있습니다.실행 중인 *자동*복구에 대 한 상태 스케일 집합에 대한 [자동 수리 정책의 서비스 상태를 보고 업데이트하는](#viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy) 방법에 대한 자세한 지침은 섹션에 나와 있습니다. 

자동 인스턴스 복구 프로세스는 다음과 같이 작동합니다.

1. [응용 프로그램 상태 확장](./virtual-machine-scale-sets-health-extension.md) 또는 [로드 밸런서 상태 프로브는](../load-balancer/load-balancer-custom-probe-overview.md) 스케일 세트의 각 가상 시스템 내부의 응용 프로그램 끝점을 ping하여 각 인스턴스에 대한 응용 프로그램 상태 상태를 가져옵니다.
2. 끝점이 상태 200(OK)으로 응답하면 인스턴스가 "정상"으로 표시됩니다. 다른 모든 경우(끝점에 연결할 수 없는 경우 포함)에서 인스턴스는 "비정상"으로 표시됩니다.
3. 인스턴스가 비정상으로 판명되면 크기 조정 집합은 비정상 인스턴스를 삭제하고 새 인스턴스를 만들어 복구 작업을 트리거합니다.
4. 인스턴스 복구는 일괄 처리로 수행됩니다. 지정된 시간에 스케일 집합의 총 인스턴스 중 5% 이하가 복구됩니다. 규모 집합에 인스턴스가 20개 미만인 경우 한 번에 하나의 비정상 인스턴스에 대해 복구가 수행됩니다.
5. 위의 프로세스는 스케일 집합의 모든 비정상 인스턴스가 복구될 때까지 계속됩니다.

## <a name="instance-protection-and-automatic-repairs"></a>인스턴스 보호 및 자동 수리

확장 집합의 인스턴스가 [보호 정책](./virtual-machine-scale-sets-instance-protection.md)중 하나를 적용하여 보호되는 경우 해당 인스턴스에서 자동 복구가 수행되지 않습니다. 이는 확장 설정 *작업으로부터 보호및* *보호라는* 보호 정책 모두에 적용됩니다. 

## <a name="terminatenotificationandautomaticrepairs"></a>알림 및 자동 수리 종료

확장 집합에서 [종료 알림](./virtual-machine-scale-sets-terminate-notification.md) 기능이 활성화된 경우 자동 복구 작업 중에 비정상 인스턴스의 삭제는 종료 알림 구성을 따릅니다. 종료 알림은 Azure 메타데이터 서비스(예약된 이벤트)를 통해 전송되고 구성된 지연 시간 동안 인스턴스 삭제가 지연됩니다. 그러나 비정상 인스턴스를 대체하는 새 인스턴스를 만들면 지연 시간 시간이 완료될 때까지 기다리지 않습니다.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>새 축척 세트를 만들 때 자동 복구 정책 사용

새 축척 집합을 만드는 동안 자동 수리 정책을 사용하도록 설정하려면 이 기능에 옵트인하기 위한 모든 [요구 사항이](#requirements-for-using-automatic-instance-repairs) 충족되었는지 확인합니다. 끝점이 구성되는 동안 의도하지 않은 복구가 트리거되지 않도록 확장 집합 인스턴스에 대해 응용 프로그램 끝점을 올바르게 구성해야 합니다. 새로 생성된 축척 집합의 경우 모든 인스턴스 복구는 유예 기간 동안 기다린 후에만 수행됩니다. 축척 집합에서 자동 인스턴스 복구를 사용하려면 가상 시스템 축척 집합 모델에서 *자동수리정책* 개체를 사용합니다.

또한 이 [빠른 시작 템플릿을](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-automatic-repairs-slb-health-probe) 사용하여 로드 밸러저 상태 프로브와 30분의 유예 기간을 사용하도록 설정된 자동 인스턴스 복구가 있는 가상 시스템 스케일 집합을 배포할 수도 있습니다.

### <a name="azure-portal"></a>Azure portal
 
다음 단계는 새 축척 집합을 만들 때 자동 복구 정책을 사용하도록 설정합니다.
 
1. 가상 **컴퓨터 크기 집합으로**이동합니다.
1. **+ 추가를** 선택하여 새 축척 세트를 만듭니다.
1. **상태** 탭으로 이동합니다. 
1. **상태** 섹션을 찾습니다.
1. 응용 **프로그램 상태 모니터링** 옵션을 사용하도록 설정합니다.
1. 자동 **복구 정책** 섹션을 찾습니다.
1. **자동 수리** 옵션을 **켭니다.**
1. **유예 기간(min)에서는**분으로 유예 기간을 지정하고 허용값은 30분에서 90분 사이입니다. 
1. 새 축척 세트 작성이 완료되면 **검토 + 만들기** 단추를 선택합니다.

### <a name="rest-api"></a>REST API

다음 예제에서는 축척 집합 모델에서 자동 인스턴스 복구를 활성화하는 방법을 보여 주습니다. API 버전 2018-10-01 이상에서 사용합니다.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT30M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

[새 AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) cmdlet을 사용하여 새 축척 세트를 만드는 동안 자동 인스턴스 복구 기능을 사용할 수 있습니다. 이 샘플 스크립트는 구성 파일: [전체 가상 시스템 축척 집합 만들기를](./scripts/powershell-sample-create-complete-scale-set.md)사용하여 축척 집합 및 관련 리소스를 만드는 데 대해 살펴봅니다. [자동 *복구]* 및 *자동 수리GracePeriod* 매개 변수를 구성 개체에 추가하여 자동 인스턴스 복구 정책을 구성할 수 있습니다. 다음 예제에서는 30분의 유예 기간을 가진 기능을 활성화합니다.

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

다음 예제는 *[az vms를](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)* 사용하여 새 축척 집합을 만드는 동안 자동 복구 정책을 활성화합니다. 먼저 리소스 그룹을 만든 다음 자동 수리 정책 유예 기간을 30분으로 설정한 새 축척 집합을 만듭니다.

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --load-balancer <existingLoadBalancer> \
  --health-probe <existingHealthProbeUnderLoaderBalancer> \
  --automatic-repairs-period 30
```

위의 예제에서는 인스턴스의 응용 프로그램 상태 모니터링을 위해 기존 로드 밸런서 및 상태 프로브를 사용합니다. 대신 모니터링에 응용 프로그램 상태 확장을 사용 하려는 경우 다음 섹션에서 설명한 대로 *az vmss 업데이트를*사용 하 여 자동 인스턴스 복구 정책을 사용 하 여 배율 집합을 만들고 응용 프로그램 상태 확장을 구성 할 수 있습니다.

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>기존 축척 세트를 업데이트할 때 자동 복구 정책 사용

기존 축척 집합에서 자동 수리 정책을 사용하도록 설정하기 전에 이 기능에 옵트인하기 위한 모든 [요구 사항이](#requirements-for-using-automatic-instance-repairs) 충족되었는지 확인합니다. 끝점이 구성되는 동안 의도하지 않은 복구가 트리거되지 않도록 확장 집합 인스턴스에 대해 응용 프로그램 끝점을 올바르게 구성해야 합니다. 축척 집합에서 자동 인스턴스 복구를 사용하려면 가상 시스템 축척 집합 모델에서 *자동수리정책* 개체를 사용합니다.

기존 축척 세트의 모델을 업데이트한 후 최신 모델이 스케일의 모든 인스턴스에 적용되는지 확인합니다. [최신 스케일 집합 모델로 VM을 최신 상태로 설정하는 방법에 대한](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)지침을 참조하십시오.

### <a name="azure-portal"></a>Azure portal

Azure 포털을 통해 설정된 기존 축척의 자동 복구 정책을 수정할 수 있습니다. 
 
1. 기존 가상 시스템 규모 집합으로 이동합니다.
1. 왼쪽 메뉴의 **설정에서** 상태 **및 복구를**선택합니다.
1. 응용 **프로그램 상태 모니터링** 옵션을 사용하도록 설정합니다.
1. 자동 **복구 정책** 섹션을 찾습니다.
1. **자동 수리** 옵션을 **켭니다.**
1. **유예 기간(min)에서는**분으로 유예 기간을 지정하고 허용값은 30분에서 90분 사이입니다. 
1. 작업이 완료되면 **저장**을 선택합니다. 

### <a name="rest-api"></a>REST API

다음 예제는 40분의 유예 기간을 가진 정책을 활성화합니다. API 버전 2018-10-01 이상에서 사용합니다.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT40M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

[Update-AzVmss](/powershell/module/az.compute/update-azvmss) cmdlet을 사용하여 기존 축척 집합에서 자동 인스턴스 복구 피쳐의 구성을 수정합니다. 다음 예제는 유예 기간을 40분으로 업데이트합니다.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

다음은 *[az vmss 업데이트를](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-update)* 사용하여 기존 축척 집합의 자동 인스턴스 복구 정책을 업데이트하는 예제입니다.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-automatic-repairs true \
  --automatic-repairs-period 30
```

## <a name="viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy"></a>자동 인스턴스 복구 정책의 서비스 상태 보기 및 업데이트

### <a name="rest-api"></a>REST API 

API 버전 2019-12-01 이상으로 [인스턴스 보기 받기를](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getinstanceview) 사용하여 가상 시스템 규모 집합에 대해 *serviceState를* 보려면 속성 *오케스트레이션서비스*에서 자동 수리를 위해 . 

```http
GET '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version=2019-12-01'
```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Running"
    }
  ]
}
```

자동 복구 상태를 설정하려면 가상 시스템 크기 집합에서 API 버전 2019-12-01 이상의 *setOrchestrationServiceState* API를 사용합니다. 확장 집합이 자동 복구 기능으로 선택되면 이 API를 사용하여 스케일 집합에 대한 자동 복구를 일시 중단하거나 다시 시작할 수 있습니다. 

 ```http
 POST '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/setOrchestrationServiceState?api-version=2019-12-01'
 ```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Suspend"
    }
  ]
}
```

### <a name="azure-cli"></a>Azure CLI 

[get-인스턴스 보기](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-get-instance-view) cmdlet을 사용하여 자동 인스턴스 복구의 *서비스 상태를* 봅니다. 

```azurecli-interactive
az vmss get-instance-view \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```

[집합 오케스트레이션 서비스 상태](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-set-orchestration-service-state) cmdlet을 사용하여 자동 인스턴스 복구를 위해 *serviceState를* 업데이트합니다. 축척 세트를 자동 복구 기능으로 선택한 다음 이 cmdlet을 사용하여 확장 집합에 대한 자동 수리를 일시 중단하거나 다시 시작할 수 있습니다. 

```azurecli-interactive
az vmss set-orchestration-service-state \
    --service-name AutomaticRepairs \
    --action Resume \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```
### <a name="azure-powershell"></a>Azure PowerShell

매개 변수 *InstanceView와* [함께 Get-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss?view=azps-3.7.0) cmdlet을 사용하여 자동 인스턴스 복구를 위해 *ServiceState를* 봅니다.

```azurepowershell-interactive
Get-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -InstanceView
```

설정 AzVmssOrchestrationState cmdlet을 사용하여 자동 인스턴스 복구를 위해 *서비스상태를* 업데이트합니다. 축척 세트를 자동 복구 기능으로 선택한 후에는 이 cmdlet을 사용하여 스케일 세트를 위해 자동 수리를 일시 중단하거나 다시 시작할 수 있습니다.

```azurepowershell-interactive
Set-AzVmssOrchestrationServiceState `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -ServiceName "AutomaticRepairs" `
    -Action "Suspend"
```

## <a name="troubleshoot"></a>문제 해결

**자동 복구 정책을 사용하도록 설정하지 못했습니다.**

'속성' 형식의 개체에서 '자동 수리 정책'이라는 메시지가 있는 'BadRequest' 오류가 발생하면 가상 시스템 규모 집합에 사용되는 API 버전을 확인합니다. 이 기능에는 API 버전 2018-10-01 이상의 기능이 필요합니다.

**정책이 활성화된 경우에도 인스턴스가 복구되지 않음**

인스턴스는 유예 기간에 있을 수 있습니다. 복구를 수행하기 전에 인스턴스의 상태가 변경된 후 대기하는 시간입니다. 이는 조기 또는 우발적인 수리를 피하기 위한 것입니다. 인스턴스에 대한 유예 기간이 완료되면 복구 작업이 수행되어야 합니다.

**스케일 세트 인스턴스에 대한 응용 프로그램 상태 보기**

가상 시스템 규모 집합의 인스턴스에 [대해 인스턴스 보기 받기 API를](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) 사용하여 응용 프로그램 상태 상태를 볼 수 있습니다. Azure PowerShell을 사용하면 *-InstanceView* 플래그를 사용하여 cmdlet [Get-AzVmssVM을](/powershell/module/az.compute/get-azvmssvm) 사용할 수 있습니다. 응용 프로그램 상태는 속성 *vmHealth*에서 제공됩니다.

Azure 포털에서 상태 상태도 볼 수 있습니다. 기존 축척 집합으로 이동하여 왼쪽 메뉴에서 **인스턴스를** 선택하고 각 축척 집합 인스턴스의 상태 상태에 대한 **상태 상태** 열을 살펴봅니다. 

## <a name="next-steps"></a>다음 단계

스케일 세트에 대한 [응용 프로그램 상태 확장](./virtual-machine-scale-sets-health-extension.md) 또는 로드 [밸러저 상태 프로브를](../load-balancer/load-balancer-custom-probe-overview.md) 구성하는 방법에 대해 알아봅니다.
