---
title: "Azure Service Fabric アプリケーション モデル | Microsoft Docs"
description: "Service Fabric のアプリケーションとサービスをモデル化し、記述する方法。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: mani-ramaswamy
ms.assetid: 17a99380-5ed8-4ed9-b884-e9b827431b02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/02/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 87db655d246dad90bf0afbc91ec507b0a86d90eb
ms.lasthandoff: 03/27/2017


---
# <a name="model-an-application-in-service-fabric"></a>Service Fabric でのアプリケーションのモデル化
ここでは、Azure Service Fabric のアプリケーション モデルの概要と、マニフェスト ファイルを使用してアプリケーションとサービスを定義する方法について説明します。

## <a name="understand-the-application-model"></a>アプリケーション モデルを理解する
アプリケーションは、特定のまたは複数の関数を実行する構成サービスのコレクションです。 サービスは完全なスタンドアロンの機能を実行し (他のサービスから独立して開始、実行できる)、コード、構成、データで構成されます。 各サービスに対して、コードは実行可能ファイルのバイナリで構成され、構成は実行時に読み込まれるサービス設定で構成され、データはサービスが消費する任意の静的データで構成されます。 この階層的なアプリケーション モデル内の各コンポーネントは、個別にバージョン管理されてアップグレードされます。

![Service Fabric のアプリケーション モデル][appmodel-diagram]

アプリケーションの種類は、サービスの種類の集まりで構成されているアプリケーションの分類です。 サービスの種類は、サービスを分類したものです。 分類にはさまざまな設定と構成を含めることができますが、コア機能は変わりません。 サービスのインスタンスは、同じサービスの種類の別のサービス構成のバリエーションです。  

アプリケーションのクラス (または "種類") とサービスは、アプリケーションをクラスターのイメージ ストアからインスタンス化する対象となるテンプレートである XML ファイル (アプリケーション マニフェストとサービス マニフェスト) を使用して記述されます。 ServiceManifest.xml と ApplicationManifest.xml ファイルのスキーマ定義は、Service Fabric SDK およびツールと共に *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd* にインストールされます。

別のアプリケーション インスタンスのコードは、同じ Service Fabric ノードでホストされている場合でも個別のプロセスとして実行されます。 さらに、各アプリケーション インスタンスのライフサイクルを個別に管理できます (つまり アップグレード)。 次の図では、コード、構成、パッケージで構成されるサービスの種類で、アプリケーションの種類がどのように構成されるかを示しています。 図を簡単にするために、 `ServiceType4` のコード/構成/データ パッケージのみが表示されていますが、各サービス タイプにはそのようなパッケージ タイプの一部または全部が含まれます。

![Service Fabric アプリケーションの種類とサービスの種類][cluster-imagestore-apptypes]

2 つの異なるマニフェスト ファイル (サービス マニフェストとアプリケーション マニフェスト) はアプリケーションとサービスの記述に使用されます。 サービス マニフェストとアプリケーション マニフェストについては次のセクションで詳しく説明します。

クラスターにはアクティブな 1 つ以上のサービスの種類のインスタンスがある可能性があります。 たとえば、ステートフル サービス インスタンスやレプリカの場合、クラスター内の別のノード上にあるレプリカ間で状態をレプリケートすることで高い信頼性を実現します。 レプリカは基本的に、クラスター内の 1 つのノードが失敗した場合でも使用できるように、サービスの冗長性を実現します。 [パーティション分割されたサービス](service-fabric-concepts-partitioning.md) は、クラスター内のノード間でその状態 (状態へのアクセス パターンも) をさらに分割します。

次の図は、アプリケーションとサービス インスタンス、パーティション、レプリカ間のリレーションシップを示しています。

![サービス内のパーティションとレプリカ][cluster-application-instances]

> [!TIP]
> http://&lt;yourclusteraddress&gt;:19080/Explorer で利用できる Service Fabric Explorer ツールを利用し、クラスターのアプリケーションのレイアウトを表示できます。 詳細については、「 [Service Fabric Explorer を使用したクラスターの視覚化](service-fabric-visualizing-your-cluster.md)」を参照してください。
> 
> 

## <a name="describe-a-service"></a>サービスを記述する
サービス マニフェストは、宣言によって、サービスの種類とバージョンを定義します。 サービスの種類、ヘルスのプロパティ、負荷分散のメトリック、サービスのバイナリ、および構成ファイルなどのサービス メタデータを指定します。  別の言い方をすれば、1 つ以上のサービスの種類をサポートするよう、サービス パッケージを構成するコード、構成、データのパッケージを記述します。 次に、サービス マニフェストの単純な例を示します。

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="MyCode" Version="CodeVersion1">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
    <EnvironmentVariables>
      <EnvironmentVariable Name="MyEnvVariable" Value=""/>
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
    </EnvironmentVariables>
  </CodePackage>
  <ConfigPackage Name="MyConfig" Version="ConfigVersion1" />
  <DataPackage Name="MyData" Version="DataVersion1" />
</ServiceManifest>
```

**Version** 属性は構造化されていない文字列で、システムでは解析されません。 これらは、アップグレード用の各コンポーネントのバージョン管理に使用されます。

**ServiceTypes** は、このマニフェストで **CodePackages** でサポートされているサービスの種類を宣言します。 これらのサービスの種類のいずれかに対してサービスがインスタンス化されると、このマニフェストで宣言されているすべてのコード パッケージは、エントリ ポイントを実行してアクティブ化されます。 実行時に、サポートされているサービスの種類を登録するために、結果のプロセスが必要になります。 サービスの種類は、コード パッケージ レベルではなく、マニフェスト レベルで宣言されることにご注意ください。 したがって、複数のコード パッケージがあるとき、システムが宣言されたサービスの種類のいずれかを検索するときは常に、すべてアクティブ化されます。

**SetupEntryPoint** は、他のエントリポイントの前に、Service Fabric と同じ資格情報で実行する特権を持つエントリ ポイントです (通常は *LocalSystem* アカウント)。 **EntryPoint** によって指定された実行可能ファイルは通常は実行時間の長いサービス ホストです。 別々にセットアップされたエントリ ポイントの存在により、長期にわたって高い権限でサービス ホストを実行する必要がなくなります。 **EntryPoint** で指定された実行可能ファイルは、**SetupEntryPoint** が正常に終了した後に実行されます。 結果のプロセスは、終了またはクラッシュした場合に、監視されて再起動されます ( **SetupEntryPoint**で再起動)。 

**SetupEntryPoint** を使用する際の一般的なシナリオは、サービス開始前に実行可能ファイルを実行する必要がある場合や、昇格した特権で操作を実行する必要がある場合になります。 次に例を示します。

* サービス実行可能ファイルが使用する可能性がある環境変数の設定と初期化などです。 これは、Service Fabric のプログラミング モデルによって記述されの実行可能ファイルだけに限定されません。 たとえば、npm.exe は node.js アプリケーションのデプロイに構成されているいくつかの環境変数が必要です。
* セキュリティ証明書のインストールによるアクセス制御の設定

**SetupEntryPoint** の構成方法について詳しくは、「[エントリ ポイント セットアップ サービスのポリシーを構成する](service-fabric-application-runas-security.md)」をご覧ください

**EnvironmentVariables** は、このコード パッケージに対して設定されている環境変数の一覧を提供します。 `ApplicationManifest.xml` でこれらをオーバーライドすれば、各種のサービス インスタンスに対して異なる値を指定できます。 

**DataPackage** は、実行時にプロセスで消費される任意の静的データを含む **Name** 属性を使用して名前が付けられたフォルダーを宣言します。

**ConfigPackage** は、*Settings.xml* ファイルを含む **Name** 属性を使用して名前が付けられたフォルダーを宣言します。 このファイルには、実行時にプロセスが読み取ることができるユーザー定義のキー値ペアの設定のセクションが含まれています。 アップグレード中に **ConfigPackage** の**バージョン**のみが変更された場合、実行中のプロセスは再起動されません。 代わりに、コールバックは構成設定が変更されたことをプロセスに通知して、動的に再読み込みされるようにします。 次に *Settings.xml* ファイルの一例を示します。

```xml
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSection">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

> [!NOTE]
> サービス マニフェストには、複数のコード、構成、データのパッケージを含めることができます。 それぞれを個別にバージョン管理できます。
> 
> 

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Resources
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application"></a>アプリケーションを記述する
アプリケーション マニフェストは、宣言によって、アプリケーションの種類とバージョンについて記述します。 安定した名前、パーティション分割スキーム、インスタンス数とレプリケーション係数、セキュリティと分離ポリシー、配置に関する制約、構成の上書き、構成サービスの種類などのサービス構成のメタデータを指定します。 アプリケーションが置かれる負荷分散のドメインについても記述します。

そのため、アプリケーション マニフェストは、アプリケーション レベルで要素を記述し、1 つ以上のサービス マニフェストを参照してアプリケーションの種類を構成します。 次に、アプリケーション マニフェストの単純な例を示します。

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ApplicationManifest
      ApplicationTypeName="MyApplicationType"
      ApplicationTypeVersion="AppManifestVersion1"
      xmlns="http://schemas.microsoft.com/2011/01/fabric"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example application manifest</Description>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyServiceManifest" ServiceManifestVersion="SvcManifestVersion1"/>
    <ConfigOverrides/>
    <EnvironmentOverrides CodePackageRef="MyCode"/>
  </ServiceManifestImport>
  <DefaultServices>
     <Service Name="MyService">
         <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
             <SingletonPartition/>
         </StatelessService>
     </Service>
  </DefaultServices>
</ApplicationManifest>
```

サービス マニフェストと同様に、 **Version** 属性は構造化されていない文字列で、システムでは解析されません。 これらは、アップグレード用の各コンポーネントのバージョン管理にも使用されます。

**ServiceManifestImport** には、このアプリケーションの種類を構成するサービス マニフェストへの参照が含まれています。 インポートされたサービス マニフェストは、このアプリケーション内で有効なサービスの種類を決定します。 ServiceManifestImport 内では、Settings.xml ファイル内の構成値と、ServiceManifest.xml ファイル内の環境変数をオーバーライドできます。 


**DefaultServices** は、アプリケーションがこのアプリケーションの種類に対してインスタンス化されるたびに自動的に作成されるサービス インスタンスを宣言します。 既定のサービスは便利で、作成後はすべての面で通常のサービスと同様に動作します。 アプリケーション インスタンスの他のサービスと共にアップグレードされ、削除することもできます。

> [!NOTE]
> アプリケーション マニフェストには、複数のサービス マニフェストのインポートと既定のサービスを含めることができます。 各サービス マニフェストのインポートは、個別にバージョン管理できます。
> 
> 

個々の環境で異なるアプリケーションやサービスのパラメーターを維持する方法については、「 [複数の環境のアプリケーション パラメーターを管理する](service-fabric-manage-multiple-environment-app-configuration.md)」を参照してください。

<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Application parameters
*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->



## <a name="next-steps"></a>次のステップ
[アプリケーションをパッケージ化](service-fabric-package-apps.md)して展開できるようにします。

[アプリケーションのデプロイと削除][10]に関するページでは、PowerShell を使用してアプリケーション インスタンスを管理する方法について説明しています。

「[複数の環境のアプリケーション パラメーターの管理][11]」では、複数のアプリケーション インスタンスに対してパラメーターと環境変数を構成する方法について説明しています。

「[アプリケーションのセキュリティ ポリシーの構成][12]」では、セキュリティ ポリシーの下でサービスを実行して、アクセスを制限する方法について説明しています。

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md

