---

title: "Azure Backup Server を使用してワークロードを Azure にバックアップする | Microsoft Docs"
description: "Azure Backup Server を使用してワークロードを保護し、Azure Portal にバックアップします。"
services: backup
documentationcenter: 
author: PVRK
manager: shivamg
editor: 
keywords: "Azure Backup Server; ワークロードの保護; ワークロードのバックアップ"
ms.assetid: e7fb1907-9dc1-4ca1-8c61-50423d86540c
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/10/2017
ms.author: masaran;trinadhk;pullabhk;markgal
translationtype: Human Translation
ms.sourcegitcommit: d8289128414bc67a7c064c827a9bec047f6f22bc
ms.openlocfilehash: 1462ee0e247fb5d590a70d23ece5723a065b4140
ms.lasthandoff: 01/28/2017


---
# <a name="preparing-to-back-up-workloads-using-azure-backup-server"></a>Azure Backup Server を使用してワークロードをバックアップするための準備
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
> * [Azure Backup Server (クラシック)](backup-azure-microsoft-azure-backup-classic.md)
> * [SCDPM (クラシック)](backup-azure-dpm-introduction-classic.md)
>
>

この記事では、Azure Backup Server を使用してワークロードをバックアップする環境の準備方法について説明します。 Azure Backup Server を使用すると、単一のコンソールから Hyper-V VM、Microsoft SQL Server、SharePoint Server、Microsoft Exchange、Windows クライアントなどのアプリケーションのワークロードを保護することができます。

> [!NOTE]
> Azure Backup Server は VMware VM を保護できるようになったため、セキュリティ機能が強化されています。 以下のセクションで説明するように製品をインストールし、Update 1 および最新の Azure Backup エージェントを適用します。 Azure Backup Server を使用した VMware バックアップの詳細については、[ハウツーブログ](https://azure.microsoft.com/blog/four-simple-steps-to-backup-vmware-vms-using-azure-backup-server/)を参照してください。 セキュリティ機能の詳細については、[Azure バックアップ セキュリティ機能のドキュメント](backup-azure-security-feature.md)を参照してください。
>
>

Infrastructure as a Server (IaaS) ワークロード (Azure の VM など) を保護することもできます。

> [!NOTE]
> Azure には、リソースの作成と操作に関して&2; 種類のデプロイメント モデルがあります。[Resource Manager デプロイメント モデルとクラシック デプロイメント モデル](../azure-resource-manager/resource-manager-deployment-model.md)です。 この記事では、Resource Manager モデルを使用してデプロイされた VM を復元するための情報および手順を示しています。
>
>

Azure Backup Server には、Data Protection Manager (DPM) のワークロード バックアップ機能の大半が継承されています。 この記事は、DPM ドキュメントと連動し、いくつかの共通の機能について説明しています。 Azure Backup Server には DPM と同じ機能が多数存在しますが、 Azure Backup Server は、テープへのバックアップ機能は持たず、System Center とも連携しません。

## <a name="1-choose-an-installation-platform"></a>1.インストール プラットフォームを選択する
Azure Backup Server を準備して実行するための最初の手順は、Windows Server のセットアップです。 サーバーの設置場所は Azure でもオンプレミスでもかまいません。

### <a name="using-a-server-in-azure"></a>Azure に設置されたサーバーを使用する場合
Azure Backup Server の実行に使用するサーバーを選ぶときは、まず Windows Server 2012 R2 Datacenter のギャラリー イメージにアクセスすることをお勧めします。 Azure で推奨される仮想マシンの作成方法については、[Azure Portalで初めての Windows 仮想マシンを作成する方法](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関する記事をご覧ください。Azure を使用したことがなくてもわかりやすいように説明されています。 サーバー仮想マシン (VM) に推奨される最小要件は A2 Standard (2 コア、3.5 GB RAM) です。

Azure Backup Server を使用したワークロードの保護には、数多くの注意点があります。 これらの注意点については、「 [Azure Virtual Machine として DPM をインストールする](https://technet.microsoft.com/library/jj852163.aspx)」の記事で説明されています。 マシンのデプロイ前に、この記事によく目を通してください。

### <a name="using-an-on-premises-server"></a>オンプレミスに設置されたサーバーを使用する場合
基本サーバーを Azure で実行したくない場合は、Hyper-V VM や VMware VM、物理ホストでサーバーを実行することができます。 サーバー ハードウェアに推奨される最小要件は 2 コア、4 GB RAM です。 サポートされるオペレーティング システムを以下の表に示します。

| オペレーティング システム | プラットフォーム | SKU |
|:--- | --- |:--- |
| Windows Server 2012 R2 と最新 SP |64 ビット |Standard、Datacenter、Foundation |
| Windows Server 2012 と最新 SP |64 ビット |Datacenter、Foundation、Standard |
| Windows Storage Server 2012 R2 と最新 SP |64 ビット |Standard、Workgroup |
| Windows Storage Server 2012 と最新 SP |64 ビット |Standard、Workgroup |

Windows Server の重複除去を使用して DPM ストレージの重複を除去することができます。 Hyper-V VM にデプロイするときは、 [DPM と重複除去](https://technet.microsoft.com/library/dn891438.aspx) が連携するしくみの詳細を確認してください。

> [!NOTE]
> ドメイン コントローラーとして実行されているコンピューターに Azure Backup Server をインストールすることはできません。
>
>

Azure Backup Server はドメインに参加させる必要があります。 サーバーを別のドメインに移動する場合には、Azure Backup Server のインストール前に新しいドメインにサーバーを参加させることをお勧めします。 デプロイ後の、新しいドメインへの既存の Azure Backup Server マシンの移動は *サポートされていません*。

## <a name="2-recovery-services-vault"></a>2.Recovery Services コンテナー
バックアップ データを Azure に送信する場合でも、ローカルに保存する場合でも、ソフトウェアを Azure に接続する必要があります。 具体的には、Azure Backup Server マシンを Recovery Services コンテナーに登録する必要があります。

Recovery Services コンテナーを作成するには、次の手順に従います。

1. [Azure ポータル](https://portal.azure.com/)にサインインします。
2. ハブ メニューで **[参照]** をクリックし、リソースの一覧で「**Recovery Services**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Recovery Services コンテナー]**をクリックします。

    ![Create Recovery Services Vault step 1](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png) <br/>

    Recovery Services コンテナーの一覧が表示されます。
3. **[Recovery Services コンテナー]** メニューの **[追加]** をクリックします。

    ![Create Recovery Services Vault step 2](./media/backup-azure-microsoft-azure-backup/rs-vault-menu.png)

    [Recovery Services コンテナー] ブレードが開き、**[名前]**、**[サブスクリプション]**、**[リソース グループ]**、**[場所]** を指定するよう求められます。

    ![Create Recovery Services vault step 5](./media/backup-azure-microsoft-azure-backup/rs-vault-attributes.png)
4. **[名前]**ボックスに、コンテナーを識別する表示名を入力します。 名前は Azure サブスクリプションに対して一意である必要があります。 2 ～ 50 文字の名前を入力します。 名前の先頭にはアルファベットを使用する必要があります。また、名前に使用できるのはアルファベット、数字、ハイフンのみです。
5. **[サブスクリプション]** をクリックして、使用可能なサブスクリプションの一覧を表示します。 どのサブスクリプションを使用すればよいかがわからない場合は、既定 (または推奨) のサブスクリプションを使用してください。 組織のアカウントが複数の Azure サブスクリプションに関連付けられている場合に限り、複数の選択肢が存在します。
6. **[リソース グループ]** をクリックして、使用可能なリソース グループを表示するか、**[新規]** をクリックして、新しいリソース グループを作成します。 リソース グループの詳細については、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)」をご覧ください。
7. **[場所]** をクリックして、コンテナーの地理的リージョンを選択します。
8. **[作成]**をクリックします。 Recovery Services コンテナーの作成に時間がかかることがあります。 ポータルの右上隅で、状態の通知を監視します。
   コンテナーが作成されると、ポータルで開かれます。

### <a name="set-storage-replication"></a>ストレージ レプリケーションの設定
ストレージ レプリケーション オプションでは、geo 冗長ストレージとローカル冗長ストレージのどちらかを選択できます。 既定では、コンテナーには geo 冗長ストレージがあります。 プライマリ バックアップの場合は、オプションが geo 冗長ストレージに設定されているままにします。 冗長性を犠牲にしても低コストなバックアップが必要な場合は、ローカル冗長ストレージを選択します。 [geo 冗長](../storage/storage-redundancy.md#geo-redundant-storage)ストレージ オプションと[ローカル冗長](../storage/storage-redundancy.md#locally-redundant-storage)ストレージ オプションの詳細については、[Azure Storage のレプリケーションの概要](../storage/storage-redundancy.md)に関する記事をご覧ください。

ストレージ レプリケーション設定を編集するには、次の手順を実行します。

1. コンテナーを選択して、コンテナーのダッシュボードと [設定] ブレードを開きます。 **[設定]** ブレードが開かない場合は、コンテナーのダッシュボードで **[すべての設定]** をクリックします。
2. **[設定]** ブレードで、**[バックアップ インフラストラクチャ]** > **[バックアップ構成]** をクリックして、**[バックアップ構成]** ブレードを開きます。 **[バックアップ構成]** ブレードで、コンテナーのストレージ レプリケーション オプションを選択します。

    ![バックアップ資格情報コンテナーの一覧](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

    コンテナーのストレージ オプションを選択したら、VM をコンテナーに関連付けることができます。 関連付けを開始するには、Azure 仮想マシンを検出して登録する必要があります。

## <a name="3-software-package"></a>3.ソフトウェア パッケージ
### <a name="downloading-the-software-package"></a>ソフトウェア パッケージのダウンロード
1. [Azure ポータル](https://portal.azure.com/)にサインインします。
2. 既に Recovery Services コンテナーが開かれている場合は、手順 3. に進みます。 Recovery Services コンテナーが開かれていない場合は、Azure Portal でハブ メニューの **[参照]** をクリックします。

   * リソース ボックスに「 **Recovery Services**」と入力します。
   * 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Recovery Services コンテナー]**が表示されたら、それをクリックします。

     ![Create Recovery Services Vault step 1](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     Recovery Services コンテナーの一覧が表示されます。
   * Recovery Services コンテナーの一覧で、コンテナーを選択します。

     選択したコンテナーのダッシュボードが開きます。

     ![Open vault blade](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. 既定では、**[設定]** ブレードが開きます。 設定ブレードが閉じている場合は、 **[設定]** をクリックし、設定ブレードを開きます。

    ![Open vault blade](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. **[バックアップ]** をクリックして、作業の開始ウィザードを開きます。

    ![バックアップ作業の開始](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    表示される **[バックアップ作業の開始]** ブレードでは、**[バックアップの目標]** が自動的に選択されます。

    ![Backup-goals-default-opened](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. **[What do you want to backup バックアップの目標]** ブレードで、**[ワークロードはどこで実行されていますか?]** メニューの **[オンプレミス]** を選択します。

    ![目標としてのオンプレミスおよびワークロード](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    **[何をバックアップしますか?]** ドロップダウン メニューで、Azure Backup Server を使用して保護するワークロードを選択し、**[OK]** をクリックします。

    **バックアップ作業の開始**ウィザードが、Azure にワークロードを保護するための **[インフラストラクチャの準備]** オプションに切り替わります。

   > [!NOTE]
   > ファイルとフォルダーだけを保護する場合は、Azure Backup エージェントを使用すること、および[ファイルとフォルダーのバックアップ](backup-try-azure-backup-in-10-mins.md)に関する記事のガイダンスに従って操作することをお勧めします。 ファイルやフォルダー以外を保護する場合、または将来的に保護のニーズを拡張する場合は、そのワークロードを選択します。
   >
   >

    ![作業の開始ウィザードの変更](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. 表示された **[インフラストラクチャの準備]** ブレードで、[Microsoft Azure Backup Server のインストール] と [コンテナー資格情報のダウンロード] の各 **[ダウンロード]** リンクをクリックします。 Azure Backup Server を Recovery Services コンテナーに登録する間は、これらのコンテナー資格情報を使用します。 これらのリンクをクリックすると、ソフトウェア パッケージを入手できるダウンロード センターが表示されます。

    ![Azure Backup Server 用のインフラストラクチャの準備](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. すべてのファイルを選択し、 **[次へ]**をクリックします。 Microsoft Azure Backup のダウンロード ページからすべてのファイルをダウンロードし、すべてのファイルを同じフォルダーに配置します。

    ![Download center 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    すべてのファイルをまとめてダウンロードするとサイズが 3 G を超えるため、10 Mbps のダウンロード リンクでは、ダウンロードが完了するまでに最大で 60 分かかることがあります。

### <a name="extracting-the-software-package"></a>ソフトウェア パッケージの抽出
すべてのファイルをダウンロードしたら、 **MicrosoftAzureBackupInstaller.exe**をクリックします。 **Microsoft Azure Backup セットアップ ウィザード** が開始され、指定した場所にセットアップ ファイルが抽出されます。 ウィザードの手順を続行し、 **[抽出]** ボタンをクリックして抽出プロセスを開始します。

> [!WARNING]
> セットアップ ファイルを抽出するには、少なくとも 4 GB の空き領域が必要です。
>
>

![Microsoft Azure Backup セットアップ ウィザード](./media/backup-azure-microsoft-azure-backup/extract/03.png)

抽出プロセスが完了したら、Microsoft Azure Backup Server のインストールを開始するために、抽出された *setup.exe* を起動するチェック ボックスをオンにし、 **[完了]** をクリックします。

### <a name="installing-the-software-package"></a>ソフトウェア パッケージのインストール
1. **[Microsoft Azure Backup]** をクリックしてセットアップ ウィザードを起動します。

    ![Microsoft Azure Backup セットアップ ウィザード](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. ようこそ画面で **[次へ]** をクリックします。 *[前提条件の確認]* セクションが表示されます。 この画面で **[確認]** をクリックして、Azure Backup Server のハードウェアとソフトウェアの前提条件が満たされているかどうかを確認します。 前提条件がすべて正常に満たされている場合は、マシンが要件を満たしていることを示すメッセージが表示されます。 **[次へ]** をクリックします。

    ![Azure Backup Server - Welcome and Prerequisites check](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Microsoft Azure Backup Server には SQL Server Standard が必要であり、Azure Backup Server のインストール パッケージには、適切な SQL Server バイナリがバンドルされています。 新しい Azure Backup Server のインストールを開始するには、**[Install new Instance of SQL Server with this Setup (このセットアップを使用して、SQL Server の新しいインスタンスをインストールします)]** をオンにし、**[確認してインストール]** をクリックします。 前提条件が正常にインストールされたら、 **[次へ]**をクリックします。

    ![Azure Backup Server - SQL check](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    エラーが発生し、コンピューターの再起動を推奨された場合は、コンピューターを再起動して **[再確認]**をクリックします。

   > [!NOTE]
   > Azure Backup Server は、リモートの SQL Server インスタンスでは動作しません。 Azure Backup Server に使用されるインスタンスは、ローカルに存在する必要があります。
   >
   >
4. Microsoft Azure Backup サーバーのファイルをインストールする場所を指定し、 **[次へ]**をクリックします。

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    スクラッチ場所は、Azure へのバックアップの要件です。 スクラッチ場所が、クラウドにバックアップする予定のデータの 5% 以上であることを確認します。 ディスクを保護するために、インストールが完了した後で個別のディスクを構成する必要があります。 記憶域プールの詳細については、「 [記憶域プールおよびディスク記憶域の構成](https://technet.microsoft.com/library/hh758075.aspx)」を参照してください。
5. 制限付きのローカル ユーザー アカウント用に強力なパスワードを指定し、 **[次へ]**をクリックします。

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. *Microsoft Update* を使用するかどうかを選択し、更新プログラムを確認して **[次へ]**をクリックします。

   > [!NOTE]
   > Windows Update を Microsoft Update にリダイレクトすることをお勧めします。Microsoft Update は、Windows と Microsoft Azure Backup Server などのその他の製品のセキュリティ更新プログラムと重要な更新プログラムを提供しています。
   >
   >

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. *[設定の概要]* を確認し、 **[インストール]**をクリックします。

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. インストールは段階的に実施します。 最初の段階では、Microsoft Azure Recovery Services エージェントをサーバーにインストールします。 インターネットに接続できるかどうかも、ウィザードによって確認されます。 インターネット接続を利用可能な場合は、インストールを続行できます。利用可能でない場合は、プロキシの詳細を指定してインターネットに接続する必要があります。

    次の手順では、Microsoft Azure Recovery Services エージェントを構成します。 構成を行う際、マシンを Recovery Services コンテナーに登録するために、コンテナーの資格情報を提供する必要があります。 また、Azure とお客様の環境間で送信されるデータを暗号化/復号化するためのパスフレーズも提供します。 自動的にパスフレーズを生成するか、最小で 16 文字の独自のパスフレーズを指定することができます。 エージェントの構成が完了するまで、ウィザードを続行します。

    ![Azure Backup Serer PreReq2](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Microsoft Azure Backup Server の登録が正常に完了すると、セットアップ ウィザードは、SQL Server と Azure Backup Server コンポーネントのインストールおよび構成を開始します。 SQL Server コンポーネントのインストールが完了すると、Microsoft Azure Backup Server コンポーネントがインストールされます。

    ![Azure Backup Server](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

インストール手順が正常に完了すると、製品のデスクトップ アイコンも作成されます。 アイコンをダブルクリックして、製品を起動します。

### <a name="add-backup-storage"></a>Backup ストレージの追加
一次バックアップ コピーは、Azure Backup Server マシンに接続されているストレージに保持されます。 ディスクを追加する方法の詳細については、「 [記憶域プールおよびディスク記憶域の構成](https://technet.microsoft.com/library/hh758075.aspx)」を参照してください。

> [!NOTE]
> Azure にデータを送信する場合でも、Backup ストレージを追加する必要があります。 Azure Backup Server の現在のアーキテクチャでは、Azure Backup コンテナーにはデータの " *2 番目の* " コピーが保持され、最初の (必須の) バックアップ コピーはローカル ストレージに保持されます。
>
>

## <a name="4-network-connectivity"></a>4.ネットワーク接続
Azure Backup Server が正常に動作するためには、Azure Backup サービスに接続されている必要があります。 マシンが Azure に接続されているかどうかを確認するには、Azure Backup Server PowerShell コンソールで ```Get-DPMCloudConnection``` コマンドレットを使用します。 コマンドレットの出力が TRUE の場合、マシンは接続されていますが、それ以外の場合は接続されていません。

同時に、Azure のサブスクリプションが正常な状態である必要があります。 サブスクリプションの状態を確認および管理するには、[サブスクリプション ポータル](https://account.windowsazure.com/Subscriptions)にログインします。

Azure への接続と Azure サブスクリプションの状態がわかれば、以下の表から、提供されるバックアップ/復元機能に対する影響を確認することができます。

| 接続状態 | Azure サブスクリプション | Azure へのバックアップ | ディスクへのバックアップ | Azure からの復元 | ディスクからの復元 |
| --- | --- | --- | --- | --- | --- |
| 接続中 |アクティブ |許可 |許可 |許可 |許可 |
| 接続中 |有効期限切れ |停止済み |停止済み |許可 |許可 |
| 接続中 |プロビジョニング解除済み |停止済み |停止済み |停止され、Azure の回復ポイントが削除される |停止済み |
| 切断されている期間が 15 日を越える |アクティブ |停止済み |停止済み |許可 |許可 |
| 切断されている期間が 15 日を越える |有効期限切れ |停止済み |停止済み |許可 |許可 |
| 切断されている期間が 15 日を越える |プロビジョニング解除済み |停止済み |停止済み |停止され、Azure の回復ポイントが削除される |停止済み |

### <a name="recovering-from-loss-of-connectivity"></a>接続の切断からの回復
ファイアウォールまたはプロキシにより Azure へのアクセスが妨げられている場合、ファイアウォール/プロキシのプロファイルのホワイトリストに以下のドメイン アドレスを追加する必要があります。

* www.msftncsi.com
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*.windows.net

Azure Backup Server マシンが Azure に接続できるようになると、実行可能な操作が Azure サブスクリプションの状態に応じて決まります。 マシンが "接続中" になった場合に許可される操作の詳細は、上記の表に記載されています。

### <a name="handling-subscription-states"></a>サブスクリプションの状態の処理
Azure サブスクリプションの状態が "*有効期限切れ*" または "*プロビジョニング解除済み*" である場合、"*アクティブ*" 状態にすることができます。 ただし、状態が " *アクティブ*" でない間は、製品の動作に次のような影響があります。

* サブスクリプションが " *プロビジョニング解除済み* " の場合、プロビジョニングが解除されている期間は機能を使用できません。 " *アクティブ*" になると、製品のバックアップ/復元機能を使用できるようになります。 ローカル ディスクのバックアップ データが十分に長い間保持されている場合は、それらのデータも回復できます。 ただし、Azure に保持されるバックアップ データは、サブスクリプションが " *プロビジョニング解除済み* " 状態になると失われ、回復できなくなります。
* サブスクリプションが "*有効期限切れ*" になった場合は、再び "*アクティブ*" になるまで機能を使用できなくなるだけです。 サブスクリプションが " *有効期限切れ* " になった期間に予定されていたバックアップは実行されません。

## <a name="troubleshooting"></a>トラブルシューティング
Microsoft Azure Backup Server がセットアップ段階 (またはバックアップや復元) でエラーのため失敗した場合、詳細については、この[エラー コードのドキュメント](https://support.microsoft.com/kb/3041338)を参照してください。
[Azure Backup 関連の FAQ](backup-azure-backup-faq.md)

## <a name="next-steps"></a>次のステップ
[DPM 用の環境の準備](https://technet.microsoft.com/library/hh758176.aspx) について、Microsoft TechNet サイトのページで詳細を確認してください。 このページには、Azure Backup Server のデプロイと使用が可能なサポートされる構成も記載されています。

以下の記事により、Microsoft Azure Backup Server を使用したワークロードの保護について理解を深めてください。

* [SQL Server のバックアップ](backup-azure-backup-sql.md)
* [SharePoint サーバーのバックアップ](backup-azure-backup-sharepoint.md)
* [代替サーバーのバックアップ](backup-azure-alternate-dpm-server.md)

