---
title: 新しい Azure Monitor Application Insights のワークスペース ベースのリソースを作成する | Microsoft Docs
description: 新しい Azure Monitor Application Insights のワークスペース ベースのリソースを有効にするために必要な手順について説明します。
ms.topic: conceptual
ms.date: 10/06/2020
ms.openlocfilehash: 3ec0b25a24af13b29a3568165009f8a6d66e0218
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100578332"
---
# <a name="workspace-based-application-insights-resources"></a>ワークスペース ベースの Application Insights リソース

ワークスペース ベースのリソースは、Application Insights と Log Analytics 間の完全な統合をサポートします。 Application Insights テレメトリを共通の Log Analytics ワークスペースに送信できるようになりました。これにより、アプリケーション、インフラストラクチャ、およびプラットフォームのログを 1 つの統合された場所に保持しながら、Log Analytics のすべての機能にフル アクセスできます。

これにより、リソース全体で一般的な Azure ロールベースのアクセス制御 (Azure RBAC) を使えるようになり、アプリ間またはワークスペース間のクエリが不要になります。

> [!NOTE]
> ワークスペース ベースの Application Insights リソースのデータ インジェストとリテンション期間は、データが保管されている Log Analytics ワークスペースを通じて課金されます。 ワークスペース ベースの Application Insights リソースの課金に関する[詳細を参照]( ./pricing.md#workspace-based-application-insights)してください。

## <a name="new-capabilities"></a>新機能

ワークスペースベースの Application Insights を使用すると、次のような、Azure Monitor および Log Analytics の最新の機能を活用できます。

* [カスタマー マネージド キー (CMK)](../logs/customer-managed-keys.md) は、あなたのみがアクセスできる暗号化キーで、保存されているお使いのデータを暗号化します。
* [Azure Private Link](../logs/private-link-security.md) を使用すると、プライベート エンドポイントを使用して Azure PaaS サービスを仮想ネットワークに安全に接続できます。
* [Profiler およびスナップショット デバッガー向けの Bring Your Own Storage (BYOS)](./profiler-bring-your-own-storage.md) では、保存時の暗号化ポリシー、有効期間管理ポリシー、および Application Insights Profiler およびスナップショット デバッガーに関連付けられているすべてのデータのネットワーク アクセスを完全に制御できます。 
* [容量予約レベル](../logs/manage-cost-storage.md#pricing-model)を利用すると、従量課金制の料金と比較して 25% も節約できます。 
* Log Analytics ストリーミング インジェストにより、データ インジェストがより高速になります。

## <a name="create-workspace-based-resource"></a>ワークスペースベースのリソースを作成する

[Azure portal](https://portal.azure.com) にサインインし、Application Insights リソースを作成します。

> [!div class="mx-imgBorder"]
> ![ワークスペース ベースの Application Insights リソース](./media/create-workspace-resource/create-workspace-based.png)

既存の Log Analytics ワークスペースをまだ持っていない場合は [Log Analytics ワークスペースの作成に関するドキュメント](../logs/quick-create-workspace.md)を参照してください。

**ワークスペース ベースのリソースは、現在、すべての商業地域および Azure Government でご利用いただけます。**

リソースが作成されると、 **[概要]** ウィンドウに対応するワークスペース情報が表示されます。

![ワークスペース名](./media/create-workspace-resource/workspace-name.png)

青いリンク テキストをクリックすると、関連付けられている Log Analytics ワークスペースに移動します。このワークスペースでは、新しい統合ワークスペース クエリ環境を利用できます。

> [!NOTE]
> Application Insights のエクスペリエンスにおける Application Insights クラシック リソース クエリ、ブック、およびログベースのアラートについては、引き続き完全な下位互換性を提供します。 [新しいワークスペース ベースのテーブル構造またはスキーマ](apm-tables.md)に対してクエリ/ビューを実行するには、まず Log Analytics ワークスペースに移動する必要があります。 [Application Insights] ペインの **[ログ (Analytics)]** を選択すると、従来の Application Insights クエリ エクスペリエンスにアクセスできます。

## <a name="copy-the-connection-string"></a>接続文字列のコピー

[接続文字列](./sdk-connection-string.md?tabs=net)により、利用統計情報と関連付けるリソースが識別されます。 また、リソースでテレメトリの宛先として使用するエンドポイントを変更することもできます。 接続文字列をコピーし、アプリケーションのコードまたは環境変数に追加する必要があります。

## <a name="monitoring-configuration"></a>監視構成

ワークスペース ベースの Application Insights リソースを作成できれば、監視の構成は比較的簡単になります。

### <a name="code-based-application-monitoring"></a>コードベースのアプリケーション監視

コードベースのアプリケーション監視では、適切な Application Insights SDK をインストールし、インストルメンテーション キーにポイントするか、接続文字列を新しく作成したリソースにポイントするだけです。  

コードベースの監視用の Application Insights SDK の設定に関する詳細なドキュメントについては、言語またはフレームワーク固有のドキュメントを参照してください。

- [ASP.NET](./asp-net.md)
- [ASP.NET Core ](./asp-net-core.md)
- [バックグラウンド タスクおよび最新のコンソール アプリケーション (.NET/.NET Core)](./worker-service.md)
- [クラシック コンソール アプリケーション (.NET)](./console.md) 
- [Java ](./java-get-started.md?tabs=maven)
- [JavaScript](./javascript.md)
- [Node.js](./nodejs.md)
- [Python](./opencensus-python.md)

### <a name="codeless-monitoring-and-visual-studio-resource-creation"></a>コード不要の監視と Visual Studio リソースの作成

Azure Functions や Azure App Service などのサービスをコードを使用せずに監視するには、最初にワークスペース ベースの Application Insights リソースを作成してから、監視の構成フェーズでそのリソースをポイントする必要もあります。

これらのサービスは、独自のリソース作成プロセスに新しい Application Insights リソースを作成するオプションを提供しますが、これらの UI オプションを使用して作成されたリソースは、現在、クラシック Application Insights エクスペリエンスに限定されています。

これは、Visual Studio for ASP.NET と ASP.NET Core の Application Insights リソースの作成エクスペリエンスにも当てはまります。 Visual Studio の監視有効化 UI から、既存のワークスペース ベースのリソースを選択する必要があります。 Visual Studio で [新しいリソースの作成] を選択すると、クラシック Application Insights リソースの作成が制限されます。

## <a name="creating-a-resource-automatically"></a>リソースの自動作成

### <a name="azure-cli"></a>Azure CLI

プレビューの Application Insights Azure CLI コマンドにアクセスするには、まず次を実行する必要があります。

```azurecli
 az extension add -n application-insights
```

`az extension add` コマンドを実行しないと、`az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.` のようなエラー メッセージが表示されます。

これで、以下を実行して Application Insights リソースを作成できるようになりました。

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--only-show-errors]
                                         [--query-access {Disabled, Enabled}]
                                         [--tags]
                                         [--workspace]
```

#### <a name="example"></a>例

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g my_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

このコマンドについての完全な Azure CLI ドキュメントについては、[Azure CLI のドキュメント](/cli/azure/ext/application-insights/monitor/app-insights/component#ext-application-insights-az-monitor-app-insights-component-create)を参照してください。

### <a name="azure-powershell"></a>Azure PowerShell

`New-AzApplicationInsights` PowerShell コマンドでは、現在、ワークスペース ベースの Application Insights リソースの作成はサポートされていません。 PowerShell を使用してワークスペース ベースのリソースを作成するには、以下の Azure Resource Manager テンプレートを使用して、PowerShell を使用してデプロイします。

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager のテンプレート

#### <a name="template-file"></a>テンプレート ファイル

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2020-02-02-preview",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]",
                "WorkspaceResourceId": "[parameters('workspaceResourceId')]"
            }
        }
    ]
}
```

#### <a name="parameters-file"></a>パラメーター ファイル

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "type": {
            "value": "web"
        },
        "name": {
            "value": "customresourcename"
        },
        "regionId": {
            "value": "eastus"
        },
        "tagsArray": {
            "value": {}
        },
        "requestSource": {
            "value": "Custom"
        },
        "workspaceResourceId": {
            "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my_resource_group/providers/microsoft.operationalinsights/workspaces/myworkspacename"
        }
    }
}

```

## <a name="modifying-the-associated-workspace"></a>関連付けられているワークスペースの変更

ワークスペース ベースの Application Insights リソースが作成されたら、関連付けられている Log Analytics ワークスペースを変更できます。

Application Insights リソース ウィンドウで **[プロパティ]**  >  **[ワークスペースの変更]**  >  **[Log Analytics ワークスペース]** を選択します。

## <a name="export-telemetry"></a>テレメトリのエクスポート

従来の連続エクスポート機能は、ワークスペース ベースのリソースではサポートされていません。 代わりに、Application Insights リソースから **[診断設定]**  >  **[診断設定を追加する]** を選択します。 すべてのテーブルまたはテーブルのサブセットを選択して、ストレージ アカウントにアーカイブしたり、Azure Event Hub にストリーミングしたりすることができます。

> [!NOTE]
> 現在、テレメトリのエクスポートに追加料金は発生しません。 この機能の価格情報については、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」ページを参照してください。  課金が開始される前に、通知が送信されます。 通知期間後も <feature name> を引き続き使用することを選択した場合は、該当する料金が適用されます。 
 

## <a name="next-steps"></a>次のステップ

* [メトリックを探索する](../essentials/metrics-charts.md)
* [Analytics クエリを作成する](../logs/log-query-overview.md)
