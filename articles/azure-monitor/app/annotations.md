---
title: Application Insights에 대한 릴리스 주석 | Microsoft Docs
description: Application Insights에서 배포 또는 빌드 표식을 메트릭 탐색기 차트에 추가합니다.
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: 0ad773ca6a7102ac718d43dfbbf6a4f834e681a0
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010731"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Application Insights의 메트릭 차트에 대한 주석

주석에는 새 빌드 또는 기타 중요한 이벤트를 배포한 위치가 표시됩니다. 주석을 사용하면 변경 내용이 응용 프로그램의 성능에 영향을 미쳤는지 쉽게 확인할 수 있습니다. Azure 파이프라인 빌드 시스템에서 자동으로 만들 수 [있습니다.](https://docs.microsoft.com/azure/devops/pipelines/tasks/) PowerShell에서 주석을 만들어 원하는 이벤트에 대한 플래그를 지정하는 주석을 만들 수도 있습니다.

## <a name="release-annotations-with-azure-pipelines-build"></a>Azure 파이프라인 빌드를 통해 릴리스 주석

릴리스 주석은 Azure DevOps의 클라우드 기반 Azure 파이프라인 서비스의 기능입니다.

### <a name="install-the-annotations-extension-one-time"></a>주석 확장 설치(한 번)

릴리스 주석을 만들려면 Visual Studio 마켓플레이스에서 사용할 수 있는 여러 Azure DevOps 확장 중 하나를 설치해야 합니다.

1. [Azure DevOps 프로젝트에 로그인합니다.](https://azure.microsoft.com/services/devops/)
   
1. Visual Studio 마켓플레이스 [릴리스 주석 확장](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) 페이지에서 Azure DevOps 조직을 선택한 다음 **설치를** 선택하여 Azure DevOps 조직에 확장을 추가합니다.
   
   ![Azure DevOps 조직을 선택한 다음 설치를 선택합니다.](./media/annotations/1-install.png)
   
Azure DevOps 조직에 대해 확장을 한 번만 설치하면 됩니다. 이제 조직의 모든 프로젝트에 대해 릴리스 주석을 구성할 수 있습니다.

### <a name="configure-release-annotations"></a>릴리스 주석 구성

각 Azure 파이프라인 릴리스 템플릿에 대해 별도의 API 키를 만듭니다.

1. [Azure 포털에](https://portal.azure.com) 로그인하고 응용 프로그램을 모니터링하는 응용 프로그램 인사이트 리소스를 엽니다. 또는 없는 경우 [새 응용 프로그램 인사이트 리소스를 만듭니다.](../../azure-monitor/app/app-insights-overview.md)
   
1. API **액세스** 탭을 열고 **응용 프로그램 인사이트 ID를**복사합니다.
   
   ![API 액세스에서 응용 프로그램 ID를 복사합니다.](./media/annotations/2-app-id.png)

1. 별도의 브라우저 창에서 Azure 파이프라인 배포를 관리하는 릴리스 템플릿을 열거나 만듭니다.
   
1. **작업 추가를**선택한 다음 메뉴에서 **응용 프로그램 인사이트 해제 별표** 작업을 선택합니다.
   
   ![작업 추가를 선택하고 응용 프로그램 인사이트 릴리스 어노션을 선택합니다.](./media/annotations/3-add-task.png)

   > [!NOTE]
   > 릴리스 별표 작업은 현재 Windows 기반 에이전트만 지원합니다. Linux, macOS 또는 다른 유형의 에이전트에서는 실행되지 않습니다.
   
1. **응용 프로그램 ID에서** **API 액세스** 탭에서 복사한 응용 프로그램 인사이트 ID를 붙여넣습니다.
   
   ![응용 프로그램 인사이트 ID 붙여넣기](./media/annotations/4-paste-app-id.png)
   
1. 응용 프로그램 인사이트 **API 액세스** 창에서 **API 키 만들기를**선택합니다. 
   
   ![API 액세스 탭에서 API 키 만들기를 선택합니다.](./media/annotations/5-create-api-key.png)
   
1. API **키 만들기** 창에서 설명을 입력하고 **주석 작성을**선택한 다음 **키 생성을**선택합니다. 새 키를 복사합니다.
   
   ![API 만들기 키 창에서 설명을 입력하고 주석 작성을 선택한 다음 키 생성을 선택합니다.](./media/annotations/6-create-api-key.png)
   
1. 릴리스 템플릿 창에서 **변수** 탭에서 **추가를** 선택하여 새 API 키에 대한 변수 정의를 만듭니다.

1. **이름**아래에서 `ApiKey`을 입력하고 **Value**에서 **API 액세스** 탭에서 복사한 API 키를 붙여넣습니다.
   
   ![Azure DevOps 변수 탭에서 추가를 선택하고 변수 ApiKey의 이름을 지정하고 값 아래에 API 키를 붙여넣습니다.](./media/annotations/7-paste-api-key.png)
   
1. 기본 릴리스 템플릿 창에서 **저장을** 선택하여 템플릿을 저장합니다.

## <a name="view-annotations"></a>주석 보기


   > [!NOTE]
   > 릴리스 주석은 현재 응용 프로그램 인사이트 메트릭 창에서 사용할 수 없습니다.

이제 릴리스 템플릿을 사용하여 새 릴리스를 배포할 때마다 응용 프로그램 인사이트로 추가가 전송됩니다. 주석은 다음 위치에서 볼 수 있습니다.

또한 수동으로 릴리스 주석을 만들 수 있는 사용 창:

![시간 동안 표시되는 사용자 방문 수가 있는 막대형 차트의 스크린샷입니다. 릴리스 주석은 릴리스가 발생한 시간을 나타내는 차트 위에 녹색 체크표시로 나타납니다.](./media/annotations/usage-pane.png)

시각화가 x축을 따라 시간을 표시하는 모든 로그 기반 통합 문서 쿼리에서

![주석이 표시된 시계열 로그 기반 쿼리가 있는 통합 문서 창의 스크린샷](./media/annotations/workbooks-annotations.png)

통합 문서의 주석을 활성화하려면 **고급 설정으로** 이동하여 **주석 표시를 선택합니다.**

![단어와 고급 설정 메뉴의 스크린 샷은 그것을 활성화하기 위해 설정 옆에 체크 표시로 강조 주석을 보여줍니다.](./media/annotations/workbook-show-annotations.png)

요청자, 소스 제어 분기, 릴리스 파이프라인 및 환경을 포함하여 릴리스에 대한 세부 정보를 열려면 모든 부술 표시기를 선택합니다.

## <a name="create-custom-annotations-from-powershell"></a>PowerShell에서 사용자 지정 주석 만들기
GitHub의 [CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) PowerShell 스크립트를 사용하여 Azure DevOps를 사용하지 않고 원하는 프로세스에서 주석을 만들 수 있습니다. 

1. 만들기의 로컬 복사본을 [만듭니다해제Annotation.ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).
   
1. 이전 절차의 단계를 사용하여 응용 프로그램 인사이트 ID를 얻고 응용 프로그램 인사이트 **API 액세스** 탭에서 API 키를 만듭니다.
   
1. 다음 코드로 PowerShell 스크립트를 호출하여 각도 괄호가 있는 자리 표시자를 값으로 바릅니다. 는 `-releaseProperties` 선택 사항입니다. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

예를 들어 스크립트를 수정하여 과거에 대한 주석을 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [작업 항목 만들기](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [PowerShell을 사용한 Automation](../../azure-monitor/app/powershell.md)
