---
title: CloudEvents 스키마에서 이벤트에 Azure Event Grid 사용
description: Azure 이벤트 그리드의 이벤트에 CloudEvents 스키마를 사용하는 방법을 설명합니다. 이 서비스는 클라우드 이벤트의 JSON 구현에서 이벤트를 지원합니다.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: 127095bef2c67a93097bf90bea54ca1b44b16c58
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394385"
---
# <a name="cloudevents-v10-schema-with-event-grid"></a>이벤트 그리드가 있는 CloudEvents v1.0 스키마

Azure Event Grid는 [기본 이벤트 스키마](event-schema.md)외에도 [CloudEvents v1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) 및 HTTP 프로토콜 [바인딩의](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md)JSON 구현에서 이벤트를 기본적으로 지원합니다. [CloudEvents](https://cloudevents.io/)는 이벤트 데이터를 설명하는 [공개 사양](https://github.com/cloudevents/spec/blob/v1.0/spec.md)입니다.

CloudEvents는 클라우드 기반 이벤트를 게시 및 사용하기 위한 일반적인 이벤트 스키마를 제공하여 상호 운용성을 간소화합니다. 이 스키마를 통해 균일한 도구, 이벤트를 라우팅 및 처리하는 표준 방법, 외부 이벤트 스키마를 역직렬화하는 유니버설 방법이 가능해집니다. 공통 스키마를 통해 여러 플랫폼에서 작업을 보다 쉽게 통합할 수 있습니다.

CloudEvents는 [Cloud Native Computing Foundation](https://www.cncf.io/)을 통해 Microsoft를 포함한 여러 [협력자](https://github.com/cloudevents/spec/blob/master/community/contributors.md)가 작성하고 있습니다. 현재 버전 1.0으로 사용할 수 있습니다.

이 문서에서는 이벤트 그리드를 통해 CloudEvents 스키마에 대해 설명합니다.

## <a name="sample-event-using-cloudevents-schema"></a>CloudEvents 스키마를 사용한 샘플 이벤트

CloudEvents 형식의 Azure Blob Storage 이벤트의 예는 다음과 같습니다.

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

CloudEvents v1.0에서 사용 가능한 필드, 해당 유형 및 정의에 대한 자세한 설명은 [여기에서 확인할 수 있습니다.](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes)

`content-type`을 제외하고 CloudEvents 스키마 및 Event Grid 스키마에 배달된 이벤트에 대한 헤더 값은 동일합니다. CloudEvents 스키마의 경우 헤더 값은 `"content-type":"application/cloudevents+json; charset=utf-8"`입니다. Event Grid 스키마의 경우 헤더 값은 `"content-type":"application/json; charset=utf-8"`입니다.

## <a name="event-grid-for-cloudevents"></a>클라우드 이벤트를 위한 이벤트 그리드

CloudEvents 스키마에서 이벤트의 입출력 둘 다에 Event Grid를 사용할 수 있습니다. Blob Storage 이벤트 및 IoT Hub 이벤트와 같은 시스템 이벤트와 사용자 지정 이벤트에 CloudEvents를 사용할 수 있습니다. 또한 온라인 상태에서 이러한 이벤트를 변환할 수도 있습니다.


| 입력 스키마       | 출력 스키마입니다.
|--------------------|---------------------
| CloudEvents 형식 | CloudEvents 형식
| Event Grid 형식  | CloudEvents 형식
| Event Grid 형식  | Event Grid 형식

모든 이벤트 스키마에 대해, Event Grid는 Event Grid 토픽에 게시할 때와 이벤트 구독을 만들 때 유효성 검사를 요구합니다. 자세한 내용은 [Event Grid 보안 및 인증](security-authentication.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
[이벤트 그리드에서 CloudEvents v1.0 스키마를 사용하는 방법을](cloudevents-schema.md)참조하십시오.  
