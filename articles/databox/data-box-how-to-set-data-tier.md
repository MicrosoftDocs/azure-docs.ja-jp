---
title: Azure Data Box または Azure Data Box Heavy を使用してホット、コールド、アーカイブ BLOB 層にデータを送信する
description: Azure Data Box または Azure Data Box Heavy を使用して、適切なブロック BLOB ストレージ層 (ホット、コールド、アーカイブなど) にデータを送信する方法について説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 05/24/2019
ms.author: alkohli
ms.openlocfilehash: 80c4d8a70454c007ac45f588e59c03ef45f10933
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92125066"
---
# <a name="use-azure-data-box-or-azure-data-box-heavy-to-send-data-to-appropriate-azure-storage-blob-tier"></a>Azure Data Box または Azure Data Box Heavy を使用して適切な Azure Storage BLOB 層にデータを送信する

Azure Data Box は、独自のストレージ デバイスをユーザーに提供して、大量のデータを Azure に移動できるようにするものです。 ユーザーはこのデバイスにデータを格納して、返却します。 Data Box のデータは、ストレージ アカウントに関連付けられている既定の層にアップロードされます。 このデータは、別のストレージ層に移動できます。

この記事では、Data Box を使用してアップロードしたデータをホット、コールド、またはアーカイブ BLOB 層に移動する方法について説明します。 この記事は Azure Data Box と Azure Data Box Heavy の両方に該当します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="choose-the-correct-storage-tier-for-your-data"></a>データに対する適切なストレージ層を選択する

Azure storage では、非常にコスト パフォーマンスに優れた方法で、ホット、コールド、またはアーカイブという 3 つの異なる層にデータを保存できます。 ホット ストレージ層は、頻繁にアクセスされるデータを保存するのに最適です。 ホット ストレージは、クール ストレージとアーカイブ ストレージに比べてストレージ コストが高くなるものの、アクセス コストが最も低くなります。

クール ストレージ層は、30 日以上保存する必要がある、頻繁にはアクセスされないデータに適しています。 コールド層のストレージ コストは、ホット ストレージ層よりも低くなります。ただし、データ アクセス料金は、ホット層と比べて高くなります。

Azure アーカイブ層はオフラインであり、ストレージ コストは最も低くなりますが、アクセス コストは最も高くなります。 この層は、180 日以上アーカイブ ストレージに残るデータを保存することを目的としています。 それぞれの層および価格モデルの詳細については、「[ストレージ層の比較](../storage/blobs/storage-blob-storage-tiers.md)」を参照してください。

Data Box または Data Box Heavy のデータは、ストレージ アカウントに関連付けられているストレージ層にアップロードされます。 ストレージ アカウントの作成時、アクセス層をホットまたはコールドとして指定できます。 このデータは、ワークロードのアクセス パターンとコストに応じて、既定の層から別のストレージ層に移動できます。

オブジェクト ストレージ データの階層制御は、BLOB ストレージ アカウントまたは General Purpose v2 (GPv2) アカウントでのみ可能です。 General Purpose v1 (GPv1) アカウントは階層制御をサポートしていません。 データに対して適切なストレージ層を選択するには、「[Azure Blob Storage:Premium ストレージ層、ホット ストレージ層、クール ストレージ層、アーカイブ ストレージ層](../storage/blobs/storage-blob-storage-tiers.md)」を参照してください。

## <a name="set-a-default-blob-tier"></a>既定の BLOB 層を設定する

既定の BLOB 層は、Azure portal 内でストレージ アカウントを作成するときに指定します。 ストレージ タイプを GPv2 または BLOB ストレージとして選択すると、アクセス層の属性を指定できます。 既定では、ホット層が選択されています。

Data Box または Data Box Heavy の注文時にアカウントの新規作成を試みた場合は、この層を指定できません。 アカウントを作成した後、ポータル内でアカウントを変更して、既定のアクセス層を設定できます。

あるいは、最初にストレージ アカウントを作成するときに、アクセス層の属性を指定することもできます。 Data Box または Data Box Heavy を注文するときは、既存のストレージ アカウントを選択します。 ストレージ アカウントの作成時に既定の BLOB 層を設定する方法の詳細については、[Azure portal でのストレージ アカウントの作成](../storage/common/storage-account-create.md?tabs=portal)に関するページを参照してください。

## <a name="move-data-to-a-non-default-tier"></a>既定以外の層にデータを移動する

Data Box デバイスのデータを既定の層にアップロードした後、そのデータを既定以外の層に移動することもできます。 データを既定以外の層に移動するには、2 つの方法があります。

- **Azure Blob Storage ライフ サイクル管理** - ポリシー ベースのアプローチを使用して、データを自動的に階層制御したり、ライフ サイクル終了時に期限切れにしたりできます。 詳細については、「[Azure Blob Storage のライフサイクルの管理](../storage/blobs/storage-lifecycle-management-concepts.md)」を参照してください。
- **スクリプト** - Azure PowerShell でスクリプトを使用して、BLOB レベルの階層制御を有効にできます。 BLOB に対する層を設定するには、`SetBlobTier` 操作を呼び出します。

## <a name="use-azure-powershell-to-set-the-blob-tier"></a>Azure PowerShell を使用して、BLOB 層を設定する

次の手順は、Azure PowerShell スクリプトを使用して、BLOB 層をアーカイブ (Archve) に設定する方法を示しています。

1. 管理者特権の Windows PowerShell セッションを開きます。 実行している PowerShell が 5.0 以降であることを確認します。 型:

   `$PSVersionTable.PSVersion`     

2. Azure PowerShell にサインインします。 

   `Login-AzAccount`  

3. ストレージ アカウント、アクセス キー、コンテナー、およびストレージ コンテキストの変数を定義します。

    ```powershell
    $StorageAccountName = "<enter account name>"
    $StorageAccountKey = "<enter account key>"
    $ContainerName = "<enter container name>"
    $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    ```

4. コンテナー内のすべての BLOB を取得します。

    `$blobs = Get-AzStorageBlob -Container "<enter container name>" -Context $ctx`
 
5. コンテナー内のすべての BLOB の層をアーカイブ (Archive) に設定します。

    ```powershell
    Foreach ($blob in $blobs) {
    $blob.ICloudBlob.SetStandardBlobTier("Archive")
    }
    ```

    サンプル出力を次に示します。

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    PS C:\WINDOWS\system32> $PSVersionTable.PSVersion

    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      17763  134
    PS C:\WINDOWS\system32> Login-AzAccount

    Account          : gus@contoso.com
    SubscriptionName : MySubscription
    SubscriptionId   : subscription-id
    TenantId         : tenant-id
    Environment      : AzureCloud

    PS C:\WINDOWS\system32> $StorageAccountName = "mygpv2storacct"
    PS C:\WINDOWS\system32> $StorageAccountKey = "mystorageacctkey"
    PS C:\WINDOWS\system32> $ContainerName = "test"
    PS C:\WINDOWS\system32> $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    PS C:\WINDOWS\system32> $blobs = Get-AzStorageBlob -Container "test" -Context $ctx
    PS C:\WINDOWS\system32> Foreach ($blob in $blobs) {
    >> $blob.ICloudBlob.SetStandardBlobTier("Archive")
    >> }
    PS C:\WINDOWS\system32>
    ```
   > [!TIP]
   > 取り込み時にデータをアーカイブする場合は、既定のアカウント層をホットに設定します。 既定の層がクールの場合、データが直ちにアーカイブに移動すると、30 日早期削除ペナルティが課されます。

## <a name="next-steps"></a>次のステップ

-  [ライフ サイクル ポリシー ルールを使用して一般的なデータ階層制御シナリオ](../storage/blobs/storage-lifecycle-management-concepts.md#examples)に対処する方法を学びます。