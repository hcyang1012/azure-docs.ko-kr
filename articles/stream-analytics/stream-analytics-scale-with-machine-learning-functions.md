---
title: Azure Stream Analytics에서 Machine Learning 함수 크기 조정
description: 이 아티클에서는 분할 및 스트림 단위를 구성하여 Microsoft Azure Machine Learning 함수를 사용하는 Stream Analytics 작업의 크기를 조정하는 방법을 설명합니다.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 5b08625d055063b3804a35a3344ff01c7edb79de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066996"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>Azure 기계 학습 스튜디오(클래식) 기능으로 스트림 분석 작업 확장

> [!TIP]
> 성능및 안정성을 높이기 위해 Azure 기계 학습 스튜디오(클래식) UDF 대신 Azure [기계 학습 UDF를](machine-learning-udf.md) 사용하는 것이 좋습니다.

이 문서에서는 Azure 기계 학습 기능을 사용하는 Azure Stream Analytics 작업을 효율적으로 확장하는 방법에 대해 설명합니다. Stream Analytics 작업의 크기를 조정하는 일반적인 방법은 [작업 크기 조정](stream-analytics-scale-jobs.md)을 참조하세요.

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Stream Analytics의 Azure Machine Learning 함수란 무엇입니까?

Stream Analytics의 Machine Learning 함수는 Stream Analytics 쿼리 언어에 일반 함수 호출처럼 사용할 수 있습니다. 그러나 이러한 함수 호출은 실제로 Azure 기계 학습 웹 서비스 요청입니다.

동일한 웹 서비스 API 호출에서 여러 행을 함께 "일괄 처리"하여 기계 학습 웹 서비스 요청의 처리량을 향상시킬 수 있습니다. 이 그룹을 미니 일괄 처리라고 합니다. 자세한 내용은 [Azure 기계 학습 스튜디오(클래식) 웹 서비스를](../machine-learning/studio/consume-web-services.md)참조하십시오. 스트림 분석에서 Azure 기계 학습 스튜디오(클래식)에 대한 지원이 미리 보기상태입니다.

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Machine Learning 함수를 사용하여 Stream Analytics 작업 구성

Stream Analytics 작업에서 사용되는 기계 학습 기능을 구성하는 두 가지 매개 변수가 있습니다.

* 기계 학습 함수 호출의 일괄 처리 크기입니다.
* 스트림 분석 작업에 대해 프로비전된 스트리밍 단위(SUS) 수입니다.

SU에 적합한 값을 결정하려면 스트림 분석 작업의 대기 시간을 최적화할지 또는 각 SU의 처리량을 최적화할지 결정합니다. 잘 분할된 스트림 분석 쿼리의 처리량을 높이기 위해 항상 작업에 수스를 추가할 수 있습니다. 추가 수로는 작업 실행 비용을 증가시게 됩니다.

스트림 분석 작업에 대한 대기 시간 *허용 오차를* 결정합니다. 일괄 처리 크기를 늘리면 Azure 기계 학습 요청의 대기 시간과 스트림 분석 작업의 대기 시간이 증가합니다.

일괄 처리 크기를 늘리면 Stream Analytics 작업이 **동일한 수의** 기계 학습 웹 서비스 요청으로 **더 많은 이벤트를** 처리할 수 있습니다. 기계 학습 웹 서비스 대기 시간의 증가는 일반적으로 일괄 처리 크기의 증가에 하위 선형입니다. 

지정된 상황에서 기계 학습 웹 서비스에 가장 비용 효율적인 일괄 처리 크기를 고려하는 것이 중요합니다. 웹 서비스 요청의 기본 일괄 처리 크기는 1000입니다. [스트림 분석 REST API](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "Stream Analytics REST API") 또는 스트림 분석에 [대한 PowerShell 클라이언트를](stream-analytics-monitor-and-manage-jobs-use-powershell.md)사용하여 이 기본 크기를 변경할 수 있습니다.

일괄 처리 크기를 결정한 후에는 함수가 초당 처리해야 하는 이벤트 수에 따라 스트리밍 단위(SUs)수를 설정할 수 있습니다. 스트리밍 단위에 대한 자세한 내용은 [Stream Analytics 크기 조정 작업](stream-analytics-scale-jobs.md)을 참조하세요.

6개의 모든 수는 기계 학습 웹 서비스에 20개의 동시 연결을 제공합니다. 그러나 1개의 SU 작업과 3개의 SU 작업은 20개의 동시 연결을 얻습니다.  

응용 프로그램에서 초당 200,000개의 이벤트를 생성하고 일괄 처리 크기가 1000인 경우 결과 웹 서비스 대기 시간은 200ms입니다. 이 속도는 모든 연결이 초당 기계 학습 웹 서비스에 5개의 요청을 할 수 있음을 의미합니다. 20개의 연결을 통해 Stream Analytics 작업은 200ms에서 20,000개의 이벤트와 100,000개의 이벤트를 초당 처리할 수 있습니다.

초당 200,000개의 이벤트를 처리하려면 Stream Analytics 작업에 40개의 동시 연결이 필요하며, 이 연결은 12개의 US로 나옵니다. 다음 다이어그램은 Stream Analytics 작업에서 Machine Learning 웹 서비스 엔드포인트로 전송되는 요청을 보여줍니다. 6SU마다 Machine Learning 웹 서비스에 대한 최대 동시 연결 20개가 있습니다.

![기계 학습 기능을 갖춘 스트림 분석 확장 두 가지 작업 예제](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "기계 학습 기능을 갖춘 스트림 분석 확장 두 가지 작업 예제")

일반적으로 배치 크기가 ***B***이고, 배치 크기 B의 웹 서비스 대기 시간이 ***L***밀리초이면 SU ***N***개가 포함된 Stream Analytics 작업의 처리량은 다음과 같습니다.

![Machine Learning 함수 수식을 사용하여 Stream Analytics 크기 조정](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Machine Learning 함수 수식을 사용하여 Stream Analytics 크기 조정")

기계 학습 웹 서비스에서 '최대 동시 호출'을 구성할 수도 있습니다. 이 매개 변수를 최대값(현재 200)으로 설정하는 것이 좋습니다.

이 설정에 대한 자세한 내용은 [Machine Learning 웹 서비스와 관련된 크기 조정 문서](../machine-learning/studio/scaling-webservice.md)를 참조하세요.

## <a name="example--sentiment-analysis"></a>예 – 정서 분석
다음 예제는 [Stream Analytics Machine Learning 통합 자습서](stream-analytics-machine-learning-integration-tutorial.md)에 설명된 대로 정서 분석 Machine Learning 함수를 사용하는 Stream Analytics 작업을 포함하고 있습니다.

다음 예제에 표시된 대로 완전히 분할된 간단한 쿼리 다음에 **sentiment** 함수가 옵니다.

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```

초당 10,000개의 트윗 속도로 트윗에 대한 감정 분석을 수행하는 Stream Analytics 작업을 만드는 데 필요한 구성을 살펴보겠습니다.

1 SU를 사용하면 이 스트림 애널리틱스 작업이 트래픽을 처리할 수 있습니까? 작업은 기본 일괄 처리 크기 1000을 사용하여 입력을 따라갈 수 있습니다. 감정 분석 기계 학습 웹 서비스의 기본 대기 시간(기본 일괄 처리 크기가 1000)은 대기 시간을 1초 이상 생성하지 않습니다.

스트림 애널리틱스 작업의 **전체** 또는 종단 간 대기 시간은 일반적으로 몇 초입니다. 이 Stream Analytics 작업, *특히* Machine Learning 함수 호출에 대해 좀 더 자세히 살펴보겠습니다. 일괄 처리 크기가 1000인 경우 10,000개의 이벤트 처리량은 웹 서비스에 약 10개의 요청을 받습니다. 1SU를 사용하더라도 이 입력 트래픽을 처리하기에 충분한 동시 연결이 있습니다.

입력 이벤트 속도가 100배 증가하면 Stream Analytics 작업은 초당 1,000,000 트윗을 처리해야 합니다. 크기 증가를 위해 다음 두 가지 옵션을 사용할 수 있습니다.

1. 일괄 처리 크기를 늘립니다.
2. 입력 스트림을 분할하여 이벤트를 병렬로 처리합니다.

첫 번째 옵션을 사용하면 작업 **대기 시간**이 증가합니다.

두 번째 옵션을 사용하면 더 많은 동시 기계 학습 웹 서비스 요청을 갖기 위해 더 많은 수액을 프로비전해야 합니다. 이렇게 많은 수의 US가 작업 **비용을**증가시킵니다.

각 배치 크기에 대해 다음 대기 시간 측정을 사용하여 크기 조정을 살펴보겠습니다.

| 대기 시간 | Batch 크기 |
| --- | --- |
| 200밀리초 | 1000개 이벤트 일괄 처리 또는 이하 |
| 250 ms | 5,000개 이벤트 일괄 처리 |
| 300 ms | 10,000개 이벤트 일괄 처리 |
| 500ms | 25,000개 이벤트 일괄 처리 |

1. 첫 번째 옵션을 사용하면(더 많은 SU를 프로비전하지 **않음**) 일괄 처리 크기가 **25,000**으로 늘어날 수 있습니다. 이러한 방식으로 일괄 처리 크기를 늘리면 작업에서 기계 학습 웹 서비스에 대한 20개의 동시 연결(호출당 500ms의 대기 시간)으로 1,000,000개의 이벤트를 처리할 수 있습니다. Machine Learning 웹 서비스 요청에 대한 감정 함수 요청으로 인해 Stream Analytics 작업의 대기 시간이 **200ms**에서 **500ms**로 증가합니다. 그러나 기계 학습 웹 서비스에서 요청의 페이로드 크기를 4MB 이하로 요구하고 웹 서비스 요청 시간 초과는 100초 후에 수행하므로 일괄 처리 크기를 무한히 늘릴 **수 없습니다.**
1. 두 번째 옵션을 사용할 경우 배치 크기는 1000으로 유지되고 웹 서비스 대기 시간이 200ms이므로, 웹 서비스에 대한 동시 연결 20개마다 1000 * 20 * 5 이벤트 = 초당 100,000개 이벤트를 처리할 수 있습니다. 따라서 초당 1,000,000 이벤트를 처리하려면 작업에 60SU가 필요합니다. 첫 번째 옵션과 비교하면, Stream Analytics 작업에서 웹 서비스 배치 요청을 더 많이 만들며, 그로 인해 비용도 함께 증가합니다.

아래는 SU 및 배치 크기(초당 이벤트 수)에 따른 Stream Analytics 작업의 처리량을 보여 주는 테이블입니다.

| 배치 크기(ML 대기 시간) | 500(200ms) | 1,000(200ms) | 5,000(250ms) | 10,000(300ms) | 25,000(500ms) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **3SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **6SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **12SU** |5,000 |10000 |40,000 |60,000 |100,000 |
| **18SU** |7,500 |15,000 |60,000 |90,000 |150,000 |
| **24SU** |10000 |20,000 |80,000 |120,000 |200,000 |
| **…** |… |… |… |… |… |
| **60SU** |25,000 |50,000 |200,000 |300,000 |500,000 |

이제 Stream Analytics 작업에서 Machine Learning 함수가 어떻게 작동하는지 충분히 이해하셨을 것입니다. 또한 Stream Analytics 작업이 데이터 원본에서 데이터를 “끌어오며” 각 “끌어오기”는 Stream Analytics 작업에서 처리할 이벤트 일괄 처리를 반환한다는 내용도 이해하셨을 것입니다. 이 끌어오기 모델이 Machine Learning 웹 서비스 요청에 어떤 영향을 미칠까요?

일반적으로 기계 학습 기능에 대해 설정한 일괄 처리 크기는 각 Stream Analytics 작업 "끌어오기"에서 반환되는 이벤트 수로 정확히 나눌 수 없습니다. 이와 같은 경우 Machine Learning 웹 서비스가 “부분” 배치를 통해 호출됩니다. 부분 일괄 처리를 사용하면 끌어오기에서 끌어오기까지 이벤트를 병합할 때 추가 작업 대기 시간 오버헤드가 발생하지 않습니다.

## <a name="new-function-related-monitoring-metrics"></a>새 함수 관련 모니터링 메트릭
Stream Analytics 작업의 모니터링 영역에 함수 관련 메트릭 세 개가 추가되었습니다. 아래 그림과 같이 **함수 요청,** **함수 이벤트** 및 실패한 **함수 요청입니다.**

![Machine Learning 함수 메트릭을 사용하여 Stream Analytics 크기 조정](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Machine Learning 함수 메트릭을 사용하여 Stream Analytics 크기 조정")

함수는 아래와 같이 정의됩니다.

**FUNCTION REQUESTS**: 함수 요청의 수

**FUNCTION EVENTS**: 함수 요청의 이벤트 수

**FAILED FUNCTION REQUESTS**: 실패한 함수 요청의 수.

## <a name="key-takeaways"></a>핵심 내용

기계 학습 기능을 사용하여 스트림 분석 작업을 확장하려면 다음 요소를 고려하십시오.

1. 입력 이벤트 속도입니다.
2. 실행 중인 Stream Analytics 작업(따라서 기계 학습 웹 서비스 요청의 일괄 처리 크기)에 대한 허용된 대기 시간입니다.
3. 프로비저닝된 스트림 분석 수와 기계 학습 웹 서비스 요청 수(추가 기능 관련 비용)입니다.

이 예에서는 완전하게 분할된 Stream Analytics 쿼리가 사용되었습니다. 보다 복잡한 쿼리가 필요한 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)에서 Stream Analytics 팀에게 추가 도움을 받을 수 있습니다.

## <a name="next-steps"></a>다음 단계
Stream Analytics에 대한 자세한 내용은 다음 항목을 참조하세요.

* [Azure 스트림 분석 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure 스트림 분석 쿼리 언어 참조](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)
