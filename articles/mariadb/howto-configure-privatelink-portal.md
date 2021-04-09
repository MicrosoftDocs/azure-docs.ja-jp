---
title: Private Link - Azure portal - Azure Database for MariaDB
description: Azure portal から Azure Database for MariaDB 用のプライベート リンクを構成する方法について説明します
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: how-to
ms.date: 01/09/2020
ms.openlocfilehash: 79b3c3f8eca2fa4442a7845ca4aa3921d0302453
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98659626"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mariadb-using-portal"></a>ポータルを使用して Azure Database for MariaDB 用の Private Link を作成および管理する

プライベート エンドポイントは、Azure におけるプライベート リンクの基本的な構成要素です。 これによって、仮想マシン (VM) などの Azure リソースが Private Link リソースと非公開で通信できるようになります。  この記事では、Azure portal を使用して Azure 仮想ネットワーク内に VM を作成し、Azure プライベート エンドポイントを含む Azure Database for MariaDB サーバーを作成する方法について説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

> [!NOTE]
> プライベート リンク機能は、General Purpose または Memory Optimized の価格レベルの Azure Database for MariaDB サーバーでのみ使用可能です。 データベース サーバーがこれらの価格レベルのいずれであることを確実にします。

## <a name="sign-in-to-azure"></a>Azure へのサインイン
[Azure portal](https://portal.azure.com) にサインインします。

## <a name="create-an-azure-vm"></a>Azure VM の作成

このセクションでは、Private Link リソース (Azure 内の MariaDB サーバー) へのアクセスに使用する VM をホストするために、仮想ネットワークとサブネットを作成します。

### <a name="create-the-virtual-network"></a>仮想ネットワークの作成
このセクションでは、Private Link リソースへのアクセスに使用する VM をホストするために、仮想ネットワークとサブネットを作成します。

1. 画面の左上で、 **[リソースの作成]**  >  **[ネットワーキング]**  >  **[仮想ネットワーク]** の順に選択します。
2. **[仮想ネットワークの作成]** に次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | 名前 | 「*MyVirtualNetwork*」と入力します。 |
    | アドレス空間 | 「*10.1.0.0/16*」を入力します。 |
    | サブスクリプション | サブスクリプションを選択します。|
    | Resource group | **[新規作成]** を選択し、「*myResourceGroup*」と入力して、 **[OK]** を選択します。 |
    | 場所 | **[西ヨーロッパ]** を選択します。|
    | サブネット - 名前 | 「*mySubnet*」と入力します。 |
    | サブネット アドレス範囲 | 「*10.1.0.0/24*」と入力します。 |
    |||
3. 残りは既定値のままにして、 **[作成]** を選択します。

### <a name="create-virtual-machine"></a>仮想マシンの作成

1. Azure portal の画面の左上で、 **[リソースの作成]**  >  **[Compute]**  >  **[仮想マシン]** を選択します。

2. **[仮想マシンの作成 - 基本]** に次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | **プロジェクトの詳細** | |
    | サブスクリプション | サブスクリプションを選択します。 |
    | Resource group | **[myResourceGroup]** を選択します。 これは前のセクションで作成しました。  |
    | **インスタンスの詳細** |  |
    | 仮想マシン名 | 「*myVm*」と入力します。 |
    | リージョン | **[西ヨーロッパ]** を選択します。 |
    | 可用性のオプション | 既定値 **[インフラストラクチャ冗長は必要ありません]** をそのまま使用します。 |
    | Image | **[Windows Server 2019 Datacenter]** を選択します。 |
    | サイズ | 既定値 **[Standard DS1 v2]** をそのまま使用します。 |
    | **管理者アカウント** |  |
    | ユーザー名 | 任意のユーザー名を入力します。 |
    | Password | 任意のパスワードを入力します。 パスワードは 12 文字以上で、[定義された複雑さの要件](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)を満たす必要があります。|
    | パスワードの確認 | パスワードを再入力します。 |
    | **受信ポートの規則** |  |
    | パブリック受信ポート | 既定値 **[なし]** のままにします。 |
    | **コスト削減** |  |
    | Windows ライセンスを既にお持ちの場合 | 既定値 **[なし]** のままにします。 |
    |||

1. **ディスク** を選択します。

1. **[仮想マシンの作成 - Disk]** で、既定値のままにし、 **[Next: Networking]\(次へ : ネットワーク\)** を選択します。

1. **[仮想マシンの作成 - ネットワーク]** で次の情報を選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | 仮想ネットワーク | 既定値 **[MyVirtualNetwork]** のままにします。  |
    | アドレス空間 | 既定値 **[10.1.0.0/24]** のままにします。|
    | Subnet | 既定値 **[mySubnet (10.1.0.0/24)]** のままにします。|
    | パブリック IP | 既定値 **(new) myVm-ip\((新規) myVm-ip** のままにします。 |
    | パブリック受信ポート | **[選択したポートを許可する]** を選択します。 |
    | 受信ポートの選択 | **[HTTP]** と **[RDP]** を選択します。|
    |||


1. **[Review + create]\(レビュー + 作成\)** を選択します。 **[確認および作成]** ページが表示され、Azure によって構成が検証されます。

1. "**証に成功しました**" というメッセージが表示されたら、 **[作成]** を選択します。

## <a name="create-an-azure-database-for-mariadb"></a>Azure Database for MariaDB を作成する

このセクションでは、Azure に Azure Database for MariaDB サーバーを作成します。 

1. Azure portal の画面の左上で、 **[リソースの作成]**  >  **[データベース]**  >  **[Azure Database for MariaDB]** を選択します。

1. **[Azure Database for MariaDB]** で、次の情報を入力します。

    | 設定 | 値 |
    | ------- | ----- |
    | **プロジェクトの詳細** | |
    | サブスクリプション | サブスクリプションを選択します。 |
    | Resource group | **[myResourceGroup]** を選択します。 これは前のセクションで作成しました。|
    | **サーバーの詳細** |  |
    |サーバー名  | 「*myserver*」と入力します。 この名前を取得する場合は、一意の名前を作成します。|
    | 管理者ユーザー名| 任意の管理者名を入力します。 |
    | Password | 任意のパスワードを入力します。 パスワードは 8 文字以上で、定義された要件を満たす必要があります。 |
    | 場所 | MariaDB サーバーを配置する Azure リージョンを選択します。 |
    |Version  | 必要な MariaDB サーバーのデータベース バージョンを選択します。|
    | コンピューティングとストレージ| ワークロードに基づいて、サーバーに必要な価格レベルを選択します。 |
    |||

7. **[OK]** を選択します。 
8. **[Review + create]\(レビュー + 作成\)** を選択します。 **[確認および作成]** ページが表示され、Azure によって構成が検証されます。 
9. "検証に成功しました" というメッセージが表示されたら、 **[作成]** を選択します。 
10. "検証に成功しました" というメッセージが表示されたら、[作成] を選択します。 

> [!NOTE]
> Azure Database for MariaDB と VNet サブネットが異なるサブスクリプションに存在する場合があります。 このような場合は、次の構成を確認する必要があります。
> - 両方のサブスクリプションに **Microsoft.DBforMariaDB** リソース プロバイダーが登録されていることを確認してください。 詳細については、[resource-manager-registration][resource-manager-portal] に関するページをご覧ください

## <a name="create-a-private-endpoint"></a>プライベート エンドポイントの作成

このセクションでは、MariaDB サーバーへのプライベート エンドポイントを作成します。 

1. Azure portal の画面の左上で、 **[リソースの作成]**  >  **[ネットワーキング]**  >  **[プライベート リンク]** を選択します。
2. **[プライベート リンク センター - 概要]** の **[サービスへのプライベート接続を構築する]** オプションで、 **[開始]** を選択します。

    ![Private Link の概要](media/concepts-data-access-and-security-private-link/privatelink-overview.png)

1. **[Create a private endpoint - Basics]\(プライベート エンドポイントの作成 - 基本\)** で次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | **プロジェクトの詳細** | |
    | サブスクリプション | サブスクリプションを選択します。 |
    | Resource group | **[myResourceGroup]** を選択します。 これは前のセクションで作成しました。|
    | **インスタンスの詳細** |  |
    | 名前 | 「*myPrivateEndpoint*」と入力します。 この名前を取得する場合は、一意の名前を作成します。 |
    |リージョン|**[西ヨーロッパ]** を選択します。|
    |||
5. **[Next:リソース]** を選択します。
6. **[プライベート エンドポイントの作成 - リソース]** で、次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    |接続方法  | 自分のディレクトリ内の Azure リソースに接続するように選択します。|
    | サブスクリプション| サブスクリプションを選択します。 |
    | リソースの種類 | **Microsoft.DBforMariaDB/servers** を選択します。 |
    | リソース |*[myServer]* を選択します。|
    |ターゲット サブリソース |*mariadbServer* を選択します|
    |||
7. **[Next:構成]** を選択します。
8. **[Create a private endpoint - Configuration]/(プライベート エンドポイントの作成 - 構成/)** で次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    |**ネットワーク**| |
    | 仮想ネットワーク| *[MyVirtualNetwork]* を選択します。 |
    | Subnet |  *[mySubnet]* を選択します。 |
    |**プライベート DNS 統合**||
    |プライベート DNS ゾーンとの統合 |**[はい]** を選択します。 |
    |プライベート DNS ゾーン |*[(新規)privatelink.mariadb.database.azure.com]* を選択します。 |
    |||

    > [!Note] 
    > サービスに事前に定義されているプライベート DNS ゾーンを使用するか、優先する DNS ゾーン名を指定します。 詳細については、「[Azure サービス DNS ゾーンの構成](../private-link/private-endpoint-dns.md)」を参照してください。

1. **[Review + create]\(レビュー + 作成\)** を選択します。 **[確認および作成]** ページが表示され、Azure によって構成が検証されます。 
2. "**証に成功しました**" というメッセージが表示されたら、 **[作成]** を選択します。 

    ![作成された Private Link](media/concepts-data-access-and-security-private-link/show-mariadb-private-link.png)

    > [!NOTE] 
    > お客様の DNS 設定の FQDN は、構成されている非公開 IP では解決されません。 [こちら](../dns/dns-operations-recordsets-portal.md)で示すように、構成された FQDN の DNS ゾーンを設定する必要があります。

## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>リモート デスクトップ (RDP) を使用して VM に接続


**myVm** を作成した後、次のようにインターネットからそれに接続します。 

1. ポータルの検索バーに、「*myVm*」と入力します。

1. **[接続]** を選択します。 **[接続]** ボタンを選択すると、 **[Connect to virtual machine]\(仮想マシンに接続する\)** が開きます。

1. **[RDP ファイルのダウンロード]** を選択します。 リモート デスクトップ プロトコル ( *.rdp*) ファイルが作成され、お使いのコンピューターにダウンロードされます。

1. *downloaded.rdp* ファイルを開きます。

    1. メッセージが表示されたら、 **[Connect]** を選択します。

    1. VM の作成時に指定したユーザー名とパスワードを入力します。

        > [!NOTE]
        > 場合によっては、 **[その他]**  >  **[別のアカウントを使用する]** を選択して、VM の作成時に入力した資格情報を指定する必要があります。

1. **[OK]** を選択します。

1. サインイン処理中に証明書の警告が表示される場合があります。 証明書の警告を受信する場合は、 **[はい]** または **[続行]** を選択します。

1. VM デスクトップが表示されたら最小化してローカル デスクトップに戻ります。

## <a name="access-the-mariadb-server-privately-from-the-vm"></a>VM から MariaDB サーバーにプライベートにアクセスする

1. *myVM* のリモート デスクトップで、PowerShell を開きます。

2. 「 `nslookup mydemomserver.privatelink.mariadb.database.azure.com`」と入力します。 

    次のようなメッセージが返されます。
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemoMariaDBserver.privatelink.mariadb.database.azure.com
    Address:  10.1.3.4
    ```

3. 利用可能な任意のクライアントを使用して、MariaDB サーバーのプライベート リンク接続をテストします。 次の例では、[MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) を使用して操作を行いました。


4. **[新しい接続]** で、この情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | サーバーの種類| **[MariaDB]** を選択します。|
    | サーバー名| *mydemoserver.privatelink.mariadb.database.azure.com* を選択します。 |
    | ユーザー名 | MariaDB サーバーの作成時に指定したユーザー名を username@servername 形式で入力します。 |
    |Password |MariaDB サーバーの作成時に指定したパスワードを入力します。 |
    |SSL|**[必須]** を選択します。|
    ||

5. **[テスト接続]** または **[OK]** を選択します。

6. (省略可能) 左側のメニューからデータベースを参照して、MariaDB データベースからの情報を作成または照会します

7. myVm へのリモート デスクトップ接続を閉じます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする
プライベート エンドポイント、MariaDB サーバー、VM を使い終えたら、リソース グループとそこに含まれるすべてのリソースを削除します。

1. ポータルの上部にある **検索** ボックスに「 *myResourceGroup*」と入力し、検索結果から  *myResourceGroup* を選択します。
2. **[リソース グループの削除]** を選択します。
3. **[TYPE THE RESOURCE GROUP NAME]\(リソース グループ名を入力してください\)** に「myResourceGroup」と入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

この記事では、仮想ネットワーク上の VM、Azure Database for MariaDB、およびプライベート アクセス用のプライベート エンドポイントを作成しました。 インターネットから 1 台の VM に接続し、Private Link を使用して MariaDB サーバーと安全に通信を行いました。 プライベート エンドポイントの詳細については、「[Azure プライベート エンドポイントとは](../private-link/private-endpoint-overview.md)」を参照してください。

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md