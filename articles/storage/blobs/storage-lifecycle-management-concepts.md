---
title: Azure Blob Storage アクセス層の自動化によるコストの最適化
description: ホット、クール、アーカイブの各階層間でデータを移動するための自動ルールを作成します。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/29/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.custom: devx-track-azurepowershell, references_regions
ms.openlocfilehash: 1b568687ffe646a91544c1bb75d26d552a23f49c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005284"
---
# <a name="optimize-costs-by-automating-azure-blob-storage-access-tiers"></a>Azure Blob Storage アクセス層の自動化によるコストの最適化

データ セットには一意のライフサイクルがあります。 ライフサイクルの早い段階で、一部のデータに頻繁にアクセスされます。 しかし、データが古くなるとアクセスの必要性が急激に低下します。 また、クラウド内で使用されず、格納された後もほとんどアクセスされないデータもあります。 データには、作成後数日または数か月で失効するものがあります。また、ライフサイクルにわたってアクティブに読み取られ、変更されるデータ セットもあります。 Azure Blob Storage のライフサイクル管理には、GPv2 アカウントと BLOB ストレージ アカウントに関する、豊富な内容のルールベースのポリシーが用意されています。 このポリシーを使用して、適切なアクセス層にデータを移行します。または、データのライフサイクルの終了時に期限切れにします。

ライフサイクル管理ポリシーによって、以下を行えます。

- パフォーマンスを最適化するためにアクセスされた場合、BLOB をクールからホットに直ちに移行する 
- コストを最適化するために、一定期間にわたってアクセスも変更もされていない場合、BLOB、BLOB のバージョン、BLOB のスナップショットをよりクールなストレージ層に移行する (ホットからクール、ホットからアーカイブ、またはクールからアーカイブ)
- ライフサイクルの最後に BLOB、BLOB のバージョン、BLOB のスナップショットを削除する
- ストレージ アカウント レベルで 1 日に 1 回実行されるようにルールを定義する
- コンテナーまたは BLOB のサブセットにルールを適用する (名前のプレフィックスまたは [ インデックス タグ](storage-manage-find-blobs.md)をフィルターとして使用)

次のようなシナリオについて考えてみましょう。データがライフサイクルの初期段階には頻繁にアクセスされるものの、2 週間後にはたまにしか必要とされなくなります。 1 か月を超えると、そのデータ セットにはほとんどアクセスされなくなります。 このシナリオの初期段階ではホット ストレージが最適です。 ときどきアクセスされるデータにはクール ストレージが適しています。 1 か月以上が経過したデータに最も適しているのは、アーカイブ ストレージです。 データの古さを考慮してストレージ層を調整することで、ニーズに合った最も低コストのストレージ オプションを設計できます。 この移行を実現するために、ライフサイクル管理ポリシー ルールを使用して、古いデータをよりクールな層に移動することができます。

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

>[!NOTE]
>StorSimple で使用する場合など、データを読み取れるままにする必要がある場合は、BLOB をアーカイブ層に移動するポリシーを設定しないでください。

## <a name="availability-and-pricing"></a>可用性と料金

ライフサイクル管理機能は、General Purpose v2 (GPv2) アカウント、BLOB ストレージ アカウント、Premium ブロック BLOB ストレージ アカウント、Azure Data Lake Storage Gen2 アカウントのすべての Azure リージョンで利用できます。 Azure portal では、既存の General Purpose (GPv1) アカウントを GPv2 アカウントにアップグレードすることができます。 ストレージ アカウントについて詳しくは、「[Azure ストレージ アカウントの概要](../common/storage-account-overview.md)」をご覧ください。

ライフサイクル管理機能は無料です。 お客様には、[Set Blob Tier](/rest/api/storageservices/set-blob-tier) API 呼び出しの通常の運用コストが課金されます。 削除操作は無料です。 価格の詳細については、「[ブロック BLOBの料金](https://azure.microsoft.com/pricing/details/storage/blobs/)」を参照してください。

## <a name="add-or-remove-a-policy"></a>ポリシーを追加または削除する

ポリシーを追加、編集、または削除するには、次のいずれかを使用します。

* [Azure Portal](https://portal.azure.com)
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Azure CLI](/cli/azure/install-azure-cli)
* [REST API](/rest/api/storagerp/managementpolicies)

ポリシーは、全体として読み取ったり書き込んだりすることができます。 部分的な更新はサポートされません。 

> [!NOTE]
> ストレージ アカウントのファイアウォール ルールを有効にしている場合、ライフサイクル管理要求がブロックされることがあります。 信頼できる Microsoft サービスに例外を指定することで、このような要求のブロックを解除できます。 詳細については、[ファイアウォールおよび仮想ネットワークの構成](../common/storage-network-security.md#exceptions)に関するページの「例外」セクションを参照してください。

この記事では、ポータルと PowerShell の方法を使用してポリシーを管理する方法について説明します。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal を通じてポリシーを追加するには、2つの方法があります。 

* [Azure portal リスト ビュー](#azure-portal-list-view)
* [Azure portal コード ビュー](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Azure portal リスト ビュー

1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure portal で、自分のストレージ アカウントを検索して選択します。 

1. **[Blob service]** で、 **[ライフサイクル管理]** を選択してルールを表示または変更します。

1. **[リスト ビュー]** タブを選択します。

1. **[ルールの追加]** を選択し、 **[詳細]** フォームでルールに名前を付けることができます。 また、 **[規則のスコープ]** 、 **[BLOB の種類]** 、 **[BLOB のサブタイプ]** の各値を設定することもできます。 次の例では、BLOB をフィルター処理するスコープを設定します。 これにより、 **[フィルター セット]** タブが追加されます。

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-details.png" alt-text="Azure portal の [ライフサイクル管理] の [ルールの追加] の [詳細] ページ":::

1. **[Base blobs]\(ベース BLOB\)** を選択して、ルールの条件を設定します。 次の例では、BLOB が 30 日間変更されない場合、BLOB はクール ストレージに移動されます。

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-base-blobs.png" alt-text="Azure portal の [ライフサイクル管理] の [Base blobs]\(ベース BLOB\) ページ":::

   **[最終アクセス日時]** オプションは、次のリージョンにおいてプレビューで使用できます。

    - フランス中部
    - カナダ東部
    - カナダ中部

   > [!IMPORTANT]
   > 最終アクセス時刻追跡プレビューは、非運用環境のみで使用されます。 運用環境のサービス レベル契約(SLA) は現在使用できません。
   
   **最終アクセス日時** オプションを使用するために、Azure portal の **ライフサイクル管理** ページで **アクセス追跡有効** を選択します。 **[最終アクセス日時]** オプションの詳細については、「[最終アクセス日付に基づいてデータを移動させる (プレビュー)](#move-data-based-on-last-accessed-date-preview)」を参照してください。

1. **[詳細]** ページで **[フィルターを使用して BLOB を制限する]** を選択した場合は、 **[フィルター セット]** を選択して省略可能なフィルターを追加します。 次の例では、"log" で始まる *mylifecyclecontainer* コンテナー内の BLOB に対してフィルター処理を行います。

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-filter-set.png" alt-text="Azure portal の [ライフサイクル管理] の [フィルター セット] ページ":::

1. **[追加]** を選択して新しいポリシーを追加します。

#### <a name="azure-portal-code-view"></a>Azure portal コード ビュー
1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure portal で、自分のストレージ アカウントを検索して選択します。

1. **[Blob service]** で、 **[Lifecycle Management]\(ライフサイクル管理\)** を選択してポリシーを表示または変更します。

1. 次の JSON は、 **[コード ビュー]** タブに貼り付けることができるポリシーの例です。

   ```json
   {
     "rules": [
       {
         "enabled": true,
         "name": "move-to-cool",
         "type": "Lifecycle",
         "definition": {
           "actions": {
             "baseBlob": {
               "tierToCool": {
                 "daysAfterModificationGreaterThan": 30
               }
             }
           },
           "filters": {
             "blobTypes": [
               "blockBlob"
             ],
             "prefixMatch": [
               "mylifecyclecontainer/log"
             ]
           }
         }
       }
     ]
   }
   ```

1. **[保存]** を選択します。

1. この JSON の例の詳細については、「[ポリシー](#policy)」セクションおよび「[ルール](#rules)」セクションを参照してください。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

次の PowerShell スクリプトを使用すると、お使いのストレージ アカウントにポリシーを追加できます。 `$rgname` 変数は、ご自身のリソース グループ名で初期化する必要があります。 `$accountName` 変数は、ご自身のストレージ アカウント名で初期化する必要があります。

```powershell
#Install the latest module
Install-Module -Name Az -Repository PSGallery

#Initialize the following with your resource group and storage account names
$rgname = ""
$accountName = ""

#Create a new action object
$action = Add-AzStorageAccountManagementPolicyAction -BaseBlobAction Delete -daysAfterModificationGreaterThan 2555
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToArchive -daysAfterModificationGreaterThan 90
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToCool -daysAfterModificationGreaterThan 30
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -SnapshotAction Delete -daysAfterCreationGreaterThan 90

# Create a new filter object
# PowerShell automatically sets BlobType as “blockblob” because it is the only available option currently
$filter = New-AzStorageAccountManagementPolicyFilter -PrefixMatch ab,cd

#Create a new rule object
#PowerShell automatically sets Type as “Lifecycle” because it is the only available option currently
$rule1 = New-AzStorageAccountManagementPolicyRule -Name Test -Action $action -Filter $filter

#Set the policy
Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgname -StorageAccountName $accountName -Rule $rule1
```

# <a name="template"></a>[テンプレート](#tab/template)

Azure Resource Manager テンプレートを使用してライフサイクル管理を定義することができます。 ライフサイクル管理ポリシーを含む RA-GRS GPv2 ストレージ アカウントをデプロイするサンプル テンプレートを次に示します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "storageAccountName": "[uniqueString(resourceGroup().id)]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-04-01",
      "sku": {
        "name": "Standard_RAGRS"
      },
      "kind": "StorageV2",
      "properties": {
        "networkAcls": {}
      }
    },
    {
      "name": "[concat(variables('storageAccountName'), '/default')]",
      "type": "Microsoft.Storage/storageAccounts/managementPolicies",
      "apiVersion": "2019-04-01",
      "dependsOn": [
        "[variables('storageAccountName')]"
      ],
      "properties": {
        "policy": {...}
      }
    }
  ],
  "outputs": {}
}
```

---

## <a name="policy"></a>ポリシー

ライフサイクル管理ポリシーは、JSON ドキュメントに記述されたルールのコレクションです。

```json
{
  "rules": [
    {
      "name": "rule1",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {...}
    },
    {
      "name": "rule2",
      "type": "Lifecycle",
      "definition": {...}
    }
  ]
}
```

ポリシーはルールのコレクションです。

| パラメーター名 | パラメーターのタイプ | Notes |
|----------------|----------------|-------|
| `rules`        | ルール オブジェクトの配列 | ポリシーには少なくとも 1 つのルールが必要です。 ポリシーでは最大 100 のルールを定義できます。|

ポリシー内の各ルールには、次のいくつかのパラメーターがあります。

| パラメーター名 | パラメーターのタイプ | Notes | 必須 |
|----------------|----------------|-------|----------|
| `name`         | String |ルール名には最大 256 の英数字を含めることができます。 ルール名は大文字と小文字が区別されます。 名前は、ポリシー内で一意にする必要があります。 | True |
| `enabled`      | Boolean | ルールを一時的に無効にすることを許可する省略可能なブール値。 設定されていない場合、既定値は true です。 | False | 
| `type`         | 列挙型の値 | 現在の有効な種類は `Lifecycle` です。 | True |
| `definition`   | ライフサイクル ルールを定義するオブジェクト | 各定義は、フィルター セットとアクション セットで構成されます。 | True |

## <a name="rules"></a>ルール

各ルール定義には、フィルター セットとアクション セットが含まれます。 [フィルター セット](#rule-filters)は、コンテナー内の特定のオブジェクト セットまたはオブジェクト名にルール アクションを制限します。 [アクション セット](#rule-actions)は、階層化または削除アクションをオブジェクトのフィルター処理されたセットに適用します。

### <a name="sample-rule"></a>ルールのサンプル

次のサンプル ルールは、`container1` 内に存在し、`foo` で始まるオブジェクトに対してアクションを実行するようにアカウントをフィルター処理します。

>[!NOTE]
>- ライフサイクル管理では、ブロック BLOB と追加 BLOB の種類がサポートされます。<br>
>- ライフサイクル管理は、$logs や $web などのシステム コンテナーには影響しません。

- BLOB を最後に変更されたときから 30 日後にクール層に階層化する
- BLOB を最後に変更されたときから 90 日後にアーカイブ層に階層化する
- BLOB を最後に変更されたときから 2,555 日 (7 年) 後に削除する
- 以前のバージョンの BLOB を作成から 90 日後に削除する

```json
{
  "rules": [
    {
      "enabled": true,
      "name": "rulefoo",
      "type": "Lifecycle",
      "definition": {
        "actions": {
          "version": {
            "delete": {
              "daysAfterCreationGreaterThan": 90
            }
          },
          "baseBlob": {
            "tierToCool": {
              "daysAfterModificationGreaterThan": 30
            },
            "tierToArchive": {
              "daysAfterModificationGreaterThan": 90
            },
            "delete": {
              "daysAfterModificationGreaterThan": 2555
            }
          }
        },
        "filters": {
          "blobTypes": [
            "blockBlob"
          ],
          "prefixMatch": [
            "container1/foo"
          ]
        }
      }
    }
  ]
}
```

### <a name="rule-filters"></a>ルール フィルター

フィルターを使用すると、ルール アクションをストレージ アカウント内の BLOB のサブセットに限定できます。 複数のフィルターが定義されている場合、すべてのフィルターで論理 `AND` が実行されます。

フィルターには次のものが含まれます。

| フィルター名 | フィルターの種類 | Notes | 必須 |
|-------------|-------------|-------|-------------|
| blobTypes   | 定義済みの列挙型の値の配列。 | 現在のリリースでは `blockBlob` および `appendBlob` をサポートしています。 `appendBlob` では削除のみがサポートされています。既定の層はサポートされていません。 | はい |
| prefixMatch | プレフィックスを照合する文字列の配列。 各ルールで最大 10 個のプレフィックスを定義できます。 プレフィックス文字列はコンテナー名で始まる必要があります。 たとえば、`https://myaccount.blob.core.windows.net/container1/foo/...` の下にあるすべての BLOB をルールに一致させたい場合、prefixMatch は `container1/foo` です。 | prefixMatch を定義していない場合、ルールはストレージ アカウント内のすべての BLOB に適用されます。 | いいえ |
| blobIndexMatch | 照合する BLOB インデックス タグ キーと値条件で構成されるディクショナリ値の配列。 各ルールには、最大 10 個の BLOB インデックス タグ条件を定義できます。 たとえば、ルールとして `https://myaccount.blob.core.windows.net/` の下にあるすべての BLOB を `Project = Contoso` に一致させたい場合、blobIndexMatch は `{"name": "Project","op": "==","value": "Contoso"}` になります。 | blobIndexMatch を定義していない場合、ルールはストレージ アカウント内のすべての BLOB に適用されます。 | いいえ |

> [!NOTE]
> BLOB インデックスはパブリック プレビュー中であり、**カナダ中部**、**カナダ東部**、**フランス中部**、および **フランス南部** リージョンで利用できます。 この機能と既知の問題と制限の詳細については、「[BLOB インデックスを使用して Azure Blob Storage でデータを管理および検索する (プレビュー)](storage-manage-find-blobs.md)」を参照してください。

### <a name="rule-actions"></a>ルールのアクション

実行条件が満たされている場合、アクションはフィルター処理された BLOB に適用されます。

ライフサイクル管理では、BLOB、以前の BLOB バージョン、および BLOB スナップショットの階層化と削除がサポートされています。 ベース BLOB、以前の BLOB バージョン、または BLOB スナップショットの各ルールに対して 1 つ以上のアクションを定義します。

| アクション                      | ベース BLOB                                  | スナップショット      | Version
|-----------------------------|--------------------------------------------|---------------|---------------|
| tierToCool                  | `blockBlob` でサポート                  | サポートされています     | サポートされています     |
| enableAutoTierToHotFromCool | `blockBlob` でサポート                  | サポートされていません | サポートされていません |
| tierToArchive               | `blockBlob` でサポート                  | サポートされています     | サポートされています     |
| delete                      | `blockBlob` および `appendBlob` に対してサポートされています | サポートされています     | サポートされています     |

>[!NOTE]
>同じ BLOB に複数のアクションを定義した場合、ライフサイクル管理によって最も低コストのアクションが BLOB に適用されます。 たとえば、`delete` アクションは `tierToArchive` アクションよりも低コストです。 `tierToArchive` アクションは `tierToCool` アクションよりも低コストです。

実行条件は、古さに基づいています。 ベース BLOB では、最終変更時刻を使用し、BLOB バージョンではバージョン作成時刻を使用し、BLOB スナップショットでは、スナップショットの作成時刻を使用して古さが追跡されます。

| アクションの実行条件               | 条件値                          | 説明                                                                      |
|------------------------------------|------------------------------------------|----------------------------------------------------------------------------------|
| daysAfterModificationGreaterThan   | 古さを日数で示す整数値 | ベース BLOB のアクションの条件                                              |
| daysAfterCreationGreaterThan       | 古さを日数で示す整数値 | BLOB バージョンおよび BLOB スナップショットのアクションの条件                         |
| daysAfterLastAccessTimeGreaterThan | 古さを日数で示す整数値 | (プレビュー) 最終アクセス日時が有効になっているときのベース BLOB アクションの条件 |

## <a name="examples"></a>例

次の例では、ライフサイクル ポリシー ルールを使用して一般的なシナリオに対処する方法を示します。

### <a name="move-aging-data-to-a-cooler-tier"></a>古いデータをよりクールな層に移動する

次の例は、プレフィックス `container1/foo` または `container2/bar` が付いたブロック BLOB を移行する方法を示しています。 このポリシーでは、30 日以上変更されていない BLOB をクール ストレージに移行し、90 日間変更されていない BLOB をアーカイブ層に移行します。

```json
{
  "rules": [
    {
      "name": "agingRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo", "container2/bar" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="move-data-based-on-last-accessed-date-preview"></a>最終アクセス日付に基づいてデータを移動させる (プレビュー)

最終アクセス時刻追跡機能を有効にして、BLOB が最後に読み書きされた時間を記録できます。 BLOB データの階層化と保持を管理するためのフィルターとして、最終アクセス時刻を使用できます。

**[最終アクセス日時]** オプションは、次のリージョンにおいてプレビューで使用できます。

 - フランス中部
 - カナダ東部
 - カナダ中部

> [!IMPORTANT]
> 最終アクセス時刻追跡プレビューは、非運用環境のみで使用されます。 運用環境のサービス レベル契約(SLA) は現在使用できません。

**最終アクセス日時** オプションを使用するために、Azure portal の **ライフサイクル管理** ページで **アクセス追跡有効** を選択します。

#### <a name="how-last-access-time-tracking-works"></a>最終アクセス時刻追跡機能のしくみ

最終アクセス時刻追跡機能が有効になっている場合、BLOB が読み取られるか書き込まれるときに `LastAccessTime` という名前の BLOB プロパティが更新されます。 [Get Blob](/rest/api/storageservices/get-blob) 操作はアクセス操作と見なされます。 [BLOB プロパティの取得](/rest/api/storageservices/get-blob-properties)、[BLOB メタデータの取得](/rest/api/storageservices/get-blob-metadata)、および [BLOB タグの取得](/rest/api/storageservices/get-blob-tags)は、アクセス操作ではないので、最終アクセス時刻を更新しません。

読み取りアクセス待ち時間への影響を最小限に抑えるために、過去 24 時間の最初の読み取りのみが最終アクセス時刻を更新します。 同じ 24 時間内のその後の読み取りでは、最終アクセス時刻は更新されません。 読み取り間で BLOB が変更された場合、最終アクセス時刻は 2 つの値のうち新しい方になります。

次の例では、BLOB は、30 日間アクセスされない場合に、クール ストレージに移動されます。 `enableAutoTierToHotFromCool` プロパティはブール値であり、BLOB がクールに階層化された後で再びアクセスされた場合に自動的にクールからホットに階層化するかどうかを示します。

```json
{
  "enabled": true,
  "name": "last-accessed-thirty-days-ago",
  "type": "Lifecycle",
  "definition": {
    "actions": {
      "baseBlob": {
        "enableAutoTierToHotFromCool": true,
        "tierToCool": {
          "daysAfterLastAccessTimeGreaterThan": 30
        }
      }
    },
    "filters": {
      "blobTypes": [
        "blockBlob"
      ],
      "prefixMatch": [
        "mylifecyclecontainer/log"
      ]
    }
  }
}
```

#### <a name="storage-account-support"></a>ストレージ アカウントのサポート

最終アクセス時刻追跡機能は、次の種類のストレージ アカウントで使用できます。

 - 汎用 v2 ストレージ アカウント
 - ブロック BLOB ストレージ アカウント
 - BLOB ストレージ アカウント

ストレージ アカウントが汎用 v1 アカウントの場合は、Azure portal を使用して、汎用 v2 アカウントにアップグレードします。

階層型名前空間が Azure Data Lake Storage Gen2 で使用可能になっているストレージ アカウントがサポートされるようになりました。

#### <a name="pricing-and-billing"></a>価格と課金

最終アクセス時刻のそれぞれの更新は、[その他の操作](https://azure.microsoft.com/pricing/details/storage/blobs/)と見なされます。

### <a name="archive-data-after-ingest"></a>取り込み後にデータをアーカイブする

また、クラウド内でアイドル状態のままとなり、格納されてからはほとんどアクセスされないデータもあります。 次のライフサイクル ポリシーは、取り込み直後にデータをアーカイブするように構成されます。 この例では、コンテナー `archivecontainer` 内のストレージ アカウントのブロック BLOB をアーカイブ層に移行します。 この移行は、最終変更時刻の 0 日後に BLOB を処理することによって実現されます。

> [!NOTE] 
> より効率的な方法として、BLOB をアーカイブ層に直接アップロードすることをお勧めします。 [PutBlob](/rest/api/storageservices/put-blob) または [PutBlockList](/rest/api/storageservices/put-block-list) の x-ms-access-tier ヘッダーを REST バージョン 2018-11-09 以降または最新の BLOB ストレージ クライアント ライブラリと使用できます。 

```json
{
  "rules": [
    {
      "name": "archiveRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "archivecontainer" ]
        },
        "actions": {
          "baseBlob": {
              "tierToArchive": { "daysAfterModificationGreaterThan": 0 }
          }
        }
      }
    }
  ]
}

```

### <a name="expire-data-based-on-age"></a>古さに基づいてデータを期限切れにする

データの中には、作成後数日または数か月後に失効することが想定されているものがあります。 データを古さに基づいて削除することでデータを期限切れに設定するように、ライフサイクル管理ポリシーを構成できます。 次の例は、365 日より古いすべてのブロック BLOB を削除するポリシーを示しています。

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 365 }
          }
        }
      }
    }
  ]
}
```

### <a name="delete-data-with-blob-index-tags"></a>BLOB インデックス タグを使用したデータを削除する
一部のデータは、削除対象として明示的にマークされている場合のみ有効期限切れになる必要があります。 期限切れにするには、データに BLOB インデックスのキー/値属性でタグ付けして、ライフサイクル管理ポリシーを構成します。 次の例は、`Project = Contoso` でタグ付けされたすべてのブロック BLOB を削除するポリシーを示しています。 BLOB インデックスの詳細については、[BLOB インデックスを使用した Azure Blob Storage でのデータの管理および検索 (プレビュー)](storage-manage-find-blobs.md) に関するページを参照してください。

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "DeleteContosoData",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "delete": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Project",
                            "op": "==",
                            "value": "Contoso"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ]
                }
            }
        }
    ]
}
```

### <a name="manage-versions"></a>バージョンの管理

有効期間全体にわたって定期的に変更およびアクセスされるデータの場合は、BLOB ストレージのバージョン管理を有効にすることで、オブジェクトの以前のバージョンを自動的に管理できます。 ポリシーを作成して、以前のバージョンを階層化または削除することができます。 バージョンの古さは、バージョンの作成時刻を評価することによって決定されます。 このポリシー ルールは、バージョンの作成後 90 日以上経過したコンテナー `activedata` 内の以前のバージョンをクール層に階層化し、365 日またはそれ以前のバージョンを削除します。

```json
{
  "rules": [
    {
      "enabled": true,
      "name": "versionrule",
      "type": "Lifecycle",
      "definition": {
        "actions": {
          "version": {
            "tierToCool": {
              "daysAfterCreationGreaterThan": 90
            },
            "delete": {
              "daysAfterCreationGreaterThan": 365
            }
          }
        },
        "filters": {
          "blobTypes": [
            "blockBlob"
          ],
          "prefixMatch": [
            "activedata"
          ]
        }
      }
    }
  ]
}
```

## <a name="faq"></a>よく寄せられる質問

**新しいポリシーを作成しましたが、アクションがすぐに実行されないのはなぜですか。**

ライフサイクル ポリシーは、プラットフォームによって 1 日に 1 回実行されます。 ポリシーを構成した後、アクションによっては、初回実行時に最大 24 時間かかる場合があります。

**既存のポリシーを更新した場合、アクションの実行にはどのくらいの時間がかかりますか。**

更新されたポリシーは、有効になるまで最大 24 時間かかります。 ポリシーが有効になると、アクションが実行されるまでに最大で 24 時間かかることがあります。 このため、ポリシーのアクションが完了するまでに最大 48 時間かかる可能性があります。

**アーカイブ済み BLOB を手動でリハイドレートしました。これが一時的にアーカイブ層に戻されないようにするにはどうすればよいですか。**

あるアクセス層から別のアクセス層に BLOB が移動されても、その BLOB の最終変更時刻は変わりません。 アーカイブ済み BLOB を手動でホット層にリハイドレートすると、その BLOB は、ライフサイクル管理エンジンによりアーカイブ層に戻されます。 この BLOB に影響を与えるルールを一時的に無効にして、再びアーカイブされないようにします。 BLOB が安全にアーカイブ層に移動されたら、ルールを再度有効にします。 BLOB を別の場所にコピーして、ホット層またはクール層から永続的に離れないようにすることもできます。

## <a name="next-steps"></a>次のステップ

誤って削除したデータを回復する方法を学習します。

- [Azure Storage Blob の論理的な削除](./soft-delete-blob-overview.md)

BLOB インデックスを使用してデータを管理および検索する方法について説明します。

- [BLOB インデックスを使用して Azure Blob Storage でデータを管理および検索する](storage-manage-find-blobs.md)