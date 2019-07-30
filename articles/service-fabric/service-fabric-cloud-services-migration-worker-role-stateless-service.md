---
title: Azure Cloud Services アプリの Service Fabric への変換 | Microsoft Docs
description: このガイドでは、Cloud Services から Service Fabric に移行するときのために、Cloud Services の Web ロールと worker ロールと、Service Fabric のステートレス サービスの比較を説明します。
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 5880ebb3-8b54-4be8-af4b-95a1bc082603
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: e82abd6a7915123a94b4355e24cb94f13f9693c8
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550384"
---
# <a name="guide-to-converting-web-and-worker-roles-to-service-fabric-stateless-services"></a>Web ロールと worker ロールを Service Fabric ステートレス サービスに変換する手順
この記事では、Cloud Services の Web ロールと worker ロールを Service Fabric ステートレス サービスに移行する方法について説明します。 アーキテクチャ全体をほぼ同じまま維持するアプリケーションの場合、これが Cloud Services から Service Fabric への最も単純な移行パスです。

## <a name="cloud-service-project-to-service-fabric-application-project"></a>Cloud Service プロジェクトから Service Fabric アプリケーション プロジェクトへ
 Cloud Service プロジェクトと Service Fabric アプリケーション プロジェクトは構造が似ており、いずれもアプリケーションのデプロイ単位を表しています。つまり、それぞれがアプリケーションを実行するためにデプロイされるパッケージ一式を定義しています。 Cloud Service プロジェクトには、1 つ以上の Web ロールまたは worker ロールが含まれています。 同様に、Service Fabric アプリケーション プロジェクトには、1 つ以上のサービスが含まれています。 

違いは、Cloud Service プロジェクトはアプリケーションのデプロイと VM のデプロイを組み合わせているので、VM 構成が含まれており、Service Fabric アプリケーション プロジェクトは、Service Fabric クラスター内の既存の VM セットにデプロイするアプリケーションのみを定義している点です。 Service Fabric クラスター自体は、Resource Manager テンプレートまたは Azure Portal を介して 1 度だけデプロイされます。複数の Service Fabric アプリケーションをデプロイできます。

![Service Fabric プロジェクトと Cloud Services プロジェクトの比較][3]

## <a name="worker-role-to-stateless-service"></a>worker ロールからステートレス サービスへ
概念上、worker ロールは、ステートレス ワークロードを表しています。つまり、ワークロードのすべてのインスタンスは同じであり、要求はいつでも任意のインスタンスにルーティングできます。 各インスタンスは、前の要求を記憶しません。 ワークロードが処理する状態は、Azure Table Storage、Azure Cosmos DB などの外部の状態ストアによって管理されます。 Service Fabric の場合、この種類のワークロードはステートレス サービスで表されます。 worker ロールを Service Fabric に移行する最も簡単なアプローチは、worker ロール コードをステートレス サービスに変換する方法です。

![worker ロールからステートレス サービスへ][4]

## <a name="web-role-to-stateless-service"></a>Web ロールからステートレス サービスへ
worker ロールと同様に、Web ロールもステートレス ワークロードを表すため、概念上、Service Fabric ステートレス サービスにマッピングできます。 ただし、Web ロールとは異なり、Service Fabric は IIS をサポートしていません。 Web ロールの Web アプリケーションをステートレス サービスに移行するには、まず自己ホスト型で、IIS や System.Web に依存しない Web フレームワーク (ASP.NET Core 1 など) に移行する必要があります。

| **Application** | **サポートされています** | **移行パス** |
| --- | --- | --- |
| ASP.NET Web Forms |いいえ |ASP.NET Core 1 MVC への変換 |
| ASP.NET MVC |移行あり |ASP.NET Core 1 MVC にアップグレードする |
| ASP.NET Web API |移行あり |自己ホスト型サーバーまたは ASP.NET Core 1 を使用する |
| ASP.NET Core 1 |はい |該当なし |

## <a name="entry-point-api-and-lifecycle"></a>エントリ ポイントの API とライフサイクル
worker ロールと Service Fabric サービス API には、同様のエントリ ポイントが用意されています。 

| **エントリ ポイント** | **worker ロール** | **Service Fabric サービス** |
| --- | --- | --- |
| 処理中 |`Run()` |`RunAsync()` |
| VM の開始 |`OnStart()` |該当なし |
| VM の停止 |`OnStop()` |該当なし |
| クライアント要求に対するリスナーを開く |該当なし |<ul><li> ステートレスの場合、次のようになります。`CreateServiceInstanceListener()`</li><li>ステートフルの場合、次のようになります。`CreateServiceReplicaListener()`</li></ul> |

### <a name="worker-role"></a>worker ロール
```csharp

using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
        }

        public override bool OnStart()
        {
        }

        public override void OnStop()
        {
        }
    }
}

```

### <a name="service-fabric-stateless-service"></a>Service Fabric のステートレス サービス
```csharp

using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace Stateless1
{
    public class Stateless1 : StatelessService
    {
        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
        }

        protected override Task RunAsync(CancellationToken cancelServiceInstance)
        {
        }
    }
}

```

いずれにも、処理を開始するプライマリ "Run" のオーバーライドがあります。 Service Fabric サービスは、`Run`、`Start`、`Stop` を 1 つのエントリ ポイント `RunAsync` に結合します。 `RunAsync` が開始されるとサービスが開始され、`RunAsync` メソッドの CancellationToken が発信されるとサービスは停止されます。 

worker ロールと Service Fabric サービスのライフサイクルと有効期間には、主な違いがいくつかあります。

* **ライフサイクル:** 最も大きな違いは、worker ロールが VM であるため、そのライフサイクルが VM に関連付けられている点です。そのため、VM の開始時と停止時のイベントが含まれます。 Service Fabric サービスには、VM のライフサイクルとは別のライフサイクルがあります。関連性がないため、そのため、ホスト VM またはコンピューターの開始時と停止時のイベントは含まれません。
* **有効期間:** worker ロール インスタンスは、`Run` メソッドの終了時にリサイクルされます。 一方、Service Fabric サービスの `RunAsync` メソッドは、完了まで実行を継続できるので、サービス インスタンスは有効なままになります。 

Service Fabric には、クライアント要求をリッスンするサービス向けに、オプションの通信セットアップ エントリ ポイントがあります。 RunAsync と通信エントリ ポイントは、いずれも Service Fabric サービスのオプションのオーバーライドです。クライアント要求のリッスンのみを行うか、処理ループのみを実行するか、またはその両方かをサービスで選択できます。クライアント要求のリッスンを継続できるので、サービス インスタンスを再開することなく RunAsync メソッドを終了できます。

## <a name="application-api-and-environment"></a>アプリケーション API と環境
Cloud Services 環境 API は、現在の VM インスタンスに関する情報と機能だけでなく、他の VM ロール インスタンスに関する情報も提供します。 Service Fabric は、ランタイムに関連する情報や、サービスが現在実行されているノードに関する情報の一部を提供します。 

| **環境タスク** | **Cloud Services** | **Service Fabric** |
| --- | --- | --- |
| 構成設定と変更通知 |`RoleEnvironment` |`CodePackageActivationContext` |
| ローカル ストレージ |`RoleEnvironment` |`CodePackageActivationContext` |
| エンドポイント情報 |`RoleInstance` <ul><li>現在のインスタンスの場合、次のようになります。`RoleEnvironment.CurrentRoleInstance`</li><li>その他のロールとインスタンスの場合、次のようになります。`RoleEnvironment.Roles`</li> |<ul><li>現在のノード アドレスの場合、次のようになります。`NodeContext`</li><li>サービス エンドポイントの検出の場合、次のようになります。`FabricClient` および `ServicePartitionResolver`</li> |
| 環境のエミュレーション |`RoleEnvironment.IsEmulated` |該当なし |
| 同時変更イベント |`RoleEnvironment` |該当なし |

## <a name="configuration-settings"></a>構成設定
VM ロールの構成設定が Cloud Services に設定され、その VM ロールのすべてのインスタンスに適用されます。 この構成設定は、ServiceConfiguration.*.cscfg ファイルに設定されたキーと値のペアです。RoleEnvironment を介して直接アクセスできます。 Service Fabric では、設定は VM ではなく、個別に各サービスと各アプリケーションに適用されます。これは、VM が複数のサービスとアプリケーションをホストできるためです。 サービスは 3 つのパッケージから構成されます。

* **Code:** サービスの実行可能ファイル、バイナリ、DLL など、サービスの実行に必要なファイルが含まれています。
* **Config:** サービスのすべての構成ファイルと設定。
* **Data:** サービスに関連する静的なデータ ファイル。

これらの各パッケージは、独立してバージョンを管理し、アップグレードすることができます。 Cloud Services と同様に、config パッケージには、プログラムで API を介してアクセスすることができます。また、イベントを使用して、サービスに config パッケージの変更を通知することができます。 Settings.xml ファイルは、App.config ファイルのアプリ設定セクションと同様に、キーと値の構成とプログラムによるアクセスに使用できます。 ただし、Cloud Services とは異なり、Service Fabric の config パッケージには、XML、JSON、YAML、カスタム バイナリ形式など、任意の形式の任意の構成ファイルを含めることができます。 

### <a name="accessing-configuration"></a>構成にアクセスする
#### <a name="cloud-services"></a>Cloud Services
ServiceConfiguration.*.cscfg の構成設定には、 `RoleEnvironment`を介してアクセスできます。 この構成設定は、同じ Cloud Service デプロイ内のすべてのロール インスタンスでグローバルに使用できます。

```csharp

string value = RoleEnvironment.GetConfigurationSettingValue("Key");

```

#### <a name="service-fabric"></a>Service Fabric
各サービスには、個別の構成パッケージがあります。 クラスター内のすべてのアプリケーションからアクセスできるグローバル構成設定に適した組み込みのメカニズムはありません。 構成パッケージ内にある Service Fabric の特別な Settings.xml 構成ファイルを使用すると、Settings.xml の値はアプリケーション レベルで上書きできるので、アプリケーションレベルの構成設定が可能です。

構成設定には、サービスの `CodePackageActivationContext`を介して各サービス インスタンス内でアクセスします。

```csharp

ConfigurationPackage configPackage = this.Context.CodePackageActivationContext.GetConfigurationPackageObject("Config");

// Access Settings.xml
KeyedCollection<string, ConfigurationProperty> parameters = configPackage.Settings.Sections["MyConfigSection"].Parameters;

string value = parameters["Key"]?.Value;

// Access custom configuration file:
using (StreamReader reader = new StreamReader(Path.Combine(configPackage.Path, "CustomConfig.json")))
{
    MySettings settings = JsonConvert.DeserializeObject<MySettings>(reader.ReadToEnd());
}

```

### <a name="configuration-update-events"></a>構成の更新イベント
#### <a name="cloud-services"></a>Cloud Services
`RoleEnvironment.Changed` イベントは、環境内で変化 (構成の変更など) が発生したときに、すべてのロール インスタンスに通知するために使用されます。 また、ロール インスタンスのリサイクルや worker プロセスの再起動を伴うことなく、構成の更新を利用するために使われます。

```csharp

RoleEnvironment.Changed += RoleEnvironmentChanged;

private void RoleEnvironmentChanged(object sender, RoleEnvironmentChangedEventArgs e)
{
   // Get the list of configuration changes
   var settingChanges = e.Changes.OfType<RoleEnvironmentConfigurationSettingChange>();
foreach (var settingChange in settingChanges) 
   {
      Trace.WriteLine("Setting: " + settingChange.ConfigurationSettingName, "Information");
   }
}

```

#### <a name="service-fabric"></a>Service Fabric
Code、Config、Data というサービス内の 3 つの各パッケージには、パッケージの更新時、追加時、または削除時にサービス インスタンスを通知するイベントがあります。 1 つのサービスには、各パッケージを複数含めることができます。 たとえば、1 つのサービスに複数の config パッケージを含めて、個別にバージョンを管理し、アップグレードすることができます。 

これらのイベントを使用すると、サービス インスタンスを再起動することなく、サービス パッケージの変更を利用することができます。

```csharp

this.Context.CodePackageActivationContext.ConfigurationPackageModifiedEvent +=
                    this.CodePackageActivationContext_ConfigurationPackageModifiedEvent;

private void CodePackageActivationContext_ConfigurationPackageModifiedEvent(object sender, PackageModifiedEventArgs<ConfigurationPackage> e)
{
    this.UpdateCustomConfig(e.NewPackage.Path);
    this.UpdateSettings(e.NewPackage.Settings);
}

```

## <a name="startup-tasks"></a>スタートアップ タスク
スタートアップ タスクは、アプリケーションの起動前に実行されるアクションです。 通常、スタートアップ タスクは、昇格された特権を使用してセットアップ スクリプトを実行するために使用されます。 Cloud Services と Service Fabric のいずれもスタートアップ タスクをサポートしています。 主な違いは、Cloud Services の場合、ロール インスタンスの一部なので、スタートアップ タスクは VM に関連付けられている点です。一方、Service Fabric では、スタートアップ タスクはサービスに関連付けられており、特定の VM には関連付けられていません。

| Service Fabric | Cloud Services |
| --- | --- |
| 構成の場所 |ServiceDefinition.csdef |
| 特権 |"制限付き" または "昇格された" 特権 |
| シーケンス処理 |"簡易"、"バックグラウンド"、"フォアグラウンド" |

### <a name="cloud-services"></a>Cloud Services
Cloud Services のスタートアップ エントリ ポイントは、ServiceDefinition.csdef でロールごとに構成されています。 

```xml

<ServiceDefinition>
    <Startup>
        <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
            <Environment>
                <Variable name="MyVersionNumber" value="1.0.0.0" />
            </Environment>
        </Task>
    </Startup>
    ...
</ServiceDefinition>

```

### <a name="service-fabric"></a>Service Fabric
Service Fabric のスタートアップ エントリ ポイントは、ServiceManifest.xml でサービスごとに構成されています。

```xml

<ServiceManifest>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>Startup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    ...
</ServiceManifest>

``` 

## <a name="a-note-about-development-environment"></a>開発環境に関する注意事項
Cloud Services と Service Fabric は、いずれもプロジェクト テンプレートで Visual Studio と統合され、デバッグ、構成、ローカルと Azure 両方へのデプロイがサポートされています。 また、Cloud Services と Service Fabric のいずれにも、ローカル開発ランタイム環境が用意されています。 違いは、Cloud Services 開発ランタイムは、実行されている Azure 環境をエミュレートしますが、Service Fabric は、エミュレーターを使用しない点です。Service Fabric は、完全な Service Fabric ランタイムを使用します。 ローカル開発コンピューターで実行している Service Fabric 環境は、運用時に実行する環境と同じです。

## <a name="next-steps"></a>次の手順
Service Fabric の全機能を活用できるように、Service Fabric の Reliable Services の詳細と、Cloud Services と Service Fabric アプリケーション アーキテクチャの違いについて説明します。

* [Service Fabric の Reliable Services の概要](service-fabric-reliable-services-quick-start.md)
* [Cloud Services と Service Fabric の違いに関する概念ガイド](service-fabric-cloud-services-migration-differences.md)

<!--Image references-->
[3]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/service-fabric-cloud-service-projects.png
[4]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/worker-role-to-stateless-service.png
