---
title: アーカイブ層から BLOB データをリハイドレートする
description: BLOB データにアクセスできるように、アーカイブ ストレージから BLOB をリハイドレートします。 アーカイブ済み BLOB をオンライン層にコピーします。
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/11/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 2f0ddca9cbd7d85909b1d86e68b92fa1d847476d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103225083"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>アーカイブ層から BLOB データをリハイドレートする

BLOB はアーカイブ アクセス層に含まれていますが、オフラインと見なされ、読み取りや変更はできません。 BLOB のメタデータはオンラインのままで使用可能であり、BLOB とそのプロパティの一覧を表示することができます。 BLOB データの読み取りと変更が可能なのは、ホットやクールなどのオンライン層のみになります。 アーカイブ アクセス層に格納されているデータを取得してアクセスするには、2 つのオプションがあります。

1. [アーカイブ済み BLOB をオンライン層にリハイドレートする](#rehydrate-an-archived-blob-to-an-online-tier) - [[BLOB 層の設定]](/rest/api/storageservices/set-blob-tier) 操作を使用して階層を変更することにより、アーカイブ BLOB をホットまたはクールにリハイドレートします。
2. [アーカイブ済み BLOB をオンライン層にコピーする](#copy-an-archived-blob-to-an-online-tier) - [[BLOB のコピー]](/rest/api/storageservices/copy-blob) 操作を使用して、アーカイブ BLOB の新しいコピーを作成します。 別の BLOB 名と、移動先の層としてホットまたはクールを指定します。

 アクセス層の詳細については、「[Azure Blob Storage: ホット、クール、アーカイブ ストレージ層](storage-blob-storage-tiers.md)」を参照してください。

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>アーカイブ済み BLOB をオンライン層にリハイドレートする

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

### <a name="lifecycle-management"></a>ライフサイクル管理

BLOB をリハイドレートしても、`Last-Modified` 時間は変わりません。 [ライフサイクル管理](storage-lifecycle-management-concepts.md)機能を使用すると、BLOB がリハイドレートされ、その後、`Last-Modified` 時間がポリシーに設定されたしきい値を超えたために、ライフサイクル管理ポリシーによって BLOB がアーカイブに戻されるというシナリオが生じることがあります。 このシナリオを回避するには、" *[アーカイブ済み BLOB をオンライン層にコピーする](#copy-an-archived-blob-to-an-online-tier)* " 方法を使用します。 このコピー方法では、`Last-Modified` 時間が更新された BLOB の新しいインスタンスが作成されるため、ライフサイクル管理ポリシーがトリガーされません。

## <a name="monitor-rehydration-progress"></a>リハイドレートの進行状況を監視する

リハイドレート中に、BLOB のプロパティの取得操作を使用して **アーカイブ ステータス** 属性をチェックし、層変更がいつ完了したかを確認します。 ステータスは、変更先の層に応じて "rehydrate-pending-to-hot" または "rehydrate-pending-to-cool" になります。 完了すると、アーカイブ ステータス プロパティが削除され、BLOB の **アクセス層** プロパティに新しい階層としてホット層またはクール層が反映されます。

## <a name="copy-an-archived-blob-to-an-online-tier"></a>アーカイブ済み BLOB をオンライン層にコピーする

アーカイブ BLOB をリハイドレートしたくない場合は、[[BLOB のコピー]](/rest/api/storageservices/copy-blob) 操作の実行を選択できます。 オンラインのホット層またはクール層で、使用する新しい BLOB が作成されている間、元の BLOB はアーカイブ内で未変更のままとなります。 **[BLOB のコピー]** 操作では、オプションの *x-ms-rehydrate-priority* プロパティを Standard または High に設定して、BLOB コピーを作成する優先順位を指定することもできます。

アーカイブからの BLOB のコピーは、選択されたリハイドレートの優先度によっては数時間かかることがあります。 **[BLOB のコピー]** 操作では、バックグラウンドでアーカイブ ソース BLOB が読み取られ、選択された移動先の層に新しいオンライン BLOB が作成されます。 BLOB を一覧表示すると新しい BLOB が表示される場合がありますが、ソース アーカイブ BLOB からの読み取りが完了し、データが新しいオンライン コピー先 BLOB に書き込まれるまでは、データを使用することができません。 新しい BLOB は独立したコピーであり、変更や削除を行ってもソース アーカイブ BLOB には影響しません。

> [!IMPORTANT]
> コピー先でコピーが正常に完了するまでは、コピー元 BLOB を削除しないでください。 コピー元 BLOB が削除された場合、コピー先 BLOB はコピーを完了できず、空になります。 *x-ms-copy-status* を確認して、コピー操作の状態を確認することができます。

アーカイブ BLOB は、同じストレージ アカウント内のオンラインの移動先の層にのみコピーできます。 アーカイブ BLOB を別のアーカイブ BLOB にコピーすることはサポートされていません。 次の表に、**BLOB のコピー** 操作の機能を示します。

|                                           | **ホット層のコピー元**   | **クール層のコピー元** | **アーカイブ層のコピー元**    |
| ----------------------------------------- | --------------------- | -------------------- | ------------------- |
| **ホット層のコピー先**                  | サポートされています             | サポートされています            | 同じアカウント内でサポートされています。リハイドレート保留               |
| **クール層のコピー先**                 | サポートされています             | サポートされています            | 同じアカウント内でサポートされています。リハイドレート保留               |
| **アーカイブ層のコピー先**              | サポートされています             | サポートされています            | サポートされていない         |

## <a name="pricing-and-billing"></a>価格と課金

アーカイブからホット層またはクール層への BLOB のリハイドレートは、読み取り操作およびデータ取得として課金されます。 High 優先度を使用すると、Standard 優先度と比較して、操作とデータ取得のコストが高くなります。 High 優先度のリハイドレートは、請求書に別の行の項目として表示されます。 数ギガバイトのアーカイブ BLOB を返す High 優先度の要求で 5 時間以上かかった場合、High 優先度の取得料金は課金されません。 ただし、そのリハイドレートがその他の要求より優先順位が高い場合にも、標準の取得レートが適用されます。

アーカイブからホット層またはクール層への BLOB のコピーは、読み取り操作およびデータ取得として課金されます。 書き込み操作は、新しい BLOB コピーの作成に対して課金されます。 ソース BLOB はアーカイブ層で変更されていないため、オンライン BLOB にコピーする場合、早期削除料金は適用されません。 優先順位の高い取得料金は、選択した場合に適用されます。

アーカイブ層の BLOB は、少なくとも 180 日間格納する必要があります。 180 日間より前にアーカイブ済み BLOB を削除またはリハイドレートすると、早期削除料金が発生します。

> [!NOTE]
> ブロック BLOB とデータ ハイドレートの価格の詳細については、[Azure Storage の価格](https://azure.microsoft.com/pricing/details/storage/blobs/)に関するページを参照してください。 送信データ転送の価格の詳細については、[データ転送料金の詳細](https://azure.microsoft.com/pricing/details/data-transfers/)に関するページを参照してください。

## <a name="quickstart-scenarios"></a>クイックスタート シナリオ

### <a name="rehydrate-an-archive-blob-to-an-online-tier"></a>アーカイブ BLOB をオンライン層にリハイドレートする
# <a name="portal"></a>[ポータル](#tab/azure-portal)
1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure portal で、 **[すべてのリソース]** を検索して選択します。

1. 使うストレージ アカウントを選びます。

1. コンテナーを選択し、お使いのBLOB を選択します。

1. **[BLOB のプロパティ]** で、 **[層の変更]** を選択します。

1. **ホット** または **クール** なアクセス層を選択します。 

1. **Standarｄ** または **High** のリハイドレート優先度を選択します。

1. 下部にある **[保存]** を選択します。

![ストレージ アカウント層を変更する](media/storage-tiers/blob-access-tier.png)
![リハイドレート ステータスを確認する](media/storage-tiers/rehydrate-status.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
次の PowerShell スクリプトを使用すると、アーカイブ BLOB の BLOB 層を変更できます。 `$rgName` 変数は、ご自身のリソース グループ名で初期化する必要があります。 `$accountName` 変数は、ご自身のストレージ アカウント名で初期化する必要があります。 `$containerName` 変数は、ご自身のコンテナー名で初期化する必要があります。 `$blobName` 変数は、ご自身の BLOB 名で初期化する必要があります。 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to Hot using Standard priority rehydrate
$blob.ICloudBlob.SetStandardBlobTier("Hot", "Standard")
```
---

### <a name="copy-an-archive-blob-to-a-new-blob-with-an-online-tier"></a>オンライン層を使用して、アーカイブ BLOB を新しい BLOB にコピーする
次の PowerShell スクリプトを使用すると、アーカイブ BLOB を同じストレージアカウント内の新しい BLOB にコピーできます。 `$rgName` 変数は、ご自身のリソース グループ名で初期化する必要があります。 `$accountName` 変数は、ご自身のストレージ アカウント名で初期化する必要があります。 `$srcContainerName`と`$destContainerName`の変数は、ご自身のコンテナー名で初期化する必要があります。 `$srcBlobName`と`$destBlobName`の変数は、ご自身の BLOB 名で初期化する必要があります。 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$srcContainerName = ""
$destContainerName = ""
$srcBlobName = ""
$destBlobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Copy source blob to a new destination blob with access tier hot using standard rehydrate priority
Start-AzStorageBlobCopy -SrcContainer $srcContainerName -SrcBlob $srcBlobName -DestContainer $destContainerName -DestBlob $destBlobName -StandardBlobTier Hot -RehydratePriority Standard -Context $ctx
```

## <a name="next-steps"></a>次の手順

* [Blob Storage 層について学習する](storage-blob-storage-tiers.md)
* [BLOB ストレージ アカウントと GPv2 アカウントのホット、クール、アーカイブのリージョンごとの料金を確認する](https://azure.microsoft.com/pricing/details/storage/)
* [Azure Blob Storage のライフサイクルの管理](storage-lifecycle-management-concepts.md)
* [データ転送の価格を確認する](https://azure.microsoft.com/pricing/details/data-transfers/)