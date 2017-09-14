---
title: "MySQL を実行するクラシック Azure VM を作成する | Microsoft Docs"
description: "クラシック デプロイ モデルを使用して、Windows Server 2012 R2 および MySQL データベースを実行する Azure 仮想マシンを作成します。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 98fa06d2-9b92-4d05-ac16-3f8e9fd4feaa
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: cynthn
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 11850e5ce20efae88a7af9c1d2e4761ed2b70cd7
ms.contentlocale: ja-jp
ms.lasthandoff: 08/11/2017

---
# <a name="install-mysql-on-a-virtual-machine-created-with-the-classic-deployment-model-running-windows-server-2016"></a>Windows Server 2016 を実行するクラシック デプロイ モデルで作成された仮想マシンに MySQL をインストールする
[MySQL](https://www.mysql.com) は広く普及しているオープン ソースの SQL データベースです。 このチュートリアルでは、**Windows Server 2016** を実行している仮想マシン上の MySQL Server として **MySQL 5.7.18 の Community バージョン**をインストールして実行する方法について説明します。 他のバージョンの MySQL または Windows Server を使用しているユーザーは、同一の結果にならない場合があります。

Linux 上で MySQL をインストールする方法について詳しくは、「[MySQL を Azure でインストールする方法](../../linux/mysql-install.md)」をご覧ください。

> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。

## <a name="create-a-virtual-machine-running-windows-server-2016"></a>Windows Server 2016 を実行する仮想マシンの作成
Windows Server 2016 を実行している VM がまだない場合は、この[チュートリアル](./tutorial.md)を使用して、仮想マシンを作成できます。

## <a name="attach-a-data-disk"></a>データ ディスクの接続
仮想マシンを作成したら、データ ディスクを必要に応じて接続できます。 OS ドライブ (C:) にはオペレーティング システムが含まれており、ここでデータ ディスクを追加するとドライブ容量の不足を回避できるため、運用環境のワークロードで推奨されています。

[データ ディスクを Windows 仮想マシンに接続する方法](../attach-managed-disk-portal.md)に関する記事を参照して、空のディスクを接続する手順に従ってください。 ホストのキャッシュ設定を **[なし]** または **[読み取り専用]** に設定します。

## <a name="log-on-to-the-virtual-machine"></a>仮想マシンへのログオン
次に、[仮想マシンにログオン](./connect-logon.md)して MySQL をインストールします。

## <a name="install-and-run-mysql-community-server-on-the-virtual-machine"></a>仮想マシンに MySQL Community Server をインストールして実行する
MySQL Server の Community バージョンをインストール、構成、と実行するには、次の手順を実行します。

> [!NOTE]
> Internet Explorer を使用して項目をダウンロードする場合は、[**IE セキュリティ強化の構成**] を [オフ] に設定し、ダウンロード プロセスを簡略化できます。 [スタート] メニューから、[管理ツール]/[サーバー マネージャー]/[ローカル サーバー] をクリックし、[**IE セキュリティ強化の構成**]をクリックして、構成を [オフ] に設定します。
>
>

1. リモート デスクトップを使用して仮想マシンに接続したら、スタート画面で **[Internet Explorer]** をクリックします。
2. 右上隅の **[ツール]** (歯車のアイコン) を選択して、**[インターネット オプション]** をクリックします。 **[セキュリティ]** タブ、**[信頼済みサイト]** アイコン、**[サイト]** の順にクリックします。 信頼済みサイトの一覧に http://*.mysql.com を追加します。 [**閉じる**] をクリックし、[**OK**] をクリックします。
3. Internet Explorer のアドレス バーに「https://dev.mysql.com/downloads/mysql/」と入力します。
4. MySQL サイトで MySQL Installer for Windows の最新バージョンを見つけ、インストールします。 MySQL Installer を選ぶ際、すべてのファイル セットが揃っているバージョンをダウンロードし (ファイル サイズが 352.8 MB の mysql-installer-community-5.7.18.0.msi など)、このインストーラーを保存します。
5. インストーラーでダウンロードが完了したら、[ **実行** ]をクリックしてセットアップを起動します。
6. **[License Agreement]** ページで、ライセンス契約の内容に同意し、**[Next]** をクリックします。
7. **[Choosing a Setup Type]** ページで目的のセットアップの種類をクリックしてから、**[Next]** をクリックします。 次の手順は、セットアップの種類として **[Server only]** を選択したことを前提にしています。
8. [**要件の確認**] ページが表示されたら、[**実行**] をクリックして、インストーラーが不足コンポーネントをインストールできるようにします。 C++ 再頒布可能パッケージ ランタイムなど、表示される指示に従います。
9. **[Installation]** ページで、**[Execute]** をクリックします。 インストールが完了したら、 **[Next]**をクリックします。

10. **[Product Configuration]** ページで、**[Next]** をクリックします。

11. **[Type and Networking]** ページで、必要な構成の種類を指定します。さらに、TCP ポートなど接続性のオプションについても必要に応じて指定します。 **[Show Advanced Options]**、**[Next]** の順にクリックします。
    ![](./media/mysql-2008r2/MySQL_TypeNetworking.png)

12. **[Accounts and Roles]** ページで強力な MySQL のルート パスワードを指定します。 必要に応じて別の MySQL ユーザー アカウントを追加し、 **[Next]**をクリックします。

    ![](./media/mysql-2008r2/MySQL_AccountsRoles_Filled.png)
13. **[Windows Service]** ページで、Windows サービスとして MySQL Server を実行するための、既定の設定に対する変更を必要に応じて指定し、**[Next]** をクリックします。

    ![](./media/mysql-2008r2/MySQL_WindowsService.png)
14. [**Plugins and Extensions**] \(プラグインと拡張機能) ページでの選択は省略可能です。 [**次へ**] をクリックして続行します。
15. **[Advanced Options]** ページで、必要に応じてログ オプションに対する変更を指定し、**[Next]** をクリックします。

    ![](./media/mysql-2008r2/MySQL_AdvOptions.png)
16. **[Apply Server Configuration]** ページで、**[Execute]** をクリックします。 構成手順の終了後、 **[Finish]**をクリックします。
17. **[Product Configuration]** ページで、**[Next]** をクリックします。
18. **[Installation Complete]** ページで、後で内容を検討する場合は **[Copy Log to Clipboard]** をクリックしてから **[Finish]** をクリックします。
19. スタート画面で「**mysql**」と入力してから、[**MySQL 5.7 Command Line Client**] をクリックします。
20. 手順 12 で指定したルート パスワードを入力すると、MySQL を構成するためのコマンドを実行できるプロンプトが表示されます。 コマンドと構文について詳しくは、[MySQL のリファレンス マニュアル](https://dev.mysql.com/doc/refman/5.7/en/server-configuration.html)をご覧ください。

    ![](./media/mysql-2008r2/MySQL_CommandPrompt.png)
21. また、ベース ディレクトリやデータ ディレクトリ、ドライブなど、サーバー構成の既定設定を構成することもできます。 詳細については、「[6.1.2 サーバー構成の既定値](https://dev.mysql.com/doc/refman/5.7/en/server-configuration-defaults.html)」を参照してください。

## <a name="configure-endpoints"></a>エンドポイントを構成する

インターネット上のクライアント コンピューターで MySQL サービスを利用できるようにするには、TCP ポートのエンドポイントを構成して、Windows ファイアウォール ルールを作成する必要があります。 MySQL Server サービスが MySQL クライアントをリッスンする既定のポート値は 3306 です。 [**Type and Networking**] ページで指定した値とポートが一致していれば (前の手順の手順 11)、別のポートを指定できます。

> [!NOTE]
> 運用環境で使用する場合は、インターネット上のすべてのコンピューターで MySQL Server サービスが利用できるようになるため、セキュリティへの影響を検討します。 Access Control リスト (ACL) でエンドポイントを使用することができる発信元 IP アドレスのセットを定義できます。 詳細については、[仮想マシンに対してエンドポイントを設定する方法](setup-endpoints.md)に関するページを参照してください。
>
>

MySQL Server サービスのエンドポイントを構成するには、次のようにします。

1. Azure Portal で、[**仮想マシン (クラシック)**] をクリックし、MySQL 仮想マシンの名前をクリックしてから、[**エンドポイント**] をクリックします。
2. コマンド バーで、 **[追加]**をクリックします。
3. [**エンドポイントの追加**] ページで、一意の名前を [**名前**] に入力します。
4. プロトコルとして [**TCP**] を選択します。
5. [**パブリック ポート**] と [**プライベート ポート**] の両方に、**3306** などのポート番号を入力し、[**OK**] をクリックします。

## <a name="add-a-windows-firewall-rule-to-allow-mysql-traffic"></a>Windows Firewall ルールを追加して MySQL トラフィックを許可する
インターネットからの MySQL トラフィックを許可する Windows ファイアウォール ルールを追加するには、MySQL サーバー仮想マシンで、_管理者特権の Windows PowerShell コマンド プロンプト_から、次のコマンドを実行します。

    New-NetFirewallRule -DisplayName "MySQL57" -Direction Inbound –Protocol TCP –LocalPort 3306 -Action Allow -Profile Public

## <a name="test-your-remote-connection"></a>リモート接続をテストする
MySQL Server サービスを実行する Azure VM へのリモート接続をテストするには、VN を含むクラウド サービスの DNS 名を指定する必要があります。

1. Azure Portal で、[**仮想マシン (クラシック)**] をクリックし、MySQL サーバー仮想マシンの名前をクリックしてから、[**ダッシュボード**] をクリックします。
2. 仮想マシンのダッシュボードから、[**DNS 名**] の値をメモします。 たとえば次のようになります。

   ![](media/mysql-2008r2/MySQL_DNSName.png)
3. MySQL または MySQL クライアントを実行するローカル コンピューターで、次のコマンドを実行して MySQL ユーザーとしてログインします。

     mysql -u <yourMysqlUsername> -p -h <yourDNSname>

   たとえば、MySQL のユーザー名に _dbadmin3_、仮想マシンの DNS 名に _testmysql.cloudapp.net_ を使用する場合は、次のコマンドを使用して MySQL を開始できます。

     mysql -u dbadmin3 -p -h testmysql.cloudapp.net

## <a name="next-steps"></a>次のステップ
MySQL の実行の詳細については、 [MySQL のドキュメント](http://dev.mysql.com/doc/)を参照してください。

