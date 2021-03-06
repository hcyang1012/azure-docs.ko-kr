---
title: 웹 API를 호출하는 데몬 앱 구성 - Microsoft ID 플랫폼 | Azure
description: 웹 API(앱 구성)를 호출하는 데몬 응용 프로그램에 대한 코드를 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: bb2cc90e586f71bb7c90efd62ba3705cd9223bc5
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885500"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>웹 API를 호출하는 데몬 앱 - 코드 구성

웹 API를 호출하는 데몬 응용 프로그램에 대한 코드를 구성하는 방법에 대해 알아봅니다.

## <a name="msal-libraries-that-support-daemon-apps"></a>데몬 앱을 지원하는 MSAL 라이브러리

이러한 Microsoft 라이브러리는 데몬 앱을 지원합니다.

  MSAL 라이브러리 | Description
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | .NET 프레임워크 및 .NET Core 플랫폼은 데몬 응용 프로그램을 빌드하는 데 지원됩니다. (UWP, Xamarin.iOS 및 Xamarin.Android는 이러한 플랫폼이 공용 클라이언트 응용 프로그램을 빌드하는 데 사용되기 때문에 지원되지 않습니다.)
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | 파이썬의 데몬 응용 프로그램 지원.
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | 자바에서 데몬 응용 프로그램에 대한 지원.

## <a name="configure-the-authority"></a>권한 구성

Daemon 응용 프로그램은 위임된 권한이 아닌 응용 프로그램 권한을 사용합니다. 따라서 지원되는 계정 유형은 조직 디렉터리 또는 개인 Microsoft 계정(예: Skype, Xbox, Outlook.com)의 계정이 될 수 없습니다. Microsoft 개인 계정에 대한 데몬 응용 프로그램에 대한 동의를 부여할 테넌트 관리자는 없습니다. 내 조직 또는 *모든 조직의 계정에서* *계정을*선택해야 합니다.

따라서 응용 프로그램 구성에 지정된 권한을 테넌트(테넌트 ID 또는 조직과 연결된 도메인 이름 지정)해야 합니다.

ISV이고 다중 테넌트 도구를 제공하려는 경우 을 `organizations`사용할 수 있습니다. 그러나 관리자 동의를 부여하는 방법도 고객에게 설명해야 합니다. 자세한 내용은 [전체 테넌트에 대한 동의 요청을](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)참조하십시오. 또한 현재 MSAL에는 제한이 있습니다: `organizations` 클라이언트 자격 증명이 인증서가 아닌 응용 프로그램 보안인 경우에만 허용됩니다.

## <a name="configure-and-instantiate-the-application"></a>응용 프로그램 구성 및 인스턴스화

MSAL 라이브러리에서 클라이언트 자격 증명(비밀 또는 인증서)은 기밀 클라이언트 응용 프로그램 생성의 매개 변수로 전달됩니다.

> [!IMPORTANT]
> 응용 프로그램이 서비스로 실행되는 콘솔 응용 프로그램인 경우에도 데몬 응용 프로그램인 경우 기밀 클라이언트 응용 프로그램이어야 합니다.

### <a name="configuration-file"></a>구성 파일

구성 파일은 다음을 정의합니다.

- 기관 또는 클라우드 인스턴스 및 테넌트 ID입니다.
- 응용 프로그램 등록에서 얻은 클라이언트 ID입니다.
- 클라이언트 보안 또는 인증서입니다.

# <a name="net"></a>[.NET](#tab/dotnet)

[appsettings.json](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/daemon-console/appsettings.json) [.NET 코어 콘솔 데몬](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) 샘플에서.

```JSon
{
  "Instance": "https://login.microsoftonline.com/{0}",
  "Tenant": "[Enter here the tenantID or domain name for your Azure AD tenant]",
  "ClientId": "[Enter here the ClientId for your application]",
  "ClientSecret": "[Enter here a client secret for your application]",
  "CertificateName": "[Or instead of client secret: Enter here the name of a certificate (from the user cert store) as registered with your application]"
}
```

또는 `CertificateName`을 `ClientSecret` 제공합니다. 이러한 설정은 배타적입니다.

# <a name="python"></a>[Python](#tab/python)

클라이언트 기밀 을 사용하여 기밀 클라이언트를 빌드할 때 [Python 데몬](https://github.com/Azure-Samples/ms-identity-python-daemon) 샘플의 [parameters.json](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/1-Call-MsGraph-WithSecret/parameters.json) 구성 파일은 다음과 같습니다.

```Json
{
  "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "secret": "The secret generated by AAD during your confidential app registration",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

인증서를 사용하여 기밀 클라이언트를 빌드할 때 [Python 데몬](https://github.com/Azure-Samples/ms-identity-python-daemon) 샘플의 [parameters.json](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/parameters.json) 구성 파일은 다음과 같습니다.

```Json
{
  "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "thumbprint": "790E... The thumbprint generated by AAD when you upload your public cert",
  "private_key_file": "server.pem",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

# <a name="java"></a>[Java](#tab/java)

```Java
 private final static String CLIENT_ID = "";
 private final static String AUTHORITY = "https://login.microsoftonline.com/<tenant>/";
 private final static String CLIENT_SECRET = "";
 private final static Set<String> SCOPE = Collections.singleton("https://graph.microsoft.com/.default");
```

---

### <a name="instantiate-the-msal-application"></a>MSAL 애플리케이션 인스턴스화

MSAL 응용 프로그램을 인스턴스화하려면 언어에 따라 MSAL 패키지를 추가, 참조 또는 가져와야 합니다.

클라이언트 암호 또는 인증서(또는 고급 시나리오로 서명된 어설션)를 사용하는지 여부에 따라 구성이 다릅니다.

#### <a name="reference-the-package"></a>패키지 참조

응용 프로그램 코드에서 MSAL 패키지를 참조합니다.

# <a name="net"></a>[.NET](#tab/dotnet)

응용 프로그램에 [Microsoft.IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet 패키지를 추가합니다.
MSAL.NET 기밀 클라이언트 응용 프로그램은 `IConfidentialClientApplication` 인터페이스로 표시됩니다.
소스 코드에서 MSAL.NET 네임스페이스를 사용합니다.

```csharp
using Microsoft.Identity.Client;
IConfidentialClientApplication app;
```

# <a name="python"></a>[Python](#tab/python)

```python
import msal
```

# <a name="java"></a>[Java](#tab/java)

```java
import com.microsoft.aad.msal4j.ClientCredentialFactory;
import com.microsoft.aad.msal4j.ClientCredentialParameters;
import com.microsoft.aad.msal4j.ConfidentialClientApplication;
import com.microsoft.aad.msal4j.IAuthenticationResult;
import com.microsoft.aad.msal4j.IClientCredential;
import com.microsoft.aad.msal4j.MsalException;
import com.microsoft.aad.msal4j.SilentParameters;
```

---

#### <a name="instantiate-the-confidential-client-application-with-a-client-secret"></a>클라이언트 비밀로 기밀 클라이언트 응용 프로그램 인스턴스화

다음은 클라이언트 암호로 기밀 클라이언트 응용 프로그램을 인스턴스화하는 코드입니다.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="java"></a>[Java](#tab/java)

```Java
IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

---

#### <a name="instantiate-the-confidential-client-application-with-a-client-certificate"></a>클라이언트 인증서로 기밀 클라이언트 응용 프로그램 인스턴스화

인증서를 사용하여 응용 프로그램을 빌드하는 코드는 다음과 같습니다.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="java"></a>[Java](#tab/java)

MSAL Java에는 인증서를 사용하여 기밀 클라이언트 응용 프로그램을 인스턴스화하는 두 개의 빌더가 있습니다.

```Java

InputStream pkcs12Certificate = ... ; /* Containing PCKS12-formatted certificate*/
string certificatePassword = ... ;    /* Contains the password to access the certificate */

IClientCredential credential = ClientCredentialFactory.createFromCertificate(pkcs12Certificate, certificatePassword);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

또는

```Java
PrivateKey key = getPrivateKey(); /* RSA private key to sign the assertion */
X509Certificate publicCertificate = getPublicCertificate(); /* x509 public certificate used as a thumbprint */

IClientCredential credential = ClientCredentialFactory.createFromCertificate(key, publicCertificate);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

---

#### <a name="advanced-scenario-instantiate-the-confidential-client-application-with-client-assertions"></a>고급 시나리오: 클라이언트 어설션을 사용하여 기밀 클라이언트 응용 프로그램 인스턴스화

# <a name="net"></a>[.NET](#tab/dotnet)

클라이언트 보안 또는 인증서 대신 기밀 클라이언트 응용 프로그램은 클라이언트 어설션을 사용하여 ID를 증명할 수도 있습니다.

MSAL.NET 기밀 클라이언트 앱에 서명된 어설션을 제공하는 두 가지 방법이 있습니다.

- `.WithClientAssertion()`
- `.WithClientClaims()`

을 사용할 `WithClientAssertion`때 서명된 JWT를 제공해야 합니다. 이 고급 시나리오는 [클라이언트 어설션에 자세히 설명되어 있습니다.](msal-net-client-assertions.md)

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

을 사용하면 `WithClientClaims`azure AD에서 예상하는 클레임과 보내려는 추가 클라이언트 클레임이 포함된 서명된 어설션이 MSAL.NET 생성됩니다.
이 코드는 이 작업을 수행하는 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여

```csharp
string ipAddress = "192.168.1.2";
var claims = new Dictionary<string, string> { { "client_ip", ipAddress } };
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, claims)
                                          .Build();```
```

다시, 자세한 내용은 [클라이언트 어설션을](msal-net-client-assertions.md)참조 하십시오.

# <a name="python"></a>[Python](#tab/python)

MSAL Python에서는 이 `ConfidentialClientApplication`개인 키로 서명할 클레임을 사용하여 클라이언트 클레임을 제공할 수 있습니다.

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    client_claims = {"client_ip": "x.x.x.x"}
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

자세한 내용은 [기밀 클라이언트 응용 프로그램에](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)대한 MSAL 파이썬 참조 문서를 참조하십시오.

# <a name="java"></a>[Java](#tab/java)

```Java
IClientCredential credential = ClientCredentialFactory.createFromClientAssertion(assertion);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

---

## <a name="next-steps"></a>다음 단계

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [데몬 앱 - 앱에 대한 토큰 획득](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [데몬 앱 - 앱에 대한 토큰 획득](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [데몬 앱 - 앱에 대한 토큰 획득](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=java)

---
