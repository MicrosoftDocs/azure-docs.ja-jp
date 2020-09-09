---
title: storage-files-create-storage-account-portal
description: Azure Files 用のストレージ アカウントを作成します。
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 03/28/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: a0db3ee9f32be35e62accc5df7b92697a883df20
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83778245"
---
ストレージ アカウントは、Azure ファイル共有またはその他のストレージ リソース (BLOB やキューなど) をデプロイできるストレージの共有プールです。 1 つのストレージ アカウントに格納できる共有の数に制限はありません。 1 つの共有に格納できるファイルの数に制限はなく、ストレージ アカウントの容量の上限までファイルを格納できます。

ストレージ アカウントを作成するには、以下の手順を実行します。

1. 左側のメニューで、 **[+]** を選択してリソースを作成します。
1. 検索ボックスに「**ストレージ アカウント**」と入力し、 **[ストレージ アカウント - Blob、File、Table、Queue]** を選択してから、 **[作成]** を選択します。
    ![ストレージ アカウントの文字がリソース検索ダイアログに入力されたところを示すスクリーンショット](../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png)

1. **[名前]** に「*mystorageacct*」と入力し、それに続けて、一意の名前であることを示す緑色のチェック マークが表示されるまでランダムな数字をいくつか入力します。 ストレージ アカウント名はすべて小文字で、グローバルに一意である必要があります。 ストレージ アカウント名をメモしておきます。 これは後で使用します。 
1. **[デプロイ モデル]** で、既定値の **[Resource Manager]** をそのまま選択します。 Azure Resource Manager とクラシック デプロイ モデルの違いの詳細については、[デプロイ モデルとリソースの状態](../articles/azure-resource-manager/management/deployment-models.md)に関するページを参照してください。
1. **[パフォーマンス]** では、既定値の **[Standard]** を選択したままにします。
    
    > [!NOTE]
    > このクイックスタートでは、Standard ファイル共有を作成しますが、Premium ファイル共有を使用する場合は、 **[Premium]** を選択します。

1. **[アカウントの種類]** で **[StorageV2]** を選択します。 ストレージ アカウントの種類の詳細については、[Azure ストレージ アカウント](../articles/storage/common/storage-account-options.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)に関するページを参照してください。

    > [!NOTE]
    > このクイックスタートでは、汎用 v2 アカウントを作成します。 Premium ファイル共有を使用する場合は、代わりに **[FileStorage]** を選択します。

1. **[レプリケーション]** で **[ローカル冗長ストレージ (LRS)]** を選択します。 
1. **[安全な転送が必須]** では常に **[有効]** を選択することをお勧めします。 このオプションの詳細については、[転送中の暗号化](../articles/storage/common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)に関するページを参照してください。
1. **[サブスクリプション]** で、ストレージ アカウントを作成するときに使用したサブスクリプションを選択します。 利用しているサブスクリプションが 1 つだけの場合は、それが既定値となります。
1. **[リソース グループ]** で **[新規作成]** を選択します。 名前として、「*myResourceGroup*」と入力します。
1. **[場所]** で **[米国東部]** を選択します。
1. **[仮想ネットワーク]** で既定のオプションである **[無効]** のままにします。 
1. ストレージ アカウントを見つけやすくするために、 **[ダッシュボードにピン留めする]** を選択します。
1. 終わったら、 **[作成]** を選択してデプロイを開始します。