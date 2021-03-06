---
title: 미리 빌드된 도메인 참조 - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS(Language Understanding Intelligent Services)에서 미리 작성된 의도 및 엔터티 컬렉션에 해당하는 미리 작성된 도메인에 대한 참조입니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 05ad340b3856291832ba0521c7da70ad55260384
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270606"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>LUIS 앱에 대한 미리 작성된 도메인 참조
이 참조는 LUIS에서 제공하는 의도 및 엔터티의 미리 작성된 컬렉션에 해당하는 [미리 작성된 도메인](luis-how-to-use-prebuilt-domains.md)에 대한 정보를 제공합니다.

반대로, [사용자 지정 도메인](luis-how-to-start-new-app.md)은 의도 및 모델 없이 시작합니다. 사용자 지정 모델에 미리 작성된 도메인 의도 및 엔터티를 추가할 수 있습니다.

## <a name="custom-domains-per-language"></a>언어별 맞춤 도메인

아래 표에는 현재 지원되는 도메인이 요약된 것입니다. 영어 에 대한 지원은 일반적으로 다른 사람보다 더 완벽합니다.

| 엔터티 형식       | KO-KR      | ZH-CN   | DE    | FR     | ES    | IT      | PT-BR |  JP  |      한국어 |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:|
| 일정  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
|통신  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Email     | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| HomeAutomation          | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| 메모     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| 장소   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| RestaurantReservation  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Todo     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| 공공 시설      | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Weather        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| 웹    | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |

미리 빌드된 도메인은 다음 에서 **지원되지 않습니다.**

* 프랑스어 캐나다인
* 힌디어
* 스페인어 멕시코

## <a name="next-steps"></a>다음 단계

간단한 [엔터티를](reference-entity-simple.md)알아봅니다.