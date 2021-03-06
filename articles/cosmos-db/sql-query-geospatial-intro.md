---
title: Azure 코스모스 DB의 지리 공간 및 GeoJSON 위치 데이터
description: Azure Cosmos DB 및 SQL API를 사용하여 공간 개체를 만드는 방법을 이해합니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 59c8b31dcc8594d2cafb2db7832e290b01026f60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367587"
---
# <a name="geospatial-and-geojson-location-data-in-azure-cosmos-db"></a>Azure 코스모스 DB의 지리 공간 및 GeoJSON 위치 데이터

이 문서에서는 Azure Cosmos DB의 지리 공간 기능을 소개합니다. 현재 지리 공간 데이터의 저장 및 액세스는 Azure Cosmos DB SQL API 계정에서만 지원됩니다. 지리 공간 인덱싱에 대한 설명서를 읽은 후 다음 질문에 답할 수 있습니다.

* Azure Cosmos DB에 공간 데이터를 저장하려면 어떻게 해야 하나요?
* SQL 및 LINQ에서 Azure Cosmos DB의 지리 공간 데이터를 쿼리하려면 어떻게 해야 하나요?
* Azure Cosmos DB에서 공간 인덱싱을 사용하거나 사용하지 않도록 설정하려면 어떻게 해야 하나요?

## <a name="introduction-to-spatial-data"></a>공간 데이터 소개

공간 데이터는 공간에서 개체의 위치와 모양을 설명합니다. 대부분의 애플리케이션에서 이러한 데이터는 지구의 개체 및 지리 공간 데이터에 해당합니다. 공간 데이터를 사용하여 사람, 관심 있는 장소 또는 도시나 호수 경계의 위치를 나타낼 수 있습니다. 일반적인 사용 사례에는 종종 근접 쿼리(예: “내 현재 위치 근처의 모든 커피숍 찾기”)가 포함됩니다.

Azure Cosmos DB의 SQL API는 **지오메트리** 데이터 형식과 **지리** 데이터 형식의 두 가지 공간 데이터 유형을 지원합니다.

- **지오메트리** 유형은 유클리드(플랫) 좌표계의 데이터를 나타냅니다.
- **geography** 형식은 둥근 표면 좌표계의 데이터를 나타냅니다.

## <a name="supported-data-types"></a>지원되는 데이터 형식

Azure Cosmos DB는 인덱싱 및 지리 공간 지점 데이터의 쿼리를 지원하고 [GeoJSON 사양](https://tools.ietf.org/html/rfc7946)을 사용하여 나타납니다. GeoJSON 데이터 구조는 항상 유효한 JSON 개체이므로 특수 도구나 라이브러리 없이 Azure Cosmos DB를 사용하여 저장 및 쿼리할 수 있습니다.

Azure Cosmos DB는 다음과 같은 공간 데이터 형식을 지원합니다.

- Point
- LineString
- Polygon
- MultiPolygon

### <a name="points"></a>점

**점** 은 공간 내의 단일 위치를 나타냅니다. 지리 공간 데이터에서 점은 식품점, 키오스크, 자동차 또는 도시의 주소일 수 있는 정확한 위치를 나타냅니다.  점은 좌표 쌍이나 경도 및 위도를 사용하여 GeoJSON(및 Azure Cosmos DB)에서 표시됩니다.

다음은 요점에 대한 JSON 예제입니다.

**Azure Cosmos DB의 점**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

공간 데이터 형식은 위치 데이터를 포함하는 사용자 프로필의 이 예와 같이 Azure Cosmos DB 문서에 포함될 수 있습니다.

**위치가 Azure Cosmos DB에 저장되어 있는 프로필 사용**

```json
{
    "id":"cosmosdb-profile",
    "screen_name":"@CosmosDB",
    "city":"Redmond",
    "topics":[ "global", "distributed" ],
    "location":{
        "type":"Point",
        "coordinates":[ 31.9, -4.8 ]
    }
}
```

### <a name="points-in-a-geometry-coordinate-system"></a>형상 좌표계의 점

지오메트리 데이터 형식의 경우 GeoJSON 사양은 수평 축을 먼저 지정하고 수직 축은 두 번째로 **지정합니다.**

### <a name="points-in-a-geography-coordinate-system"></a>지리 좌표계의 점

**지리** 데이터 형식의 경우 GeoJSON 사양은 경도 1차 및 위도 2를 지정합니다. 다른 매핑 애플리케이션과 마찬가지로 경도와 위도는 각도이며 도 단위로 표시됩니다. 경도 값은 본초 자오선에서 측정되고 -180도와 180.0도 사이이고, 위도 값은 적도에서 측정되고 -90.0도와 90.0도 사이입니다.

Azure Cosmos DB는 WGS-84 참조 시스템을 기준으로 좌표를 해석합니다. 좌표 참조 시스템에 대한 자세한 내용은 아래를 참조하세요.

### <a name="linestrings"></a>줄 문자열

**LineString** 은 공간의 둘 이상 점과 이러한 점을 연결하는 선 세그먼트를 나타냅니다. 지리 공간 데이터에서 LineStrings은 일반적으로 고속도로나 강을 나타내는 데 사용됩니다.

**지오손의 줄줄**

```json
    "type":"LineString",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ]
    ] ]
```

### <a name="polygons"></a>다각형

**Polygon**은 닫힌 LineString을 형성하는 연결된 점의 경계입니다. 다각형은 일반적으로 호수와 같은 자연스러운 대형이나 구/군/시 및 시/도와 같은 정치적 관할지를 나타내는 데 사용됩니다. 다음은 Azure 코스모스 DB의 다각형의 예입니다.

**GeoJSON의 다각형**

```json
{
    "type":"Polygon",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ],
        [ 32, -4.7 ],
        [ 32, -5 ],
        [ 31.8, -5 ]
    ] ]
}
```

> [!NOTE]
> GeoJSON 사양에서는 유효한 다각형이 되기 위해 마지막 좌표 쌍을 첫 번째 좌표 쌍과 동일하게 제공하여 닫힌 도형을 만들어야 합니다.
>
> 다각형 내의 점을 시계 반대 방향 순서로 지정해야 합니다. 시계 방향 순서로 지정된 다각형은 내부 영역의 반전을 나타냅니다.
>
>

### <a name="multipolygons"></a>멀티 폴리곤

**다중 Polygon은** 0 개 이상의 다각형의 배열입니다. **다중Polygons는** 측면을 겹치거나 공통 영역을 가질 수 없습니다. 그들은 하나 이상의 지점에서 만질 수 있습니다.

**지오손의 멀티폴리곤**

```json
{
    "type":"MultiPolygon",
    "coordinates":[[[
        [52.0, 12.0],
        [53.0, 12.0],
        [53.0, 13.0],
        [52.0, 13.0],
        [52.0, 12.0]
        ]],
        [[
        [50.0, 0.0],
        [51.0, 0.0],
        [51.0, 5.0],
        [50.0, 5.0],
        [50.0, 0.0]
        ]]]
}
```

## <a name="coordinate-reference-systems"></a>좌표 참조 시스템

지구의 모양이 불규칙하기 때문에 지리 지리 공간 데이터의 좌표는 많은 좌표 기준 계(CRS)에 표시되며, 각 좌표 는 자체 참조 프레임과 측정 단위를 가지고 있습니다. 예를 들어 "National Grid of Britain"은 영국에서만 정확하고 그 밖의 지역에서는 정확하지 않은 참조 시스템입니다.

현재 가장 많이 사용되는 CRS는 World Geodetic System [WGS-84](https://earth-info.nga.mil/GandG/update/index.php)입니다. GPS 디바이스와 Google Map 및 Bing 지도 API를 비롯한 많은 매핑 서비스는 WGS-84를 사용합니다. Azure Cosmos DB는 WGS-84 CRS만을 사용하여 지리 지리 공간 데이터의 인덱싱 및 쿼리를 지원합니다.

## <a name="creating-documents-with-spatial-data"></a>공간 데이터를 포함하는 문서 만들기
GeoJSON 값을 포함하는 문서를 만드는 경우, 컨테이너의 인덱싱 정책에 따라 공간 인덱스를 사용하여 자동으로 인덱싱됩니다. Python 또는 Node.js와 같은 동적으로 형식화된 언어로 Azure Cosmos DB SDK 작업을 수행하는 경우 유효한 GeoJSON을 만들어야 합니다.

**Node.js에서 지리 공간 데이터를 포함하는 문서 만들기**

```javascript
var userProfileDocument = {
    "id":"cosmosdb",
    "location":{
        "type":"Point",
        "coordinates":[ -122.12, 47.66 ]
    }
};

client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
    // additional code within the callback
});
```

SQL API로 작업하는 경우 `Point` `LineString` `Polygon` `MultiPolygon` `Microsoft.Azure.Cosmos.Spatial` 네임스페이스 내에서 의 및 클래스를 사용하여 응용 프로그램 개체 내에 위치 정보를 포함할 수 있습니다. 이러한 클래스는 GeoJSON으로 공간 데이터 직렬화 및 역직렬화를 간소화하는 데 도움이 됩니다.

**.NET에서 지리 공간 데이터를 포함하는 문서 만들기**

```csharp
using Microsoft.Azure.Cosmos.Spatial;

public class UserProfile
{
    [JsonProperty("id")]
    public string id { get; set; }

    [JsonProperty("location")]
    public Point Location { get; set; }

    // More properties
}

await container.CreateItemAsync( new UserProfile
    {
        id = "cosmosdb",
        Location = new Point (-122.12, 47.66)
    });
```

위도 및 경도 정보가 없지만 도시 또는 국가/지역과 같은 실제 주소 또는 위치 이름이 있는 경우 Bing Maps REST 서비스와 같은 지오코딩 서비스를 사용하여 실제 좌표를 조회할 수 있습니다. [여기](https://msdn.microsoft.com/library/ff701713.aspx)서 Bing 지도 지오코딩에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB에서 지리 공간 지원을 시작하는 방법을 배웠으므로 이제 다음 작업을 수행할 수 있습니다.

* [Azure Cosmos DB 쿼리](sql-query-getting-started.md)에 대해 알아보기
* [Azure Cosmos DB를 사용하여 공간 데이터 쿼리에](sql-query-geospatial-query.md) 대해 자세히 알아보기
* [Azure 코스모스 DB를 사용하여 인덱스 공간 데이터에](sql-query-geospatial-index.md) 대해 자세히 알아보기