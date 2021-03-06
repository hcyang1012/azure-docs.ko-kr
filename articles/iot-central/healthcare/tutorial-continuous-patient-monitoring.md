---
title: Azure IoT Central을 사용하여 지속적인 환자 모니터링 앱 만들기 | Microsoft Docs
description: Azure IoT Central 애플리케이션 템플릿을 사용하여 지속적인 환자 모니터링 애플리케이션을 만드는 방법에 대해 알아봅니다.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 35ac39109bfcb4dc63b738c947d2ad8caf8ac0a6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "77021290"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>자습서: 지속적인 환자 모니터링 앱 템플릿 배포 및 연습



이 자습서에서는 솔루션 개발자로서 IoT Central 지속적인 환자 모니터링 애플리케이션을 배포하여 시작하는 방법을 보여줍니다. 템플릿을 배포하는 방법, 기본적으로 제공되는 항목 및 다음에 수행할 수 있는 작업을 알아봅니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 애플리케이션 템플릿 만들기
> * 애플리케이션 템플릿 살펴보기

## <a name="create-an-application-template"></a>애플리케이션 템플릿 만들기

[Azure IoT Central 애플리케이션 관리자 웹 사이트](https://apps.azureiotcentral.com/)로 이동합니다. 왼쪽 탐색 모음에서 **빌드**를 선택하고 **의료** 탭을 클릭합니다. 

>[!div class="mx-imgBorder"] 
>![앱 관리자 의료](media/app-manager-health.png)

**앱 만들기** 단추를 클릭하여 애플리케이션 만들기를 시작한 다음, Microsoft 개인/회사/학교 계정으로 로그인합니다. **새 애플리케이션** 페이지로 이동합니다.

![의료 애플리케이션 만들기](media/app-manager-health-create.png)

![의료 애플리케이션 만들기 청구 정보](media/app-manager-health-create-billinginfo.png)

애플리케이션을 만드는 방법은 다음과 같습니다.

1. Azure IoT Central은 사용자가 선택한 템플릿을 기반으로 애플리케이션 이름을 자동으로 제안합니다. 이 이름을 그대로 적용해도 되고, **지속적인 환자 모니터링**처럼 친숙한 애플리케이션 이름을 직접 입력해도 됩니다. 또한 Azure IoT Central은 애플리케이션 이름을 기반으로 고유한 URL 접두사를 자동으로 생성합니다. 원하는 경우 이 URL 접두사를 더욱 기억하기 쉬운 것으로 자유롭게 변경할 수 있습니다.

2. *무료* 가격 책정 플랜 또는  *표준* 가격 책정 플랜 중 무엇을 사용하여 애플리케이션을 만들지 선택할 수 있습니다. 무료 플랜을 사용하여 만드는 애플리케이션은 7일 후 만료되며, 최대 5대의 디바이스에서 사용할 수 있습니다. 만료되기 전에는 언제든지 무료 플랜에서 표준 플랜으로 애플리케이션을 이동할 수 있습니다. 무료 플랜을 선택한 경우 연락처 정보를 입력하고 Microsoft에서 정보 및 팁을 받을 것인지 여부를 선택합니다. 표준 플랜을 사용하여 만든 애플리케이션은 최대 2개의 디바이스에서 사용할 수 있으며 요금 청구를 위해 Azure 구독 정보를 입력해야 합니다.

3. 페이지 맨 아래에서 **만들기**를 선택하여 애플리케이션을 배포합니다.

## <a name="walk-through-the-application-template"></a>애플리케이션 템플릿 살펴보기

### <a name="dashboards"></a>대시보드

앱 템플릿을 배포한 후에는 가장 먼저 **Lamna 입원 환자 모니터링 대시보드**로 이동됩니다. Lamna Healthcare는 Woodgrove 병원과 Burkville 병원을 포함하고 있는 가상의 병원 시스템입니다. Woodgrove 병원의 이 운영자 대시보드에는 수행할 수 있는 명령, 작업 및 동작 세트와 함께 이 템플릿의 디바이스에 대한 정보와 원격 분석 데이터가 표시됩니다. 대시보드에서 다음을 수행할 수 있습니다.

* 디바이스의 **배터리 잔량**이나 **연결** 상태 같은 디바이스 원격 분석 데이터 및 속성을 확인합니다.

* 스마트 바이탈 패치 디바이스의 **평면도**와 위치를 확인합니다.

* 새 환자의 스마트 바이탈 패치를 **다시 프로비저닝**합니다.

* 병원의 진료 팀이 환자를 추적할 때 볼 수 있는 **공급자 대시보드**의 예를 살펴봅니다.

* 디바이스가 입원 환자에게 사용되고 있는지 아니면 원격 시나리오에 사용되고 있는지 나타내도록 디바이스의 **환자 상태**를 변경합니다.

>[!div class="mx-imgBorder"] 
>![Lamna 입원 환자](media/lamna-in-patient.png)

또한 **원격 환자 대시보드로 이동**을 클릭하여 Burkville 병원에 사용되는 두 번째 운영자 대시보드를 볼 수 있습니다. 이 대시보드에는 비슷한 작업, 원격 분석 데이터 및 정보 세트가 포함되어 있습니다. 뿐만 아니라 여러 디바이스가 사용되는 것을 볼 수 있으며 각 디바이스의 **펌웨어를 업데이트**할 수 있습니다.

>[!div class="mx-imgBorder"] 
>![Lamna 원격](media/lamna-remote.png)

두 대시보드에서 언제든지 이 설명서에 다시 연결할 수 있습니다.

### <a name="device-templates"></a>디바이스 템플릿

**디바이스 템플릿** 탭을 클릭하면 다음 두 가지 유형의 디바이스가 템플릿에 포함된 것을 볼 수 있습니다.

* **스마트 바이탈 패치**: 이 디바이스는 다양한 종류의 바이탈 사인을 측정하는 패치를 나타냅니다. 병원 안팎에서 환자를 모니터링하는 데 사용할 수 있습니다. 템플릿을 클릭하면 패치가 배터리 잔량이나 디바이스 온도 같은 디바이스 데이터 외에도, 분당 호흡수나 혈압 같은 환자 상태 데이터를 전송하는 것을 볼 수 있습니다.

* **스마트 무릎 보호대**: 이 디바이스는 무릎 관절 교체 수술을 받은 환자가 회복할 때 사용할 수 있는 무릎 보호대를 나타냅니다. 이 템플릿을 클릭하면 디바이스 데이터 외에도 동작 범위나 가속도 같은 기능이 표시됩니다.

>[!div class="mx-imgBorder"] 
>![스마트 바이탈 패치 디바이스 템플릿](media/smart-vitals-device-template.png)

**디바이스 그룹** 탭을 클릭하면 해당 디바이스 템플릿에 디바이스 그룹이 자동으로 생성되는 것을 볼 수 있습니다.

### <a name="rules"></a>규칙

규칙 탭으로 이동하면 애플리케이션 템플릿에 있는 다음 세 가지 규칙이 표시됩니다.

* **무릎 보호대 온도 높음**: 이 규칙은 스마트 무릎 보호대의 디바이스 온도가 5분 넘게 95&deg;F를 초과할 때 트리거됩니다. 이 규칙을 사용하여 환자 및 진료 팀에게 경고하고, 디바이스를 원격으로 냉각할 수 있습니다.

* **낙상 감지됨**: 이 규칙은 환자의 낙상이 감지된 경우에 트리거됩니다. 이 규칙을 사용하여 낙상 환자를 도와줄 운영 팀을 파견할 수 있습니다.

* **패치 배터리 부족**: 이 규칙은 디바이스의 배터리 잔량이 10% 미만일 때 트리거됩니다. 이 규칙을 사용하여 환자에게 디바이스를 충전하라는 알림을 트리거할 수 있습니다.

>[!div class="mx-imgBorder"] 
>![무릎 보호대 온도 높음 규칙](media/brace-temp-rule.png)

### <a name="devices"></a>디바이스

**디바이스** 탭을 클릭한 다음, **스마트 무릎 보호대** 인스턴스를 선택합니다. 선택한 특정 디바이스에 대한 정보를 살펴볼 수 있는 세 가지 보기가 표시됩니다. 이러한 보기는 디바이스에 대한 디바이스 템플릿을 작성할 때 생성 및 게시되며, 이는 연결하거나 시뮬레이션하는 모든 디바이스 간에 일관성이 있음을 의미합니다.

**대시보드** 보기는 디바이스에서 제공하는 운영자 중심의 원격 분석 및 속성에 대한 개요를 제공합니다.

**속성** 탭에서는 클라우드 속성 및 읽기/쓰기 디바이스 속성을 편집할 수 있습니다.

**명령** 탭에서는 디바이스 템플릿의 일부로 모델링된 명령을 실행할 수 있습니다.

>[!div class="mx-imgBorder"] 
>![무릎 보호대 보기](media/knee-brace-dashboard.png)

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 더 이상 사용하지 않으려면 **관리 > 애플리케이션 설정**으로 이동한 후 **삭제**를 클릭하여 애플리케이션을 삭제합니다.

>[!div class="mx-imgBorder"] 
>![앱 삭제](media/admin-delete.png)

## <a name="next-steps"></a>다음 단계

다음 문서로 넘어가서 IoT Central 애플리케이션에 연결하는 공급자 대시보드를 만드는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [공급자 대시보드 빌드](howto-health-data-triage.md)