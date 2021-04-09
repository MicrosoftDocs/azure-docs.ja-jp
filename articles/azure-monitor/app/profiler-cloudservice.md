---
title: Application Insights でライブ Azure Cloud Services をプロファイリングする | Microsoft Docs
description: Azure Cloud Services に対して Application Insights Profiler を有効にします。
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 5eb6680bba44872821a2f0d094d31d0ad994f8a0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100589584"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Application Insights でライブ Azure Cloud Services をプロファイリングする

Application Insights Profiler を次のサービスにデプロイすることもできます。
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric アプリケーション](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler は、Azure Diagnostics 拡張機能と共にインストールされています。 必要な作業は、Profiler をインストールして Application Insights リソースにプロファイルを送信するように、Azure Diagnostics を構成することだけです。

## <a name="enable-profiler-for-azure-cloud-services"></a>Azure Cloud Services に対して Profiler を有効にする
1. [.NET Framework 4.6.1](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 以降を使用していることを確認します。 OS ファミリ 4 を使用している場合は、[スタートアップ タスク](../../cloud-services/cloud-services-dotnet-install-dotnet.md)を使用して、.NET Framework 4.6.1 以降をインストールする必要があります。 OS ファミリ 5 には、互換性のあるバージョンの .NET Framework が既定で含まれています。 

1. [Application Insights SDK を Azure Cloud Services](./cloudservices.md?toc=%2fazure%2fazure-monitor%2ftoc.json) に追加します。

    **Cloud Services 向けの WAD に付属しているプロファイラーのバグが修正されました。** Cloud Services 向け WAD の最新バージョン (1.12.2.0) は、最近リリースされたすべての App Insights SDK バージョンで動作します。 Cloud Service のホストでは WAD が自動的にアップグレードされますが、即時に行われるわけではありません。 アップグレードを強制するには、サービスを再デプロイするか、ノードを再起動します。

1. Application Insights で要求を追跡します。

    * ASP.NET Web ロールの場合、Application Insights で要求を自動的に追跡できます。

    * worker ロールの場合、[要求を追跡するコードを追加します](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json)。

1. Profiler を有効にするために Azure Diagnostics 拡張機能を次のように構成します。

    a. アプリケーション ロールで、次に示すように [Azure Diagnostics](../agents/diagnostics-extension-overview.md) の *diagnostics.wadcfgx* ファイルを探します。  

      ![診断構成ファイルの場所](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      ファイルが見つからない場合は、「[Set up diagnostics for Azure Cloud Services and Virtual Machines](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)」(Azure クラウド サービスと仮想マシンに対する診断を設定する) をご覧ください。

    b. `WadCfg` の子要素として次の `SinksConfig` セクションを追加します。  

      ```xml
      <WadCfg>
        <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
        <SinksConfig>
          <Sink name="MyApplicationInsightsProfiler">
            <!-- Replace with your own Application Insights instrumentation key. -->
            <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
          </Sink>
        </SinksConfig>
      </WadCfg>
      ```

    > [!NOTE]
    > *diagnostics.wadcfgx* ファイルに ApplicationInsights 型の別のシンクが含まれている場合、次の 3 つのインストルメンテーション キーがすべて一致する必要があります。  
    > * アプリケーションによって使用されるキー。 
    > * ApplicationInsights シンクによって使用されるキー。 
    > * ApplicationInsightsProfiler シンクによって使用されるキー。 
    >
    > `ApplicationInsights` シンクで実際に使用されているインストルメンテーション キーの値は、*ServiceConfiguration.\*.cscfg* ファイルで確認できます。 
    > Visual Studio 15.5 Azure SDK リリース以降は、アプリケーションと ApplicationInsightsProfiler シンクで使用されるインストルメンテーション キーのみが相互に一致する必要があります。

1. 新しい診断構成でサービスをデプロイすると、ご利用のサービスで Application Insights Profiler が実行されるように構成されます。
 
## <a name="next-steps"></a>次のステップ

* アプリケーションへのトラフィックを生成します (たとえば、[可用性テスト](monitor-web-app-availability.md)を起動します)。 その後、Application Insights インスタンスへのトレースの送信が開始されるまで 10 ～ 15 分待機します。
* Azure ポータルで [Profiler トレース](profiler-overview.md?toc=/azure/azure-monitor/toc.json)を表示します。
* プロファイラーの問題のトラブルシューティングについては、[Profiler のトラブルシューティング](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)に関するページをご覧ください。

