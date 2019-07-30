---
title: Azure Service Fabric のアプリとサービスの記述 | Microsoft Docs
description: マニフェストを使って Service Fabric のアプリケーションとサービスを記述する方法について説明します。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: mani-ramaswamy
ms.assetid: 17a99380-5ed8-4ed9-b884-e9b827431b02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/19/2018
ms.author: atsenthi
ms.openlocfilehash: e5fb28b176ce14a9b871b2a6a775e0017fcc993d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052672"
---
# <a name="service-fabric-application-and-service-manifests"></a>Service Fabric のアプリケーション マニフェストとサービス マニフェスト
この記事では、ApplicationManifest.xml ファイルと ServiceManifest.xml ファイルを使って、Service Fabric のアプリケーションとサービスの定義およびバージョン管理を行う方法について説明します。  詳細な例については、[アプリケーションとサービスのマニフェストの例](service-fabric-manifest-examples.md)を参照してください。  これらのマニフェスト ファイルの XML スキーマについては、「[ServiceFabricServiceModel.xsd スキーマ ドキュメント](service-fabric-service-model-schema.md)」をご覧ください。

> [!WARNING]
> マニフェストの XML ファイルのスキーマは、子要素の正確な順序を強制的に適用します。  部分的な回避策として、Service Fabric マニフェストのいずれかの作成または変更中に Visual Studio の "C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd" を開きます。 これにより、子要素の順序を確認でき、intelli-sense が提供されます。

## <a name="describe-a-service-in-servicemanifestxml"></a>ServiceManifest.xml でサービスを記述する
サービス マニフェストは、宣言によって、サービスの種類とバージョンを定義します。 サービスの種類、ヘルスのプロパティ、負荷分散のメトリック、サービスのバイナリ、および構成ファイルなどのサービス メタデータを指定します。  別の言い方をすれば、1 つ以上のサービスの種類をサポートするよう、サービス パッケージを構成するコード、構成、データのパッケージを記述します。 サービス マニフェストにはコード、構成、データの複数のパッケージを含めることができ、個別にバージョンを管理できます。 次に示すのは、[投票サンプル アプリケーション](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart)の ASP.NET Core Web フロントエンド サービスのサービス マニフェストです (より詳細な例は[こちら](service-fabric-manifest-examples.md)にいくつかあります)。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

**Version** 属性は構造化されていない文字列で、システムでは解析されません。 Version 属性は、アップグレード用の各コンポーネントのバージョン管理に使用されます。

**ServiceTypes** は、このマニフェストで **CodePackages** でサポートされているサービスの種類を宣言します。 これらのサービスの種類のいずれかに対してサービスがインスタンス化されると、このマニフェストで宣言されているすべてのコード パッケージは、エントリ ポイントを実行してアクティブ化されます。 実行時に、サポートされているサービスの種類を登録するために、結果のプロセスが必要になります。 サービスの種類は、コード パッケージ レベルではなく、マニフェスト レベルで宣言されます。 したがって、複数のコード パッケージがあるとき、システムが宣言されたサービスの種類のいずれかを検索するときは常に、すべてアクティブ化されます。

**EntryPoint** によって指定された実行可能ファイルは通常は実行時間の長いサービス ホストです。 **SetupEntryPoint** は、他のエントリポイントの前に、Service Fabric と同じ資格情報で実行する特権を持つエントリ ポイントです (通常は *LocalSystem* アカウント)。  別々にセットアップされたエントリ ポイントの存在により、長期にわたって高い権限でサービス ホストを実行する必要がなくなります。 **EntryPoint** で指定された実行可能ファイルは、**SetupEntryPoint** が正常に終了した後に実行されます。 プロセスが終了またはクラッシュした場合、結果のプロセスは監視されて再起動されます (**SetupEntryPoint** で再起動)。  

**SetupEntryPoint** を使用する際の一般的なシナリオは、サービス開始前に実行可能ファイルを実行する場合や、昇格した特権で操作を実行する場合になります。 例:

* サービス実行可能ファイルが使用する可能性がある環境変数の設定と初期化などです。 これは、Service Fabric のプログラミング モデルによって記述されの実行可能ファイルだけに限定されません。 たとえば、npm.exe は node.js アプリケーションのデプロイに構成されているいくつかの環境変数が必要です。
* セキュリティ証明書のインストールによるアクセス制御の設定

SetupEntryPoint の構成方法について詳しくは、「[エントリ ポイント セットアップ サービスのポリシーを構成する](service-fabric-application-runas-security.md)」をご覧ください

**EnvironmentVariables** (前の例では設定されていません) は、このコード パッケージに対して設定されている環境変数の一覧を提供します。 `ApplicationManifest.xml` で環境変数をオーバーライドして、各種のサービス インスタンスに対して異なる値を指定できます。 

**DataPackage** (前の例では設定されていません) は、実行時にプロセスで消費される任意の静的データを含む **Name** 属性で名前が付けられたフォルダーを宣言します。

**ConfigPackage** は、*Settings.xml* ファイルを含む **Name** 属性を使用して名前が付けられたフォルダーを宣言します。 この設定ファイルには、実行時にプロセスが読み取るユーザー定義のキー値ペアの設定のセクションが含まれています。 アップグレード中に **ConfigPackage** の**バージョン**のみが変更された場合、実行中のプロセスは再起動されません。 代わりに、コールバックは構成設定が変更されたことをプロセスに通知して、動的に再読み込みされるようにします。 次に *Settings.xml* ファイルの一例を示します。

```xml
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSection">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

Service Fabric のサービス **エンドポイント**は、Service Fabric リソースの例です。 Service Fabric リソースは、コンパイルしたコードを変更せずに宣言/変更することができます。 サービス マニフェストで指定した Service Fabric リソースへのアクセスは、**SecurityGroup** を使ってアプリケーション マニフェスト内で制御できます。 サービス マニフェストでエンドポイント リソースが定義されていると、ポートが明示的に指定されていない場合、Service Fabric は予約済みのアプリケーション ポートの範囲からポートを割り当てます。 詳しくは、[エンドポイント リソースの指定またはオーバーライド](service-fabric-service-manifest-resources.md)に関するページをご覧ください。


<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application-in-applicationmanifestxml"></a>ApplicationManifest.xml でアプリケーションを記述する
アプリケーション マニフェストは、宣言によって、アプリケーションの種類とバージョンについて記述します。 安定した名前、パーティション分割スキーム、インスタンス数とレプリケーション係数、セキュリティと分離ポリシー、配置に関する制約、構成のオーバーライド、構成サービスの種類などのサービス構成のメタデータを指定します。 アプリケーションが置かれる負荷分散のドメインについても記述します。

そのため、アプリケーション マニフェストは、アプリケーション レベルで要素を記述し、1 つ以上のサービス マニフェストを参照してアプリケーションの種類を構成します。 [投票サンプル アプリケーション](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart)のアプリケーション マニフェストを次に示します (より詳細な例は[こちら](service-fabric-manifest-examples.md)にいくつかあります)。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="VotingData">
      <StatefulService ServiceTypeName="VotingDataType" TargetReplicaSetSize="[VotingData_TargetReplicaSetSize]" MinReplicaSetSize="[VotingData_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[VotingData_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    <Service Name="VotingWeb" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="VotingWebType" InstanceCount="[VotingWeb_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

サービス マニフェストと同様に、 **Version** 属性は構造化されていない文字列で、システムでは解析されません。 Version 属性は、アップグレード用の各コンポーネントのバージョン管理にも使用されます。

**Parameters** では、アプリケーション マニフェスト全体で使われるパラメーターを定義します。 これらのパラメーターの値は、アプリケーションがインスタンス化されるときに指定でき、アプリケーションまたはサービスの構成設定をオーバーライドできます。  アプリケーションのインスタンス化中に値が変更されない場合は、パラメーターの既定値が使われます。 個々の環境で異なるアプリケーションやサービスのパラメーターを維持する方法については、「 [複数の環境のアプリケーション パラメーターを管理する](service-fabric-manage-multiple-environment-app-configuration.md)」を参照してください。

**ServiceManifestImport** には、このアプリケーションの種類を構成するサービス マニフェストへの参照が含まれています。 アプリケーション マニフェストは、複数のサービス マニフェストのインポートを含むことができ、個別に独立してバージョン管理できます。 インポートされたサービス マニフェストは、このアプリケーション内で有効なサービスの種類を決定します。 ServiceManifestImport 内では、Settings.xml ファイル内の構成値と、ServiceManifest.xml ファイル内の環境変数をオーバーライドします。 エンドポイントのバインディング、セキュリティとアクセス、およびパッケージ共有に関する **Policies** (前の例では設定されていません) は、インポートされるサービス マニフェストで設定できます。  詳しくは、「[アプリケーションのセキュリティ ポリシーの構成](service-fabric-application-runas-security.md)」をご覧ください。

**DefaultServices** は、アプリケーションがこのアプリケーションの種類に対してインスタンス化されるたびに自動的に作成されるサービス インスタンスを宣言します。 既定のサービスは便利で、作成後はすべての面で通常のサービスと同様に動作します。 アプリケーション インスタンスの他のサービスと共にアップグレードされ、削除することもできます。 アプリケーション マニフェストは、複数の既定サービスを含むことができます。

**Certificates** (前の例では設定されていません) では、[HTTPS エンドポイントのセットアップ](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service)または[アプリケーション マニフェストでのシークレットの暗号化](service-fabric-application-secret-management.md)に使われる証明書を宣言します。

**Policies** (前の例では設定されていません) では、サービスが Service Fabric ランタイムにアクセスできるかどうかなど、アプリケーション レベルで設定するログ コレクション、[既定の実行アカウント](service-fabric-application-runas-security.md)、[正常性](service-fabric-health-introduction.md#health-policies)、[セキュリティ アクセス](service-fabric-application-runas-security.md)の各ポリシーを記述します。

> [!NOTE] 
> 既定で、Service Fabric アプリケーションは、アプリケーション固有の要求を受け入れるエンドポイント、および Fabric とアプリケーション固有のファイルを含むホストのファイル パスを指す環境変数の形式で Service Fabric ランタイムにアクセスすることができます。 アプリケーションが信頼できないコード (つまり、出所が不明のコード、または実行することが安全でないことをアプリケーション所有者が認識しているコード) をホストしている場合は、このアクセス権を無効にすることを検討してください。 詳細については、「[Service Fabric でのセキュリティのベスト プラクティス](service-fabric-best-practices-security.md#platform-isolation)」を参照してください。 
>

**Principals** (前の例では設定されていません) では、[サービスの実行およびサービスのリソースのセキュリティ保護](service-fabric-application-runas-security.md)に必要なセキュリティ プリンシパル (ユーザーまたはグループ) を記述します。  プリンシパルは、**Policies** セクションで参照されます。





<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->



## <a name="next-steps"></a>次の手順
- [アプリケーションをパッケージ化](service-fabric-package-apps.md)してデプロイできるようにします。
- [アプリケーションをデプロイおよび削除](service-fabric-deploy-remove-applications.md)します。
- [異なるアプリケーション インスタンスのパラメーターおよび環境変数を構成](service-fabric-manage-multiple-environment-app-configuration.md)します。
- [アプリケーションのセキュリティ ポリシーを構成](service-fabric-application-runas-security.md)します。
- [HTTPS エンドポイントをセットアップ](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service)します。
- [アプリケーション マニフェストでシークレットを暗号化](service-fabric-application-secret-management.md)します

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png



