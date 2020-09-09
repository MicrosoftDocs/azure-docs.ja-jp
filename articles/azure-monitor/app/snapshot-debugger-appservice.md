---
title: Azure App Service で .NET アプリでスナップショット デバッガーを有効にする | Microsoft Docs
description: Azure App Service で .NET アプリでスナップショット デバッガーを有効にする
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6928da704236c4bb5492f99a4a5327bf297a323d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84676846"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Azure App Service で .NET アプリでスナップショット デバッガーを有効にする

スナップショット デバッガーは、現在、Windows サービス プランの Azure App Service で実行されている ASP.NET アプリと ASP.NET Core アプリで機能します。

## <a name="enable-snapshot-debugger"></a><a id="installation"></a> スナップショット デバッガーを有効にする
アプリでスナップショット デバッガーを有効にするには、次の手順に従います。 別の種類の Azure サービスを実行している場合、他のサポート対象プラットフォームでスナップショット デバッガーを有効にする手順については以下を参照してください。
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric サービス](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines と仮想マシン スケール セット](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [オンプレミスの仮想マシンまたは物理マシン](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

プレビュー版の .NET Core を使用している場合は、最初に[他の環境用のスナップショット デバッガーを有効にする](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)ための手順に従って、[Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet パッケージをアプリケーションに含めた後、次の手順の残りの部分を完了します。 

Application Insights スナップショット デバッガーは App Services ランタイムの一部としてプレインストールされますが、App Service アプリのスナップショットを取得するには、スナップショット デバッガーを有効にする必要があります。 App Insights SDK をソース コードに組み込んでいる場合でも、アプリをデプロイしたら以下の手順に従ってスナップショット デバッガーを有効にしてください。

1. App Service の Azure コントロール パネルに移動します。
2. **[設定]、[Application Insights]** ページの順に移動します。

   ![App Service ポータルで App Insights を有効にする](./media/snapshot-debugger/applicationinsights-appservices.png)

3. ウィンドウの指示に従って新しいリソースを作成するか、既存の App Insights リソースを選択してアプリを監視します。 また、スナップショット デバッガーの両方のスイッチが**オン**になっていることも確認します。

   ![App Insights のサイト拡張機能を追加する][Enablement UI]

4. スナップショット デバッガーは、App Services のアプリ設定を使用して有効になりました。

    ![スナップショット デバッガーのアプリ設定][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>スナップショット デバッガーを無効にする

「**スナップショット デバッガーを有効にする**」と同じ手順に従ってください。ただし、スナップショット デバッガーの両方のスイッチを**オフ**にします。
アプリケーション例外の診断を容易にするために、すべてのアプリでスナップショット デバッガーを有効にすることをお勧めします。

## <a name="azure-resource-manager-template"></a>Azure Resource Manager テンプレート

Azure App Service の場合、Azure Resource Manager テンプレートでアプリ設定を指定して、スナップショット デバッガーと Profiler を有効にできます。 アプリ設定を含む構成リソースを、Web サイトの子リソースとして追加します。

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('webSiteName')]",
  "type": "Microsoft.Web/sites",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  "tags": { 
    "[concat('hidden-related:', resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName')))]": "empty",
    "displayName": "Website"
  },
  "properties": {
    "name": "[parameters('webSiteName')]",
    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "name": "appsettings",
      "type": "config",
      "dependsOn": [
        "[parameters('webSiteName')]",
        "[concat('AppInsights', parameters('webSiteName'))]"
      ],
      "properties": {
        "APPINSIGHTS_INSTRUMENTATIONKEY": "[reference(resourceId('Microsoft.Insights/components', concat('AppInsights', parameters('webSiteName'))), '2014-04-01').InstrumentationKey]",
        "APPINSIGHTS_PROFILERFEATURE_VERSION": "1.0.0",
        "APPINSIGHTS_SNAPSHOTFEATURE_VERSION": "1.0.0",
        "DiagnosticServices_EXTENSION_VERSION": "~3",
        "ApplicationInsightsAgent_EXTENSION_VERSION": "~2"
      }
    }
  ]
},
```

## <a name="next-steps"></a>次のステップ

- 例外をトリガーできるアプリケーションへのトラフィックを生成します。 その後、Application Insights インスタンスにスナップショットが送信がされるまで 10 ～ 15 分待機します。
- Azure portal で[スナップショット](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal)を確認します。
- スナップショット デバッガーの問題のトラブルシューティングについては、[スナップショット デバッガーのトラブルシューティング](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)のページを参照してください。

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

