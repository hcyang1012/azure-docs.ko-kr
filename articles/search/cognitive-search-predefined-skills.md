---
title: 인덱싱 중에 내장된 텍스트 및 이미지 처리
titleSuffix: Azure Cognitive Search
description: 데이터 추출, 자연어, 이미지 처리 인지 능력은 Azure Cognitive Search 파이프라인의 원시 콘텐츠에 의미체계와 구조를 추가합니다.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b1cf1750924ab8ea8afff6ac788683565433866b
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81618016"
---
# <a name="built-in-cognitive-skills-for-text-and-image-processing-during-indexing-azure-cognitive-search"></a>인덱싱 중 텍스트 및 이미지 처리를 위한 기본 제공 인지 기술(Azure Cognitive Search)

이 문서에서는 콘텐츠 및 구조를 추출하는 기술 집합에 포함할 수 있는 Azure Cognitive Search와 함께 제공되는 인지 기술에 대해 알아봅니다. *인지 기술은* 어떤 식으로든 콘텐츠를 변환하는 모듈 또는 작업입니다. 대체로, 데이터를 추출하거나 구조를 추론하여 입력 데이터에 대한 이해를 보강하는 구성 요소입니다. 출력은 거의 항상 텍스트 기반입니다. 기능** 은 보강 파이프라인을 정의하는 기술 컬렉션입니다. 

> [!NOTE]
> 처리 빈도를 늘리거나, 문서를 더 추가하거나, AI 알고리즘을 추가하여 범위를 확장하려면 [청구 가능한 인지 서비스 리소스를 연결해야](cognitive-search-attach-cognitive-services.md)합니다. Cognitive Services에서 API를 호출하는 경우와 Azure Cognitiv Search에서 문서 크래킹 단계의 일부로 이미지를 추출하는 경우에는 요금이 부과됩니다. 문서에서 텍스트 추출할 때는 요금이 발생하지 않습니다.
>
> 기본 제공 기술을 실행하는 요금은 기존 [Cognitive Services 종량제 가격](https://azure.microsoft.com/pricing/details/cognitive-services/)으로 청구됩니다. 이미지 추출 가격 책정은 [Azure Cognitiv Search 가격 책정 페이지](https://go.microsoft.com/fwlink/?linkid=2042400)에 설명되어 있습니다.
>
> [증분 보강(미리 보기)](cognitive-search-incremental-indexing-conceptual.md) 기능을 사용하면 인덱서가 나중에 기술 집합을 수정하는 데 필요한 인지 기술만 실행할 때 보다 효율적으로 실행할 수 있는 캐시를 제공하여 시간과 비용을 절약할 수 있습니다.


## <a name="built-in-skills"></a>기본 제공 기술

여러 기술은 사용하거나 생성하는 대상에 유연성이 있습니다. 일반적으로 대부분의 기술은 미리 학습된 모델을 기반으로 하므로 고유한 학습 데이터를 사용하여 모델을 학습할 수 없습니다. 다음 표에서는 Microsoft에서 제공하는 기술을 열거하고 설명합니다. 

| 기술 | Description |
|-------|-------------|
|[마이크로소프트.스킬.텍스트.커스텀엔티룩업스킬](cognitive-search-skill-custom-entity-lookup.md)| 사용자 정의 단어 및 구 목록에서 텍스트를 찾습니다.|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | 이 기술은 미리 학습된 모델을 통해 용어 배치, 언어 규칙, 다른 용어에 대한 근접성 및 원본 데이터 내에서 용어가 비정상적인 정도에 따라 중요한 문구를 검색합니다. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | 이 기술은 미리 학습된 모델을 통해 사용된 언어를 검색합니다(문서당 하나의 언어 ID). 동일한 텍스트 세그먼트 내에서 여러 언어가 사용되는 경우 주로 사용된 언어의 LCID가 출력됩니다.|
| [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md) | 필드 컬렉션에서 단일 필드로 텍스트를 통합합니다.  |
| [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | 이 기술은 미리 학습된 모델을 통해 고정된 범주 세트(사람, 위치, 조직, 이메일, URL, 날짜/시간 필드)에 대한 엔터티를 설정합니다. |
| [Microsoft.Skills.Text.PIIDetectionSkill](cognitive-search-skill-pii-detection.md)  | 이 기술은 미리 학습된 모델을 사용하여 지정된 텍스트에서 개인 식별 정보를 추출합니다. 또한 이 기술은 텍스트에서 검색된 PII 엔터티를 마스킹하기 위한 다양한 옵션을 제공합니다.  |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | 이 기술은 미리 학습된 모델을 통해 레코드 단위로 긍정적이거나 부정적인 감정의 점수를 매깁니다. 점수는 0에서 1 사이입니다. 감정을 검색할 수 없는 null인 경우와 중립으로 간주되는 텍스트의 경우 둘 다 중립 점수가 지정됩니다.  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | 콘텐츠를 증분 방식으로 보강하거나 보충할 수 있도록 텍스트를 페이지로 분할합니다. |
| [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md) | 이 기술은 미리 학습된 모델을 사용하여 입력 텍스트를 정규화 또는 지역화 사용 사례에 대한 다양한 언어로 변환합니다. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | 이 기술은 이미지 검색 알고리즘을 사용하여 이미지의 콘텐츠를 식별하고 텍스트 설명을 생성합니다. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | 광학 문자 인식 |
| [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md) | 필터링, 기본값 할당 및 조건에 따라 데이터를 병합할 수 있습니다.|
| [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) | 보강 파이프라인 내의 파일에서 콘텐츠를 추출합니다. |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | 출력을 복합 형식(전체 이름, 여러 줄로 이루어진 주소 또는 성과 개인 식별자의 조합에 사용될 수 있는 다중 파트 데이터 형식)에 매핑합니다. |
| [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md) | 사용자 지정 웹 API에 HTTP 호출을 통해 AI 보강 파이프라인의 확장성을 허용합니다. |


[사용자 지정 기술](cognitive-search-custom-skill-web-api.md)만들기에 대한 지침은 사용자 지정 [인터페이스를 정의하는 방법](cognitive-search-custom-skill-interface.md) 및 [예제: AI 보강을 위한 사용자 지정 기술 만들기를](cognitive-search-create-custom-skill-example.md)참조하십시오.

## <a name="see-also"></a>참고 항목

+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [사용자 지정 기술 인터페이스 정의](cognitive-search-custom-skill-interface.md)
+ [자습서: AI를 갖춘 풍부한 인덱싱](cognitive-search-tutorial-blob.md)
