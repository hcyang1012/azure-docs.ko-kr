---
title: Azure 서비스 패브릭 응용 프로그램 보안 암호 관리
description: Service Fabric 애플리케이션에서 비밀 값을 보호하는 방법에 대해 설명합니다(플랫폼에 관계 없음).
author: vturecek
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: 4d2138935122b9e08b21963519fce3f72466ab1f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414506"
---
# <a name="manage-encrypted-secrets-in-service-fabric-applications"></a>Service Fabric 애플리케이션에서 암호화된 비밀 관리
이 가이드에서는 Service Fabric 애플리케이션에서 비밀을 관리하는 단계를 안내합니다. 스토리지 연결 문자열, 암호, 일반 텍스트로 처리하면 안 되는 값 등 모든 민감한 정보를 비밀로 처리할 수 있습니다.

Service Fabric 애플리케이션에서 암호화된 비밀을 사용하는 데에는 세 가지 단계가 포함됩니다.
* 암호화 인증서 설정 및 비밀 암호화
* 애플리케이션에서 암호화된 비밀 지정
* 서비스 코드에서 암호화된 비밀 해독

## <a name="set-up-an-encryption-certificate-and-encrypt-secrets"></a>암호화 인증서 설정 및 비밀 암호화
암호화 인증서를 설정하고 이를 사용하여 비밀을 암호화하는 것은 Windows와 Linux 간에 다릅니다.
* [Windows 클러스터에서 암호화 인증서 설정 및 비밀 암호화][secret-management-windows-specific-link]
* [Linux 클러스터에서 암호화 인증서를 설정하고 비밀을 암호화합니다.][secret-management-linux-specific-link]

## <a name="specify-encrypted-secrets-in-an-application"></a>애플리케이션에서 암호화된 비밀 지정
이전 단계에서 인증서를 사용하여 비밀을 암호화하고 애플리케이션에서 사용할 수 있도록 base-64로 인코딩된 문자열을 생성하는 방법을 설명했습니다. 이 base-64로 인코딩된 문자열을 서비스의 Settings.xml에서 암호화된 [매개 변수][parameters-link]로, 또는 서비스의 ServiceManifest.xml에서 암호화된 [환경 변수][environment-variables-link]로 지정할 수 있습니다.

서비스의 Settings.xml 구성 파일에서 암호화된 [매개 변수][parameters-link]를 `true`로 설정된 `IsEncrypted` 특성을 통해 지정합니다.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```
서비스의 ServiceManifest.xml 파일에서 암호화된 [환경 변수][environment-variables-link]를 `Encrypted`로 설정된 `Type` 특성을 통해 지정합니다.
```xml
<CodePackage Name="Code" Version="1.0.0">
  <EnvironmentVariables>
    <EnvironmentVariable Name="MyEnvVariable" Type="Encrypted" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </EnvironmentVariables>
</CodePackage>
```

암호는 응용 프로그램 매니페스트에 인증서를 지정하여 서비스 패브릭 응용 프로그램에도 포함되어야 합니다. **SecretsCertificate** 요소를 **ApplicationManifest.xml에** 추가하고 원하는 인증서의 지문을 포함합니다.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```
> [!NOTE]
> SecretsCertificate을 지정 하는 응용 프로그램을 활성화 하면 서비스 패브릭 일치 하는 인증서를 찾을 것입니다 및 응용 프로그램이 인증서의 개인 키에 대 한 전체 권한으로 실행 되는 ID를 부여 합니다. 또한 서비스 패브릭은 인증서에서 변경 내용을 모니터링하고 그에 따라 권한을 다시 적용합니다. 일반 이름으로 선언된 인증서의 변경 내용을 검색하기 위해 Service Fabric은 일치하는 모든 인증서를 검색하고 캐시된 지문 목록과 비교하는 정기적인 작업을 실행합니다. 새 지문이 검색되면 해당 주체의 인증서가 갱신되었음을 의미합니다. 작업은 클러스터의 각 노드에서 분당 한 번 실행됩니다.
>
> SecretsCertificate은 제목 기반 선언을 허용하지만 암호화된 설정은 클라이언트의 설정을 암호화하는 데 사용된 키 쌍에 연결됩니다. 원본 암호화 인증서(또는 이에 상응하는 인증서)가 주체 기반 선언과 일치하고 해당 개인 키를 포함하여 응용 프로그램을 호스팅할 수 있는 클러스터의 모든 노드에 설치되었는지 확인해야 합니다. 주체 기반 선언과 일치하고 원래 암호화 인증서와 동일한 키 쌍에서 빌드된 모든 시간 유효한 인증서는 등가물로 간주됩니다.
>

### <a name="inject-application-secrets-into-application-instances"></a>애플리케이션 비밀을 애플리케이션 인스턴스에 삽입
여러 환경에 배포할 때에는 배포를 최대한 자동화하는 것이 좋습니다. 빌드 환경에서 비밀 암호화를 수행하고 애플리케이션 인스턴스를 만들 때 암호화된 비밀을 매개 변수로 제공하면 배포를 자동화할 수 있습니다.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Settings.xml에 재정의 가능한 매개 변수 사용
Settings.xml 구성 파일은 애플리케이션 생성 시 제공할 수 있는 재정의 가능한 매개 변수를 허용합니다. 매개 변수 값을 입력하는 대신 `MustOverride` 특성을 사용합니다.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Settings.xml의 값을 재정의하려면 ApplicationManifest.xml에서 서비스의 재정의 매개 변수를 선언합니다.

```xml
<ApplicationManifest ... >
  <Parameters>
    <Parameter Name="MySecret" DefaultValue="" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides>
      <ConfigOverride Name="Config">
        <Settings>
          <Section Name="MySettings">
            <Parameter Name="MySecret" Value="[MySecret]" IsEncrypted="true" />
          </Section>
        </Settings>
      </ConfigOverride>
    </ConfigOverrides>
  </ServiceManifestImport>
 ```

이제 애플리케이션의 인스턴스를 만들 때 이 값을 *애플리케이션 매개 변수* 로 지정할 수 있습니다. 애플리케이션 인스턴스 만들기를 PowerShell로 스크립팅하거나 C#으로 작성하면 빌드 프로세스에 쉽게 통합할 수 있습니다.

PowerShell을 사용하면 매개 변수가 `New-ServiceFabricApplication` 명령에 [해시 테이블](https://technet.microsoft.com/library/ee692803.aspx)로 제공됩니다.

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

C#을 사용하면 애플리케이션 매개 변수가 `ApplicationDescription`에 `NameValueCollection`로 지정됩니다.

```csharp
FabricClient fabricClient = new FabricClient();

NameValueCollection applicationParameters = new NameValueCollection();
applicationParameters["MySecret"] = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=";

ApplicationDescription applicationDescription = new ApplicationDescription(
    applicationName: new Uri("fabric:/MyApp"),
    applicationTypeName: "MyAppType",
    applicationTypeVersion: "1.0.0",
    applicationParameters: applicationParameters)
);

await fabricClient.ApplicationManager.CreateApplicationAsync(applicationDescription);
```

## <a name="decrypt-encrypted-secrets-from-service-code"></a>서비스 코드에서 암호화된 비밀 해독
[매개 변수][parameters-link] 및 [환경 변수][environment-variables-link]에 액세스하기 위한 API로 암호화된 값을 손쉽게 해독할 수 있습니다. 암호화된 문자열에는 암호화에 사용된 인증서 정보가 포함되어 있으므로 수동으로 인증서를 지정할 필요가 없습니다. 서비스가 실행되고 있는 노드에 인증서를 설치하기만 하면 됩니다.

```csharp
// Access decrypted parameters from Settings.xml
ConfigurationPackage configPackage = FabricRuntime.GetActivationContext().GetConfigurationPackageObject("Config");
bool MySecretIsEncrypted = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].IsEncrypted;
if (MySecretIsEncrypted)
{
    SecureString MySecretDecryptedValue = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].DecryptValue();
}

// Access decrypted environment variables from ServiceManifest.xml
// Note: you do not have to call any explicit API to decrypt the environment variable.
string MyEnvVariable = Environment.GetEnvironmentVariable("MyEnvVariable");
```

## <a name="next-steps"></a>다음 단계
* 서비스 패브릭 [시크릿 스토어](service-fabric-application-secret-store.md) 
* [애플리케이션 및 서비스 보안](service-fabric-application-and-service-security.md)에 대한 자세한 정보

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[service fabric secrets store]: service-fabric-application-secret-store.md