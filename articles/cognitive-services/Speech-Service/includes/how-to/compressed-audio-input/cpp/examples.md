---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: b565bf3bae61cf92133b957bb75fdae9545c030e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421704"
---
압축된 오디오 형식으로 음성 서비스로 스트리밍하려면 `PullAudioInputStream` `PushAudioInputStream`를 만들거나 를 만듭니다. 그런 다음 `AudioConfig` 스트림 클래스의 인스턴스에서 스트림의 압축 형식을 지정하여 만듭니다.

입력 스트림 클래스가 라는 `pushStream` 가정하 고 OPUS/OGG를 사용 하는 가정 해 봅시다. 코드는 다음과 같을 수 있습니다.

```cpp
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;

// ... omitted for brevity

 auto config =
    SpeechConfig::FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion"
    );

auto audioFormat =
    AudioStreamFormat::GetCompressedFormat(
        AudioStreamContainerFormat::OGG_OPUS
    );
auto audioConfig =
    AudioConfig::FromStreamInput(
        pushStream,
        audioFormat
    );

auto recognizer = SpeechRecognizer::FromConfig(config, audioConfig);
auto result = recognizer->RecognizeOnceAsync().get();

auto text = result->Text;
```