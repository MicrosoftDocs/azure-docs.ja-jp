---
title: Azure Storage のライフサイクルの管理
description: 古いデータをホット層からクール層およびアーカイブ層へ移行するためのライフサイクル ポリシー ルールの作成方法について説明します。
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mhopkins
ms.reviewer: yzheng
ms.subservice: common
ms.openlocfilehash: 43a673621aa3c114f99479a6da97153dae44990d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67696088"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>Azure Blob Storage のライフサイクルを管理する

データ セットには一意のライフサイクルがあります。 ライフサイクルの早い段階で、一部のデータに頻繁にアクセスされます。 しかし、データが古くなるとアクセスの必要性が急激に低下します。 また、クラウド内で使用されず、格納された後もほとんどアクセスされないデータもあります。 データには、作成後数日または数か月で失効するものがあります。また、ライフサイクルにわたってアクティブに読み取られ、変更されるデータ セットもあります。 Azure Blob Storage のライフサイクル管理には、GPv2 および BLOB ストレージ アカウントのための豊富な、ルール ベースのポリシーが用意されています。 このポリシーを使用して、適切なアクセス層にデータを移行します。または、データのライフサイクルの終了時に期限切れにします。

ライフサイクル管理ポリシーによって、以下を行えます。

- パフォーマンスとコストを最適化するために、BLOB をよりクールなストレージ層に移行する (ホットからクール、ホットからアーカイブ、またはクールからアーカイブ)
- ライフサイクルの最後に BLOB を削除する
- ストレージ アカウント レベルで 1 日に 1 回実行されるようにルールを定義する
- コンテナーまたは BLOB のサブセットにルールを適用する (プレフィックスをフィルターとして使用)

次のようなシナリオについて考えてみましょう。データがライフサイクルの初期段階には頻繁にアクセスされるものの、2 週間後にはたまにしか必要とされなくなります。 1 か月を超えると、そのデータ セットにはほとんどアクセスされなくなります。 このシナリオの初期段階ではホット ストレージが最適です。 ときどきアクセスされるデータにはクール ストレージが適しています。 1 か月以上が経過したデータに最も適しているのは、アーカイブ ストレージです。 データの古さを考慮してストレージ層を調整することで、ニーズに合った最も低コストのストレージ オプションを設計できます。 この移行を実現するために、ライフサイクル管理ポリシー ルールを使用して、古いデータをよりクールな層に移動することができます。

## <a name="storage-account-support"></a>ストレージ アカウントのサポート

ライフサイクル管理ポリシーは、General Purpose v2 (GPv2) アカウント、Blob Storage アカウント、Premium Block Blob Storage アカウントで利用できます。 Azure portal では、既存の General Purpose (GPv1) アカウントを GPv2 アカウントにアップグレードすることができます。 ストレージ アカウントについて詳しくは、「[Azure ストレージ アカウントの概要](../common/storage-account-overview.md)」をご覧ください。  

## <a name="pricing"></a>価格

ライフサイクル管理機能は無料です。 お客様には、[List Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) および [Set Blob Tier](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) API 呼び出しの通常の運用コストが課金されます。 削除操作は無料です。 価格の詳細については、「[ブロック BLOBの料金](https://azure.microsoft.com/pricing/details/storage/blobs/)」を参照してください。

## <a name="regional-availability"></a>リージョン別の提供状況

ライフサイクル管理機能は、世界中のあらゆる Azure リージョンと Azure Government リージョンで使用できます。

## <a name="add-or-remove-a-policy"></a>ポリシーを追加または削除する

ポリシーを追加、編集、または削除するには、次のいずれかを使用します。

* [Azure Portal](https://portal.azure.com)
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [REST API](https://docs.microsoft.com/rest/api/storagerp/managementpolicies)

この記事では、ポータルと PowerShell の方法を使用してポリシーを管理する方法について説明します。  

> [!NOTE]
> ストレージ アカウントのファイアウォール ルールを有効にしている場合、ライフサイクル管理要求がブロックされることがあります。 例外を指定することで、これらの要求のブロックを解除することができます。 必要なバイパスは `Logging,  Metrics,  AzureServices` です。 詳細については、[ファイアウォールおよび仮想ネットワークの構成](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)に関するページの「例外」セクションを参照してください。

### <a name="azure-portal"></a>Azure ポータル

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. **[すべてのリソース]** を選択してから、ストレージ アカウントを選択します。

3. **[Blob service]** で、 **[Lifecycle management]\(ライフサイクル管理)** を選択してポリシーを表示または変更します。

4. 次の JSON はルールの例です。これは、 **[ライフ サイクル管理]** ポータル ページに貼り付けることができます。

   ```json
   {
     "rules": [
       {
         "name": "ruleFoo",
         "enabled": true,
         "type": "Lifecycle",
         "definition": {
           "filters": {
             "blobTypes": [ "blockBlob" ],
             "prefixMatch": [ "container1/foo" ]
           },
           "actions": {
             "baseBlob": {
               "tierToCool": { "daysAfterModificationGreaterThan": 30 },
               "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
               "delete": { "daysAfterModificationGreaterThan": 2555 }
             },
             "snapshot": {
               "delete": { "daysAfterCreationGreaterThan": 90 }
             }
           }
         }
       }
     ]
   }
   ```

5. この JSON の例の詳細については、「[ポリシー](#policy)」セクションおよび「[ルール](#rules)」セクションを参照してください。

### <a name="powershell"></a>PowerShell

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
$policy = Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgname -StorageAccountName $accountName -Rule $rule1
```

## <a name="azure-resource-manager-template-with-lifecycle-management-policy"></a>ライフサイクル管理ポリシーを含む Azure Resource Manager テンプレート

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

| パラメーター名 | パラメーターのタイプ | メモ |
|----------------|----------------|-------|
| `rules`        | ルール オブジェクトの配列 | ポリシーには少なくとも 1 つのルールが必要です。 ポリシーでは最大 100 のルールを定義できます。|

ポリシー内の各ルールには、次のいくつかのパラメーターがあります。

| パラメーター名 | パラメーターのタイプ | メモ | 必須 |
|----------------|----------------|-------|----------|
| `name`         | string |ルール名には最大 256 の英数字を含めることができます。 ルール名は大文字と小文字が区別されます。  名前は、ポリシー内で一意にする必要があります。 | True |
| `enabled`      | Boolean | ルールを一時的に無効にすることを許可する省略可能なブール値。 設定されていない場合、既定値は true です。 | False | 
| `type`         | 列挙型の値 | 現在の有効な種類は `Lifecycle` です。 | True |
| `definition`   | ライフサイクル ルールを定義するオブジェクト | 各定義は、フィルター セットとアクション セットで構成されます。 | True |

## <a name="rules"></a>ルール

各ルール定義には、フィルター セットとアクション セットが含まれます。 [フィルター セット](#rule-filters)は、コンテナー内の特定のオブジェクト セットまたはオブジェクト名にルール アクションを制限します。 [アクション セット](#rule-actions)は、階層化または削除アクションをオブジェクトのフィルター処理されたセットに適用します。

### <a name="sample-rule"></a>ルールのサンプル

次のサンプル ルールは、`container1` 内に存在し、`foo` で始まるオブジェクトに対してアクションを実行するようにアカウントをフィルター処理します。  

- BLOB を最後に変更されたときから 30 日後にクール層に階層化する
- BLOB を最後に変更されたときから 90 日後にアーカイブ層に階層化する
- BLOB を最後に変更されたときから 2,555 日 (7 年) 後に削除する
- BLOB のスナップショットを作成したときから 90 日後にスナップショットを削除する

```json
{
  "rules": [
    {
      "name": "ruleFoo",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
            "delete": { "daysAfterModificationGreaterThan": 2555 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="rule-filters"></a>ルール フィルター

フィルターを使用すると、ルール アクションをストレージ アカウント内の BLOB のサブセットに限定できます。 複数のフィルターが定義されている場合、すべてのフィルターで論理 `AND` が実行されます。

フィルターには次のものが含まれます。

| フィルター名 | フィルターの種類 | メモ | 必須 |
|-------------|-------------|-------|-------------|
| blobTypes   | 定義済みの列挙型の値の配列。 | 現在のリリースでは `blockBlob` をサポートしています。 | はい |
| prefixMatch | プレフィックスを照合する文字列の配列。 各ルールで最大 10 個のプレフィックスを定義できます。 プレフィックス文字列はコンテナー名で始まる必要があります。 たとえば、`https://myaccount.blob.core.windows.net/container1/foo/...` の下にあるすべての BLOB をルールに一致させたい場合、prefixMatch は `container1/foo` です。 | prefixMatch を定義していない場合、ルールはストレージ アカウント内のすべての BLOB に適用されます。  | いいえ |

### <a name="rule-actions"></a>ルールのアクション

実行条件が満たされている場合、アクションはフィルター処理された BLOB に適用されます。

ライフサイクル管理では、BLOB の階層化と削除、および BLOB スナップショットの削除がサポートされています。 BLOB または BLOB スナップショットの各ルールに対して 1 つ以上のアクションを定義します。

| Action        | ベース BLOB                                   | スナップショット      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | 現在ホット層にある BLOB をサポートします         | サポートされていません |
| tierToArchive | 現在ホット層またはクール層にある BLOB をサポートします | サポートされていません |
| delete        | サポートされています                                   | サポートされています     |

>[!NOTE]
>同じ BLOB に複数のアクションを定義した場合、ライフサイクル管理によって最も低コストのアクションが BLOB に適用されます。 たとえば、`delete` アクションは `tierToArchive` アクションよりも低コストです。 `tierToArchive` アクションは `tierToCool` アクションよりも低コストです。

実行条件は、古さに基づいています。 ベース BLOB では、最終変更時刻を使用して古さが追跡されます。BLOB スナップショットでは、スナップショットの作成時刻を使用して古さが追跡されます。

| アクションの実行条件             | 条件値                          | 説明                             |
|----------------------------------|------------------------------------------|-----------------------------------------|
| daysAfterModificationGreaterThan | 古さを日数で示す整数値 | ベース BLOB のアクションの条件     |
| daysAfterCreationGreaterThan     | 古さを日数で示す整数値 | BLOB スナップショットのアクションの条件 |

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

### <a name="archive-data-at-ingest"></a>取り込み時にデータをアーカイブする

また、クラウド内でアイドル状態のままとなり、格納されてからはほとんどアクセスされないデータもあります。 次のライフサイクル ポリシーは、取り込まれたデータをアーカイブするように構成されています。 この例では、コンテナー `archivecontainer` 内のストレージ アカウントのブロック BLOB をアーカイブ層に移行します。 この移行は、最終変更時刻の 0 日後に BLOB を処理することによって実現されます。

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

### <a name="delete-old-snapshots"></a>古いスナップショットを削除する

保存期間中に定期的に変更およびアクセスされるデータの場合、データの古いバージョンを追跡するためにスナップショットが使用されることがよくあります。 スナップショットの古さに基づいて古いスナップショットを削除するポリシーを作成できます。 スナップショットの古さは、スナップショットの作成時刻を評価することによって決定されます。 このポリシー ルールでは、スナップショットの作成時点から 90 日以上前の、コンテナー `activedata` 内のブロック BLOB のスナップショットを削除します。

```json
{
  "rules": [
    {
      "name": "snapshotRule",
      "enabled": true,
      "type": "Lifecycle",
    "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "activedata" ]
        },
        "actions": {
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

## <a name="faq"></a>FAQ

**新しいポリシーを作成しましたが、アクションがすぐに実行されないのはなぜですか。**  
ライフサイクル ポリシーは、プラットフォームによって 1 日に 1 回実行されます。 ポリシーを構成した後、アクションによっては、初回実行時に最大 24 時間かかる場合があります。  

**アーカイブ済み BLOB を手動でリハイドレートしました。これが一時的にアーカイブ層に戻されないようにするにはどうすればよいですか。**  
あるアクセス層から別のアクセス層に BLOB が移動されても、その BLOB の最終変更時刻は変わりません。 アーカイブ済み BLOB を手動でホット層にリハイドレートすると、その BLOB は、ライフサイクル管理エンジンによりアーカイブ層に戻されます。 この動作が発生しないようにするには、この BLOB に一時的に影響を及ぼすルールを無効にします。 BLOB がホット層から離れないようにするには、その BLOB を他の場所にコピーします。 BLOB が安全にアーカイブ層に移動されたら、ルールを再度有効にできます。 


## <a name="next-steps"></a>次の手順

誤って削除したデータを回復する方法を学習します。

- [Azure Storage Blob の論理的な削除](../blobs/storage-blob-soft-delete.md)
