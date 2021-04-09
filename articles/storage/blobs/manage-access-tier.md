---
title: Azure Storage アカウントの BLOB のアクセス層を管理する
description: GPv2 または Blob Storage アカウントの BLOB の層を変更する方法について説明します
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: klaasl
ms.openlocfilehash: 183593022c934eaf52ffe18649c23e8deced34d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98166457"
---
# <a name="manage-the-access-tier-of-a-blob-in-an-azure-storage-account"></a>Azure Storage アカウントの BLOB のアクセス層を管理する

各 BLOB には、ホット、クール、アーカイブのいずれかの既定のアクセス層があります。 BLOB は、それが作成された Azure Storage アカウントの既定のアクセス層を使用します。 Blob Storage および GPv2 アカウントでは、アカウント レベルで **アクセス層** 属性が公開されます。 この属性は、オブジェクト レベルでアクセス層が明示的に設定されていない BLOB の既定のアクセス層を指定します。 オブジェクト レベルで階層が設定されているオブジェクトについては、アカウントの階層は適用されません。 アーカイブ層は、オブジェクト レベルでのみ適用することができます。 アクセス層は、下の手順に従っていつでも切り替えることができます。

## <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>GPv2 または Blob Storage アカウントの BLOB の層を変更する

次のシナリオでは、Azure portal または PowerShell を使用します。

# <a name="portal"></a>[ポータル](#tab/portal)

1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure portal で、 **[すべてのリソース]** を検索して選択します。

1. 使うストレージ アカウントを選びます。

1. コンテナーを選択し、お使いのBLOB を選択します。

1. **[BLOB のプロパティ]** で、 **[層の変更]** を選択します。

1. **[ホット]** 、 **[クール]** 、または **[アーカイブ]** アクセス層を選択します。 BLOB が現在アーカイブ内にあるときに、オンライン層にリハイドレートする場合は、リハイドレート優先度として **[標準]** または **[高]** を選択することもできます。

1. 下部にある **[保存]** を選択します。

![Azure portal で BLOB 層を変更する](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

次の PowerShell スクリプトを使用すると、BLOB 層を変更できます。 `$rgName` 変数は、ご自身のリソース グループ名で初期化する必要があります。 `$accountName` 変数は、ご自身のストレージ アカウント名で初期化する必要があります。 `$containerName` 変数は、ご自身のコンテナー名で初期化する必要があります。 `$blobName` 変数は、ご自身の BLOB 名で初期化する必要があります。

```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName == ""

#Select the storage account and get the context
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to archive
$blob.ICloudBlob.SetStandardBlobTier("Archive")
```

---

## <a name="next-steps"></a>次の手順

- [Azure Storage アカウントの既定のアカウント アクセス層を変更する方法](../common/manage-account-default-access-tier.md)
- [アーカイブ層から BLOB データをリハイドレートする方法を確認する](storage-blob-rehydration.md)
- [Blob Storage アカウントと GPv2 アカウントのホット、クール、アーカイブのリージョンごとの料金を確認する](https://azure.microsoft.com/pricing/details/storage/)
