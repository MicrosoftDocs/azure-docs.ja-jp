---
title: Azure App Service で .NET アプリでスナップショット デバッガーを有効にする | Microsoft Docs
description: Azure App Service で .NET アプリでスナップショット デバッガーを有効にする
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 03/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 5a637a6f355be32f82878a52a30c77d020190651
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211619"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Azure App Service で .NET アプリでスナップショット デバッガーを有効にする

スナップショット デバッガーは、現在、Windows サービス プランの Azure App Service で実行されている ASP.NET アプリと ASP.NET Core アプリでサポートされています。

スナップショット デバッガーを使用する場合は、Basic サービス レベル以上でアプリケーションを実行することをお勧めします。

ほとんどのアプリケーションの場合、Free および Shared サービス レベルでは、スナップショットを保存するための十分なメモリまたはディスク領域がありません。

## <a name="enable-snapshot-debugger"></a><a id="installation"></a> スナップショット デバッガーを有効にする
アプリでスナップショット デバッガーを有効にするには、次の手順に従います。

別の種類の Azure サービスを実行している場合、他のサポート対象プラットフォームでスナップショット デバッガーを有効にする手順については次を参照してください。
* [Azure 関数](snapshot-debugger-function-app.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric サービス](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines と仮想マシン スケール セット](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [オンプレミスの仮想マシンまたは物理マシン](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

> [!NOTE]
> プレビュー版の .NET Core を使用している場合、またはアプリケーションで、依存アセンブリを介して直接または間接的に Application Insights SDK が参照されている場合は、[他の環境用のスナップショット デバッガーを有効にする](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)ための手順に従って、[Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet パッケージをアプリケーションに含めた後、下の手順の残りの部分を完了します。 
>
> Application Insights スナップショット デバッガーのコード不要のインストールは、.NET Core サポート ポリシーに従います。
> サポートされているランタイムの詳細については、[.Net Core サポート ポリシー](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)に関するページを参照してください。

スナップショット デバッガーは App Services ランタイムの一部としてプレインストールされますが、App Service アプリのスナップショットを取得するには、スナップショット デバッガーを有効にする必要があります。

アプリをデプロイしたら、下の手順に従ってスナップショット デバッガーを有効にします。

1. App Service の Azure コントロール パネルに移動します。
2. **[設定]、[Application Insights]** ページの順に移動します。

   ![App Service ポータルで App Insights を有効にする](./media/snapshot-debugger/applicationinsights-appservices.png)

3. ウィンドウの指示に従って新しいリソースを作成するか、既存の App Insights リソースを選択してアプリを監視します。 また、スナップショット デバッガーの両方のスイッチが **オン** になっていることも確認します。

   ![App Insights のサイト拡張機能を追加する][Enablement UI]

4. スナップショット デバッガーは、App Services のアプリ設定を使用して有効になりました。

    ![スナップショット デバッガーのアプリ設定][snapshot-debugger-app-setting]

## <a name="enable-snapshot-debugger-for-other-clouds"></a>他のクラウドに対してスナップショット デバッガーを有効にする

現在、エンドポイントの変更が必要なリージョンは [Azure Government](https://docs.microsoft.com/azure/azure-government/compare-azure-government-global-azure#application-insights) と [Azure China](https://docs.microsoft.com/azure/china/resources-developer-guide) のみであり、Application Insights の接続文字列が利用されます。

|接続文字列プロパティ    | 米国政府のクラウド | China Cloud |   
|---------------|---------------------|-------------|
|SnapshotEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

その他の接続のオーバーライドの詳細については、[Application Insights のドキュメント](https://docs.microsoft.com/azure/azure-monitor/app/sdk-connection-string?tabs=net#connection-string-with-explicit-endpoint-overrides)を参照してください。

## <a name="disable-snapshot-debugger"></a>スナップショット デバッガーを無効にする

「**スナップショット デバッガーを有効にする**」と同じ手順に従ってください。ただし、スナップショット デバッガーの両方のスイッチを **オフ** にします。

アプリケーション例外の診断を容易にするために、すべてのアプリでスナップショット デバッガーを有効にすることをお勧めします。

## <a name="azure-resource-manager-template"></a>Azure Resource Manager テンプレート

Azure App Service の場合、Azure Resource Manager テンプレート内でアプリ設定を指定して、スナップショット デバッガーと Profiler を有効にできます。次のテンプレート スニペットを参照してください。

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

