---
title: "Service Fabric サービス エンドポイントの指定 | Microsoft Docs"
description: "サービス マニフェストにエンドポイント リソースを記述する方法 (HTTPS エンドポイントの設定方法を含みます)"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: da36cbdb-6531-4dae-88e8-a311ab71520d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/02/2017
ms.author: subramar
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 9cfdb94d1e030fe9d467389acf8894d79efd17d1
ms.lasthandoff: 03/29/2017


---
# <a name="specify-resources-in-a-service-manifest"></a>サービス マニフェストにリソースを指定する
## <a name="overview"></a>Overview
サービス マニフェストを使用すると、コンパイルしたコードを変更することなく、サービスで使用するリソースを宣言/変更できます。 Azure Service Fabric は、サービスで使用するエンドポイント リソースの構成をサポートします。 サービス マニフェストで指定したリソースへのアクセスは、SecurityGroup を使用してアプリケーション マニフェスト内で制御できます。 リソースを宣言すると、宣言したリソースをデプロイメント時に変更できるため、サービスに新しい構成メカニズムを導入する必要がありません。 ServiceManifest.xml ファイルのスキーマ定義は、Service Fabric SDK およびツールと共に *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd* にインストールされます。

## <a name="endpoints"></a>エンドポイント
サービス マニフェストにエンドポイント リソースが定義されているが、ポートが明示的に指定されていない場合、Service Fabric は、予約済みのアプリケーション ポートの範囲からポートを割り当てます。 たとえば、この後のマニフェスト スニペットで指定されているエンドポイント *ServiceEndpoint1* をご覧ください。 さらに、サービスでリソースの特定のポートを要求することもできます。 別のクラスター ノードで実行されているサービスのレプリカには、異なるポート番号を割り当てることができます。一方、同じノードで実行されているサービスのレプリカはポートを共有します。 その後、サービス レプリカは、レプリケーションやクライアント要求のリッスンのために、必要に応じてこのポートを使用できます。

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

構成パッケージ設定ファイル (settings.xml) からエンドポイントを参照する方法の詳細については、「 [ステートフル Reliable Services の構成](service-fabric-reliable-services-configuration.md) 」をご覧ください。

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>例: サービスの HTTP エンドポイントを指定する
次のサービス マニフェストは、1 つの TCP エンドポイント リソースと 2 つの HTTP エンドポイント リソースを &lt;Resources&gt; 要素内に定義しています。

HTTP エンドポイントは Service Fabric によって自動的に ACL に登録されます。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
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

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an
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

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>例: サービスの HTTPS エンドポイントを指定する
HTTPS プロトコルはサーバー認証を提供し、また、クライアント/サーバー間通信の暗号化に使用されます。 Service Fabric サービスで HTTPS を有効にするには、サービス マニフェストの "*リソース -> エンドポイント -> エンドポイント*" セクションにプロトコルを指定します (前述のエンドポイント *ServiceEndpoint3* を参照してください)。

> [!NOTE]
> アプリケーションのアップグレード中はサービスのプロトコルを変更することはできません。 アップグレード中に変更する場合、これは重大な変更です。
> 
> 

HTTPS で設定する必要がある ApplicationManifest の例を次に示します  証明書のサムプリントを指定する必要があります。 EndpointRef は、HTTPS プロトコルを設定する ServiceManifest 内の EndpointResource への参照です。 複数の EndpointCertificate を追加することができます。  

```
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
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
      <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint3"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="TestCert1" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

