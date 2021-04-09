---
title: Azure Backup Server を使用してワークロードをバックアップする
description: この記事では、Microsoft Azure Backup Server (MABS) を使用してワークロードを保護およびバックアップするように環境を準備する方法について説明します。
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: d476c228a619f03f798c1a2cd6854a8d603c3637
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98987024"
---
# <a name="install-and-upgrade-azure-backup-server"></a>Azure Backup Server のインストールとアップグレード

> [!div class="op_single_selector"]
>
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

> 適用先:MABS v3 (MABS v2 は現在サポートされていません。 MABS v3 より前のバージョンを使用している場合、最新版にアップグレードしてください)

この記事では、Microsoft Azure Backup Server (MABS) を使用してワークロードをバックアップする環境の準備方法について説明します。 Azure Backup Server を使用すると、単一のコンソールから Hyper-V VM、Microsoft SQL Server、SharePoint Server、Microsoft Exchange、Windows クライアントなどのアプリケーションのワークロードを保護することができます。

> [!NOTE]
> Azure Backup Server は VMware VM を保護できるようになったため、セキュリティ機能が強化されています。 以下のセクションで説明するように製品をインストールし、最新の Azure Backup エージェントをインストールしてください。 Azure Backup Server による VMware サーバーのバックアップに関する詳細については、「[Azure への VMware サーバーのバックアップ](backup-azure-backup-server-vmware.md)」をご覧ください。 セキュリティ機能の詳細については、[Azure Backup のセキュリティ機能のドキュメント](backup-azure-security-feature.md)を参照してください。
>
>

Azure VM にデプロイされた MABS では、Azure で VM をバックアップできますが、バックアップ操作を有効にするには、それらが同じドメイン内にある必要があります。 Azure VM をバックアップするプロセスは、オンプレミスで VM をバックアップするプロセスと変わりませんが、Azure で MABS をデプロイするには、いくつかの制限があります。 制限の詳細については、「[Azure の仮想マシンとしての DPM](/system-center/dpm/install-dpm#setup-prerequisites)」を参照してください。

> [!NOTE]
> Azure には、リソースの作成と操作に関して 2 種類のデプロイ モデルがあります。[Resource Manager とクラシック](../azure-resource-manager/management/deployment-models.md)です。 この記事では、Resource Manager モデルを使用してデプロイされた VM を復元するための情報および手順を示しています。
>
>

Azure Backup Server には、Data Protection Manager (DPM) のワークロード バックアップ機能の大半が継承されています。 この記事は、DPM ドキュメントと連動し、いくつかの共通の機能について説明しています。 Azure Backup Server と DPM の多くの機能は共通ですが、Azure Backup Server ではテープへのバックアップや System Center との統合は行われません。

## <a name="choose-an-installation-platform"></a>インストール プラットフォームを選択する

Azure Backup Server を準備して実行するための最初の手順は、Windows Server のセットアップです。 サーバーの設置場所は Azure でもオンプレミスでもかまいません。

* オンプレミスのワークロードを保護するには、MABS サーバーをオンプレミスに配置する必要があります。
* Azure VM で実行されているワークロードを保護するには、MABS サーバーを Azure に配置し、Azure VM として実行する必要があります。

### <a name="using-a-server-in-azure"></a>Azure に設置されたサーバーを使用する場合

Azure Backup Server の実行に使用するサーバーを選ぶときは、まず Windows Server 2016 Datacenter または Windows Server 2019 Datacenter のギャラリー イメージにアクセスすることをお勧めします。 Azure で推奨される仮想マシンの作成方法については、[Azure Portalで初めての Windows 仮想マシンを作成する方法](../virtual-machines/windows/quick-create-portal.md?toc=/azure/virtual-machines/windows/toc.json)に関する記事をご覧ください。Azure を使用したことがなくてもわかりやすいように説明されています。 サーバー仮想マシン (VM) に推奨される最小要件は4 つのコアと 8 GB の RAM を持つ Standard_A4_v2 です。

Azure Backup Server を使用したワークロードの保護には、数多くの注意点があります。 これらの注意点については、[MABS の保護マトリックス](./backup-mabs-protection-matrix.md)に関するページで説明されています。 マシンをデプロイする前に、この記事によく目を通してください。

### <a name="using-an-on-premises-server"></a>オンプレミスに設置されたサーバーを使用する場合

基本サーバーを Azure で実行したくない場合は、Hyper-V VM や VMware VM、物理ホストでサーバーを実行することができます。 サーバー ハードウェアに推奨される最小要件は 2 コア、8 GB RAM です。 サポートされるオペレーティング システムを以下の表に示します。

| オペレーティング システム | プラットフォーム | SKU |
|:--- | --- |:--- |
| Windows Server 2019 |64 ビット |Standard、Datacenter、Essentials |
| Windows Server 2016 と最新 SP |64 ビット |Standard、Datacenter、Essentials  |

Windows Server の重複除去を使用して DPM ストレージの重複を除去することができます。 Hyper-V VM にデプロイするときは、 [DPM と重複除去](/system-center/dpm/deduplicate-dpm-storage) が連携するしくみの詳細を確認してください。

> [!NOTE]
> Azure Backup Server は、単一目的の専用サーバーで動作するように設計されています。 Azure Backup Server は、次の場所にインストールすることはできません。
>
> * ドメイン コントローラーとして実行されているコンピューター
> * アプリケーション サーバー ロールがインストールされているコンピューター
> * System Center Operations Manager 管理サーバーであるコンピューター
> * Exchange Server が実行されているコンピューター
> * クラスターのノードであるコンピューター
>
> Azure Backup Server のインストールは、Windows Server Core または Microsoft Hyper-V Server ではサポートされていません。

Azure Backup Server は、常にドメインに参加させる必要があります。 サーバーを別のドメインに移動する予定がある場合は、まず Azure Backup Server をインストールし、次にそのサーバーを新しいドメインに参加させます。 デプロイ後の、新しいドメインへの既存の Azure Backup Server マシンの移動は *サポートされていません*。

バックアップ データを Azure に送信するか、またはローカルに保持するかどうかにかかわらず、Azure Backup Server を Recovery Services コンテナーに登録する必要があります。

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>ストレージ レプリケーションの設定

ストレージ レプリケーション オプションでは、geo 冗長ストレージとローカル冗長ストレージのどちらかを選択できます。 既定では、Recovery Services コンテナーは geo 冗長ストレージを使用します。 このコンテナーがプライマリ コンテナーの場合は、ストレージ オプションの設定を geo 冗長ストレージのままにします。 冗長性を犠牲にしても低コストなバックアップが必要な場合は、ローカル冗長ストレージを選択します。 [geo 冗長](../storage/common/storage-redundancy.md#geo-redundant-storage)、[ローカル冗長](../storage/common/storage-redundancy.md#locally-redundant-storage)、[ゾーン冗長](../storage/common/storage-redundancy.md#zone-redundant-storage)のストレージ オプションの詳細については、[Azure Storage のレプリケーションの概要](../storage/common/storage-redundancy.md)に関する記事をご覧ください。

ストレージ レプリケーション設定を編集するには、次の手順を実行します。

1. **[Recovery Services コンテナー]** ペインで、新しいコンテナーを選択します。 **[設定]** セクションの **[プロパティ]** を選択します。
2. **[プロパティ]** で、 **[バックアップ構成]** の **[更新]** を選択します。

3. ストレージのレプリケーションの種類を選択し、 **[保存]** を選択します。

     ![新しいコンテナーのストレージ構成を設定する](./media/backup-create-rs-vault/recovery-services-vault-backup-configuration.png)

## <a name="software-package"></a>ソフトウェア パッケージ

### <a name="downloading-the-software-package"></a>ソフトウェア パッケージのダウンロード

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. Recovery Services コンテナーが既に開かれている場合は、手順 3 に進みます。 Recovery Services コンテナーを開いていなくても、Azure portal にいる場合は、メイン メニューの **[参照]** を選択します。

   * リソース ボックスに「 **Recovery Services**」と入力します。
   * 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Recovery Services コンテナー]** が表示されたら、それを選択します。

     ![Recovery Services コンテナー作成ステップ 1](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     Recovery Services コンテナーの一覧が表示されます。
   * Recovery Services コンテナーの一覧で、コンテナーを選択します。

     選択したコンテナーのダッシュボードが開きます。

     ![コンテナー ダッシュボード](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. 既定では、 **[設定]** ペインが開きます。 閉じている場合は、 **[設定]** を選択して設定ペインを開きます。

    ![設定ペイン](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. **[バックアップ]** を選択して、作業の開始ウィザードを開きます。

    ![バックアップ作業の開始](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    表示される **[バックアップ作業の開始]** ペインでは、 **[バックアップの目標]** が自動的に選択されます。

    ![Backup-goals-default-opened](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. **[バックアップの目標]** ペインで、 **[ワークロードはどこで実行されていますか?]** メニューの **[オンプレミス]** を選択します。

    ![目標としてのオンプレミスおよびワークロード](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    **[何をバックアップしますか?]** ドロップダウン メニューで、Azure Backup Server を使用して保護するワークロードを選択し、 **[OK]** を選択します。

    **バックアップ作業の開始** ウィザードが、Azure にワークロードを保護するための **[インフラストラクチャの準備]** オプションに切り替わります。

   > [!NOTE]
   > ファイルとフォルダーだけをバックアップする場合は、Azure Backup エージェントを使用すること、および[ファイルとフォルダーのバックアップ](./backup-windows-with-mars-agent.md)に関する記事のガイダンスに従って操作することをお勧めします。 ファイルやフォルダー以外を保護する場合、または将来的に保護のニーズを拡張する場合は、そのワークロードを選択します。
   >
   >

    ![作業の開始ウィザードの変更](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. 表示された **[インフラストラクチャの準備]** ペインで、[Microsoft Azure Backup Server のインストール] と [コンテナー資格情報のダウンロード] の各 **[ダウンロード]** リンクを選択します。 Azure Backup Server を Recovery Services コンテナーに登録する間は、これらのコンテナー資格情報を使用します。 これらのリンクをクリックすると、ソフトウェア パッケージを入手できるダウンロード センターが表示されます。

    ![Azure Backup Server 用のインフラストラクチャの準備](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. すべてのファイルを選択し、 **[次へ]** を選択します。 Microsoft Azure Backup のダウンロード ページからすべてのファイルをダウンロードし、すべてのファイルを同じフォルダーに配置します。

    ![Download center 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    すべてのファイルをまとめてダウンロードするとサイズが 3 GB を超えるため、10 MBps のダウンロード リンクでは、ダウンロードが完了するまでに最大で 60 分かかることがあります。

### <a name="extracting-the-software-package"></a>ソフトウェア パッケージの抽出

すべてのファイルをダウンロードしたら、**MicrosoftAzureBackupInstaller.exe** を選択します。 **Microsoft Azure Backup セットアップ ウィザード** が開始され、指定した場所にセットアップ ファイルが抽出されます。 ウィザードの手順を続行し、 **[抽出]** ボタンを選択して抽出プロセスを開始します。

> [!WARNING]
> セットアップ ファイルを抽出するには、少なくとも 4 GB の空き領域が必要です。
>
>

![インストールするファイルを抽出するセットアップ](./media/backup-azure-microsoft-azure-backup/extract/03.png)

抽出プロセスが完了したら、Microsoft Azure Backup Server のインストールを開始するために、新しく抽出される *setup.exe* を起動するチェック ボックスをオンにし、 **[完了]** を選択します。

### <a name="installing-the-software-package"></a>ソフトウェア パッケージのインストール

1. **[Microsoft Azure Backup]** を選択してセットアップ ウィザードを起動します。

    ![Microsoft Azure Backup セットアップ ウィザード](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. ようこそ画面で、 **[次へ]** を選択します。 *[前提条件の確認]* セクションが表示されます。 この画面で **[確認]** を選択して、Azure Backup Server のハードウェアとソフトウェアの前提条件が満たされているかどうかを確認します。 前提条件がすべて正常に満たされている場合は、マシンが要件を満たしていることを示すメッセージが表示されます。 **[次へ]** ボタンを選択します。

    ![Azure Backup Server - Welcome and Prerequisites check](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Azure Backup Server のインストール パッケージには、必要となる適切な SQL Server バイナリがバンドルされています。 新しい Azure Backup Server のインストールを開始する場合は、 **[このセットアップを使用して、SQL Server の新しいインスタンスをインストールします]** オプションを選択し、 **[確認してインストール]** ボタンを選択します。 前提条件が正常にインストールされたら、 **[次へ]** を選択します。

    >[!NOTE]
    >独自の SQL Server を使用する場合、サポートされる SQL Server のバージョンは SQL Server 2014 SP1 以降、2016 および 2017 となります。  すべての SQL Server のバージョンが、Standard または Enterprise 64 ビットである必要があります。
    >Azure Backup Server は、リモートの SQL Server インスタンスでは動作しません。 Azure Backup Server に使用されるインスタンスは、ローカルに存在する必要があります。 既存の SQL Server を MABS で使用している場合、MABS のセットアップでは、SQL Server の "*名前付きインスタンス*" の使用のみがサポートされます。

    ![Azure Backup Server - SQL check](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    エラーが発生し、マシンの再起動を推奨された場合は、そのようにして、 **[再確認]** を選択します。 SQL の構成に問題がある場合は、SQL のガイドラインに従って SQL を構成し、既存の SQL インスタンスを使用して MABS のインストールまたはアップグレードを再試行します。

   **手動で構成**

   SQL の独自のインスタンスを使用するときは、sysadmin ロールに対する builtin\Administrators をマスター DB に追加してください。

    **SQL 2017 の SSRS の構成**

    SQL 2017 の独自のインスタンスを使用するときは、手動で SSRS を構成する必要があります。 SSRS の構成の後で、SSRS の *IsInitialized* プロパティが *True* に設定されていることを確認します。 これが True に設定されていると、MABS は SSRS が既に構成されていると見なして SSRS 構成をスキップします。

    SSRS 構成では次の値を使用します。
    * サービス アカウント:‘組み込みアカウントの使用’ はネットワーク サービスにする必要があります
    * Web サービスの URL:‘仮想ディレクトリ’ は ReportServer_\<SQLInstanceName> にする必要があります
    * データベース: DatabaseName は ReportServer$\<SQLInstanceName> にする必要があります
    * Web ポータルの URL:‘仮想ディレクトリ’ は ReportServer_\<SQLInstanceName> にする必要があります

    SSRS の構成について詳しくは、[こちら](/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode)をご覧ください。

    > [!NOTE]
    > MABS のデータベースとして使用される SQL Server のライセンスは、[マイクロソフト オンライン サービス条件](https://www.microsoft.com/licensing/product-licensing/products)によって管理されます。 OST に従って、MABS にバンドルされている SQL Server は、MABS のデータベースとしてのみ使用できます。

4. Microsoft Azure Backup サーバーのファイルをインストールする場所を指定し、 **[次へ]** を選択します。

    ![ファイルのインストール場所の指定](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    スクラッチ場所は、Azure へのバックアップの要件です。 スクラッチ場所が、クラウドにバックアップする予定のデータの 5% 以上であることを確認します。 ディスクを保護するために、インストールが完了した後で個別のディスクを構成する必要があります。 記憶域プールの詳細については、「[データ ストレージの準備](/system-center/dpm/plan-long-and-short-term-data-storage)」を参照してください。

    ディスク ストレージの容量の要件は、主に保護されるデータのサイズ、毎日の回復ポイントのサイズ、予想されるデータ成長率、および保持期間の目標によって異なります。 ディスク ストレージは、保護されるデータの 2 倍のサイズにすることをお勧めします。 毎日の回復ポイント サイズを保護データ サイズの 10% とし、保有期間を 10 日間と想定しています。 適切なサイズ見積もりを取得するには、[DPM Capacity Planner](https://www.microsoft.com/download/details.aspx?id=54301) を確認します。 

5. 制限付きのローカル ユーザー アカウント用に強力なパスワードを指定し、 **[次へ]** を選択します。

    ![強力なパスワードの指定](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. 更新プログラムを確認するために *Microsoft Update* を使用するかどうかを選択して、 **[次へ]** を選択します。

   > [!NOTE]
   > Windows Update を Microsoft Update にリダイレクトすることをお勧めします。Microsoft Update は、Windows と Microsoft Azure Backup Server などのその他の製品のセキュリティ更新プログラムと重要な更新プログラムを提供しています。
   >
   >

    ![Microsoft Update のオプトイン](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. *[設定の概要]* を確認して、 **[インストール]** を選択します。

    ![設定の概要](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. インストールは段階的に実施します。 最初の段階では、Microsoft Azure Recovery Services エージェントをサーバーにインストールします。 インターネットに接続できるかどうかも、ウィザードによって確認されます。 インターネット接続が利用可能な場合は、インストールを続行できます。 そうでない場合は、インターネットに接続するためのプロキシの詳細を指定する必要があります。

    次の手順では、Microsoft Azure Recovery Services エージェントを構成します。 構成を行う際、マシンを Recovery Services コンテナーに登録するために、コンテナーの資格情報を提供する必要があります。 また、Azure とお使いの環境間で送信されるデータを暗号化/復号化するためのパスフレーズも提供します。 自動的にパスフレーズを生成するか、最小で 16 文字の独自のパスフレーズを指定することができます。 エージェントの構成が完了するまで、ウィザードを続行します。

    ![サーバーの登録ウィザード](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Microsoft Azure Backup Server の登録が正常に完了すると、セットアップ ウィザードは、SQL Server と Azure Backup Server コンポーネントのインストールおよび構成を開始します。 SQL Server コンポーネントのインストールが完了すると、Microsoft Azure Backup Server コンポーネントがインストールされます。

    ![Azure Backup Server セットアップの進行状況](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

インストール手順が正常に完了すると、製品のデスクトップ アイコンも作成されます。 アイコンをダブルクリックして、製品を起動します。

### <a name="add-backup-storage"></a>Backup ストレージの追加

一次バックアップ コピーは、Azure Backup Server マシンに接続されているストレージに保持されます。 ディスクを追加する方法の詳細については、「 [記憶域プールおよびディスク記憶域の構成](./backup-mabs-add-storage.md)」を参照してください。

> [!NOTE]
> Azure にデータを送信する場合でも、Backup ストレージを追加する必要があります。 Azure Backup Server の現在のアーキテクチャでは、Azure Backup コンテナーにはデータの " *2 番目の* " コピーが保持され、最初の (必須の) バックアップ コピーはローカル ストレージに保持されます。
>
>

### <a name="install-and-update-the-data-protection-manager-protection-agent"></a>Data Protection Manager 保護エージェントのインストールと更新

MABS は、System Center Data Protection Manager 保護エージェントを使用します。 保護サーバー上に保護エージェントをインストールする手順は、[こちら](/system-center/dpm/deploy-dpm-protection-agent)をご覧ください。

この後のセクションでは、クライアント コンピューターのために保護エージェントを更新する方法を説明します。

1. Backup Server の管理者コンソールで、 **[Management]** \(管理) >  **[Agents]** \(エージェント) の順に選択します。

2. 表示ウィンドウで、保護エージェントを更新するクライアント コンピューターを選択します。

   > [!NOTE]
   > **[Agent Updates]** \(エージェントの更新プログラム) 列は、保護されるコンピューターごとに保護エージェントの更新がいつ行われるかを示します。 **[アクション]** ウィンドウでは、 **[更新する]** アイコンは、保護されるコンピューターが選択されていて、更新プログラムが利用可能なときのみ表示されます。
   >
   >

3. 選択したコンピューターに更新された保護エージェントをインストールするには、 **[アクション]** ウィンドウで、 **[更新する]** を選択します。

4. ネットワークに接続されていないクライアント コンピューターでは、コンピューターがネットワークに接続されるまで、**[エージェントの状態]** 列には **[更新保留中]** の状態が表示されます。

   クライアント コンピューターがネットワークに接続された後、クライアント コンピューターの **[Agent Updates]** \(エージェントの更新プログラム) 列には、 **[更新中]** の状態が表示されます。

## <a name="move-mabs-to-a-new-server"></a>MABS を新しいサーバーに移動する

記憶域を維持したまま MABS を新しいサーバーに移動する必要がある場合はこの後の手順に従います。 これを実行できるのは、すべてのデータが Modern Backup Storage にある場合のみです。

  > [!IMPORTANT]
  >
  > * 新しいサーバー名は元の Azure Backup Server インスタンスと同じ名前にする必要があります。 以前の記憶域プールと MABS Database (DPMDB) を使用して復旧ポイントを保持する場合は、新しい Azure Backup Server インスタンスの名前を変更することはできません。
  > * MABS Database (DPMDB) のバックアップを用意する必要があります。 これは、後でデータベースを復元する際に必要になります。

1. 表示ウィンドウで、保護エージェントを更新するクライアント コンピューターを選択します。
2. 元の Azure Backup Server をシャットダウンするか、オフラインにします。
3. Active Directory でマシン アカウントをリセットします。
4. 新しいマシンに Server 2016 をインストールし、元の Azure Backup Server と同じマシン名を付けます。
5. ドメインに参加します。
6. Azure Backup Server V3 以降をインストールします (MABS 記憶域プールのディスクを古いサーバーから移動してインポートします)。
7. 手順 1 で作成した DPMDB を復元します。
8. 元のバックアップ サーバーの記憶域を新しいサーバーにアタッチします。
9. SQL から DPMDB を復元します。
10. 新しいサーバーで CMD を (管理者として) 実行します。 Microsoft Azure Backup のインストール場所とゴミ箱フォルダーに移動します

    パスの例: `C:\windows\system32>cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\"`

11. Azure Backup に接続するには、`DPMSYNC -SYNC` を実行します

    DPM 記憶域プールに古いディスクを移動するのではなく、**新しい** ディスクを追加した場合は、`DPMSYNC -Reallocatereplica` を実行します。

## <a name="network-connectivity"></a>ネットワーク接続

Azure Backup Server が正常に動作するためには、Azure Backup サービスに接続されている必要があります。 マシンが Azure に接続されているかどうかを確認するには、Azure Backup Server PowerShell コンソールで ```Get-DPMCloudConnection``` コマンドレットを使用します。 コマンドレットの出力が TRUE の場合、マシンは接続されていますが、そうでない場合は接続されていません。

同時に、Azure のサブスクリプションが正常な状態である必要があります。 サブスクリプションの状態を確認および管理するには、[サブスクリプション ポータル](https://account.windowsazure.com/Subscriptions)にサインインします。

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

マシンのインターネットへのアクセスが制限されている場合、マシンまたはプロキシのファイアウォール設定によって次の URL と IP アドレスが許可されていることを確保してください。

* URL
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP アドレス
  * 20.190.128.0/18
  * 40.126.0.0/18

ExpressRoute Microsoft ピアリングを使用している場合、次のサービスまたはリージョンを選択します。

* Azure Active Directory (12076:5060)
* Microsoft Azure リージョン (Recovery Services コンテナーの場所による)
* Azure Storage (Recovery Services コンテナーの場所による)

詳細については、「[ExpressRoute ルーティングの要件](../expressroute/expressroute-routing.md)」を参照してください。

Azure Backup Server マシンが Azure に接続できるようになると、実行可能な操作が Azure サブスクリプションの状態に応じて決まります。 マシンが "接続中" になった場合に許可される操作の詳細は、上記の表に記載されています。

### <a name="handling-subscription-states"></a>サブスクリプションの状態の処理

Azure サブスクリプションの状態が "*有効期限切れ*" または "*プロビジョニング解除済み*" である場合、"*アクティブ*" 状態にすることができます。 ただし、状態が "*アクティブ*" でない場合は、製品の動作に次のような影響があります。

* サブスクリプションが "*プロビジョニング解除済み*" の場合、プロビジョニングが解除されている期間は機能を使用できません。 " *アクティブ*" になると、製品のバックアップ/復元機能を使用できるようになります。 ローカル ディスクのバックアップ データが十分に長い間保持されている場合は、それらのデータも回復できます。 ただし、Azure に保持されるバックアップ データは、サブスクリプションが " *プロビジョニング解除済み* " 状態になると失われ、回復できなくなります。
* サブスクリプションが "*有効期限切れ*" になった場合は、再び "*アクティブ*" になるまで機能を使用できなくなるだけです。 サブスクリプションが "*有効期限切れ*" になった期間に予定されていたバックアップは実行されません。

## <a name="upgrade-mabs"></a>MABS をアップグレードする

MABS をアップグレードするには、次の手順を使用します。

### <a name="upgrade-from-mabs-v2-to-v3"></a>MABS V2 から V3 にアップグレードする

> [!NOTE]
>
> MABS V2 は、MABS V3 をインストールするための前提条件ではありません。 ただし、MABS V3 にアップグレードできるのは MABS V2 からのみです。

次の手順を使用して MABS をアップグレードします。

1. MABS V2 を MABS V3 にアップグレードするには、OS を Windows Server 2016 または Windows Server 2019 (必要な場合) にアップグレードします。

2. サーバーをアップグレードします。 手順は[インストール](#install-and-upgrade-azure-backup-server)と同様です。 ただし、SQL の設定で、SQL インスタンスを SQL 2017 にアップグレードするか、SQL Server 2017 の独自のインスタンスを使用するかという選択肢があります。

   > [!NOTE]
   >
   > SQL インスタンスのアップグレード中は終了しないでください。 終了すると、SQL レポート インスタンスがアンインストールされるため、MABS を再アップグレードする試みが失敗します。

   > [!IMPORTANT]
   >
   >  SQL 2017 アップグレードの一環として、SQL 暗号化キーをバックアップし、レポート サービスをアンインストールします。 SQL Server のアップグレード後に、レポート サービス (14.0.6827.4788) がインストールされ、暗号化キーが復元されます。
   >
   > SQL 2017 を手動で構成するときは、インストール手順の下にある「*SQL 2017 の SSRS 構成*」セクションを参照してください。

3. 保護サーバー上の保護エージェントを更新します。
4. バックアップを続行します。実稼働サーバーを再起動する必要はありません。
5. これで、データの保護を開始できます。 保護しながら、Modern Backup Storage にアップグレードする場合は、バックアップを格納するボリュームを選択して、プロビジョニングされた領域の下をチェックすることもできます。 [詳細については、こちらを参照してください](backup-mabs-add-storage.md)。

## <a name="troubleshooting"></a>トラブルシューティング

Microsoft Azure Backup Server がセットアップ段階 (またはバックアップや復元) でエラーのため失敗した場合、詳細については、この[エラー コードのドキュメント](https://support.microsoft.com/kb/3041338)を参照してください。
[Azure Backup 関連の FAQ](backup-azure-backup-faq.md)

## <a name="next-steps"></a>次のステップ

DPM 用の環境の準備については[こちら](/system-center/dpm/prepare-environment-for-dpm)に詳細があります。 このページには、Azure Backup Server のデプロイと使用が可能なサポートされる構成も記載されています。 一連の [PowerShell コマンドレット](/powershell/module/dataprotectionmanager/)を使用して、さまざまな操作を実行できます。

以下の記事により、Microsoft Azure Backup Server を使用したワークロードの保護について理解を深めてください。

* [SQL Server のバックアップ](backup-azure-backup-sql.md)
* [SharePoint サーバーのバックアップ](backup-azure-backup-sharepoint.md)
* [代替サーバーのバックアップ](backup-azure-alternate-dpm-server.md)
