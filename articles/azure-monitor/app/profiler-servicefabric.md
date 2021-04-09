---
title: Application Insights を使用してライブ Azure Service Fabric アプリをプロファイルする
description: Service Fabric アプリケーションに対して Profiler を有効にします
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 67e7765a1f46c2be5790c11687e06ea624702b9b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100589572"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Application Insights を使用してライブ Azure Service Fabric アプリケーションをプロファイルする

Application Insights Profiler を次のサービスにデプロイすることもできます。
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>環境デプロイ定義を設定する

Application Insights Profiler は、Azure Diagnostics に付属しています。 Service Fabric クラスター用の Azure Resource Manager テンプレートを使用して、Azure Diagnostics 拡張機能をインストールできます。 [Service Fabric クラスターに Azure Diagnostics をインストールするテンプレート](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)を入手します。

環境を設定するには、次の操作を実行します。

1. プロファイラはWindows .NET Framework と .NET Core をサポートします。 .NET Framework を使用している場合は、[.NET Framework 4.6.1](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)以降を使用していることを確認してください。 配置されている OS が`Windows Server 2012 R2`以降であることを確認すれば十分です。 プロファイラーは、.NET Core 2.1 以降のアプリケーションをサポートします。

1. デプロイ テンプレート ファイルで、[Azure Diagnostics](../agents/diagnostics-extension-overview.md) の拡張機能を探します。

1. `WadCfg` の子要素として次の `SinksConfig` セクションを追加します。 `ApplicationInsightsProfiler` プロパティ値は、自分の Application Insights のインストルメンテーション キーに置き換えます。  

      ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
          }
        ]
      }
      ```

      デプロイ テンプレートへの診断拡張機能の追加の詳細については、「[Windows VM と Azure Resource Manager テンプレートで監視と診断を利用する](../../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json)」を参照してください。

1. Azure Resource Manager テンプレートを使用して、Service Fabric クラスターをデプロイします。  
  設定が正しい場合、Azure Diagnostics 拡張機能をインストールすると Application Insights Profiler がインストールされて有効になります。 

1. Application Insights を Service Fabric アプリケーションに追加します。  
  Profiler で要求のプロファイルを収集するには、アプリケーションが Application Insights の操作を追跡している必要があります。 ステートレス API の場合は、[プロファイリングのための要求の追跡](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json)の手順を参照できます。 他の種類のアプリでのカスタム操作の追跡の詳細については、「[Application Insights .NET SDK でカスタム操作を追跡する](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json)」を参照してください

1. アプリケーションを再デプロイします。


## <a name="next-steps"></a>次のステップ

* アプリケーションへのトラフィックを生成します (たとえば、[可用性テスト](monitor-web-app-availability.md)を起動します)。 その後、Application Insights インスタンスへのトレースの送信が開始されるまで 10 ～ 15 分待機します。
* Azure ポータルで [Profiler トレース](profiler-overview.md?toc=/azure/azure-monitor/toc.json)を表示します。
* Profiler の問題のトラブルシューティングについては、[Profiler のトラブルシューティング](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)に関する記事をご覧ください。
