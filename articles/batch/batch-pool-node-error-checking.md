---
title: 풀 및 노드 오류 확인 - Azure Batch
description: 이 문서에서는 확인할 오류와 풀 및 노드를 만들 때 이를 방지하는 방법과 함께 발생할 수 있는 백그라운드 작업을 다룹니다.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/23/2019
ms.topic: conceptual
ms.openlocfilehash: a68d812a044c776819d169d5bf179f011d06390f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472948"
---
# <a name="check-for-pool-and-node-errors"></a>풀 및 노드 오류 확인

Azure Batch 풀을 만들고 관리할 때 일부 작업은 즉시 수행됩니다. 그러나 일부 작업은 비동기이며 백그라운드에서 실행되며 완료하는 데 몇 분 정도 가내가 됩니다.

즉시 실행되는 작업에서는 오류를 쉽게 검색할 수 있습니다. API, CLI 또는 UI에서 오류가 즉시 반환되기 때문입니다.

이 문서에서는 풀 및 풀 노드에 대해 발생할 수 있는 백그라운드 작업을 설명합니다. 또한 오류를 감지하고 피하는 방법을 지정합니다.

## <a name="pool-errors"></a>풀 오류

### <a name="resize-timeout-or-failure"></a>크기 조정 시간 제한 또는 오류

새 풀을 만들거나 기존 풀의 크기를 조정할 때는 노드의 대상 수를 지정합니다.  만들기 또는 크기 조정 작업은 즉시 완료되지만 새 노드의 실제 할당 또는 기존 노드 제거에는 몇 분 정도 걸릴 수 있습니다.  [create](https://docs.microsoft.com/rest/api/batchservice/pool/add) 또는 [resize](https://docs.microsoft.com/rest/api/batchservice/pool/resize) API에서 크기 조정 시간 제한을 지정합니다. Batch가 크기 조정 시간 초과 기간 동안 대상 노드 수를 가져올 수 없는 경우 풀은 정상 상태로 전환되고 보고서 크기 조정 오류입니다.

가장 최근 평가에 대한 [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) 속성에는 발생한 오류가 나열됩니다.

크기 조정 오류의 일반적인 원인은 다음과 같습니다.

- 크기 조정 시간 제한이 너무 짧음
  - 대부분의 경우 기본 시간 제한인 15분은 풀 노드를 할당하거나 제거할 수 있도록 충분히 깁니다.
  - 많은 수의 노드를 할당하는 경우에는 크기 조정 시간 제한을 30분으로 설정하는 것이 좋습니다. 예를 들어, 1개의 Azure Marketplace 이미지에서 1,000개가 넘는 노드로 크기를 조정하거나 1개의 사용자 지정 VM 이미지에서 300개가 넘는 노드로 크기를 조정할 수 있습니다.
- 코어 할당량 부족
  - Batch 계정은 모든 풀에서 할당할 수 있는 코어 수가 제한됩니다. 해당 할당량에 도달하면 Batch의 노드 할당이 중지됩니다. Batch에서 더 많은 노드를 할당할 수 있도록 코어 할당량을 [늘릴 수 있습니다](https://docs.microsoft.com/azure/batch/batch-quota-limit).
- [풀이 가상 네트워크에 있을 때](https://docs.microsoft.com/azure/batch/batch-virtual-network) 서브넷 IP가 부족함
  - 가상 네트워크 서브넷은 요청한 모든 풀 노드에 할당할 수 있는 충분한 미할당 IP 주소가 있어야 합니다. 그렇지 않으면 노드를 만들 수 없습니다.
- [풀이 가상 네트워크에 있을 때](https://docs.microsoft.com/azure/batch/batch-virtual-network) 리소스가 부족함
  - Batch 계정과 동일한 구독에 부하 분산 장치, 공용 IP 및 네트워크 보안 그룹과 같은 리소스를 만들 수 있습니다. 구독 할당량이 이러한 리소스에 대해 충분한지 확인합니다.
- 사용자 지정 VM 이미지를 사용하는 큰 풀
  - 사용자 지정 VM 이미지를 사용하는 큰 풀은 할당 시간이 더 오래 걸릴 수 있으며 크기 조정 오류가 발생할 수 있습니다.  제한 및 구성에 대한 권장 사항은 [공유 이미지 갤러리가 있는 풀 만들기를](batch-sig-images.md) 참조하십시오.

### <a name="automatic-scaling-failures"></a>자동 크기 조정 실패

풀의 노드 수를 자동으로 조정하도록 Azure Batch를 설정할 수도 있습니다. [풀의 자동 크기 조정 수식](https://docs.microsoft.com/azure/batch/batch-automatic-scaling)에 대한 매개 변수를 정의합니다. Batch 서비스는 이 수식을 사용하여 풀의 노드 수를 정기적으로 평가하고 새 목표 개수를 설정합니다. 다음과 같은 유형의 문제가 발생할 수 있습니다.

- 자동 크기 조정 평가가 실패합니다.
- 평가 결과에 따른 크기 조정 작업이 실패하고 시간이 초과됩니다.
- 자동 크기 조정 수식에 문제가 있으면 노드 대상 값이 잘못됩니다. 크기 조정이 작동하거나 시간이 초과됩니다.

[autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun) 속성을 사용하여 마지막 자동 크기 조정 평가에 대한 정보를 얻을 수 있습니다. 이 속성은 평가 시간, 값과 결과, 성능 오류를 보고합니다.

[풀 크기 조정 완료 이벤트](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event)는 모든 평가 정보를 캡처합니다.

### <a name="delete"></a>DELETE

노드를 포함하는 풀을 삭제하면 Batch는 먼저 노드를 삭제합니다. 그런 다음, 풀 개체 자체를 삭제합니다. 풀 노드가 삭제되려면 몇 분 정도 걸릴 수 있습니다.

삭제 프로세스 중에 Batch는 [풀 상태](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate)를 **deleting**으로 설정합니다. 호출 애플리케이션은 **state** 및 **stateTransitionTime** 속성을 사용하여 풀 삭제가 너무 오래 걸리는지를 검색할 수 있습니다.

## <a name="pool-compute-node-errors"></a>풀 컴퓨팅 노드 오류

Batch가 풀에서 노드를 성공적으로 할당하는 경우에도 다양한 문제로 인해 일부 노드가 비정상으로 발생하여 작업을 실행할 수 없습니다. 이러한 노드에는 여전히 요금이 부과되므로 사용할 수 없는 노드에 대한 비용을 지불하지 않도록 문제를 감지하는 것이 중요합니다. 일반적인 노드 오류 외에도 현재 [작업 상태를](/rest/api/batchservice/job/get#jobstate) 알면 문제 해결에 유용합니다.

### <a name="start-task-failures"></a>작업 실패 시작

풀에 대해 선택적 [시작 작업](/rest/api/batchservice/pool/add#starttask)을 지정할 수 있습니다. 일반적인 작업과 마찬가지로 스토리지에서 다운로드할 명령줄과 리소스 파일을 지정할 수 있습니다. 시작 작업은 시작된 후 각 노드에 대해 실행됩니다. **waitForSuccess** 속성은 Batch가 노드에 대한 작업을 예약하기 전에 시작 작업이 성공적으로 완료될 때까지 대기하는지 여부를 지정합니다.

시작 작업이 성공적으로 완료될 때까지 대기하도록 노드를 구성했으나 시작 작업이 실패하면 어떻게 될까요? 이 경우 노드는 사용할 수 없지만 요금이 계속 부과됩니다.

최상위 [startTaskInfo](/rest/api/batchservice/computenode/get#starttaskinformation) 노드 속성의 [result](/rest/api/batchservice/computenode/get#taskexecutionresult) 및 [failureInfo](/rest/api/batchservice/computenode/get#taskfailureinformation) 속성을 사용하여 시작 작업 실패를 감지할 수 있습니다.

또한 실패한 시작 작업으로 인해 **Batch는 waitForSuccess가** **true로**설정된 경우 노드 [상태를](/rest/api/batchservice/computenode/get#computenodestate) **starttaskfailed로** 설정합니다.

일반적인 작업과 마찬가지로 시작 작업 역시 여러 가지 원인으로 인해 실패할 수 있습니다.  문제를 해결하려면 stdout, stderr 및 추가 작업 관련 로그 파일을 확인합니다.

시작 작업이 동일한 노드에서 여러 번 실행될 수 있으므로 시작 작업이 다시 시작해야 합니다. 시작 작업은 노드를 다시 이미지화하거나 재부팅할 때 실행됩니다. 드문 경우지만 이벤트가 노드 재부팅을 일으킨 후 시작 작업이 실행되며, 운영 체제 또는 임시 디스크 중 하나가 이미지화되고 다른 하나는 그렇지 않은 경우입니다. 배치 시작 작업(예: 모든 Batch 작업)은 임시 디스크에서 실행되므로 일반적으로 문제가 되지 않지만 시작 작업이 운영 체제 디스크에 응용 프로그램을 설치하고 임시 디스크에 다른 데이터를 유지하는 경우 동기화되지 않아 문제가 발생합니다. 두 디스크를 모두 사용하는 경우 그에 따라 응용 프로그램을 보호합니다.

### <a name="application-package-download-failure"></a>애플리케이션 패키지 다운로드 실패

하나의 풀에 대해 하나 이상의 애플리케이션 패키지를 지정할 수 있습니다. Batch는 지정된 패키지 파일을 각 노드에 다운로드하고 노드가 시작된 후 작업이 예약되기 전에 파일압축을 해제합니다. 애플리케이션 패키지와 함께 시작 작업 명령줄을 사용하는 것이 일반적입니다. 예를 들어, 파일을 다른 위치로 복사하거나 설치 프로그램을 실행합니다.

노드 [오류](/rest/api/batchservice/computenode/get#computenodeerror) 속성은 응용 프로그램 패키지를 다운로드하고 압축 해제하는 데 실패했다고 보고합니다. 노드 상태를 사용할 **수 없는**상태로 설정됩니다.

### <a name="container-download-failure"></a>컨테이너 다운로드 실패

풀에서 하나 이상의 컨테이너 참조를 지정할 수 있습니다. 일괄 처리는 지정된 컨테이너를 각 노드에 다운로드합니다. 노드 [오류](/rest/api/batchservice/computenode/get#computenodeerror) 속성은 컨테이너를 다운로드하는 데 실패를 보고하고 노드 상태를 **사용할 수 없게**설정합니다.

### <a name="node-in-unusable-state"></a>unusable 상태의 노드

Azure Batch에서는 여러 가지 이유로 [노드 상태](/rest/api/batchservice/computenode/get#computenodestate)를 **unusable**로 설정할 수 있습니다. 노드 상태를 **unusable**로 설정하면 작업을 노드로 예약할 수 없지만 요금은 여전히 부과됩니다.

**노드는 사용할 수 없지만** [오류가](/rest/api/batchservice/computenode/get#computenodeerror) 없으면 Batch가 VM과 통신할 수 없음을 의미합니다. 이 경우 Batch는 항상 VM을 복구하려고 시도합니다. 일괄 처리는 상태를 **사용할 수 없는**경우에도 응용 프로그램 패키지 또는 컨테이너를 설치하지 못한 VM을 자동으로 복구하려고 시도하지 않습니다.

Batch가 원인을 확인할 수 있으면 노드 [errors](/rest/api/batchservice/computenode/get#computenodeerror) 속성이 그 원인을 보고합니다.

노드가 **unusable** 상태로 설정되는 원인의 추가 예제는 다음과 같습니다.

- 사용자 지정 VM 이미지를 올바르지 않습니다. 예를 들어, 제대로 준비되지 않은 이미지입니다.

- VM이 인프라 오류 또는 하위 수준 업그레이드로 인해 이동됩니다. Batch가 노드를 복구합니다.

- VM 이미지는 지원하지 않는 하드웨어에 배포되었습니다. 예를 들어 [Standard_D1_v2](../virtual-machines/dv2-dsv2-series.md) VM에서 CentOS HPC 이미지를 실행하려고 합니다.

- VM이 Azure [가상 네트워크에](batch-virtual-network.md)있으며 주요 포트에 대한 트래픽이 차단되었습니다.

- VM은 가상 네트워크에 있지만 Azure 저장소에 대한 아웃바운드 트래픽이 차단됩니다.

- VM은 고객 DNS 구성이 있는 가상 네트워크에 있으며 DNS 서버는 Azure 저장소를 확인할 수 없습니다.

### <a name="node-agent-log-files"></a>노드 에이전트 로그 파일

각 풀 노드에서 실행되는 Batch 에이전트 프로세스는 풀 노드 문제에 대한 지원팀에 문의해야 하는 경우 도움이 될 수 있는 로그 파일을 제공할 수 있습니다. Azure Portal, Batch Explorer 또는 [API](/rest/api/batchservice/computenode/uploadbatchservicelogs)를 통해 노드의 로그 파일을 업로드할 수 있습니다. 로그 파일을 업로드하고 저장하면 유용합니다. 나중에 노드 또는 풀을 삭제하여 실행 중인 노드 비용을 절감할 수 있습니다.

### <a name="node-disk-full"></a>노드 디스크 전체

풀 노드 VM에 대한 임시 드라이브는 작업 파일, 작업 파일 및 공유 파일에 대해 Batch에서 사용됩니다.

- 응용 프로그램 패키지 파일
- 작업 리소스 파일
- 배치 폴더 중 하나에 다운로드된 응용 프로그램별 파일
- 각 작업 응용 프로그램 실행을 위한 Stdout 및 stderr 파일
- 응용 프로그램별 출력 파일

이러한 파일 중 일부는 풀 응용 프로그램 패키지 또는 풀 시작 작업 리소스 파일과 같이 풀 노드가 만들어지면 한 번만 작성됩니다. 노드를 만들 때 한 번만 작성하더라도 이러한 파일이 너무 크면 임시 드라이브를 채울 수 있습니다.

다른 파일은 stdout 및 stderr와 같은 노드에서 실행되는 각 작업에 대해 작성됩니다. 동일한 노드에서 많은 수의 작업이 실행되거나 작업 파일이 너무 큰 경우 임시 드라이브를 채울 수 있습니다.

임시 드라이브의 크기는 VM 크기에 따라 다릅니다. VM 크기를 선택할 때 고려해야 할 한 가지 사항은 임시 드라이브에 충분한 공간이 있는지 확인하는 것입니다.

- 풀을 추가할 때 Azure 포털에서 VM 크기의 전체 목록을 표시할 수 있으며 '리소스 디스크 크기' 열이 있습니다.
- 모든 VM 크기를 설명하는 문서에는 '임시 저장소' 열이 있는 테이블이 있습니다. 예를 들어 [최적화된 VM 크기 계산](/azure/virtual-machines/windows/sizes-compute)

각 작업별로 작성된 파일의 경우 자동으로 정리되기 전에 작업 파일이 보존되는 기간을 결정하는 각 작업에 대해 보존 시간을 지정할 수 있습니다. 저장소 요구 사항을 낮추기 위해 보존 시간을 줄일 수 있습니다.


임시 디스크의 공간이 부족하거나 공간이 매우 부족한 경우 노드가 [사용할 수 없는](/rest/api/batchservice/computenode/get#computenodestate) 상태로 이동하고 디스크가 가득 찼다는 노드 오류가 보고됩니다.

### <a name="what-to-do-when-a-disk-is-full"></a>디스크가 가득 찼을 때 수행할 일

디스크가 가득 찬 이유 확인: 노드에서 공간을 차지하는 것이 무엇인지 잘 모르는 경우 노드로 원격으로 이동한 공간을 수동으로 조사하는 것이 좋습니다. [일괄 처리 목록 파일 API를](https://docs.microsoft.com/rest/api/batchservice/file/listfromcomputenode) 사용하여 Batch 관리 되는 폴더(예: 작업 출력)의 파일을 검사할 수도 있습니다. 이 API는 Batch 관리 디렉터리에서 파일만 나열되며 작업에서 다른 곳에서 파일을 만든 경우 파일을 볼 수 없습니다.

필요한 데이터가 노드에서 검색되었거나 지속 가능한 저장소에 업로드되었는지 확인합니다. 디스크 전체 문제의 모든 완화에는 공간을 확보하기 위해 데이터를 삭제하는 것이 포함됩니다.

### <a name="recovering-the-node"></a>노드 복구

1. 풀이 [C.loudServiceConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#cloudserviceconfiguration) 풀인 경우 [Batch 다시 이미지 API를](https://docs.microsoft.com/rest/api/batchservice/computenode/reimage)통해 노드를 다시 이미지화할 수 있습니다. 이렇게 하면 전체 디스크가 정리됩니다. [가상 시스템 구성](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration) 풀에서 현재 이미지 재가 지원되지 않습니다.

2. 풀이 [VirtualMachineConfiguration인](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration)경우 [노드 제거 API를](https://docs.microsoft.com/rest/api/batchservice/pool/removenodes)사용하여 풀에서 노드를 제거할 수 있습니다. 그런 다음 풀을 다시 성장하여 불량 노드를 새 노드로 바꿀 수 있습니다.

3.  완료된 이전 작업 또는 작업 데이터가 노드에 남아 있는 이전 완료된 작업을 삭제합니다. 노드에 있는 작업/작업 데이터에 대한 힌트를 보려면 노드의 [RecentTasks 컬렉션](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskinformation) 또는 [노드의 파일에서](https://docs.microsoft.com//rest/api/batchservice/file/listfromcomputenode)볼 수 있습니다. 작업을 삭제하면 작업의 모든 작업이 삭제되고 작업의 작업을 삭제하면 삭제할 노드의 작업 디렉토리의 데이터가 트리거되므로 공간이 확보됩니다. 충분한 공간을 확보하면 노드를 재부팅하고 "사용할 수 없음" 상태에서 다시 "유휴"로 이동해야 합니다.

## <a name="next-steps"></a>다음 단계

특히 비동기 작업에 대해 포괄적인 오류 검사를 구현하도록 애플리케이션을 설정했는지 확인합니다. 즉시 문제를 검색하고 진단하는 것이 중요할 수 있습니다.
