---
title: Azure 미디어 서비스 v3 REST API를 사용 하 여 필터 만들기
description: 이 항목에서는 클라이언트가 스트림의 특정 섹션을 스트리밍하는 데 사용할 수 있는 필터를 생성하는 방법을 설명합니다. 이 선택적 스트리밍은 Media Services가 동적 매니페스트를 생성하여 이루어집니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: f9134dd3bc926e6e2f454e5187e03365e91ed22a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75780337"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Media Services REST API를 사용하여 필터 만들기

고객에게 콘텐츠를 제공(라이브 이벤트 또는 주문형 비디오를 스트리밍)하는 경우 클라이언트에게 기본 자산의 매니페스트 파일에 설명된 내용보다 더 많은 유연성이 필요할 수 있습니다. Azure Media Services를 사용하면 콘텐츠에 사용할 계정 필터 및 자산 필터를 정의할 수 있습니다. 

이 기능 및 이 기능이 사용되는 시나리오에 대한 자세한 설명은 [동적 매니페스트](filters-dynamic-manifest-overview.md) 및 [필터 를](filters-concept.md)참조하십시오.

이 항목에서는 주문형 비디오 자산에 대한 필터를 정의하고 REST API를 사용하여 [계정 필터](https://docs.microsoft.com/rest/api/media/accountfilters) 및 [자산 필터](https://docs.microsoft.com/rest/api/media/assetfilters)를 만드는 방법을 보여 줍니다. 

> [!NOTE]
> [프리젠 테이션을 검토해야합니다TimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>사전 요구 사항 

이 항목에 설명된 단계를 완료하려면 다음을 수행해야 합니다.

- [필터 및 동적 매니페스트](filters-dynamic-manifest-overview.md)를 검토합니다.
- [Azure Media Services REST API 호출에 대해 Postman 구성](media-rest-apis-with-postman.md)

    [Azure AD 토큰 가져오기](media-rest-apis-with-postman.md#get-azure-ad-token) 항목의 마지막 단계를 수행해야 합니다. 

## <a name="define-a-filter"></a>필터 정의  

다음은 매니페스트에 추가되는 트랙 선택 조건을 정의하는 **요청 본문** 예제입니다. 이 필터는 EC-3인 오디오 트랙 및 비트 전송률이 0-1000000 범위인 비디오 트랙을 모두 포함합니다.

```json
{
    "properties": {
        "tracks": [
          {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Audio",
                        "operation": "Equal"
                    },
                    {
                        "property": "FourCC",
                        "value": "EC-3",
                        "operation": "Equal"
                    }
                ]
            },
            {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Video",
                        "operation": "Equal"
                    },
                    {
                        "property": "Bitrate",
                        "value": "0-1000000",
                        "operation": "Equal"
                    }
                ]
            }
        ]
     }
}
```

## <a name="create-account-filters"></a>계정 필터 만들기

다운로드한 Postman 컬렉션에서 **계정 필터**->**만들기 또는 업데이트 계정 필터를**선택합니다.

**PUT** HTTP 요청 메서드는 다음과 유사합니다.

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

**본문** 탭을 선택하고 [이전에 정의](#define-a-filter)한 json 코드를 붙여넣습니다.

**전송을 선택합니다.** 

필터를 만들었습니다.

자세한 내용은 [만들기 또는 업데이트](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate)를 참조하세요. [필터에 대한 JSON 예제](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create-an-account-filter)도 참조하세요.

## <a name="create-asset-filters"></a>자산 필터 만들기  

다운로드한 "미디어 서비스 v3" 우체부 모음에서 **자산**->**만들기 또는 업데이트 에셋 필터를**선택합니다.

**PUT** HTTP 요청 메서드는 다음과 유사합니다.

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

**본문** 탭을 선택하고 [이전에 정의](#define-a-filter)한 json 코드를 붙여넣습니다.

**전송을 선택합니다.** 

자산 필터를 만들었습니다.

자산 필터 만들기 또는 업데이트 방법에 대한 자세한 내용은 [만들기 또는 업데이트](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate)를 참조하세요. [필터에 대한 JSON 예제](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create-an-asset-filter)도 참조하세요. 

## <a name="associate-filters-with-streaming-locator"></a>필터를 스트리밍 로케이터와 연결

스트리밍 로케이터에 적용되는 저작물 또는 계정 필터 목록을 지정할 수 있습니다. [동적 패키지(스트리밍 끝점)는](dynamic-packaging-overview.md) 클라이언트가 URL에서 지정한 필터 목록과 함께 이 필터 목록을 적용합니다. 이 조합은 스트리밍 로케이터에서 지정한 URL + 필터의 필터를 기반으로 하는 [동적 매니페스트를](filters-dynamic-manifest-overview.md)생성합니다. 필터를 적용하지만 URL에 필터 이름을 노출하지 않으려는 경우 이 기능을 사용하는 것이 좋습니다.

REST를 사용하여 필터를 만들고 스트리밍 로케이터와 연결하려면 [스트리밍 로케이터 -](https://docs.microsoft.com/rest/api/media/streaminglocators/create) `properties.filters` API 만들기 및 [요청 본문에](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body)지정합니다.
                                
## <a name="stream-using-filters"></a>필터를 사용하여 스트림

필터를 정의하고 나면 클라이언트가 스트리밍 URL에 필터를 사용할 수 있습니다. Apple HLS(HTTP 라이브 스트리밍), MPEG-DASH, 부드러운 스트리밍 등의 적응 비트 전송률 스트리밍 프로토콜에 필터를 적용할 수 있습니다.

다음 표에서는 필터가 있는 URL의 몇 가지 예제를 보여 줍니다.

|프로토콜|예제|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|부드러운 스트리밍|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>다음 단계

[비디오 스트리밍](stream-files-tutorial-with-rest.md) 
