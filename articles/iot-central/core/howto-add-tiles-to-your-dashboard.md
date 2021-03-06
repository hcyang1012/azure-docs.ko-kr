---
title: 대시보드에 타일 추가 | 마이크로 소프트 문서
description: 빌더는 기본 Azure IoT Central 응용 프로그램 대시보드를 구성하는 방법을 알아봅니다.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f75e5a28f7ec56750432e74ee48ba68491a5e481
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310294"
---
# <a name="configure-the-application-dashboard"></a>응용 프로그램 대시보드 구성

**대시보드는** 응용 프로그램에 액세스할 수 있는 사용자가 응용 프로그램의 URL로 이동할 때 로드되는 페이지입니다. **응용 프로그램 템플릿**중 하나에서 응용 프로그램을 만든 경우 응용 프로그램에 미리 정의된 대시보드가 시작됩니다. **레거시 응용 프로그램** 응용 프로그램 템플릿에서 응용 프로그램을 만든 경우 대시보드를 시작할 수 없습니다.

> [!NOTE]
> 사용자는 기본 응용 프로그램 대시보드 외에 [여러 대시보드를 만들](howto-create-personal-dashboards.md) 수 있습니다. 이러한 대시보드는 사용자에게만 개인용이거나 응용 프로그램의 모든 사용자 간에 공유할 수 있습니다. 

## <a name="add-tiles"></a>타일 추가

다음 스크린샷은 **사용자 지정 응용** 프로그램 템플릿에서 만든 응용 프로그램의 대시보드를 보여 주며 있습니다. 응용 프로그램의 기본 대시보드를 사용자 지정하려면 페이지 왼쪽 상단에 있는 **편집을** 선택합니다.

> [!div class="mx-imgBorder"]
> !["샘플 Contoso" 템플릿을 기반으로 하는 응용 프로그램에 대 한 대시보드](media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png)

**편집을** 선택하면 대시보드 라이브러리 패널이 열립니다. 라이브러리에는 대시보드를 사용자 지정하는 데 사용할 수 있는 타일 및 대시보드 기본 내용이 포함되어 있습니다.

> [!div class="mx-imgBorder"]
> ![대시보드 라이브러리](media/howto-add-tiles-to-your-dashboard/dashboard-library.png)

예를 들어 장치의 현재 온도에 대한 **원격 분석** 타일을 추가할 수 있습니다. 이렇게 하려면 다음을 수행합니다.
1. 장치 **템플릿** 선택
1. 대시보드 타일에 표시할 장치의 **장치 인스턴스를** 선택합니다. 그런 다음 타일에서 사용할 수 있는 장치의 속성 목록이 표시됩니다.
1. 대시보드에서 타일을 만들려면 **온도를** 클릭하고 대시보드 영역으로 드래그합니다. **온도** 옆의 확인란을 클릭하고 **[결합]을**클릭할 수도 있다. 다음 스크린샷은 장치 템플릿 및 장치 인스턴스를 선택한 다음 대시보드에서 온도 원격 분석 타일을 만드는 것을 보여 주었습니다.
1. 타일을 대시보드에 저장하려면 왼쪽 상단에 **저장을** 선택합니다.

> [!div class="mx-imgBorder"]
> ![설정 및 속성에 대한 세부 정보가 있는 "디바이스 세부 정보 구성" 양식](media/howto-add-tiles-to-your-dashboard/device-details.png)

이제 운영자가 기본 응용 프로그램 대시보드를 볼 때 장치의 **온도가** 있는 새 타일이 표시됩니다. 각 타일에는 타일이 생성될 때 표시되는 미리 선택된 그래프, 차트 등이 있습니다. 그러나 사용자는 이 시각화를 편집하고 변경할 수 있습니다. 

> [!div class="mx-imgBorder"]
> ![타일에 대한 설정 및 속성이 표시된 “대시보드” 탭](media/howto-add-tiles-to-your-dashboard/settings-and-properties.png)

## <a name="edit-tiles"></a>타일 편집

대시보드에서 타일을 편집하려면 먼저 페이지 왼쪽 상단에 있는 **편집을** 클릭하면 대시보드 및 모든 타일에 대한 편집 모드가 열립니다. 

> [!div class="mx-imgBorder"]
> ![선택한 타일에 대해 편집 모드가 활성화된 대시보드 화면](media/howto-add-tiles-to-your-dashboard/edit-mode.png)

그런 다음 편집하려는 타일의 오른쪽 상단 모서리에 있는 **기어** 아이콘을 클릭합니다. 여기에서 제목, 시각화, 집계 등을 포함하여 타일의 측면을 편집할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![타일 집계 설정에 대한 드롭다운](media/howto-add-tiles-to-your-dashboard/aggregation-settings.png)

타일에서 **눈금자** 아이콘을 클릭하여 차트 시각화를 변경할 수도 있습니다.

> [!div class="mx-imgBorder"]
> ![타일 시각화 설정에 대한 드롭다운](media/howto-add-tiles-to-your-dashboard/visualization-settings.png)

## <a name="tile-types"></a>타일 유형

다음 표에서는 Azure IoT Central의 타일 사용량을 요약합니다.
 
| 타일 | 대시보드 | 설명
| ----------- | ------- | ------- |
| 콘텐츠 | 애플리케이션 및 장치 세트 대시보드 |마크다운 지원 타일은 제목과 설명 텍스트를 표시하는 클릭 가능한 타일입니다. 이 타일을 링크 타일로 사용하여 사용자가 응용 프로그램과 관련된 URL로 이동할 수 있도록 할 수도 있습니다.|
| 이미지 | 애플리케이션 및 장치 세트 대시보드 |이미지 타일은 사용자 지정 이미지를 표시하며 클릭할 수 있습니다. 이미지 타일을 사용하여 대시보드에 그래픽을 추가하고 사용자가 응용 프로그램과 관련된 URL로 이동할 수 있도록 선택적으로 사용할 수 있습니다.|
| 레이블 | 애플리케이션 대시보드 |레이블 타일은 대시보드에 사용자 지정 텍스트를 표시합니다. 텍스트 의 크기를 선택할 수 있습니다. 레이블 타일을 사용하여 설명, 연락처 세부 정보 또는 도움말과 같은 관련 정보를 대시보드에 추가합니다.|
| 지도 | 애플리케이션 및 장치 대시보드 |맵 타일은 맵에 장치의 위치를 표시합니다. 기기의 위치 기록을 최대 100개까지 표시할 수도 있습니다. 예를 들어 지난 주에 장치가 있었던 곳의 표시 샘플링 경로를 사용할 수 있습니다.|
| 꺾은선형 차트 | 애플리케이션 및 장치 대시보드 |선부차트 타일에는 기간 동안 장치에 대한 집계 측정 차트가 표시됩니다. 예를 들어 지난 1시간 동안 장치의 평균 온도와 압력을 표시하는 선도판을 표시할 수 있습니다.|
| 가로 막대형 차트 | 애플리케이션 및 장치 대시보드 |막대형 차트 타일에는 기간 동안 장치에 대한 총 측정 차트가 표시됩니다. 예를 들어 지난 1시간 동안 장치의 평균 온도와 압력을 표시하는 막대형 차트를 표시할 수 있습니다.|
| 원형 차트 | 애플리케이션 및 장치 세트 대시보드 |원형 차트 타일에는 특정 기간 동안 장치에 대한 집계 측정 차트가 표시됩니다.|
| 열 지도 | 애플리케이션 및 장치 세트 대시보드 |히트 맵 타일은 장치 집합에 대한 정보를 표시하며 색상으로 표시됩니다.|
| 이벤트 기록 | 애플리케이션 및 장치 대시보드 |이벤트 기록 타일은 기간 동안 장치에 대한 이벤트를 표시합니다. 예를 들어, 지난 1시간 동안 장치의 모든 온도 변화를 표시하는 데 사용할 수 있습니다.|
| 상태 기록 | 애플리케이션 및 장치 대시보드 |상태 기록 타일은 기간 동안의 측정 값을 표시합니다. 예를 들어, 마지막 한 시간 동안 장치의 온도 값을 표시 하는 데 사용할 수 있습니다.|
| KPI | 애플리케이션 및 장치 대시보드 | KPI 타일은 기간 동안 집계 원격 분석 또는 이벤트 측정을 표시합니다. 예를 들어, 지난 1시간 동안 장치에 도달한 최대 온도를 표시하는 데 사용할 수 있습니다.|
| 마지막으로 알려진 값 | 애플리케이션 및 장치 대시보드 |마지막으로 알려진 값 타일은 원격 분석 또는 상태 측정에 대한 최신 값을 표시합니다. 예를 들어 이 타일을 사용하여 장치의 온도, 압력 및 습도에 대한 최신 측정값을 표시할 수 있습니다.|

## <a name="next-steps"></a>다음 단계

Azure IoT Central 기본 응용 프로그램 대시보드를 구성하는 방법을 배웠으니 [개인 대시보드를 만드는 방법을 배울](howto-create-personal-dashboards.md)수 있습니다.
