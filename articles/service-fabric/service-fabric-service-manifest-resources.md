---
title: 서비스 패브릭 서비스 끝점 지정
description: HTTPS 엔드포인트를 설정하는 방법을 포함하여 서비스 매니페스트에서 엔드포인트 리소스를 설명하는 방법
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 88e71d15829e68bde635f5b4d40224b8fa914f40
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417587"
---
# <a name="specify-resources-in-a-service-manifest"></a>서비스 매니페스트에서 리소스 지정
## <a name="overview"></a>개요
서비스 매니페스트를 사용하면 컴파일된 코드를 변경하지 않고 서비스에서 사용하는 리소스를 선언하거나 변경할 수 있습니다. 서비스 패브릭은 서비스에 대한 엔드포인트 리소스구성을 지원합니다. 서비스 매니페스트에 지정된 리소스에 대한 액세스는 애플리케이션 매니페스트의 SecurityGroup을 통해 제어할 수 있습니다. 리소스를 선언하면 배포 시에 이러한 리소스를 변경할 수 있으며 즉, 서비스에 새로운 구성 메커니즘을 도입하지 않아도 됩니다. ServiceManifest.xml 파일에 대한 스키마 정의는 Service Fabric SDK 및 도구와 함께 *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*에 설치됩니다.

## <a name="endpoints"></a>엔드포인트
서비스 매니페스트에 엔드포인트 리소스가 정의되면 서비스 패브릭에서는 포트가 명시적으로 지정되지 않을 경우 예약된 애플리케이션 포트 범위에 포함되는 포트를 할당합니다. 예를 들어 이 단락 다음에 제공된 매니페스트 코드 조각에 지정된 엔드포인트 *ServiceEndpoint1* 을 보세요. 또한 서비스에서 리소스의 특정 포트를 요청할 수도 있습니다. 다른 클러스터 노드에서 실행되는 서비스 복제본을 다른 포트 번호에 할당할 수 있으며, 같은 노드에서 실행되는 서비스의 복제본은 포트를 공유합니다. 그러면 서비스 복제본은 복제 및 클라이언트 요청의 수신 대기를 위해 필요한 경우 이러한 포트를 사용할 수 있습니다.

https 끝점을 지정하는 서비스를 활성화하면 Service Fabric은 포트에 대한 액세스 제어 항목을 설정하고 지정된 서버 인증서를 포트에 바인딩하고 서비스가 인증서의 개인 키에 대한 사용 권한으로 실행중인 ID를 부여합니다. 서비스 패브릭이 시작될 때마다 또는 업그레이드를 통해 응용 프로그램의 인증서 선언이 변경될 때마다 활성화 흐름이 호출됩니다. 또한 끝점 인증서는 변경/갱신에 대해 모니터링되며 필요에 따라 사용 권한이 주기적으로 다시 적용됩니다.

서비스가 종료되면 서비스 패브릭은 끝점 액세스 제어 항목을 정리하고 인증서 바인딩을 제거합니다. 그러나 인증서의 개인 키에 적용 된 모든 권한은 정리 되지 않습니다.

> [!WARNING] 
> 정적 포트는 클러스터매니페스트에 지정된 응용 프로그램 포트 범위와 겹치지 않아야 합니다. 정적 포트를 지정하는 경우 응용 프로그램 포트 범위 외부에 할당하면 포트 충돌이 발생합니다. 릴리스 6.5CU2에서는 이러한 충돌을 감지할 때 **상태 경고를** 발행하지만 배포가 제공된 6.5 동작과 동기화될 수 있도록 합니다. 그러나 다음 주요 릴리스에서 응용 프로그램 배포를 방지할 수 있습니다.
>
> 릴리스 7.0에서는 응용 프로그램 포트 범위 사용량이 HostingConfig::ApplicationPort배분 임계값(기본값 80%)을 초과하는 것을 감지하면 **상태 경고를** 발행합니다.
>

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

단일 서비스 패키지에 여러 코드 패키지가 있으면 해당 코드 패키지가 **엔드포인트** 섹션에서도 참조되어야 합니다.  예를 들어 **ServiceEndpoint2a** 및 **ServiceEndpoint2b**가 다른 코드 패키지를 참조하는 동일한 서비스 패키지의 엔드포인트이면, 각 엔드포인트에 해당하는 코드 패키지는 다음과 같이 구분됩니다.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

구성 패키지 설정 파일(settings.xml)의 엔드포인트 참조에 대한 자세한 내용은 [상태 저장 Reliable Services 구성](service-fabric-reliable-services-configuration.md) 을 참조하세요.

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>예: 서비스에 대한 HTTP 엔드포인트 지정
다음 서비스 매니페스트는 &lt;Resources&gt; 요소에서 한 개의 TCP 엔드포인트 리소스와 두 개의 HTTP 엔드포인트 리소스를 정의합니다.

HTTP 엔드포인트는 서비스 패브릭에 의해 자동으로 ACL 처리됩니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
      <Endpoint Name="ServiceEndpoint4" Protocol="https" Port="14023"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>예: 서비스에 대한 HTTPS 엔드포인트 지정
HTTPS 프로토콜은 서버 인증을 제공하며, 클라이언트-서버 통신을 암호화하는 데에도 사용됩니다. Service Fabric 서비스에서 HTTPS를 사용할 수 있도록 설정하려면 위의 *ServiceEndpoint3* 엔드포인트와 같이 서비스 매니페스트의 *리소스 -&gt; 엔드포인트 -&gt; 엔드포인트* 섹션에서 프로토콜을 지정합니다.

> [!NOTE]
> 서비스의 프로토콜은 애플리케이션 업그레이드 중에 변경될 수 없습니다. 업그레이드 중에 변경되는 경우 불안정한 변경이 됩니다.
> 

> [!WARNING] 
> HTTPS를 사용하는 경우 동일한 노드에 배포된 다른 서비스 인스턴스(애플리케이션과 독립적)에 동일한 포트 및 인증서를 사용하지 않습니다. 다른 애플리케이션 인스턴스에서 동일한 포트를 사용하는 두 가지 다른 서비스를 업그레이드하면 업그레이드 오류가 발생합니다. 자세한 내용은 [HTTPS 엔드포인트로 여러 애플리케이션 업그레이드](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints)를 참조하세요.
>

다음은 HTTPS 끝점에 필요한 구성을 보여 주는 ApplicationManifest 예제입니다. 서버/끝점 인증서는 지문 또는 제목 공통 이름으로 선언될 수 있으며 값을 제공해야 합니다. EndpointRef는 서비스 매니페스트에서 EndpointResource에 대한 참조이며 프로토콜이 'https' 프로토콜로 설정되어 있어야 합니다. 둘 이상의 Endpointcertificate를 추가할 수 있습니다.  

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="SslCertByTP" EndpointRef="ServiceEndpoint3"/>
      <EndpointBindingPolicy CertificateRef="SslCertByCN" EndpointRef="ServiceEndpoint4"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_ ]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="SslCertByTP" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
    <EndpointCertificate Name="SslCertByCN" X509FindType="FindBySubjectName" X509FindValue="ServiceFabric-EndpointCertificateBinding-Test" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

Linux 클러스터의 경우 **내** 저장소는 기본적으로 **/var/lib/sfcerts** 폴더입니다.


## <a name="overriding-endpoints-in-servicemanifestxml"></a>ServiceManifest.xml에서 엔드포인트 재정의

ApplicationManifest에서 ConfigOverrides 섹션의 형제로 지정될 ResourceOverrides 섹션을 추가합니다. 이 섹션에서는 ServiceManiifest에 지정된 resources 섹션의 Endpoints 섹션에 대한 재정의를 지정할 수 있습니다. 엔드포인트 재정의는 런타임 5.7.217/SDK 2.7.217 이상에서만 지원됩니다.

ApplicationParameters를 사용하여 ServiceManifest에서 EndPoint를 재정의하려면 ApplicationManifest를 다음과 같이 변경합니다.

ServiceManifestImport 섹션에서 새 섹션 "ResourceOverrides"를 추가합니다.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="ServiceEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
        <Endpoint Name="ServiceEndpoint1" Port="[Port1]" Protocol="[Protocol1] "/>
      </Endpoints>
    </ResourceOverrides>
        <Policies>
           <EndpointBindingPolicy CertificateRef="SslCertByTP" EndpointRef="ServiceEndpoint"/>
        </Policies>
  </ServiceManifestImport>
```

Parameters에서 아래 내용을 추가합니다.

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

애플리케이션을 배포하는 동안 이러한 값을 ApplicationParameters로 제공할 수 있습니다.  다음은 그 예입니다.

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

참고: ApplicationParameters에 제공된 값이 비어 있으면 해당 EndPointName에 대한 ServiceManifest에 제공된 기본값으로 돌아갑니다.

다음은 그 예입니다.

ServiceManifest에서 다음을 지정했습니다.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

또한 Applications 매개 변수에 대한 Port1 및 Protocol1 값은 null이거나 비어 있습니다. 포트는 여전히 ServiceFabric에 의해 결정됩니다. 또한 Protocol은 tcp가 됩니다.

잘못된 값을 지정한다고 가정해 보겠습니다. 포트와 마찬가지로 int 대신 문자열 값 "Foo"를 지정했습니다.  New-ServiceFabricApplication 명령은 오류와 함께 실패합니다 : 'ResourceOverrides' 섹션에서 이름 'ServiceEndpoint1' 속성 'Port1'이 있는 재정의 매개 변수가 잘못되었습니다. 지정된 값은 'Foo'이지만 'int'가 필요합니다.
