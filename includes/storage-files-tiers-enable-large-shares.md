---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 02/03/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: a086aae35c9a800c6a4cfc3e872a34438bc84095
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99569532"
---
既定では、Standard ファイル共有に使用できるのは最大 5 TiB だけですが、共有の制限は 100 TiB に増やすことができます。 共有の制限を増やすには、ご使用のストレージ アカウントで **大きなファイル共有** を有効にします。 Premium ストレージ アカウント (*FileStorage* ストレージ アカウント) の場合は、すべての Premium ファイル共有が既に 100 TiB の最大容量までプロビジョニングできるようになっているため、大きなファイル共有機能のフラグはありません。

大きなファイル共有を有効にできるのは、ローカル冗長またはゾーン冗長の Standard ストレージ アカウントだけです。 大きなファイル共有機能フラグを有効にした後では、冗長性レベルを geo 冗長ストレージまたは geo ゾーン冗長ストレージに変更することはできません。

既存のストレージ アカウントで大きなファイル共有を有効にするには、ストレージ アカウントの目次で **[ファイル共有]** に移動します。
このブレードで、 **[容量を共有]** を選択し、共有容量を **[100 TiB]** に変更してから **[保存]** を選択します。

:::image type="content" source="media/storage-files-tiers-enable-large-shares/enable-lfs.png" alt-text="Azure portal での大きなファイル共有の有効化オプトイン設定のスクリーンショット。" lightbox="media/storage-files-tiers-enable-large-shares/increase-share-capacity.png":::

[`Set-AzStorageAccount`](/powershell/module/az.storage/set-azstorageaccount) PowerShell コマンドレットおよび [`az storage account update`](/cli/azure/storage/account#az-storage-account-update) Azure CLI コマンドを使用して、100 TiB のファイル共有を有効にすることもできます。 大きなファイル共有を有効にする詳しい手順については、「[大きなファイル共有の有効化と作成](../articles/storage/files/storage-files-how-to-create-large-file-share.md)」を参照してください。

新しいストレージ アカウントにファイル共有を作成にする方法の詳細については、[Azure ファイル共有の作成](../articles/storage/files/storage-how-to-create-file-share.md)に関する記事を参照してください。