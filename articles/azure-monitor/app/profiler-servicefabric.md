---
title: Application Insights を使用してライブ Azure Service Fabric アプリケーションをプロファイルする | Microsoft Docs
description: Service Fabric アプリケーションに対して Profiler を有効にします
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 0b1a06d181fc4d2a44d389d47d1f9480c2fdcb40
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401101"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Application Insights を使用してライブ Azure Service Fabric アプリケーションをプロファイルする

Application Insights Profiler を次のサービスにデプロイすることもできます。
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>環境デプロイ定義を設定する

Application Insights Profiler は、Azure Diagnostics に付属しています。 Service Fabric クラスター用の Azure Resource Manager テンプレートを使用して、Azure Diagnostics 拡張機能をインストールできます。 [Service Fabric クラスターに Azure 診断をインストールするテンプレート](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)を入手します。

環境を設定するには、次の操作を実行します。

1. [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 以降が使用されていることを確認するために、デプロイされている OS が `Windows Server 2012 R2` 以降であることを確認します。

1. デプロイ テンプレート ファイルで、[Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) の拡張機能を探します。

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

      デプロイ テンプレートへの診断拡張機能の追加の詳細については、「[Windows VM と Azure Resource Manager テンプレートで監視と診断を利用する](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。

1. Azure Resource Manager テンプレートを使用して、Service Fabric クラスターをデプロイします。  
  設定が正しい場合、Azure Diagnostics 拡張機能をインストールすると Application Insights Profiler がインストールされて有効になります。 

1. Application Insights を Service Fabric アプリケーションに追加します。  
  Profiler で要求のプロファイルを収集するには、アプリケーションが Application Insights の操作を追跡している必要があります。 ステートレス API の場合は、[プロファイリングのための要求の追跡](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json)の手順を参照できます。 他の種類のアプリでのカスタム操作の追跡の詳細については、「[Application Insights .NET SDK でカスタム操作を追跡する](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json)」を参照してください

1. アプリケーションを再デプロイします。

> [ヒント] 仮想マシンで、JSON ベースの手順に先行する手順としては、Azure portal で **[Virtual Machines]** > **[診断設定]** > **[シンク]** > **に移動し、Application Insights への診断データの送信を [有効] に設定**して、Application Insights アカウントまたは特定の ikey を選択します。

## <a name="next-steps"></a>次の手順

* アプリケーションへのトラフィックを生成します (たとえば、[可用性テスト](monitor-web-app-availability.md)を起動します)。 その後、Application Insights インスタンスへのトレースの送信が開始されるまで 10 ～ 15 分待機します。
* Azure ポータルで [Profiler トレース](profiler-overview.md?toc=/azure/azure-monitor/toc.json)を表示します。
* Profiler の問題のトラブルシューティングについては、[Profiler のトラブルシューティング](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)に関する記事をご覧ください。
