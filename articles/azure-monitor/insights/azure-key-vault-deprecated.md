---
title: Azure Monitor における Azure Key Vault ソリューション | Microsoft Docs
description: Azure Monitor の Azure Key Vault ソリューションを使用して、Azure Key Vault のログを調査することができます。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/27/2019
ms.openlocfilehash: 24869841a106a68234cfec600d9a98e519f8c6cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101734198"
---
# <a name="azure-key-vault-analytics-solution-in-azure-monitor"></a>Azure Monitor の Azure Key Vault Analytics ソリューション

> [!NOTE]
> このソリューションは非推奨です。 [Azure Monitor for Key Vault を使用することをお勧めします](./key-vault-insights-overview.md)。

![Key Vault のシンボル](media/azure-key-vault/key-vault-analytics-symbol.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor の Azure Key Vault ソリューションを使用して、Azure Key Vault の AuditEvent ログを調査することができます。

このソリューションを使用するには、Azure Key Vault の診断ログを有効にし、診断を Log Analytics ワークスペースに送信する必要があります。 Azure Blob Storage にログを書き込む必要はありません。

> [!NOTE]
> 2017 年 1 月に、Key Vault から Log Analytics へのログ送信のサポート方法が変更されました。 使用している Key Vault ソリューションのタイトルに *(非推奨)* と表示される場合は、「[古い Key Vault ソリューションからの移行](#migrating-from-the-old-key-vault-solution)」を参照し、必要な手順に従ってください。
>
>

## <a name="install-and-configure-the-solution"></a>ソリューションのインストールと構成
Azure Key Vault ソリューションのインストールと構成は、次の手順で行います。

1. [Solutions Gallery からの Azure Monitor ソリューションの追加](./solutions.md)に関するページで説明されているプロセスを使用して、Azure Key Vault ソリューションを Log Analytics ワークスペースに追加します。
2. [ポータル](#enable-key-vault-diagnostics-in-the-portal)か [PowerShell](#enable-key-vault-diagnostics-using-powershell) を使用して、監視する Key Vault リソースの診断ログを有効にします。

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>ポータルで Key Vault 診断を有効にする

1. Azure Portal で、監視する Key Vault リソースに移動します。
2. *[診断設定]* を選択して、次のページを開きます。

   ![キー コンテナー リソース ContosoKVSCUS の診断設定ページのスクリーンショット。診断をオンにするオプションが強調表示されています。](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics01.png)
3. *[診断を有効にする]* をクリックして、次のページを開きます。

   ![診断設定を構成するためのページのスクリーンショット。 [Send to Log Analytics]\(Log Analytics に送信\)、AuditEvent ログ、AllMetrics のオプションが選択されています。](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics02.png)
4. 診断設定の名前を付けます。
5. *[Send to Log Analytics]* (Log Analytics に送信) のチェックボックスをクリックします。
6. 既存の Log Analytics ワークスペースを選択するか、ワークスペースを作成します。
7. *AuditEvent* ログを有効にするには、[ログ] の下のチェックボックスをクリックしてください。
8. *[保存]* をクリックして Log Analytics ワークスペースの診断ログを有効にします。

### <a name="enable-key-vault-diagnostics-using-powershell"></a>PowerShell を使用して Key Vault 診断を有効にする
次の PowerShell スクリプトは、`Set-AzDiagnosticSetting` を使用して Key Vault のリソース ログを有効にする方法の例を示しています。
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'

Set-AzDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Azure Key Vault データ収集の詳細の確認
Key Vault の診断ログは、Azure Key Vault ソリューションによって直接収集されます。
Azure Blob Storage にログを記述する必要はありません。データ収集のエージェントも不要です。

次の表は、Azure Key Vault のデータ収集手段とデータ収集方法に関する各種情報をまとめたものです。

| プラットフォーム | 直接エージェント | Systems Center Operations Manager エージェント | Azure | Operations Manager が必要か | 管理グループによって送信される Operations Manager エージェントのデータ | 収集の頻度 |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | 着信時 |

## <a name="use-azure-key-vault"></a>Azure Key Vault の使用
[ソリューションをインストール](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?source=intercept.nl&tab=Overview)すると、Azure Monitor の **[概要]** ページの **[Key Vault Analytics]** タイルをクリックすることで、Key Vault データが表示されます。 このページは、**Azure Monitor** メニューの **[Insights]** セクションの **[More]\(詳細\)** をクリックして開きます。 

![Azure Monitor の [概要] ページの [Key Vault Analytics] タイルのスクリーンショット。一定期間を対象とするキー コンテナー操作のボリュームをグラフで確認できます。](media/azure-key-vault/log-analytics-keyvault-tile.png)

**[Key Vault Analytics]** タイルをクリックした後、収集されたログの概要を表示し、次のカテゴリについて詳細表示することができます。

* すべての Key Vault 操作の時系列ボリューム
* 失敗した操作の時系列ボリューム
* 操作ごとの平均待機時間
* 操作のサービス品質 (所要時間が 1,000 ミリ秒を超える操作の数と所要時間が 1,000 ミリ秒を超える操作の一覧)

![Azure Key Vault ダッシュボードのスクリーンショット。すべての操作、失敗した操作、操作の平均待機時間のグラフィック データが含まれるタイルを確認できます。](media/azure-key-vault/log-analytics-keyvault01.png)

![Azure Key Vault ダッシュボードのスクリーンショット。操作の平均待機時間、サービスの品質、推奨される検索のデータが含まれるタイルを確認できます。](media/azure-key-vault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>いずれかの操作の詳細を表示するには
1. **[概要]** ページの **[Key Vault Analytics]** タイルをクリックします。
2. **[Azure Key Vault]** ダッシュボードにあるいずれかのブレードで概要情報を確認し、ログの検索ページで、詳細情報の表示対象をクリックします。

    どのログの検索ページでも、時間、詳細結果、ログ検索履歴を表示することができます。 結果を絞り込むファセットを使用してフィルター処理することもできます。

## <a name="azure-monitor-log-records"></a>Azure Monitor のログ レコード
Azure Key Vault ソリューションによって分析されるのは、Azure Diagnostics の [AuditEvent ログ](../../key-vault/general/logging.md)から収集された **KeyVaults** タイプのレコードです。  これらのレコードは、次の表に示したプロパティを持ちます。  

| プロパティ | 説明 |
|:--- |:--- |
| `Type` |*AzureDiagnostics* |
| `SourceSystem` |*Azure* |
| `CallerIpAddress` |要求を行ったクライアントの IP アドレス |
| `Category` | *AuditEvent* |
| `CorrelationId` |オプションの GUID であり、クライアント側のログとサービス側の (Key Vault) ログを対応付ける場合に渡します。 |
| `DurationMs` |REST API 要求を処理するのにかかった時間 (ミリ秒単位) です。 この時間にはネットワーク待機時間が含まれません。したがって、クライアント側で測定する時間はこの時間と一致しない場合があります。 |
| `httpStatusCode_d` |要求によって返された HTTP 状態コード (例: *200*) |
| `id_s` |要求の一意の ID |
| `identity_claim_appid_g` | アプリケーション ID の GUID |
| `OperationName` |操作の名前 (「[Azure Key Vault のログ記録](../../key-vault/general/logging.md)」を参照) |
| `OperationVersion` |クライアントによって要求された REST API バージョン (例: *2015-06-01*) |
| `requestUri_s` |要求の URI |
| `Resource` |Key Vault の名前 |
| `ResourceGroup` |Key Vault のリソース グループ |
| `ResourceId` |Azure リソース マネージャー リソース ID。 Key Vault のログの場合は、Key Vault リソース ID となります。 |
| `ResourceProvider` |*MICROSOFT.KEYVAULT* |
| `ResourceType` | *VAULTS* |
| `ResultSignature` |HTTP の状態 (例: *OK*) |
| `ResultType` |REST API 要求の結果 (例: *Success*) |
| `SubscriptionId` |Key Vault を含んでいるサブスクリプションの Azure サブスクリプション ID |

## <a name="migrating-from-the-old-key-vault-solution"></a>古い Key Vault ソリューションからの移行
2017 年 1 月に、Key Vault から Log Analytics へのログ送信のサポート方法が変更されました。 これらの変更には次の利点があります。
+ ストレージ アカウントを使用する必要がなく、ログは Log Analytics ワークスペースに直接書き込まれます。
+ ログが生成されてから Log Analytics で使用できるまでの待機時間が短縮されます。
+ 構成手順が簡素化されます。
+ すべての種類の Azure Diagnostics の共通形式です。

最新のソリューションを使用するには、次の操作を行います。

1. [Key Vault から Log Analytics ワークスペースに診断が直接送信されるように構成します。](#enable-key-vault-diagnostics-in-the-portal)  
2. [Solutions Gallery からの Azure Monitor ソリューションの追加](./solutions.md)について説明されている手順に従って Azure Key Vault ソリューションを有効にします。
3. 新しいデータ型を使用するように、保存されたクエリ、ダッシュボード、またはアラートを更新します。
   + 型をKeyVaults から AzureDiagnostics に変更します。 ResourceType を使用して、Key Vault のログをフィルター処理できます。
   + `KeyVaults` の代わりに `AzureDiagnostics | where ResourceType'=="VAULTS"` を使用してください。
   + フィールド:(フィールド名は大文字小文字が区別されます)
   + 名前に \_s、\_d、または \_g のサフィックスがあるフィールドについては、最初の文字を小文字に変更します。
   + 名前に \_o のサフィックスがあるフィールドについては、入れ子になったフィールド名に基づき、データは個別のフィールドに分割されます。 例: 呼び出し元の UPN を `identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s` のフィールドに格納する
   + フィールド CallerIpAddress は CallerIPAddress に変更されます。
   + フィールド RemoteIPCountry は存在しません。
4. *Key Vault Analytics (非推奨)* ソリューションを削除します。 PowerShell を使用している場合は、次のコードを使用します。`Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`

変更前に収集されたデータは、新しいソリューションには表示されません。 元の型とフィールド名を使用して、このデータのクエリを続行できます。

## <a name="troubleshooting"></a>トラブルシューティング
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>次のステップ
* Azure Monitor で[ログ クエリ](../logs/log-query-overview.md)を使用して、詳細な Azure Key Vault データを表示します。

