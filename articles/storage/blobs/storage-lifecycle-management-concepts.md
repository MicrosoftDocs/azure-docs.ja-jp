---
title: Azure Storage のライフサイクルの管理
description: 古いデータをホット層からクール層およびアーカイブ層へ移行するためのライフサイクル ポリシー ルールの作成方法について説明します。
services: storage
author: yzheng-msft
ms.service: storage
ms.topic: article
ms.date: 11/04/2018
ms.author: yzheng
ms.component: common
ms.openlocfilehash: 5c77d7d8f1ce3b4a13e497d461244aae5b34d08c
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53631364"
---
# <a name="managing-the-azure-blob-storage-lifecycle-preview"></a>Azure Blob Storage のライフサイクルの管理 (プレビュー)

データ セットには一意のライフサイクルがあります。 ライフサイクルの早い段階で、一部のデータに頻繁にアクセスされます。 しかし、データが古くなるとアクセスの必要性が急激に低下します。 また、クラウド内で使用されず、格納された後もほとんどアクセスされないデータもあります。 データには、作成後数日または数か月で失効するものがあります。また、ライフサイクルにわたってアクティブに読み取られ、変更されるデータ セットもあります。 Azure Blob Storage のライフサイクル管理 (プレビュー) には、GPv2 および BLOB ストレージ アカウント用に豊富なルールベース ポリシーが用意されています。 このポリシーを使用して、適切なアクセス層にデータを移行します。または、データのライフサイクルの終了時に期限切れにします。

ライフサイクル管理ポリシーによって、以下を行えます。

- パフォーマンスとコストを最適化するために、BLOB をよりクールなストレージ層に移行する (ホットからクール、ホットからアーカイブ、またはクールからアーカイブ)
- ライフサイクルの最後に BLOB を削除する
- ストレージ アカウント レベルで 1 日に 1 回実行されるようにルールを定義する
- コンテナーまたは BLOB のサブセットにルールを適用する (プレフィックスをフィルターとして使用)

次のようなシナリオについて考えてみましょう。データ セットがライフサイクルの初期段階には頻繁にアクセスされるものの、2 週間後にはたまにしか必要とされなくなります。 1 か月を超えると、そのデータ セットにはほとんどアクセスされなくなります。 このシナリオの初期段階ではホット ストレージが最適です。 ときどきアクセスされるデータにはクール ストレージが適し、1 か月以上経過したデータにはアーカイブ ストレージが最善の層になります。 データの古さを考慮してストレージ層を調整することで、ニーズに合った最も低コストのストレージ オプションを設計できます。 この移行を実現するために、ライフサイクル管理ポリシー ルールを使用して、古いデータをよりクールな層に移動することができます。

## <a name="storage-account-support"></a>ストレージ アカウントのサポート

ライフサイクル管理ポリシーは、General Purpose v2 (GPv2) アカウントと Blob Storage アカウントの両方で利用できます。 Azure portal では、簡単なワンクリック プロセスを通じて、既存の General Purpose (GPv1) アカウントを GPv2 アカウントにアップグレードすることができます。 ストレージ アカウントについて詳しくは、「[Azure ストレージ アカウントの概要](../common/storage-account-overview.md)」をご覧ください。  

## <a name="pricing"></a>価格 

プレビュー段階のライフサイクル管理機能は無料です。 お客様には、[List Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) および [Set Blob Tier](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) API 呼び出しの通常の運用コストが課金されます。 価格の詳細については、「[ブロック BLOBの料金](https://azure.microsoft.com/pricing/details/storage/blobs/)」を参照してください。

## <a name="register-for-preview"></a>プレビューの利用登録 
パブリック プレビューに登録するには、この機能をサブスクリプションに登録するための要求を送信する必要があります。 要求は通常 2 週間以内に承認されます。 承認されると、次のリージョンのすべての既存および新規 GPv2 または BLOB ストレージ アカウントには、以下の機能が含まれます。米国西部 2、米国中西部、米国東部 2、および西ヨーロッパで使用できます。 プレビューでサポートされるのは、ブロック BLOB のみです。 ほとんどのプレビューと同様、この機能は GA に達するまで実稼働ワークロードに使用しないでください。

要求を送信するには、次の PowerShell または CLI コマンドを実行します。

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

要求を送信するには:

```powershell
Register-AzProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage 
```
登録承認状態は、次のコマンドで確認できます。
```powershell
Get-AzProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage
```
承認され、適切に登録されていると、以前の要求を送信したときに、*登録済み*状態を受け取ります。

### <a name="azure-cli"></a>Azure CLI

要求を送信するには: 
```cli
az feature register --namespace Microsoft.Storage --name DLM
```
登録承認状態は、次のコマンドで確認できます。
```cli
az feature show --namespace Microsoft.Storage --name DLM
```
承認され、適切に登録されていると、以前の要求を送信したときに、*登録済み*状態を受け取ります。


## <a name="add-or-remove-a-policy"></a>ポリシーを追加または削除する 

ポリシーを追加、編集、または削除するには、Azure portal、[PowerShell](https://www.powershellgallery.com/packages/Az.Storage)、[Azure CLI](https://docs.microsoft.com/cli/azure/ext/storage-preview/storage/account/management-policy?view=azure-cli-latest#ext-storage-preview-az-storage-account-management-policy-create)、[REST API](https://docs.microsoft.com/rest/api/storagerp/managementpolicies/createorupdate)、または [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/8.0.0-preview)、[Python](https://pypi.org/project/azure-mgmt-storage/2.0.0rc3/)、[Node.js]( https://www.npmjs.com/package/azure-arm-storage/v/5.0.0)、[Ruby](https://rubygems.org/gems/azure_mgmt_storage/versions/0.16.2) の言語のクライアント ツールを使用することができます。 

### <a name="azure-portal"></a>Azure ポータル

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. **[すべてのリソース]** を選択し、ストレージ アカウントを選択します。

3. ポリシーを表示または変更するには、Blob service の下にグループ化された **[ライフサイクル管理 (プレビュー)]** を選択します。

### <a name="powershell"></a>PowerShell

```powershell
$rules = '{ ... }' 

Set-AzStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName] -Policy $rules 

Get-AzStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName]
```

### <a name="azure-cli"></a>Azure CLI

```
az account set --subscription "[subscriptionName]”
az extension add --name storage-preview
az storage account management-policy show --resource-group [resourceGroupName] --account-name [accountName]
```

> [!NOTE]
ストレージ アカウントのファイアウォール ルールを有効にしている場合、ライフサイクル管理要求がブロックされることがあります。 例外を指定することで、これらの要求のブロックを解除することができます。 詳細については、[ファイアウォールおよび仮想ネットワークの構成](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)に関するページの「例外」セクションを参照してください。

## <a name="policy"></a>ポリシー

ライフサイクル管理ポリシーは、JSON ドキュメントに記述されたルールのコレクションです。

```json
{
  "version": "0.5",
  "rules": [
    {
      "name": "rule1",
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


ポリシーでは、次の 2 つのパラメーターが必要です。

| パラメーター名 | パラメーターのタイプ | メモ |
|----------------|----------------|-------|
| version        | `x.x` として表される文字列 | プレビューのバージョン番号は 0.5 です。 |
| 規則          | ルール オブジェクトの配列 | 各ポリシーには少なくとも 1 つのルールが必要です。 プレビュー段階では、ポリシーごとに最大 4 つのルールを指定できます。 |

ポリシー内の各ルールでは、次の 3 つのパラメーターが必要です。

| パラメーター名 | パラメーターのタイプ | メモ |
|----------------|----------------|-------|
| Name           | String | ルール名には、英数字の任意の組み合わせを含めることができます。 ルール名は大文字と小文字が区別されます。 名前は、ポリシー内で一意にする必要があります。 |
| type           | 列挙型の値 | プレビューで有効な値は `Lifecycle` です。 |
| 定義     | ライフサイクル ルールを定義するオブジェクト | 各定義は、フィルター セットとアクション セットで構成されます。 |

## <a name="rules"></a>ルール

各ルール定義には、フィルター セットとアクション セットが含まれます。 [フィルター セット](#rule-filters)は、コンテナー内の特定のオブジェクト セットまたはオブジェクト名にルール アクションを制限します。 [アクション セット](#rule-actions)は、階層化または削除アクションをオブジェクトのフィルター処理されたセットに適用します。

### <a name="sample-rule"></a>ルールのサンプル
次のサンプル ルールは、`container1/foo` だけでアクションが実行されるように、アカウントをフィルター処理します。 `container1` 内に存在し、**かつ**、`foo` で始まるすべてのオブジェクトに対し、以下のアクションが実行されます。 

- BLOB を最後に変更されたときから 30 日後にクール層に階層化する
- BLOB を最後に変更されたときから 90 日後にアーカイブ層に階層化する
- BLOB を最後に変更されたときから 2,555 日 (7 年) 後に削除する
- BLOB のスナップショットを作成したときから 90 日後にスナップショットを削除する

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "ruleFoo", 
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

プレビュー段階で有効なフィルターは次のとおりです。

| フィルター名 | フィルターの種類 | メモ | 必須 |
|-------------|-------------|-------|-------------|
| blobTypes   | 定義済みの列挙型の値の配列。 | プレビュー リリースでは、`blockBlob` のみがサポートされています。 | はい |
| prefixMatch | プレフィックスを照合する文字列の配列。 プレフィックス文字列はコンテナー名で始まる必要があります。 たとえば、"https://myaccount.blob.core.windows.net/container1/foo/..." の下にあるすべての BLOB をルールに一致させたい場合、prefixMatch は "`container1/foo`" です。 | prefixMatch を定義していない場合、ルールはアカウント内のすべての BLOB に適用されます。 | いいえ  |

### <a name="rule-actions"></a>ルールのアクション

アクションは、実行条件が満たされるとフィルター処理された BLOB に適用されます。

ライフサイクル管理のプレビューでは、BLOB の階層化と削除、および BLOB スナップショットの削除がサポートされています。 BLOB または BLOB スナップショットの各ルールに対して 1 つ以上のアクションを定義します。

| Action        | ベース BLOB                                   | スナップショット      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | 現在ホット層にある BLOB をサポートします         | サポートされていません |
| tierToArchive | 現在ホット層またはクール層にある BLOB をサポートします | サポートされていません |
| 削除        | サポートされています                                   | サポートされています     |

>[!NOTE] 
同じ BLOB に複数のアクションを定義した場合、ライフサイクル管理によって最も低コストのアクションが BLOB に適用されます。 たとえば、`delete` アクションは `tierToArchive` アクションよりも低コストです。 `tierToArchive` アクションは `tierToCool` アクションよりも低コストです。

プレビューでは、アクションの実行条件は古さに基づいています。 ベース BLOB では、最終変更時刻を使用して古さが追跡されます。BLOB スナップショットでは、スナップショットの作成時刻を使用して古さが追跡されます。

| アクションの実行条件 | 条件値 | 説明 |
|----------------------------|-----------------|-------------|
| daysAfterModificationGreaterThan | 古さを日数で示す整数値 | ベース BLOB のアクションに有効な条件 |
| daysAfterCreationGreaterThan     | 古さを日数で示す整数値 | BLOB スナップショットのアクションに有効な条件 | 

## <a name="examples"></a>例
次の例では、ライフサイクル ポリシー ルールを使用して一般的なシナリオに対処する方法を示します。

### <a name="move-aging-data-to-a-cooler-tier"></a>古いデータをよりクールな層に移動する

次の例は、プレフィックス `container1/foo` または `container2/bar` が付いたブロック BLOB を移行する方法を示しています。 このポリシーでは、30 日以上変更されていない BLOB をクール ストレージに移行し、90 日間変更されていない BLOB をアーカイブ層に移行します。

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "agingRule", 
      "type": "Lifecycle", 
      "definition": 
        {
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

また、クラウド内でアイドル状態のままとなり、格納されてからはほとんどアクセスされないデータもあります。 このようなデータは、取り込んだ後即座にアーカイブします。 次のライフサイクル ポリシーは、取り込み時にデータをアーカイブするように構成されています。 この例では、コンテナー `archivecontainer` 内のストレージ アカウントのブロック BLOB を即座にアーカイブ層に移行します。 即時の移行は、最終変更時刻後の 0 日後に BLOB に作用することによって実現されます。

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "archiveRule", 
      "type": "Lifecycle", 
      "definition": 
        {
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

データの中には、コストを削減したり、政府の要件を満たしたりするために、作成後数日または数か月後に失効することが想定されているものがあります。 データを古さに基づいて削除することでデータを期限切れに設定するように、ライフサイクル管理ポリシーを構成できます。 次の例は、365 日より古い (プレフィックスが指定されていない) のすべてのブロック BLOB を削除するポリシーを示しています。

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "expirationRule", 
      "type": "Lifecycle", 
      "definition": 
        {
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
  "version": "0.5",
  "rules": [ 
    {
      "name": "snapshotRule", 
      "type": "Lifecycle", 
      "definition": 
        {
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
## <a name="faq---i-created-a-new-policy-why-are-the-actions-not-run-immediately"></a>FAQ - 新しいポリシーを作成しましたが、アクションがすぐに実行されないのはなぜですか? 

ライフサイクル ポリシーは、プラットフォームによって 1 日に 1 回実行されます。 新しいポリシーを設定した場合、一部のアクション (階層化や削除など) が開始されて実行されるまでに、最大で 24 時間がかかることがあります。  

## <a name="next-steps"></a>次の手順

誤って削除したデータを回復する方法を学習します。

- [Azure Storage Blob の論理的な削除](../blobs/storage-blob-soft-delete.md)
