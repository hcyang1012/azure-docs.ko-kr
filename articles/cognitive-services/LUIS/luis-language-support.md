---
title: 언어 지원 - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS는 서비스 내에 다양한 기능을 포함합니다. 모든 기능이 동일한 언어 패리티에 있는 것은 아닙니다. 관심 있는 기능이 사용자가 원하는 언어 문화권에서 지원되는지 확인합니다. LUIS 앱은 문화권에 관련되며 설정된 후에는 변경할 수 없습니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 82efa70b30e829cfedd0b1fa7a21fd06949aa6d5
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80744156"
---
# <a name="language-and-region-support-for-luis"></a>LUIS에 대한 언어 및 지역 지원

LUIS는 서비스 내에 다양한 기능을 포함합니다. 모든 기능이 동일한 언어 패리티에 있는 것은 아닙니다. 관심 있는 기능이 사용자가 원하는 언어 문화권에서 지원되는지 확인합니다. LUIS 앱은 문화권에 관련되며 설정된 후에는 변경할 수 없습니다.

## <a name="multi-language-luis-apps"></a>다국어 LUIS 앱

챗봇과 같은 다국어 LUIS 클라이언트 애플리케이션이 필요한 경우, 몇 가지 옵션이 있습니다. LUIS가 모든 언어를 지원하는 경우에는 언어별로 LUIS 앱을 개발합니다. 각 LUIS 앱에는 고유한 앱 ID와 엔드포인트 로그가 있습니다. 지원되지 않는 언어 LUIS에 대한 Language Understanding을 제공해야 하는 경우, [Microsoft Translator API](../Translator/translator-info-overview.md)를 사용하여 발화를 지원되는 언어로 번역하고, 발화를 LUIS 엔드포인트에 제출하고, 결과 점수를 받을 수 있습니다.

## <a name="languages-supported"></a>지원되는 언어

LUIS는 발화를 다음 언어로 이해합니다.

| 언어 |Locale  |  미리 빌드된 도메인 | 미리 빌드된 엔터티 | 구 목록 권장 사항 | **[텍스트 분석](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(감정 및<br>키워드)|
|--|--|:--:|:--:|:--:|:--:|
| 미국 영어 |`en-US` | ✔ | ✔  |✔|✔|
| 아랍어 (미리보기 - 현대 표준 아랍어) |`ar-AR`|-|-|-|-|
| *[중국어](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| 네덜란드어 |`nl-NL` |✔|-|-|✔|
| 프랑스어(프랑스) |`fr-FR` |✔| ✔ |✔ |✔|
| 프랑스어(캐나다) |`fr-CA` |-|-|-|✔|
| 독일어 |`de-DE` |✔| ✔ |✔ |✔|
| 구자라트어 | `gu-IN`|-|-|-|-|
| 힌디어 | `hi-IN`|-|✔|-|-|
| 이탈리아어 |`it-IT` |✔| ✔ |✔|✔|
| *[일본어](#japanese-support-notes) |`ja-JP` |✔| ✔ |✔|주요 구문만|
| 한국어 |`ko-KR` |✔|-|-|주요 구문만|
| 마라티어 | `mr-IN`|-|-|-|-|
| 포르투갈어(브라질) |`pt-BR` |✔| ✔ |✔ |일부 하위 문화권은 아님|
| 스페인어(스페인) |`es-ES` |✔| ✔ |✔|✔|
| 스페인어(멕시코)|`es-MX` |-|-|✔|✔|
| 타밀어 | `ta-IN`|-|-|-|-|
| 텔루구어 | `te-IN`|-|-|-|-|
| 터키어 | `tr-TR` |✔|✔|-|감정만|




언어 지원은 [미리 빌드된 엔터티](luis-reference-prebuilt-entities.md) 및 [미리 빌드된 도메인](luis-reference-prebuilt-domains.md)에 따라 다릅니다.

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

### <a name="japanese-support-notes"></a>*일본어 지원 참고

 - LUIS는 구문 분석을 제공하지 않으며 Keigo와 비공식 일본어 간의 차이를 이해하지 못하므로 여러 수준의 형식을 애플리케이션의 학습 예제로 통합해야 합니다.
     - でございます는 です와 같지 않습니다.
     - です는 だ와 같지 않습니다.

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]

### <a name="speech-api-supported-languages"></a>Speech API 지원되는 언어
Speech 받아쓰기 모드 언어에 대해서는 Speech [지원되는 언어](../speech-service/speech-to-text.md)를 참조하세요.

### <a name="bing-spell-check-supported-languages"></a>Bing Spell Check 지원되는 언어
지원되는 언어 및 상태 목록은 Bing Spell Check [지원되는 언어](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages)를 참조하세요.

## <a name="rare-or-foreign-words-in-an-application"></a>애플리케이션의 희귀 또는 외래 단어
`en-us` 문화권에서 LUIS는 은어를 포함하여 대부분의 영어 단어를 구분하도록 학습됩니다. `zh-cn` 문화권에서 LUIS는 대부분의 중국어 문자를 구분하도록 학습됩니다. `en-us`의 희귀 단어 또는 `zh-cn`의 문자를 사용할 때 LUIS가 해당 단어나 문자를 구분할 수 없는 것으로 보이면 해당 단어나 문자를 [구문 목록 기능](luis-how-to-add-features.md)에 추가할 수 있습니다. 예를 들어, 애플리케이션 문화권 외부의 단어(외래 단어)는 구문 목록 기능에 추가해야 합니다.

<!--This phrase list should be marked non-interchangeable, to indicate that the set of rare words forms a class that LUIS should learn to recognize, but they are not synonyms or interchangeable with each other.-->

### <a name="hybrid-languages"></a>하이브리드 언어
하이브리드 언어는 영어 및 중국어와 같은 두 문화권의 단어를 결합합니다. 앱은 단일 문화권을 기반으로 하므로 이러한 언어는 LUIS에서 지원되지 않습니다.

## <a name="tokenization"></a>토큰화
기계 학습을 수행하기 위해 LUIS는 문화권에 따라 발화를 [토큰](luis-glossary.md#token)으로 구분합니다.

|언어|  모든 공백 또는 특수 문자 | 문자 수준|복합 단어
|--|:--:|:--:|:--:|
|아랍어|✔|||
|중국어||✔||
|네덜란드어|✔||✔|
|미국 영어(en-us)|✔ |||
|프랑스어(fr-FR)|✔|||
|프랑스어(fr-CA)|✔|||
|독일어|✔||✔|
|구자라트어|✔|||
|힌디어|✔|||
|이탈리아어|✔|||
|일본어|||✔
|한국어||✔||
|마라티어|✔|||
|포르투갈어(브라질)|✔|||
|스페인어(es-ES)|✔|||
|스페인어(es-MX)|✔|||
|타밀어|✔|||
|텔루구어|✔|||
|터키어|✔|||


### <a name="custom-tokenizer-versions"></a>사용자 지정 토큰화 버전

다음 문화식에는 사용자 지정 토큰화 자 버전이 있습니다.

|culture|버전|목적|
|--|--|--|
|독일어<br>`de-de`|1.0.0|복합 단어를 단일 구성 요소로 분할하려고 하는 기계 학습 기반 토큰화기를 사용하여 단어를 분할하여 단어를 토큰화합니다.<br>사용자가 `Ich fahre einen krankenwagen` 발언으로 입력하면 `Ich fahre einen kranken wagen`로 전환됩니다. 서로 다른 `kranken` 엔터티로 독립적으로 표시할 `wagen` 수 있도록 합니다.|
|독일어<br>`de-de`|1.0.2|공백에 단어를 분할하여 단어를 토큰화합니다.<br> 사용자가 `Ich fahre einen krankenwagen` 발언으로 입력하면 단일 토큰으로 유지됩니다. 따라서 `krankenwagen` 단일 엔터티로 표시됩니다. |
|네덜란드어<br>`de-de`|1.0.0|복합 단어를 단일 구성 요소로 분할하려고 하는 기계 학습 기반 토큰화기를 사용하여 단어를 분할하여 단어를 토큰화합니다.<br>사용자가 `Ik ga naar de kleuterschool` 발언으로 입력하면 `Ik ga naar de kleuter school`로 전환됩니다. 서로 다른 `kleuter` 엔터티로 독립적으로 표시할 `school` 수 있도록 합니다.|
|네덜란드어<br>`de-de`|1.0.1|공백에 단어를 분할하여 단어를 토큰화합니다.<br> 사용자가 `Ik ga naar de kleuterschool` 발언으로 입력하면 단일 토큰으로 유지됩니다. 따라서 `kleuterschool` 단일 엔터티로 표시됩니다. |


### <a name="migrating-between-tokenizer-versions"></a>토큰화 자 버전 간에 마이그레이션
<!--
Your first choice is to change the tokenizer version in the app file, then import the version. This action changes how the utterances are tokenized but allows you to keep the same app ID.

Tokenizer JSON for 1.0.0. Notice the property value for  `tokenizerVersion`.

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.0",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.0",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 23,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```

Tokenizer JSON for version 1.0.1. Notice the property value for  `tokenizerVersion`.

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.1",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.1",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 16,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```
-->

토큰화는 앱 수준에서 수행됩니다. 버전 수준 토큰화는 지원되지 않습니다.

버전을 [대신 파일을 새 앱으로 가져옵니다.](luis-how-to-start-new-app.md) 이 작업은 새 앱에 다른 앱 ID가 있지만 파일에 지정된 토큰화 자 버전을 사용한다는 것을 의미합니다.
