---
title: Azure Storage アカウントの既定のアクセス層を管理する
description: GPv2 または Blob Storage アカウントの既定のアクセス層を変更する方法を説明します
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2021
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.reviewer: klaasl
ms.openlocfilehash: 637f748882b3ac84127c8b71761a06629e1e0957
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653834"
---
# <a name="manage-the-default-access-tier-of-an-azure-storage-account"></a>Azure Storage アカウントの既定のアクセス層を管理する

各 Azure Storage アカウントには、ホットまたはクールのいずれかの既定のアクセス層があります。 アクセス層は、ストレージ アカウントを作成するときに割り当てます。 既定のアクセス層はホットです。

既定のアカウント層を変更するには、ストレージ アカウントで **アクセス層** 属性を設定します。 アカウント層の変更は、アカウントに格納されている、層が明示的に設定されていないすべてのオブジェクトに適用されます。 アカウント層をホットからクールに切り替えた場合、GPv2 アカウントについてのみ、階層が設定されていないすべての BLOB に関して、書き込み操作 (10,000 件単位) の料金が発生します。クールからホットへの切り替えでは、Blob Storage および GPv2 アカウントのすべての BLOB に関して、読み取り操作 (10,000 件単位) とデータ取得 (GB 単位) の両方の料金が発生します。

オブジェクト レベルで層が設定されている BLOB については、アカウント層は適用されません。 アーカイブ層は、オブジェクト レベルでのみ適用することができます。 アクセス層はいつでも切り替えることができます。

ストレージ アカウントを作成した後で既定のアクセス層を変更するには、下の手順に従ってください。

## <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>GPv2 または Blob Storage アカウントの既定のアクセス層を変更する

次のシナリオでは、Azure portal または PowerShell を使用します。

# <a name="portal"></a>[ポータル](#tab/portal)

1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure portal で、 **[すべてのリソース]** を検索して選択します。

1. 使うストレージ アカウントを選びます。

1. **[設定]** で **[構成]** を選択し、アカウント構成を表示および変更します。

1. ニーズに応じた適切なアクセス層を選択します。 **[アクセス層]** を **[クール]** と **[ホット]** のいずれかに設定します。

1. 上部にある **[保存]** をクリックします。

![Azure portal で既定のアカウント層を変更する](media/manage-account-default-access-tier/account-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

次の PowerShell スクリプトを使用すると、アカウント層を変更できます。 `$rgName` 変数は、ご自身のリソース グループ名で初期化する必要があります。 `$accountName` 変数は、ご自身のストレージ アカウント名で初期化する必要があります。

```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```

---

## <a name="next-steps"></a>次の手順

- [Azure Storage アカウントの BLOB の層を管理する方法](../blobs/manage-access-tier.md)
- [Premium パフォーマンスがアプリにとってメリットがあるかどうかを判断する](../blobs/storage-blob-performance-tiers.md)
- [Azure Storage のメトリックを有効にして現在のストレージ アカウントの使用状況を評価する](../blobs/monitor-blob-storage.md)
