---
title: Azure Migrate Server Assessment でインポートされたサーバー データを使用してサーバーを評価する
description: Azure Migrate Server Assessment でインポートされたデータを使用して、Azure への移行についてオンプレミス サーバーを評価する方法について説明します。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: 40dd81b9eb7ff2b20abb1bf3ea4337b63fb2a831
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2020
ms.locfileid: "87447078"
---
# <a name="assess-servers-by-using-imported-data"></a>インポートされたデータを使用してサーバーを評価する

この記事では、オンプレミス サーバーを評価する方法について説明します。そのためには、[Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) ツールを使用し、コンマ区切り値 (CSV) 形式でサーバーのメタデータをインポートします。 この評価方法では、評価を作成するために Azure Migrate アプライアンスを設定する必要はありません。 この方法は次の場合に便利です。

- アプライアンスをデプロイする前に、クイック初期評価を作成したい。
- Azure Migrate アプライアンスを組織内に展開することはできません。
- オンプレミス サーバーへのアクセスを許可する資格情報を共有することはできません。
- セキュリティの制約があるため、アプライアンスによって収集されたデータを収集して Azure に送信することはできません。 インポートするファイルで共有するデータを制御できます。 また、データの多く (たとえば、IP アドレスの指定) は省略可能です。

## <a name="before-you-start"></a>開始する前に

次の点に注意してください。

- 1 つの CSV ファイルで最大 20,000 台のサーバーを追加できます。
- CSV を使用して Azure Migrate プロジェクトに最大 20,000 台のサーバーを追加できます。
- CSV を使用してサーバー情報を Server Assessment に複数回アップロードできます。
- アプリケーション情報を収集することは、移行についてお使いのオンプレミス環境を評価するときに役立ちます。 ただし、現在、Server Assessment では、アプリケーションレベルの評価は実行されません。また、評価の作成時にアプリケーションは考慮されません。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * Azure Migrate プロジェクトを設定する。
> * CSV ファイルにサーバー情報を入力します。
> * ファイルをインポートし、サーバー情報を Server Assessment に追加します。
> * 評価を作成して確認します。

> [!NOTE]
> チュートリアルでは、概念実証をすばやく設定できるように、シナリオの最も簡単なデプロイ パスを示します。 チュートリアルではできるだけ既定のオプションを使用しており、使用可能な設定とパスをすべて示しているわけではありません。 詳細な手順については、ハウツー ガイドを参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。

## <a name="set-azure-permissions-for-azure-migrate"></a>Azure Migrate の Azure アクセス許可を設定する

ご自分の Azure アカウントには、Azure Migrate プロジェクトを作成するためのアクセス許可が必要です。

1. Azure portal でサブスクリプションを開き、 **[アクセス制御 (IAM)]** を選択します。
2. **[アクセスの確認]** で関連するアカウントを探し、それを選択してアクセス許可を表示します。
3. **共同作成者**または**所有者**のアクセス許可を持っていることを確認します。
    - 無料の Azure アカウントを作成したばかりであれば、自分のサブスクリプションの所有者になっています。
    - サブスクリプションの所有者でない場合は、所有者と協力してロールを割り当てます。

## <a name="set-up-an-azure-migrate-project"></a>Azure Migrate プロジェクトを設定する

新しい Azure Migrate プロジェクトを設定するには:

1. Azure portal の **[すべてのサービス]** で、**Azure Migrate** を検索します。
2. **[サービス]** で **[Azure Migrate]** を選択します。
3. **[概要]** の **[サーバーの検出、評価、移行]** で、 **[サーバーの評価と移行]** を選択します。

    ![サーバーの検出と評価](./media/tutorial-assess-import/assess-migrate.png)

4. **[作業の開始]** で、 **[ツールの追加]** を選択します。
5. **[移行プロジェクト]** で、自分の Azure サブスクリプションを選択し、リソース グループがない場合は作成します。
6. **[プロジェクトの詳細]** で、プロジェクト名と、プロジェクトを作成する地理的な場所を指定します。 詳細情報:

    - [パブリック](migrate-support-matrix.md#supported-geographies-public-cloud)と [Government クラウド](migrate-support-matrix.md#supported-geographies-azure-government)でサポートされている地域を確認してください。
    - 移行を実行するときは、任意のターゲット リージョンを選択できます。

    ![Azure Migrate プロジェクトの作成](./media/tutorial-assess-import/migrate-project.png)

7. **[次へ]** を選択します。
8. **[評価ツールの選択]** で、次を選択します: **[Azure Migrate: Server Assessment]**  >  **[次へ]** 。

    ![Azure Migrate 評価を作成する](./media/tutorial-assess-import/assessment-tool.png)

9. **[移行ツールの選択]** で、 **[今は移行ツールの追加をスキップします]**  >  **[次へ]** の順に選択します。
10. **[確認 + ツールの追加]** で設定を確認し、 **[Add tools]\(ツールの追加\)** を選択します。
11. Azure Migrate プロジェクトがデプロイされるまで数分待ちます。 プロジェクトのページが表示されます。 プロジェクトが表示されない場合は、Azure Migrate ダッシュボードの **[サーバー]** からアクセスできます。

## <a name="prepare-the-csv"></a>CSV を準備する

CSV テンプレートをダウンロードし、サーバー情報を追加します。

### <a name="download-the-template"></a>テンプレートをダウンロードする

1. **移行の目標** > **サーバー** > **Azure Migrate: Server Assessment** で、**検出** を選択します。
2. **[マシンの検出]** で **[Import using CSV]\(CSV を使用してインポート\)** を選択します。
3. **[ダウンロード]** を選択して CSV テンプレートをダウンロードします。 また、[直接ダウンロードする](https://go.microsoft.com/fwlink/?linkid=2109031)こともできます。

    ![CSV テンプレートをダウンロードする](./media/tutorial-assess-import/download-template.png)

### <a name="add-server-information"></a>サーバー情報を追加する

サーバー データを収集し、CSV ファイルに追加します。

- データを収集するには、VMware vSphere や構成管理データベース (CMDB) など、オンプレミスのサーバー管理に使用するツールからデータをエクスポートします。
- サンプル データを確認するには、[サンプル ファイル](https://go.microsoft.com/fwlink/?linkid=2108405)をダウンロードします。

次の表は、入力するファイル フィールドをまとめたものです。

**フィールド名** | **必須** | **詳細**
--- | --- | ---
**サーバー名** | はい | 完全修飾ドメイン名 (FQDN) を指定することをお勧めします。
**IP アドレス (IP address)** | いいえ | サーバー アドレス。
**コア** | はい | サーバーに割り当てられているプロセッサ コアの数。
**[メモリ]** | はい | サーバーに割り当てられている合計 RAM (MB 単位)。
**OS 名** | はい | サーバーのオペレーティング システム。 <br/> この[一覧](#supported-operating-system-names)内の名前と一致するか、この名前を含むオペレーティング システム名は、評価によって認識されます。
**OS バージョン** | いいえ | サーバーのオペレーティング システムのバージョン。
**OS アーキテクチャ** | いいえ | サーバー OS アーキテクチャ <br/> 有効な値: x64、x86、amd64、32 ビット、64 ビット
**ディスクの数** | いいえ | 個々のディスクの詳細が指定されている場合は必要ありません。
**Disk 1 size (ディスク 1 のサイズ)**  | いいえ | ディスクの最大サイズ (GB 単位)。<br/>テンプレートに[列を追加](#add-multiple-disks)することで、さらにディスクの詳細を追加できます。 最大 8 つのディスクを追加できます。
**Disk 1 read ops (ディスク 1 の読み取り操作)** | いいえ | 1 秒あたりのディスク読み取り操作。
**Disk 1 write ops (ディスク 1 の書き込み操作)** | いいえ | 1 秒あたりのディスク書き込み操作。
**Disk 1 read throughput (ディスク 1 の読み取りスループット)** | いいえ | 1 秒あたりにディスクから読み取られたデータ (MB/ 秒)。
**Disk 1 write throughput (ディスク 1 の書き込みスループット)** | いいえ | 1 秒あたりにディスクに書き込まれたデータ (MB/ 秒)。
**CPU utilization percentage (CPU 使用率)** | いいえ | 使用されている CPU の割合。
**Memory utilization percentage (メモリ使用率)** | いいえ | 使用されている RAM の割合。
**Total disks read ops (ディスク読み取り操作の合計)** | いいえ | 1 秒あたりのディスク読み取り操作。
**Total disks write ops (ディスク書き込み操作の合計)** | いいえ | 1 秒あたりのディスク書き込み操作。
**Total disks read throughput (ディスク読み取りスループットの合計)** | いいえ | ディスクから読み取られたデータ (MB/ 秒)。
**Total disks write throughput (ディスク書き込みスループットの合計)** | いいえ | ディスクに書き込まれたデータ (MB/ 秒)。
**Network In throughput (ネットワークの受信スループット)** | いいえ | サーバーによって受信されたデータ (MB/秒)。
**Network out throughput (ネットワークの送信スループット)** | いいえ | サーバーによって送信されたデータ (MB/秒)。
**Firmware type (ファームウェアの種類)** | いいえ | サーバーのファームウェア。 値には "BIOS" または "UEFI" を指定できます。
**MAC アドレス**| いいえ | サーバーの MAC アドレス。


### <a name="add-operating-systems"></a>オペレーティング システムを追加する

評価では特定のオペレーティング システム名が認識されます。 指定する名前は、[サポートされている名前の一覧](#supported-operating-system-names)内のいずれかの文字列と正確に一致している必要があります。

### <a name="add-multiple-disks"></a>複数のディスクを追加する

このテンプレートには、最初のディスクの既定フィールドが用意されています。 最大 8 台のディスクに対して同様の列を追加できます。

たとえば、2 番目のディスクに対してすべてのフィールドを指定するには、次の列を追加します。

- Disk 2 size (ディスク 2 のサイズ)
- Disk 2 read ops (ディスク 2 の読み取り操作)
- Disk 2 write ops (ディスク 2 の書き込み操作)
- Disk 2 read throughput (ディスク 2 の読み取りスループット)
- Disk 2 write throughput (ディスク 2 の書き込みスループット)


## <a name="import-the-server-information"></a>サーバー情報をインポートする

CSV テンプレートに情報を追加したら、Server Assessment にサーバーをインポートします。

1. Azure Migrate の **[マシンの検出]** で、入力したテンプレートに移動します。
2. **[インポート]** を選択します。
3. インポートの状態が表示されます。
    - 状態に警告が表示される場合は、それらを修正するか、対処せずに続行することができます。
    - 評価の精度を向上させるには、警告内の提案に従ってサーバー情報を修正します。
    - 警告を確認して修正を行うには、 **[Download warning details .CSV]\(警告の詳細の .CSV のダウンロード\)** を選択します。 この操作により、警告が含まれた CSV がダウンロードされます。 警告を確認し、必要に応じて問題を修正します。
    - 状態にエラーが表示される場合 (つまり、インポートの状態が **[失敗]** の場合)、インポートを続行する前にこれらのエラーを修正する必要があります。
        1. エラーの詳細を含む CSV をダウンロードします。
        1. 必要に応じて、エラーを確認して対処します。 
        1. 変更したファイルを再度アップロードします。
4. インポートの状態が **[完了]** の場合は、サーバーの情報がインポートされています。

## <a name="update-server-information"></a>サーバー情報を更新する

サーバーの情報を更新するには、同じ**サーバー名**を使用してサーバーのデータをもう一度インポートします。 **[サーバー名]** フィールドを変更することはできません。 サーバーの削除は現在サポートされていません。

## <a name="verify-servers-in-the-portal"></a>ポータルでサーバーを確認する

検出後にサーバーが Azure portal に表示されることを確認するには:

1. Azure Migrate ダッシュボードを開きます。
2. **[Azure Migrate - サーバー]**  >  **[Azure Migrate: Server Assessment]** ページで、**検出済みサーバー**の数を表示するアイコンを選択します。
3. **[Import based]\(インポート ベース\)** タブを選択します。

## <a name="set-up-and-run-an-assessment"></a>評価を設定および実行する

Server Assessment を使用して、次の 2 種類の評価を作成できます。


**評価の種類** | **詳細**
--- | --- 
**Azure VM** | オンプレミスのサーバーを Azure 仮想マシンに移行するための評価。 <br/><br/> このタイプの評価を使用すると、Azure への移行について、オンプレミスの [VMware VM](how-to-set-up-appliance-vmware.md)、[Hyper-V VM](how-to-set-up-appliance-hyper-v.md)、[物理サーバー](how-to-set-up-appliance-physical.md)を評価できます。
**Azure VMware Solution (AVS)** | オンプレミスのサーバーを [Azure VMware Solution (AVS)](../azure-vmware/introduction.md) に移行するための評価。 <br/><br/> このタイプの評価を使用すると、Azure VMware Solution (AVS) への移行について、オンプレミスの [VMware VM](how-to-set-up-appliance-vmware.md) を評価できます。[詳細情報](concepts-azure-vmware-solution-assessment-calculation.md)

### <a name="sizing-criteria"></a>サイズ変更の設定基準

Server Assessment には、サイズ変更の設定基準として、次の 2 つのオプションが用意されています。

**サイズ変更の設定基準** | **詳細** | **データ**
--- | --- | ---
**パフォーマンスベース** | 収集されたパフォーマンス データに基づいて推奨を行う評価 | **Azure VM の評価**: VM サイズの推奨値は、CPU とメモリの使用率データに基づきます。<br/><br/> ディスクの種類に関する推奨事項 (標準 HDD/SSD またはプレミアム マネージド ディスク) は、オンプレミス ディスクの IOPS とスループットに基づきます。<br/><br/> **Azure VMware Solution (AVS) の評価**: AVS ノードの推奨値は、CPU とメモリの使用率データに基づきます。
**現状のオンプレミス** | パフォーマンス データを使用せずに推奨を行う評価。 | **Azure VM の評価**: VM サイズに関する推奨事項は、オンプレミスの VM サイズに基づきます<br/><br> 推奨されるディスクの種類は、評価の [ストレージの種類] 設定で選択した内容に基づきます。<br/><br/> **Azure VMware Solution (AVS) の評価**: AVS ノードに関する推奨事項は、オンプレミスの VM サイズに基づきます。


評価を実行するには:

1. 評価を作成するための[ベスト プラクティス](best-practices-assessment.md)を確認します。
2. **[サーバー]** タブの **[Azure Migrate: Server Assessment]** タイルで、 **[評価]** を選択します。

    ![アクセス](./media/tutorial-assess-physical/assess.png)

3. **[サーバーの評価]** で評価の名前を指定し、**評価**の種類として、 *[Azure VM]* (Azure VM の評価を実行する場合) または *[Azure VMware Solution (AVS)]* (AVS の評価を実行する場合) を選択します。

    ![評価の基本](./media/how-to-create-assessment/assess-servers-azurevm.png)

4. **[Discovery source]\(検出ソース\)** で、 **[Machines added via import to Azure Migrate]\(インポートで Azure Migrate に追加されたマシン\)** を選択します。

5. **[すべて表示]** を選択して、評価のプロパティを確認します。

    ![評価のプロパティ](./media/tutorial-assess-physical/view-all.png)

6. **[次へ]** をクリックして**評価するマシンを選択**します。 **[グループを選択または作成します]** で **[新規作成]** を選択し、グループ名を指定します。 グループで、評価のために 1 つ以上の VM をまとめます。
7. **[グループにマシンを追加します]** で、グループに追加するサーバーを選択します。
8. **[次へ]** をクリックして **[評価の確認と作成]** を選択し、評価の詳細を確認します。
9. **[評価の作成]** をクリックしてグループを作成し、評価を実行します。

    ![評価を作成する](./media/tutorial-assess-physical/assessment-create.png)

9. 評価が作成されたら、それを表示します ( **[サーバー]**  >  **[Azure Migrate: Server Assessment]**  >  **[評価]** )。
10. **[評価のエクスポート]** を選択し、Microsoft Excel ファイルとしてダウンロードします。

**Azure VMware Solution (AVS)** の評価について詳しくは、[こちら](how-to-create-azure-vmware-solution-assessment.md)を参照してください。 

## <a name="review-an-azure-vm-assessment"></a>Azure VM の評価を確認する

Azure VM の評価で評される内容は次のとおりです。

- **Azure 対応性**: サーバーが Azure への移行に適しているかどうか。
- **月間コスト見積もり**: Azure でサーバーを実行するためのコンピューティングとストレージの月間推定コスト。
- **月間ストレージ コスト見積もり**: 移行後のディスク ストレージの推定コスト。

### <a name="view-an-assessment"></a>評価を表示する

1. **[移行の目標]**  >  **[サーバー]** で、 **[評価]** を **[Azure Migrate: Server Assessment]** でクリックします。
2. **[評価]** で、評価を選択して開きます。

    ![評価の概要](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Azure 対応性の確認

1. **[Azure 対応性]** で、サーバーが Azure への移行の準備ができているかどうかを判断します。
2. 状態を確認します。
    - **[Azure に対応]** : Azure Migrate によって、評価内の VM の VM サイズとコスト見積もりが推奨されます。
    - **[条件付きで対応]** : 問題と推奨される修復方法が示されます。
    - **[Azure に未対応]** : 問題と推奨される修復方法が示されます。
    - **[Readiness 不明]** : データの可用性の問題により、Azure Migrate で対応性を評価できません。

3. **[Azure 対応性]** の状態を選択します。 サーバー対応性の詳細を表示し、ドリルダウンしてサーバーの詳細 (コンピューティング、ストレージ、ネットワークの設定など) を表示できます。

### <a name="review-cost-details"></a>コスト詳細の確認

このビューには、Azure で VM を実行する際のコンピューティングおよびストレージの推定コストが表示されます。 次のようにすることができます。

- 月間のコンピューティングおよびストレージのコストを確認します。 評価対象のグループ内のすべてのサーバーのコストが集計されます。

    - コストの見積もりは、マシンの推奨サイズ、およびそのディスクとプロパティに基づいています。
    - コンピューティングとストレージの月間推定コストが表示されます。
    - コスト見積もりは、オンプレミスのサーバーをサービスとしてのインフラストラクチャ (IaaS) VM として実行するためのものです。 Server Assessment では、サービスとしてのプラットフォーム (PaaS) のコストも、サービスとしてのソフトウェア (SaaS) のコストも考慮されません。

- 月間ストレージ コストの見積もりを確認します。 このビューには、評価されたグループの集計されたストレージ コストが、ストレージ ディスクの種類ごとに分けて表示されます。
- ドリルダウンして、特定の VM の詳細を表示します。

> [!NOTE]
> CSV を使用して Server Assessment にインポートされたサーバーの評価には、信頼度の評価は割り当てられません。

## <a name="supported-operating-system-names"></a>サポートされるオペレーティング システム名

CSV で指定するオペレーティング システムの名前は、この一覧に記載されている名前と一致しているか、それらを含んでいる必要があります。 このことは、指定した名前が評価で有効と認識されるための要件です。

<!-- BEGIN A - H -->

:::row:::
   :::column span="2":::
      **A - H**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Apple Mac OS X 10
   :::column-end:::
   :::column span="":::
      Asianux 3<br/>
      Asianux 4<br/>
      Asianux 5
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      CentOS<br/>
      CentOS 4/5
   :::column-end:::
   :::column span="":::
      CoreOS Linux
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Debian GNU/Linux 4<br/>
      Debian GNU/Linux 5<br/>
      Debian GNU/Linux 6<br/>
      Debian GNU/Linux 7<br/>
      Debian GNU/Linux 8
   :::column-end:::
   :::column span="":::
      FreeBSD
   :::column-end:::
:::row-end:::

<!-- BEGIN I - R -->

:::row:::
   :::column span="2":::
      **I - R**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      IBM OS/2
   :::column-end:::
   :::column span="":::
      MS-DOS
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Novell NetWare 5<br/>
      Novell NetWare 6
   :::column-end:::
   :::column span="":::
      Oracle Linux<br/>
       Oracle Linux 4/5<br/>
      Oracle Solaris 10<br/>
       Oracle Solaris 11
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Red Hat Enterprise Linux 2<br/>
      Red Hat Enterprise Linux 3<br/>
      Red Hat Enterprise Linux 4<br/>
      Red Hat Enterprise Linux 5<br/>
      Red Hat Enterprise Linux 6<br/>
      Red Hat Enterprise Linux 7<br/>
      Red Hat Fedora
   :::column-end:::
:::row-end:::

<!-- BEGIN S - T -->

:::row:::
   :::column span="2":::
      **S - T**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      SCO OpenServer 5<br/>
      SCO OpenServer 6<br/>
      SCO UnixWare 7
   :::column-end:::
   :::column span="":::
      Serenity Systems eComStation 1<br/>
      Serenity Systems eComStation 2
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Sun Microsystems Solaris 8<br/>
      Sun Microsystems Solaris 9
   :::column-end:::
   :::column span="":::
      SUSE Linux Enterprise 10<br/>
      SUSE Linux Enterprise 11<br/>
      SUSE Linux Enterprise 12<br/>
      SUSE Linux Enterprise 8/9<br/>
      SUSE Linux Enterprise 11<br/>
      SUSE openSUSE
   :::column-end:::
:::row-end:::

<!-- BEGIN U - Z -->
:::row:::
   :::column span="2":::
      **U - Z**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Ubuntu Linux
   :::column-end:::
   :::column span="":::
      VMware ESXi 4<br/>
      VMware ESXi 5<br/>
      VMware ESXi 6
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Windows 10<br/>
      Windows 2000<br/>
      Windows 3<br/>
      Windows 7<br/>
      Windows 8<br/>
      Windows 95<br/>
      Windows 98<br/>
      Windows NT<br/>
      Windows Server (R) 2008<br/>
      Windows Server 2003
   :::column-end:::
   :::column span="":::
      Windows Server 2008<br/>
      Windows Server 2008 R2<br/>
      Windows Server 2012<br/>
      Windows Server 2012 R2<br/>
      Windows Server 2016<br/>
      Windows Server 2019<br/>
      Windows Server Threshold<br/>
      Windows Vista<br/>
      Windows Web Server 2008 R2<br/>
      Windows XP Professional
   :::column-end:::
:::row-end:::

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次のことを行いました。

> [!div class="checklist"]
> * Azure Migrate: Server Assessment に、CSV を使用してサーバー情報をインポートしました。
> * 評価を作成して確認しました。

次は、より正確な評価のために[アプライアンスをデプロイ](./migrate-appliance.md)し、[依存関係分析](./concepts-dependency-visualization.md)を使用してより深い評価のためにサーバーをグループにまとめます。
