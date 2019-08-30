---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: ee368b58195d61a1c6792a3a3655122af7104d58
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012184"
---
### <a name="azure-storage-linked-service"></a>Azure Storage のリンクされたサービス
**Azure Storage のリンクされたサービス**を利用すると、**アカウント キー**を使用して Azure ストレージ アカウントを Azure データ ファクトリにリンクすることができます。これにより、データ ファクトリは Azure Storage にグローバルにアクセスすることができます。 次の表は、Azure Storage のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type |type プロパティは、次のように設定する必要があります:**AzureStorage** |はい |
| connectionString |connectionString プロパティのために Azure Storage に接続するために必要な情報を指定します。 |はい |

Azure Storage のアカウント キーを表示またはコピーする手順については、次のセクションを参照してください。[アクセス キー](../articles/storage/common/storage-account-manage.md#access-keys)。

**例:**  

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="azure-storage-sas-linked-service"></a>Azure Storage SAS のリンクされたサービス
Shared Access Signature (SAS) を使用すると、ストレージ アカウント内のリソースへの委任アクセスが可能になります。 これにより、ストレージ アカウントのオブジェクトへの制限付きアクセス許可を、期間とアクセス許可セットを指定してクライアントに付与できます。また、アカウント アクセス キーを共有する必要はありません。 SAS とは、ストレージ リソースへの認証アクセスに必要なすべての情報をクエリ パラメーター内に含む URI です。 クライアントは、SAS 内で適切なコンストラクターまたはメソッドに渡すだけで、SAS でストレージ リソースにアクセスできます。 SAS の詳細については、「[Shared Access Signatures (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](../articles/storage/common/storage-sas-overview.md)」を参照してください。

> [!IMPORTANT]
> Azure Data Factory は、**サービス SAS** のみをサポートします。アカウント SAS はサポートしません。 Azure Portal またはストレージ エクスプローラーから生成できる SAS URL はアカウント SAS であり、サポートされません。

> [!TIP]
> 以下の PowerShell コマンドを使用して、ストレージ アカウント用のサービス SAS を生成できます (プレースホルダーを置き換え、必要なアクセス許可を付与します): `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Azure Storage SAS のリンクされたサービスを利用すると、Shared Access Signature (SAS) を使用して Azure ストレージ アカウントを Azure Data Factory にリンクできます。 これによって、Data Factory は、ストレージ内のすべてまたは特定のリソース (BLOB/コンテナー) へのアクセスが制限付きまたは期限付きになります。 次の表は、Azure Storage SAS のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。 

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type |type プロパティは、次のように設定する必要があります:**AzureStorageSas** |はい |
| sasUri |BLOB、コンテナー、テーブルなどの Azure Storage リソースへの Shared Access Signature URI を指定します。  |はい |

**例:**

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<Specify SAS URI of the Azure Storage resource>"
        }
    }
}
```

**SAS URI**を作成する際は、次の点を考慮してください。  

* リンクされたサービス (読み取り、書き込み、読み取り/書き込み) がデータ ファクトリ内でどのように使用されているかに応じて、オブジェクトに対する適切な読み取り/書き込み**アクセス許可**を設定します。
* **有効期限**を適切に設定します。 Azure Storage オブジェクトへのアクセスがパイプラインのアクティブな期間内に期限切れにならないことを確認します。
* URI は、必要に応じて、適切なコンテナーや BLOB またはテーブル レベルで作成する必要があります。 Azure BLOB への SAS URI を使用すると、Data Factory サービスから特定の BLOB にアクセスできます。 Azure BLOB コンテナーへの SAS URI を使用すると、Data Factory サービスはそのコンテナー内の BLOB に対して反復処理を行うことができます。 アクセスするオブジェクトの数を後で変更する必要がある場合、または SAS URI を更新する必要がある場合は、リンクされたサービスを新しい URI で更新することを忘れないでください。   

