---
title: 좋은 예 발언 - LUIS
description: 발언은 앱이 해석해야 하는 사용자의 입력입니다. 사용자가 입력할 것으로 생각되는 구를 수집합니다. 같은 내용을 의미하지만, 단어 길이 및 단어 배치가 다르게 구성된 발언을 포함합니다.
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: d851082a4ec4a003619826eeffd4f4b856a67824
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382280"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>LUIS 앱에 적합한 발언이 무엇인지 이해

**발언**은 앱이 해석해야 하는 사용자의 입력입니다. 여기에서 의도와 엔터티를 추출하도록 LUIS를 학습시키려면 각 의도에 대한 다양한 예제 발언을 캡처하는 것이 중요합니다. 활성 학습 또는 새 발언에 대한 지속적인 학습 프로세스는 LUIS에서 제공하는 기계 학습 인텔리전스에 반드시 필요합니다.

사용자가 입력할만한 발언을 수집합니다. 같은 의미를 갖지만 다르게 구성된 다양한 발언을 포함시킵니다.

* 발언 길이 - 클라이언트 애플리케이션에 대해 짧게, 중간으로, 길게
* 단어 길이 및 문구 길이
* 단어 배치 - 발언의 시작, 중간 및 끝에 있는 엔터티
* 문법
* 복수형
* 형태소 분석
* 명사 및 동사 선택
* [구두점](luis-reference-application-settings.md#punctuation-normalization) - 올바른, 잘못된, 그리고 문법을 사용하여 좋은 다양성

## <a name="how-to-choose-varied-utterances"></a>다양한 발언을 선택하는 방법

LUIS 모델에 [예제 발언을 추가](luis-how-to-add-example-utterances.md)하여 처음 시작할 때 다음 원칙에 유의하세요.

### <a name="utterances-arent-always-well-formed"></a>발언이 항상 올바른 형식인 것은 아닙니다.

"내 파리행 티켓 예약"과 같은 문장이나 "예약" 또는 “파리행 항공권”과 같은 문장의 일부분이 될 수도 있습니다.  사용자는 종종 맞춤법 오류를 범합니다. 앱을 계획할 때는 사용자 입력을 LUIS에 전달하기 전에 수정하기 위해 [Bing Spell Check](luis-tutorial-bing-spellcheck.md)를 사용할지 여부를 고려합니다.

사용자 발언의 맞춤법을 검사하지 않으면 입력 오류 및 철자 오류가 포함된 발언으로 LUIS를 학습하게 됩니다.

### <a name="use-the-representative-language-of-the-user"></a>사용자의 대표 언어 사용

발언을 선택할 때는 내가 일반적인 용어나 문구라고 생각하는 것이 클라이언트 애플리케이션의 일반적인 사용자에게는 올바르지 않을 수도 있다는 것을 인지해야 합니다. 도메인 환경이 없는 경우도 있습니다. 사용자가 전문가인 경우에만 말할 수 있는 용어나 문구를 사용할 때는 주의하십시오.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>관용구 뿐만 아니라 변형 용어 선택

아무리 다양한 문장 패턴을 만들기 위해 애쓰더라도 일부 어휘가 여전히 반복될 수 있습니다.

다음과 같은 예제 발언을 사용하세요.

|예제 발화|
|--|
|how do I get a computer?|
|Where do I get a computer?|
|I want to get a computer, how do I go about it?|
|When can I have a computer?|

여기서 핵심 용어인 "컴퓨터"는 다양하지 않습니다. 데스크톱 컴퓨터, 랩톱, 워크스테이션 또는 간단히 머신과 같은 대체 용어를 사용합니다. LUIS는 컨텍스트에서 동의어를 지능적으로 추론할 수 있지만 교육을 위해 발언을 만들 때 항상 이를 변경하는 것이 좋습니다.

## <a name="example-utterances-in-each-intent"></a>각 의도의 예제 발언

각 의도에는 15개 이상의 예제 발언이 필요합니다. 예제 발언이 없는 의도로는 LUIS를 학습시킬 수 없습니다. 예제 발언이 하나이거나 거의 없는 의도가 있는 경우 LUIS가 의도를 정확하게 예측하지 못할 수 있습니다.

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>각 작성 반복에 대해 15개의 소규모 발언 그룹 추가

모델의 각 반복에서 많은 양의 발언을 추가하지 않도록 합니다. 15개 정도의 발언을 추가합니다. [학습](luis-how-to-train.md), [게시](luis-how-to-publish-app.md) 및 [테스트](luis-interactive-test.md)를 다시 진행합니다.

LUIS는 LUIS 모델 작성자가 신중하게 선택한 발언으로 효과적인 모델을 빌드합니다. 너무 많은 발언을 추가하면 혼동을 가져오므로 유용하지 않습니다.

처음에는 소수의 발언으로 시작한 후 정확한 의도 예측 및 엔터티 추출을 위해 [엔드포인트 발언을 검토](luis-how-to-review-endpoint-utterances.md)합니다.

## <a name="utterance-normalization"></a>발화 정규화

발화 정규화는 교육 및 예측 중에 구두점 및 분음 부호의 영향을 무시하는 프로세스입니다. [응용 프로그램 설정을](luis-reference-application-settings.md) 사용하여 발사 정규화가 발화 예측에 미치는 영향을 제어합니다.

## <a name="utterance-normalization-for-diacritics-and-punctuation"></a>분음 부호 및 문장 부호에 대한 발화 정규화

발화 정규화는 응용 프로그램 JSON 파일의 설정이기 때문에 앱을 만들거나 가져올 때 정의됩니다. 발화 정규화 설정은 기본적으로 꺼져 있습니다.

분음 부호는 다음과 같이 텍스트 내의 표시 또는 기호입니다.

```
İ ı Ş Ğ ş ğ ö ü
```

앱이 정규화를 켜면 **분음** 부호 또는 문장 부호를 사용하여 모든 발언에 대해 테스트 창, 일괄 처리 테스트 및 끝점 쿼리의 점수가 변경됩니다.

`settings` 매개 변수에서 LUIS JSON 앱 파일에 분음 부호 또는 문장 부호에 대한 발화 정규화를 켭니다.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"},
    {"name": "NormalizeDiacritics", "value": "true"}
]
```

문장 **부호를** 정규화한다는 것은 모델이 학습되기 전에 끝점 쿼리가 예측되기 전에 발포점에서 문장 부호가 제거된다는 것을 의미합니다.

분음 **부호를** 정규화하는 것은 발언에서 분음 부호로 문자를 일반 문자로 바꿉니다. 예를 `Je parle français` 들어. `Je parle francais`

정규화는 예제 발언 이나 예측 응답에서 구두점 및 분음 부호를 볼 수 없습니다 의미 하지는 않습니다., 단지 그들은 훈련 및 예측 하는 동안 무시 됩니다.

### <a name="punctuation-marks"></a>문장 부호

문장 부호는 LUIS에서 별도 토큰입니다. 끝에 마침표와 끝에 마침표가 포함되지 않은 발언이 포함된 발언은 두 개의 별도 발언이며 두 개의 서로 다른 예측을 얻을 수 있습니다.

구두점이 정규화되지 않은 경우 일부 클라이언트 응용 프로그램은 이러한 마크에 중요한 의미를 지정할 수 있으므로 LUIS는 기본적으로 구두점 표시를 무시하지 않습니다. 문장 부호를 사용하는 예제 발언과 문장 부호를 사용하지 않은 예제 발언이 모두 동일한 관련 점수를 반환하는지 확인해야 합니다.

모델이 예제 발언(문장 부호를 포함하거나 포함하지 않음) 또는 [패턴](luis-concept-patterns.md)(`I am applying for the {Job} position[.]` 특수 구문을 사용하여 문장 부호를 무시하는 것이 더 쉬움)에서 문장 부호를 처리하는지 확인합니다.

구두점클라이언트 응용 프로그램에 특별한 의미가 없는 경우 문장 부호를 정규화하여 [문장 부호를 무시하는](#utterance-normalization) 것이 좋습니다.

### <a name="ignoring-words-and-punctuation"></a>단어 및 문장 부호 무시

패턴에서 특정 단어 나 문장 부호를 무시하려면 대괄호를 _무시구가있는_ `[]` [패턴을](luis-concept-patterns.md#pattern-syntax) 사용합니다.

## <a name="training-utterances"></a>발언 학습

학습은 일반적으로 비결정적입니다. 발언 예측은 버전이나 앱마다 약간 다를 수 있습니다.
모든 교육 데이터를 사용하기 위해 `UseAllTrainingData` 이름/값 쌍으로 [버전 설정](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) API를 업데이트하여 비결정적 학습을 제거할 수 있습니다.

## <a name="testing-utterances"></a>발언 테스트

개발자는 발언을 [예측 엔드포인트](luis-how-to-azure-subscription.md) URL로 전송하여 실제 트래픽으로 LUIS 애플리케이션 테스트를 시작해야 합니다. 이러한 발언은 [발언 검토](luis-how-to-review-endpoint-utterances.md)로 의도 및 엔터티의 성능을 개선하는 데 사용됩니다. LUIS 웹 사이트 테스트 창을 통해 제출된 테스트는 엔드포인트로 전송되지 않으므로 활성 학습에 기여하지 않습니다.

## <a name="review-utterances"></a>발화 검토

모델이 학습되고, 게시되고 [엔드포인트](luis-glossary.md#endpoint) 쿼리를 수신하면 LUIS에서 제안한 [발언을 검토](luis-how-to-review-endpoint-utterances.md)합니다. LUIS는 의도 또는 엔터티에 대해 낮은 점수를 갖는 엔드포인트 발언을 선택합니다.

## <a name="best-practices"></a>모범 사례

[모범 사례](luis-concept-best-practices.md)를 검토하고 일반 제작 주기의 일환으로 적용합니다.

## <a name="label-for-word-meaning"></a>단어 의미에 대한 레이블

단어 선택이나 단어 배열은 동일하지만 같은 의미를 나타내지 않는 경우 엔터티로 레이블을 지정하지 마세요.

다음 발화에서 단어 `fair`는 동형이의어입니다. 즉, 철자는 동일하지만 의미가 다릅니다.

|발화|
|--|
|What kind of county fairs are happening in the Seattle area this summer?|
|Is the current rating for the Seattle review fair?|

이벤트 엔터티에서 모든 이벤트 데이터를 찾도록 하려면 두 번째 발화가 아닌 첫 번째 발화에서 `fair` 단어에 레이블을 지정하세요.


## <a name="next-steps"></a>다음 단계
사용자 발언을 이해하도록 LUIS 앱을 학습하는 방법에 대한 내용은 [예제 발언 추가](luis-how-to-add-example-utterances.md)를 참조하세요.

