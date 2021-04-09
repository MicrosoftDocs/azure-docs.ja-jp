---
title: Azure Monitor のカスタマー マネージド キー
description: Azure Key Vault キーを使用して Log Analytics ワークスペースのデータを暗号化するように、カスタマー マネージド キーを構成するための情報と手順。
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 01/10/2021
ms.openlocfilehash: 9fdaf42f18c320bf841e710b7066451fca24eaae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030989"
---
# <a name="azure-monitor-customer-managed-key"></a>Azure Monitor のカスタマー マネージド キー 

Azure Monitor のデータは、Microsoft マネージド キーを使用して暗号化されます。 独自の暗号化キーを使用して、ワークスペース内のデータと保存済みクエリを保護できます。 カスタマー マネージド キーを指定すると、そのキーはデータへのアクセスを保護および制御するために使用され、構成されると、ワークスペースに送信されるすべてのデータは Azure Key Vault キーを使用して暗号化されるようになります。 カスタマー マネージド キーを使用すると、アクセス制御をより柔軟に管理できます。

構成の前に「[制限と制約](#limitationsandconstraints)」を確認することをお勧めします。

## <a name="customer-managed-key-overview"></a>カスタマー マネージド キーの概要

[保存時の暗号化](../../security/fundamentals/encryption-atrest.md)は、組織の一般的なプライバシーおよびセキュリティ要件です。 保存時の暗号化は Azure で完全に管理できますが、暗号化および暗号化キーを厳密に管理するさまざまなオプションが提供されています。

Azure Monitor により、Microsoft マネージド キー (MMK) を使用して、すべてのデータおよび保存されたクエリが保存時に暗号化されるようになります。 Azure Monitor には、[Azure Key Vault](../../key-vault/general/overview.md) に格納されている独自のキーを使用した暗号化のオプションも用意されています。これにより、いつでもデータへのアクセスを取り消すことができます。 Azure Monitor での暗号化の使用は、[Azure Storage の暗号化](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption)での運用方法とまったく同じです。

カスタマー マネージド キーは、より高い保護レベルと制御を可能にする[専用のクラスター](./logs-dedicated-clusters.md)で提供されます。 専用クラスターに取り込まれたデータは、2 回暗号化されます。Microsoft のマネージド キーまたはカスタマー マネージド キーを使用してサービス レベルで一度暗号化され、2 つの異なる暗号化アルゴリズムと 2 つの異なるキーを使用してインフラストラクチャ レベルで一度暗号化されます。 [二重暗号化](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)を使用すると、暗号化アルゴリズムまたはキーのいずれかが侵害される可能性があるシナリオから保護されます。 この場合は、追加の暗号化レイヤーによって引き続きデータが保護されます。 専用クラスターを使用すると、[ロックボックス](#customer-lockbox-preview) コントロールを使用してデータを保護することもできます。

過去 14 日間に取り込まれたデータも、効率的なクエリ エンジン操作のためにホットキャッシュ (SSD ベース) で保持されます。 このデータは、カスタマー マネージド キーの構成に関係なく、Microsoft キーで暗号化されたままになりますが、SSD データに対する制御は[キーの失効](#key-revocation)に従います。 2021 年の前半には、カスタマー マネージド キーを使用して SSD データを暗号化できるように準備しています。

Log Analytics 専用クラスターには、1000 GB/日以上の容量予約[価格モデル](./logs-dedicated-clusters.md#cluster-pricing-model)を使用します。

## <a name="how-customer-managed-key-works-in-azure-monitor"></a>Azure Monitor でのカスタマー マネージド キーの動作

Azure Monitor は、マネージド ID を使用して Azure Key Vault にアクセス権を付与します。 Log Analytics クラスターの ID はクラスター レベルでサポートされます。 複数のワークスペースでカスタマー マネージド キーを保護できるようにするために、新しい Log Analytics "*クラスター*" リソースは、Key Vault と Log Analytics のワークスペースの間の中間 ID 接続として実行されます。 クラスターのストレージは、"*クラスター*" リソースに関連付けられているマネージド ID を使用して、Azure Active Directory 経由で Azure Key Vault に対する認証を行います。\' 

カスタマー マネージド キーの構成後、専用クラスターにリンクされたワークスペースに新しく取り込まれたデータは、お客様のキーを使用して暗号化されます。 いつでも、クラスターからワークスペースのリンクを解除できます。 その後、新しいデータが Log Analytics ストレージに取り込まれて Microsoft キーで暗号化されますが、新しいデータと古いデータに対してシームレスにクエリを実行することができます。

> [!IMPORTANT]
> カスタマー マネージド キー機能はリージョン単位で機能します。 Azure Key Vault、クラスター、リンクされた Log Analytics ワークスペースは、同じリージョンに存在している必要がありますが、サブスクリプションは異なっていてもかまいません。

![カスタマー マネージド キーの概要](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Key Vault に対するアクセス許可があるマネージド ID を持つ Log Analytics "*クラスター*" リソース – ID は、土台となる専用の Log Analytics クラスター ストレージに伝達されます
3. 専用の Log Analytics クラスター
4. "*クラスター*" リソースにリンクされたワークスペース 

### <a name="encryption-keys-operation"></a>暗号化キー操作

ストレージ データの暗号化には、次の 3 種類のキーが関係します。

- **KEK** - キー暗号化キー (カスタマー マネージド キー)
- **AEK** - アカウント暗号化キー
- **DEK** - データ暗号化キー

次の規則が適用されます。

- Log Analytics クラスター ストレージ アカウントでは、すべてのストレージ アカウントに対して一意の暗号化キーが生成されます。これは、AEK と呼ばれます。
- AEK は、ディスクに書き込まれた各データ ブロックの暗号化に使用されるキーである DEK を派生させるために使用されます。
- Key Vault でキーを構成し、それをクラスターで参照すると、Azure Storage によって Azure Key Vault に要求が送信され、AEK がラップおよびラップ解除されて、データの暗号化と解読の操作が実行されます。
- KEK が Key Vault から離れることはありません。また、HSM キーの場合はハードウェアから離れることはありません。
- Azure Storage により、"*クラスター*" リソースに関連付けられているマネージド ID を使用して、Azure Active Directory 経由での Azure Key Vault への認証とアクセスが行われます。

### <a name="customer-managed-key-provisioning-steps"></a>カスタマー マネージド キーのプロビジョニング手順

1. Azure Key Vault の作成とキーの格納
1. クラスターの作成
1. Key Vault へのアクセス許可の付与
1. キー識別子の詳細を使用したクラスターの更新
1. Log Analytics ワークスペースのリンク

現在、カスタマー マネージド キーの構成は Azure portal でサポートされておらず、プロビジョニングは [PowerShell](/powershell/module/az.operationalinsights/)、[CLI](/cli/azure/monitor/log-analytics)、または [REST](/rest/api/loganalytics/) の要求を使用して実行できます。

### <a name="asynchronous-operations-and-status-check"></a>非同期操作と状態のチェック

構成手順の一部はすぐに完了できないため、非同期的に実行されます。 応答の `status` は、次のいずれかになります。'InProgress'、'Updating'、'Deleting'、'Succeeded、'Failed' (エラー コードを伴う)。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

該当なし

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

N/A

# <a name="powershell"></a>[PowerShell](#tab/powershell)

該当なし

# <a name="rest"></a>[REST](#tab/rest)

REST を使用している場合、応答では最初に HTTP 状態コード 202 (承認済み) と *Azure-AsyncOperation* プロパティを持つヘッダーが返されます。
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

*Azure-AsyncOperation* ヘッダー値に含まれるエンドポイントに GET 要求を送信することにより、非同期操作の状態を確認できます。
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

---

## <a name="storing-encryption-key-kek"></a>暗号化キー (KEK) の格納

Azure Key Vault を作成するか既存のものを使用して、データの暗号化に使用するキーを生成またはインポートします。 キーを保護し、Azure Monitor のデータへのアクセスを保護するには、Azure Key Vault を回復可能として構成する必要があります。 この構成は Key Vault のプロパティで確認できます。 *[論理的な削除]* と *[Purge protection]\(消去保護\)* の両方を有効にしてください。

![論理的な削除と消去保護の設定](media/customer-managed-keys/soft-purge-protection.png)

これらの設定は、CLI と PowerShell を使用して Key Vault で更新できます。

- [論理的な削除](../../key-vault/general/soft-delete-overview.md)
- [[Purge protection]\(消去保護\)](../../key-vault/general/soft-delete-overview.md#purge-protection) は、論理的な削除の後もシークレットやコンテナーを強制削除から保護します

## <a name="create-cluster"></a>クラスターの作成

クラスターでは、システム割り当てとユーザー割り当ての 2 つの[マネージド ID の種類](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)がサポートされていますが、シナリオに応じて単一の ID をクラスターで定義できます。 
- システム割り当てマネージド ID の方がシンプルで、ID の `type` が "*SystemAssigned*" に設定されている場合、クラスターの作成時に自動的に作成されます。 後でこの ID を使用して、ラップおよびラップ解除操作に必要な Key Vault へのストレージ アクセス権を付与できます。 
  
  システム割り当てマネージド ID に関するクラスターの ID 設定
  ```json
  {
    "identity": {
      "type": "SystemAssigned"
      }
  }
  ```

- クラスターの作成時にカスタマー マネージド キーを定義する場合は、事前に Key Vault でキーを作成し、ユーザー割り当て ID を付与します。その後、これらの設定 (ID の `type` として "*UserAssigned*"、ID の *リソース ID* が設定された `UserAssignedIdentities`) を使用してクラスターを作成します。

  ユーザー割り当てマネージド ID に関するクラスターの ID 設定
  ```json
  {
  "identity": {
  "type": "UserAssigned",
    "userAssignedIdentities": {
      "subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft. ManagedIdentity/UserAssignedIdentities/<cluster-assigned-managed-identity>"
      }
  }
  ```

> [!IMPORTANT]
> Key Vault がプライベート リンク (VNet) に配置されている場合は、ユーザー割り当てマネージド ID を使用できません。 このシナリオでは、システム割り当てマネージド ID を使用できます。

[専用クラスターに関する記事](./logs-dedicated-clusters.md#creating-a-cluster)で示されている手順に従います。 

## <a name="grant-key-vault-permissions"></a>Key Vault アクセス許可を付与する

Key Vault でアクセス ポリシーを作成し、クラスターにアクセス許可を付与します。 これらのアクセス許可は、下層の Azure Monitor ストレージによって使用されます。 Azure portal で Key Vault を開き、 *[アクセス ポリシー]* 、 *[+ アクセス ポリシーの追加]* の順にクリックして、次の設定でポリシーを作成します。

- キーのアクセス許可: *[取得]* 、 *[キーを折り返す]* 、 *[キーの折り返しを解除]* を選択します。
- プリンシパルの選択: クラスターで使用される ID の種類 (システムまたはユーザー割り当てマネージド ID) に応じて、システム割り当てマネージド ID またはユーザー割り当てマネージド ID 名に対してクラスター名またはクラスター プリンシパル ID を入力します。

![Key Vault アクセス許可の付与](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

*[取得]* アクセス許可は、キーを保護し、Azure Monitor データへのアクセスを保護するために、Key Vault が回復可能として構成されていることを確認するために必要です。

## <a name="update-cluster-with-key-identifier-details"></a>キー識別子の詳細を使用してクラスターを更新する

クラスター上のすべての操作には、`Microsoft.OperationalInsights/clusters/write` アクションのアクセス許可が必要です。 このアクセス許可を付与するには、`*/write` アクションを含む所有者または共同作成者、または `Microsoft.OperationalInsights/*` アクションを含む Log Analytics 共同作成者ロールを使用します。

このステップでは、データの暗号化に使用されるキーのバージョンで Azure Monitor ストレージを更新します。 更新すると、新しいキーを使用してストレージ キー (AEK) のラップとラップ解除が行われます。

Azure Key Vault で現在のバージョンのキーを選択して、キー識別子の詳細を取得します。

![Key Vault アクセス許可を付与する](media/customer-managed-keys/key-identifier-8bit.png)

クラスターの KeyVaultProperties を、キー識別子の詳細で更新します。

操作は非同期であり、完了するまでに時間がかかることがあります。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

該当なし

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --key-name "key-name" --key-vault-uri "key-uri" --key-version "key-version"
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version"
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/cluster-name?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
  },
  "sku": {
    "name": "CapacityReservation",
    "capacity": 1000
  }
}
```

**Response**

キーの伝達が完了するまで数分かかります。 更新の状態を確認するには、次の 2 つの方法があります。
1. 応答から Azure-AsyncOperation URL 値をコピーし、[非同期操作と状態のチェック](#asynchronous-operations-and-status-check)に従います。
2. クラスターに GET 要求を送信し、*KeyVaultProperties* のプロパティを調べます。 最近更新されたキーが応答で返されます。

キーの更新が完了すると、GET 要求に対する応答は次のようになります。202 (承認済み) とヘッダー
```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
    },
  "sku": {
    "name": "capacityReservation",
    "capacity": 1000,
    "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
    },
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
      },
    "provisioningState": "Succeeded",
    "billingType": "cluster",
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

---

## <a name="link-workspace-to-cluster"></a>ワークスペースをクラスターにリンクする

> [!IMPORTANT]
> この手順は、Log Analytics クラスターのプロビジョニングが完了した後にのみ実行してください。 プロビジョニングの前にワークスペースをリンクしてデータを取り込むと、取り込まれたデータは削除され、回復できなくなります。

この操作を実行するには、ワークスペースとクラスターの両方に対して、`Microsoft.OperationalInsights/workspaces/write` および `Microsoft.OperationalInsights/clusters/write` が含まれる "書き込み" アクセス許可が必要です。

[専用クラスターに関する記事](./logs-dedicated-clusters.md#link-a-workspace-to-cluster)で示されている手順に従います。

## <a name="key-revocation"></a>キーの失効

> [!IMPORTANT]
> - データへのアクセスを失効させる方法としては、キーを無効にするか、Key Vault でアクセス ポリシーを削除することをお勧めします。
> - クラスターの `identity` `type` を "None" に設定すると、データへのアクセスが失効しますが、クラスターで `identity` を再設定するときにサポート リクエストを開かないと失効を取り消すことができないため、この方法はお勧めできません。

クラスター ストレージは常に 1 時間以内にキーのアクセス許可の変更に対応し、ストレージは使用できなくなります。 クラスターにリンクされているワークスペースに取り込まれた新しいデータは、すべて削除されて復旧できなくなり、データにはアクセスできず、これらのワークスペースへのクエリは失敗します。 クラスターおよびワークスペースが削除されていない限り、以前に取り込まれたデータはストレージに残ります。 アクセスできないデータは、データ保持ポリシーによって管理され、リテンション期間に達すると削除されます。 過去 14 日間に取り込まれたデータも、効率的なクエリ エンジン操作のためにホットキャッシュ (SSD ベース) で保持されます。 これはキーの失効操作で削除され、アクセスできなくなります。

クラスターのストレージでは、Key Vault を定期的にチェックして暗号化キーのラップを解除しようとします。アクセス後、データ インジェストとクエリが 30 分以内に再開されます。

## <a name="key-rotation"></a>キーの交換

カスタマー マネージド キーのローテーションを行うには、Azure Key Vault の新しいキー バージョンでクラスターを明示的に更新する必要があります。 [キー識別子の詳細を使用してクラスターを更新します](#update-cluster-with-key-identifier-details)。 クラスターの新しいキー バージョンを更新しない場合、Log Analytics クラスター ストレージでの暗号化には以前のキーが引き続き使用されます。 クラスターの新しいキーを更新する前に古いキーを無効にしたり削除したりすると、[キーの失効](#key-revocation)状態になります。

アカウント暗号化キー (AEK) は新しいキー暗号化キー (KEK) バージョンによって Key Vault で暗号化されるようになりますが、データは常に AEK によって暗号化されているため、キー ローテーション操作後も、すべてのデータにアクセスできます。

## <a name="customer-managed-key-for-saved-queries"></a>保存されたクエリ用のカスタマー マネージド キー

Log Analytics で使用されるクエリ言語は表現力が豊かで、クエリに追加するコメントまたはクエリ構文に機密情報を含めることができます。 組織によっては、このような情報をカスタマー マネージド キー ポリシーによって保護する必要があり、キーで暗号化された状態でクエリを保存する必要があります。 Azure Monitor では、独自のキーを使用して暗号化された "*保存された検索条件*" および "*ログ アラート*" のクエリを、ワークスペースに接続したときに独自のストレージ アカウントに保存しておくことができます。 

> [!NOTE]
> Log Analytics クエリは、使用されるシナリオに応じて、さまざまなストアに保存できます。 カスタマー マネージド キーの構成に関係なく、次のシナリオでは、Microsoft キー (MMK) を使用してクエリの暗号化が維持されます。Azure Monitor のブック、Azure ダッシュボード、Azure Logic App、Azure Notebooks、および Automation Runbook。

Bring Your Own Storage (BYOS) を使用して、それをワークスペースにリンクすると、サービスによって、"*保存された検索条件*" と "*ログ アラート*" のクエリがストレージ アカウントにアップロードされます。 つまり、Log Analytics クラスター内のデータの暗号化に使用するのと同じキー、または別のキーを使用して、ストレージ アカウントと[保存時の暗号化ポリシー](../../storage/common/customer-managed-keys-overview.md)を制御することを意味します。 ただし、そのストレージ アカウントに関連するコストについては、お客様が責任を負うものとします。 

**クエリにカスタマー マネージド キーを設定する前の考慮事項**
* ワークスペースとストレージ アカウントの両方に対して "書き込み" アクセス許可を持っている必要があります
* ストレージ アカウントは、Log Analytics ワークスペースと同じリージョンに作成してください。
* ストレージ内の *保存された検索条件* はサービス アーティファクトと見なされ、その形式は変更される可能性があります
* 既存の *保存された検索条件* は、ワークスペースから削除されます。 必要な *保存された検索条件* は、構成前にコピーしてください。 [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch) を使用して、"*保存された検索条件*" を表示できます
* クエリ履歴はサポートされていないため、実行したクエリは表示できません
* クエリを保存するために、1 つのストレージ アカウントをワークスペースにリンクすることができますが、これは "*保存された検索条件*" と "*ログ アラート*" のクエリの両方から使用できます
* ダッシュボードへのピン留めはサポートされていません

**保存された検索条件のクエリ用に BYOS を構成する**

"*クエリ*" 用のストレージ アカウントをワークスペースにリンクします。"*保存された検索条件*" のクエリはストレージ アカウントに保存されます。 

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

該当なし

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type Query --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Query -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Query?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Query", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

---

構成が完了すると、新しい *保存された検索条件* クエリがストレージに保存されます。

**ログ アラートのクエリ用の BYOS を構成する**

"*アラート*" 用のストレージ アカウントをワークスペースにリンクします。"*ログ アラート*" のクエリはストレージ アカウントに保存されます。 

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

該当なし

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type ALerts --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Alerts -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Alerts?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Alerts", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

---

構成が完了すると、新しいアラート クエリがストレージに保存されます。

## <a name="customer-lockbox-preview"></a>カスタマー ロックボックス (プレビュー)

お客様は、ロックボックスを使用して、サポート リクエストへの対応時の Microsoft のエンジニアによる顧客データへのアクセス要求を承認または拒否できます。

Azure Monitor を使用すると、Log Analytics 専用クラスターにリンクされているワークスペースのデータに対してこの制御権が与えられます。 ロックボックス コントロールは Log Analytics 専用クラスターに格納されているデータに適用され、ロックボックスで保護されているサブスクリプションの下にあるクラスターのストレージ アカウントに分離されたままになります。  

詳細については、「[Microsoft Azure 用カスタマー ロックボックス](../../security/fundamentals/customer-lockbox-overview.md)」を参照してください。

## <a name="customer-managed-key-operations"></a>カスタマー マネージド キーの操作

カスタマー マネージド キーは専用クラスター上で指定されます。これらの操作については[専用クラスターに関する記事](./logs-dedicated-clusters.md#change-cluster-properties)を参照してください。

- リソース グループ内のすべてのクラスターを取得する  
- サブスクリプション内のすべてのクラスターを取得する
- クラスターの "*容量予約*" を更新する
- クラスターの *billingType* を更新する
- クラスターからワークスペースのリンクを解除する
- クラスターを削除する

## <a name="limitations-and-constraints"></a>制限と制約

- リージョンおよびサブスクリプションごとのクラスターの最大数は 2 です

- クラスターにリンクできるワークスペースの最大数は 1000 です

- クラスターにワークスペースをリンクし、その後、リンクを解除することができます。 特定のワークスペースでのワークスペース リンク操作の回数は、30 日間に 2 回に制限されています

- カスタマー マネージド キーの暗号化は、構成後に新しく取り込まれたデータに適用されます。 構成より前に取り込まれたデータは、Microsoft キーで暗号化されたままになります。 カスタマー マネージド キーの構成の前後に取り込まれたデータのクエリは、シームレスに実行できます。

- Azure Key Vault は、回復可能として構成する必要があります。 これらのプロパティは既定では有効になっておらず、CLI または PowerShell を使用して構成する必要があります。<br>
  - [論理的な削除](../../key-vault/general/soft-delete-overview.md)
  - 論理的な削除の後でもシークレット/コンテナーの強制削除を防ぐには、[消去保護](../../key-vault/general/soft-delete-overview.md#purge-protection)を有効にする必要があります。

- クラスターの別のリソース グループまたはサブスクリプションへの移動は、現時点ではサポートされていません。

- お使いの Azure Key Vault、クラスター、ワークスペースは、同じリージョンで同じ Azure Active Directory (Azure AD) テナント内に存在している必要がありますが、サブスクリプションは異なっていてもかまいません。

- ロックボックスは、現在、中国では使用できません。 

- [二重暗号化](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)は、サポートされているリージョンで 2020 年 10 月以降に作成されたクラスターに対して自動的に構成されます。 クラスターが二重暗号化されるように構成されているかどうかを確認するには、クラスターに対して GET 要求を送信し、二重暗号化が有効になっているクラスターの `isDoubleEncryptionEnabled` の値が `true` かどうかを検査します。 
  - クラスターを作成したときに、「<リージョン名> ではクラスターの二重暗号化がサポートされていません」というエラーが表示された場合でも、REST 要求本文に `"properties": {"isDoubleEncryptionEnabled": false}` を追加すると、二重暗号化なしでクラスターを作成できます。
  - クラスターの作成後に、二重暗号化の設定を変更することはできません。

  - クラスターにユーザー割り当てマネージド ID が設定されている場合、`UserAssignedIdentities` を `None` に設定すると、クラスターが中断され、データにアクセスできなくなりますが、サポート リクエストを開かずに、失効を元に戻して、クラスターをアクティブ化することはできません。 この制限は、システム割り当てマネージド ID には適用されません。

  - Key Vault がプライベート リンク (VNet) に配置されている場合は、ユーザー割り当てマネージド ID を持つカスタマー マネージド キーを使用できません。 このシナリオでは、システム割り当てマネージド ID を使用できます。

## <a name="troubleshooting"></a>トラブルシューティング

- Key Vault の可用性に関する動作
  - 通常の運用では、Storage は一時的に AEK をキャッシュし、定期的に Key Vault に戻ってラップを解除します。
    
  - 一時的な接続エラー -- Storage は、キーが短時間キャッシュにとどまることができるようにすることで一時的なエラー (タイムアウト、接続エラー、DNS の問題) を処理し、これにより可用性の小さな中断を克服できます。 クエリ機能と取り込み機能は中断されることなく続行されます。
    
  - ライブ サイト -- 約 30 分で使用できなくなると、Storage アカウントが使用できなくなります。 クエリ機能は使用できず、データの損失を防ぐために、Microsoft キーを使用して数時間の間、取り込まれたデータがキャッシュされます。 Key Vault へのアクセスが復元されると、クエリが使用可能になり、一時的にキャッシュされたデータがデータストアに取り込まれ、カスタマー マネージド キーで暗号化されます。

  - Key Vault へのアクセス レート - 折り返し操作と折り返しの解除操作のために Azure Monitor Storage が Key Vault にアクセスする頻度は、6 秒から 60 秒です。

- クラスターがプロビジョニング中または更新中の状態のときにクラスターを更新すると、更新は失敗します。

- クラスターを作成するときに競合エラーが発生する場合は、過去 14 日以内にクラスターを削除し、それが論理的な削除の期間内にある可能性があります。 クラスター名は、論理的な削除の期間中は予約されたままであり、その名前で新しいクラスターを作成することはできません。 この名前は、論理的な削除の期間の後、クラスターが完全に削除されたときに解放されます。

- 別のクラスターにリンクされている場合、クラスターへのワークスペースのリンクは失敗します。

- システム ID がクラスターに割り当てられるまでアクセス ポリシーを定義できないため、クラスターを作成して KeyVaultProperties をすぐに指定すると、操作が失敗するおそれがあります。

- KeyVaultProperties で既存のクラスターを更新し、Key Vault に "Get" キーのアクセス ポリシーがない場合、操作は失敗します。

- クラスターのデプロイに失敗した場合は、Azure Key Vault、クラスター、およびリンクされている Log Analytics ワークスペースが同じリージョンにあることを確認してください。 サブスクリプションは異なっていてもかまいません。

- Key Vault でキー バージョンを更新し、クラスターの新しいキー識別子の詳細を更新しない場合、Log Analytics クラスターにより以前のキーが引き続き使用され、データにアクセスできなくなります。 クラスターで新しいキー識別子の詳細を更新して、データの取り込みを再開し、データのクエリを実行できるようにします。

- クラスターの作成、クラスターのキーの更新、クラスターの削除などの一部の操作には長時間かかるため、完了までにしばらく時間がかかる場合があります。 操作の状態を確認するには、次の 2 つの方法があります。
  1. REST を使用している場合、応答から Azure-AsyncOperation URL 値をコピーし、「[非同期操作と状態のチェック](#asynchronous-operations-and-status-check)」に従います。
  2. GET 要求をクラスターまたはワークスペースに送信し、応答を観察します。 たとえば、リンクされていないワークスペースには、*features* の下に *clusterResourceId* が存在しません。

- エラー メッセージ
  
  **クラスターの作成**
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

  **クラスターの更新**
  -  400 -- Cluster is in deleting state. (400 -- クラスターは削除中の状態です。) 非同期操作が進行中です。 更新操作を実行する前に、クラスターでの操作が完了している必要があります。
  -  400 -- KeyVaultProperties is not empty but has a bad format. (400 -- KeyVaultProperties は空ではありませんが、形式が無効です。) [キー識別子の更新](#update-cluster-with-key-identifier-details)に関するセクションを参照してください。
  -  400 -- Failed to validate key in Key Vault. (400 -- Key Vault 内のキーの検証に失敗しました。) 必要なアクセス許可がないことが原因である可能性があります。または、キーが存在しません。 Key Vault で[キーとアクセス ポリシーを設定](#grant-key-vault-permissions)したことを確認してください。
  -  400 -- Key is not recoverable. (400 -- キーは回復不能です。) Key Vault に論理的な削除と消去保護を設定する必要があります。 [Key Vault のドキュメント](../../key-vault/general/soft-delete-overview.md)を参照してください
  -  400 -- Operation cannot be executed now. (400 -- 現在、操作を実行できません。) 非同期操作が完了するまで待ってから、もう一度お試しください。
  -  400 -- Cluster is in deleting state. (400 -- クラスターは削除中の状態です。) 非同期操作が完了するまで待ってから、もう一度お試しください。

  **クラスターの取得**
    -  404 -- Cluster not found, the cluster may have been deleted. (404 -- クラスターが見つかりません。クラスターは削除された可能性があります。) クラスターをその名前で作成しようとし、競合が発生した場合、そのクラスターは 14 日間の論理的な削除状態にあります。 回復するためにサポートに連絡するか、別の名前を使用して新しいクラスターを作成できます。 

  **クラスターの削除**
    -  409 -- Can't delete a cluster while in provisioning state. (409 -- プロビジョニング状態の間は、クラスターを削除できません。) 非同期操作が完了するまで待ってから、もう一度お試しください。

  **ワークスペースのリンク**
  -  404 -- ワークスペースが見つかりません。 指定したワークスペースが存在しないか、削除されました。
  -  409 -- ワークスペースのリンクまたはリンク解除操作が進行中です。
  -  400 -- Cluster not found, the cluster you specified doesn’t exist or was deleted. (400 -- クラスターが見つかりません。指定したクラスターが存在しないか、削除されました。) クラスターをその名前で作成しようとし、競合が発生した場合、そのクラスターは 14 日間の論理的な削除状態にあります。 回復するには、サポートにお問い合わせください。

  **ワークスペースのリンク解除**
  -  404 -- ワークスペースが見つかりません。 指定したワークスペースが存在しないか、削除されました。
  -  409 -- ワークスペースのリンクまたはリンク解除操作が進行中です。
## <a name="next-steps"></a>次の手順

- [Log Analytics 専用クラスターの課金](./manage-cost-storage.md#log-analytics-dedicated-clusters)について学習します
- [Log Analytics ワークスペースの適切な設計](./design-logs-deployment.md)について学習します