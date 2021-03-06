---
title: Azure Cosmos DB 및 Active Directory를 사용 하 인 인증서 기반 인증
description: Azure Cosmos DB의 키에 액세스하도록 인증서 기반 인증에 대한 Azure AD ID를 구성하는 방법에 대해 알아봅니다.
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: 085280a8064e4d12ac63939ada7cdb296d47dc70
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365778"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-db-account"></a>Azure Cosmos DB 계정에서 키에 액세스하는 Azure AD ID에 대한 인증서 기반 인증

인증서 기반 인증을 사용하면 클라이언트 인증서와 함께 Azure AD(Azure Active Directory)를 사용하여 클라이언트 애플리케이션을 인증할 수 있습니다. Azure의 온-프레미스 컴퓨터 또는 가상 머신과 같이 ID가 필요한 컴퓨터에서 인증서 기반 인증을 수행할 수 있습니다. 그런 다음 응용 프로그램에서 직접 키를 갖지 않고도 Azure Cosmos DB 키를 읽을 수 있습니다. 이 문서에서는 샘플 Azure AD 응용 프로그램을 만들고, 인증서 기반 인증을 구성하고, 새 응용 프로그램 ID를 사용하여 Azure에 로그인한 다음 Azure Cosmos 계정에서 키를 검색하는 방법을 설명합니다. 이 문서에서는 Azure PowerShell을 사용하여 ID를 설정하고 Azure Cosmos 계정에서 키를 인증하고 액세스하는 C# 샘플 앱을 제공합니다.  

## <a name="prerequisites"></a>사전 요구 사항

* Azure PowerShell의 [최신 버전을](/powershell/azure/install-az-ps) 설치합니다.

* [Azure 구독이](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)없는 경우 시작하기 전에 [무료 계정을](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 만듭니다.

## <a name="register-an-app-in-azure-ad"></a>Azure AD에 앱 등록

이 단계에서는 Azure AD 계정에 샘플 웹 응용 프로그램을 등록합니다. 이 응용 프로그램은 나중에 Azure Cosmos DB 계정에서 키를 읽는 데 사용됩니다. 다음 단계를 사용하여 응용 프로그램을 등록합니다. 

1. [Azure 포털에](https://portal.azure.com/)로그인합니다.

1. Azure **Active Directory** 창을 열고 **앱 등록** 창으로 이동한 다음 새 **등록을**선택합니다. 

   ![Active Directory의 새 응용 프로그램 등록](./media/certificate-based-authentication/new-app-registration.png)

1. 신청서 **등록** 양식을 작성하여 다음 세부 정보를 확인하십시오.  

   * **이름** - 응용 프로그램에 대한 이름을 제공, 그것은 "sampleApp"와 같은 모든 이름이 될 수 있습니다.
   * **지원되는 계정 유형** - 현재 디렉터리에서 이 응용 프로그램에 액세스할 수 있도록 **하려면 이 조직 디렉터리에서 만 계정을 선택합니다(기본 디렉터리).** 
   * **URL 리디렉션** – **웹** 형식의 응용 프로그램을 선택하고 응용 프로그램이 호스팅되는 URL을 제공하므로 모든 URL이 될 수 있습니다. 이 예제에서는 앱이 존재하지 않더라도 `https://sampleApp.com` 괜찮습니다.와 같은 테스트 URL을 제공할 수 있습니다.

   ![샘플 웹 응용 프로그램 등록](./media/certificate-based-authentication/register-sample-web-app.png)

1. 양식을 작성한 후 **등록을** 선택합니다.

1. 앱이 등록된 후 **응용 프로그램(클라이언트) ID** 및 **개체 ID를**기록하면 다음 단계에서 이러한 세부 정보를 사용합니다. 

   ![응용 프로그램 및 개체 아이디 받기](./media/certificate-based-authentication/get-app-object-ids.png)

## <a name="install-the-azuread-module"></a>AzureAD 모듈 설치

이 단계에서는 Azure AD PowerShell 모듈을 설치합니다. 이 모듈은 이전 단계에서 등록한 응용 프로그램의 ID를 가져오고 자체 서명된 인증서를 해당 응용 프로그램에 연결하는 데 필요합니다. 

1. 관리자 권한이 있는 Windows PowerShell ISE를 엽니다. 아직 완료하지 않은 경우 AZ PowerShell 모듈을 설치하고 구독에 연결합니다. 구독이 여러 개인 경우 다음 명령에 표시된 대로 현재 구독의 컨텍스트를 설정할 수 있습니다.

   ```powershell
   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. [AzureAD](/powershell/module/azuread/?view=azureadps-2.0) 모듈 설치 및 가져오기

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>Azure AD에 로그인

응용 프로그램을 등록한 Azure AD에 로그인합니다. 연결-AzureAD 명령을 사용하여 계정에 로그인하고 팝업 창에서 Azure 계정 자격 증명을 입력합니다. 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>자체 서명된 인증서 만들기

Windows PowerShell ISE의 다른 인스턴스를 열고 다음 명령을 실행하여 자체 서명된 인증서를 만들고 인증서와 연결된 키를 읽습니다.

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>인증서 기반 자격 증명 만들기 

다음으로 다음 명령을 실행하여 응용 프로그램의 개체 ID를 얻고 인증서 기반 자격 증명을 만듭니다. 이 예제에서는 인증서가 1년 후에 만료하도록 설정하고 필요한 종료 날짜로 설정할 수 있습니다.

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

위의 명령은 아래 스크린샷과 유사한 출력을 생성합니다.

![인증서 기반 자격 증명 생성 출력](./media/certificate-based-authentication/certificate-based-credential-output.png)

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>Azure Cosmos 계정을 새 ID를 사용하도록 구성합니다.

1. [Azure 포털에](https://portal.azure.com/)로그인합니다.

1. Azure Cosmos 계정으로 이동하여 **IAM(액세스 컨트롤)** 블레이드를 엽니다.

1. 역할 할당 **추가** 및 **추가를**선택합니다. 다음 스크린샷과 같이 **기여자** 역할을 사용하여 이전 단계에서 만든 샘플앱을 추가합니다.

   ![새 ID를 사용하도록 Azure Cosmos 계정 구성](./media/certificate-based-authentication/configure-cosmos-account-with-identify.png)

1. 양식을 작성한 후 **저장을** 선택합니다.

## <a name="register-your-certificate-with-azure-ad"></a>Azure AD에 인증서 등록

Azure 포털에서 Azure AD의 클라이언트 응용 프로그램과 인증서 기반 자격 증명을 연결할 수 있습니다. 자격 증명을 연결하려면 인증서 파일을 다음 단계와 함께 업로드해야 합니다.

클라이언트 애플리케이션에 대한 Azure 앱 등록에서:

1. [Azure 포털에](https://portal.azure.com/)로그인합니다.

1. Azure **Active Directory** 창을 열고 **앱 등록** 창으로 이동한 다음 이전 단계에서 만든 샘플 앱을 엽니다. 

1. **인증서 & 비밀을** 선택한 다음 **인증서를 업로드합니다.** 업로드할 이전 단계에서 만든 인증서 파일을 찾아봅습니다.

1. **추가**를 선택합니다. 인증서를 업로드한 후 지문, 시작 날짜 및 만료 값이 표시됩니다.

## <a name="access-the-keys-from-powershell"></a>PowerShell에서 키에 액세스

이 단계에서는 응용 프로그램 및 만든 인증서를 사용하여 Azure에 로그인하고 Azure Cosmos 계정의 키에 액세스합니다. 

1. 처음에는 계정에 로그인하는 데 사용한 Azure 계정의 자격 증명을 지웁히 지웁습니다. 다음 명령을 사용하여 자격 증명을 지울 수 있습니다.

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. 그런 다음 응용 프로그램의 자격 증명을 사용하여 Azure Portal에 로그인하고 Azure Cosmos DB 키에 액세스할 수 있는지 확인합니다.

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Get-AzCosmosDBAccountKey `
      -ResourceGroupName "<Resource_Group_Name_of_your_Azure_Cosmos_account>" `
      -Name "<Your_Azure_Cosmos_Account_Name>" `
      -Type "Keys"
   ```

이전 명령에Azure Cosmos 계정의 기본 및 보조 마스터 키가 표시됩니다. Azure Cosmos 계정의 활동 로그를 보고 get 키 요청이 성공하고 이벤트가 "sampleApp" 응용 프로그램에 의해 시작되는지 확인할 수 있습니다.

![Azure AD에서 get 키 호출 유효성 검사](./media/certificate-based-authentication/activity-log-validate-results.png)

## <a name="access-the-keys-from-a-c-application"></a>C# 응용 프로그램에서 키에 액세스 

C# 응용 프로그램의 키에 액세스하여 이 시나리오의 유효성을 검사할 수도 있습니다. Active Directory에 등록된 앱을 사용하여 Azure Cosmos DB 키에 액세스할 수 있는 다음 C# 콘솔 응용 프로그램입니다. 코드를 실행하기 전에 tenantId, clientID, certName, 리소스 그룹 이름, 구독 ID, Azure Cosmos 계정 이름 세부 정보를 업데이트해야 합니다. 

```csharp
using System;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Linq;
using System.Net.Http;
using System.Security.Cryptography.X509Certificates;
using System.Threading;
using System.Threading.Tasks;
 
namespace TodoListDaemonWithCert
{
    class Program
    {
        private static string aadInstance = "https://login.windows.net/";
        private static string tenantId = "<Your_Tenant_ID>";
        private static string clientId = "<Your_Client_ID>";
        private static string certName = "<Your_Certificate_Name>";
 
        private static int errorCode = 0;
        static int Main(string[] args)
        {
            MainAync().Wait();
            Console.ReadKey();
 
            return 0;
        } 
 
        static async Task MainAync()
        {
            string authContextURL = aadInstance + tenantId;
            AuthenticationContext authContext = new AuthenticationContext(authContextURL);
            X509Certificate2 cert = ReadCertificateFromStore(certName);
 
            ClientAssertionCertificate credential = new ClientAssertionCertificate(clientId, cert);
            AuthenticationResult result = await authContext.AcquireTokenAsync("https://management.azure.com/", credential);
            if (result == null)
            {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }
 
            string token = result.AccessToken;
            string subscriptionId = "<Your_Subscription_ID>";
            string rgName = "<ResourceGroup_of_your_Cosmos_account>";
            string accountName = "<Your_Cosmos_account_name>";
            string cosmosDBRestCall = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{rgName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2015-04-08";
 
            Uri restCall = new Uri(cosmosDBRestCall);
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Remove("Authorization");
            httpClient.DefaultRequestHeaders.Add("Authorization", "Bearer " + token);
            HttpResponseMessage response = await httpClient.PostAsync(restCall, null);
 
            Console.WriteLine("Got result {0} and keys {1}", response.StatusCode.ToString(), response.Content.ReadAsStringAsync().Result);
        }
 
        /// <summary>
        /// Reads the certificate
        /// </summary>
        private static X509Certificate2 ReadCertificateFromStore(string certName)
        {
            X509Certificate2 cert = null;
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection certCollection = store.Certificates;
 
            // Find unexpired certificates.
            X509Certificate2Collection currentCerts = certCollection.Find(X509FindType.FindByTimeValid, DateTime.Now, false);
 
            // From the collection of unexpired certificates, find the ones with the correct name.
            X509Certificate2Collection signingCert = currentCerts.Find(X509FindType.FindBySubjectName, certName, false);
 
            // Return the first certificate in the collection, has the right name and is current.
            cert = signingCert.OfType<X509Certificate2>().OrderByDescending(c => c.NotBefore).FirstOrDefault();
            store.Close();
            return cert;
        }
    }
}
```

이 스크립트는 다음 스크린샷과 같이 기본 및 보조 마스터 키를 출력합니다.

![csharp 응용 프로그램 출력](./media/certificate-based-authentication/csharp-application-output.png)

이전 섹션과 마찬가지로 Azure Cosmos 계정의 활동 로그를 보고 "sampleApp" 응용 프로그램에서 키 요청 이벤트를 시작 하 고 있는지 확인할 수 있습니다. 


## <a name="next-steps"></a>다음 단계

* [Azure Key Vault를 사용하여 Azure Cosmos 키 보호](access-secrets-from-keyvault.md)

* [Azure 코스모스 DB에 대한 보안 기준](security-baseline.md)
