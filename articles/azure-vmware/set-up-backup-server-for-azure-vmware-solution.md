---
title: Azure VMware Solution 向けに Azure Backup Server を設定する
description: Azure Backup Server を使用して仮想マシンをバックアップするように Azure VMware Solution 環境を設定します。
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: e9204b9f86c7e9ef67d2e3d6b45ccf4248d00b32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99581484"
---
# <a name="set-up-azure-backup-server-for-azure-vmware-solution"></a>Azure VMware Solution 向けに Azure Backup Server を設定する

Azure Backup Server は、事業継続とディザスター リカバリー (BCDR) 戦略に貢献します。 Azure VMware Solution を使用する場合、Azure Backup Server を使用して仮想マシン (VM) レベルのバックアップのみを構成できます。 

Azure Backup Server では次の場所にバックアップ データを格納できます。

- **ディスク**: 短期的な記憶の場合、Azure Backup Server ではデータがディスク プールにバックアップされます。
- **Azure**: 短期と長期両方のオフプレミスへの記憶の場合、ディスク プールに格納されている Azure Backup Server データを、Azure Backup を使用して Microsoft Azure クラウドにバックアップできます。

Azure Backup Server を使用して、データをソースまたは別の場所に復元します。 このように、予定されていた問題または予期しない問題のために元のデータを使用できない場合は、データを代わりの場所に復元できます。

この記事では、Azure Backup Server を使用して VM をバックアップするように Azure VMware Solution 環境を準備する方法について説明します。 次の手順について説明します。 

> [!div class="checklist"]
> * 推奨される VM ディスクの種類と使用するサイズを決定します。
> * 復旧ポイントを格納する Recovery Services コンテナーを作成します。
> * Recovery Services コンテナーのストレージ レプリケーションを設定します。
> * Azure Backup Server にストレージを追加します。

## <a name="supported-vmware-features"></a>サポートされている VMware の機能

- **エージェントレス バックアップ:** Azure Backup Server では、VM をバックアップするために、vCenter または ESXi サーバーにエージェントをインストールする必要はありません。 代わりに、IP アドレスまたは完全修飾ドメイン名 (FQDN) と、Azure Backup Server で VMware サーバーを認証するために使用されるサインイン資格情報を指定するだけです。
- **クラウド統合バックアップ:** Azure Backup Server では、ディスクとクラウドへのワークロードが保護されます。 Azure Backup Server のバックアップと復旧のワークフローを使用すると、長期保有とオフサイト バックアップを管理できます。
- **vCenter によって管理される VM の検出と保護:** Azure Backup Server では、vCenter または ESXi サーバーにデプロイされている VM が検出されて保護されます。 また、Azure Backup Server では、vCenter によって管理される VM も検出されるため、大規模なデプロイを保護できます。
- **フォルダー レベルの自動保護:** vCenter では、VM フォルダー内の VM を整理できます。 Azure Backup Server によってこれらのフォルダーが検出されます。 これを使用して、すべてのサブフォルダーを含むフォルダー レベルで VM を保護できます。 フォルダーを保護する場合、Azure Backup Server によってそのフォルダー内の VM が保護され、後で追加された VM も保護されます。 Azure Backup Server によって新しい VM が毎日検出され、自動的に保護されます。 再帰的なフォルダーで VM を整理すると、Azure Backup Server によって自動的に検出され、再帰フォルダーにデプロイされた新しい VM が保護されます。
- **クラスター内の vMotion によって移行された VM の Azure Backup Server による継続的な保護:** VM がクラスター内の負荷分散のために vMotion によって移行されると、Azure Backup Server によって自動的に検出されて、VM は引き続き保護されます。
- **必要なファイルの迅速な復旧:** Azure Backup Server では、VM 全体を復旧することなく、Windows VM からファイルやフォルダーを復旧できます。

## <a name="limitations"></a>制限事項

- Azure Backup Server v3 の更新プログラム ロールアップ 1 がインストールされている必要があります。
- Azure Backup Server による最初のバックアップの前に、ユーザー スナップショットをバックアップすることはできません。 Azure Backup Server による最初のバックアップが終了した後では、ユーザー スナップショットをバックアップできます。
- Azure Backup Server では、パススルー ディスクと物理 RAW デバイス マッピング (pRDM) を使用する VMware VM を保護することはできません。
- Azure Backup Server では、VMware vApp を検出または保護することはできません。

Azure VMware Solution 用に Azure Backup Server を設定するには、次の手順を完了する必要があります。

- 前提条件と環境を設定します。
- Recovery Services コンテナーを作成する。
- Azure Backup Server をダウンロードしてインストールします。
- Azure Backup Server にストレージを追加します。

### <a name="deployment-architecture"></a>デプロイメント アーキテクチャ

Azure Backup Server は、Azure VMware Solution の VM を保護するために、Azure のサービスとしてのインフラストラクチャ (IaaS) VM としてデプロイされます。

:::image type="content" source="media/azure-vmware-solution-backup/deploy-backup-server-azure-vmware-solution-diagram.png" alt-text="Azure Backup Server は、Azure VMware Solution の VM を保護するために、Azure のサービスとしてのインフラストラクチャ (IaaS) VM としてデプロイされます。" border="false":::

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Azure Backup Server 環境の前提条件

Azure 環境に Azure Backup Server をインストールするときは、このセクションの推奨事項を検討してください。

### <a name="azure-virtual-network"></a>Azure Virtual Network

[Azure に VMware プライベート クラウド用のネットワークを構成してある](tutorial-configure-networking.md)ことを確認します。

### <a name="determine-the-size-of-the-vm"></a>VM のサイズを決定する

[Azure portal で初めての Windows VM を作成する](../virtual-machines/windows/quick-create-portal.md)チュートリアルの手順に従います。  前の手順で作成した仮想ネットワークに VM を作成します。 Windows Server 2019 Datacenter のギャラリー イメージから始めて、Azure Backup Server を実行します。 

この表は、各 Azure Backup Server VM サイズの保護されるワークロードの最大数をまとめたものです。 この情報は、ワークロードのサイズとチャーンの標準値を使用した、内部のパフォーマンス テストとスケーリング テストに基づいています。 実際のワークロードのサイズはさらに大きくなる可能性がありますが、Azure Backup Server VM に接続されているディスクに収まる必要があります。

| 保護される最大ワークロード数 | 平均ワークロード サイズ | 平均ワークロード チャーン (日単位) | 最大ストレージ IOPS | 推奨されるディスクの種類とサイズ      | 推奨される VM サイズ |
|-------------------------|-----------------------|--------------------------------|------------------|-----------------------------------|---------------------|
| 20                      | 100 GB                | 正味 5% チャーン                   | 2,000             | Standard HDD (ディスクあたり 8 TB 以上のサイズ)  | A4V2       |
| 40                      | 150 GB                | 正味 10% チャーン                  | 4,500             | Premium SSD* (ディスクあたり 1 TB 以上のサイズ) | DS3_V2     |
| 60                      | 200 GB                | 正味 10% チャーン                  | 10,500            | Premium SSD* (ディスクあたり 8 TB 以上のサイズ) | DS3_V2     |

\* 必要な IOPS を得るには、推奨される最小サイズまたはそれ以上のディスクを使用します。 小さいサイズのディスクでは、IOPS が低くなります。

> [!NOTE]
> Azure Backup Server は、単一目的の専用サーバーで動作するように設計されています。 次のようなコンピューターには Azure Backup Server をインストールできません。
> * ドメイン コントローラーとして実行されている。
> * アプリケーション サーバーの役割がインストールされている。
> * System Center Operations Manager 管理サーバーである。
> * Exchange Server が実行されている。
> * クラスターのノードである。

### <a name="disks-and-storage"></a>ディスクとストレージ

Azure Backup Server にはインストール用のディスクが必要です。 

| 要件                      | 推奨サイズ  |
|----------------------------------|-------------------------|
| Azure Backup Server のインストール                | インストール場所: 3 GB<br />データベース ファイル ドライブ: 900 MB<br />システム ドライブ: SQL Server のインストール用に 1 GB<br /><br />また、アーカイブするときに、Azure Backup Server によってファイル カタログが一時的なインストール場所にコピーされるための領域が必要になります。      |
| 記憶域プール用のディスク<br />(ベーシック ボリュームを使用、ダイナミック ディスクは使用不可) | 保護対象データのサイズの 2 から 3 倍。<br />記憶域を詳細に計算するには、[DPM Capacity Planner](https://www.microsoft.com/download/details.aspx?id=54301) を使用してください。   |

新しいマネージド データ ディスクを既存の Azure VM に接続する方法については、「[Azure portal を使用して Windows VM にマネージド データ ディスクを接続する](../virtual-machines/windows/attach-managed-disk-portal.md)」を参照してください。

> [!NOTE]
> 記憶域プールには、1 つの Azure Backup Server に対して 120 TB のソフト制限があります。

### <a name="store-backup-data-on-local-disk-and-in-azure"></a>ローカル ディスクと Azure にバックアップ データを格納する

バックアップ データを Azure に格納すると、Azure Backup Server VM 上のバックアップ インフラストラクチャが減ります。 運用復旧 (バックアップ) の場合、Azure Backup Server では VM に接続された Azure ディスクにバックアップ データが格納されます。 ディスクと記憶域スペースが VM に接続された後は、記憶域は Azure Backup Server によって自動的に管理されます。 ストレージのサイズは、各 Azure VM に接続されているディスクの数とサイズによって変わります。 Azure VM の各サイズには、接続できるディスクの最大数があります。 たとえば、A2 は 4 ディスク、A3 は 8 ディスク、A4 は 16 ディスクです。 ここでも、ディスクのサイズと数によってバックアップ記憶域プールの合計容量が決まります。

> [!IMPORTANT]
> Azure Backup Server に接続されたディスク上に、運用復旧データを 5 日より長く保持しては "*いけません*"。 5 日より長く経過したデータは、Recovery Services コンテナーに格納します。

Azure にバックアップ データを保存するには、Recovery Services コンテナーを作成または使用します。 Azure Backup Server ワークロードのバックアップを準備するときに、[Recovery Services コンテナーを構成](#create-a-recovery-services-vault)します。 構成が済むと、オンライン バックアップ ジョブが実行されるたびに、コンテナーに復旧ポイントが作成されます。 各 Recovery Services コンテナーには、最大 9,999 個の復旧ポイントが保持されます。 作成された復旧ポイントの数と保持期間によっては、バックアップ データを何年も保持できます。 たとえば、毎月の復旧ポイントを作成して 5 年間保持することができます。

> [!IMPORTANT]
> バックアップ データを Azure に送信するか、またはローカルに保持するかにかかわらず、Azure Backup Server を Recovery Services コンテナーに登録する必要があります。

### <a name="scale-deployment"></a>デプロイをスケーリングする

展開をスケーリングするには、次の選択肢があります。

- **スケールアップ**:Azure Backup Server VM のサイズを A シリーズから DS3 シリーズに増やし、ローカル ストレージを増やします。
- **データのオフロード**: 古いデータを Azure に送信し、Azure Backup Server マシンに接続されたストレージに最新のデータのみを保持します。
- **スケールアウト**:Azure Backup Server マシンをさらに追加してワークロードを保護します。

### <a name="net-framework"></a>.NET Framework

VM には、.NET Framework 3.5 SP1 以降をインストールする必要があります。

### <a name="join-a-domain"></a>ドメインに参加する

Azure Backup Server VM はドメインに参加している必要があります。 管理者特権を持つドメイン ユーザーは、VM 上に Azure Backup Server をインストールする必要があります。

Azure VM にデプロイされた Azure Backup Server を使用して、Azure VMware Solution の VM 上のワークロードをバックアップすることができます。 バックアップ操作を有効にするには、ワークロードが同じドメイン内にある必要があります。

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

Recovery Services コンテナーは、経時的に作成された復旧ポイントを格納するストレージ エンティティです。 また、ここには保護項目に関連付けられているバックアップ ポリシーも格納されます。

1. [Azure Portal](https://portal.azure.com/) でサブスクリプションにサインインします。

1. 左側のメニューから、 **[すべてのサービス]** を選択します。

   ![左側のメニューから、 [すべてのサービス] を選択します。](../backup/media/backup-create-rs-vault/click-all-services.png)

1. **[すべてのサービス]** ダイアログ ボックスで「**Recovery Services**」と入力し、一覧から **Recovery Services コンテナー** を選択します。

   ![[Recovery Services コンテナー] と入力して選択します。](../backup/media/backup-create-rs-vault/all-services.png)

   サブスクリプションに Recovery Services コンテナーの一覧が表示されます。

1. **[Recovery Services コンテナー]** ダッシュボードで **[追加]** を選択します。

   ![Recovery Services コンテナーを追加します。](../backup/media/backup-create-rs-vault/add-button-create-vault.png)

   **[Recovery Services コンテナー]** ダイアログ ボックスが開きます。

1. **[名前]** 、 **[サブスクリプション]** 、 **[リソース グループ]** 、および **[場所]** の値を入力します。

   ![Recovery Services コンテナーを構成します。](../backup/media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Name**:コンテナーを識別するフレンドリ名を入力します。 名前は Azure サブスクリプションに対して一意である必要があります。 2 文字以上で、50 文字以下の名前を指定します。 名前の先頭にはアルファベットを使用する必要があります。また、名前に使用できるのはアルファベット、数字、ハイフンのみです。
   - **サブスクリプション**:使用するサブスクリプションを選択します。 1 つのサブスクリプションのみのメンバーの場合は、その名前が表示されます。 どのサブスクリプションを使用すればよいかがわからない場合は、既定 (推奨) のサブスクリプションを使用してください。 職場または学校アカウントが複数の Azure サブスクリプションに関連付けられている場合に限り、複数の選択肢が存在します。
   - **[リソース グループ]** :既存のリソース グループを使用するか、新しいリソース グループを作成します。 サブスクリプションの使用可能なリソース グループの一覧を表示するには、 **[既存のものを使用]** を選択し、ドロップダウン リストからリソースを選択します。 新しいリソース グループを作成するには、 **[新規作成]** を選択し、名前を入力します。
   - **[場所]** :コンテナーの地理的リージョンを選択します。 Azure VMware Solution の仮想マシンを保護するためのコンテナーは、Azure VMware Solution のプライベート クラウドと同じリージョンに作成する "*必要があります*"。

1. Recovery Services コンテナーを作成する準備ができたら、 **[作成]** を選択します。

   ![Recovery Services コンテナーを作成します。](../backup/media/backup-create-rs-vault/click-create-button.png)

   Recovery Services コンテナーの作成に時間がかかることがあります。 ポータルの右上隅の **[通知]** 領域で、状態の通知を監視します。 コンテナーの作成後は、Recovery Services コンテナーの一覧に表示されます。 コンテナーが表示されない場合は、 **[最新の情報に更新]** を選択します。

   ![バックアップ コンテナーの一覧を最新の情報に更新します。](../backup/media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-replication"></a>ストレージ レプリケーションの設定

ストレージ レプリケーション オプションでは、geo 冗長ストレージ (規定値) とローカル冗長ストレージのどちらかを選択できます。 geo 冗長ストレージでは、ストレージ アカウント内のデータがセカンダリ リージョンにコピーされて、データが永続化されます。 ローカル冗長ストレージは、永続的ではない低コストのオプションです。 geo 冗長ストレージとローカル冗長ストレージのオプションの詳細については、「[Azure Storage の冗長性](../storage/common/storage-redundancy.md)」を参照してください。

> [!IMPORTANT]
> Recovery Services コンテナーに対する **ストレージ レプリケーションの種類のローカル冗長または geo 冗長** の設定の変更は、コンテナーでバックアップを構成する前に行う必要があります。 バックアップを構成した後では、変更オプションは無効になり、ストレージ レプリケーションの種類は変更できません。

1. **[Recovery Services コンテナー]** で、新しいコンテナーを選択します。 

1. **[設定]** で **[プロパティ]** を選択します。 **[バックアップ構成]** で **[更新]** を選択します。

1. ストレージのレプリケーションの種類を選択し、 **[保存]** を選択します。

## <a name="download-and-install-the-software-package"></a>ソフトウェア パッケージをダウンロードしてインストールする

このセクションの手順に従って、ソフトウェア パッケージをダウンロードし、展開して、インストールします。

### <a name="download-the-software-package"></a>ソフトウェア パッケージをダウンロードする

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. Recovery Services コンテナーが既に開かれている場合は、次のステップに進みます。 Recovery Services コンテナーを開いておらず、Azure portal を表示している場合は、メイン メニューの **[参照]** を選択します。

   1. リソースの一覧で、「**Recovery Services**」と入力します。

   1. 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Recovery Services コンテナー]** が表示されたら、それを選択します。

   ![Recovery Services コンテナー作成ステップ 1](../backup/media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

1. Recovery Services コンテナーの一覧で、コンテナーを選択します。

   選択したコンテナーのダッシュボードが開きます。

   ![選択したコンテナーのダッシュボードが開きます。](../backup/media/backup-azure-microsoft-azure-backup/vault-dashboard.png)

   **[設定]** オプションが既定で開きます。 閉じている場合は、 **[設定]** を選択して開きます。

   ![[設定] オプションが既定で開きます。 閉じている場合は、 [設定] を選択して開きます。](../backup/media/backup-azure-microsoft-azure-backup/vault-setting.png)

1. **[バックアップ]** を選択して、**作業の開始** ウィザードを開きます。

   ![[バックアップ] を選択して、作業の開始ウィザードを開きます。](../backup/media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

1. 表示されるウィンドウで、次の操作を実行します。

   1. **[ワークロードはどこで実行されていますか?]** メニューで、 **[オンプレミス]** を選択します。

      :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-on-premises-workload.png" alt-text="ワークロードはどこで実行されていますか?":::

   1. **[何をバックアップしますか?]** メニューで、Azure Backup Server を使用して保護するワークロードを選択します。

   1. **[インフラストラクチャの準備]** を選択し、Azure Backup Server とコンテナーの資格情報をダウンロードしてインストールします。

      :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-prepare-infrastructure.png" alt-text="インフラストラクチャの準備":::

1. 表示される **[インフラストラクチャの準備]** ウィンドウで、次の操作を実行します。

   1. **[ダウンロード]** リンクを選択して、Azure Backup Server をインストールします。

   1. **[Already downloaded or using the latest Azure Backup Server installation]\(既にダウンロード済み、または最新の Azure Backup Server インストールの使用\)** を選択し、 **[ダウンロード]** を選択してコンテナーの資格情報をダウンロードします。 Azure Backup Server を Recovery Services コンテナーに登録するときに、これらの資格情報を使用します。 リンク先のダウンロード センターで、ソフトウェア パッケージをダウンロードします。

   :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-prepare-infrastructure2.png" alt-text="インフラストラクチャの準備 - Azure Backup Server":::

1. ダウンロード ページで、すべてのファイルを選択して **[次へ]** を選択します。

   > [!NOTE]
   > すべてのファイルを同じフォルダーにダウンロードする必要があります。 ファイルをまとめてダウンロードするとサイズが 3 GB を超えるため、ダウンロードが完了するまでに最大で 60 分かかることがあります。 

   ![ダウンロード ページで、すべてのファイルを選択して [次へ] を選択します。](../backup/media/backup-azure-microsoft-azure-backup/downloadcenter.png)

### <a name="extract-the-software-package"></a>ソフトウェア パッケージを抽出する

ソフトウェア パッケージを別のサーバーにダウンロードした場合は、Azure Backup Server をデプロイするために作成した VM にファイルをコピーします。

> [!WARNING]
> セットアップ ファイルを抽出するには、少なくとも 4 GB の空き領域が必要です。

1. すべてのファイルをダウンロードした後、**MicrosoftAzureBackupInstaller.exe** をダブルクリックして **Microsoft Azure Backup** セットアップ ウィザードを開き、 **[次へ]** を選択します。

1. ファイルの抽出先の場所を選択し、 **[次へ]** を選択します。

1. **[抽出]** を選択して、抽出処理を開始します。

   ![[抽出] を選択して、抽出処理を開始します。](../backup/media/backup-azure-microsoft-azure-backup/extract/03.png)

1. 抽出したら、 **[Execute setup.exe]\(setup.exe の実行\)** オプションを選択し、 **[完了]** を選択します。

> [!TIP]
> ソフトウェア パッケージを抽出したフォルダーから setup.exe ファイルを検索することもできます。

### <a name="install-the-software-package"></a>ソフトウェア パッケージをインストールする

1. セットアップ ウィンドウの **[インストール]** で、**Microsoft Azure Backup** を選択してセットアップ ウィザードを開きます。

   ![セットアップ ウィンドウの [インストール] で、Microsoft Azure Backup を選択してセットアップ ウィザードを開きます。](../backup/media/backup-azure-microsoft-azure-backup/launch-screen2.png)

1. **[ようこそ]** 画面で **[次へ]** を選択して、 **[前提条件の確認]** ページに進みます。

1. **[再確認]** を選択して、Azure Backup Server のハードウェアとソフトウェアの前提条件が満たされているかどうかを確認します。 正しく満たされている場合は、 **[次へ]** を選択します。

   ![ [再確認] を選択して、Azure Backup Server のハードウェアとソフトウェアの前提条件が満たされているかどうかを確認します。 正しく満たされている場合は、 [次へ] を選択します。](../backup/media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)

1. Azure Backup Server のインストール パッケージには、必要となる適切な SQL Server バイナリがバンドルされています。 新しい Azure Backup Server のインストールを始めるときは、 **[このセットアップを使用して、SQL Server の新しいインスタンスをインストールします]** オプションを選択し ます。 次に、 **[確認してインストール]** を選択します。

   ![Azure Backup Server のインストール パッケージには、必要となる適切な SQL Server バイナリがバンドルされています。](../backup/media/backup-azure-microsoft-azure-backup/sql/01.png)

   > [!NOTE]
   > 独自の SQL Server インスタンスを使用する場合、サポートされる SQL Server のバージョンは、SQL Server 2014 SP1 以降、2016、2017 です。 すべての SQL Server のバージョンが、Standard または Enterprise 64 ビットである必要があります。 Azure Backup Server によって使用されるインスタンスは、ローカルのみにする必要があります。リモートにすることはできません。 Azure Backup Server で既存の SQL Server インスタンスを使用する場合は、セットアップでは、SQL Server の "*名前付きインスタンス*" の使用のみがサポートされます。

   エラーが発生し、マシンの再起動を推奨された場合は、そのようにして、 **[再確認]** を選択します。 SQL Server の構成に問題がある場合は、SQL Server のガイドラインに従って SQL Server を再構成します。 その後、SQL Server の既存のインスタンスを使用して、Azure Backup Server のインストールまたはアップグレードを再試行します。

   **手動で構成**

   お使いの SQL Server インスタンスを使用するときは、sysadmin ロールに対する builtin\Administrators を master データベースの sysadmin ロールに追加します。

   **SQL Server 2017 を使用してレポート サービスを構成する**

   お使いの SQL Server 2017 インスタンスを使用するときは、SQL Server 2017 Reporting Services (SSRS) を手動で構成する必要があります。 SSRS を構成した後に、SSRS の **IsInitialized** プロパティを **True** に設定します。 **True** に設定されていると、Azure Backup Server によって SSRS は構成済みと見なされ、SSRS の構成はスキップされます。

   SSRS の構成状態を確認するには、以下を実行します。

   ```powershell
   $configset =Get-WmiObject –namespace 
   "root\Microsoft\SqlServer\ReportServer\RS_SSRS\v14\Admin" -class 
   MSReportServer_ConfigurationSetting -ComputerName localhost

   $configset.IsInitialized
   ```

   SSRS 構成では次の値を使用します。

   * **[サービス アカウント]** : **[ビルトイン アカウントを使用する]** を **[ネットワーク サービス]** にする必要があります。
   * **[Web サービスの URL]** : **[仮想ディレクトリ]** を **ReportServer_\<SQLInstanceName>** にする必要があります。
   * **データベース**: **[データベース名]** を **ReportServer$\<SQLInstanceName>** にする必要があります。
   * **[Web ポータル URL]** : **[仮想ディレクトリ]** を **Reports_\<SQLInstanceName>** にする必要があります。

   SSRS の構成について詳しくは、[こちら](/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode)をご覧ください。

   > [!NOTE]
   > Azure Backup Server のデータベースとして使用される SQL Server のライセンスは、[マイクロソフト オンライン サービス条件](https://www.microsoft.com/licensing/product-licensing/products) (OST) によって管理されます。 OST に従って、Azure Backup Server にバンドルされている SQL Server は、Azure Backup Server 用のデータベースとしてのみ使用してください。

1. インストールが正常に完了したら、 **[次へ]** を選択します。

1. Microsoft Azure Backup Server のファイルをインストールする場所を指定し、 **[次へ]** を選択します。

   > [!NOTE]
   > Azure にバックアップするにはスクラッチ場所が必要です。 スクラッチ場所が、クラウドにバックアップする予定のデータの 5% 以上であることを確認します。 ディスクを保護するために、インストールの完了後に個別のディスクを構成する必要があります。 記憶域プールの詳細については、「 [記憶域プールおよびディスク記憶域の構成](/previous-versions/system-center/system-center-2012-r2/hh758075(v=sc.12))」を参照してください。

   ![Microsoft Azure Backup Server のファイルをインストールする場所を指定し、[次へ] を選択します。](../backup/media/backup-azure-microsoft-azure-backup/space-screen.png)

1. 制限付きのローカル ユーザー アカウント用に強力なパスワードを指定し、 **[次へ]** を選択します。

   ![制限付きのローカル ユーザー アカウント用に強力なパスワードを指定し、[次へ] を選択します。](../backup/media/backup-azure-microsoft-azure-backup/security-screen.png)

1. 更新プログラムを確認するために Microsoft Update を使用するかどうかを選択して、 **[次へ]** を選択します。

   > [!NOTE]
   > Windows Update を Microsoft Update にリダイレクトすることをお勧めします。そうすれば、Windows と Azure Backup Server などの他の製品に対するセキュリティ更新プログラムと重要な更新プログラムが提供されます。

   ![更新プログラムを確認するために Microsoft Update を使用するかどうかを選択して、[次へ] を選択します。](../backup/media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

1. **[設定の概要]** を確認して、 **[インストール]** を選択します。

   インストールは段階的に実施します。 
   - 最初のフェーズでは、Microsoft Azure Recovery Services Agent をインストールします。
   - 2 つ目のフェーズでは、インターネット接続を確認します。 使用できる場合は、インストールを続行できます。 使用できない場合は、インターネットに接続するためのプロキシの詳細を指定する必要があります。 
   - 最後のフェーズでは、前提条件となるソフトウェアが確認されます。 インストールされていない場合は、Microsoft Azure Recovery Services エージェントと共に、不足しているソフトウェアがインストールされます。

1. **[参照]** を選択し、Recovery Services コンテナーにマシンを登録するためのコンテナー資格情報を検索して、 **[次へ]** を選択します。

1. Azure とユーザーの環境の間で送信されるデータを暗号化または復号化するためのパスフレーズを選択します。

   > [!TIP]
   > 自動的にパスフレーズを生成するか、最小で 16 文字の独自のパスフレーズを指定することができます。

1. パスフレーズを保存する場所を入力し、 **[次へ]** を選択してサーバーを登録します。

   > [!IMPORTANT]
   > ローカル サーバー以外の安全な場所にパスフレーズを保存します。 Azure Key Vault を使用してパスフレーズを格納するすることを強くお勧めします。

   Microsoft Azure Recovery Services エージェントのセットアップが完了すると、インストール手順は SQL Server と Azure Backup Server コンポーネントのインストールと構成に進みます。

   ![Microsoft Azure Recovery Services エージェントのセットアップが完了すると、インストール手順は SQL Server と Azure Backup Server コンポーネントのインストールと構成に進みます。](../backup/media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

1. インストール ステップが完了したら、 **[閉じる]** を選択します。

### <a name="install-update-rollup-1"></a>更新プログラム ロールアップ 1 をインストールする

ワークロードを保護するには、Azure Backup Server v3 用の更新プログラム ロールアップ 1 のインストールが必須です。  バグの修正とインストールの手順については、[サポート技術情報の記事](https://support.microsoft.com/en-us/help/4534062/)を参照してください。

## <a name="add-storage-to-azure-backup-server"></a>Azure Backup Server へのストレージの追加

Azure Backup Server v3 では、以下を提供する Modern Backup Storage がサポートされています。

-  50% のストレージ節約。
-  3 倍高速のバックアップ。
-  より効率的なストレージ。
-  ワークロード対応のストレージ。

### <a name="volumes-in-azure-backup-server"></a>Azure Backup Server のボリューム

まだ追加されていない場合は、Azure Backup Server VM に必要なストレージの容量を持つデータ ディスクを追加します。

Azure Backup Server v3 では、ストレージ ボリュームのみが受け入れられます。 ボリュームを追加すると、Azure Backup Server によってボリュームは Resilient File System (ReFS) にフォーマットされます。これは Modern Backup Storage で必要です。

### <a name="add-volumes-to-azure-backup-server-disk-storage"></a>Azure Backup Server ディスク ストレージにボリュームを追加する

1. **[管理]** ペインで、ストレージを再スキャンして、 **[追加]** を選択します。 

1. 使用可能なボリュームから選択して記憶域プールに追加します。 

1. 使用可能なボリュームを追加した後、それらを管理するのに役立つフレンドリ名を付けます。 

1. Azure Backup Server で Modern Backup Storage の利点を利用できるように、 **[OK]** を選択してこれらのボリュームを ReFS にフォーマットします。


## <a name="next-steps"></a>次のステップ

ここでは Azure VMware Solution 向けに Azure Backup Server を設定する方法について説明したので、次は以下について学習してください。

- [Azure VMware Solution VM のバックアップを構成する](backup-azure-vmware-solution-virtual-machines.md).
- [Azure Security Center 統合を使用して Azure VMware Solution VM を保護する](azure-security-integration.md)
