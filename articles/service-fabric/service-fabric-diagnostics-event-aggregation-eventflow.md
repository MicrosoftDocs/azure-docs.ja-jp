---
title: EventFlow を使用した Azure Service Fabric のイベントの集計
description: Azure Service Fabric クラスターの監視と診断に EventFlow を使用したイベントの集計と収集について説明します。
author: srrengar
ms.topic: conceptual
ms.date: 2/25/2019
ms.author: srrengar
ms.custom: devx-track-csharp
ms.openlocfilehash: a80eea5a50aa7b1e441049eeb2cae381994cd3ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96006338"
---
# <a name="event-aggregation-and-collection-using-eventflow"></a>EventFlow を使用したイベントの集計と収集

[Microsoft Diagnostics EventFlow](https://github.com/Azure/diagnostics-eventflow) では、ノードから 1 つ以上の監視対象にイベントをルーティングすることができます。 EventFlow は NuGet パッケージとしてサービス プロジェクトに含まれるため、EventFlow のコードと構成はサービスと共に移動します。これにより、Azure Diagnostics で説明したノードごとの構成の問題が排除されます。 EventFlow はサービス プロセス内で実行され、構成済みの出力に直接接続されます。 この直接接続により、EventFlow は Azure、コンテナー、オンプレミスの各サービス デプロイで機能します。 各 EventFlow パイプラインは外部接続を行うため、コンテナーなどの高密度シナリオで EventFlow を実行する場合は注意してください。 そのため、複数のプロセスをホストしている場合、いくつかの送信接続が発生することになります。 Service Fabric アプリケーションでは、`ServiceType` のすべてのレプリカが同じプロセス内で実行され、送信接続の数が限られるため、これはそれほど問題にはなりません。 また、EventFlow ではイベントのフィルター処理も行われるので、指定したフィルターと一致するイベントだけが送信されます。

## <a name="set-up-eventflow"></a>EventFlow の設定

EventFlow バイナリは、一連の NuGet パッケージとして入手できます。 Service Fabric サービス プロジェクトに EventFlow を追加するには、ソリューション エクスプローラーでプロジェクトを右クリックし、[NuGet パッケージの管理] を選びます。 [参照] タブに切り替え、"`Diagnostics.EventFlow`" を検索します。

![Visual Studio の NuGet パッケージ マネージャー UI での EventFlow NuGet パッケージ](./media/service-fabric-diagnostics-event-aggregation-eventflow/eventflow-nuget.png)

"Inputs" と "Outputs" のラベルがついた、さまざまなパッケージの一覧が表示されます。 EventFlow は、非常に多様なログ プロバイダーやアナライザーをサポートしています。 EventFlow をホストするサービスには、アプリケーション ログの送信元と送信先に応じた適切なパッケージが含まれる必要があります。 コア ServiceFabric パッケージだけでなく、少なくとも 1 つの構成された入力と出力も必要となります。 たとえば、次のパッケージを追加して、EventSource イベントを Application Insights に送信できます。

* `Microsoft.Diagnostics.EventFlow.Inputs.EventSource` (サービスの EventSource クラス、および *Microsoft-ServiceFabric-Services* や *Microsoft-ServiceFabric-Actors* などの標準 EventSource からデータをキャプチャするため)
* `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights` (ここでは、Azure Application Insights リソースにログを送信します)
* `Microsoft.Diagnostics.EventFlow.ServiceFabric` (Service Fabric サービス構成からの EventFlow パイプラインの初期化を可能にし、診断データの送信に関する問題を Service Fabric 正常性レポートとして報告します)

>[!NOTE]
>`Microsoft.Diagnostics.EventFlow.Inputs.EventSource` パッケージでは、サービス プロジェクトが .NET Framework 4.6 以降を対象とする必要があります。 このパッケージをインストールする前に、プロジェクトのプロパティで適切な対象フレームワークが設定されていることを確認します。

すべてのパッケージをインストールした後は、サービスで EventFlow を構成して有効にします。

## <a name="configure-and-enable-log-collection"></a>ログの収集の構成と有効化
ログの送信を行う EventFlow パイプラインは、構成ファイルに格納されている仕様から作成されます。 `Microsoft.Diagnostics.EventFlow.ServiceFabric` パッケージは、初期の EventFlow 構成ファイルを `eventFlowConfig.json` という名前の `PackageRoot\Config` ソリューション フォルダーにインストールします。 サービスの既定の `EventSource` クラスおよび構成する任意の他の入力からデータをキャプチャして適切な場所にデータを送信するように、この構成ファイルを変更する必要があります。

>[!NOTE]
>プロジェクト ファイルで Visual Studio 2017 形式を使用している場合、`eventFlowConfig.json` ファイルは自動的に追加されません。 これを修正するには、そのファイルを `Config` フォルダーに作成し、ビルド アクションを `Copy if newer` に設定します。 

上記で説明した NuGet パッケージに基づくサンプル *eventFlowConfig.json* です。
```json
{
  "inputs": [
    {
      "type": "EventSource",
      "sources": [
        { "providerName": "Microsoft-ServiceFabric-Services" },
        { "providerName": "Microsoft-ServiceFabric-Actors" },
        // (replace the following value with your service's ServiceEventSource name)
        { "providerName": "your-service-EventSource-name" }
      ]
    }
  ],
  "filters": [
    {
      "type": "drop",
      "include": "Level == Verbose"
    }
  ],
  "outputs": [
    {
      "type": "ApplicationInsights",
      // (replace the following value with your AI resource's instrumentation key)
      "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
  ],
  "schemaVersion": "2016-08-11"
}
```

サービスの ServiceEventSource の名前は、ServiceEventSource クラスに適用される `EventSourceAttribute` の Name プロパティの値です。 すべては `ServiceEventSource.cs` ファイルで指定されており、サービス コードの一部です。 たとえば、次のコード スニペットでは、ServiceEventSource の名前は *MyCompany-Application1-Stateless1* です。

```csharp
[EventSource(Name = "MyCompany-Application1-Stateless1")]
internal sealed class ServiceEventSource : EventSource
{
    // (rest of ServiceEventSource implementation)
}
```

`eventFlowConfig.json` ファイルはサービス構成パッケージの一部であることに注意してください。 このファイルに対する変更は、サービスの完全なアップグレードまたは構成のみのアップグレードに含めることができ、Service Fabric アップグレードの正常性チェックの対象にでき、アップグレードが失敗した場合は自動的にロールバックできます。 詳しくは、「[Service Fabric アプリケーションのアップグレード](service-fabric-application-upgrade.md)」をご覧ください。

構成の *フィルター* セクションでは、EventFlow パイプラインを通って出力される情報をさらにカスタマイズして、特定の情報を含めたり、または除外したり、イベント データの構造を変更したりできます。 フィルタリングの詳細については、[EventFlow フィルター](https://github.com/Azure/diagnostics-eventflow#filters)に関するページをご覧ください。

最後に、`Program.cs` ファイル内にあるサービスのスタートアップ コードで、EventFlow パイプラインをインスタンス化します。

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric;
using Microsoft.ServiceFabric.Services.Runtime;

// **** EventFlow namespace
using Microsoft.Diagnostics.EventFlow.ServiceFabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>
        private static void Main()
        {
            try
            {
                // **** Instantiate log collection via EventFlow
                using (var diagnosticsPipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MyApplication-MyService-DiagnosticsPipeline"))
                {

                    ServiceRuntime.RegisterServiceAsync("Stateless1Type",
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                    Thread.Sleep(Timeout.Infinite);
                }
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
                throw;
            }
        }
    }
}
```

`ServiceFabricDiagnosticsPipelineFactory` の `CreatePipeline` メソッドのパラメーターとして渡される名前は、EventFlow ログ収集パイプラインを表す "*正常性エンティティ*" の名前です。 この名前は、EventFlow でエラーが発生し、Service Fabric 正常性サブシステムでそれが報告される場合に使われます。

### <a name="use-service-fabric-settings-and-application-parameters-in-eventflowconfig"></a>eventFlowConfig で Service Fabric の設定とアプリケーション パラメーターを使用する

EventFlow では、EventFlow 設定を構成する Service Fabric の設定とアプリケーションのパラメーターの使用がサポートされます。 以下の特殊な構文を値にして Service Fabric の設定パラメーターを参照できます。

```json
servicefabric:/<section-name>/<setting-name>
```

`<section-name>` は、Service Fabric の構成セクションの名前を示し、`<setting-name>` は、EventFlow の設定の構成に使用する値を提供する構成設定です。 その設定方法についての詳細は、「[Support for Service Fabric settings and application parameters (Service Fabric の設定とアプリケーション パラメーターのサポート)](https://github.com/Azure/diagnostics-eventflow#support-for-service-fabric-settings-and-application-parameters)」をご覧ください。

## <a name="verification"></a>検証

サービスを開始し、Visual Studio の [デバッグ] 出力ウィンドウを観察します。 サービスを開始すると、構成した出力に対して、サービスがレコードを送信している証拠が表示され始めるはずです。 イベント分析と視覚化プラットフォームに移動し、ログ表示が開始したことを確認します (数分かかる場合があります)。

## <a name="next-steps"></a>次のステップ

* [Application Insights を使用したイベントの分析と視覚化](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Azure Monitor ログを使用したイベントの分析と視覚化](service-fabric-diagnostics-event-analysis-oms.md)
* [EventFlow のドキュメント](https://github.com/Azure/diagnostics-eventflow)
