---
title: 레거시를 사용하여 MongoDB의 데이터 복사
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 Mongo DB에서 지원되는 싱크 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 08/12/2019
ms.openlocfilehash: 803e34a93e8019cfc2577bfaab3ba13c409c6b01
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418170"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Azure Data Factory를 사용하여 MongoDB에서 데이터 복사

> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-on-premises-mongodb-connector.md)
> * [현재 버전](connector-mongodb.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 MongoDB 데이터베이스에서 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

>[!IMPORTANT]
>ADF는 이 ODBC 기반 구현에 비해 향상된 MongoDB 지원을 제공하는 새 MongoDB 커넥터를 발표했습니다. 자세한 내용은 [MongoDB 커넥터](connector-mongodb.md) 문서를 참조하세요. 이 레거시 MongoDB 커넥터는 역방향 호환성을 위해 현재 상태 그대로 계속 지원되며, 새 워크로드인 경우 새 커넥터를 사용하세요.

## <a name="supported-capabilities"></a>지원되는 기능

MongoDB 데이터베이스에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 MongoDB 커넥터는 다음을 지원합니다.

- MongoDB **버전 2.4, 2.6, 3.0, 3.2, 3.4 및 3.6**
- **Basic** 또는 **Anonymous** 인증을 사용하여 데이터를 복사합니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

통합 런타임은 기본 제공 MongoDB 드라이버를 제공하므로 MongoDB에서 데이터를 복사할 때 수동으로 드라이버를 설치할 필요가 없습니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 MongoDB 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

MongoDB 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type |형식 속성은 **MongoDb**로 설정해야 합니다. |예 |
| 서버 |MongoDB 서버의 IP 주소 또는 호스트 이름입니다. |예 |
| 포트 |MongoDB 서버가 클라이언트 연결을 수신하는 데 사용하는 TCP 포트입니다. |아니요(기본값: 27017) |
| databaseName |액세스하려는 MongoDB 데이터베이스의 이름입니다. |예 |
| authenticationType | MongoDB 데이터베이스에 연결하는 데 사용되는 인증 형식입니다.<br/>허용된 값은 **기본**및 **익명**입니다. |예 |
| 사용자 이름 |MongoDB에 액세스하는 사용자 계정입니다. |예(기본 인증을 사용하는 경우) |
| password |사용자에 대한 암호입니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. |예(기본 인증을 사용하는 경우) |
| authSource |인증에 대한 자격 증명을 확인하는 데 사용하려는 MongoDB 데이터베이스의 이름입니다. |아니요. 기본 인증의 경우 기본값은 관리자 계정 및 databaseName 속성을 사용하여 지정된 데이터베이스를 사용하는 것입니다. |
| enableSsl | 서버에 대한 연결이 TLS를 사용하여 암호화되는지 여부를 지정합니다. 기본값은 False입니다.  | 예 |
| allowSelfSignedServerCert | 서버의 자체 서명된 인증서를 허용할지 여부를 지정합니다. 기본값은 False입니다.  | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임입니다.](concepts-integration-runtime.md) [필수 구성 조건](#prerequisites) 섹션에서 자세히 알아보십시오. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |예 |

**예제:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDb",
        "typeProperties": {
            "server": "<server name>",
            "databaseName": "<database name>",
            "authenticationType": "Basic",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트 및 연결된 서비스](concepts-datasets-linked-services.md)를 참조하세요. MongoDB 데이터 세트에 대해 다음 속성을 지원합니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성을 **MongoDbCollection**으로 설정해야 합니다. | 예 |
| collectionName |MongoDB 데이터베이스에 있는 컬렉션의 이름입니다. |예 |

**예제:**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<Collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 MongoDB 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="mongodb-as-source"></a>MongoDB를 원본으로

다음 속성은 복사 활동 **소스** 섹션에서 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성을 **MongoDbSource**로 설정해야 합니다. | 예 |
| Query |사용자 지정 SQL-92 쿼리를 사용하여 데이터를 읽습니다. 예: select * from MyTable. |아니요(데이터 세트의 "collectionName"이 지정된 경우) |

**예제:**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "MongoDbSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

> [!TIP]
> SQL 쿼리를 지정할 때 DateTime 형식에 주의합니다. 예: `SELECT * FROM Account WHERE LastModifiedDate >= '2018-06-01' AND LastModifiedDate < '2018-06-02'` 또는 매개 변수 `SELECT * FROM Account WHERE LastModifiedDate >= '@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}' AND LastModifiedDate < '@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'` 사용

## <a name="schema-by-data-factory"></a>Data Factory에서의 스키마

Azure Data Factory 서비스는 컬렉션의 **최신 100개 문서를** 사용하여 MongoDB 컬렉션에서 스키마를 추론합니다. 이러한 100개의 문서에 전체 스키마가 포함되어 있지 않는 경우, 일부 열은 복사 작업 중 무시될 수 있습니다.

## <a name="data-type-mapping-for-mongodb"></a>MongoDB에 대한 데이터 형식 매핑

MongoDB에서 데이터를 복사하는 경우 MongoDB 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| MongoDB 데이터 형식 | Data Factory 중간 데이터 형식 |
|:--- |:--- |
| 이진 |Byte[] |
| 부울 |부울 |
| Date |DateTime |
| NumberDouble |Double |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |String |
| String |String |
| UUID |Guid |
| Object |중첩 구분 기호로 "_"를 사용한 평면화된 열에 다시 정규화 |

> [!NOTE]
> 가상 테이블을 사용한 배열 지원에 대해 알아보려면 [가상 테이블을 사용하는 복합 형식에 대한 지원](#support-for-complex-types-using-virtual-tables) 섹션을 참조하세요.
>
> 현재 DBPointer, JavaScript, 최대/최소 키, 정규식, 기호, 타임 스탬프, 그리고 정의되지 않은 MongoDB 데이터 형식은 지원되지 않습니다.

## <a name="support-for-complex-types-using-virtual-tables"></a>가상 테이블을 사용하는 복합 형식에 대한 지원

Azure Data Factory는 기본 제공 ODBC 드라이버를 사용하여 MongoDB 데이터베이스에 연결하고 데이터를 복사합니다. 서로 다른 형식의 문서 간에 배열 또는 개체와 같은 복합 형식의 경우, 드라이버는 해당 가상 테이블에 데이터를 다시 정규화합니다. 특히, 테이블에 그러한 열이 포함되어 있으면 드라이버는 다음 가상 테이블을 생성합니다.

* **기본 테이블**: 복합 형식 열을 제외하고 실제 테이블과 동일한 데이터가 포함되어 있습니다. 기본 테이블은 나타내는 실제 테이블과 동일한 이름을 사용합니다.
* **가상 테이블** : 각 복합 형식 열에 대해 생성되며, 중첩된 데이터를 확장합니다. 가상 테이블 이름은 실제 테이블의 이름, 구분 기호 "_", 그리고 배열 또는 개체 이름을 사용하여 지정합니다.

가상 테이블은 실제 테이블의 데이터를 나타내며, 드라이버가 정규화되지 않은 데이터에 액세스할 수 있도록 합니다. 가상 테이블을 쿼리 및 조인하여 MongoDB 배열의 콘텐츠에 액세스할 수 있습니다.

### <a name="example"></a>예제

예를 들어 여기 ExampleTable은 MongoDB 테이블로, 각 셀의 개체 배열이 있는 하나의 열(송장)과 스칼라 형식의 배열이 있는 하나의 열(등급)이 있습니다.

| _id | 고객 이름 | 송장 | 서비스 수준 | 등급 |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:"123", item:"toaster", price:"456", discount:"0.2"}, {invoice_id:"124", item:"oven", price: "1235", discount: "0.2"}] |은 |[5,6] |
| 2222 |XYZ |[{invoice_id:"135", item:"fridge", price: "12543", discount: "0.0"}] |금 |[1,2] |

드라이버는 이 단일 테이블을 나타내는 여러 개의 가상 테이블을 생성합니다. 첫 번째 가상 테이블은 예제에 표시된 "ExampleTable"이라는 기본 테이블입니다. 기본 테이블에는 모든 원본 테이블의 데이터가 있지만, 배열의 데이터는 생략되었으며 가상 테이블에서 확장됩니다.

| _id | 고객 이름 | 서비스 수준 |
| --- | --- | --- |
| 1111 |ABC |은 |
| 2222 |XYZ |금 |

다음 표는 예제에서 원본 배열을 나타내는 가상 테이블을 나타냅니다. 이들 테이블은 다음을 포함합니다.

* 원본 배열의 행에 해당하는 원본 기본 키 열에 역참조(_id 열을 통해)
* 원본 배열 내에서 데이터의 위치 표시
* 배열 내의 각 요소에 대해 확장된 데이터

**표 "ExampleTable_Invoices":**

| _id | ExampleTable_Invoices_dim1_idx | 송장_id | 항목 | price | 할인 |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |토스터 |456 |0.2 |
| 1111 |1 |124 |오븐 |1235 |0.2 |
| 2222 |0 |135 |냉장고 |12543 |0.0 |

**표 "ExampleTable_Ratings":**

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
