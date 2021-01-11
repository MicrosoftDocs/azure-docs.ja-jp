---
title: Azure Monitor のカスタマー マネージド キー
description: カスタマー マネージド キー (CMK) を、Azure Key Vault キーを使用して Log Analytics ワークスペースのデータを暗号化するように構成するための情報と手順。
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 07/05/2020
ms.openlocfilehash: 9b47326d32b393af5dcf167c373b6873fe39cd7c
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89279739"
---
# <a name="azure-monitor-customer-managed-key"></a>Azure Monitor のカスタマー マネージド キー 

この記事では、Log Analytics のワークスペースに対して、カスタマー マネージド キー (CMK) を構成するための背景情報と手順について説明します。 構成が完了すると、ワークスペースに送信されるすべてのデータが Azure Key Vault キーで暗号化されます。

構成の前に「[制限と制約](#limitationsandconstraints)」を確認することをお勧めします。

## <a name="customer-managed-key-cmk-overview"></a>カスタマー マネージド キー (CMK) の概要

[保存時の暗号化](../../security/fundamentals/encryption-atrest.md) は、組織の一般的なプライバシーおよびセキュリティ要件です。 保存時の暗号化は Azure で完全に管理できますが、暗号化または暗号化キーを厳密に管理するさまざまなオプションが提供されています。

Azure Monitor により、Microsoft マネージド キー (MMK) を使用して、すべてのデータおよび保存されたクエリが保存時に暗号化されるようになります。 Azure Monitor には、[Azure Key Vault](../../key-vault/general/overview.md) に格納され、システムによって割り当てられた[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) 認証を使用してアクセスされる、独自のキーを使用した暗号化のオプションも用意されています。 このキー (CMK) は、[ソフトウェアまたはハードウェアの HSM で保護](../../key-vault/general/overview.md)できます。

Azure Monitor の暗号化の使用は、 [Azure Storage の暗号化](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption) と同じように動作します。

CMK を使用すると、データへのアクセスを制御し、いつでもそれを取り消すことができます。 Azure Monitor Storage は、常に 1 時間以内にキーのアクセス許可の変更に対応します。 過去 14 日間に取り込まれたデータも、効率的なクエリ エンジン操作のためにホットキャッシュ (SSD ベース) で保持されます。 このデータは、CMK 構成に関係なく、Microsoft キーで暗号化されたままになりますが、SSD データに対する制御は [キーの失効](#cmk-kek-revocation)に従います。 2020 年の後半には、SSD データを CMK で暗号化できるように取り組んでいます。

CMK 機能は専用の Log Analytics クラスターで提供されます。 お客様のリージョンに必要な容量があることを確認するために、お客様のサブスクリプションが事前に許可されている必要があります。 CMK の構成を開始する前に、Microsoft の担当者に依頼して、お客様のサブスクリプションを許可してください。

 [Log Analytics クラスターの価格モデル](./manage-cost-storage.md#log-analytics-dedicated-clusters) では、1000 GB/日レベルから始まる容量予約が使用されます。

## <a name="how-cmk-works-in-azure-monitor"></a>Azure Monitor での CMK の動作

Azure Monitor は、システム割り当てのマネージド ID を活用して Azure Key Vault にアクセス権を付与します。 システム割り当てマネージド ID は、1 つの Azure リソースにのみ関連付けできますが、Log Analytics クラスターの ID はクラスター レベルでサポートされます。これは、CMK 機能が専用の Log Analytics クラスターで提供されることを意味します。 複数のワークスペースで CMK をサポートするために、新しい Log Analytics "*クラスター*" リソースは、Key Vault と Log Analytics のワークスペースの間の中間 ID 接続として実行されます。 Log Analytics クラスター ストレージは、"*クラスター*" リソースに関連付けられているマネージド ID を使用して、Azure Active Directory 経由で Azure Key Vault に対する認証を行います。 

CMK の構成後、"*クラスター*" リソースに関連付けられているワークスペースに取り込まれるすべてのデータは、Key Vault でキーを使用して暗号化されます。 "*クラスター*" リソースとワークスペースの関連付けは、いつでも解除できます。 新しいデータが Log Analytics ストレージに取り込まれて Microsoft キーで暗号化されますが、新しいデータと古いデータに対してシームレスにクエリを実行することができます。


![CMK の概要](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Key Vault に対するアクセス許可があるマネージド ID を持つ Log Analytics "*クラスター*" リソース – ID は、土台となる専用の Log Analytics クラスター ストレージに伝達されます
3. 専用の Log Analytics クラスター
4. CMK 暗号化のために "*クラスター*" リソースに関連付けられているワークスペース

## <a name="encryption-keys-operation"></a>暗号化キー操作

ストレージ データの暗号化には、次の 3 種類のキーが関係します。

- **KEK** - キー暗号化キー (CMK)
- **AEK** - アカウント暗号化キー
- **DEK** - データ暗号化キー

次の規則が適用されます。

- Log Analytics クラスター ストレージ アカウントは、すべてのストレージ アカウントに対して一意の暗号化キーを生成します。これは、AEK と呼ばれます。

- AEK は、ディスクに書き込まれた各データ ブロックの暗号化に使用されるキーである DEK を派生させるために使用されます。

- Key Vault でキーを構成し、それを*クラスター* リソースで参照すると、Azure Storage から Azure Key Vault に要求が送信され、AEK をラップおよびアンラップしてデータの暗号化および復号化操作を実行します。

- KEK が Key Vault から離れることはありません。また、HSM キーの場合はハードウェアから離れることはありません。

- Azure Storage は、*クラスター* リソースに関連付けられているマネージド ID を使用して、Azure Active Directory 経由で Azure Key Vault へのアクセスを認証します。

## <a name="cmk-provisioning-procedure"></a>CMK のプロビジョニング手順

1. サブスクリプションの許可 - CMK 機能は専用の Log Analytics クラスターで提供されます。 お客様のリージョンに必要な容量があることを確認するために、お客様のサブスクリプションが事前に許可されている必要があります。 Microsoft の担当者に依頼して、お客様のサブスクリプションを許可してください。
2. Azure Key Vault の作成とキーの格納
3. *クラスター* リソースの作成
4. Key Vault へのアクセス許可の付与
5. Log Analytics ワークスペースの関連付け

この手順は Azure portal ではサポートされておらず、プロビジョニングは PowerShell または REST 要求を使用して実行されます。

> [!IMPORTANT]
> すべての API 要求には、要求ヘッダーにベアラー認証トークンを含める必要があります。

次に例を示します。

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2020-08-01
Authorization: Bearer eyJ0eXAiO....
```

ここで *eyJ0eXAiO...* は完全な認証トークンを表します。 

トークンは次のいずれかの方法を使用して取得できます。

1. [アプリの登録](/graph/auth/auth-concepts#access-tokens)方法を使用します。
2. Azure ポータルで次の操作を行います。
    1. "開発者ツール" (F12) の使用中に Azure portal に移動します。
    1. "batch? api-version" インスタンスのいずれかで、"要求ヘッダー" の下にある認証文字列を検索します。 これは "authorization: Bearer eyJ0eXAiO...." のようになります。 
    1. これを、次の例に従って API 呼び出しにコピーして追加します。
3. Azure REST ドキュメント サイトに移動します。 任意の API で [試してみる] を押し、ベアラー トークンをコピーします。

### <a name="asynchronous-operations-and-status-check"></a>非同期操作と状態のチェック

この構成手順の一部の操作は迅速に完了できないため、非同期的に実行されます。 構成で REST 要求を使用している場合、応答では最初に HTTP 状態コード 200 (OK) が返され、受け入れられたときに *Azure-AsyncOperation* プロパティを持つヘッダーが返されます。
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

次に、*Azure-AsyncOperation* ヘッダー値に GET 要求を送信することにより、非同期操作の状態を確認できます。
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

この応答には、操作とその*状態*に関する情報が含まれています。 これは、次のいずれかになります。

操作が進行中です
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "InProgress", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
}
```

キー識別子の更新操作が進行中です
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Updating", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

"*クラスター*" リソースの削除が進行中です - ワークスペースが関連付けられている "*クラスター*" リソースを削除すると、時間がかかる場合がある非同期操作で、ワークスペースごとに関連付けの解除操作が実行されます。
これは、ワークスペースが関連付けられていない "*クラスター*" を削除する場合には関係ありません。この場合、"*クラスター*" リソースが直ちに削除されます。
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Deleting", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

操作が完了しました
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Succeeded", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

操作に失敗しました
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Failed", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "error" : { 
        "code": "error-code",  
        "message": "error-message" 
    }
}
```

### <a name="allowing-subscription-for-cmk-deployment"></a>CMK デプロイのためのサブスクリプションの許可

CMK 機能は専用の Log Analytics クラスターで提供されます。 お客様のリージョンに必要な容量があることを確認するために、お客様のサブスクリプションが事前に許可されている必要があります。 連絡先を Microsoft に登録して、サブスクリプション ID を提供します。

> [!IMPORTANT]
> CMK 機能はリージョン別です。 Azure Key Vault、*クラスター* リソース、および関連付けられた Log Analytics のワークスペースは同じリージョンに存在している必要があります。ただし、サブスクリプションは異なっていてもかまいません。

### <a name="storing-encryption-key-kek"></a>暗号化キー (KEK) の格納

Azure Key Vault を作成するか既存のものを使用して、データの暗号化に使用するキーを生成またはインポートします。 キーを保護し、Azure Monitor のデータへのアクセスを保護するには、Azure Key Vault を回復可能として構成する必要があります。 この構成は Key Vault のプロパティで確認できます。 *[論理的な削除]* と *[Purge protection]\(消去保護\)* の両方を有効にしてください。

![論理的な削除と消去保護の設定](media/customer-managed-keys/soft-purge-protection.png)

これらの設定は、CLI と PowerShell を使用して更新できます。

- [論理的な削除](../../key-vault/general/soft-delete-overview.md)
- [[Purge protection]\(消去保護\)](../../key-vault/general/soft-delete-overview.md#purge-protection) は、論理的な削除の後もシークレットやコンテナーを強制削除から保護します

### <a name="create-cluster-resource"></a>*クラスター* リソースを作成する

このリソースは、Key Vault と Log Analytics ワークスペースの間の中間 ID 接続として使用されます。 サブスクリプションが許可されたことを示すメッセージが表示されたら、ワークスペースが配置されているリージョンで Log Analytics の*クラスター* リソースを作成します。

"*クラスター*" リソースを作成するときに、"*容量予約*" レベル (sku) を指定する必要があります。 "*容量予約*" レベルは、1 日あたり 1000 GB から 3000 GB の範囲で指定でき、それを 100 刻みで更新できます。 1 日あたり 3000 GB を超える容量予約レベルが必要な場合は、LAIngestionRate@microsoft.com までお問い合わせください。 [詳細情報](./manage-cost-storage.md#log-analytics-dedicated-clusters)

*billingType* プロパティによって、"*クラスター*" リソースとそのデータの課金の帰属が決まります。
- *クラスター* (既定)--クラスターの容量予約コストは、*クラスター* リソースに帰属します。
- *ワークスペース*--クラスターの容量予約コストは、クラスター内のワークスペースに比例的に帰属します。その日に取り込まれた合計データが容量予約を下回る場合に使用量の一部が*クラスター* リソースに課金されます。 クラスターの価格モデルの詳細については、[Log Analytics 専用クラスター](manage-cost-storage.md#log-analytics-dedicated-clusters)に関するページを参照してください。 

> [!NOTE]
> * "*クラスター*" リソースを作成した後は、PATCH REST 要求を使用して *sku*、*keyVaultProperties* または *billingType* でそれを更新できます。
> * 現在、REST 要求を使用して *billingType* を更新できますが、これは PowerShell ではサポートされていません。

この操作は非同期であり、完了するまでに時間が掛かります。

> [!IMPORTANT]
> これらの詳細は次の手順で必要になるため、応答をコピーして保存してください。
> 

```powershell
New-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -Location "region-name" -SkuCapacity daily-ingestion-gigabyte 
```

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

この ID は、作成時に "*クラスター*" リソースに割り当てられます。

**Response**

200 OK とヘッダー。

Log Analytics クラスターのプロビジョニングには時間がかかりますが、次の 2 つの方法でプロビジョニングの状態を確認できます。

1. 応答から Azure-AsyncOperation URL 値をコピーし、[非同期操作と状態のチェック](#asynchronous-operations-and-status-check)に従います。
2. *クラスター* リソースに GET 要求を送信し、*provisioningState* 値を確認します。 プロビジョニング中は *ProvisioningAccount*、完了時は *Succeeded* になります。

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

"principalId" GUID は、*クラスター* リソースのマネージド ID サービスによって生成されます。

### <a name="grant-key-vault-permissions"></a>Key Vault アクセス許可を付与する

*クラスター* リソースにアクセス許可を付与する新しいアクセス ポリシーで Key Vault を更新します。 これらのアクセス許可は、データの暗号化のために下層の Azure Monitor ストレージによって使用されます。 Azure portal で Key Vault を開き、[アクセス ポリシー]、[+ アクセス ポリシーの追加] の順にクリックして、次の設定でポリシーを作成します。

- [キーのアクセス許可]: [取得]、[キーを折り返す]、および [キーの折り返しを解除] の各アクセス許可を選択します。
- [プリンシパルの選択]: 前の手順の応答で返された*クラスター* リソース名または principal-id 値を入力します。

![Key Vault アクセス許可の付与](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

*[取得]* アクセス許可は、キーを保護し、Azure Monitor データへのアクセスを保護するために、Key Vault が回復可能として構成されていることを確認するために必要です。

### <a name="update-cluster-resource-with-key-identifier-details"></a>キー識別子の詳細を使用してクラスター リソースを更新する

この手順は、Key Vault の初期および将来のキー バージョンの更新時に実行されます。 データの暗号化に使用されるキーのバージョンについて Azure Monitor ストレージに通知します。 更新すると、新しいキーを使用してストレージ キー (AEK) のラップとラップ解除が行われます。

Key Vault の*キー識別子*の詳細で*クラスター リソー*スを更新するには、Azure Key Vault で現在のバージョンのキーを選択して、キー識別子の詳細を取得します。

![Key Vault アクセス許可を付与する](media/customer-managed-keys/key-identifier-8bit.png)

キー識別子の詳細を使用して*クラスター* リソース KeyVaultProperties を更新します。

この操作は、キー識別子の詳細を更新するときは非同期であり、完了するまでに時間が掛かることがあります。 容量の値を更新するときに同期されます。

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version"
```

> [!NOTE]
> PATCH を使用して、*クラスター* リソース *sku*、*keyVaultProperties* または *billingType* を更新できます。

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
   "identity": { 
     "type": "systemAssigned" 
     },
   "sku": {
     "name": "capacityReservation",
     "capacity": 1000
     },
   "properties": {
    "billingType": "cluster",
     "KeyVaultProperties": {
       "KeyVaultUri": "https://<key-vault-name>.vault.azure.net",
       "KeyName": "<key-name>",
       "KeyVersion": "<current-version>"
       }
   },
   "location":"<region-name>"
}
```

"KeyVaultProperties" には Key Vault キー識別子の詳細が含まれています。

**Response**

200 OK とヘッダー。
キー識別子の伝播が完了するまで数分かかります。 更新の状態を確認するには、次の 2 つの方法があります。
1. 応答から Azure-AsyncOperation URL 値をコピーし、[非同期操作と状態のチェック](#asynchronous-operations-and-status-check)に従います。
2. *クラスター* リソースに GET 要求を送信し、*KeyVaultProperties* 値を確認します。 最近更新されたキー識別子の詳細が、応答で返されます。

キー識別子の更新が完了すると、*クラスター* リソースの GET 要求に対する応答は次のようになります。

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

### <a name="workspace-association-to-cluster-resource"></a>*クラスター* リソースへのワークスペースの関連付け

この操作を実行するには、ワークスペースと "*クラスター*" リソースの両方に対して、以下のアクションが含まれる "書き込み" アクセス許可を保持している必要があります。

- ワークスペースの場合:Microsoft.OperationalInsights/workspaces/write
- "*クラスター*" リソースの場合:Microsoft.OperationalInsights/clusters/write

> [!IMPORTANT]
> この手順は、Log Analytics クラスターのプロビジョニングが完了した後にのみ実行してください。 このプロビジョニングの前にワークスペースを関連付けてデータを取り込むと、取り込まれたデータは削除され、回復できなくなります。

この操作は非同期であり、完了までに時間が掛かります。

```powershell
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name").id
Set-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId
```

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

**Response**

200 OK とヘッダー。

取り込まれたデータは、関連付け操作の後、マネージド キーで暗号化された状態で格納されます。これが完了するには最大 90 分かかることがあります。 ワークスペースの関連付けの状態は、次の 2 つの方法で確認できます。

1. 応答から Azure-AsyncOperation URL 値をコピーし、[非同期操作と状態のチェック](#asynchronous-operations-and-status-check)に従います。
2. [Workspaces – Get](/rest/api/loganalytics/workspaces/get) 要求を送信し、応答を観察します。関連付けられたワークスペースでは "features" の下に clusterResourceId が含まれます。

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-08-01
Authorization: Bearer <token>
```

**Response**

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

## <a name="cmk-kek-revocation"></a>CMK (KEK) の失効

Key Vault でキーを無効にするか、"*クラスター*" リソースのアクセス ポリシーを削除することによって、データへのアクセスを失効させることができます。 Log Analytics クラスターは常に 1 時間以内にキーのアクセス許可の変更に対応し、ストレージがすぐに使用できなくなります。 " *クラスター * " リソースに関連付けられているワークスペースに取り込まれた新しいデータは、すべて削除されて復旧できなくなり、データにアクセスできず、これらのワークスペースへのクエリは失敗します。 "*クラスター*" リソースおよびワークスペースが削除されていない限り、以前に取り込まれたデータはストレージに残ります。 アクセスできないデータは、データ保持ポリシーによって管理され、リテンション期間に達すると削除されます。 

過去 14 日間に取り込まれたデータも、効率的なクエリ エンジン操作のためにホットキャッシュ (SSD ベース) で保持されます。 これはキーの失効操作で削除され、アクセスできなくなります。

ストレージは、Key Vault を定期的にポーリングして暗号化キーの折り返しを解除しようとし、アクセスされた後、データ インジェストとクエリが 30 分以内に再開されます。

## <a name="cmk-kek-rotation"></a>CMK (KEK) のローテーション

CMK のローテーションを行うには、Azure Key Vault の新しいキー バージョンで "*クラスター*" リソースを明示的に更新する必要があります。 「キー識別子の詳細で "*クラスター*" リソースを更新する」の手順に従ってください。 "*クラスター*" リソースの新しいキー識別子の詳細を更新しない場合、Log Analytics クラスター ストレージでは暗号化に以前のキーが引き続き使用されます。 "*クラスター*" リソースの新しいキーを更新する前に古いキーを無効にしたり削除したりすると、[キーの失効](#cmk-kek-revocation)状態になります。

アカウント暗号化キー (AEK) は新しいキー暗号化キー (KEK) バージョンによって Key Vault で暗号化されるようになりますが、データは常に AEK によって暗号化されているため、キー ローテーション操作後も、すべてのデータにアクセスできます。

## <a name="cmk-for-queries"></a>クエリの CMK

Log Analytics で使用されるクエリ言語は表現力が豊かで、クエリに追加するコメントまたはクエリ構文に機密情報を含めることができます。 組織によっては、このような情報を CMK ポリシーの一環として保護しておく必要があり、キーで暗号化された状態でクエリを保存する必要があります。 Azure Monitor では、独自のキーを使用して暗号化された "*保存された検索条件*" および "*ログ アラート*" のクエリを、ワークスペースに接続したときに独自のストレージ アカウントに保存しておくことができます。 

> [!NOTE]
> Log Analytics クエリは、使用されるシナリオに応じて、さまざまなストアに保存できます。 CMK の構成に関係なく、次のシナリオでは、Microsoft キー (MMK) を使用してクエリの暗号化が維持されます: Azure Monitor のブック、Azure ダッシュボード、Azure Logic App、Azure Notebooks、および Automation Runbook。

Bring Your Own Storage (BYOS) をワークスペースに関連付けると、サービスによって、"*保存された検索条件*" と "*ログ アラート*" のクエリがストレージ アカウントにアップロードされます。 つまり、Log Analytics クラスター内のデータの暗号化に使用するのと同じキー、または別のキーを使用して、ストレージ アカウントと[保存時の暗号化ポリシー](../../storage/common/encryption-customer-managed-keys.md)を制御することを意味します。 ただし、そのストレージ アカウントに関連するコストについては、お客様が責任を負うものとします。 

**クエリ用に CMK を設定する前の考慮事項**
* ワークスペースとストレージ アカウントの両方に対して "書き込み" アクセス許可を持っている必要があります
* ストレージ アカウントは、Log Analytics ワークスペースと同じリージョンに作成してください。
* ストレージ内の*保存された検索条件*はサービス アーティファクトと見なされ、その形式は変更される可能性があります
* 既存の*保存された検索条件*は、ワークスペースから削除されます。 必要な*保存された検索条件*は、構成前にコピーしてください。 [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch) を使用して、"*保存された検索条件*" を表示できます
* クエリ履歴はサポートされていないため、実行したクエリは表示できません
* クエリを保存するために、1 つのストレージ アカウントをワークスペースに関連付けることができますが、これは "*保存された検索条件*" と "*ログ アラート*" のクエリの両方から使用できます
* ダッシュボードへのピン留めはサポートされていません

**保存された検索条件のクエリ用に BYOS を構成する**

"*クエリ*" 用のストレージ アカウントをワークスペースに関連付けます。"*保存された検索条件*" のクエリはストレージ アカウントに保存されます。 

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Query -StorageAccountIds $storageAccount.Id
```

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

構成が完了すると、新しい*保存された検索条件*クエリがストレージに保存されます。

**ログ アラートのクエリ用の BYOS を構成する**

"*アラート*" 用のストレージ アカウントをワークスペースに関連付けます。"*ログ アラート*" のクエリはストレージ アカウントに保存されます。 

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Alerts -StorageAccountIds $storageAccount.Id
```

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

構成が完了すると、新しいアラート クエリがストレージに保存されます。

## <a name="cmk-management"></a>CMK 管理

- **リソース グループのすべての "*クラスター*" リソースを取得する**
  
  ```powershell
  Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
  ```

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Response**
  
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

- **サブスクリプションのすべての "*クラスター*" リソースを取得する**
  
  ```powershell
  Get-AzOperationalInsightsCluster
  ```

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```
    
  **Response**
    
  'リソース グループの*クラスター* リソース' について、ただしサブスクリプション スコープ内のリソースと同じ応答です。

- **"*クラスター*" リソースの "*容量予約*" を更新する**

  関連付けられたワークスペースへのデータ量が時間の経過と共に変化し、容量予約レベルを適切に更新する必要が生じることがあります。 ["*クラスター*" リソースを更新する](#update-cluster-resource-with-key-identifier-details)手順に従って、新しい容量の値を指定してください。 これは 1 日あたり 1000 GB から 3000 GB の範囲で、100 刻みで指定できます。 1 日あたり 3000 GB を超えるレベルの場合は、Microsoft の担当者に有効化を依頼してください。 完全な REST 要求本文を指定する必要はなく、sku を含める必要があることに注意してください。

  ```powershell
  Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity "daily-ingestion-gigabyte"
  ```

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 1000
    }
  }
  ```

- **"*クラスター*" リソース内の *billingType* を更新する**

  *billingType* プロパティによって、"*クラスター*" リソースとそのデータの課金の帰属が決まります。
  - *cluster* (既定) - 課金は、クラスター リソースをホストするサブスクリプションに帰属します
  - *workspaces* - 課金は、ワークスペースをホストするサブスクリプションに比例的に帰属します
  
  ["*クラスター*" リソースを更新する](#update-cluster-resource-with-key-identifier-details)手順に従って、新しい billingType 値を指定してください。 完全な REST 要求本文を指定する必要はなく、*billingType* を含める必要があることに注意してください。

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

- **ワークスペースの関連付けを解除する**

  この操作を実行するには、ワークスペースおよび "*クラスター*" リソースに対する "書き込み" アクセス許可が必要です。 "*クラスター*" リソースとワークスペースの関連付けは、いつでも解除できます。 関連付け解除操作後に新たに取り込まれたデータは、Log Analytics ストレージに格納され、Microsoft キーで暗号化されます。 "*クラスター*" リソースがプロビジョニングされ、有効な Key Vault キーで構成されている場合は、関連付け解除の前および後にワークスペースに取り込まれたデータに対してシームレスにクエリを実行できます。

  この操作は非同期であり、完了までに時間が掛かります。

  ```powershell
  Remove-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -Name "workspace-name" -LinkedServiceName cluster
  ```

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Response**

  200 OK とヘッダー。

  関連付け解除操作後に取り込まれたデータは、Log Analytics ストレージに格納されますが、これが完了するまでに 90 分かかることがあります。 ワークスペースの関連付け解除の状態は、次の 2 つの方法で確認できます。

  1. 応答から Azure-AsyncOperation URL 値をコピーし、[非同期操作と状態のチェック](#asynchronous-operations-and-status-check)に従います。
  2. [Workspaces – Get](/rest/api/loganalytics/workspaces/get) 要求を送信し、応答を観察します。関連付け解除されたワークスペースには *features* の下に *clusterResourceId* が含まれません。

- **ワークスペースの関連付けの状態を確認する**
  
  ワークスペースで Get 操作を実行し、応答の *features* の下に *clusterResourceId* プロパティが存在するかどうかを確認します。 関連付けられたワークスペースには、*clusterResourceId* プロパティが存在します。

  ```powershell
  Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
  ```

- **"*クラスター*" リソースを削除する**

  この操作を実行するには、"*クラスター*" リソースに対する "書き込み" アクセス許可が必要です。 不注意による削除か意図的な削除かにかかわらず、14 日以内であればデータを含む*クラスター* リソースを復旧できるように、論理的な削除操作が実行されます。 *クラスター* リソース名は、論理的な削除の期間中は予約されたままであり、その名前で新しいクラスターを作成することはできません。 論理的な削除の期間後は、"*クラスター*" リソース名が解放され、"*クラスター*" リソースとデータは完全に削除されて、復旧できなくなります。 関連付けられているすべてのワークスペースは、削除操作時に "*クラスター*" リソースから関連付けを解除されます。 新たに取り込まれたデータは、Log Analytics ストレージに格納され、Microsoft キーで暗号化されます。 
  
  ワークスペースの関連付け解除操作は非同期であり、完了するまでに最大 90 分かかることがあります。

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Response**

  200 OK

- **"*クラスター*" リソースとデータを復旧する** 
  
  過去 14 日以内に削除された "*クラスター*" リソースは、論理的な削除状態であり、そのデータで復旧できます。 "*クラスター*" リソースの削除によって、すべてのワークスペースが "*クラスター*" リソースから関連付け解除されているため、CMK 暗号化のために回復後にワークスペースを再関連付けする必要があります。 復旧操作は、現在、製品グループごとに手動で実行されます。 復旧要求には、Microsoft チャネルを使用します。

## <a name="limitationsandconstraints"></a>制限と制約

- CMK は専用の Log Analytics クラスターでサポートされており、1 日あたり 1 TB 以上を送信するお客様に適しています。

- リージョンおよびサブスクリプションごとの "*クラスター*" リソースの最大数は 2 です

- ワークスペースを "*クラスター*" リソースに関連付け、そのワークスペースで CMK が不要な場合は関連付けを解除することができます。 30 日間の期間における特定のワークスペースでのワークスペースの関連付けの数は、2 に制限されています

- "*クラスター*" リソースへのワークスペースの関連付けは、Log Analytics クラスターのプロビジョニングが完了したことを確認した後にのみ実行してください。 完了前にワークスペースに送信されたデータは削除され、復旧できなくなります。

- CMK の暗号化は、CMK の構成後に新しく取り込まれたデータに適用されます。 CMK の構成より前に取り込まれたデータは、Microsoft キーで暗号化されたままになります。 取り込まれたデータのクエリは CMK 構成の前後にシームレスに実行できます。

- Azure Key Vault は、回復可能として構成する必要があります。 これらのプロパティは既定では有効になっておらず、CLI または PowerShell を使用して構成する必要があります。<br>
  - [論理的な削除](../../key-vault/general/soft-delete-overview.md)
  - 論理的な削除の後でもシークレット/コンテナーの強制削除を防ぐには、[消去保護](../../key-vault/general/soft-delete-overview.md#purge-protection)を有効にする必要があります。

- *クラスター* リソースの別のリソース グループまたはサブスクリプションへの移動は、現時点ではサポートされていません。

- Azure Key Vault、*クラスター* リソースおよび関連付けられたワークスペースは、同じリージョンで同じ Azure Active Directory (Azure AD) テナント内に存在している必要がありますが、サブスクリプションは異なっていてもかまいません。

- *クラスター* リソースへのワークスペースの関連付けは、別の*クラスター* リソースに関連付けられている場合は失敗します

## <a name="troubleshooting"></a>トラブルシューティング

- Key Vault の可用性に関する動作
  - 通常の運用では、Storage は一時的に AEK をキャッシュし、定期的に Key Vault に戻ってラップを解除します。
    
  - 一時的な接続エラー -- Storage は、キーが短時間キャッシュにとどまることができるようにすることで一時的なエラー (タイムアウト、接続エラー、DNS の問題) を処理し、これにより可用性の小さな中断を克服できます。 クエリ機能と取り込み機能は中断されることなく続行されます。
    
  - ライブ サイト -- 約 30 分で使用できなくなると、Storage アカウントが使用できなくなります。 クエリ機能は使用できず、データの損失を防ぐために、Microsoft キーを使用して数時間の間、取り込まれたデータがキャッシュされます。 Key Vault へのアクセスが復元されると、クエリが使用可能になり、一時的にキャッシュされたデータがデータストアに取り込まれ、CMK で暗号化されます。

  - Key Vault へのアクセス レート - 折り返し操作と折り返しの解除操作のために Azure Monitor Storage が Key Vault にアクセスする頻度は、6 秒から 60 秒です。

- システム ID が*クラスター* リソースに割り当てられるまでアクセス ポリシーを定義できないため、*クラスター* リソースを作成して KeyVaultProperties をすぐに指定すると、操作が失敗する可能性があります。

- KeyVaultProperties で既存の*クラスター* リソースを更新し、Key Vault に 'Get' キーのアクセス ポリシーがない場合、操作は失敗します。

- "*クラスター*" リソースを作成するときに競合エラーが発生した場合は、過去 14 日以内に "*クラスター*" リソースを削除し、それが論理的な削除の期間内にある可能性があります。 *クラスター* リソース名は、論理的な削除の期間中は予約されたままであり、その名前で新しいクラスターを作成することはできません。 この名前は、論理的な削除の期間の後、"*クラスター*" リソースが完全に削除されたときに解放されます。

- 操作の実行中に*クラスター* リソースを更新すると、操作は失敗します。

- "*クラスター*" リソースのデプロイに失敗した場合は、Azure Key Vault、" *クラスター* " リソース、および関連付けられている Log Analytics ワークスペースが同じリージョンにあることを確認してください。 サブスクリプションは異なっていてもかまいません。

- Key Vault でキー バージョンを更新し、"*クラスター*" リソースの新しいキー識別子の詳細を更新しない場合、Log Analytics クラスターでは以前のキーが引き続き使用され、データにアクセスできなくなります。 "*クラスター*" リソースで新しいキー識別子の詳細を更新して、データの取り込みを再開し、データのクエリを実行できるようにします。

- *クラスター*の作成、*クラスター*のキーの更新、*クラスター*の削除などの一部の操作には長時間かかるため、完了までにしばらく時間がかかる場合があります。 操作の状態を確認するには、次の 2 つの方法があります。
  1. REST を使用している場合、応答から Azure-AsyncOperation URL 値をコピーし、「[非同期操作と状態のチェック](#asynchronous-operations-and-status-check)」に従います。
  2. GET 要求を*クラスター*またはワークスペースに送信し、応答を観察します。 たとえば、関連付けられていないワークスペースでは、*features* の下に *clusterResourceId* が存在しません。

- カスタマー マネージド キーに関するサポートおよびヘルプを受けるには、お客様の連絡先を Microsoft に登録してください。

- エラー メッセージ
  
  "*クラスター*" リソースの作成:
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

  "*クラスター*" リソースの更新
  -  400 -- Cluster is in deleting state. (400 -- クラスターは削除中の状態です。) 非同期操作が進行中です。 更新操作を実行する前に、クラスターでの操作が完了している必要があります。
  -  400 -- KeyVaultProperties is not empty but has a bad format. (400 -- KeyVaultProperties は空ではありませんが、形式が無効です。) [キー識別子の更新](#update-cluster-resource-with-key-identifier-details)に関するセクションを参照してください。
  -  400 -- Failed to validate key in Key Vault. (400 -- Key Vault 内のキーの検証に失敗しました。) 必要なアクセス許可がないことが原因である可能性があります。または、キーが存在しません。 Key Vault で[キーとアクセス ポリシーを設定](#grant-key-vault-permissions)したことを確認してください。
  -  400 -- Key is not recoverable. (400 -- キーは回復不能です。) Key Vault に論理的な削除と消去保護を設定する必要があります。 [Key Vault のドキュメント](../../key-vault/general/soft-delete-overview.md)を参照してください
  -  400 -- Operation cannot be executed now. (400 -- 現在、操作を実行できません。) 非同期操作が完了するまで待ってから、もう一度お試しください。
  -  400 -- Cluster is in deleting state. (400 -- クラスターは削除中の状態です。) 非同期操作が完了するまで待ってから、もう一度お試しください。

    "*クラスター*" リソースの取得:
    -  404 -- Cluster not found, the cluster may have been deleted. (404 -- クラスターが見つかりません。クラスターは削除された可能性があります。) クラスターをその名前で作成しようとし、競合が発生した場合、そのクラスターは 14 日間の論理的な削除状態にあります。 回復するためにサポートに連絡するか、別の名前を使用して新しいクラスターを作成できます。 

  "*クラスター*" リソースの削除
    -  409 -- Can't delete a cluster while in provisioning state. (409 -- プロビジョニング状態の間は、クラスターを削除できません。) 非同期操作が完了するまで待ってから、もう一度お試しください。

  ワークスペースの関連付け:
  -  404 -- ワークスペースが見つかりません。 指定したワークスペースが存在しないか、削除されました。
  -  409 -- Workspace association or disassociation operation in process. (409 -- ワークスペースの関連付けまたは関連付け解除の操作が進行中です。)
  -  400 -- Cluster not found, the cluster you specified doesn’t exist or was deleted. (400 -- クラスターが見つかりません。指定したクラスターが存在しないか、削除されました。) クラスターをその名前で作成しようとし、競合が発生した場合、そのクラスターは 14 日間の論理的な削除状態にあります。 回復するには、サポートにお問い合わせください。

  ワークスペースの関連付け解除:
  -  404 -- ワークスペースが見つかりません。 指定したワークスペースが存在しないか、削除されました。
  -  409 -- Workspace association or disassociation operation in process. (409 -- ワークスペースの関連付けまたは関連付け解除の操作が進行中です。)
