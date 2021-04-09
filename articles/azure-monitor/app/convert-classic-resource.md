---
title: Azure Monitor Application Insights のクラシック リソースをワークスペースベースのリソースに移行する | Microsoft Docs
description: Azure Monitor Application Insights のクラシック リソースを新しいワークスペースベースのモデルにアップグレードするために必要な手順について説明します。
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 5791abe33dee2e62aadb00ae1024338e1e44a900
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100584263"
---
# <a name="migrate-to-workspace-based-application-insights-resources"></a>ワークスペースベースの Application Insights リソースに移行する

このガイドでは、クラシック Application Insights リソースをワークスペースベースのリソースに移行するプロセスについて説明します。 ワークスペース ベースのリソースは、Application Insights と Log Analytics 間の完全な統合をサポートします。 ワークスペースベースのリソースは、Application Insights テレメトリを共通の Log Analytics ワークスペースに送信します。これにより、アプリケーション、インフラストラクチャ、およびプラットフォームのログを 1 つの統合された場所に保持しながら、[Azure Monitor の最新機能](#new-capabilities)にアクセスできます。

ワークスペースベースのリソースにより、リソース全体で共通の Azure ロールベースのアクセス制御 (Azure RBAC) が有効になり、アプリ間またはワークスペース間のクエリが不要になります。

**ワークスペースベースのリソースは、現在、すべての商用リージョンおよび Azure US Government でご利用いただけます**

## <a name="new-capabilities"></a>新機能

ワークスペースベースの Application Insights を使用すると、次のような、Azure Monitor および Log Analytics の最新の機能をすべて活用できます。

* [カスタマー マネージド キー (CMK)](../logs/customer-managed-keys.md) は、あなたのみがアクセスできる暗号化キーで、保存されているお使いのデータを暗号化します。
* [Azure Private Link](../logs/private-link-security.md) を使用すると、プライベート エンドポイントを使用して Azure PaaS サービスを仮想ネットワークに安全に接続できます。
* [Profiler およびスナップショット デバッガー向けの Bring Your Own Storage (BYOS)](./profiler-bring-your-own-storage.md) では、保存時の暗号化ポリシー、有効期間管理ポリシー、および Application Insights Profiler およびスナップショット デバッガーに関連付けられているすべてのデータのネットワーク アクセスを完全に制御できます。 
* [容量予約レベル](../logs/manage-cost-storage.md#pricing-model)を利用すると、従量課金制の料金と比較して 25% も節約できます。 
* Log Analytics ストリーミング インジェストにより、データ インジェストがより高速になります。

## <a name="migration-process"></a>移行プロセス

ワークスペースベースのリソースに移行するときに、クラシック リソースのストレージから新しいワークスペースベースのストレージにデータが転送されることはありません。 その代わりに、移行を選ぶと、クラシック リソース データへのアクセスを保持しつつ、新しいデータの書き込み先が Log Analytics ワークスペースに変更されます。 

クラシック リソース データは永続的に保持され、従来の Application Insights リソースにデータ保持設定が適用されます。 移行後に取り込まれるすべての新しいデータには、関連付けられている Log Analytics ワークスペースの[データ保持設定](../logs/manage-cost-storage.md#change-the-data-retention-period)が適用されます。Log Analytics ワークスペースでは、[データの種類別のデータ保持設定](../logs/manage-cost-storage.md#retention-by-data-type)もサポートされています。
移行プロセスは **永続的な処理であり、元に戻すことはできません**。 リソースをワークスペースベースの Application Insights に移行すると、そのリソースは常にワークスペースベースのリソースであり続けます。 ただし、移行の完了後、必要に応じて対象のワークスペースを変更できます。 

> [!NOTE]
> ワークスペース ベースの Application Insights リソースのデータ インジェストとリテンション期間は、データが保管されている [Log Analytics ワークスペースを通じて課金](../logs/manage-cost-storage.md)されます。 移行前にクラシック Application Insights リソース データに取り込まれたデータに対して 90 日を超える保持期間を選択した場合、データ保持の料金はその Application Insights リソースを通じて課金され続けます。 ワークスペース ベースの Application Insights リソースの課金に関する[詳細を参照]( ./pricing.md#workspace-based-application-insights)してください。

既存のリソースを移行する必要がなく、代わりにワークスペースベースの Application Insights リソースを新しく作成する場合は、[ワークスペースベースのリソースの作成ガイド](create-workspace-resource.md)を使用してください。

## <a name="pre-requisites"></a>前提条件 

- アクセス制御モードとして **`use resource or workspace permissions`** 設定が指定されている Log Analytics ワークスペース。 

    - ワークスペースベースの Application Insights リソースは、専用の **`workspace based permissions`** 設定が指定されているワークスペースとは互換性がありません。 Log Analytics ワークスペースのアクセス制御の詳細については、[Log Analytics のアクセス制御モードの構成ガイダンス](../logs/manage-access.md#configure-access-control-mode)を参照してください

    - 既存の Log Analytics ワークスペースをまだ持っていない場合は [Log Analytics ワークスペースの作成に関するドキュメント](../logs/quick-create-workspace.md)を参照してください。
    
- ワークスペースベースのリソースでは、連続エクスポートはサポートされていないため、無効にする必要があります。
移行が完了したら、[診断設定](../essentials/diagnostic-settings.md)を使用して、ストレージ アカウントへのデータのアーカイブまたは Azure Event Hubs へのストリーミングを構成できます。  

- Log Analytics ワークスペースの **[全般]**  >  **[使用量と推定コスト]**  >  **[データ保持]** で、現在のデータ保持設定を確認してください。 この設定は、Application Insights リソースを移行した後、新たに取り込まれるデータが格納される期間に影響します。 現在、Application Insights データを既定の 90 日より長く格納していて、その長い保持期間を維持する場合は、ワークスペースのデータ保持設定を調整しなければならないことがあります。

## <a name="migrate-your-resource"></a>リソースを移行する

このセクションでは、クラシック Application Insights リソースを新しいワークスペースベースのリソースの種類に移行するプロセスについて説明します。

1. Application Insights リソースで、左側のメニュー バーの **[構成]** 見出しの下にある **[プロパティ]** を選択します。

    ![赤い四角形で強調されている [プロパティ]](./media/convert-classic-resource/properties.png)

2. **`Migrate to Workspace-based`** を選びます。
    
     ![リソースの移行ボタン](./media/convert-classic-resource/migrate.png)

3. 今後取り込まれるすべての Application Insights テレメトリを格納する Log Analytics ワークスペースを選択します。

     ![ターゲット ワークスペースを選択するオプションがある移行ウィザードの UI](./media/convert-classic-resource/migration.png)
    

リソースが移行されると、 **[概要]** ペインに、対応するワークスペース情報が表示されます。

![ワークスペース名](./media/create-workspace-resource/workspace-name.png)

青いリンク テキストをクリックすると、関連付けられている Log Analytics ワークスペースに移動します。このワークスペースでは、新しい統合ワークスペース クエリ環境を利用できます。

## <a name="understanding-log-queries"></a>ログ クエリについて

Application Insights のエクスペリエンスにおける Application Insights クラシック リソース クエリ、ブック、およびログベースのアラートについては、引き続き完全な下位互換性を提供します。 

[新しいワークスペースベースのテーブル構造またはスキーマ](apm-tables.md)に対するクエリを記述するには、まず Log Analytics ワークスペースに移動する必要があります。 

ワークスペース内の Log Analytics UI からクエリを直接実行すると、移行後に取り込まれたデータのみが表示されます。 クラシック Application Insights データと、移行後に新たに取り込まれたデータの両方を統合されたクエリ エクスペリエンスで確認するには、移行した Application Insights リソース内から Logs (Analytics) クエリ ビューを使用します。

## <a name="programmatic-resource-migration"></a>プログラムによるリソースの移行

### <a name="azure-cli"></a>Azure CLI

プレビューの Application Insights Azure CLI コマンドにアクセスするには、まず次を実行する必要があります。

```azurecli
 az extension add -n application-insights
```

`az extension add` コマンドを実行しないと、`az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.` のようなエラー メッセージが表示されます。

これで、以下を実行して Application Insights リソースを作成できるようになりました。

```azurecli
az monitor app-insights component update --app
                                         --resource-group
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--query-access {Disabled, Enabled}]
                                         [--retention-time]
                                         [--workspace]
```

#### <a name="example"></a>例

```azurecli
az monitor app-insights component update --app your-app-insights-resource-name -g your_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

このコマンドについての完全な Azure CLI ドキュメントについては、[Azure CLI のドキュメント](/cli/azure/ext/application-insights/monitor/app-insights/component#ext-application-insights-az-monitor-app-insights-component-update)を参照してください。

### <a name="azure-powershell"></a>Azure PowerShell

`Update-AzApplicationInsights` PowerShell コマンドでは、現在、クラシック Application Insights リソースからワークスペースベースのものへの移行はサポートされていません。 PowerShell を使用してワークスペース ベースのリソースを作成するには、以下の Azure Resource Manager テンプレートを使用して、PowerShell を使用してデプロイします。

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

Application Insights リソース ペイン内で **[プロパティ]**  >  **[ワークスペースの変更]**  >  **[Log Analytics ワークスペース]** の順に選択します。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="access-mode"></a>アクセス モード

**エラー メッセージ:** *The selected workspace is configured with workspace-based access mode. (選択されたワークスペースは、ワークスペースベースのアクセス モードで構成されています。)Some APM features may be impacted. (一部の APM 機能に影響する可能性があります。)Select another workspace or allow resource-based access in the workspace settings. (別のワークスペースを選択するか、ワークスペースの設定でリソースベースのアクセスを許可してください。)You can override this error by using CLI. (CLI を使用すると、このエラーをオーバーライドできます。)* 

ワークスペースベースの Application Insights リソースを正常に動作させるには、ターゲット Log Analytics ワークスペースのアクセス制御モードを **リソースまたはワークスペースのアクセス許可** 設定に変更する必要があります。 この設定は、 **[プロパティ]**  >  **[アクセス制御モード]** の Log Analytics ワークスペースの UI にあります。 詳細な手順については、[Log Analytics のアクセス制御モードの構成ガイダンス](../logs/manage-access.md#configure-access-control-mode)を参照してください。 アクセス制御モードとして、排他的な **[ワークスペースのアクセス許可が必要]** 設定が指定されている場合、ポータルの移行エクスペリエンスを使用した移行はブロックされたままになります。

現在のターゲット ワークスペースのセキュリティ上の理由により、アクセス制御モードを変更できない場合は、移行に使用する新しい Log Analytics ワークスペースを作成することをお勧めします。 

### <a name="continuous-export"></a>連続エクスポート

**エラー メッセージ:** *Continuous Export needs to be disabled before continuing. (続行する前に、連続エクスポートを無効にする必要があります。)After migration, use Diagnostic Settings for export. (移行後に、エクスポート用の診断設定を使用してください。)* 

従来の連続エクスポート機能は、ワークスペース ベースのリソースではサポートされていません。 移行の前に、連続エクスポートを無効にする必要があります。

1. Application Insights リソース ビューで、 **[構成]** 見出しの下にある **[連続エクスポート]** を選択します。

    ![[連続エクスポート] メニュー項目](./media/convert-classic-resource/continuous-export.png)

2. **[無効にする]** を選択します。

    ![連続エクスポートを無効にするボタン](./media/convert-classic-resource/disable.png)

- [無効にする] を選択すると、移行 UI に戻ることができます。 連続エクスポートの編集のページで、設定が保存されないことを示すメッセージが表示される場合、これは連続エクスポートの無効化と有効化には関係がないため、[OK] を選択してかまいません。

- Application Insights リソースをワークスペースベースのものに正常に移行したら、診断設定を使用して、連続エクスポートによって提供されていた機能を置き換えることができます。 Application Insights リソース内で、 **[診断設定]**  >  **[診断設定を追加する]** の順に選択します。 すべてのテーブルまたはテーブルのサブセットを選択して、ストレージ アカウントにアーカイブしたり、Azure Event Hub にストリーミングしたりすることができます。 診断設定の詳細なガイダンスについては、[Azure Monitor の診断設定のガイダンス](../essentials/diagnostic-settings.md)を参照してください。

### <a name="retention-settings"></a>保有期間の設定

**警告メッセージ:** *Your customized Application Insights retention settings will not apply to data sent to the workspace. (カスタマイズした Application Insights のデータ保持設定は、ワークスペースに送信されるデータには適用されません。)You will need to reconfigure this separately. (これを別途再構成する必要があります。)*

移行の前に変更を加える必要はありませんが、このメッセージは、現在の Application Insights のデータ保持設定が既定の 90 日の保持期間に設定されていないことを警告しています。 この警告メッセージは、移行して新しいデータの取り込みを開始する前に、Log Analytics ワークスペースのデータ保持設定を変更したい場合もあることを意味します。 

Log Analytics の現在のデータ保持設定は、Log Analytics UI 内の **[全般]**  >  **[使用量と推定コスト]**  >  **[データ保持]** で確認できます。 この設定は、Application Insights リソースを移行した後、新たに取り込まれるデータが格納される期間に影響します。

## <a name="next-steps"></a>次のステップ

* [メトリックを探索する](../essentials/metrics-charts.md)
* [Analytics クエリを作成する](../logs/log-query-overview.md)
