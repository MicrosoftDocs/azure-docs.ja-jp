---
title: BLOB の暗号化状態を確認する - Azure Storage
description: Azure portal、PowerShell、または Azure CLI を使用して特定の BLOB が暗号化されているかどうかを確認する方法について説明します。 BLOB が暗号化されていない場合、AzCopy を使用して、BLOB をダウンロードおよび再アップロードすることで暗号化を強制的に実行する方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: ee54357250e3f31ef9db633d933d897fff362f48
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98878563"
---
# <a name="check-the-encryption-status-of-a-blob"></a>BLOB の暗号化状態を確認する

2017 年 10 月 20 日以降に Azure Storage に書き込まれたすべてのブロック BLOB、追加 BLOB、またはページ BLOB は、Azure Storage 暗号化を使用して暗号化されます。 この日付より前に作成された BLOB は、引き続きバックグラウンド プロセスによって暗号化されます。

この記事では、特定の BLOB が暗号化されているかどうかを確認する方法について説明します。

## <a name="check-a-blobs-encryption-status"></a>BLOB の暗号化状態を確認する

Azure portal、PowerShell、または Azure CLI を使用して、コードを使用せずに BLOB が暗号化されているかどうかを確認します。

### <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure portal を使用して BLOB が暗号化されているかどうかを確認するには、次の手順を実行します。

1. Azure Portal のストレージ アカウントに移動します。
1. **[コンテナー]** を選択して、アカウント内のコンテナーの一覧に移動します。
1. BLOB を探して、その **[概要]** タブを表示します。
1. **[暗号化されたサーバー]** プロパティを表示します。 次の図に示すように **[TRUE]** の場合、BLOB は暗号化されています。 BLOB のプロパティには、BLOB が作成された日時も含まれていることに注意してください。

    ![Azure portal で [暗号化されたサーバー] プロパティを確認する方法を示すスクリーンショット](media/storage-blob-encryption-status/blob-encryption-property-portal.png)

### <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用して BLOB が暗号化されているかどうかを確認するには、BLOB の **IsServerEncrypted** プロパティを確認します。 山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$blob = Get-AzStorageBlob -Context $account.Context `
    -Container <container> `
    -Blob <blob>
$blob.ICloudBlob.Properties.IsServerEncrypted
```

BLOB が作成された日時を確認するには、**Created** プロパティの値を確認します。

```powershell
$blob.ICloudBlob.Properties.IsServerEncrypted
```

### <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Azure CLI を使用して BLOB が暗号化されているかどうかを確認するには、BLOB の **IsServerEncrypted** プロパティを確認します。 山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```azurecli-interactive
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query "properties.serverEncrypted"
```

BLOB が作成された日時を確認するには、**created** プロパティの値を確認します。

---

## <a name="force-encryption-of-a-blob"></a>BLOB の暗号化を強制的に実行する

2017 年 10 月 20 日より前に作成された BLOB がまだバックグラウンド プロセスによって暗号化されていない場合は、BLOB をダウンロードして再アップロードすることで、直ちに暗号化を強制実行できます。 これを簡単に行う方法は、AzCopy を使用することです。

AzCopy を使用してローカル ファイル システムに BLOB をダウンロードするには、次の構文を使用します。

```
azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'

Example:
azcopy copy 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt' 'C:\temp\blob1.txt'
```

AzCopy を使用して Azure Storage に BLOB を再アップロードするには、次の構文を使用します。

```
azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'

Example:
azcopy copy 'C:\temp\blob1.txt' 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt'
```

AzCopy を使用した BLOB データのコピーの詳細については、「[AzCopy と Blob Storage でデータを転送する](../common/storage-use-azcopy-v10.md#transfer-data)」を参照してください。

## <a name="next-steps"></a>次のステップ

[保存データに対する Azure Storage 暗号化](../common/storage-service-encryption.md)
