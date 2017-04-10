---
title: "Azure Service Fabric サービス プロセスからログを直接収集する | Microsoft Azure"
description: "Service Fabric アプリケーションは Azure 診断エージェントに頼ることなく Azure Application Insights や Elasticsearch などの中央の場所に直接ログを送信できることを説明します。"
services: service-fabric
documentationcenter: .net
author: karolz-ms
manager: rwike77
editor: 
ms.assetid: ab92c99b-1edd-4677-8c28-4e591d909b47
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/18/2017
ms.author: karolz@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: d7f7b157d8d6fb54259c8f23d5005509f4eb7872
ms.openlocfilehash: 22acb6afbfbfff753e71b5e821385798cc76ffdd


---
# <a name="collect-logs-directly-from-an-azure-service-fabric-service-process"></a>Azure Service Fabric サービス プロセスからログを直接収集する
## <a name="in-process-log-collection"></a>プロセス内ログ収集
[Azure 診断拡張機能](service-fabric-diagnostics-how-to-setup-wad.md)を使ってアプリケーション ログを収集するのは、ログの送信元と送信先のセットが小さく、あまり変化せず、送信元と送信先の間に単純なマッピングがある場合に、**Azure Service Fabric** サービスに適したオプションです。 それ以外の場合は、代わりに、サービスで中央の場所に直接ログを送信します。 このプロセスは**プロセス内ログ収集**と呼ばれ、いくつかの潜在的な利点があります。

* *構成とデプロイメントが容易*

    * 診断データ収集の構成は、サービス構成のごく一部に過ぎません。 アプリケーションのその他の部分と常に "同期を保つ" ことは簡単です。
    * アプリケーションごと、またはサービスごとの構成を簡単に実現できます。
        * エージェントに基づくログ収集には、通常、診断エージェントを別にデプロイして構成する必要があります。そのため、管理者の作業とエラーの潜在的な原因が増えます。 多くの場合、仮想マシン (ノード) ごとに許可されるエージェントのインスタンスは&1; つだけであり、エージェントの構成はそのノードで実行されているすべてのアプリケーションとサービスの間で共有されます。 

* *柔軟性*
   
    * 対象データ ストレージ システムをサポートするクライアント ライブラリがある場合、必要なときにいつでもアプリケーションからデータを送信できます。 必要に応じて、新しい送信先を追加できます。
    * 複雑なキャプチャ、フィルター処理、データ集計ルールを実装できます。
    * エージェントに基づくログ収集は、多くの場合、エージェントがサポートするデータ シンクによって制限されます。 一部のエージェントは拡張可能です。

* *内部アプリケーション データおよびコンテキストへのアクセス*
   
    * アプリケーション/サービス プロセス内で実行される診断サブシステムは、コンテキスト情報を使用してトレースを簡単に強化できます。
    * エージェントに基づくログ収集では、Event Tracing for Windows などのなんらかのプロセス内通信メカニズムを使ってデータをエージェントに送信する必要があります。 このしくみの結果、制限が増える可能性があります。

両方の収集方法を組み合わせて長所を活かすこともできます。 実際、それが多くのアプリケーションに最適なソリューションです。 エージェントに基づく収集は、クラスター全体および個々のクラスター ノードに関連するログの収集に適したソリューションです。 サービスの起動時の問題とクラッシュを診断するのに、プロセス内ログ収集よりはるかに信頼性の高い方法です。 また、Service Fabric クラスター内で多くのサービスが実行されている場合、各サービスが個別にプロセス内ログ収集を行うと、クラスターからの発信接続が膨大になります。 多数の発信接続は、ネットワーク サブシステムとログ送信先の両方にとって大きな負荷になります。 [**Azure 診断**](../cloud-services/cloud-services-dotnet-diagnostics.md)などのエージェントは、複数のサービスからデータを収集し、少数の接続を通してすべてのデータを送信できるので、スループットが向上します。 

この記事では、[**EventFlow オープン ソース ライブラリ**](https://github.com/Azure/diagnostics-eventflow)を使ってプロセス内ログ収集を設定する方法について説明します。 他のライブラリを同じ目的に使うこともできますが、EventFlow には、プロセス内ログ収集専用に設計されていて、Service Fabric サービスをサポートするという利点があります。 ログの送信先としては、[**Azure Application Insights**](https://azure.microsoft.com/services/application-insights/) を使います。 [**Event Hubs**](https://azure.microsoft.com/services/event-hubs/) や [**Elasticsearch**](https://www.elastic.co/products/elasticsearch) などの他の送信先もサポートされています。 これは、適切な NuGet パッケージをインストールし、EventFlow 構成ファイルで送信先を構成するだけの問題です。 Application Insights 以外のログ送信先について詳しくは、[EventFlow のドキュメント](https://github.com/Azure/diagnostics-eventflow)をご覧ください。

## <a name="adding-eventflow-library-to-a-service-fabric-service-project"></a>Service Fabric サービス プロジェクトに EventFlow ライブラリを追加する
EventFlow バイナリは、一連の NuGet パッケージとして入手できます。 Service Fabric サービス プロジェクトに EventFlow を追加するには、ソリューション エクスプローラーでプロジェクトを右クリックし、[NuGet パッケージの管理] を選びます。 [参照] タブに切り替え、"`Diagnostics.EventFlow`" を検索します。

![Visual Studio の NuGet パッケージ マネージャー UI での EventFlow NuGet パッケージ][1]

EventFlow をホストするサービスには、アプリケーション ログの送信元と送信先に応じた適切なパッケージが含まれる必要があります。 次のパッケージを追加します。 

* `Microsoft.Diagnostics.EventFlow.Input.EventSource` 
    * (サービスの EventSource クラス、および *Microsoft-ServiceFabric-Services* や *Microsoft-ServiceFabric-Actors* などの標準 EventSource からデータをキャプチャするため)
* `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights` 
    * (ここでは、Azure Application Insights リソースにログを送信します)  
* `Microsoft.Diagnostics.EventFlow.ServiceFabric` 
    * (Service Fabric サービス構成からの EventFlow パイプラインの初期化を可能にし、診断データの送信に関する問題を Service Fabric 正常性レポートとして報告します)

> [!NOTE]
> `Microsoft.Diagnostics.EventFlow.Input.EventSource` パッケージでは、サービス プロジェクトが .NET Framework 4.6 以降を対象とする必要があります。 このパッケージをインストールする前に、プロジェクトのプロパティで適切な対象フレームワークが設定されていることを確認します。 

すべてのパッケージをインストールした後は、サービスで EventFlow を構成して有効にします。

## <a name="configuring-and-enabling-log-collection"></a>ログ収集の構成と有効化
ログの送信を行う EventFlow パイプラインは、構成ファイルに格納されている仕様から作成されます。 `Microsoft.Diagnostics.EventFlow.ServiceFabric` パッケージは、初期の EventFlow 構成ファイルを `PackageRoot\Config` ソリューション フォルダーにインストールします。 ファイル名は `eventFlowConfig.json` です。 サービスの既定の `EventSource` クラスからデータをキャプチャして Application Insights サービスにデータを送信するように、この構成ファイルを変更する必要があります。

> [!NOTE]
> この記事の説明では、読者が **Azure Application Insights** サービスを理解していて、Service Fabric サービスの監視に Application Insights リソースを使う予定であるものとします。 詳しくは、「[Application Insights リソースの作成](../application-insights/app-insights-create-new-resource.md)」をご覧ください。

`eventFlowConfig.json` ファイルをエディターで開き、その内容を次のように変更します。 ServiceEventSource の名前と Application Insights のインストルメンテーション キーをコメントに従って置き換えることに注意してください。 

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

> [!NOTE]
> サービスの ServiceEventSource の名前は、ServiceEventSource クラスに適用される `EventSourceAttribute` の Name プロパティの値です。 すべては `ServiceEventSource.cs` ファイルで指定されており、サービス コードの一部です。 たとえば、次のコード スニペットでは、ServiceEventSource の名前は *MyCompany-Application1-Stateless1* です。
> ```csharp
> [EventSource(Name = "MyCompany-Application1-Stateless1")]
> internal sealed class ServiceEventSource : EventSource
> {
>    // (rest of ServiceEventSource implementation)
>} 
> ```

`eventFlowConfig.json` ファイルはサービス構成パッケージの一部であることに注意してください。 このファイルに対する変更は、サービスの完全なアップグレードまたは構成のみのアップグレードに含めることができ、Service Fabric アップグレードの正常性チェックの対象にでき、アップグレードが失敗した場合は自動的にロールバックできます。 詳しくは、「[Service Fabric アプリケーションのアップグレード](service-fabric-application-upgrade.md)」をご覧ください。

最後に、`Program.cs` ファイル内にあるサービスのスタートアップ コードで、EventFlow パイプラインをインスタンス化します。 次の例では、EventFlow に関連して追加された箇所は、`****` で始まるコメントで示されています。

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

## <a name="verification"></a>確認
サービスを開始し、Visual Studio の [デバッグ] 出力ウィンドウを観察します。 サービスを開始すると、サービスが "Application Insights Telemetry" レコードを送信している証拠が表示され始めるはずです。 Web ブラウザーを開き、Application Insights リソースに移動します。 [検索] タブを開きます (既定の [概要] ブレードの先頭) 。 短い遅延の後、Application Insights ポータルでトレースの表示が始まります。

![Service Fabric アプリケーションからのログを表示する Application Insights ポータル][2]

## <a name="next-steps"></a>次のステップ
* [Service Fabric サービスの診断と監視](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
* [EventFlow のドキュメント](https://github.com/Azure/diagnostics-eventflow)


<!--Image references-->
[1]: ./media/service-fabric-diagnostics-collect-logs-without-an-agent/eventflow-nugets.png
[2]: ./media/service-fabric-diagnostics-collect-logs-without-an-agent/ai-traces.png


<!--HONumber=Jan17_HO3-->


