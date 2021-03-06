---
title: 기록 원격 분석 데이터 수집
description: 이 문서에서는 기록 원격 분석 데이터를 수집하는 방법에 대해 설명합니다.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: e0a5e89f256b562ce5f702e9ff1388cb4d021bf5
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437684"
---
# <a name="ingest-historical-telemetry-data"></a>기록 원격 분석 데이터 수집

이 문서에서는 Azure FarmBeats에 기록 센서 데이터를 통합하는 방법에 대해 설명합니다.

장치 및 센서와 같은 사물 인터넷(IoT) 리소스에서 기록 데이터를 수집하는 것은 FarmBeats의 일반적인 시나리오입니다. 장치 및 센서에 대한 메타데이터를 만든 다음 기록 데이터를 표준 형식으로 FarmBeats에 수집합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 진행하기 전에 FarmBeats를 설치하고 IoT 장치에서 기록 데이터를 수집했는지 확인하십시오. 또한 다음 단계에서 설명한 대로 파트너 액세스를 사용하도록 설정해야 합니다.

## <a name="enable-partner-access"></a>파트너 액세스 사용

Azure FarmBeats 인스턴스에 파트너 통합을 사용하도록 설정해야 합니다. 이 단계에서는 Azure FarmBeats 인스턴스에 대한 액세스 권한이 있는 클라이언트를 장치 파트너로 만들고 후속 단계에서 필요한 다음 값을 제공합니다.

- API 끝점: 예를 들어 datahub>\<datahub.net을 https:// Datahub URL입니다.
- 테넌트 ID
- 클라이언트 ID
- 클라이언트 암호
- EventHub 연결 문자열

다음 단계를 수행하세요.

> [!NOTE]
> 다음 단계를 수행하려면 관리자여야 합니다.

1. [https://manage.visualstudio.com](https://portal.azure.com/) 에 로그인합니다.

2. **FarmBeats 버전 1.2.7 이상에 있는 경우 a, b 및 c 단계를 건너뛰고 3단계로 이동합니다.** FarmBeats UI의 오른쪽 상단 모서리에 있는 **설정** 아이콘을 선택하여 FarmBeats 버전을 확인할 수 있습니다.

      a.  Azure **Active 디렉터리** > **앱 등록으로** 이동

      b. FarmBeats 배포의 일부로 만든 **앱 등록을** 선택합니다. FarmBeats 데이터 허브와 이름이 동일합니다.

      다. **API > 클라이언트** **응용 프로그램 추가를** 선택하고 **04bb07795-8ddb-461a-bbee-02f9e1bf7b46을** 입력하고 **범위 승인을**선택합니다. 이렇게 하면 Azure CLI(클라우드 셸)에 액세스하여 다음 단계를 수행할 수 있습니다.

3. Cloud Shell을 엽니다. 이 옵션은 Azure 포털의 오른쪽 위 모서리에 있는 도구 모음에서 사용할 수 있습니다.

    ![Azure 포털 도구 모음](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

4. 환경이 **PowerShell으로**설정되어 있는지 확인합니다. 기본적으로 Bash로 설정됩니다.

    ![파워쉘 툴바 설정](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

5. 홈 디렉토리로 이동합니다.

    ```azurepowershell-interactive 
    cd  
    ```

6. 다음 명령을 실행합니다. 이렇게 하면 홈 디렉터리로 스크립트가 다운로드됩니다.

    ```azurepowershell-interactive 

    wget –q https://aka.ms/farmbeatspartnerscriptv3 -O ./generatePartnerCredentials.ps1

    ```

7. 다음 스크립트를 실행합니다. 스크립트는 **Azure Active Directory** > **개요** 페이지에서 가져올 수 있는 테넌트 ID를 요청합니다.

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1   

    ```

8. 화면의 지침에 따라 API **끝점,** **테넌트 ID,** **클라이언트 ID,** **클라이언트 보안**및 EventHub 연결 문자열에 대한 값을 **캡처합니다.**


## <a name="create-device-or-sensor-metadata"></a>장치 또는 센서 메타데이터 만들기

 이제 필요한 자격 증명이 있으므로 장치 및 센서를 정의할 수 있습니다. 이렇게 하려면 FarmBeats API를 호출하여 메타데이터를 만듭니다. 위의 섹션에서 만든 클라이언트 앱으로 API를 호출해야 합니다.

 FarmBeats Datahub에는 장치 또는 센서 메타데이터를 만들고 관리할 수 있는 다음과 같은 API가 있습니다.

 > [!NOTE]
 > 파트너는 메타데이터를 읽고, 만들고, 업데이트할 수 있는 액세스 권한만 있습니다. **삭제 옵션은 파트너로 제한됩니다.**

- /**DeviceModel**: DeviceModel은 게이트웨이 또는 노드인 장치 유형 및 제조업체와 같은 장치의 메타데이터에 해당합니다.
- /**장치**: 장치는 팜에 있는 물리적 장치에 해당합니다.
- /**센서 모델**: SensorModel은 제조업체, 아날로그 또는 디지털 센서 유형, 주변 온도 및 압력과 같은 센서 측정과 같은 센서의 메타데이터에 해당합니다.
- /**센서**: 센서는 값을 기록하는 물리적 센서에 해당합니다. 센서는 일반적으로 장치 ID가 있는 장치에 연결됩니다.  


|        DeviceModel   |  제안   |
| ------- | -------             |
|     유형(노드, 게이트웨이)        |          장치 유형 - 노드 또는 게이트웨이      |
|          제조업체            |         제조업체 이름    |
|  Productcode                    |  장치 제품 코드 또는 모델 이름 또는 번호. 예를 들어, 엔비로모니터#6800.  |
|            포트          |     디지털 또는 아날로그 포트 이름 및 유형입니다.
|     속성                 |  리소스를 식별하는 이름입니다. 예를 들어 모델 이름 또는 제품 이름입니다.
      Description     | 모델에 대한 의미 있는 설명을 제공합니다.
|    속성          |    제조업체의 추가 속성입니다.   |
|    **디바이스**             |                      |
|   장치 모델 ID     |     연결된 장치 모델의 ID입니다.  |
|  하드웨어 ID          | MAC 주소와 같은 장치의 고유 ID입니다.
|  ReportingInterval        |   보고 간격을 초 단위로 보고합니다.
|  위치            |  장치 위도(-90 ~ +90), 경도(-180 ~ 180) 및 고도(미터).   
|부모장치 ID       |    이 장치가 연결된 상위 장치의 ID입니다. 예를 들어 게이트웨이에 연결된 노드입니다. 노드에는 게이트웨이로 parentDeviceId가 있습니다.  |
|    속성            | 리소스를 식별하는 이름입니다. 장치 파트너는 파트너 측의 장치 이름과 일치하는 이름을 보내야 합니다. 파트너 장치 이름이 사용자 정의된 경우 동일한 사용자 정의 이름을 FarmBeats에 전파해야 합니다.|
|     Description       |      의미 있는 설명을 제공합니다. |
|     속성    |  제조업체의 추가 속성입니다.
|     **센서 모델**        |          |
|       유형(아날로그, 디지털)          |      아날로그이든 디지털이든 센서의 유형입니다.       |
|          제조업체            |       센서 제조업체입니다.     |
|     Productcode| 제품 코드 또는 모델 이름 또는 번호. 예를 들어, RS-CO2-N01. |
|       센서> 이름 측정       | 센서 측정값의 이름입니다. 소문자만 지원됩니다. 다른 깊이에서 측정할 때 깊이를 지정합니다. 예를 들어, soil_moisture_15cm. 이 이름은 원격 분석 데이터와 일치해야 합니다.  |
|          센서데이터유형에> 측정       |원격 분석 데이터 형식입니다. 현재 이중이 지원됩니다.|
|    센서측정 > 유형    |센서 원격 분석 데이터의 측정 유형입니다. 시스템 정의 유형은 앰비언트온도, CO2, 깊이, 전기전도도, 잎습지, 길이, 액체 레벨, 질산염, O2, PH, 인산염, PointInTime, 칼륨, 압력, 레인게이지, 상대 습도, 염도, 토양 모이스처, 토양 온도, 태양 복사, 상태, 시간 지속 시간, UV사, UVIndex, 볼륨, 풍향, 풍진, 풍진, 풍진, 풍진, 풍진, 풍사이다. 더 추가하려면 /ExtendedType API를 참조하십시오.|
|        센서측정 > 유닛              | 센서 원격 분석 데이터의 단위입니다. 시스템 정의 단위는 NoUnit, 섭씨, 화씨, 켈빈, 랭킨, 파스칼, 수은, PSI, 밀리미터, 센티미터, 미터, 인치, 피트, 마일, 킬로미터, 마일스퍼아워, 마일스퍼아시, 마일스퍼초, 킬로미터초, 미터퍼아워, 미터퍼초, 도, 와츠퍼스퀘어미터, 킬로와츠퍼스퀘어미터, 밀리와츠퍼스퀘어센티미터, 밀리줄스퍼스퀘어센티미터, 볼륨리터콘텐츠, 백분율, 파트퍼백만, 마이크로몰, 마이크로몰, 마이크로몰퍼리터, 지멘스퍼스퀘어미터퍼몰, 밀리시멘스퍼센티미터, 센티바, 데시지멘스퍼미터, 킬로파스칼, 체피트리니언콘텐츠, 리터, 밀리리터, 초, 유닉스타임스탬프, 마이크로몰퍼미터스퀘어퍼초, 인치퍼아워 추가하려면 /ExtendedType API를 참조하십시오.|
|    센서측정> 집계유형    |  값은 없음, 평균, 최대값, 최소 값 또는 StandardDeviation일 수 있습니다.  |
|          속성            | 리소스를 식별하는 이름입니다. 예를 들어 모델 이름 또는 제품 이름입니다.  |
|    Description        | 모델에 대한 의미 있는 설명을 제공합니다.|
|   속성       |  제조업체의 추가 속성입니다.|
|    **센서**      |          |
| 하드웨어 ID          |   제조업체에서 설정한 센서의 고유 ID입니다.|
|  센서모델Id     |    관련 센서 모델의 ID입니다.|
| 위치          |  센서 위도(-90 ~ +90), 경도(-180 ~ 180) 및 고도(미터).|
|   포트 > 이름        |  센서가 장치에 연결된 포트의 이름 및 유형입니다. 이 이름은 장치 모델에 정의된 것과 같아야 합니다.|
|    DeviceID  |    센서가 연결된 장치의 ID입니다. |
| 속성            |   리소스를 식별할 이름입니다. 예를 들어 센서 이름 또는 제품 이름, 모델 번호 또는 제품 코드.|
|    Description      | 의미 있는 설명을 제공합니다.|
|    속성        |제조업체의 추가 속성입니다.|

개체에 대한 자세한 내용은 [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)를 참조하십시오.

### <a name="api-request-to-create-metadata"></a>메타데이터 생성을 위한 API 요청

API 요청을 하려면 HTTP(POST) 메서드, API 서비스에 대한 URL 및 URI를 리소스에 결합하여 요청을 쿼리, 데이터 제출, 생성 또는 삭제합니다. 그런 다음 하나 이상의 HTTP 요청 헤더를 추가합니다. API 서비스에 대한 URL은 API 끝점, 즉 Datahub URL(데이터허브>.azurewebsite.nethttps://)입니다.\<  

### <a name="authentication"></a>인증

FarmBeats Datahub는 이전 섹션에서 생성된 다음 자격 증명이 필요한 베어러 인증을 사용합니다.

- 클라이언트 ID
- 클라이언트 암호
- 테넌트 ID

이러한 자격 증명을 사용하여 호출자는 액세스 토큰을 요청할 수 있습니다. 토큰은 다음과 같이 헤더 섹션에서 후속 API 요청에서 보내야 합니다.

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

다음 샘플 파이썬 코드는 FarmBeats에 대한 후속 API 호출에 사용할 수있는 액세스 토큰을 제공합니다. 

```python
import azure 

from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net" [Azure website](https://<yourdatahub>.azurewebsites.net)
CLIENT_ID = "<Your Client ID>"   
CLIENT_SECRET = "<Your Client Secret>"   
TENANT_ID = "<Your Tenant ID>" 
AUTHORITY_HOST = 'https://login.microsoftonline.com' 
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID 
#Authenticating with the credentials 
context = adal.AuthenticationContext(AUTHORITY) 
token_response = context.acquire_token_with_client_credentials(ENDPOINT, CLIENT_ID, CLIENT_SECRET) 
#Should get an access token here 
access_token = token_response.get('accessToken') 
```

**HTTP 요청 헤더**

다음은 FarmBeats Datahub에 대한 API 호출을 수행할 때 지정해야 하는 가장 일반적인 요청 헤더입니다.

- **콘텐츠 유형**: 응용 프로그램 / json
- **권한 부여**: 액세스 토큰> <보유자
- **수락**: 신청서/json

### <a name="input-payload-to-create-metadata"></a>메타데이터 생성을 위한 입력 페이로드

DeviceModel


```json
{
  "type": "Node",
  "manufacturer": "string",
  "productCode": "string",
  "ports": [
    {
      "name": "string",
      "type": "Analog"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

디바이스

```json
{
  "deviceModelId": "string",
  "hardwareId": "string",
  "farmId": "string",
  "reportingInterval": 0,
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "parentDeviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

센서 모델

```json
{
  "type": "Analog",
  "manufacturer": "string",
  "productCode": "string",
  "sensorMeasures": [
    {
      "name": "string",
      "dataType": "Double",
      "type": "string",
      "unit": "string",
      "aggregationType": "None",
      "depth": 0,
      "description": "string"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}

```
센서

```json
{
  "hardwareId": "string",
  "sensorModelId": "string",
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "depth": 0,
  "port": {
    "name": "string",
    "type": "Analog"
  },
  "deviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

다음 샘플 요청은 장치를 만듭니다. 이 요청에는 JSON을 요청 본문에 페이로드로 입력했습니다.

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H  
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  
\"reportingInterval\": 900,  \"name\": \"Device123\",  
\"description\": \"Test Device 123\"}" *
```

다음은 파이썬의 샘플 코드입니다. 이 샘플에 사용된 액세스 토큰은 인증 중에 수신된 것과 동일합니다.

```python
import requests
import json

# Got access token - Calling the Device Model API

headers = {
    "Authorization": "Bearer " + access_token,
    "Content-Type" : "application/json"
    }
payload = '{"type" : "Node", "productCode" : "TestCode", "ports": [{"name": "port1","type": "Analog"}], "name" : "DummyDevice"}'
response = requests.post(ENDPOINT + "/DeviceModel", data=payload, headers=headers)
```

> [!NOTE]
> API는 생성된 각 인스턴스에 대해 고유한 아이디를 반환합니다. 해당 원격 분석 메시지를 보내려면 아이디를 유지해야 합니다.

### <a name="send-telemetry"></a>원격 분석 전송

FarmBeats에서 장치 및 센서를 만들었으니 관련 원격 분석 메시지를 보낼 수 있습니다.

### <a name="create-a-telemetry-client"></a>원격 분석 클라이언트 만들기

처리를 위해 원격 분석을 Azure 이벤트 허브로 보내야 합니다. Azure Event Hubs는 연결된 장치 및 응용 프로그램에서 실시간 데이터(원격 분석) 수집을 가능하게 하는 서비스입니다. 원격 분석 데이터를 FarmBeats에 보내려면 FarmBeats의 이벤트 허브에 메시지를 보내는 클라이언트를 만듭니다. 원격 분석 전송에 대한 자세한 내용은 [Azure 이벤트 허브를](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)참조하십시오.

### <a name="send-a-telemetry-message-as-the-client"></a>클라이언트로 원격 분석 메시지 보내기

이벤트 허브 클라이언트로 설정된 연결이 있으면 이벤트 허브에 JSON으로 메시지를 보낼 수 있습니다.

다음은 지정된 이벤트 허브에 클라이언트로 원격 분석을 보내는 파이썬 샘플 코드입니다.

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub Connection String provided by customer>"
EVENTHUBNAME = "<EventHub Name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical Telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

기록 센서 데이터 형식을 Azure FarmBeats가 이해하는 표준 형식으로 변환합니다. 표준 메시지 형식은 다음과 같습니다.

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

해당 장치 및 센서를 추가한 후 이전 섹션에 설명된 대로 원격 분석 메시지에서 장치 ID 및 센서 ID를 가져옵니다.

다음은 원격 분석 메시지의 예입니다.


 ```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version": "1",
  "sensors": [
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 15
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 42
        }
      ]
    },
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 20
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 89
        }
      ]
    }
  ]
}
```

## <a name="troubleshooting"></a>문제 해결

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>센서에서 기록/스트리밍 데이터를 수집한 후 원격 분석 데이터를 볼 수 없습니다.

**증상**: 장치 또는 센서가 배포되고 FarmBeats에서 장치/센서를 만들고 EventHub에 수집된 원격 분석을 생성했지만 FarmBeats에서 원격 분석 데이터를 얻거나 볼 수는 없습니다.

**시정 조치**:

1. 적절한 파트너 등록을 완료했는지 확인 - datahub swagger로 이동하여 이를 확인하고 /파트너 API로 이동하여 Get을 수행하고 파트너가 등록되었는지 확인할 수 있습니다. 그렇지 않은 경우 [다음 단계를](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) 수행하여 파트너를 추가합니다.

2. 파트너 클라이언트 자격 증명을 사용하여 메타데이터(장치 모델, 장치, 센서 모델, 센서)를 만들어야 합니다.

3. 올바른 원격 분석 메시지 형식을 사용했습니까(아래에 지정된 대로).

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

## <a name="next-steps"></a>다음 단계

REST API 기반 통합 세부 정보에 대한 자세한 내용은 [REST API](rest-api-in-azure-farmbeats.md)를 참조하십시오.
