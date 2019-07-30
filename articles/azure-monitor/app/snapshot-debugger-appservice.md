---
title: Azure App Service で .NET アプリでスナップショット デバッガーを有効にする | Microsoft Docs
description: Azure App Service で .NET アプリでスナップショット デバッガーを有効にする
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: bfung
ms.openlocfilehash: 3e8ce3c2eff7b1f7184bb37f141e62563d4fe714
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612678"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Azure App Service で .NET アプリでスナップショット デバッガーを有効にする

スナップショット デバッガーは、現在、Windows サービス プランの Azure App Service で実行されている ASP.NET アプリと ASP.NET Core アプリで機能します。

## <a id="installation"></a> スナップショット デバッガーを有効にする
アプリでスナップショット デバッガーを有効にするには、次の手順に従います。 別の種類の Azure サービスを実行している場合、他のサポート対象プラットフォームでスナップショット デバッガーを有効にする手順については以下を参照してください。
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric サービス](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines と仮想マシン スケール セット](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [オンプレミスの仮想マシンまたは物理マシン](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

プレビュー版の .NET Core を使用している場合は、最初に[他の環境用のスナップショット デバッガーを有効にする](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)ための手順に従って、[Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet パッケージをアプリケーションに含めた後、次の手順の残りの部分を完了します。 

Application Insights スナップショット デバッガーは App Services ランタイムの一部としてプレインストールされますが、App Service アプリのスナップショットを取得するには、スナップショット デバッガーを有効にする必要があります。 App Insights SDK をソース コードに組み込んでいる場合でも、アプリをデプロイしたら以下の手順に従ってスナップショット デバッガーを有効にしてください。

1. Azure Portal の **[App Services]** ウィンドウに移動します。
2. **[設定] > [Application Insights]** ウィンドウに移動します。

   ![App Service ポータルで App Insights を有効にする](./media/snapshot-debugger/applicationinsights-appservices.png)

3. ウィンドウの指示に従って新しいリソースを作成するか、既存の App Insights リソースを選択してアプリを監視します。 また、スナップショット デバッガーの両方のスイッチが**オン**になっていることも確認します。

   ![App Insights のサイト拡張機能を追加する][Enablement UI]

4. スナップショット デバッガーは、App Services のアプリ設定を使用して有効になりました。

    ![スナップショット デバッガーのアプリ設定][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>スナップショット デバッガーを無効にする

「**スナップショット デバッガーを有効にする**」と同じ手順に従ってください。ただし、スナップショット デバッガーの両方のスイッチを**オフ**にします。
アプリケーション例外の診断を容易にするために、すべてのアプリでスナップショット デバッガーを有効にすることをお勧めします。

## <a name="next-steps"></a>次の手順

- 例外をトリガーできるアプリケーションへのトラフィックを生成します。 その後、Application Insights インスタンスにスナップショットが送信がされるまで 10 ～ 15 分待機します。
- Azure portal で[スナップショット](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal)を確認します。
- スナップショット デバッガーの問題のトラブルシューティングについては、[スナップショット デバッガーのトラブルシューティング](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)に関する記事をご覧ください。

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

