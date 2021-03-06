---
title: .NET에서 Azure 인지 검색 사용
titleSuffix: Azure Cognitive Search
description: C# 및 .NET SDK를 사용하여 .NET 응용 프로그램에서 Azure 인지 검색을 사용하는 방법을 알아봅니다. 코드 기반 작업에는 서비스에 연결, 콘텐츠 인덱싱 및 인덱스 쿼리가 포함됩니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b31a4e40c1e9095499faf265673ab4213ad6bde0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283070"
---
# <a name="how-to-use-azure-cognitive-search-from-a-net-application"></a>.NET 응용 프로그램에서 Azure 인지 검색을 사용하는 방법

이 문서는 [Azure 인지 검색 .NET SDK를](https://aka.ms/search-sdk)사용하여 실행하기 위한 연습입니다. .NET SDK를 사용하여 Azure Cognitive Search를 사용하여 응용 프로그램에서 풍부한 검색 환경을 구현할 수 있습니다.

## <a name="whats-in-the-azure-cognitive-search-sdk"></a>Azure 인지 검색 SDK의 내용
SDK는 HTTP와 JSON의 세부 정보를 처리하지 않고도 인덱스, 데이터 원본, 인덱서, 동의어 맵을 관리할 뿐만 아니라 문서를 업로드 및 관리하고 쿼리를 실행할 수 있는 클라이언트 라이브러리 몇 개로 구성되어 있습니다. 이러한 클라이언트 라이브러리는 모두 NuGet 패키지로 배포됩니다.

기본 NuGet 패키지는 `Microsoft.Azure.Search`이며, 이는 다른 모든 패키지를 종속 항목으로 포함하는 메타 패키지입니다. 막 시작중이거나 응용 프로그램에 Azure Cognitive Search의 모든 기능이 필요하다는 것을 알고 있는 경우 이 패키지를 사용합니다.

SDK의 다른 NuGet 패키지는 다음과 같습니다.
 
  - `Microsoft.Azure.Search.Data`: Azure Cognitive Search를 사용하여 .NET 응용 프로그램을 개발하는 경우 인덱스에서 문서를 쿼리하거나 업데이트하기만 하면 됩니다. 인덱스, 동의어 맵 또는 서비스 수준의 다른 리소스도 만들거나 업데이트해야 하는 경우에는 대신 `Microsoft.Azure.Search` 패키지를 사용합니다.
  - `Microsoft.Azure.Search.Service`: .NET에서 자동화를 개발하여 Azure Cognitive Search 인덱스, 동의어 맵, 인덱서, 데이터 원본 또는 기타 서비스 수준 리소스를 관리하는 경우 이 패키지를 사용합니다. 인덱스에서 문서를 쿼리하거나 업데이트하기만 하면 되는 경우에는 대신 `Microsoft.Azure.Search.Data` 패키지를 사용합니다. Azure Cognitive Search의 모든 기능이 필요한 `Microsoft.Azure.Search` 경우 패키지를 대신 사용합니다.
  - `Microsoft.Azure.Search.Common`: Azure 인지 검색 .NET 라이브러리에 필요한 공통 형식입니다. 응용 프로그램에서 이 패키지를 직접 사용할 필요는 없습니다. 종속성으로만 사용할 수 있습니다.

다양한 클라이언트 라이브러리가 `Index`, `Field`, `Document` 등의 클래스뿐만 아니라 `SearchServiceClient` 및 `SearchIndexClient` 클래스의 `Indexes.Create` 및 `Documents.Search` 등을 정의합니다. 이러한 클래스는 다음과 같은 네임 스페이스에 구성됩니다.

* [마이크로소프트.Azure.검색](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

SDK의 향후 업데이트에 대한 피드백을 제공하려면 피드백 [페이지를](https://feedback.azure.com/forums/263029-azure-search/) 참조하거나 [GitHub에서](https://github.com/azure/azure-sdk-for-net/issues) 문제를 만들고 문제 제목에 "Azure 인지 검색"을 언급하십시오.

.NET SDK는 `2019-05-06` Azure [인지 검색 REST API](https://docs.microsoft.com/rest/api/searchservice/)의 버전을 지원합니다. 이 버전에는 Azure Blob을 인덱싱할 때 [복잡한 유형,](search-howto-complex-data-types.md) [AI 보강,](cognitive-search-concept-intro.md) [자동 완성](https://docs.microsoft.com/rest/api/searchservice/autocomplete)및 [JsonLines 구문 분석 모드에](search-howto-index-json-blobs.md) 대한 지원이 포함되어 있습니다. 

이 SDK는 Search 서비스 생성 및 확장, API 키 관리 등의 [관리 작업](https://docs.microsoft.com/rest/api/searchmanagement/)을 지원하지 않습니다. .NET 응용 프로그램에서 검색 리소스를 관리해야 하는 경우 [Azure 인지 검색 .NET 관리 SDK](https://aka.ms/search-mgmt-sdk)를 사용할 수 있습니다.

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>최신 버전의 SDK로 업그레이드
이미 이전 버전의 Azure Cognitive Search .NET SDK를 사용하고 있고 일반적으로 사용 가능한 최신 버전으로 업그레이드하려는 경우 [이 문서에서는](search-dotnet-sdk-migration-version-9.md) 방법을 설명합니다.

## <a name="requirements-for-the-sdk"></a>SDK의 요구 사항
1. 비주얼 스튜디오 2017 이상.
2. 사용자 고유의 Azure 인지 검색 서비스입니다. SDK를 사용하려면 서비스 이름과 하나 이상의 API 키가 필요합니다. [포털에서 서비스 만들기](search-create-service-portal.md)는 이들 단계를 통해 도움을 받을 수 있습니다.
3. Visual Studio에서 "NuGet 패키지 관리"를 사용하여 Azure 인지 검색 .NET SDK [NuGet 패키지를](https://www.nuget.org/packages/Microsoft.Azure.Search) 다운로드합니다. NuGet.org에서 패키지 이름 `Microsoft.Azure.Search`를 검색하기만 하면 됩니다. 아니면 기능 하위 집합만 필요한 경우에는 위에 나온 다른 패키지 이름 중 하나를 검색합니다.

Azure 인지 검색 .NET SDK는 .NET 프레임워크 4.5.2 이상과 .NET Core 2.0 이상을 대상으로 하는 응용 프로그램을 지원합니다.

## <a name="core-scenarios"></a>핵심 시나리오
검색 애플리케이션에서 수행해야 할 몇 가지 작업이 있습니다. 이 자습서에서는 이러한 핵심 시나리오를 다룹니다.

* 인덱스 만들기
* 문서를 사용하여 인덱스 채우기
* 전체 텍스트 검색 및 필터를 사용하여 문서 검색하기

다음 샘플 코드는 이러한 각 시나리오를 보여 줍니다. 사용자 애플리케이션에서 코드 조각을 자유롭게 사용하세요.

### <a name="overview"></a>개요
"호텔"이라는 이름의 새로운 인덱스를 탐색하려는 샘플 애플리케이션은 몇몇 문서로 채운 다음, 일부 검색 쿼리를 실행합니다. 전체 흐름을 보여주는 주 프로그램은 다음과 같습니다.

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    string indexName = configuration["SearchIndexName"];

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteIndexIfExists(indexName, serviceClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateIndex(indexName, serviceClient);

    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);

    ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

> [!NOTE]
> [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)에서 이 연습에 사용된 샘플 애플리케이션의 전체 소스 코드를 찾을 수 있습니다.
> 
>

단계별로 연습해보겠습니다. 먼저 새로운 `SearchServiceClient`을(를) 만들어야 합니다. 이 개체를 통해 인덱스를 관리할 수 있습니다. 하나를 구성하려면 Azure 인지 검색 서비스 이름과 관리자 API 키를 제공해야 합니다. [샘플 애플리케이션](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)의 `appsettings.json` 파일에 이 정보를 입력할 수 있습니다.

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

> [!NOTE]
> 잘못된 키(예, 관리 키가 필요한 쿼리 키)를 제공하는 경우, `SearchServiceClient`은(는) `Indexes.Create`와(과) 같은 작업 메서드를 처음 호출하면 "사용할 수 없음"이라는 오류 메시지와 함께 `CloudException`을(를) 발생시킵니다. 이 경우 API 키를 다시 확인하십시오.
> 
> 

다음 몇 줄은 "호텔"이라는 인덱스를 만드는 메서드를 호출하며, 이미 있는 경우 이를 먼저 삭제합니다. 잠시 후 이들 메서드를 연습해보겠습니다.

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, serviceClient);
```

그런 다음 인덱스를 채워야 합니다. 인덱스를 채우려면 `SearchIndexClient`. 두 가지 방법 즉, 키를 생성하거나 `SearchServiceClient`에서 `Indexes.GetClient`을(를) 호출하여 키를 얻을 수 있습니다. 편의를 위해 후자를 사용합니다.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
```

> [!NOTE]
> 일반적인 검색 응용 프로그램에서 인덱스 관리 및 채우기는 검색 쿼리와 는 별도의 구성 요소에 의해 처리될 수 있습니다. `Indexes.GetClient`추가 `SearchCredentials`를 제공하는 문제를 줄일 수 있으므로 인덱스를 채우는 데 편리합니다. 이는 새 `SearchIndexClient`에 `SearchServiceClient`을(를) 만드는 데 사용하는 관리 키를 눌러 수행됩니다. 그러나 쿼리를 실행하는 응용 프로그램의 일부에서는 관리자 키 대신 `SearchIndexClient` 데이터만 읽을 수 있는 쿼리 키를 전달할 수 있도록 직접 쿼리 키를 만드는 것이 좋습니다. 이는 최소 권한의 원칙와 일치하고 애플리케이션을 더욱 안전하게 하는데 도움이 됩니다. 관리 키와 쿼리 키에 대한 자세한 내용은 [여기](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization)에서 확인할 수 있습니다.
> 
> 

이제 `SearchIndexClient`이 생겼고 인덱스를 채울 수 있습니다. 인덱스 채우기는 나중에 안내할 다른 방법으로 수행됩니다.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

마지막으로 몇 가지 검색 쿼리를 실행하고 결과를 표시합니다. 이번에는 다른 `SearchIndexClient`를 사용할 것입니다.

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(indexName, configuration);

RunQueries(indexClientForQueries);
```

`RunQueries` 메서드에 대해서는 이후에 좀 더 자세히 알아보겠습니다. 다음은 새 `SearchIndexClient`를 만드는 코드입니다.

```csharp
private static SearchIndexClient CreateSearchIndexClient(string indexName, IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, indexName, new SearchCredentials(queryApiKey));
    return indexClient;
}
```

이번에는 인덱스에 대해 쓰기 액세스 권한이 필요하지 않으므로 쿼리 키를 사용합니다. [샘플 애플리케이션](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)의 `appsettings.json` 파일에 이 정보를 입력할 수 있습니다.

유효한 서비스 이름 및 API 키로 이 응용 프로그램을 실행하는 경우 출력은 다음과 같이 표시됩니다. 그림으로 할 수 있습니다.)

    Deleting index...

    Creating index...

    Uploading documents...

    Waiting for documents to be indexed...

    Search the entire index for the term 'motel' and return only the HotelName field:

    Name: Secret Point Motel

    Name: Twin Dome Motel


    Apply a filter to the index to find hotels with a room cheaper than $100 per night, and return the hotelId and description:

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Times Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.

    HotelId: 2
    Description: The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.


    Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

    Name: Triple Landscape Hotel
    Last renovated on: 9/20/2015 12:00:00 AM +00:00

    Name: Twin Dome Motel
    Last renovated on: 2/18/1979 12:00:00 AM +00:00


    Search the hotel names for the term 'hotel':

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

    Complete.  Press any key to end application... 

애플리케이션의 전체 소스 코드는 이 문서의 마지막 부분에 제공됩니다.

다음으로, `Main`에 의해 호출된 각 메서드를 좀더 자세히 살펴볼 것입니다.

### <a name="creating-an-index"></a>인덱스 만들기
을 `SearchServiceClient` `Main` 만든 후 이미 있는 경우 "호텔" 인덱스를 삭제합니다. 해당 삭제는 다음 방법으로 수행됩니다.

```csharp
private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists(indexName))
    {
        serviceClient.Indexes.Delete(indexName);
    }
}
```

이 메서드는 주어진 `SearchServiceClient` 을(를) 사용하여 인덱스가 존재하는지 확인하고 존재하면 이를 삭제합니다.

> [!NOTE]
> 이 문서의 예제 코드는 단순하게 Azure Cognitive Search .NET SDK의 동기 메서드를 사용합니다. 확장성과 응답성이 유지하기 위해 사용 중인 애플리케이션에서 비동기 메서드를 사용하는 것이 좋습니다. 예를 들어, 위의 메서드의 경우 `Exists` 및 `Delete` 대신`ExistsAsync` 및 `DeleteAsync`을(를) 사용할 수 있습니다.
> 
> 

그 다음 `Main`이(가) 이 메서드를 호출하여 새 "호텔" 인덱스를 만듭니다.

```csharp
private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    
    serviceClient.Indexes.Create(definition);
}
```

이 메서드는 새 인덱스의 스키마를 정의하는 `Field` 개체 목록과 함께 새 `Index` 개체를 만듭니다. 각 필드에는 이름, 데이터 유형, 그리고 검색 동작을 정의하는 몇 가지 특성이 있습니다. `FieldBuilder` 클래스는 리플렉션을 사용하여 지정된 `Hotel` 모델 클래스의 public 속성 및 특성을 검사하고 인덱스에 대한 `Field` 개체 목록을 만듭니다. `Hotel` 클래스에 대해서는 이후에 좀 더 자세히 알아보겠습니다.

> [!NOTE]
> 필요한 경우 `FieldBuilder`를 사용하는 대신, `Field` 개체 목록을 항상 직접 만들 수 있습니다. 예를 들어 모델 클래스를 사용하지 않으려고 하거나, 특성을 추가하여 수정하지 않으려는 기존 모델 클래스를 사용해야 할 수 있습니다.
>
> 

필드 외에도 점수 매기기 프로필, 제안자 또는 CORS 옵션을 인덱스에 추가할 수도 있습니다(이러한 매개 변수는 간결성을 위해 샘플에서 생략). [SDK 참조및](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index) [Azure 인지 검색 REST API 참조에서](https://docs.microsoft.com/rest/api/searchservice/)인덱스 개체 및 해당 구성 부분에 대한 자세한 정보를 찾을 수 있습니다.

### <a name="populating-the-index"></a>인덱스 채우기
다음 `Main` 단계에서새로 만든 인덱스를 채웁니다. 이 인덱스 채우기는 다음 메서드에서 수행됩니다(일부 코드는 "..."로 대체되었습니다. 일러스트레이션을 위해 사용됩니다.  전체 데이터 채우기 코드에 대한 전체 샘플 솔루션을 참조하십시오.

```csharp
private static void UploadDocuments(ISearchIndexClient indexClient)
{
    var hotels = new Hotel[]
    {
        new Hotel()
        {
            HotelId = "1",
            HotelName = "Secret Point Motel",
            ...
            Address = new Address()
            {
                StreetAddress = "677 5th Ave",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Budget Room, 1 Queen Bed (Cityside)",
                    ...
                },
                new Room()
                {
                    Description = "Budget Room, 1 King Bed (Mountain View)",
                    ...
                },
                new Room()
                {
                    Description = "Deluxe Room, 2 Double Beds (City View)",
                    ...
                }
            }
        },
        new Hotel()
        {
            HotelId = "2",
            HotelName = "Twin Dome Motel",
            ...
            {
                StreetAddress = "140 University Town Center Dr",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Suite, 2 Double Beds (Mountain View)",
                    ...
                },
                new Room()
                {
                    Description = "Standard Room, 1 Queen Bed (City View)",
                    ...
                },
                new Room()
                {
                    Description = "Budget Room, 1 King Bed (Waterfront View)",
                    ...
                }
            }
        },
        new Hotel()
        {
            HotelId = "3",
            HotelName = "Triple Landscape Hotel",
            ...
            Address = new Address()
            {
                StreetAddress = "3393 Peachtree Rd",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Standard Room, 2 Queen Beds (Amenities)",
                    ...
                },
                new Room ()
                {
                    Description = "Standard Room, 2 Double Beds (Waterfront View)",
                    ...
                },
                new Room()
                {
                    Description = "Deluxe Room, 2 Double Beds (Cityside)",
                    ...
                }
            }
        }
    };

    var batch = IndexBatch.Upload(hotels);

    try
    {
        indexClient.Documents.Index(batch);
    }
    catch (IndexBatchException e)
    {
        // Sometimes when your Search service is under load, indexing will fail for some of the documents in
        // the batch. Depending on your application, you can take compensating actions like delaying and
        // retrying. For this simple demo, we just log the failed document keys and continue.
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

    Console.WriteLine("Waiting for documents to be indexed...\n");
    Thread.Sleep(2000);
}
```

이 메서드는 네 부분으로 이루어져 있습니다. 첫 번째는 인덱스에 `Hotel` 업로드할 입력 `Room` 데이터역할을 하는 3개의 개체가 있는 3개의 개체 배열을 만듭니다. 이 데이터는 간단히 하기 위해 하드 코딩합니다. 사용 중인 애플리케이션의 경우, 데이터는 SQL 데이터베이스와 같은 외부 데이터 원본에서 나올 가능성이 높습니다.

두 번째 부분은 문서를 포함하는 `IndexBatch`를 만듭니다. 배치를 만들 때 배치에 적용할 작업을 지정합니다. 이 경우에는 `IndexBatch.Upload`를 호출하여 지정합니다. 그런 다음 일괄 처리는 메서드에 의해 `Documents.Index` Azure 인지 검색 인덱스에 업로드됩니다.

> [!NOTE]
> 이 예에서는 문서를 업로드하는 것입니다. 변경 사항을 기존 문서에 병합하거나 문서를 삭제하려면 `IndexBatch.Merge`, `IndexBatch.MergeOrUpload` 또는 `IndexBatch.Delete`를 호출하여 배치를 만듭니다. 또한 개체 컬렉션을 `IndexBatch.New` `IndexAction` 가져와 문서에서 특정 작업을 수행하도록 Azure Cognitive Search를 알려주는 호출을 수행하여 서로 다른 작업을 단일 일괄 처리로 혼합할 수도 있습니다. `IndexAction.Merge`, `IndexAction.Upload` 등을 비롯한 해당 메서드를 호출하여 각 `IndexAction`과 자체 작업을 만들 수 있습니다.
> 
> 

이 메서드의 세 번째 부분은 인덱싱에 중요한 오류 사례를 처리하는 catch 블록입니다. Azure Cognitive Search 서비스가 일괄 처리에서 일부 문서를 인덱싱하지 `Documents.Index`못하면 에 의해 `IndexBatchException` throw됩니다. 이 예외는 서비스가 과부하가 있는 동안 문서를 인덱싱하는 경우 발생할 수 있습니다. **이 경우 코드에서 명시적으로 처리하는 것이 좋습니다.** 실패한 문서 인덱싱을 잠시 후 다시 시도하거나, 샘플에서 하던 것처럼 기록하여 계속하거나, 애플리케이션의 데이터 일관성 요구 사항에 따라 다른 작업을 수행할 수 있습니다.

> [!NOTE]
> 메서드를 [`FindFailedActionsToRetry`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception.findfailedactionstoretry) 사용하여 이전 호출에서 실패한 작업만 포함하는 새 일괄 처리를 `Index`생성할 수 있습니다. 이 메서드를 [StackOverflow](https://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry)에서 적절히 사용하는 방법을 확인할 수 있습니다.
>
>

마지막으로, `UploadDocuments` 메서드가 2초 동안 지연됩니다. 인덱싱은 Azure Cognitive Search 서비스에서 비동기적으로 발생하므로 샘플 응용 프로그램은 문서를 검색할 수 있는지 확인하기 위해 잠시 기다려야 합니다. 이와 같이 데모, 테스트, 샘플 애플리케이션에서는 일반적으로 지연만 필요합니다.

<a name="how-dotnet-handles-documents"></a>

#### <a name="how-the-net-sdk-handles-documents"></a>.NET SDK가 문서를 처리하는 방법
Azure Cognitive Search .NET SDK가 사용자 정의 클래스의 인스턴스를 `Hotel` 인덱스에 업로드할 수 있는 방법을 궁금할 수 있습니다. 이 질문에 대답하기 위해 `Hotel` 클래스를 살펴보겠습니다.

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsSearchable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.EnLucene)]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("Description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    // SmokingAllowed reflects whether any room in the hotel allows smoking.
    // The JsonIgnore attribute indicates that a field should not be created 
    // in the index for this property and it will only be used by code in the client.
    [JsonIgnore]
    public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? Rating { get; set; }

    public Address Address { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }

    public Room[] Rooms { get; set; }
}
```

가장 먼저 주의해야 할 점은 `Hotel` 클래스의 각 공용 속성의 이름이 인덱스 정의에서 이름이 같은 필드에 매핑된다는 것입니다. 각 필드가 소문자("camel case")로 시작하도록 하려면 SDK에 속성 이름을 클래스의 `[SerializePropertyNamesAsCamelCase]` 특성과 함께 자동으로 낙타 대/소문자로 매핑하도록 지시할 수 있습니다. 이 시나리오는 .NET의 "Pascal 사례" 명명 지침을 위반하지 않고 대상 스키마가 응용 프로그램 개발자의 제어 를 벗어난 데이터 바인딩을 수행하는 .NET 응용 프로그램에서 일반적입니다.

> [!NOTE]
> Azure 인지 검색 .NET SDK는 [뉴턴소프트 JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) 라이브러리를 사용하여 JSON에서 사용자 지정 모델 개체를 직렬화하고 역직렬화합니다. 필요한 경우 직렬화를 사용자 지정할 수 있습니다. 자세한 내용은 [JSON.NET 있는 사용자 지정 직렬화를](#JsonDotNet)참조하십시오.
> 
> 

두 번째로 주의해야 할 점은 각 속성이 `IsFilterable` `IsSearchable`". `Key` `Analyzer`및. 이러한 특성은 Azure [인지 검색 인덱스의 해당 필드 특성에](/rest/api/searchservice/create-index)직접 매핑됩니다. 클래스는 `FieldBuilder` 이러한 속성을 사용하여 인덱스에 대한 필드 정의를 생성합니다.

클래스의 `Hotel` 세 번째 중요한 점은 공용 속성의 데이터 형식입니다. 이러한 속성의 .NET 유형은 인덱스 정의의 동등한 필드 유형에 매핑합니다. 예를 들어, `Category` 문자열 속성은 `Edm.String` 유형인 `category` 필드에 매핑됩니다. . `bool?` `Edm.Boolean` `DateTimeOffset?`등 간에 유사한 형식 매핑이 `Edm.DateTimeOffset` 있습니다. 형식 매핑에 대한 특정 규칙은 `Documents.Get` [Azure 인지 검색 .NET SDK 참조의](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get)메서드와 함께 문서화됩니다. `FieldBuilder` 클래스는 사용자를 위해 이러한 매핑을 처리하는 역할을 하지만 serialization 문제를 해결해야 하는 경우에도 알아두면 도움이 될 수 있습니다.

`SmokingAllowed` 숙소를 알게 되었습니까?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

이 `JsonIgnore` 속성의 특성은 `FieldBuilder` 인덱스를 필드로 직렬화하지 않도록 지시합니다.  이는 응용 프로그램에서 도우미로 사용할 수 있는 클라이언트 측 계산 속성을 만드는 좋은 방법입니다.  이 경우, `SmokingAllowed` 숙소는 `Room` `Rooms` 본인이 흡연을 허용하는지 여부를 반영합니다.  모두 거짓이라면 호텔 전체가 흡연을 허용하지 않음을 나타냅니다.

와 같은 `Address` 일부 `Rooms` 속성은 .NET 클래스의 인스턴스입니다.  이러한 속성은 보다 복잡한 데이터 구조를 나타내므로 인덱스에 [복잡한 데이터 형식이](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) 있는 필드가 필요합니다.

속성은 `Address` 아래에 정의된 클래스의 `Address` 여러 값 집합을 나타냅니다.

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
{
    public partial class Address
    {
        [IsSearchable]
        public string StreetAddress { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string City { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string StateProvince { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string PostalCode { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string Country { get; set; }
    }
}
```

이 클래스에는 미국 또는 캐나다의 주소를 설명하는 데 사용되는 표준 값이 포함되어 있습니다. 이와 같은 형식을 사용하여 인덱스에서 논리 필드를 함께 그룹화할 수 있습니다.

속성은 `Rooms` 개체의 `Room` 배열을 나타냅니다.

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
{
    public partial class Room
    {
        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrMicrosoft)]
        [JsonProperty("Description_fr")]
        public string DescriptionFr { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string Type { get; set; }

        [IsFilterable, IsFacetable]
        public double? BaseRate { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string BedOptions { get; set; }

        [IsFilterable, IsFacetable]
        public int SleepsCount { get; set; }

        [IsFilterable, IsFacetable]
        public bool? SmokingAllowed { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string[] Tags { get; set; }
    }
}
```

.NET 및 해당 인덱스 스키마의 데이터 모델은 최종 사용자에게 제공하려는 검색 환경을 지원하도록 설계되어야 합니다. .NET의 각 최상위 개체( 즉 인덱스의 문서)는 사용자 인터페이스에 표시될 검색 결과에 해당합니다. 예를 들어 호텔 검색 응용 프로그램에서 최종 사용자는 호텔 이름, 호텔의 기능 또는 특정 객실의 특성으로 검색할 수 있습니다. 몇 가지 쿼리 예제를 조금 나중에 살펴보겠습니다.

사용자 고유의 클래스를 사용하여 인덱스의 문서와 상호 작용하는 이 기능은 양방향으로 작동합니다. 다음 섹션에서 볼 수 있듯이 검색 결과를 검색하고 SDK가 자동으로 원하는 유형으로 역직렬화하도록 할 수도 있습니다.

> [!NOTE]
> 또한 Azure Cognitive Search .NET SDK는 필드 이름에 `Document` 대한 필드 이름의 키/값 매핑인 클래스를 사용하여 동적으로 형식이 설정된 문서를 지원합니다. 디자인 타임에서 인덱스 스키마를 알 수 없거나 특정 모델 클래스에 바인딩하기 불편한 시나리오에서 유용합니다. 문서를 처리하는 SDK의 모든 메서드에는 `Document` 클래스와 연동하는 오버로드와 제네릭 유형 매개 변수를 취하는 강력한 유형의 오버로드가 있습니다. 이 자습서의 샘플 코드에서는 후자만 사용됩니다. [ `Document` 클래스는](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document) 에서 `Dictionary<string, object>`상속됩니다.
> 
>

**Null 허용 데이터 형식을 사용해야 하는 이유**

Azure Cognitive Search 인덱스에 매핑하도록 사용자 고유의 모델 클래스를 디자인할 때는 `int` 값 형식의 속성을 `bool?` `bool` `bool` 선언하고 null(예: 대신)하는 것이 좋습니다. Null이 허용되지 않는 속성을 사용하는 경우 인덱스의 문서가 해당 필드에 대해 Null 값을 포함하지 않도록 **보장** 해야 합니다. SDK나 Azure 인지 검색 서비스는 이를 적용하는 데 도움이 되지 않습니다.

이것은 가상의 문제가 아닙니다. `Edm.Int32` 형식인 기존 인덱스에 새 필드를 추가하는 시나리오를 가정하겠습니다. 인덱스 정의를 업데이트한 후 모든 문서는 새 필드에 대한 null 값을 갖습니다(Azure Cognitive Search에서 모든 형식은 null가능하기 때문에). 그런 다음 해당 필드에 대해 Null이 허용되지 않는 `int` 속성으로 모델 클래스를 사용하는 경우 문서를 검색하려고 시도할 때 다음과 같은 `JsonSerializationException`이 발생합니다.

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

이러한 이유로 모델 클래스에는 Null을 허용하는 형식을 사용하는 것이 가장 좋습니다.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>JSON.NET으로 직렬화 사용자 지정
이 SDK는 문서를 직렬화 및 역직렬화하는 데 JSON.NET을 사용합니다. 사용자 고유의 `JsonConverter` 또는 `IContractResolver`을 정의하여 필요한 경우 직렬화 및 직렬화를 사용자 지정할 수 있습니다. 자세한 내용은 JSON.NET [문서를](https://www.newtonsoft.com/json/help/html/Introduction.htm)참조하십시오. 이 기능은 Azure Cognitive Search 및 기타 고급 시나리오와 함께 사용할 수 있도록 응용 프로그램에서 기존 모델 클래스를 조정하려는 경우에 유용할 수 있습니다. 예를 들어 사용자 지정 serialization으로 다음을 수행할 수 있습니다.

* 모델 클래스의 특정 속성을 문서 필드로 저장하는 데 포함 또는 제외할 수 있습니다.
* 코드의 속성 이름과 인덱스의 필드 이름을 매핑할 수 있습니다.
* 문서 필드에 속성에 매핑하는 데 사용할 수 있는 사용자 지정 특성을 만듭니다.

GitHub의 Azure 인지 검색 .NET SDK에 대한 단위 테스트에서 사용자 지정 직렬화를 구현하는 예제를 찾을 수 있습니다. 적절한 시작 지점은 [이 폴더](https://github.com/Azure/azure-sdk-for-net/tree/4f6f4e4c90200c1b0621c4cead302a91e89f2aba/sdk/search/Microsoft.Azure.Search/tests/Tests/Models)입니다. 여기에는 사용자 지정 serialization 테스트에 사용되는 클래스가 있습니다.

### <a name="searching-for-documents-in-the-index"></a>인덱스에서 문서 검색
샘플 응용 프로그램의 마지막 단계는 인덱스의 일부 문서를 검색하는 것입니다.

```csharp
private static void RunQueries(ISearchIndexClient indexClient)
{
    SearchParameters parameters;
    DocumentSearchResult<Hotel> results;

    Console.WriteLine("Search the entire index for the term 'motel' and return only the HotelName field:\n");

    parameters =
        new SearchParameters()
        {
            Select = new[] { "HotelName" }
        };

    results = indexClient.Documents.Search<Hotel>("motel", parameters);

    WriteDocuments(results);

    Console.Write("Apply a filter to the index to find hotels with a room cheaper than $100 per night, ");
    Console.WriteLine("and return the hotelId and description:\n");

    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(r: r/BaseRate lt 100)",
            Select = new[] { "HotelId", "Description" }
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    parameters =
        new SearchParameters()
        {
            OrderBy = new[] { "LastRenovationDate desc" },
            Select = new[] { "HotelName", "LastRenovationDate" },
            Top = 2
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.WriteLine("Search the entire index for the term 'hotel':\n");

    parameters = new SearchParameters();
    results = indexClient.Documents.Search<Hotel>("hotel", parameters);

    WriteDocuments(results);
}
```

쿼리를 실행할 때마다 이 메서드는 먼저 새 `SearchParameters` 개체를 만듭니다. 이 개체는 정렬, 필터링, 페이징 및 면작업과 같은 쿼리에 대한 추가 옵션을 지정하는 데 사용됩니다. 이 메서드에서 다른 쿼리에 대해 `Filter`, `Select`, `OrderBy` 및 `Top` 속성을 설정합니다. 모든 `SearchParameters` 속성은 [여기](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters)에 설명되어 있습니다.

다음 단계를 실제로 검색 쿼리를 실행합니다. 검색 실행은 메서드를 `Documents.Search` 사용하여 수행됩니다. 각 쿼리에 대해 문자열(또는 검색 텍스트가 없는 경우 `"*"`)로 사용할 검색 텍스트와 앞서 만든 검색 매개 변수를 전달합니다. 또한 SDK에 검색 결과 내 문서를 `Hotel` 유형의 개체로 역직렬화하도록 명령하는 `Documents.Search`에 대한 유형 매개 변수로 `Hotel`을(를) 지정할 수도 있습니다.

> [!NOTE]
> 검색 쿼리 식 구문에 대한 자세한 내용은 [여기](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search)에서 찾을 수 있습니다.
> 
> 

마지막으로, 각 쿼리 후에 이 메서드는 검색 결과에서 일치하는 모든 항목을 반복하여 각 문서를 콘솔에 인쇄합니다.

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

이러한 각 쿼리를 차례대로 조금 더 자세히 살펴보겠습니다. 첫 번째 쿼리를 실행하는 코드는 다음과 같습니다.

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "HotelName" }
    };

results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

이 경우 검색 가능한 필드에서 "motel"이라는 단어에 대한 전체 인덱스를 검색하고 `Select` 매개 변수에 지정된 대로 호텔 이름만 검색하려고 합니다. 결과는 다음과 같습니다.

    Name: Secret Point Motel

    Name: Twin Dome Motel

다음 쿼리는 좀 더 흥미롭습니다.  야간 요금이 $100 미만인 객실을 찾고 호텔 ID와 설명만 반환합니다.

```csharp
parameters =
    new SearchParameters()
    {
        Filter = "Rooms/any(r: r/BaseRate lt 100)",
        Select = new[] { "HotelId", "Description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

이 쿼리는 OData `$filter` 식 `Rooms/any(r: r/BaseRate lt 100)`을 사용하여 인덱스의 문서를 필터링합니다. 이렇게 하면 [모든 연산자가](https://docs.microsoft.com/azure/search/search-query-odata-collection-operators) 룸 컬렉션의 모든 항목에 'BaseRate lt 100'을 적용합니다. Azure 인지 검색에서 지원하는 OData 구문에 대한 자세한 내용은 여기에서 확인할 수 [있습니다.](https://docs.microsoft.com/azure/search/query-odata-filter-orderby-syntax)

다음은 쿼리 결과입니다.

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York...

    HotelId: 2
    Description: The hotel is situated in a nineteenth century plaza, which has been expanded and renovated to...

다음에는 가장 최근에 리모델링한 상위 두 개 호텔을 찾은 후 호텔 이름 및 마지막 보수 날짜를 표시하려고 합니다. 코드는 다음과 같습니다. 

```csharp
parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "LastRenovationDate desc" },
        Select = new[] { "HotelName", "LastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

이 경우 OData 구문을 다시 사용하여 `OrderBy` 매개 변수를 `lastRenovationDate desc`로 지정합니다. 또한 `Top`을 2로 설정하여 상위 2개의 문서만 표시합니다. 앞에서 나온 것처럼 `Select`를 설정하여 반환할 필드를 지정합니다.

결과는 다음과 같습니다.

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

마지막으로, 우리는 단어 "호텔"과 일치하는 모든 호텔 이름을 찾으려면 :

```csharp
parameters = new SearchParameters()
{
    SearchFields = new[] { "HotelName" }
};
results = indexClient.Documents.Search<Hotel>("hotel", parameters);

WriteDocuments(results);
```

결과는 다음과 같습니다. 여기서는 `Select` 속성을 지정하지 않았으므로 모든 필드가 포함됩니다.

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

이 단계에서 자습서를 완료하지만 여기서 멈추지 마십시오. **다음 단계는 Azure 인지 검색에 대해 자세히 알아보기 위한 추가 리소스를 제공합니다.

## <a name="next-steps"></a>다음 단계
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) 및 [REST API](https://docs.microsoft.com/rest/api/searchservice/)에 대한 참고 자료를 찾아봅니다.
* [명명 규칙](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) 을 검토하여 다양한 개체 명명에 대한 규칙에 알아보십시오.
* Azure 인지 검색에서 [지원되는 데이터 형식을](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) 검토합니다.
