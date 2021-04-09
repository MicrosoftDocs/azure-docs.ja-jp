---
title: クイック スタート:Azure で Linux SQL Server VM を作成する
description: このチュートリアルでは、Azure Portal で Linux SQL Server 2017 仮想マシンを作成する方法について説明します。
services: virtual-machines-linux
author: MashaMSFT
ms.date: 10/22/2019
tags: azure-service-management
ms.topic: quickstart
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: fd5c289f2b441b5862d863d9a390a1cd054acbfa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92790153"
---
# <a name="provision-a-linux-virtual-machine-running-sql-server-in-the-azure-portal"></a>Azure portal で SQL Server を実行している Linux 仮想マシンをプロビジョニングする
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Linux](sql-vm-create-portal-quickstart.md)
> * [Windows](../windows/sql-vm-create-portal-quickstart.md)

このクイック スタート チュートリアルでは、Azure portal を使用して、SQL Server 2017 がインストールされている Linux 仮想マシンを作成します。 次のことについて学習します。 


* [ギャラリーから SQL Server を実行する Linux VM を作成する](#create)
* [SSH による新しい VM への接続](#connect)
* [SA パスワードの変更](#password)
* [リモート接続の構成](#remote)

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free) を作成してください。

## <a name="create-a-linux-vm-with-sql-server-installed"></a><a id="create"></a>SQL Server がインストールされた Linux VM の作成

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. 左側のウィンドウで、 **[リソースの作成]** を選びます。

1. **[リソースの作成]** ウィンドウで、 **[Compute]** を選択します。

1. **[おすすめ]** 見出しの横にある **[すべて表示]** を選択します。

   ![VM イメージをすべて表示する](./media/sql-vm-create-portal-quickstart/azure-compute-blade.png)

1. 検索ボックスに「**SQL Server 2019**」と入力し、**Enter** キーを押して検索を開始します。

1. **[オペレーティング システム]**  >  **[Redhat]** の順に選択して検索結果を限定します。

    ![SQL Server 2019 VM イメージの検索フィルター](./media/sql-vm-create-portal-quickstart/searchfilter.png)

1. 検索結果から SQL Server 2019 Linux イメージを選択します。 このチュートリアルでは **SQL Server 2019 on RHEL74** を使用します。

   > [!TIP]
   > Developer エディションでは、Enterprise エディションの機能を使用してテストまたは開発を行うことができますが、SQL Server のライセンス コストはかかりません。 料金は、Linux VM の実行コストに対してのみ支払います。

1. **［作成］** を選択します 


### <a name="set-up-your-linux-vm"></a>Linux VM の設定

1. **[基本]** タブで、**サブスクリプション** と **リソース グループ** を選択します。 

    ![[基本] ウィンドウ](./media/sql-vm-create-portal-quickstart/basics.png)

1. **[仮想マシン名]** に、新しい Linux VM の名前を入力します。
1. 次に、以下の値を入力または選択します。
   * **[リージョン]** :適切な Azure リージョンを選択します。
   * **[可用性オプション]** :アプリとデータに最適な可用性と冗長性のオプションを選択します。
   * **[サイズの変更]** :このオプションを選択してマシン サイズを選択し、完了したら **[選択]** を選択します。 VM マシン サイズの詳細については、[VM のサイズ](../../../virtual-machines/sizes.md)に関するページを参照してください。

     ![VM サイズを選択する](./media/sql-vm-create-portal-quickstart/vmsizes.png)

   > [!TIP]
   > 開発と機能テストには、**DS2** 以上の VM サイズを使用してください。 パフォーマンス テストには、**DS13** 以上を使用してください。

   * **[認証の種類]** : **[SSH 公開キー]** を選択します。

     > [!Note]
     > 認証には、SSH 公開キーまたはパスワードを使用する選択肢があります。 安全性が高いのは SSH です。 SSH キーを生成する方法の手順については、[Azure の Linux VM と Mac for Linux VM に SSH キーを作成する方法](../../../virtual-machines/linux/mac-create-ssh-keys.md)に関するページを参照してください。

   * **[ユーザー名]** : VM の管理者名を入力します。
   * **[SSH 公開キー]** : RSA 公開キーを入力します。
   * **[パブリック受信ポート]** : **[選択したポートを許可する]** を選択し、 **[パブリック受信ポートを選択]** ボックスの一覧で **[SSH (22)]** ポートを選択します。 このクイック スタートでは、SQL Server に接続し、構成を完了するために、この手順が必要です。 SQL Server にリモートから接続したい場合は、仮想マシンの作成後に Microsoft SQL Server がインターネット上の接続に使用する既定のポート (1433) へのトラフィックを手動で許可する必要があります。

     ![受信ポート](./media/sql-vm-create-portal-quickstart/port-settings.png)

1. 次の追加のタブで、設定を変更するか、既定の設定をそのまま使用します。
    * **ディスク**
    * **ネットワーク**
    * **管理**
    * **ゲストの構成**
    * **タグ**

1. **[Review + create]\(レビュー + 作成\)** を選択します。
1. **[確認および作成]** ウィンドウで、 **[作成]** を選択します。

## <a name="connect-to-the-linux-vm"></a><a id="connect"></a>Linux VM への接続

BASH シェルを既に使用している場合、**ssh** コマンドを使用して Azure VM に接続します。 次のコマンドの VM ユーザー名と IP アドレスを置き換えて、Linux VM に接続します。

```bash
ssh azureadmin@40.55.55.555
```

VM の IP アドレスは、Azure Portal で確認できます。

![Azure Portal の IP アドレス](./media/sql-vm-create-portal-quickstart/vmproperties.png)

Windows を使用していて BASH シェルがない場合は PuTTY などの SSH クライアントをインストールします。

1. [PuTTY をダウンロードしてインストールします](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

1. PuTTY を実行します。

1. PuTTY の構成画面で、VM のパブリック IP アドレスを入力します。

1. **[Open]\(開く\)** を選択し、プロンプトでユーザー名とパスワードを入力します。

Linux VM への接続の詳細については、[ポータルを使用して Azure に Linux VM を作成する方法](../../../virtual-machines/linux/quick-create-portal.md)に関するページを参照してください。

> [!NOTE]
> サーバーのホストキーがレジストリにキャッシュされていないことを示す PuTTY のセキュリティ アラートが表示された場合は、次のオプションから選択します。 このホストを信頼する場合は、 **[Yes]\(はい\)** を選択して PuTTy のキャッシュにキーを追加し、接続を続行します。 キーをキャッシュに追加せずに接続を 1 回だけ実行する場合は、 **[No]\(いいえ\)** を選択します。 このホストを信頼しない場合は、 **[Cancel]\(キャンセル\)** を選択して接続を破棄します。

## <a name="change-the-sa-password"></a><a id="password"></a> SA パスワードの変更

新しい仮想マシンには、ランダム SA パスワードで SQL Server がインストールされます。 このパスワードは、SA ログインで SQL Server に接続する前にリセットしてください。

1. Linux VM に接続した後、新しいコマンド ターミナルを開きます。

1. 次のコマンドで SA パスワードを変更します。

   ```bash
   sudo systemctl stop mssql-server
   sudo /opt/mssql/bin/mssql-conf set-sa-password
   ```

   求められたら、新しい SA パスワードとパスワードの確認を入力します。

1. SQL Server サービスを再起動します。

   ```bash
   sudo systemctl start mssql-server
   ```

## <a name="add-the-tools-to-your-path-optional"></a>パスへのツールの追加 (オプション)

既定では、SQL Server コマンドライン ツール パッケージなど、いくつかの SQL Server [パッケージ](sql-server-on-linux-vm-what-is-iaas-overview.md#packages)がインストールされています。 このツール パッケージには、**sqlcmd** ツールと **bcp** ツールが含まれています。 便宜上、必要に応じてツールのパス `/opt/mssql-tools/bin/` を **PATH** 環境変数に追加することができます。

1. 次のコマンドを実行し、ログイン セッションと対話型/非ログイン セッションの両方の **PATH** を変更します。

   ```bash
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
   source ~/.bashrc
   ```

## <a name="configure-for-remote-connections"></a><a id="remote"></a>リモート接続の構成

Azure VM 上の SQL Server にリモートで接続する必要がある場合、ネットワーク セキュリティ グループで受信規則を構成する必要があります。 この規則により、SQL Server がリッスンするポート (既定では 1433) のトラフィックが許可されます。 次の手順では、この手順に Azure Portal を使用する方法について説明します。

> [!TIP]
> プロビジョニング時に設定で受信ポート **[MS SQL (1433)]** を選択した場合、これらの変更が自動的に行われています。 ファイアウォールの構成方法に関する次のセクションに進むことができます。

1. ポータルで、 **[仮想マシン]** を選び、SQL Server VM を選びます。
1. 左側のナビゲーション ウィンドウで、 **[設定]** の **[ネットワーク]** を選択します。
1. [ネットワーク] ウィンドウで、 **[受信ポートの規則]** の下にある **[受信ポートを追加する]** を選択します。

   ![受信ポートの規則](./media/sql-vm-create-portal-quickstart/networking.png)

1. **[サービス]** の一覧で **[MS SQL]** を選択します。

    ![MS SQL セキュリティ グループ規則](./media/sql-vm-create-portal-quickstart/sqlnsgrule.png)

1. **[OK]** をクリックして VM の規則を保存します。

### <a name="open-the-firewall-on-rhel"></a>RHEL でファイアウォールを開く

このチュートリアルでは、Red Hat Enterprise Linux (RHEL) VM を作成するよう説明してきました。 RHEL VM にリモートで接続する場合、Linux ファイアウォールのポート 1433 を開くことも必要になります。

1. RHEL VM に[接続](#connect)します。

1. BASH シェルで、次のコマンドを実行します。

   ```bash
   sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
   sudo firewall-cmd --reload
   ```

## <a name="next-steps"></a>次のステップ

Azure に SQL Server 2017 仮想マシンを作成できたので、**sqlcmd** を使用してローカルで接続し、Transact-SQL クエリを実行できます。

SQL Server のリモート接続用に Azure VM を構成した場合は、リモート接続が可能になります。 Windows から SQL Server on Linux にリモートで接続する方法の例については、[Windows 上の SSMS を使用した SQL Server on Linux への接続](/sql/linux/sql-server-linux-develop-use-ssms)に関するページを参照してください。 Visual Studio Code を使用して接続するには、「[Use Visual Studio Code to create and run Transact-SQL scripts for SQL Server (Visual Studio Code を使用して SQL Server の Transact-SQL スクリプトを作成して実行する)](/sql/linux/sql-server-linux-develop-use-vscode)」を参照してください。

SQL Server on Linux の全般的な情報については、[SQL Server 2017 on Linux の概要](/sql/linux/sql-server-linux-overview)に関するページを参照してください。 SQL Server 2017 Linux 仮想マシンの使用の詳細については、[Azure の SQL Server 2017 仮想マシンの概要](sql-server-on-linux-vm-what-is-iaas-overview.md)に関するページを参照してください。