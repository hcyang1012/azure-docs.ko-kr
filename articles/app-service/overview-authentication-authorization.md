---
title: 인증 및 권한 부여
description: Azure App Service 및 Azure Functions의 기본 제공 인증 및 권한 부여 지원에 대해 알아보고 무단 액세스에 대해 앱을 보호하는 방법을 알아보십시오.
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.topic: article
ms.date: 04/15/2020
ms.reviewer: mahender
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: a4ceed0d897f069a7895a3eb6b10c327566afbe5
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457861"
---
# <a name="authentication-and-authorization-in-azure-app-service-and-azure-functions"></a>Azure 앱 서비스 및 Azure 함수의 인증 및 권한 부여

> [!NOTE]
> 현재 ASP.NET Core는 현재 인증/권한 부여 기능을 통해 현재 사용자를 채우는 것을 지원하지 않습니다.
>

Azure App Service는 내장된 인증 및 권한 부여 지원을 제공하므로 웹앱, RESTful API 및 모바일 백 엔드에 코드를 최소한으로 작성하거나 코드를 작성하지 않고 사용자를 로그인시켜 데이터에 액세스할 수 있으며 [Azure Functions](../azure-functions/functions-overview.md)도 사용할 수 있습니다. 이 문서는 App Service가 앱의 인증 및 권한 부여를 단순화하는 방법에 대해 설명합니다.

안전한 인증 및 권한 부여에는 페더레이션, 암호화, [JSON 웹 토큰(JWT)](https://wikipedia.org/wiki/JSON_Web_Token) 관리, [부여 유형](https://oauth.net/2/grant-types/) 등 보안에 대한 깊은 이해가 필요합니다. App Service가 이러한 유틸리티를 제공하기 때문에, 고객에게 비즈니스 가치를 제공하는 데 더 많은 시간과 에너지를 투자할 수 있습니다.

> [!IMPORTANT]
> 인증 및 권한 부여를 위해 이 기능을 사용할 필요는 없습니다. 선택한 웹 프레임워크에서 번들 보안 기능을 사용하거나 직접 유틸리티를 작성할 수 있습니다. 그러나 Chrome 80은 쿠키용 SameSite(2020년 3월 경 출시일)의 [구현을 주요 변경 중이며,](https://www.chromestatus.com/feature/5088147346030592) 클라이언트 Chrome 브라우저가 업데이트될 때 교차 사이트 쿠키 게시에 의존하는 사용자 지정 원격 인증 또는 기타 시나리오가 중단될 수 있습니다. 해결 방법은 다른 브라우저에 대해 서로 다른 SameSite 동작을 지원해야 하기 때문에 복잡합니다. 
>
> ASP.NET 코어 2.1 이상 버전 앱 서비스에서 호스팅하는 이 주요 변경 내용은 이미 패치되어 Chrome 80 및 이전 브라우저를 적절하게 처리합니다. 또한 ASP.NET 프레임워크 4.7.2에 대한 동일한 패치가 2020년 1월 내내 앱 서비스 인스턴스에 배포됩니다. 앱이 패치를 받았는지 확인하는 방법을 포함하여 자세한 내용은 [Azure App Service SameSite 쿠키 업데이트를](https://azure.microsoft.com/updates/app-service-samesite-cookie-update/)참조하십시오.
>

기본 모바일 응용 프로그램과 관련된 자세한 내용은 [Azure App Service를 사용하여 모바일 응용 프로그램에 대한 사용자 인증 및 권한 부여](../app-service-mobile/app-service-mobile-auth.md)를 참조하세요.

## <a name="how-it-works"></a>작동 방법

인증 및 권한 부여 모듈은 애플리케이션 코드와 동일한 샌드박스에서 실행됩니다. 이 기능이 활성화되면 애플리케이션 코드에 의해 처리되기 전에 들어오는 모든 HTTP 요청이 여기를 통과합니다.

![](media/app-service-authentication-overview/architecture.png)

이 모듈은 다음과 같이 앱에 대한 몇 가지 사항을 처리합니다.

- 지정된 공급자를 사용하여 사용자 인증
- 토큰의 유효성 검사, 저장 및 새로 고침
- 인증된 세션 관리
- 요청 헤더에 ID 정보 삽입

모듈은 애플리케이션 코드와 별도로 실행되며 앱 설정을 사용하여 구성됩니다. SDK, 특정 언어 또는 애플리케이션 코드의 변경이 필요하지 않습니다. 

### <a name="userapplication-claims"></a>사용자/애플리케이션 클레임

모든 언어 프레임워크에 대해 App Service는 요청 헤더에 삽입하여 코드에서 사용할 수 있는 들어오는 토큰(인증된 최종 사용자 또는 클라이언트 응용 프로그램의 클레임)에서 클레임을 만듭니다. ASP.NET 4.6 응용 프로그램의 경우 App Service는 인증된 사용자의 클레임을 사용하여 [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current)를 채우기 때문에 `[Authorize]` 특성을 비롯한 표준 .NET 코드 패턴을 따를 수 있습니다. 마찬가지로 PHP 앱의 경우, App Service는 `_SERVER['REMOTE_USER']` 변수를 채웁니다. Java 앱의 경우 [Tomcat 서볼릿에서 클레임에 액세스할 수](containers/configure-language-java.md#authenticate-users-easy-auth)있습니다.

Azure `ClaimsPrincipal.Current` [Functions의](../azure-functions/functions-overview.md)경우 .NET 코드에 대해 채워지지 않지만 요청 헤더에서 사용자 클레임을 `ClaimsPrincipal` 찾거나 요청 컨텍스트에서 또는 바인딩 매개 변수를 통해 개체를 얻을 수 있습니다. 자세한 내용은 [클라이언트 ID 작업을](../azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities) 참조하십시오.

자세한 내용은 [사용자 클레임 액세스](app-service-authentication-how-to.md#access-user-claims)를 참조하세요.

### <a name="token-store"></a>토큰 저장소

App Service는 웹앱, API 또는 기본 모바일 앱의 사용자와 연결된 토큰 리포지토리인 내장 토큰 저장소를 제공합니다. 공급자와 인증을 사용하도록 설정하면 이 토큰 저장소를 앱에서 즉시 사용할 수 있습니다. 다음과 같이 애플리케이션 코드가 사용자를 대신하여 이러한 공급자의 데이터에 액세스해야 하는 경우, 

- 인증된 사용자의 Facebook 타임라인에 게시
- Microsoft 그래프 API를 사용하여 사용자의 회사 데이터 읽기

일반적으로 애플리케이션에서 이러한 토큰을 수집, 저장 및 새로 고치는 코드를 작성해야 합니다. 토큰 저장소를 사용하면 토큰이 필요할 때 [토큰을 가져오고](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) 토큰이 무효화되면 [App Service에 알려 이를 새로 고치도록](app-service-authentication-how-to.md#refresh-identity-provider-tokens) 해야 합니다. 

ID 토큰, 액세스 토큰 및 새로 고침 토큰은 인증된 세션에 대해 캐시되며 연결된 사용자만 액세스할 수 있습니다.  

앱에서 토큰을 사용할 필요가 없는 경우 토큰 저장소를 사용하지 않도록 설정할 수 있습니다.

### <a name="logging-and-tracing"></a>로깅 및 추적

[애플리케이션 로깅을 사용하도록 설정](troubleshoot-diagnostic-logs.md)하면 로그 파일에서 인증 및 권한 추적을 바로 볼 수 있습니다. 예상하지 못했던 인증 오류가 표시되면 기존 응용 프로그램 로그를 확인하여 모든 세부 정보를 편리하게 찾을 수 있습니다. [실패한 요청 추적](troubleshoot-diagnostic-logs.md)을 사용하면 실패한 요청에서 인증 및 권한 부여 모듈이 수행한 역할을 정확히 볼 수 있습니다. 추적 로그에서 `EasyAuthModule_32/64`라는 모듈에 대한 참조를 찾습니다. 

## <a name="identity-providers"></a>ID 공급자

App Service는 [페더레이션 ID](https://en.wikipedia.org/wiki/Federated_identity)를 사용하며 여기서 타사 ID 공급자는 사용자 ID와 인증 흐름을 관리합니다. 기본적으로 5가지 ID 공급자를 사용할 수 있습니다. 

| 공급자 | 로그인 엔드포인트 |
| - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` |
| [Microsoft 계정](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/identity/choose-auth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` |

이러한 공급자중 하나를 사용하여 인증 및 권한 부여를 활성화하면 사용자 인증과 공급자의 인증 토큰 유효성 검사에 로그인 엔드포인트를 사용할 수 있습니다. 사용자에게 여러 가지 로그인 옵션을 쉽게 제공할 수 있습니다. 다른 ID 공급자 또는 [사용자 고유의 사용자 지정 ID 솔루션][custom-auth]을 통합할 수도 있습니다.

## <a name="authentication-flow"></a>인증 흐름

인증 흐름은 모든 공급자에 대해 동일하지만 공급자의 SDK로 로그인하는지 여부에 따라 다릅니다.

- 공급자 SDK가 없는 경우: 애플리케이션은 페드레이션 로그인을 App Service에 위임합니다. 이는 일반적으로 공급자의 로그인 페이지를 사용자에게 제공할 수 있는 브라우저 앱을 사용하는 경우입니다. 서버 코드는 로그인 프로세스를 관리하므로 _서버 방향 흐름_ 또는 _서버 흐름_이라고도 합니다. 이 경우는 브라우저 앱에 적용됩니다. 또한 SDK가 App Service 인증을 사용하여 사용자를 로그인시키기 위해 웹 보기를 열기 때문에 Mobile Apps 클라이언트 SDK를 사용하여 사용자를 로그인시키는 네이티브 앱에도 적용됩니다. 
- 공급자 SDK 사용: 애플리케이션은 사용자를 수동으로 공급자에 로그인시킨 다음, 유효성 검사를 위해 인증 토큰을 App Service에 제출합니다. 이는 일반적으로 공급자의 로그인 페이지를 사용자에게 제공할 수 없는 브라우저리스 앱을 사용하는 경우입니다. 애플리케이션 코드는 로그인 프로세스를 관리하므로 _클라이언트 방향 흐름_ 또는 _클라이언트 흐름_이라고도 합니다. 이러한 경우는 REST API, [Azure Functions](../azure-functions/functions-overview.md) 및 JavaScript 브라우저 클라이언트뿐만 아니라 로그인 프로세스에서 더 많은 유연성이 필요한 브라우저 앱에도 적용됩니다. 공급자의 SDK를 사용하여 사용자를 로그인시키는 네이티브 모바일 앱에도 적용됩니다.

> [!NOTE]
> 앱 서비스의 신뢰할 수 있는 브라우저 앱에서 앱 서비스 또는 [Azure 함수의](../azure-functions/functions-overview.md) 다른 REST API로의 호출은 서버 지향 흐름을 사용하여 인증할 수 있습니다. 자세한 내용은 [App Service에서 인증 및 권한 부여 사용자 지정](app-service-authentication-how-to.md)을 참조하세요.
>

아래 표는 인증 흐름 단계를 보여줍니다.

| 단계 | SDK 공급자가 없는 경우 | SDK 공급자가 있는 경우 |
| - | - | - |
| 1. 로그인 사용자 | 클라이언트를 `/.auth/login/<provider>`로 리디렉션합니다. | 클라이언트 코드는 공급자의 SDK를 사용하여 사용자를 직접 로그인시키고 인증 토큰을 받습니다. 자세한 내용은 공급자 설명서를 참조하세요. |
| 2. 사후 인증 | 공급자가 클라이언트를 `/.auth/login/<provider>/callback`으로 리디렉션합니다. | 클라이언트 코드는 유효성 `/.auth/login/<provider>` 검사를 위해 [공급자에서 토큰을 게시합니다.](app-service-authentication-how-to.md#validate-tokens-from-providers) |
| 3. 인증된 세션 설정 | App Service는 인증된 쿠키를 응답에 추가합니다. | App Service는 자체 인증 토큰을 클라이언트 코드로 반환합니다. |
| 4. 인증된 콘텐츠 제공 | 클라이언트는 후속 요청에 인증 쿠키를 포함합니다(브라우저에 의해 자동 처리됨). | 클라이언트 코드는 `X-ZUMO-AUTH` 헤더에 인증 토큰을 제공합니다(Mobile Apps 클라이언트 SDK에 의해 자동 처리됨). |

클라이언트 브라우저의 경우 App Service는 인증되지 않은 모든 사용자를 자동으로 `/.auth/login/<provider>`로 보냅니다. 사용자 자신이 선택한 제공자를 사용하여 앱에 로그인할 수 있도록 하나 이상의 `/.auth/login/<provider>` 링크를 사용자에게 할 수도 있습니다.

<a name="authorization"></a>

## <a name="authorization-behavior"></a>권한 부여 동작

Azure [포털에서](https://portal.azure.com)들어오는 요청이 인증되지 않은 경우 여러 동작으로 앱 서비스 인증을 구성할 수 있습니다.

![](media/app-service-authentication-overview/authorization-flow.png)

다음 제목은 옵션을 설명합니다.

### <a name="allow-anonymous-requests-no-action"></a>익명 요청 허용(작업 없음)

이 옵션은 응용 프로그램 코드에 대한 인증되지 않은 트래픽의 승인을 연기합니다. 인증된 요청의 경우 App Service는 HTTP 헤더의 인증 정보도 전달합니다. 

이 옵션은 익명 요청을 보다 유연하게 처리할 수 있습니다. 예를 들어 [여러 로그인 공급자](app-service-authentication-how-to.md#use-multiple-sign-in-providers)를 사용자에게 제공할 수 있습니다. 그러나 코드를 작성해야 합니다. 

### <a name="allow-only-authenticated-requests"></a>인증된 요청만 허용

옵션은 **\<공급자>를 사용한 로그인**입니다. App Service는 사용자가 선택한 공급자에 대한 모든 익명 요청을 `/.auth/login/<provider>`로 리디렉션합니다. 익명의 요청이 네이티브 모바일 앱에서 오는 경우 반환된 응답은 `HTTP 401 Unauthorized`입니다.

이 옵션을 사용하면 앱에서 인증 코드를 작성할 필요가 없습니다. 역할별 권한 부여와 같이 보다 정교한 권한 부여는 사용자의 클레임을 검사하여 처리할 수 있습니다([사용자 클레임 액세스](app-service-authentication-how-to.md#access-user-claims) 참조).

> [!CAUTION]
> 이러한 방식으로 액세스를 제한하는 것은 앱에 대한 모든 호출에 적용되며, 이는 많은 단일 페이지 응용 프로그램과 마찬가지로 공개적으로 사용 가능한 홈 페이지를 원하는 앱에는 바람직하지 않을 수 있습니다.

> [!NOTE]
> 인증/권한 부여는 이전에 Easy Auth로 알려졌습니다.
>

## <a name="more-resources"></a>추가 리소스

[자습서: Azure App Service에서 엔드투엔드 사용자 인증 및 권한 부여(Windows)](app-service-web-tutorial-auth-aad.md)  
[자습서: Azure App Service에서 Linux용 엔드투엔드 사용자 인증 및 권한 부여](containers/tutorial-auth-aad.md)  
[앱 서비스](app-service-authentication-how-to.md)
[.NET 코어 통합에서](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth)
인증 및 권한 부여를 사용자 지정하여[.NET Core(제3자)와 함께 작동하는 Azure App Service 인증](https://github.com/kirkone/KK.AspNetCore.EasyAuthAuthentication) 받기

공급자별 방법 가이드:

* [Azure Active Directory 로그인을 사용하도록 앱을 구성하는 방법][AAD]
* [Facebook 로그인을 사용하도록 앱을 구성하는 방법][Facebook]
* [Google 로그인을 사용하도록 앱을 구성하는 방법][Google]
* [Microsoft 계정 로그인을 사용하도록 앱을 구성하는 방법][MSA]
* [Twitter 로그인을 사용하도록 앱을 구성하는 방법][Twitter]
* [방법: 애플리케이션에 사용자 지정 인증 사용][custom-auth]

[AAD]: configure-authentication-provider-aad.md
[Facebook]: configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: configure-authentication-provider-microsoft.md
[Twitter]: configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
