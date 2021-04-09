---
title: Azure Monitor の Log Analytics ワークスペースのデータ エクスポート (プレビュー)
description: Log Analytics のデータ エクスポートを使用すると、選択したテーブルのデータを収集する際に Log Analytics ワークスペースから Azure ストレージ アカウントまたは Azure Event Hubs への連続エクスポートが可能になります。
ms.subservice: logs
ms.topic: conceptual
ms.custom: references_regions, devx-track-azurecli
author: bwren
ms.author: bwren
ms.date: 02/07/2021
ms.openlocfilehash: ea33eff30e712c1597c3606d74cb6d56683211ae
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102615586"
---
# <a name="log-analytics-workspace-data-export-in-azure-monitor-preview"></a>Azure Monitor の Log Analytics ワークスペースのデータ エクスポート (プレビュー)
Azure Monitor で Log Analytics ワークスペースのデータ エクスポートを使用すると、Log Analytics ワークスペースで選択したテーブルのデータを収集する際に Azure ストレージ アカウントまたは Azure Event Hubs への連続エクスポートが可能になります。 この記事では、この機能の詳細と、ワークスペースでデータ エクスポートを構成する手順について説明します。

## <a name="overview"></a>概要
Log Analytics ワークスペースのデータ エクスポートを構成すると、ワークスペースで選択されたテーブルに送信された新しいデータはすべて、1 時間ごとの追加 BLOB でストレージ アカウントに自動的にエクスポートされるか、ほぼリアルタイムで対象のイベント ハブにエクスポートされます。

![データ エクスポートの概要](media/logs-data-export/data-export-overview.png)

含まれるテーブルのすべてのデータが、フィルターを使用せずにエクスポートされます。 たとえば、*SecurityEvent* テーブルに対してデータ エクスポート ルールを構成すると、その構成時点から、*SecurityEvent* テーブルに送信されたすべてのデータがエクスポートされます。


## <a name="other-export-options"></a>その他のエクスポート オプション
Log Analytics ワークスペースのデータ エクスポートでは、Log Analytics ワークスペースからデータが連続してエクスポートされます。 特定のシナリオでデータをエクスポートするその他のオプションには、次のようなものがあります。

- ロジック アプリを使用したログ クエリからのスケジュールされたエクスポート。 これはデータ エクスポート機能に似ていますが、フィルター処理または集計されたデータを Azure ストレージに送信できます。 ただし、この方法には[ログ クエリの制限](../service-limits.md#log-analytics-workspaces)が適用されます。「[ロジック アプリを使用して Log Analytics ワークスペースから Azure ストレージへデータをアーカイブする](logs-export-logic-app.md)」を参照してください。
- PowerShell スクリプトを使用したローカル コンピューターへのワンタイム エクスポート。 「[Invoke-AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport)」を参照してください。


## <a name="limitations"></a>制限事項

- 構成は、現在、CLI または REST 要求を使用して行うことができます。 Azure portal または PowerShell はまだサポートされていません。
- CLI と REST の ```--export-all-tables``` オプションはサポートされていないため、削除されます。 エクスポート ルールでテーブルの一覧を明示的に指定する必要があります。
- 現在、サポート対象のテーブルは、以下の「[サポート対象のテーブル](#supported-tables)」セクションに記載されているものに限定されています。 たとえば、カスタム ログ テーブルは現在サポートされていません。
- サポートされていないテーブルがデータ エクスポート ルールに含まれている場合、操作は成功しますが、そのテーブルのデータはテーブルがサポートされるまでエクスポートされません。 
- 存在しないテーブルがデータ エクスポート ルールに含まれている場合、```Table <tableName> does not exist in the workspace``` エラーで失敗します。
- Log Analytics ワークスペースは、以下を除くすべてのリージョンに配置できます。
  - Azure Government リージョン
  - 西日本
  - ブラジル南東部
  - ノルウェー東部
  - アラブ首長国連邦北部
- 1 つのワークスペースで 2 つのエクスポート ルールを作成できます。イベント ハブに対して 1 つのルール、ストレージ アカウントに対して 1 つのルールです。
- エクスポート先のストレージ アカウントまたはイベント ハブは、Log Analytics ワークスペースと同じリージョンに配置されている必要があります。
- エクスポートするテーブルの名前は、ストレージ アカウントでは 60 文字以内、イベント ハブでは 47 文字以内にする必要があります。 これよりも長い名前のテーブルはエクスポートされません。
- Azure Data Lake Storage の追加 BLOB のサポートは、[制限付きパブリック プレビューになりました](https://azure.microsoft.com/updates/append-blob-support-for-azure-data-lake-storage-preview/)

## <a name="data-completeness"></a>データの完全性
データ エクスポートでは、エクスポート先が使用できない場合に最大 30 分間、データ送信の再試行が続行されます。 30 分経ってもまだ使用できない場合、データは、エクスポート先が使用可能になるまで破棄されます。

## <a name="cost"></a>コスト
現在、データ エクスポート機能に追加料金は発生しません。 データ エクスポートの価格は、後で発表され、課金が始まる前に通知されます。 通知期間後もデータ エクスポートを引き続き使用することを選択した場合は、該当する料金が適用されます。

## <a name="export-destinations"></a>エクスポート先

### <a name="storage-account"></a>ストレージ アカウント
データは、Azure Monitor に到達し、1 時間ごとの追加 BLOB に格納されると、ストレージ アカウントに送信されます。 このデータ エクスポート構成により、ストレージ アカウント内の各テーブルにコンテナーが作成されます。これには、*am-* の後にテーブルの名前が続く名前が付けられます。 たとえば、テーブル *SecurityEvent* は、*am-SecurityEvent* という名前のコンテナーに送信されます。

ストレージ アカウントの BLOB パスは、*WorkspaceResourceId=/subscriptions/subscription-id/resourcegroups/\<resource-group\>/providers/microsoft.operationalinsights/workspaces/\<workspace\>/y=\<four-digit numeric year\>/m=\<two-digit numeric month\>/d=\<two-digit numeric day\>/h=\<two-digit 24-hour clock hour\>/m=00/PT1H.json* です。 追加 BLOB はストレージへの書き込みが 50K に制限されているため、追加の数が多い場合はエクスポートされる BLOB の数が増える可能性があります。 このような場合の BLOB の名前付けパターンは PT1H_#.json となり、# は増分の BLOB 数です。

ストレージ アカウントのデータ形式は [JSON 行](../essentials/resource-logs-blob-format.md)です。 つまり、各レコードは改行で区切られ、外部のレコード配列や JSON レコード間のコンマはありません。 

[![ストレージのサンプル データ](media/logs-data-export/storage-data.png)](media/logs-data-export/storage-data.png#lightbox)

Log Analytics のデータ エクスポートでは、時間ベースのアイテム保持ポリシーで *allowProtectedAppendWrites* 設定が有効になっている場合に、不変ストレージ アカウントに追加 BLOB を書き込むことができます。 これにより、追加 BLOB への新しいブロックの書き込みが許可される一方で、不変性の保護とコンプライアンスは維持されます。 「[保護された追加 BLOB の書き込みを許可する](../../storage/blobs/storage-blob-immutable-storage.md#allow-protected-append-blobs-writes)」を参照してください。

> [!NOTE]
> Azure Data Lake ストレージの追加 BLOB のサポートが、プレビューとしてすべての Azure リージョンで利用できるようになりました。 Azure Data Lake ストレージへのエクスポート ルールを作成するには、事前に[制限付きパブリック プレビューに登録してください](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4mEEwKhLjlBjU3ziDwLH-pURDk2NjMzUTVEVzU5UU1XUlRXSTlHSlkxQS4u)。 この登録を行わないと、エクスポートは機能しません。

### <a name="event-hub"></a>イベント ハブ
データは、Azure Monitor に到達すると、ほぼリアルタイムでイベント ハブに送信されます。 イベント ハブは、エクスポートするデータ型ごとに作成され、*am-* の後にテーブルの名前が続く名前が付けられます。 たとえば、テーブル *SecurityEvent* は、*am-SecurityEvent* という名前のイベント ハブに送信されます。 エクスポートされたデータを特定のイベント ハブに到達させる場合や、47 文字の制限を超える名前の付いたテーブルがある場合は、独自のイベント ハブ名を指定して、定義されたテーブルのすべてのデータをそれにエクスポートすることができます。

> [!IMPORTANT]
> [名前空間あたりサポートされるイベント ハブの数は 10](../../event-hubs/event-hubs-quotas.md#common-limits-for-all-tiers) です。 10 を超えるテーブルをエクスポートする場合は、独自のイベント ハブ名を指定して、すべてのテーブルをそのイベント ハブにエクスポートします。

考慮事項:
1. "Basic" イベント ハブ SKU では、下のほうのイベント サイズ[制限](../../event-hubs/event-hubs-quotas.md#basic-vs-standard-tiers)がサポートされます。ワークスペースの一部のログはそれを超過し、削除されることがありまする "Standard" または "Dedicated" イベント ハブをエクスポート先として使用することをお勧めします。
2. 多くの場合、エクスポートされるデータの量は時間の経過と共に増加します。そのため、より高い転送速度を処理し、調整シナリオやデータ待ち時間を回避するために、イベント ハブのスケールを拡大する必要があります。 Event Hubs の自動インフレ機能を使用して、自動的にスケールアップし、スループット ユニットの数を増やすことで、使用量のニーズを満たす必要があります。 詳細については、「[Azure Event Hubs のスループット ユニットを自動的にスケールアップする](../../event-hubs/event-hubs-auto-inflate.md)」参照してください。

## <a name="prerequisites"></a>前提条件
Log Analytics のデータ エクスポートを構成する前に、次の前提条件が揃っている必要があります。

- ストレージ アカウントまたはイベント ハブは既に作成済みで、Log Analytics ワークスペースと同じリージョンに配置されている必要があります。 対象のデータを他のストレージ アカウントにレプリケートする必要がある場合は、[Azure Storage の冗長性オプション](../../storage/common/storage-redundancy.md)のいずれかを使用できます。  
- ストレージ アカウントは StorageV1 または StorageV2 である必要があります。 従来のストレージはサポートされていません  
- 選択したネットワークからのアクセスを許可するように対象のストレージ アカウントを構成した場合は、対象のストレージへの書き込みを Azure Monitor に許可するようそのストレージ アカウントの設定に例外を追加する必要があります。

## <a name="enable-data-export"></a>データ エクスポートを有効にする
Log Analytics のデータ エクスポートを有効にするには、次の手順を実行する必要があります。 それぞれの詳細については、以降のセクションを参照してください。

- リソースプロバイダーを登録する。
- 信頼された Microsoft サービスを許可する。
- エクスポートするテーブルとそのエクスポート先を定義するデータ エクスポート ルールを 1 つ以上作成する。

### <a name="register-resource-provider"></a>リソース プロバイダーの登録
Log Analytics のデータ エクスポートを有効にするには、次の Azure リソース プロバイダーを対象のサブスクリプションに登録する必要があります。 

- Microsoft.Insights

このリソース プロバイダーは、おそらくほとんどの Azure Monitor ユーザー用に既に登録されています。 確認するには、Azure portal で **[サブスクリプション]** に移動します。 対象のサブスクリプションを選択し、メニューの **[設定]** セクションで **[リソース プロバイダー]** をクリックします。 **Microsoft.Insights** を見つけます。 その状態が **[登録済み]** の場合は、既に登録されています。 そうでない場合は、 **[登録]** をクリックして登録します。

また、「[Azure リソース プロバイダーと種類](../../azure-resource-manager/management/resource-providers-and-types.md)」で説明されているように、リソース プロバイダーを登録するために使用可能な方法のいずれかを使用できます。 PowerShell を使用したサンプル コマンドを次に示します。

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.insights
```

### <a name="allow-trusted-microsoft-services"></a>信頼された Microsoft サービスを許可する
選択したネットワークからのアクセスを許可するように対象のストレージ アカウントが構成した場場合は、そのアカウントへの書き込みを Azure Monitor に許可するための例外を追加する必要があります。 対象のストレージ アカウントの **[ファイアウォールと仮想ネットワーク]** から、 **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** を選択します。

[![ストレージ アカウントの [ファイアウォールと仮想ネットワーク]](media/logs-data-export/storage-account-vnet.png)](media/logs-data-export/storage-account-vnet.png#lightbox)

### <a name="create-or-update-data-export-rule"></a>データ エクスポート ルールを作成または更新する
データ エクスポート ルールは、データをエクスポートするテーブルとその宛先を定義します。 現在、その宛先ごとに 1 つのルールを作成できます。

エクスポート ルールには、ワークスペースにあるテーブルを含める必要があります。 ワークスペース内で使用できるテーブルの一覧を表示するには、このクエリを実行します。

```kusto
find where TimeGenerated > ago(24h) | distinct Type
```

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

N/A

# <a name="powershell"></a>[PowerShell](#tab/powershell)

該当なし

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

CLI を使用してストレージ アカウントに対するデータ エクスポート ルールを作成するには、次のコマンドを使用します。

```azurecli
$storageAccountResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $storageAccountResourceId
```

CLI を使用してイベント ハブに対するデータ エクスポート ルールを作成するには、次のコマンドを使用します。 テーブルごとに個別のイベント ハブが作成されます。

```azurecli
$eventHubsNamespacesResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.EventHub/namespaces/namespaces-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubsNamespacesResourceId
```

CLI を使用して特定のイベント ハブに対するデータ エクスポート ルールを作成するには、次のコマンドを使用します。 すべてのテーブルは、指定されたイベント ハブ名にエクスポートされます。 

```azurecli
$eventHubResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.EventHub/namespaces/namespaces-name/eventHubName/eventhub-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubResourceId
```

# <a name="rest"></a>[REST](#tab/rest)

REST API を使用してデータ エクスポート ルールを作成するには、次の要求を使用します。 この要求では、ベアラー トークン承認とコンテンツ タイプ application/json を使用する必要があります。

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

この要求の本文では、テーブルのエクスポート先を指定します。 ストレージ アカウントを対象とした REST 要求のサンプル本文を次に示します。

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
            "table1",
            "table2" 
        ],
        "enable": true
    }
}
```

イベント ハブを対象とした REST 要求のサンプル本文を次に示します。

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
}
```

イベント ハブ名が指定されているイベント ハブを対象とした REST 要求のサンプル本文を次に示します。 この場合、エクスポートされたすべてのデータがこのイベント ハブに送信されます。

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name",
            "metaData": {
                "EventHubName": "eventhub-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
  }
}
```

# <a name="template"></a>[テンプレート](#tab/json)

テンプレートを使用してストレージ アカウントに対するデータ エクスポート ルールを作成するには、次のコマンドを使用します。

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "storageAccountRuleName": {
            "defaultValue": "storage-account-rule-name",
            "type": "string"
        },
        "storageAccountResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
                {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/' , parameters('storageAccountRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('storageAccountResourceId')]"
                      },
                      "tableNames": [
                          "Heartbeat",
                          "InsightsMetrics",
                          "VMConnection",
                          "Usage"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

テンプレートを使用してイベント ハブに対するデータ エクスポート ルールを作成するには、次のコマンドを使用します。 テーブルごとに個別のイベント ハブが作成されます。

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "eventhubRuleName": {
            "defaultValue": "event-hub-rule-name",
            "type": "string"
        },
        "namespacesResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/microsoft.eventhub/namespaces/namespaces-name",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
              {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/', parameters('eventhubRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('namespacesResourceId')]"
                      },
                      "tableNames": [
                          "Usage",
                          "Heartbeat"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

テンプレートを使用して特定のイベント ハブに対するデータ エクスポート ルールを作成するには、次のコマンドを使用します。 すべてのテーブルは、指定されたイベント ハブ名にエクスポートされます。

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "eventhubRuleName": {
            "defaultValue": "event-hub-rule-name",
            "type": "string"
        },
        "namespacesResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/microsoft.eventhub/namespaces/namespaces-name",
            "type": "String"
        },
        "eventhubName": {
            "defaultValue": "event-hub-name",
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
              {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/', parameters('eventhubRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('namespacesResourceId')]",
                          "metaData": {
                              "eventHubName": "[parameters('eventhubName')]"
                          }
                      },
                      "tableNames": [
                          "Usage",
                          "Heartbeat"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

---

## <a name="view-data-export-rule-configuration"></a>データ エクスポート ルールの構成の表示

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

N/A

# <a name="powershell"></a>[PowerShell](#tab/powershell)

該当なし

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

CLI を使用してデータ エクスポート ルールの構成を表示するには、次のコマンドを使用します。

```azurecli
az monitor log-analytics workspace data-export show --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

# <a name="rest"></a>[REST](#tab/rest)

REST API を使用してデータ エクスポート ルールの構成を表示するには、次の要求を使用します。 この要求では、ベアラー トークン承認を使用する必要があります。

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

# <a name="template"></a>[テンプレート](#tab/json)

該当なし

---

## <a name="disable-an-export-rule"></a>エクスポート ルールを無効にする

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

N/A

# <a name="powershell"></a>[PowerShell](#tab/powershell)

該当なし

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

テストの実行中など、データを一定期間保持する必要がないときにエクスポートを停止できるように、エクスポート ルールを無効にすることができます。 CLI を使用してデータ エクスポート ルールを無効にするには、次のコマンドを使用します。

```azurecli
az monitor log-analytics workspace data-export update --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --enable false
```

# <a name="rest"></a>[REST](#tab/rest)

テストの実行中など、データを一定期間保持する必要がないときにエクスポートを停止できるように、エクスポート ルールを無効にすることができます。 REST API を使用してデータ エクスポート ルールを無効にするには、次の要求を使用します。 この要求では、ベアラー トークン承認を使用する必要があります。

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
"table1",
    "table2" 
        ],
        "enable": false
    }
}
```

# <a name="template"></a>[テンプレート](#tab/json)

テストの実行中など、データを一定期間保持する必要がないときにエクスポートを停止できるように、エクスポート ルールを無効にすることができます。 データ エクスポートを無効にするには、テンプレートに ```"enable": false``` を設定します。

---

## <a name="delete-an-export-rule"></a>エクスポート ルールを削除する

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

N/A

# <a name="powershell"></a>[PowerShell](#tab/powershell)

該当なし

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

CLI を使用してデータ エクスポート ルールを削除するには、次のコマンドを使用します。

```azurecli
az monitor log-analytics workspace data-export delete --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

# <a name="rest"></a>[REST](#tab/rest)

REST API を使用してデータ エクスポート ルールを削除するには、次の要求を使用します。 この要求では、ベアラー トークン承認を使用する必要があります。

```rest
DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

# <a name="template"></a>[テンプレート](#tab/json)

該当なし

---

## <a name="view-all-data-export-rules-in-a-workspace"></a>ワークスペース内のすべてのデータ エクスポート ルールを表示する

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

N/A

# <a name="powershell"></a>[PowerShell](#tab/powershell)

該当なし

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

CLI を使用してワークスペース内のすべてのデータ エクスポート ルールを表示するには、次のコマンドを使用します。

```azurecli
az monitor log-analytics workspace data-export list --resource-group resourceGroupName --workspace-name workspaceName
```

# <a name="rest"></a>[REST](#tab/rest)

REST API を使用してワークスペース内のすべてのデータ エクスポート ルールを表示するには、次の要求を使用します。 この要求では、ベアラー トークン承認を使用する必要があります。

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports?api-version=2020-08-01
```

# <a name="template"></a>[テンプレート](#tab/json)

該当なし

---

## <a name="unsupported-tables"></a>サポート対象外のテーブル
サポート対象外のテーブルがデータ エクスポート ルールに含まれている場合、構成は成功しますが、そのテーブルのデータはエクスポートされません。 そのテーブルが後でサポートされるようになると、その時点でテーブルのデータがエクスポートされます。

存在しないテーブルがデータ エクスポート ルールに含まれている場合、"Table <tableName> does not exist in the workspace (テーブル <tableName> がワークスペースに存在しません)" エラーで失敗します。


## <a name="supported-tables"></a>サポート対象のテーブル
サポート対象のテーブルは、現在、以下に記載されているものに限定されています。 制限事項が指定されている場合を除き、テーブルのすべてのデータがエクスポートされます。 この一覧は、その他のテーブルのサポートが追加されると更新されます。


| テーブル | 制限事項 |
|:---|:---|
| AADDomainServicesAccountLogon |  |
| AADDomainServicesAccountManagement |  |
| AADDomainServicesDirectoryServiceAccess |  |
| AADDomainServicesLogonLogoff |  |
| AADDomainServicesPolicyChange |  |
| AADDomainServicesPrivilegeUse |  |
| AADManagedIdentitySignInLogs |  |
| AADNonInteractiveUserSignInLogs |  |
| AADProvisioningLogs |  |
| AADServicePrincipalSignInLogs |  |
| ABSBotRequests |  |
| ACSBillingUsage |  |
| ACSSMSIncomingOperations |  |
| ADAssessmentRecommendation |  |
| ADFActivityRun |  |
| ADFPipelineRun |  |
| ADFTriggerRun |  |
| ADReplicationResult |  |
| ADSecurityAssessmentRecommendation |  |
| ADTDigitalTwinsOperation |  |
| ADTEventRoutesOperation |  |
| ADTModelsOperation |  |
| ADTQueryOperation |  |
| ADXCommand |  |
| ADXQuery |  |
| AegDeliveryFailureLogs |  |
| AegPublishFailureLogs |  |
| アラート: |  |
| AmlOnlineEndpointConsoleLog |  |
| ApiManagementGatewayLogs |  |
| AppCenterError |  |
| AppPlatformSystemLogs |  |
| AppServiceAppLogs |  |
| AppServiceAuditLogs |  |
| AppServiceConsoleLogs |  |
| AppServiceFileAuditLogs |  |
| AppServiceHTTPLogs |  |
| AppServicePlatformLogs |  |
| AuditLogs |  |
| AutoscaleEvaluationsLog |  |
| AutoscaleScaleActionsLog |  |
| AWSCloudTrail |  |
| AzureAssessmentRecommendation |  |
| AzureDevOpsAuditing |  |
| BehaviorAnalytics |  |
| BlockchainApplicationLog |  |
| BlockchainProxyLog |  |
| CommonSecurityLog |  |
| ComputerGroup |  |
| ConfigurationData | 部分的なサポート – データの一部は、エクスポートがサポートされていない内部サービスを介して取り込まれます。 現在、この部分はエクスポートに含まれません。 |
| ContainerImageInventory |  |
| ContainerInventory |  |
| ContainerLog |  |
| ContainerNodeInventory |  |
| ContainerServiceLog |  |
| CoreAzureBackup |  |
| DatabricksAccounts |  |
| DatabricksClusters |  |
| DatabricksDBFS |  |
| DatabricksInstancePools |  |
| DatabricksJobs |  |
| DatabricksNotebook |  |
| DatabricksSecrets |  |
| DatabricksSQLPermissions |  |
| DatabricksSSH |  |
| DatabricksWorkspace |  |
| DnsEvents |  |
| DnsInventory |  |
| Dynamics365Activity |  |
| イベント | 部分的なサポート – このテーブルに対するデータの一部は、ストレージ アカウントを介して取り込まれます。 現在、この部分はエクスポートに含まれません。 |
| ExchangeAssessmentRecommendation |  |
| FailedIngestion |  |
| FunctionAppLogs |  |
| Heartbeat |  |
| HuntingBookmark |  |
| InsightsMetrics | 部分的なサポート – データの一部は、エクスポートがサポートされていない内部サービスを介して取り込まれます。 現在、この部分はエクスポートに含まれません。 |
| IntuneAuditLogs |  |
| IntuneDevices |  |
| IntuneOperationalLogs |  |
| KubeEvents |  |
| KubeHealth |  |
| KubeMonAgentEvents |  |
| KubeNodeInventory |  |
| KubePodInventory |  |
| KubeServices |  |
| LAQueryLogs |  |
| McasShadowItReporting |  |
| MicrosoftAzureBastionAuditLogs |  |
| MicrosoftDataShareReceivedSnapshotLog |  |
| MicrosoftDataShareSentSnapshotLog |  |
| MicrosoftHealthcareApisAuditLogs |  |
| NWConnectionMonitorPathResult |  |
| NWConnectionMonitorTestResult |  |
| OfficeActivity | 部分的なサポート – データの一部は、Webhook を介して O365 から LA に取り込まれます。 現在、この部分はエクスポートに含まれません。 |
| Operation | 部分的なサポート – データの一部は、エクスポートがサポートされていない内部サービスを介して取り込まれます。 現在、この部分はエクスポートに含まれません。 |
| Perf | 部分的なサポート – 現在、Windows のパフォーマンス データのみがサポートされています。 現在、Linux のパフォーマンス データはエクスポートに含まれません。 |
| PowerBIDatasetsTenant |  |
| PowerBIDatasetsWorkspace |  |
| PowerBIDatasetsWorkspacePreview |  |
| SCCMAssessmentRecommendation |  |
| SCOMAssessmentRecommendation |  |
| SecurityAlert |  |
| SecurityBaseline |  |
| SecurityBaselineSummary |  |
| SecurityDetection |  |
| SecurityEvent | 部分的なサポート – このテーブルに対するデータの一部は、ストレージ アカウントを介して取り込まれます。 現在、この部分はエクスポートに含まれません。 |
| SecurityIncident |  |
| SecurityIoTRawEvent |  |
| SecurityNestedRecommendation |  |
| SecurityRecommendation |  |
| SfBAssessmentRecommendation |  |
| SfBOnlineAssessmentRecommendation |  |
| SharePointOnlineAssessmentRecommendation |  |
| SignalRServiceDiagnosticLogs |  |
| SigninLogs |  |
| SPAssessmentRecommendation |  |
| SQLAssessmentRecommendation |  |
| SucceededIngestion |  |
| SynapseBigDataPoolApplicationsEnded |  |
| SynapseBuiltinSqlPoolRequestsEnded |  |
| SynapseGatewayApiRequests |  |
| SynapseIntegrationActivityRuns |  |
| SynapseIntegrationPipelineRuns |  |
| SynapseIntegrationTriggerRuns |  |
| SynapseRbacOperations |  |
| SynapseSqlPoolDmsWorkers |  |
| SynapseSqlPoolExecRequests |  |
| SynapseSqlPoolRequestSteps |  |
| SynapseSqlPoolSqlRequests |  |
| SynapseSqlPoolWaits |  |
| syslog | 部分的なサポート – このテーブルに対するデータの一部は、ストレージ アカウントを介して取り込まれます。 現在、この部分はエクスポートに含まれません。 |
| ThreatIntelligenceIndicator |  |
| 更新 | 部分的なサポート – データの一部は、エクスポートがサポートされていない内部サービスを介して取り込まれます。 現在、この部分はエクスポートに含まれません。 |
| UpdateRunProgress |  |
| UpdateSummary |  |
| 使用方法 |  |
| Watchlist |  |
| WindowsEvent |  |
| WindowsFirewall |  |
| WireData | 部分的なサポート – データの一部は、エクスポートがサポートされていない内部サービスを介して取り込まれます。 現在、この部分はエクスポートに含まれません。 |
| WVDCheckpoints |  |
| WVDConnections |  |
| WVDErrors |  |
| WVDFeeds |  |
| WVDManagement |  |


## <a name="next-steps"></a>次のステップ

- [Azure Data Explorer からエクスポートされたデータのクエリを実行する](../logs/azure-data-explorer-query-storage.md)。
