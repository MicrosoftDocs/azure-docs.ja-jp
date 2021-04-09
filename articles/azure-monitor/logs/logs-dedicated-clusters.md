---
title: Azure Monitor ログ専用クラスター
description: 1 日に 1 TB を超える監視データを取り込むお客様は、共有クラスターではなく専用クラスターを使用できます
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 09/16/2020
ms.openlocfilehash: 1fbb8f82366ee961d10ce8a6bc098128bad6555a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041010"
---
# <a name="azure-monitor-logs-dedicated-clusters"></a>Azure Monitor ログ専用クラスター

Azure Monitor ログ専用クラスターは、Azure Monitor ログのお客様向けの高度な機能を有効にするデプロイ オプションです。 専用クラスターを使用しているお客様は、これらのクラスターでホストされるワークスペースを選択できます。

専用クラスターを必要とする機能は次のとおりです。

- **[カスタマー マネージド キー](../logs/customer-managed-keys.md)** - お客様によって指定、管理されるキーを利用してクラスター データを暗号化します。
- **[Lockbox](../logs/customer-managed-keys.md#customer-lockbox-preview)** - お客様は、Microsoft サポート エンジニアのデータ アクセス要求を制御できます。
- **[二重暗号化](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)** を使用すると、暗号化アルゴリズムまたはキーのいずれかが侵害される可能性があるシナリオから保護されます。 この場合は、追加の暗号化レイヤーによって引き続きデータが保護されます。
- **[マルチワークスペース](../logs/cross-workspace-query.md)** - お客様が運用に複数のワークスペースを使用している場合、専用クラスターの使用をお勧めします。 すべてのワークスペースが同じクラスター上にある場合は、クロスワークスペース クエリの実行速度が速くなります。 割り当てられた容量予約レベルですべてのクラスターのインジェストが考慮されるため、専用クラスターを使用する方がコスト効率が高くなる可能性もあります。その一部が小さく、容量予約の割引の対象になっていない場合でも、すべてのワークスペースに適用されます。

専用クラスターでは、1 日あたり少なくとも 1 TB のデータ インジェストの容量を使用してコミットを行う必要があります。 専用クラスターへの移行は簡単です。 データの損失やサービスの中断はありません。 

## <a name="management"></a>管理 

専用クラスターは、Azure Monitor ログ クラスターを表す Azure リソースを介して管理されます。 すべての操作は、PowerShell または REST API を使用して、このリソースに対して行われます。

クラスターを作成したら、そのクラスターを構成し、ワークスペースにリンクすることができます。 ワークスペースがクラスターにリンクされている場合、ワークスペースに送信された新しいデータはクラスターに存在します。 クラスターにリンクできるのは、クラスターと同じリージョンにあるワークスペースだけです。 クラスターからワークスペースのリンクを解除できますが、いくつかの制限があります。 これらの制限事項の詳細についてもこの記事に記載されています。 

専用クラスターに取り込まれたデータは、2 回暗号化されます。Microsoft のマネージド キーまたは[カスタマー マネージド キー](../logs/customer-managed-keys.md)を使用してサービス レベルで一度暗号化され、2 つの異なる暗号化アルゴリズムと 2 つの異なるキーを使用してインフラストラクチャ レベルで一度暗号化されます。 [二重暗号化](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)を使用すると、暗号化アルゴリズムまたはキーのいずれかが侵害される可能性があるシナリオから保護されます。 この場合は、追加の暗号化レイヤーによって引き続きデータが保護されます。 専用クラスターを使用すると、[ロックボックス](../logs/customer-managed-keys.md#customer-lockbox-preview) コントロールを使用してデータを保護することもできます。

クラスター レベルでのすべての操作には、クラスターに対する `Microsoft.OperationalInsights/clusters/write` アクション権限が必要です。 このアクセス許可を付与するには、`*/write` アクションを含む所有者または共同作成者、または `Microsoft.OperationalInsights/*` アクションを含む Log Analytics 共同作成者ロールを使用します。 Log Analytics のアクセス許可の詳細については、「[Azure Monitor でログ データとワークスペースへのアクセスを管理する](./manage-access.md)」を参照してください。 


## <a name="cluster-pricing-model"></a>クラスターの価格モデル

Log Analytics 専用クラスターには、1000 GB/日以上の容量予約価格モデルを使用します。 予約レベルを超える使用量は、従量課金制で請求されます。  容量予約の価格情報については、[Azure Monitor の価格に関するページ]( https://azure.microsoft.com/pricing/details/monitor/)を参照してください。  

クラスター容量予約レベルは、`Sku` の下にある `Capacity` パラメーターを使用して、Azure Resource Manager でプログラムによって構成します。 `Capacity` は GB 単位で指定し、1000 GB/日以上の値を 100 GB/日の単位で設定できます。

クラスターでの使用については、2 つの課金モードがあります。 これらは、ご自分のクラスターを構成するときに、`billingType` パラメーターで指定できます。 

1. **クラスター**: この場合 (既定)、取り込まれたデータに対してクラスター レベルで課金されます。 クラスターに関連付けられている各ワークスペースが取り込んだデータ量が集計され、クラスターの日次請求が計算されます。 

2. **ワークスペース**: ご使用のクラスターの容量予約コストは、クラスター内のワークスペースに比例します (各ワークスペースに対する [Azure Security Center](../../security-center/index.yml) からのノードごとの割り当てを考慮した後)。

ワークスペースで従来のノードごとの価格レベルが使用されている場合、クラスターにリンクされると、クラスターの容量予約に対するデータ インジェストに基づいて課金され、ノードごとには課金されなくなります。 Azure Security Center からのノードごとのデータ割り当ては引き続き適用されます。

Log Analytics 専用クラスターの課金の詳細については、[こちら]( https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-dedicated-clusters)をご覧ください。

## <a name="asynchronous-operations-and-status-check"></a>非同期操作と状態のチェック

構成手順の一部はすぐに完了できないため、非同期的に実行されます。 応答の状態には、次のいずれかが含まれます。'InProgress'、'Updating'、'Deleting'、'Succeeded、'Failed' (エラー コードを伴う)。 REST を使用している場合、応答では最初に HTTP 状態コード 202 (承認済み) と Azure-AsyncOperation プロパティを持つヘッダーが返されます。

```JSON
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

Azure-AsyncOperation ヘッダー値に GET 要求を送信することにより、非同期操作の状態を確認できます。

```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

## <a name="creating-a-cluster"></a>クラスターを作成する

最初にクラスター リソースを作成して、専用クラスターの作成を開始します。

次のプロパティを指定する必要があります。

- **ClusterName**:管理目的で使用されます。 この名前はユーザーには公開されません。
- **ResourceGroupName**:すべての Azure リソースと同様に、クラスターはリソース グループに属します。 クラスターは通常、組織内の多くのチームによって共有されるため、中央の IT リソース グループを使用することをお勧めします。 設計に関するその他の考慮事項については、「[Azure Monitor ログのデプロイの設計](../logs/design-logs-deployment.md)」を参照してください。
- **[場所]** :クラスターは特定の Azure リージョンにあります。 このクラスターにリンクできるのは、このリージョンにあるワークスペースだけです。
- **SkuCapacity**:"*クラスター*" リソースを作成するときに、"*容量予約*" レベル (sku) を指定する必要があります。 "*容量予約*" レベルは、1 日あたり 1,000 GB から 3,000 GB の範囲で指定できます。 必要に応じて、後から 100 刻みで更新できます。 1 日あたり 3,000 GB を超える容量予約レベルが必要な場合は、LAIngestionRate@microsoft.com までお問い合わせください。 クラスターのコストの詳細については、[Log Analytics クラスターのコストの管理](./manage-cost-storage.md#log-analytics-dedicated-clusters)に関するページを参照してください。

"*クラスター*" リソースを作成した後、*sku*、*keyVaultProperties、*billingType* などの追加のプロパティを編集できます。 詳細については、以下を参照してください。

リージョンごと、サブスクリプションごとに最大 2 つのアクティブ クラスターを使用できます。 クラスターは、削除されても、14 日間は予約されています。 リージョンごと、サブスクリプションごとに最大 4 つの (アクティブな、または最近削除された) 予約済みクラスターを使用できます。

> [!WARNING]
> クラスターの作成によって、リソース割り当てとプロビジョニングがトリガーされます。 この操作が完了するまで数時間かかることがあります。 非同期的に実行することをお勧めします。

クラスターを作成するユーザー アカウントには、Azure リソースの作成に関する標準的なアクセス許可 `Microsoft.Resources/deployments/*` およびクラスターの書き込みアクセス許可 `Microsoft.OperationalInsights/clusters/write` が必要です。このために、ロールの割り当てで、この特定のアクション、`Microsoft.OperationalInsights/*` または `*/write` を使用します。

### <a name="create"></a>作成 

**PowerShell**

```powershell
New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -Location {region-name} -SkuCapacity {daily-ingestion-gigabyte} -AsJob

# Check when the job is done
Get-Job -Command "New-AzOperationalInsightsCluster*" | Format-List -Property *
```

**REST**

*Call* 
```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
  "identity": {
    "type": "systemAssigned"
    },
  "sku": {
    "name": "capacityReservation",
    "Capacity": 1000
    },
  "properties": {
    "billingType": "cluster",
    },
  "location": "<region-name>",
}
```

*Response*

202 (承認済み) とヘッダーである必要があります。

### <a name="check-cluster-provisioning-status"></a>クラスターのプロビジョニング状態を確認する

Log Analytics クラスターのプロビジョニングは、完了するまでに時間がかかります。 プロビジョニングの状態を確認するには、いくつかの方法があります。

- リソース グループ名を指定して Get-AzOperationalInsightsCluster PowerShell コマンドを実行し、ProvisioningState プロパティを確認します。 この値は、プロビジョニング中は *ProvisioningAccount*、完了時は *Succeeded* になります。
  ```powershell
  New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} 
  ```

- 応答から Azure-AsyncOperation URL 値をコピーし、非同期操作と状態のチェックに従います。

- *クラスター* リソースに GET 要求を送信し、*provisioningState* 値を確認します。 この値は、プロビジョニング中は *ProvisioningAccount*、完了時は *Succeeded* になります。

   ```rst
   GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
   Authorization: Bearer <token>
   ```

   **Response**

   ```json
   {
     "identity": {
       "type": "SystemAssigned",
       "tenantId": "tenant-id",
       "principalId": "principal-id"
       },
     "sku": {
       "name": "capacityReservation",
       "capacity": 1000,
       "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
       },
     "properties": {
       "provisioningState": "ProvisioningAccount",
       "billingType": "cluster",
       "clusterId": "cluster-id"
       },
     "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
     "name": "cluster-name",
     "type": "Microsoft.OperationalInsights/clusters",
     "location": "region-name"
   }
   ```

*principalId* GUID は、"*クラスター*" リソースのマネージド ID サービスによって生成されます。

## <a name="link-a-workspace-to-cluster"></a>ワークスペースをクラスターにリンクする

ワークスペースが専用クラスターにリンクされている場合、ワークスペースに取り込まれた新しいデータは新しいクラスターにルーティングされ、既存のデータは既存のクラスターに残ります。 専用クラスターがカスタマー マネージド キー (CMK) を使用して暗号化されている場合は、新しいデータのみがキーで暗号化されます。 この違いはユーザーから分離されています。ユーザーは、システムがバックエンドでクラスター間クエリを実行している間、通常どおりにワークスペースを照会するだけです。

クラスターは最大 100 のワークスペースにリンクできます。 リンクされたワークスペースは、クラスターと同じリージョンにあります。 システムのバックエンドを保護し、データの断片化を避けるために、ワークスペースを 1 か月に 3 回以上クラスターにリンクすることはできません。

リンク操作を実行するには、ワークスペースと "*クラスター*" リソースの両方に対する次の "書き込み" アクセス許可を持っている必要があります。

- ワークスペース:*Microsoft.OperationalInsights/workspaces/write*
- "*クラスター*" リソース:*Microsoft.OperationalInsights/clusters/write*

請求の側面を除き、リンクされたワークスペースは、データ保有期間などの独自の設定を保持します。
ワークスペースとクラスターは、異なるサブスクリプションに配置できます。 Azure Lighthouse を使用して両方のテナントを 1 つのテナントにマップすると、ワークスペースとクラスターが異なるテナントに配置される可能性があります。

クラスターの操作と同様に、ワークスペースのリンクは、Log Analytics クラスターのプロビジョニングが完了した後にのみ実行できます。

> [!WARNING]
> ワークスペースをクラスターにリンクするには、複数のバックエンド コンポーネントを同期して、キャッシュのハイドレーションを確保する必要があります。 この操作が完了するまでに最大 2 時間かかる場合があります。 非同期的に実行することをお勧めします。


**PowerShell**

次の PowerShell コマンドを使用して、クラスターにリンクします。

```powershell
# Find cluster resource ID
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name}).id

# Link the workspace to the cluster
Set-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId -AsJob

# Check when the job is done
Get-Job -Command "Set-AzOperationalInsightsLinkedService" | Format-List -Property *
```


**REST**

次の REST 呼び出しを使用して、クラスターにリンクします。

*Send*

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-08-01 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

*Response*

202 (承認済み) とヘッダー。

### <a name="check-workspace-link-status"></a>ワークスペースのリンク状態を確認する
  
カスタマー マネージド キーを使用する場合、取り込まれたデータは、関連付け操作の後、マネージド キーで暗号化された状態で格納されます。これが完了するには最大 90 分かかることがあります。 

ワークスペースの関連付けの状態は、次の 2 つの方法で確認できます。

- 応答から Azure-AsyncOperation URL 値をコピーし、非同期操作と状態のチェックに従います。

- ワークスペースで Get 操作を実行し、応答の *features* の下に *clusterResourceId* プロパティが存在するかどうかを確認します。

**CLI**

```azurecli
az monitor log-analytics cluster show --resource-group "resource-group-name" --name "cluster-name"
```

**PowerShell**

```powershell
Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

**REST**

*Call*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>?api-version=2020-08-01
Authorization: Bearer <token>
```

*Response*

```json
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-name",
    "provisioningState": "Succeeded",
    "sku": {
      "name": "pricing-tier-name",
      "lastSkuUpdate": "Tue, 28 Jan 2020 12:26:30 GMT"
    },
    "retentionInDays": 31,
    "features": {
      "legacy": 0,
      "searchVersion": 1,
      "enableLogAccessUsingOnlyResourcePermissions": true,
      "clusterResourceId": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name"
    },
    "workspaceCapping": {
      "dailyQuotaGb": -1.0,
      "quotaNextResetTime": "Tue, 28 Jan 2020 14:00:00 GMT",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}
```

## <a name="change-cluster-properties"></a>クラスターのプロパティを変更する

"*クラスター*" リソースを作成し、完全にプロビジョニングされたら、PowerShell または REST API を使用して、クラスター レベルで追加のプロパティを編集できます。 クラスターの作成時に使用できるプロパティ以外の追加のプロパティは、クラスターがプロビジョニングされた後でのみ設定できます。

- **keyVaultProperties** - Azure Key Vault のキーを更新します。 「[キー識別子の詳細を使用してクラスターを更新する](../logs/customer-managed-keys.md#update-cluster-with-key-identifier-details)」をご覧ください。 これには次のパラメーターが含まれています。*KeyVaultUri*、*KeyName*、*KeyVersion*。 
- **billingType** - *billingType* プロパティによって、"*クラスター*" リソースとそのデータの課金の帰属が決まります。
  - **クラスター** (既定) - クラスターの容量予約コストは、"*クラスター*" リソースに帰属します。
  - **ワークスペース** - クラスターの容量予約コストは、クラスター内のワークスペースに比例的に帰属します。その日に取り込まれた合計データが容量予約を下回る場合に使用量の一部が "*クラスター*" リソースに課金されます。 クラスターの価格モデルの詳細については、[Log Analytics 専用クラスター](./manage-cost-storage.md#log-analytics-dedicated-clusters)に関するページを参照してください。 

> [!NOTE]
> *billingType* プロパティは、PowerShell ではサポートされていません。

### <a name="get-all-clusters-in-resource-group"></a>リソース グループ内のすべてのクラスターを取得する
  
**CLI**

```azurecli
az monitor log-analytics cluster list --resource-group "resource-group-name"
```

**PowerShell**

```powershell
Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
```

**REST**

*Call*

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

*Response*
  
  ```json
  {
    "value": [
      {
        "identity": {
          "type": "SystemAssigned",
          "tenantId": "tenant-id",
          "principalId": "principal-Id"
        },
        "sku": {
          "name": "capacityReservation",
          "capacity": 1000,
          "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
          },
        "properties": {
           "keyVaultProperties": {
              "keyVaultUri": "https://key-vault-name.vault.azure.net",
              "keyName": "key-name",
              "keyVersion": "current-version"
              },
          "provisioningState": "Succeeded",
          "billingType": "cluster",
          "clusterId": "cluster-id"
        },
        "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
        "name": "cluster-name",
        "type": "Microsoft.OperationalInsights/clusters",
        "location": "region-name"
      }
    ]
  }
  ```

### <a name="get-all-clusters-in-subscription"></a>サブスクリプション内のすべてのクラスターを取得する

**CLI**

```azurecli
az monitor log-analytics cluster list
```

**PowerShell**

```powershell
Get-AzOperationalInsightsCluster
```

**REST**

*Call*

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
Authorization: Bearer <token>
```
    
*Response*
    
"リソース グループ内のクラスター" に対するものと同じですが、サブスクリプション スコープです。



### <a name="update-capacity-reservation-in-cluster"></a>クラスターの "容量予約" を更新する

リンクされたワークスペースへのデータ量が時間の経過と共に変化し、容量予約のレベルを適切に更新することが必要になる場合があります。 容量は GB 単位で指定し、1000 GB/日以上の値を 100 GB/日の単位で設定できます。 完全な REST 要求本文を指定する必要はなく、SKU を含める必要があることに注意してください。

**CLI**

```azurecli
az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --sku-capacity 1000
```

**PowerShell**

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity 1000
```

**REST**

*Call*

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 2000
    }
  }
  ```

### <a name="update-billingtype-in-cluster"></a>クラスターの billingType を更新する

*billingType* プロパティによって、クラスターとそのデータの課金の属性が決まります。
- *cluster* (既定) - 課金は、クラスター リソースをホストするサブスクリプションに帰属します
- *workspaces* - 課金は、ワークスペースをホストするサブスクリプションに比例的に帰属します

**REST**

*Call*

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ```

### <a name="unlink-a-workspace-from-cluster"></a>クラスターからワークスペースのリンクを解除する

クラスターからワークスペースのリンクを解除することができます。 クラスターからワークスペースのリンクを解除すると、このワークスペースに関連付けられた新しいデータは専用クラスターに送信されません。 また、ワークスペースの課金はクラスター経由で行われなくなります。 リンクを解除したワークスペースの古いデータがクラスターに残っている可能性があります。 このデータがカスタマー マネージド キー (CMK) を使用して暗号化されている場合、Key Vault シークレットは保持されます。 この変更は Log Analytics ユーザーから分離されています。 ユーザーはワークスペースに通常どおりクエリを実行できます。 システムは必要に応じてバックエンドでクラスター間クエリを実行し、ユーザーには何も通知しません。  

> [!WARNING] 
> 特定のワークスペースのリンク操作は 1 か月に 2 回という制限があります。 リンク解除アクションは考慮に時間をかけて計画してください。

**CLI**

```azurecli
az monitor log-analytics workspace linked-service delete --resource-group "resource-group-name" --workspace-name "MyWorkspace" --name cluster
```

**PowerShell**

次の PowerShell コマンドを使用して、クラスターからワークスペースのリンクを解除します。

```powershell
# Unlink a workspace from cluster
Remove-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster
```

### <a name="delete-cluster"></a>クラスターを削除する

専用クラスター リソースは削除できます。 クラスターを削除する前に、すべてのワークスペースのリンクを解除する必要があります。 この操作を実行するには、"*クラスター*" リソースに対する "書き込み" アクセス許可が必要です。 

クラスター リソースが削除されると、物理クラスターは消去と削除のプロセスに入ります。 クラスターを削除すると、クラスターに格納されていたすべてのデータが削除されます。 このデータは、過去にクラスターにリンクされたワークスペースのものである可能性があります。

過去 14 日以内に削除された "*クラスター*" リソースは、論理的な削除状態であり、そのデータで復旧できます。 "*クラスター*" リソースの削除によって、すべてのワークスペースが "*クラスター*" リソースから関連付け解除されているため、回復後にワークスペースを再関連付けする必要があります。 ユーザーが復旧操作を実行することはできません。Microsoft チャネルまたはサポートに問い合わせて、復旧要求を行ってください。

削除後 14 日間クラスター リソース名は予約され、他のリソースでは使用できません。

> [!WARNING] 
> サブスクリプションあたり 3 つのクラスターに制限されています。 アクティブなクラスターと論理的に削除されたクラスターは、この一部としてカウントされます。 クラスターを作成して削除する反復手順は作成しないでください。 Log Analytics バックエンド システムに大きな影響を与えます。

**PowerShell**

次の PowerShell コマンドを使用して、クラスターを削除します。

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

**REST**

次の REST 呼び出しを使用して、クラスターを削除します。

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Response**

  200 OK

## <a name="limits-and-constraints"></a>制限および制約

- リージョンおよびサブスクリプションごとのアクティブ クラスターの最大数は 2 です

- リージョンおよびサブスクリプションごとの (アクティブな、または最近削除された) 予約済みクラスターの最大数は 4 です 

- クラスターにリンクされたワークスペースの最大数は 1000 です

- クラスターにワークスペースをリンクし、その後、リンクを解除することができます。 特定のワークスペースでのワークスペース リンク操作の回数は、30 日間に 2 回に制限されています

- クラスターの別のリソース グループまたはサブスクリプションへの移動は、現時点ではサポートされていません。

- ロックボックスは、現在、中国では使用できません。 

- [二重暗号化](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)は、サポートされているリージョンで 2020 年 10 月以降に作成されたクラスターに対して自動的に構成されます。 クラスターが二重暗号化されるように構成されているかどうかを確認するには、クラスターに対して GET 要求を送信し、二重暗号化が有効になっているクラスターの `isDoubleEncryptionEnabled` の値が `true` かどうかを検査します。 
  - クラスターを作成したときに、「<リージョン名> ではクラスターの二重暗号化がサポートされていません」というエラーが表示された場合でも、REST 要求本文に `"properties": {"isDoubleEncryptionEnabled": false}` を追加すると、二重暗号化なしでクラスターを作成できます。
  - クラスターの作成後に、二重暗号化の設定を変更することはできません。

## <a name="troubleshooting"></a>トラブルシューティング

- クラスターを作成するときに競合エラーが発生する場合は、過去 14 日以内にクラスターを削除し、それが論理的な削除状態にある可能性があります。 クラスター名は、論理的な削除の期間中は予約されたままであり、その名前で新しいクラスターを作成することはできません。 この名前は、論理的な削除の期間の後、クラスターが完全に削除されたときに解放されます。

- クラスターがプロビジョニング中または更新中の状態のときにクラスターを更新すると、更新は失敗します。

- クラスターの作成、クラスターのキーの更新、クラスターの削除などの一部の操作には長時間かかるため、完了までにしばらく時間がかかる場合があります。 操作の状態を確認するには、次の 2 つの方法があります。
  - REST を使用している場合、応答から Azure-AsyncOperation URL 値をコピーし、「[非同期操作と状態のチェック](#asynchronous-operations-and-status-check)」に従います。
  - GET 要求をクラスターまたはワークスペースに送信し、応答を観察します。 たとえば、リンクされていないワークスペースには、*features* の下に *clusterResourceId* が存在しません。

- 別のクラスターにリンクされている場合、クラスターへのワークスペースのリンクは失敗します。

- エラー メッセージ
  
  クラスターの作成:
  -  400 -- Cluster name is not valid. (400 -- クラスター名が無効です。) クラスター名に含めることができる文字は a から z、A から Z、0 から 9 であり、長さは 3 から 63 文字です。
  -  400 -- The body of the request is null or in bad format. (400 -- 要求の本文が null であるか無効な形式です。)
  -  400 -- SKU name is invalid. (400 -- SKU 名が無効です。) SKU 名を capacityReservation に設定してください。
  -  400 -- Capacity was provided but SKU is not capacityReservation. (400 -- 容量が指定されましたが、SKU は capacityReservation ではありません。) SKU 名を capacityReservation に設定してください。
  -  400 -- Missing Capacity in SKU. (400 -- SKU に容量がありません。) 容量の値を 1000 以上に指定してください。100 (GB) 刻みで指定できます。
  -  400 -- Capacity in SKU is not in range. (400 -- SKU の容量が範囲内ではありません。) 最小 1000 から、ワークスペースの [使用量と推定コスト] で設定可能な許容される最大容量までにする必要があります。
  -  400 -- Capacity is locked for 30 days. (400 -- 容量は 30 日間ロックされます。) 容量の減少は更新の 30 日後に許可されます。
  -  400 -- No SKU was set. (400 -- SKU が設定されていませんでした。) SKU 名を capacityReservation にし、容量の値を 100 (GB) 刻みで 1000 以上に設定してください。
  -  400 -- Identity is null or empty. (400 -- ID が null または空です。) systemAssigned の種類の ID を設定してください。
  -  400 -- KeyVaultProperties are set on creation. (400 -- 作成時に KeyVaultProperties が設定されます。) クラスターの作成後に KeyVaultProperties を更新してください。
  -  400 -- Operation cannot be executed now. (400 -- 現在、操作を実行できません。) 非同期操作は成功以外の状態になっています。 更新操作を実行する前に、クラスターでの操作が完了している必要があります。

  クラスターの更新
  -  400 -- Cluster is in deleting state. (400 -- クラスターは削除中の状態です。) 非同期操作が進行中です。 更新操作を実行する前に、クラスターでの操作が完了している必要があります。
  -  400 -- KeyVaultProperties is not empty but has a bad format. (400 -- KeyVaultProperties は空ではありませんが、形式が無効です。) [キー識別子の更新](../logs/customer-managed-keys.md#update-cluster-with-key-identifier-details)に関するセクションを参照してください。
  -  400 -- Failed to validate key in Key Vault. (400 -- Key Vault 内のキーの検証に失敗しました。) 必要なアクセス許可がないことが原因である可能性があります。または、キーが存在しません。 Key Vault で[キーとアクセス ポリシーを設定](../logs/customer-managed-keys.md#grant-key-vault-permissions)したことを確認してください。
  -  400 -- Key is not recoverable. (400 -- キーは回復不能です。) Key Vault に論理的な削除と消去保護を設定する必要があります。 [Key Vault のドキュメント](../../key-vault/general/soft-delete-overview.md)を参照してください
  -  400 -- Operation cannot be executed now. (400 -- 現在、操作を実行できません。) 非同期操作が完了するまで待ってから、もう一度お試しください。
  -  400 -- Cluster is in deleting state. (400 -- クラスターは削除中の状態です。) 非同期操作が完了するまで待ってから、もう一度お試しください。

  クラスターの取得:
    -  404 -- Cluster not found, the cluster may have been deleted. (404 -- クラスターが見つかりません。クラスターは削除された可能性があります。) クラスターをその名前で作成しようとし、競合が発生した場合、そのクラスターは 14 日間の論理的な削除状態にあります。 回復するためにサポートに連絡するか、別の名前を使用して新しいクラスターを作成できます。 

  クラスターの削除
    -  409 -- Can't delete a cluster while in provisioning state. (409 -- プロビジョニング状態の間は、クラスターを削除できません。) 非同期操作が完了するまで待ってから、もう一度お試しください。

  ワークスペースのリンク:
  -  404 -- ワークスペースが見つかりません。 指定したワークスペースが存在しないか、削除されました。
  -  409 -- ワークスペースのリンクまたはリンク解除操作が進行中です。
  -  400 -- Cluster not found, the cluster you specified doesn’t exist or was deleted. (400 -- クラスターが見つかりません。指定したクラスターが存在しないか、削除されました。) クラスターをその名前で作成しようとし、競合が発生した場合、そのクラスターは 14 日間の論理的な削除状態にあります。 回復するには、サポートにお問い合わせください。

  ワークスペースのリンク解除:
  -  404 -- ワークスペースが見つかりません。 指定したワークスペースが存在しないか、削除されました。
  -  409 -- ワークスペースのリンクまたはリンク解除操作が進行中です。

## <a name="next-steps"></a>次の手順

- [Log Analytics 専用クラスターの課金](./manage-cost-storage.md#log-analytics-dedicated-clusters)について学習します
- [Log Analytics ワークスペースの適切な設計](../logs/design-logs-deployment.md)について学習します