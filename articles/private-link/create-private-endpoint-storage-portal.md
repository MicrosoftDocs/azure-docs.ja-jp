---
title: Azure プライベート エンドポイントを使用して非公開でストレージ アカウントに接続する
description: プライベート エンドポイントを使用して、Azure 内でストレージ アカウントに非公開で接続する方法について説明します。
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: bb0c6e9d20c12df3532a52df1fe4d9574344d4b3
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104728"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>Azure プライベート エンドポイントを使用して非公開でストレージ アカウントに接続する
Azure プライベート エンドポイントは、Azure におけるプライベート リンクの基本的な構成要素です。 仮想マシン (VM) などの Azure リソースとプライベート リンク リソースとの非公開での通信が可能になります。

このクイックスタートでは、Azure portal を使って、Azure 仮想ネットワーク上の VM、プライベート エンドポイントを備えたストレージ アカウントを作成する方法について説明します。 その後は、VM からそのストレージ アカウントへ安全にアクセスできます。


## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="create-a-vm"></a>VM の作成
このセクションでは、プライベート リンク リソース (この例では、ストレージ アカウント) へのアクセスに使用する VM をホストするために、仮想ネットワークとサブネットを作成します。

### <a name="create-the-virtual-network"></a>仮想ネットワークの作成

このセクションでは、プライベート リンク リソースへのアクセスに使用する VM をホストするために、仮想ネットワークとサブネットを作成します。

1. 画面の左上で、 **[リソースの作成]**  >  **[ネットワーキング]**  >  **[仮想ネットワーク]** の順に選択します。
1. **[仮想ネットワークの作成]** に次の情報を入力または選択します。

    | Setting | 値 |
    | ------- | ----- |
    | 名前 | 「*MyVirtualNetwork*」と入力します。 |
    | アドレス空間 | 「*10.1.0.0/16*」を入力します。 |
    | Subscription | サブスクリプションを選択します。|
    | Resource group | **[新規作成]** を選択し、「*myResourceGroup*」と入力して、 **[OK]** を選択します。 |
    | Location | **[WestCentralUS]** を選択します。|
    | サブネット - 名前 | 「*mySubnet*」と入力します。 |
    | サブネット アドレス範囲 | 「*10.1.0.0/24*」と入力します。 |
    |||
1. 残りは既定値のままにして、 **[作成]** を選択します。


### <a name="create-virtual-machine"></a>仮想マシンの作成

1. Azure portal の画面の左上で、 **[リソースの作成]**  >  **[Compute]**  >  **[仮想マシン]** を選択します。

1. **[仮想マシンの作成 - 基本]** に次の情報を入力または選択します。

    | Setting | 値 |
    | ------- | ----- |
    | **プロジェクトの詳細** | |
    | Subscription | サブスクリプションを選択します。 |
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

## <a name="create-your-private-endpoint"></a>プライベート エンドポイントを作成する
このセクションでは、対応するプライベート エンドポイントを使ってプライベート ストレージ アカウントを作成します。 

1. Azure portal の画面の左上で、 **[リソースの作成]**  >  **[ストレージ]**  >  **[ストレージ アカウント]** の順に選択します。

1. **[ストレージ アカウントの作成 - 基本]** で、次の情報を入力または選択します。

    | Setting | 値 |
    | ------- | ----- |
    | **プロジェクトの詳細** | |
    | Subscription | サブスクリプションを選択します。 |
    | Resource group | **[myResourceGroup]** を選択します。 これは前のセクションで作成しました。|
    | **インスタンスの詳細** |  |
    | ストレージ アカウント名  | 「*mystorageaccount*」と入力します。 この名前を取得する場合は、一意の名前を作成します。 |
    | リージョン | **[WestCentralUS]** を選択します。 |
    | パフォーマンス| 既定値 **[標準]** のままにします。 |
    | アカウントの種類 | 既定値 **[ストレージ (汎用 v2)]** のままにします。 |
    | レプリケーション | **[読み取りアクセス geo 冗長ストレージ (RA-GRS)]** を選択します。 |
    |||
  
3.  **[次へ: ネットワーク]** を選択します。
4.  **[Create a storage account - Networking]\(ストレージ アカウントの作成 - ネットワーク\)** で、接続方法に **[プライベート エンドポイント]** を選択します。
5.  **[Create a storage account - Networking]\(ストレージ アカウントの作成 - ネットワーク\)** で、 **[プライベート エンドポイントの追加]** を選択します。 
6.  **[プライベート エンドポイントを作成する]** で、次の情報を入力または選択します。

    | Setting | 値 |
    | ------- | ----- |
    | **プロジェクトの詳細** | |
    | Subscription | サブスクリプションを選択します。 |
    | Resource group | **[myResourceGroup]** を選択します。 これは前のセクションで作成しました。|
    |Location|**[WestCentralUS]** を選択します。|
    |名前|「 *myPrivateEndpoint*」と入力します。  |
    |ストレージ サブリソース|既定値  **[Blob]** のままにします。 |
    | **ネットワーク** |  |
    | 仮想ネットワーク  | リソース グループの *[myResourceGroup]* から、 *[MyVirtualNetwork]* を選択します。 |
    | Subnet |  *[mySubnet]* を選択します。 |
    | **プライベート DNS 統合**|  |
    | プライベート DNS ゾーンとの統合  | 既定値 **[はい]** のままにします。 |
    | プライベート DNS ゾーン  | 既定値 ** [(New) privatelink.blob.core.windows.net]** のままにします。 |
    |||
7.  **[OK]** を選択します。 
8.  **[確認および作成]** を選択します。  **[確認および作成]**   ページが表示され、Azure によって構成が検証されます。 
9. " **検証に成功しました**"  というメッセージが表示されたら、 **[作成]** を選択します。 
10. 先ほど作成したストレージ アカウント リソースに移動します。
11. 左側のコンテンツ メニューから、 **[アクセス キー]** を選択します。
12. key1 の接続文字列上で **[コピー]** を選択します。
 
## <a name="connect-to-a-vm-from-the-internet"></a>インターネットから VM に接続する

次のように、インターネットから VM *myVm* に接続します。

1. ポータルの検索バーに、「*myVm*」と入力します。

1. **[接続]** を選択します。 **[接続]** ボタンを選択すると、 **[Connect to virtual machine]\(仮想マシンに接続する\)** が開きます。

1. **[RDP ファイルのダウンロード]** を選択します。 リモート デスクトップ プロトコル ( *.rdp*) ファイルが作成され、お使いのコンピューターにダウンロードされます。

1. ダウンロードした .rdp* ファイルを開きます。

    1. メッセージが表示されたら、 **[Connect]** を選択します。

    1. VM の作成時に指定したユーザー名とパスワードを入力します。

        > [!NOTE]
        > 場合によっては、 **[その他]**  >  **[別のアカウントを使用する]** を選択して、VM の作成時に入力した資格情報を指定する必要があります。

1. **[OK]** を選択します。

1. サインイン処理中に証明書の警告が表示される場合があります。 証明書の警告を受信する場合は、 **[はい]** または **[続行]** を選択します。

1. VM デスクトップが表示されたら最小化してローカル デスクトップに戻ります。  

## <a name="access-storage-account-privately-from-the-vm"></a>VM から非公開でストレージ アカウントにアクセスする

このセクションでは、プライベート エンドポイントを使ったストレージ アカウントに非公開で接続します。

> [!IMPORTANT]
> ストレージ用の DNS 構成には、特定のアカウントの FQDN を含めるために、hosts ファイルに手動で変更を加える必要があります。管理者権限を使用して、Windows の c:\Windows\System32\Drivers\etc\hosts または Linux の /etc/hosts にあるファイルを変更してください。[プライベート IP アドレス] myaccount.blob.core.windows.net の形式で、前の手順のアカウントの DNS 情報を含めます。

1.  *myVM* のリモート デスクトップで、PowerShell を開きます。
2. 「 `nslookup mystorageaccount.blob.core.windows.net`」と入力します。次のようなメッセージが返されます。
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
3. Install [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Select **Storage accounts** with the right-click.
5. Select **Connect to an azure storage**.
6. Select **Use a connection string**.
7. Select **Next**.
8. Enter the connection string by pasting the information previously copied.
9. Select **Next**.
10. Select **Connect**.
11. Browse the Blob containers from mystorageaccount 
12. (Optionally) Create folders and/or upload files to *mystorageaccount*. 
13. Close the remote desktop connection to *myVM*. 

Additional options to access the storage account:
- Microsoft Azure Storage Explorer is a standalone free app from Microsoft that enables you to work visually with Azure storage data on Windows, macOS, and Linux. You can install the application to browse privately the storage account content. 
 
- The AzCopy utility is another option for high-performance scriptable data transfer for Azure storage. Use AzCopy to transfer data to and from Blob, File, and Table storage. 


## Clean up resources 
When you're done using the Private Endpoint, storage account and the VM, delete the resource group and all of the resources it contains: 
1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results. 
2. Select **Delete resource group**. 
3. Enter *myResourceGroup* for **TYPE THE RESOURCE GROUP NAME** and select **Delete**. 

## Next steps
In this Quickstart, you created a VM on a virtual network and storage account and a Private Endpoint. You connected to one VM from the internet and securely communicated to the storage account using Private Link. To learn more about Private Endpoint, see [What is Azure Private Endpoint?](private-endpoint-overview.md).
