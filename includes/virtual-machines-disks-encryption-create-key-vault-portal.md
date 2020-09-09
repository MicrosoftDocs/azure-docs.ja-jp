---
title: ファイルを含める
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/23/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a967777b65c06cf23239a47e8e691fb3a29231b4
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815472"
---
ディスクにカスタマー マネージド キーを設定する作業を初めて実行するときは、特定の順序でリソースを作成する必要があります。 最初に、Azure キー コンテナーの作成と設定を行う必要があります。

## <a name="set-up-your-azure-key-vault"></a>Azure Key Vault のセットアップ

1. [Azure Portal](https://aka.ms/diskencryptionupdates) にサインインします。
1. **[キー コンテナー]** を検索して選択します。

    [![検索ダイアログ ボックスが展開されている Azure portal のスクリーンショット。](./media/virtual-machines-disk-encryption-portal/server-side-encryption-key-vault-portal-search.png)](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > デプロイを成功させるには、Azure キー コンテナー、ディスク暗号化セット、VM、ディスク、スナップショットがすべて同じリージョンとサブスクリプションに存在している必要があります。

1. **[+ 追加]** を選択して、新しいキー コンテナーを作成します。
1. 新しいリソース グループを作成します。
1. キー コンテナー名を入力し、リージョンを選択して、価格レベルを選択します。

    > [!NOTE]
    > Key Vault インスタンスを作成する場合、論理的な削除と消去保護を有効にする必要があります。 論理的な削除では、Key Vault は削除されたキーを特定の保持期間 (既定では90日) にわたって保持します。 消去保護では、保持期間が経過するまで、削除されたキーを完全に削除できないようになります。 これらの設定は、誤って削除したためにデータが失われるのを防ぎます。 これらの設定は、Key Vault を使用してマネージド ディスクを暗号化する場合は必須です。

1. **[確認および作成]** を選択し、選択内容を確認してから、 **[作成]** を選択します。

    ![Azure キー コンテナーの作成エクスペリエンスのスクリーンショット。 作成する個々の値が表示された状態](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-vault.png)

1. キー コンテナーのデプロイが完了したら、それを選択します。
1. **[設定]** で **[キー]** を選択します。
1. **[Generate/Import]\(生成/インポート\)** を選択します。

    ![Key Vault のリソース設定ペインのスクリーンショット。 設定内で [生成/インポート] ボタンが表示されています。](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-generate-settings.png)

1. **[キーの種類]** は **[RSA]** 、 **[RSA キー サイズ]** は **[2048]** に設定されているので、どちらもそのままにしておきます。
1. 必要に応じて残りの選択項目を入力したら、 **[作成]** を選択します。

    ![[生成/インポート] ボタンを選択すると表示される [Create a key]\(キーを作成します\) ブレードのスクリーンショット](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-generate.png)

## <a name="set-up-your-disk-encryption-set"></a>ディスク暗号化セットを設定する

1. **ディスク暗号化セット**を検索して選択します。
1. **[ディスク暗号化セット]** ブレードで、 **[+ 追加]** を選択します。

    ![ディスク暗号化ポータルのメイン画面のスクリーンショット。 [追加] ボタンが強調表示されています](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-create-disk-encryption-set.png)

1. リソース グループを選択し、暗号化セットに名前を付け、キー コンテナーと同じリージョンを選択します。
1. **[暗号化の種類]** で、 **[カスタマー マネージド キーを使用した保存時の暗号化]** を選択します。

    > [!NOTE]
    > 特定の種類の暗号化を使用してディスク暗号化セットを作成すると、そのセットを変更することはできません。 別の種類の暗号化を使用したい場合は、新しいディスク暗号化セットを作成する必要があります。

1. **[クリックしてキーを選択します]** を選択します。
1. 以前に作成したキー コンテナーとキーのほか、バージョンを選択します。
1. **[選択]** を選択します。
1. **[確認および作成]** 、 **[作成]** の順に選択します。

    ![ディスク暗号化作成ブレードのスクリーンショット。 サブスクリプション、リソース グループ、ディスク暗号化セット名、リージョン、キー コンテナーとキーのセレクターが表示されています。](./media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-set-blade.png)

1. 作成が完了したら、ディスク暗号化セットを開き、ポップアップ表示されるアラートを選択します。

    ![アラート ポップアップ "このディスク暗号化セットにディスク、イメージ、またはスナップショットを関連付けるには、キー コンテナーにアクセス許可を付与する必要があります。" のスクリーンショット。 このアラートを選択して続行します](./media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-encryption-set-alert-fix.png)

    2 つの通知がポップアップ表示され、成功します。 これにより、キー コンテナーでディスク暗号化セットを使用できるようになります。

    ![キー コンテナーに対するアクセス許可の付与とロールの割り当てが成功したことを示すスクリーンショット。](./media/virtual-machines-disk-encryption-portal/disk-encryption-notification-success.png)