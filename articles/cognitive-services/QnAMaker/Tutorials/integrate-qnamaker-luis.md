---
title: LUIS 및 QnAMaker - 봇 통합
titleSuffix: Azure Cognitive Services
description: QnA Maker 기술 자료가 커짐에 따라 단일 모놀리식 집합으로 유지하기가 어려워집니다. 기술 기반을 더 작고 논리적인 청크로 분할합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 09/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b0d28c77966668f919cdf1265f8cc63b4931d5fd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402711"
---
# <a name="use-a-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>QnA Maker 및 LUIS와 함께 봇을 사용하여 기술 자료 배포
QnA Maker 기술 자료가 커짐에 따라 단일 모놀리식 집합으로 유지하기가 어려워집니다. 기술 기반을 더 작고 논리적인 청크로 분할합니다.

QnA Maker에서 여러 기술 기반을 만드는 것은 간단하지만 들어오는 질문을 적절한 기술 자료로 라우팅하려면 몇 가지 논리가 필요합니다. LUIS를 사용하여 이 작업을 수행할 수 있습니다.

이 문서에서는 봇 프레임워크 v3 SDK를 사용합니다. 이 정보의 봇 프레임워크 v4 SDK 버전에 관심이 있는 경우 [여러 LUIS 및 QnA 모델 사용을](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp)참조하십시오.

## <a name="architecture"></a>Architecture

![언어 이해와 QnA 메이커의 아키텍처를 보여주는 그래픽](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

앞의 그래픽에서는 QnA Maker가 먼저 LUIS 모델에서 들어오는 질문의 의도를 얻는 것을 보여 주며, 이에 대한 의도를 보여줍니다. 그런 다음 QnA Maker는 해당 의도를 사용하여 질문을 올바른 QnA Maker 기술 자료로 라우팅합니다.

## <a name="create-a-luis-app"></a>LUIS 앱 만들기

1. [LUIS](https://www.luis.ai/) 포털에 로그인합니다.
1. [앱을 만듭니다.](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app)
1. 각 QnA Maker 기술 자료에 대해 [의도를 추가](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents)합니다. 예제 발언은 QnA Maker 기술 자료의 질문과 일치해야 합니다.
1. [LUIS 앱을 학습하고](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) [LUIS 앱을 게시합니다.](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp)
1. **관리** 섹션에서 LUIS 앱 ID, LUIS 끝점 키 및 [사용자 지정 도메인 이름을](../../cognitive-services-custom-subdomains.md)기록합니다. 나중에 이러한 값이 필요합니다.

## <a name="create-qna-maker-knowledge-bases"></a>QnA Maker 기술 자료 만들기

1. [QnA 메이커에](https://qnamaker.ai)로그인합니다.
1. LUIS 앱의 각 의도에 대한 기술 자료를 [만듭니다.](https://www.qnamaker.ai/Create)
1. 기술 자료를 테스트하고 게시합니다. 각 ID를 게시할 때 ID, 리소스 _이름(.azurewebsites.net/qnamaker_이전의 사용자 지정 하위 도메인) 및 권한 부여 끝점 키를 기록합니다. 나중에 이러한 값이 필요합니다.

    이 문서에서는 기술 자료가 모두 동일한 Azure QnA Maker 구독에서 생성된다고 가정합니다.

    ![QnA 메이커 HTTP 요청의 스크린 샷](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>웹앱 봇

1. LUIS 앱을 자동으로 포함하는 [Azure 봇 서비스를 사용하여 "기본" 봇을 만듭니다.](https://docs.microsoft.com/azure/bot-service/bot-service-quickstart?view=azure-bot-service-4.0) C# 프로그래밍 언어를 선택합니다.

1. Azure 포털에서 웹 앱 봇을 만든 후 웹 앱 봇을 선택합니다.
1. 웹 앱 봇 서비스 탐색에서 **응용 프로그램 설정을**선택합니다. 그런 다음 사용 가능한 **설정의 응용 프로그램 설정** 섹션으로 스크롤합니다.
1. **LuisAppId를** 이전 섹션에서 만든 LUIS 앱 값으로 변경합니다. 그런 다음 **저장을**선택합니다.


## <a name="change-the-code-in-the-basicluisdialogcs-file"></a>BasicLuisDialog.cs 파일의 코드 변경
1. Azure Portal에서 웹앱 봇 탐색의 **봇 관리** 섹션에서 **빌드**를 선택합니다.
2. **온라인 코드 편집기 열기**를 선택합니다. 새 브라우저 탭에서 온라인 편집 환경이 열립니다.
3. **WWWROOT** 섹션에서 **대화 상자** 디렉토리를 선택한 다음 **BasicLuisDialog.cs**엽니다.
4. **BasicLuisDialog.cs** 파일 맨 위에 종속성을 추가합니다.

    ```csharp
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Builder.Luis;
    using Microsoft.Bot.Builder.Luis.Models;
    using Newtonsoft.Json;
    using System.Text;
    ```

5. 다음 클래스를 추가하여 QnA Maker 응답을 역직렬화합니다.

    ```csharp
    public class Metadata
    {
        public string name { get; set; }
        public string value { get; set; }
    }

    public class Answer
    {
        public IList<string> questions { get; set; }
        public string answer { get; set; }
        public double score { get; set; }
        public int id { get; set; }
        public string source { get; set; }
        public IList<object> keywords { get; set; }
        public IList<Metadata> metadata { get; set; }
    }

    public class QnAAnswer
    {
        public IList<Answer> answers { get; set; }
    }
    ```


6. 다음 클래스를 추가하여 QnA Maker 서비스에 대한 HTTP 요청을 수행합니다. **권한 부여** 헤더의 값에는 단어 `EndpointKey`다음에 공백이 있는 단어가 포함됩니다. JSON 결과는 이전 클래스로 직렬화되고 첫 번째 대답이 반환됩니다.

    ```csharp
    [Serializable]
    public class QnAMakerService
    {
        private string qnaServiceResourceName;
        private string knowledgeBaseId;
        private string endpointKey;

        public QnAMakerService(string resourceName, string kbId, string endpointkey)
        {
            qnaServiceResourceName = resourceName;
            knowledgeBaseId = kbId;
            endpointKey = endpointkey;

        }
        async Task<string> Post(string uri, string body)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(body, Encoding.UTF8, "application/json");
                request.Headers.Add("Authorization", "EndpointKey " + endpointKey);

                var response = await client.SendAsync(request);
                return  await response.Content.ReadAsStringAsync();
            }
        }
        public async Task<string> GetAnswer(string question)
        {
            string uri = qnaServiceResourceName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer";
            string questionJSON = "{\"question\": \"" + question.Replace("\"","'") +  "\"}";

            var response = await Post(uri, questionJSON);

            var answers = JsonConvert.DeserializeObject<QnAAnswer>(response);
            if (answers.answers.Count > 0)
            {
                return answers.answers[0].answer;
            }
            else
            {
                return "No good match found.";
            }
        }
    }
    ```


7. 클래스를 `BasicLuisDialog` 수정합니다. 각 LUIS 의도에는 **LuisIntent**로 데코레이트된 메서드가 있습니다. 데코레이션에 대한 매개 변수는 실제 LUIS 의도 이름입니다. 데코레이팅된 메서드 이름은 가독성 및 유지 관리성을 위해 LUIS 의도 _이름이어야_ 하지만 디자인이나 런타임에서 동일할 필요는 없습니다.

    ```csharp
    [Serializable]
    public class BasicLuisDialog : LuisDialog<object>
    {
        // LUIS Settings
        static string LUIS_appId = "<LUIS APP ID>";
        static string LUIS_apiKey = "<LUIS API KEY>";
        static string LUIS_hostRegion = "westus.api.cognitive.microsoft.com";

        // QnA Maker global settings
        // assumes all knowledge bases are created with same Azure service
        static string qnamaker_endpointKey = "<QnA Maker endpoint KEY>";
        static string qnamaker_resourceName = "my-qnamaker-s0-s";

        // QnA Maker Human Resources Knowledge base
        static string HR_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // QnA Maker Finance Knowledge base
        static string Finance_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // Instantiate the knowledge bases
        public QnAMakerService hrQnAService = new QnAMakerService("https://" + qnamaker_resourceName + ".azurewebsites.net", HR_kbID, qnamaker_endpointKey);
        public QnAMakerService financeQnAService = new QnAMakerService("https://" + qnamaker_resourceName + ".azurewebsites.net", Finance_kbID, qnamaker_endpointKey);

        public BasicLuisDialog() : base(new LuisService(new LuisModelAttribute(
            LUIS_appId,
            LUIS_apiKey,
            domain: LUIS_hostRegion)))
        {
        }

        [LuisIntent("None")]
        public async Task NoneIntent(IDialogContext context, LuisResult result)
        {
            HttpClient client = new HttpClient();
            await this.ShowLuisResult(context, result);
        }

        // HR Intent
        [LuisIntent("HR")]
        public async Task HumanResourcesIntent(IDialogContext context, LuisResult result)
        {
            // Ask the HR knowledge base
            var qnaMakerAnswer = await hrQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }

        // Finance intent
        [LuisIntent("Finance")]
        public async Task FinanceIntent(IDialogContext context, LuisResult result)
        {
            // Ask the finance knowledge base
            var qnaMakerAnswer = await financeQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }
        private async Task ShowLuisResult(IDialogContext context, LuisResult result)
        {
            await context.PostAsync($"You have reached {result.Intents[0].Intent}. You said: {result.Query}");
            context.Wait(MessageReceived);
        }
    }
    ```


## <a name="build-the-bot"></a>봇 빌드
1. 코드 편집기에서 **build.cmd를**마우스 오른쪽 단추로 클릭하고 **콘솔에서 실행을**선택합니다.

    ![코드 편집기에서 콘솔에서 실행 옵션의 스크린샷](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. 코드 뷰는 빌드의 진행 률과 결과를 보여 주는 터미널 창으로 대체됩니다.

    ![콘솔 빌드의 스크린샷](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>봇 테스트
Azure Portal에서 **웹 채팅에서 테스트**를 선택하여 봇을 테스트합니다. 다양한 의도의 메시지를 입력하여 해당하는 기술 자료의 응답을 가져옵니다.

![웹 채팅 테스트의 스크린 샷](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료와 파워 가상 에이전트의 에이전트 통합](integrate-with-power-virtual-assistant-fallback-topic.md)
