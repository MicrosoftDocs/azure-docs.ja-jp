---
title: Azure portal を使用して Windows VM を作成、暗号化する
description: このクイックスタートでは、Azure portal を使用して Windows 仮想マシンを作成および暗号化する方法について説明します
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: e29d1de976a027e32564a258fd5669b1e54c60f3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564443"
---
# <a name="quickstart-create-and-encrypt-a-windows-virtual-machine-with-the-azure-portal"></a>クイック スタート:Azure portal を使用した Windows 仮想マシンの作成と暗号化

Azure 仮想マシン (VM) は、Azure portal で作成できます。 Azure portal では、ブラウザー ベースのユーザー インターフェイスを使用して、VM とその関連リソースを作成できます。 このクイックスタートでは、Azure portal を使用して Windows 仮想マシンを展開し、暗号化キーを格納するキー コンテナーを作成し、VM を暗号化します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。


## <a name="create-a-virtual-machine"></a>仮想マシンの作成

1. Azure portal の左上隅にある **[リソースの作成]** を選択します。
1. [新規] ページの [人気順] で、 **[Windows Server 2016 Datacenter]** を選択します。
1. [基本] タブの [プロジェクトの詳細] で、正しいサブスクリプションが選択されていることを確認します。
1. "リソース グループ" には **[新規作成]** を選択します。 名前として「*myResourceGroup*」と入力し、 **[OK]** を選択します。
1. **[仮想マシン名]** に「*MyVM*」と入力します。
1. **[リージョン]** で *[(米国) 米国東部]* を選択します。
1. **[サイズ]** が *[Standard D2s v3]* になっていることを確認します。
1. **[管理者アカウント]** で **[パスワード]** を選択します。 ユーザー名とパスワードを入力します。

    :::image type="content" source="../media/disk-encryption/portal-quickstart-windows-vm-creation.png" alt-text="Windows VM の作成画面":::

    > [!WARNING]
    > [ディスク] タブには、 **[ディスクのオプション]** の下に [Encryption Type]\(暗号化の種類\) フィールドがあります。 このフィールドは、Azure Disk Encryption ではなく [Managed Disks](../managed-disks-overview.md) + CMK の暗号化オプションを指定するために使用されます。
    >
    > 混乱を避けるために、このチュートリアルを完了する間は、 *[ディスク]* タブを完全にスキップすることをお勧めします。

1. [管理] タブを選択し、[診断ストレージ アカウント] が存在することを確認します。 ストレージ アカウントがない場合は、[新規作成] を選択して新しいアカウントに名前を付け、[OK] を選択します

    :::image type="content" source="../media/disk-encryption/portal-quickstart-vm-creation-storage.png" alt-text="ResourceGroup の作成画面":::

1. [Review + Create]\(レビュー + 作成\) をクリックします。
1. **[仮想マシンの作成]** ページで、これから作成しようとしている VM の詳細を確認できます。 準備ができたら **[作成]** を選択します。

VM がデプロイされるまでに数分かかります。 デプロイが完了したら、次のセクションに移動してください。

## <a name="encrypt-the-virtual-machine"></a>仮想マシンを暗号化する

1. VM のデプロイが完了したら、 **[リソースに移動]** を選択します。
1. 左側のサイド バーで **[ディスク]** を選択します。
1. 上部のバーで、 **[追加設定]** を選択します。
1. **[暗号化の設定]**  >  **[Disks to encrypt]\(暗号化するディスク\)** で、 **[OS とデータ ディスク]** を選択します。

    :::image type="content" source="../media/disk-encryption/portal-quickstart-disks-to-encryption.png" alt-text="OS とデータ ディスクを示すスクリーンショット。":::

1. **[暗号化設定]** の **[暗号化用のキー コンテナーとキーを選択する]** を選択します。
1. **[Azure Key Vault からのキーの選択]** 画面で **[新規作成]** を選択します。

    :::image type="content" source="../media/disk-encryption/portal-qs-keyvault-create.png" alt-text="[新規作成] オプションを示すスクリーンショット。":::

1. **[キー コンテナーとキー]** の左側にある **[クリックしてキーを選択します]** を選択します。
1. **[Azure Key Vault からのキーの選択]** の **[キー コンテナー]** フィールドで、 **[新規作成]** を選択します。
1. **[キー コンテナーの作成]** 画面で、[リソース グループ] が *[myResourceGroup]* になっていることを確認し、キー コンテナーに名前を付けます。  すべてのキー コンテナーに Azure 全体で一意の名前を付ける必要があります。
1. **[アクセス ポリシー]** タブで、 **[Azure Disk Encryption (ボリューム暗号化用)]** チェック ボックスをオンにします。

    :::image type="content" source="../media/disk-encryption/portal-quickstart-keyvault-enable.png" alt-text="ディスクと暗号化の選択":::

1. **[Review + create]\(レビュー + 作成\)** を選択します。  
1. キー コンテナーが検証を通過した後、 **[作成]** を選択します。 **[Azure Key Vault からのキーの選択]** 画面が再度表示されます。
1. **[キー]** フィールドは空白のままにして、 **[選択]** を選択します。
1. 暗号化画面の上部の **[保存]** をクリックします。 VM が再起動されることを示す警告がポップアップ表示されます。 **[はい]** をクリックします。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、リソース グループ、仮想マシン、およびすべての関連リソースを削除できます。 これを行うには、仮想マシンのリソース グループを選択し、[削除] を選択した後、削除するリソース グループの名前を確認します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、暗号化キーに使用できるキー コンテナーを作成し、仮想マシンを作成して、その暗号化を有効にしました。  

> [!div class="nextstepaction"]
> [Azure Disk Encryption の概要](disk-encryption-overview.md)