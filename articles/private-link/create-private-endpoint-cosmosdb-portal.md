---
title: Azure Private Link を使用して Azure Cosmos アカウントに接続する
description: プライベート エンドポイントを作成して、VM から Azure Cosmos アカウントに安全にアクセスする方法について説明します。
author: asudbring
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: allensu
ms.openlocfilehash: e54aa00df9efa60cce0fd6fa1da32720f2947b12
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851198"
---
# <a name="connect-privately-to-an-azure-cosmos-account-using-azure-private-link"></a>Azure Private Link を使用して Azure Cosmos アカウントに非公開で接続する

Azure プライベート エンドポイントは、Azure におけるプライベート リンクの基本的な構成要素です。 仮想マシン (VM) などの Azure リソースとプライベート リンク リソースとの非公開での通信が可能になります。

この記事では、Azure portal を使って、Azure 仮想ネットワーク上に VM を作成する方法、およびプライベート エンドポイントを備えた Azure Cosmos アカウントを作成する方法について説明します。 その後は、VM から Azure Cosmos アカウントに安全にアクセスできます。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="create-a-vm"></a>VM の作成

### <a name="create-the-virtual-network"></a>仮想ネットワークの作成

このセクションでは、Private Link リソース (この例では、Azure Cosmos アカウント) へのアクセスに使用する VM をホストするために、仮想ネットワークとサブネットを作成します。

1. 画面の左上で、 **[リソースの作成]**  >  **[ネットワーキング]**  >  **[仮想ネットワーク]** の順に選択します。

1. **[仮想ネットワークの作成]** に次の情報を入力または選択します。

    | Setting | 値 |
    | ------- | ----- |
    | 名前 | 「*MyVirtualNetwork*」と入力します。 |
    | アドレス空間 | 「*10.1.0.0/16*」を入力します。 |
    | サブスクリプション | サブスクリプションを選択します。|
    | Resource group | **[新規作成]** を選択し、「*myResourceGroup*」と入力して、 **[OK]** を選択します。 |
    | Location | **[WestCentralUS]** を選択します。|
    | サブネット - 名前 | 「*mySubnet*」と入力します。 |
    | サブネット アドレス範囲 | 「*10.1.0.0/24*」と入力します。 |
    |||

1. 残りは既定値のままにして、 **[作成]** を選択します。

### <a name="create-the-virtual-machine"></a>仮想マシンの作成

1. Azure portal の画面の左上で、 **[リソースの作成]**  >  **[Compute]**  >  **[仮想マシン]** を選択します。

1. **[仮想マシンの作成 - 基本]** に次の情報を入力または選択します。

    | Setting | 値 |
    | ------- | ----- |
    | **プロジェクトの詳細** | |
    | サブスクリプション | サブスクリプションを選択します。 |
    | Resource group | **[myResourceGroup]** を選択します。 これは前のセクションで作成しました。  |
    | **インスタンスの詳細** |  |
    | 仮想マシン名 | 「*myVm*」と入力します。 |
    | リージョン | **[WestCentralUS]** を選択します。 |
    | 可用性のオプション | 既定値 **[インフラストラクチャ冗長は必要ありません]** をそのまま使用します。 |
    | Image | **[Windows Server 2019 Datacenter]** を選択します。 |
    | Size | 既定値 **[Standard DS1 v2]** をそのまま使用します。 |
    | **管理者アカウント** |  |
    | ユーザー名 | 任意のユーザー名を入力します。 |
    | パスワード | 任意のパスワードを入力します。 パスワードは 12 文字以上で、[定義された複雑さの要件](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)を満たす必要があります。|
    | パスワードの確認 | パスワードを再入力します。 |
    | **受信ポートの規則** |  |
    | パブリック受信ポート | 既定値 **[なし]** のままにします。 |
    | **コスト削減** |  |
    | Windows ライセンスを既にお持ちの場合 | 既定値 **[なし]** のままにします。 |
    |||

1. **[次へ:ディスク]** を選択します。

1. **[仮想マシンの作成 - Disk]** で、既定値のままにし、 **[Next: Networking]\(次へ : ネットワーク\)** を選択します。

1. **[Create a virtual machine - Networking]\(仮想マシンの作成 - ネットワーク\)** で次の情報を選択します。

    | Setting | 値 |
    | ------- | ----- |
    | 仮想ネットワーク | 既定値 **[MyVirtualNetwork]** のままにします。  |
    | アドレス空間 | 既定値 **[10.1.0.0/24]** のままにします。|
    | Subnet | 既定値 **[mySubnet (10.1.0.0/24)]** のままにします。|
    | パブリック IP | 既定値 **(new) myVm-ip\((新規) myVm-ip** のままにします。 |
    | パブリック受信ポート | **[選択したポートを許可する]** を選択します。 |
    | 受信ポートの選択 | **[HTTP]** と **[RDP]** を選択します。|
    ||

1. **[Review + create]\(レビュー + 作成\)** を選択します。 **[確認および作成]** ページが表示され、Azure によって構成が検証されます。

1. "**証に成功しました**" というメッセージが表示されたら、 **[作成]** を選択します。

## <a name="create-an-azure-cosmos-account"></a>Azure Cosmos アカウントを作成する

[Azure Cosmos SQL API アカウント](../cosmos-db/create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account)を作成します。 わかりやすくするために、Azure Cosmos アカウントを他のリソースと同じリージョン ("WestCentralUS") に作成することができます。

## <a name="create-a-private-endpoint-for-your-azure-cosmos-account"></a>Azure Cosmos アカウントのプライベート エンドポイントを作成する

リンク先の記事の「[Azure portal を使用して Private Link を作成する](../cosmos-db/how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-the-azure-portal)」セクションの説明に従って、Azure Cosmos アカウントの Private Link を作成します。

## <a name="connect-to-a-vm-from-the-internet"></a>インターネットから VM に接続する

次のように、インターネットから VM *myVm* に接続します。

1. ポータルの検索バーに、「*myVm*」と入力します。

1. **[接続]** を選択します。 **[接続]** ボタンを選択すると、 **[Connect to virtual machine]\(仮想マシンに接続する\)** が開きます。

1. **[RDP ファイルのダウンロード]** を選択します。 リモート デスクトップ プロトコル ( *.rdp*) ファイルが作成され、お使いのコンピューターにダウンロードされます。

1. ダウンロードされた *.rdp* ファイルを開きます。

    1. メッセージが表示されたら、 **[Connect]** を選択します。

    1. VM の作成時に指定したユーザー名とパスワードを入力します。

        > [!NOTE]
        > 場合によっては、 **[その他]**  >  **[別のアカウントを使用する]** を選択して、VM の作成時に入力した資格情報を指定する必要があります。

1. **[OK]** を選択します。

1. サインイン処理中に証明書の警告が表示される場合があります。 証明書の警告を受信する場合は、 **[はい]** または **[続行]** を選択します。

1. VM デスクトップが表示されたら最小化してローカル デスクトップに戻ります。  

## <a name="access-the-azure-cosmos-account-privately-from-the-vm"></a>VM から非公開で Azure Cosmos アカウントにアクセスする

このセクションでは、プライベート エンドポイントを使用して Azure Cosmos アカウントに非公開で接続します。 

1. IP アドレスと DNS のマッピングを含めるには、仮想マシン *myVM* にサインインし、`c:\Windows\System32\Drivers\etc\hosts` ファイルを開き、前の手順で取得した DNS 情報を次の形式で含めます。

   [プライベート IP アドレス] [アカウント エンドポイント].documents.azure.com

   **例:**

   10.1.255.13 mycosmosaccount.documents.azure.com

   10.1.255.14 mycosmosaccount-eastus.documents.azure.com


1.  *myVM* のリモートデスクトップで、[Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows) をインストールします。

1. 右クリックで **[Cosmos DB Accounts (Preview)]\(Cosmos DB アカウント (プレビュー)\)** を選択します。

1. **[Connect to Cosmos DB]\(Cosmos DB に接続\)** を選択します。

1. **[API]** を選択します。

1. 前にコピーした情報を貼り付けて、接続文字列を入力します。

1. **[次へ]** を選択します。

1. **[接続]** を選択します。

1. *mycosmosaccount* から Azure Cosmos データベースおよびコンテナーを参照します。

1. (省略可能必要に応じて) *mycosmosaccount* に新しい項目を追加します。

1.  *myVM* へのリモート デスクトップ接続を閉じます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

プライベート エンドポイント、Azure Cosmos アカウント、および VM の使用が完了したら、リソース グループとそこに含まれるすべてのリソースを削除します。 

1. ポータルの上部にある**検索**ボックスに「 *myResourceGroup*」と入力し、検索結果から  *myResourceGroup* を選択します。

1. **[リソース グループの削除]** を選択します。

1. **[リソース グループ名を入力してください]**  に「 *myResourceGroup*」と入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

この記事では、仮想ネットワーク上に VM を作成し、Azure Cosmos アカウントとプライベート エンドポイントを作成しました。 インターネットから VM に接続し、Private Link を使用して Azure Cosmos アカウントと安全に通信を行いました。

* プライベート エンドポイントの詳細については、「 [Azure プライベート エンドポイントとは](private-endpoint-overview.md)」を参照してください。

* Azure Cosmos DB で使用する際のプライベート エンドポイントの制限の詳細については、[Azure Cosmos DB での Azure Private Link](../cosmos-db/how-to-configure-private-endpoints.md) に関する記事をご覧ください。