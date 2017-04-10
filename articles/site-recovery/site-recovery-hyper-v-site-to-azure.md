---
title: "Hyper-V VM を Azure にレプリケートする | Microsoft Docs"
description: "オンプレミス Hyper-V VM の Azure へのレプリケーション、フェールオーバー、および復旧を調整する方法を説明します"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 1777e0eb-accb-42b5-a747-11272e131a52
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 03/05/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 9da7a59ca5d544121dc9c540a25a3b975988e9a1
ms.lasthandoff: 03/21/2017

---

# <a name="replicate-hyper-v-virtual-machines-without-vmm-to-azure-using-azure-site-recovery-with-the-azure-portal"></a>Azure Portal で Azure Site Recovery を使用して、(VMM なしで) Hyper-V 仮想マシンを Azure にレプリケートする
> [!div class="op_single_selector"]
> * [Azure ポータル](site-recovery-hyper-v-site-to-azure.md)
> * [Azure クラシック](site-recovery-hyper-v-site-to-azure-classic.md)
> * [PowerShell - Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md)
>
>

この記事では、オンプレミスの Hyper-V 仮想マシンを、Azure Portal の [Azure Site Recovery](site-recovery-overview.md) を使用して Azure にレプリケートする方法について説明します。

プライマリ サイトが使用できなくなった場合は、HYPER-V VM を Azure ストレージにレプリケートし、VM を Azure にフェールオーバーします。 Azure でワークロードにアクセスでき、通常の動作に戻った時点でオンプレミスにフェールバックすることができます。 この記事の手順に従って、VM を Azure に移行することもできます。 移行シナリオでは、VM をレプリケートしてフェールオーバーしますが、フェールバックは行いません。

コメントはこの記事の末尾に投稿し、技術的な質問は [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。


## <a name="scenario-architecture"></a>シナリオのアーキテクチャ
シナリオの構成要素を次に示します。

* **Hyper-V ホストまたはクラスター**: オンプレミスの Hyper-V ホスト サーバーまたはクラスター。 保護対象の VM を実行している Hyper-V ホストは、Site Recovery のデプロイ時に Hyper-V 論理サイトに収集されます。
* **Azure Site Recovery Provider と Recovery Services エージェント**: デプロイ時に、Azure Site Recovery Provider と Microsoft Azure Recovery Services エージェントを Hyper-V ホスト サーバーにインストールします。 プロバイダーは、HTTPS 443 経由で Azure Site Recovery と通信して、オーケストレーションをレプリケートします。 Hyper-V ホスト サーバー上のエージェントは、既定では HTTPS 443 経由で Azure Storage にデータをレプリケートします。
* **Azure**: Azure サブスクリプション、レプリケートされたデータを格納するための Azure ストレージ アカウント、および Azure VM がフェールオーバー後にネットワークに接続されように Azure 仮想ネットワークが必要です。

![Hyper-V サイト アーキテクチャ](./media/site-recovery-hyper-v-site-to-azure/architecture.png)

## <a name="azure-prerequisites"></a>Azure の前提条件


| **前提条件** | **詳細** |
| --- | --- |
| **Azure アカウント** | [Microsoft Azure](http://azure.microsoft.com/) アカウント。 アカウントがなくても、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)を使用できます。 [こちら](https://azure.microsoft.com/pricing/details/site-recovery/) をご覧ください。 |
| **Azure Storage** | Standard ストレージ アカウント。 LRS または GRS ストレージ アカウントを使用できます。 地域的障害が発生した場合やプライマリ リージョンが復旧できない場合にデータの復元性を確保できるように、GRS をお勧めします。 [詳細情報](../storage/storage-redundancy.md)。<br/><br/> アカウントは、Recovery Services コンテナーと同じリージョンにある必要があります。<br/><br/> Premium Storage はサポートされていません。<br/><br/> レプリケートされたデータは Azure Storage に格納され、フェールオーバーが発生すると Azure VM が作成されます。<br/><br/> Azure Storage については、[こちら](../storage/storage-introduction.md)をご覧ください。 |
| **Azure ネットワーク** |フェールオーバーが発生した場合に Azure VM が接続する Azure 仮想ネットワークが必要です。 Azure 仮想ネットワークは、Recovery Services コンテナーと同じリージョンに存在する必要があります。 |

## <a name="on-premises-prerequisites"></a>オンプレミスの前提条件
オンプレミスで必要となるものを次に示します。

| **前提条件** | **詳細** |
| --- | --- |
| **Hyper-V** |最新の更新プログラムと Hyper-V ロールが有効になっているか、**Microsoft Hyper-V Server 2012 R2** を含む **Windows Server 2012 R2** を実行する 1 つ以上のオンプレミスのサーバー。<br/><br/>Hyper-V サーバーには、1 つ以上の仮想マシンが搭載されます。<br/><br/>Hyper-V サーバーは、直接、またはプロキシを経由して、インターネットに接続します。<br/><br/>Hyper-V サーバーには [KB2961977](https://support.microsoft.com/en-us/kb/2961977 "KB2961977") で説明されている修正プログラムをインストールする必要があります。 |
| **プロバイダーとエージェント** |Azure Site Recovery のデプロイ時に、Azure Site Recovery Provider をインストールします。 保護対象の仮想マシンを実行している各 Hyper-V サーバーにプロバイダーをインストールすると、Azure Recovery Services エージェントもインストールされます。 Site Recovery コンテナー内のすべての Hyper-V サーバーで、プロバイダーとエージェントが同じバージョンである必要があります。<br/><br/>プロバイダーは、インターネット経由で Azure Site Recovery に接続する必要があります。 トラフィックを直接送信することも、プロキシ経由で送信することもできます。 HTTPS ベースのプロキシはサポートされていないことに注意してください。 プロキシ サーバーでは、次の URL へのアクセスを許可する必要があります: <br/><br/> ``*.accesscontrol.windows.net``<br/><br/> ``*.backup.windowsazure.com``<br/><br/> ``*.hypervrecoverymanager.windowsazure.com`` <br/><br/> ``*store.core.windows.net``<br/><br/> ``*.blob.core.windows.net``<br/><br/> ``https://www.msftncsi.com/ncsi.txt``<br/><br/> ``time.windows.com``<br/><br/> ``time.nist.gov``<br/><br/> サーバーに IP アドレスベースのファイアウォール規則がある場合、規則で Azure との通信を許可していることを確認します。<br/><br/> [Azure データセンターの IP の範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)と HTTPS (443) ポートを許可します。<br/><br/> ご利用のサブスクリプションの Azure リージョンと米国西部の IP アドレス範囲を許可します。 |

## <a name="virtual-machine-prerequisites"></a>仮想マシンの前提条件
| **前提条件** | **詳細** |
| --- | --- |
| **保護対象の VM** |VM をフェールオーバーする前に、Azure VM に割り当てられる名前が [Azure の前提条件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)に準拠していることを確認する必要があります。 名前は、VM のレプリケーションを有効にした後で変更できます。<br/><br/> 保護対象のマシン上の個々のディスク容量が 1023 GB 以下である必要があります。 VM は最大 64 個のディスク (従って最大 64 TB) に対応できます。<br/><br/> 共有ディスク ゲスト クラスターはサポートされていません。<br/><br/> ソース VM に NIC チーミングがある場合、Azure へのフェールオーバー後に単一の NIC に変換されます。<br/><br/>静的 IP アドレスを持ち、Linux を実行している VM は保護できません。 |

## <a name="prepare-for-deployment"></a>デプロイの準備をする
デプロイを準備するには、次の手順に従います。

1. [Azure ネットワークをセットアップ](#set-up-an-azure-network) します。
2. [Azure ストレージ アカウントを設定](#set-up-an-azure-storage-account) します。
3. [Hyper-V ホストを準備](#prepare-the-hyper-v-hosts) します。

### <a name="set-up-an-azure-network"></a>Azure ネットワークをセットアップ

Azure ネットワークをセットアップします。 これは、フェールオーバー後に作成された Azure VM がネットワークに接続するために必要となります。

* このネットワークは、Recovery Services コンテナーをデプロイするリージョンと同じリージョンである必要があります。
* フェールオーバーされた Azure VM に使用するリソース モデルに応じて、Azure ネットワークを [Resource Manager モード](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)または[クラシック モード](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)でセットアップします。
* ネットワークをセットアップしてから、以下の作業を開始することをお勧めします。 行わない場合は、Site Recovery のデプロイ中に行う必要があります。

> [!NOTE]
> Site Recovery のデプロイ用のネットワークでは、同じサブスクリプション内のリソース グループ間またはサブスクリプション間での[ネットワークの移行](../azure-resource-manager/resource-group-move-resources.md)はサポートされていません。
>
>

### <a name="set-up-an-azure-storage-account"></a>Azure Storage アカウントを設定

- Azure にレプリケートされたデータを保持するために Standard Azure ストレージ アカウントが必要になります。
- フェールオーバーされた Azure VM に使用するリソース モデルに応じて、アカウントを [Resource Manager モード](../storage/storage-create-storage-account.md)または[クラシック モード](../storage/storage-create-storage-account-classic-portal.md)でセットアップします。
- ストレージ アカウントをセットアップしてから、以下の作業を開始することをお勧めします。 行わない場合は、Site Recovery のデプロイ中に行う必要があります。 アカウントは、Recovery Services コンテナーと同じリージョンに存在する必要があります。
- 同じサブスクリプション内のリソース グループ間、または異なるサブスクリプション間で、Site Recovery で使用されるストレージ アカウントを移動することはできません。


### <a name="prepare-the-hyper-v-hosts"></a>Hyper-V ホストを準備
* Hyper-V ホストが [前提条件](#on-premises-prerequisites)に準拠していることを確認します。

### <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する
1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **[新規]** > **[管理]** > **[Backup and Site Recovery (OMS) (バックアップと Site Recovery (OMS))]** の順にクリックします。 または、**[参照]** > **[Recovery Services** コンテナー] > **[追加]** の順にクリックします。

    ![新しいコンテナー](./media/site-recovery-hyper-v-site-to-azure/new-vault3.png)
3. **[名前]** に、コンテナーを識別するフレンドリ名を入力します。 複数のサブスクリプションがある場合は、いずれかを選択します。
4. [新しいリソース グループを作成](../azure-resource-manager/resource-group-template-deploy-portal.md) するか、既存のリソース グループを選択し、Azure リージョンを指定します。 マシンは、このリージョンにレプリケートされます。 サポートされているリージョンを確認するには、「 [Azure Site Recovery Pricing Details (Azure Site Recovery の価格の詳細)](https://azure.microsoft.com/pricing/details/site-recovery/)
5. ダッシュボードからコンテナーにすばやくアクセスするには、**[ダッシュボードにピン留めする]** をクリックし、**[コンテナーの作成]** をクリックします。

    ![新しいコンテナー](./media/site-recovery-hyper-v-site-to-azure/new-vault-settings.png)

新しいコンテナーは、**[ダッシュボード]** > **[すべてのリソース]** と、メインの **[Recovery Services コンテナー]** ブレードに表示されます。

## <a name="get-started"></a>作業開始
Site Recovery に用意されている [使用の開始] エクスペリエンスを利用すると、最小限の時間でデプロイできます。 [作業の開始] によって前提条件が確認され、Site Recovery のデプロイ手順が適切な順序で説明されます。

[作業の開始] では、レプリケートするマシンの種類とレプリケート先の場所を選択します。 また、オンプレミス サーバー、Azure ストレージ アカウント、およびネットワークをセットアップします。 さらに、レプリケーション ポリシーを作成し、キャパシティ プランニングを実施します。 インフラストラクチャのセットアップが完了したら、VM のレプリケーションを有効にします。 特定のマシンのフェールオーバーを実行することも、複数のマシンをフェールオーバーする復旧計画を作成することもできます。

[作業の開始] では、まず、Site Recovery をデプロイする方法を選択します。 作業の開始フローは、レプリケーションの要件によって多少変化します。

## <a name="step-1-choose-your-protection-goals"></a>ステップ 1: 保護の目標を選択する
レプリケートの対象とレプリケート先を選択します。

1. **[Recovery Services コンテナー]** で、コンテナーを選びます。
2. **[作業の開始]** で、**[Site Recovery]** > **[手順 1: インフラストラクチャを準備する]** > **[保護の目標]** の順にクリックします。

    ![Choose goals](./media/site-recovery-hyper-v-site-to-azure/choose-goals.png)
3. **[保護の目標]** で、**[To Azure (Azure へ)]** を選択し、**[Yes, with Hyper-V (はい、Hyper-V を使用する)]** を選択します。 VMM を使用していないことを確認するために、 **[いいえ]** を選択します。 次に、 **[OK]**をクリックします

    ![Choose goals](./media/site-recovery-hyper-v-site-to-azure/choose-goals2.png)

## <a name="step-2-set-up-the-source-environment"></a>ステップ 2: ソース環境をセットアップする
Hyper-V サイトをセットアップし、Azure Site Recovery Provider と Azure Recovery Services エージェントを Hyper-V ホストにインストールして、ホストをコンテナーに登録します。

1. **[手順 2: インフラストラクチャを準備する]** > **[ソース]** の順にクリックします。 Hyper-V ホストまたはクラスターのコンテナーとして、新しい Hyper-V サイトを追加するには、 **[+ Hyper-V サイト]**をクリックします。

    ![Set up source](./media/site-recovery-hyper-v-site-to-azure/set-source1.png)
2. **[Hyper-V サイトの作成]** ブレードで、サイトの名前を指定します。 次に、 **[OK]**をクリックします 作成したサイトを選択します。

    ![Set up source](./media/site-recovery-hyper-v-site-to-azure/set-source2.png)
3. **[+ Hyper-V Server]** をクリックして、サーバーをサイトに追加します。
4. **[サーバーの追加]**  >  **[サーバーの種類]** で、**[Hyper-V server (Hyper-V サーバー)]** が表示されていることを確認します。 追加する Hyper-V サーバーが [前提条件](#on-premises-prerequisites) に準拠しており、指定された URL にアクセスできることを確認します。
5. Azure Site Recovery プロバイダーのインストール ファイルをダウンロードします。 このファイルを実行して、各 Hyper-V ホストにプロバイダーと Recovery Services エージェントの両方をインストールします。
6. 登録キーをダウンロードします。 セットアップを実行する際に、これが必要になります。 キーは生成後 5 日間有効です。

    ![Set up source](./media/site-recovery-hyper-v-site-to-azure/set-source3.png)
7. Hyper-V サイトに追加した各ホストで、プロバイダーのセットアップ ファイルを実行します。 Hyper-V クラスターにインストールする場合は、各クラスター ノードでセットアップを実行します。 各 Hyper-V クラスター ノードにインストールして登録すると、仮想マシンをノード間で移行した場合も保護が維持されます。

### <a name="install-the-provider-and-agent"></a>プロバイダーとエージェントのインストール
1. プロバイダーのセットアップ ファイルを実行します。
2. **[Microsoft Update]** で更新プログラムを登録すると、Microsoft Update ポリシーに従ってプロバイダーの更新プログラムがインストールされます。
3. **[インストール]** で、プロバイダーの既定のインストール先をそのまま使用するか、インストール先を変更して、**[インストール]** をクリックします。
4. **[資格情報コンテナー設定]** ページで **[参照]** をクリックして、ダウンロードしたコンテナー キー ファイルを選択します。 Azure Site Recovery のサブスクリプション、コンテナー名、Hyper-V サーバーが属している Hyper-V サイトを指定します。

    ![サーバー登録](./media/site-recovery-hyper-v-site-to-azure/provider3.png)

5. **[プロキシ設定]** で、サーバーにインストールされるプロバイダーからインターネット経由で Azure Site Recovery に接続する方法を指定します。

    * プロバイダーから直接接続するようにするには、 **[Connect directly without a proxy (プロキシなしで直接接続する)]**を選択します。
    * サーバーに現在設定されているプロキシを使って接続する場合は、 **[Connect with existing proxy settings (既存のプロキシ設定を使用して接続する)]**を選択します。
    * 既存のプロキシに認証が必要な場合、またはプロバイダー接続にカスタム プロキシを使用する場合は、**[Connect with custom proxy settings (カスタム プロキシ設定を使用して接続する)]** を選択します。
    * カスタム プロキシを使用する場合、アドレス、ポート、資格情報を指定する必要があります。
    * プロキシを使用する場合、[前提条件](#on-premises-prerequisites)に関するセクションで示されている URL が許可されていることを確認します。

    ![internet](./media/site-recovery-hyper-v-site-to-azure/provider7.PNG)

6. インストールが完了したら、**[登録]** をクリックして、サーバーをコンテナーに登録します。

    ![インストール場所](./media/site-recovery-hyper-v-site-to-azure/provider2.png)

7. 登録が完了すると、Azure Site Recovery によって Hyper-V サーバーからメタデータが取得され、**[Site Recovery インフラストラクチャ]** > **[Hyper-V Hosts (Hyper-V ホスト)]** ブレードにサーバーが表示されます。

### <a name="command-line-installation"></a>コマンド ラインを使用したインストール
Azure Site Recovery Provider とエージェントは、次のコマンド ラインを使用してインストールすることもできます。 このメソッドを使用すると、Windows Server 2012 R2 の Server Core にプロバイダーをインストールできます。

1. プロバイダーのインストール ファイルと登録キーをフォルダーにダウンロードします。 たとえば、C:\ASR です。
2. 管理者特権のコマンド プロンプトで次のコマンドを実行して、プロバイダーのインストーラーを抽出します。

            C:\Windows\System32> CD C:\ASR
            C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. 次のコマンドを実行して、コンポーネントをインストールします。

            C:\ASR> setupdr.exe /i
4. 次のコマンドを実行して、コンテナーにサーバーを登録します。

            CD C:\Program Files\Microsoft Azure Site Recovery Provider\
            C:\Program Files\Microsoft Azure Site Recovery Provider\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file>

<br/>
各値の説明:

* **/Credentials**: 登録キー ファイルが配置されている場所を指定する必須パラメーターです。  
* **/Friendlyname**: Azure Site Recovery ポータルに表示される、Hyper-V ホスト サーバーの名前を表す必須パラメーターです。
* **/proxyAddress**: 省略可能。プロキシ サーバーのアドレスを指定します。
* **/proxyport**: 省略可能。プロキシ サーバーのポートを指定します。
* **/proxyUsername**: 省略可能。プロキシのユーザー名を指定します (認証が必要なプロキシの場合)。
* **/proxyPassword**: 省略可能。プロキシ サーバーの認証に使用するパスワードを指定します (認証が必要なプロキシの場合)。

## <a name="step-3-set-up-the-target-environment"></a>ステップ 3: ターゲット環境をセットアップする

レプリケーションに使用する Azure ストレージ アカウントと、フェールオーバー後に Azure VM が接続する Azure ネットワークを指定します。

1. **[インフラストラクチャの準備]** > **[ターゲット]** の順にクリックし、フェールオーバーされた仮想マシンを作成するサブスクリプションとリソース グループを選択します。 Azure で、フェールオーバーされた仮想マシンに使用するデプロイ モデル (クラシックまたはリソース管理) を選択します。

3. Site Recovery によって、互換性のある Azure ストレージ アカウントとネットワークが 1 つ以上あるかどうかが確認されます。

      ![Storage](./media/site-recovery-vmware-to-azure/enable-rep3.png)


4. まだストレージ アカウントを作成しておらず、Resource Manager を使用して作成する場合は、**[+ ストレージ アカウント]** をクリックしてインラインで作成します。 **[ストレージ アカウントの作成]** ブレードで、アカウント名、種類、サブスクリプション、場所を指定します。 アカウントは、Recovery Services コンテナーと同じ場所にある必要があります。

      ![Storage](./media/site-recovery-hyper-v-site-to-azure/gs-createstorage.png)


クラシック モデルを使用してストレージ アカウントを作成する場合は、 [Azure Portal](../storage/storage-create-storage-account-classic-portal.md) で作成できます。


まだ Azure ネットワークを作成しておらず、Resource Manager を使用して作成する場合は、**[+ ネットワーク]** をクリックしてインラインで作成します。 **[仮想ネットワークの作成]** ブレードで、ネットワーク名、アドレス範囲、サブネットの詳細、サブスクリプション、場所を指定します。 ネットワークは、Recovery Services コンテナーと同じ場所にある必要があります。

   ![ネットワーク](./media/site-recovery-hyper-v-site-to-azure/gs-createnetwork.png)

クラシック モデルを使用してネットワークを作成する場合は、 [Azure Portal](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) で作成できます。

## <a name="step-4-set-up-replication-settings"></a>ステップ 4: レプリケーション設定をセットアップする
1. 新しいレプリケーション ポリシーを作成するには、**[インフラストラクチャの準備]** > **[レプリケーションの設定]** > **[+ 作成と関連付け]** の順にクリックします。

    ![ネットワーク](./media/site-recovery-hyper-v-site-to-azure/gs-replication.png)
2. **[ポリシーの作成と関連付け]** で、ポリシー名を指定します。
3. **[コピーの頻度]** で、初期レプリケーションの後、差分データをレプリケートする頻度 (30 秒ごと、5 分ごと、または 15 分ごと) を指定します。
4. **[復旧ポイントのリテンション期間]**で、各復旧ポイントのリテンション期間の長さを時間単位で指定します。 保護されたマシンはこの期間内のどのポイントにも復旧できます。
5. **[アプリ整合性スナップショットの頻度]** で、アプリケーション整合性スナップショットを含む復旧ポイントの作成頻度 (1 ～ 12 時間) を指定します。 Hyper-V では 2 種類のバックアップを使用します。1 つは標準バックアップで、仮想マシン全体の増分バックアップを実行します。もう 1 つは、アプリケーション整合性スナップショットで、仮想マシン内部のアプリケーション データの特定の時点のスナップショットを作成します。 アプリケーション整合性スナップショットでは、ボリューム シャドウ コピー サービス (VSS) を使用して、スナップショットを作成するときにアプリケーションを一貫性のある状態に保ちます。 アプリケーション整合性スナップショットを有効にすると、ソースの仮想マシンで実行するアプリケーションのパフォーマンスに影響があります。 設定する値は、追加で構成する復旧ポイントの数より少ない数にしてください。
6. **[初期レプリケーションの開始時刻]** で、初期レプリケーションを開始する時刻を指定します。 レプリケーションはご利用のインターネット帯域幅で行われるため、トラフィックの多い時間帯を避けるようにスケジュールを設定することをお勧めします。 次に、 **[OK]**をクリックします

    ![Replication policy](./media/site-recovery-hyper-v-site-to-azure/gs-replication2.png)

新しいポリシーを作成すると、自動的に Hyper-V サイトに関連付けられます。 **[OK]**をクリックします。 Hyper-V サイト (およびサイト内の VM) を複数のレプリケーション ポリシーに関連付けるには、**[レプリケーション]**、ポリシー名、**[Associate Hyper-V Site (Hyper-V サイトの関連付け)]** の順にクリックします。

## <a name="step-5-capacity-planning"></a>ステップ 5: キャパシティ プランニング
基本的なインフラストラクチャをセットアップできたので、キャパシティ プランニングを立案し、追加のリソースが必要かどうかを検討できます。

Site Recovery が備えている Capacity Planner を使用して、ソース環境、Site Recovery のコンポーネント、ネットワーク、およびストレージに適切なリソースを割り当てることができます。 このキャパシティ プランニング ツールは、VM、ディスク、およびストレージの平均数に基づく見積もりを使用するクイック モードか、ワークロード レベルで数値を入力する詳細モードで実行できます。 開始する前に、次のことを行う必要があります。

* VM、VM あたりのディスク数、ディスクあたりのストレージなど、レプリケーション環境の情報を収集する。
* レプリケートされたデータの 1 日の変更 (チャーン) 率を見積もる。 この見積もりには、 [Capacity Planner for Hyper-V Replica](https://www.microsoft.com/download/details.aspx?id=39057) が役立ちます。

1. **[ダウンロード]** をクリックしてツールをダウンロードし、実行する。 [こちらの記事をご覧ください](site-recovery-capacity-planner.md) 。
2. 作業が完了したら、 **[はい]** in **[はい]**を選択する。

   ![容量計画](./media/site-recovery-hyper-v-site-to-azure/gs-capacity-planning.png)

### <a name="network-bandwidth-considerations"></a>ネットワーク帯域幅に関する考慮事項
Capacity Planner を使用して、レプリケーション (初期レプリケーションとその後の差分レプリケーション) に必要な帯域幅を計算できます。 レプリケーションの帯域幅の使用量を制御する方法には、いくつかのオプションがあります。

* **帯域幅を調整する**: Azure にレプリケートされる Hyper-V トラフィックは、特定の Hyper-V ホストを経由します。 このホスト サーバーの帯域幅をスロットルできます。
* **帯域幅の微調整**: いくつかのレジストリ キーを使用して、レプリケーションに使用される帯域幅に影響を与えることができます。

#### <a name="throttle-bandwidth"></a>帯域幅のスロットル
1. Hyper-V ホスト サーバーの Microsoft Azure Backup MMC スナップインを開きます。 既定では、Microsoft Azure Backup のショートカットが、デスクトップか C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin にあります。
2. スナップインで **[プロパティの変更]**をクリックします。
3. **[調整]** タブで、**[バックアップ操作用のインターネット使用帯域幅の調整を有効にする]** チェック ボックスをオンにし、勤務時間用と勤務時間外用の制限値を設定します。 有効な範囲は、1 秒あたり 512 Kbps ～ 102 Mbps です。

    ![帯域幅を調整する](./media/site-recovery-hyper-v-site-to-azure/throttle2.png)

[Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) コマンドレットを使用して、スロットルを設定することもできます。 サンプルを次に示します。

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** は、スロットルが不要であることを示します。

#### <a name="influence-network-bandwidth"></a>ネットワーク帯域幅に影響を与える
1. レジストリで、**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication** に移動します。
   * レプリケートするディスクで帯域幅のトラフィックに影響を与えるには、 **UploadThreadsPerVM**値を変更するか、このキーが存在しない場合は作成します。
   * Azure からのフェールバックのトラフィックの帯域幅に影響を与えるには、 **DownloadThreadsPerVM**値を変更します。
2. 既定値は 4 ですが、 "プロビジョニング超過" 状態のネットワークの場合、このレジストリ キーを既定値から変更する必要があります。 最大値は 32 です。 トラフィックを監視して値を最適化できます。

## <a name="step-6-enable-replication"></a>ステップ 6: レプリケーションを有効にする
レプリケーションを有効にするには、次の手順に従います。

1. **[手順 2: アプリケーションをレプリケートする]** > **[ソース]** の順にクリックします。 レプリケーションを初めて有効にした後は、コンテナーで **[+ レプリケート]** をクリックして、追加のマシンのレプリケーションを有効にします。

    ![Enable replication](./media/site-recovery-hyper-v-site-to-azure/enable-replication.png)
2. **[ソース]** ブレードで、Hyper-V サイトを選択します。 次に、 **[OK]**をクリックします
3. **[ターゲット]** で、コンテナーのサブスクリプションと、フェールオーバー後に Azure で使用するフェールオーバー モデル (クラシックまたは Resource Manager) を選択します。
4. 使用するストレージ アカウントを選択します。 現在持っているものとは別のストレージ アカウントを使用する場合は、 [ストレージ アカウントを作成](#set-up-an-azure-storage-account)できます。 Resource Manager モデルを使用してストレージ アカウントを作成するには、 **[新規作成]**をクリックします。 クラシック モデルを使用してストレージ アカウントを作成する場合は、 [Azure Portal](../storage/storage-create-storage-account-classic-portal.md) で作成できます。 次に、 **[OK]**をクリックします
5. フェールオーバー後に Azure VM がスピンアップされたときに接続する Azure ネットワークとサブネットを選択します。 保護の対象として選択したすべてのマシンにネットワーク設定を適用する場合は、**[選択したマシン用に今すぐ構成します。]** を選択します。 マシンごとに Azure ネットワークを選択する場合は、**[後で構成する]** を選択します。 現在あるものとは別のネットワークを使用する場合は、[ネットワークを作成](#set-up-an-azure-network)できます。 Resource Manager モデルを使用してネットワークを作成するには、**[新規作成]** をクリックします。クラシック モデルを使用してネットワークを作成する場合は、[Azure Portal で](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)作成します。 該当する場合は、サブネットを選択します。 次に、 **[OK]**をクリックします
   ![Enable replication](./media/site-recovery-hyper-v-site-to-azure/enable-replication11.png)

6. **[仮想マシン]** > **[仮想マシンの選択]** で、レプリケートする各マシンをクリックして選択します。 選択できるのは、レプリケーションを有効にできるマシンのみです。 次に、 **[OK]**をクリックします

    ![Enable replication](./media/site-recovery-hyper-v-site-to-azure/enable-replication5-for-exclude-disk.png)
7. **[プロパティ]**  >  **[プロパティの構成]** で、選択した VM のオペレーティング システムと OS ディスクを選択します。 既定では、VM のすべてのディスクがレプリケーションの対象として選択されています。 レプリケーションからディスクを除外して、不要なデータを Azure にレプリケートする際の帯域幅使用量を削減することもできます。 たとえば、一時的なデータや、マシンまたはアプリケーションを再起動するたびに更新されるデータ (pagefile.sys や Microsoft SQL Server tempdb など) が保存されたディスクをレプリケーションから除外できます。 ディスクをレプリケーションから除外するには、ディスクの選択を解除します。 Azure VM の名前 (ターゲット名) が [Azure 仮想マシンの要件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)に準拠していることを確認し、必要に応じて変更します。 次に、 **[OK]**をクリックします 後で追加のプロパティを設定できます。

     ![Enable replication](./media/site-recovery-hyper-v-site-to-azure/enable-replication6-with-exclude-disk.png)

     > [!NOTE]
     >
     > * レプリケーションから除外できるのは、ベーシック ディスクだけです。 OS ディスクを除外することはできないため、ダイナミック ディスクの除外はお勧めしません。 Site Recovery は、ゲスト VM 内の VHD ディスクがベーシック ディスクであるかダイナミック ディスクであるかを識別できません。  依存するダイナミック ボリューム ディスクすべてが除外されていない場合に VM がフェールオーバーし、そのディスクのデータにアクセスできなくなると、保護されたダイナミック ディスクは障害が発生したディスクとして表示されます。
    > * レプリケーションが有効になった後で、レプリケーション用のディスクを追加または削除することはできません。 ディスクを追加または除外する場合は、VM の保護を無効にし、再度有効にする必要があります。
    > * アプリケーションが動作するために必要なディスクを除外した場合、Azure へのフェールオーバー後、レプリケートされたアプリケーションを実行できるように、Azure でディスクを手動で作成する必要があります。 別の方法として、Azure 
    > * Automation を復旧計画に組み込み、マシンのフェールオーバー時にディスクを作成することもできます。
    > * Azure で手動で作成したディスクはフェールバックされません。 たとえば、3 つのディスクをフェールオーバーし、Azure VM に直接 2 つのディスクを作成した場合、フェールオーバーされた 3 つのディスクだけが Azure から Hyper-V にフェールバックされます。 Hyper-V から Azure へのフェールバックまたはレプリケーションの反転に、手動で作成されたディスクを含めることはできません。
    >
    >       

8. **[レプリケーションの設定]**  >  **[レプリケーション設定の構成]** で、保護対象の VM に適用するレプリケーション ポリシーを選択します。 次に、 **[OK]**をクリックします レプリケーション ポリシーを変更するには、**[レプリケーション ポリシー]**、ポリシー名、**[設定の編集]** の順にクリックします。 適用する変更は、既にレプリケートしているマシンと、新しいマシンに使用されます。

   ![Enable replication](./media/site-recovery-hyper-v-site-to-azure/enable-replication7.png)

**[ジョブ]** > **[Site Recovery ジョブ]** の順にクリックして、**保護の有効化**ジョブの進行状況を追跡できます。 **保護の最終処理** ジョブが実行されると、マシンはフェールオーバーできる状態になります。

### <a name="view-and-manage-vm-properties"></a>VM プロパティを表示して管理する
ソース マシンのプロパティを確認することをお勧めします。

1. **[保護されたアイテム]** で、**[レプリケートされたアイテム]** をクリックし、マシンを選びます。

    ![Enable replication](./media/site-recovery-hyper-v-site-to-azure/test-failover1.png)
2. **[プロパティ]** で、VM のレプリケーションとフェールオーバーの情報を確認できます。

    ![Enable replication](./media/site-recovery-hyper-v-site-to-azure/test-failover2.png)
3. **[計算とネットワーク]** > **[計算のプロパティ]** で、Azure VM の名前とターゲットのサイズを指定できます。 必要に応じて、Azure の要件に準拠するように名前を変更します。 Azure VM に割り当てられるターゲット ネットワーク、サブネット、および IP アドレスに関する情報を表示し、変更することもできます。 以下の点に注意してください。

   * ターゲット IP アドレスを設定できます。 アドレスを指定しなかった場合、フェールオーバーされたマシンで DHCP が使用されます。 フェールオーバーで使用できないアドレスが設定された場合、フェールオーバーは失敗します。 テスト フェールオーバー ネットワークのアドレスを利用できる場合、テスト フェールオーバーに同じターゲット IP アドレスを使用できます。
   * ネットワーク アダプターの数は、次に示すように、ターゲット仮想マシンに指定したサイズによって異なります。

     * ソース マシン上のネットワーク アダプターの数が、ターゲット マシンのサイズに許可されているアダプターの数以下の場合、ターゲットのアダプターの数は、ソースと同じになります。
     * ソース仮想マシン用のアダプターの数が、ターゲットのサイズに許可されている数を超える場合は、ターゲットの最大サイズが使用されます。
     * たとえば、ソース マシンに 2 つのネットワーク アダプターがあり、ターゲット マシンのサイズが 4 つをサポートしている場合は、ターゲット マシンのアダプターの数は、2 つになります。 ソース マシンに 2 つのアダプターがあるが、サポートされているターゲット サイズで 1 つしかサポートしていない場合、ターゲット マシンのアダプターの数は 1 つだけになります。     
     * VM に複数のネットワーク アダプターがある場合は、すべて同じネットワークに接続されます。
     * 仮想マシンにネットワーク アダプターが複数ある場合は、一覧で最初に表示されるアダプターが、Azure 仮想マシンの*既定*のネットワーク アダプターとなります。

     ![Enable replication](./media/site-recovery-hyper-v-site-to-azure/test-failover4.png)

4. **[ディスク]** で、レプリケートされる VM のオペレーティング システム ディスクとデータ ディスクを確認できます。



### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>フェールオーバー後に Azure VM に接続するための準備をする
フェールオーバー後に RDP を使用して Azure VM に接続する場合は、次のことを行う必要があります。

**フェールオーバー前にオンプレミスのマシンで行う操作**:

* インターネット経由のアクセスで RDP を有効にする場合は、TCP と UDP の規則が **[パブリック]** に追加されていることを確認し、すべてのプロファイルについて、**[Windows ファイアウォール]** -> **[許可されたアプリおよび機能]** で RDP が許可されていることを確認します。
* マシンでサイト間接続経由のアクセスに対して RDP を有効にする場合は、**[ドメイン]** および **[プライベート]** ネットワークについて、**[Windows ファイアウォール]** -> **[許可されたアプリおよび機能]** で RDP が許可されていることを確認します。
* オンプレミスのマシンに [Azure VM エージェント](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) をインストールします。
* オペレーティング システムの SAN ポリシーが OnlineAll に設定されていることを確認します。 [詳細情報](https://support.microsoft.com/kb/3031135)
* フェールオーバーを実行する前に、IPSec サービスを無効にします。

**フェールオーバー後に Azure VM で行う操作**:

* Azure VM に関連付けられている NIC にパブリック IP アドレスを追加して、RDP を許可します。
* パブリック アドレスを使用する仮想マシンへの接続を妨げるドメイン ポリシーを使用していないことを確認します。
* 接続を試みます。 接続できない場合は、VM が実行されていることを確認します。 トラブルシューティングのヒントについては、 [こちらの記事](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)をご覧ください。

フェールオーバー後に Linux が実行されている Azure VM に Secure Shell クライアント (ssh) を使用してアクセスする場合は、次の手順に従います。

**フェールオーバー前にオンプレミスのマシンで行う操作**:

* Azure VM 上の Secure Shell サービスがシステム起動時に自動的に開始されるよう設定されていることを確認します。
* ファイアウォール規則で SSH 接続が許可されていることを確認します。

**フェールオーバー後に Azure VM で行う操作**:

* フェールオーバーされた VM および接続先の Azure サブネットのネットワーク セキュリティ グループの規則で、SSH ポートへの着信接続を許可する必要があります。
* SSH ポート (既定では TCP ポート 22) で着信接続を許可するようにパブリック エンドポイントが作成されている必要があります。
* VM に VPN 接続 (ExpressRoute またはサイト間 VPN) を介してアクセスする場合は、クライアントを使用して SSH 経由で直接 VM に接続できます。

## <a name="step-7-run-a-test-failover"></a>ステップ 7: テスト フェールオーバーを実行する
デプロイをテストするために、単一の仮想マシンに対して、または 1 つ以上の仮想マシンを含む復旧計画に対してテスト フェールオーバーを実行できます。

1. 1 つの仮想マシンをフェールオーバーする場合は、**[レプリケートされたアイテム]** で、その VM をクリックし、**[+ テスト フェールオーバー]** をクリックします。
2. 復旧計画をフェールオーバーする場合は、**[復旧計画]** で、計画を右クリックし、**[テスト フェールオーバー]** をクリックします。 復旧計画を作成する場合は、[こちらの手順に従ってください](site-recovery-create-recovery-plans.md)。
3. **[テスト フェールオーバー]** で、フェールオーバー後に Azure VM が接続する Azure ネットワークを選択します。
4. **[OK]** をクリックすると、フェールオーバーが開始されます。 進行状況を追跡するには、VM をクリックしてそのプロパティを開くか、コンテナー名をクリックし、**[ジョブ]** > **[Site Recovery ジョブ]** の順にクリックして、**[テスト フェールオーバー]** ジョブをクリックします。
5. フェールオーバーの完了後は、Azure Portal の **[仮想マシン]** にレプリカの Azure マシンも表示されるようになります。 VM が適切なサイズであること、適切なネットワークに接続していること、実行されていることを確認する必要があります。
6. [フェールオーバー後の接続の準備](#prepare-to-connect-to-azure-vms-after-failover)が完了したら、Azure VM に接続できるようになります。
7. 完了したら、復旧計画の **[Cleanup test failover (テスト フェールオーバーのクリーンアップ)]** をクリックします。 **[メモ]** を使用して、テスト フェールオーバーに関連する観察結果をすべて記録し、保存します。 これで、テスト フェールオーバー中に作成された仮想マシンが削除されます。

詳しくは、「[Site Recovery での Azure へのフェールオーバーをテストする](site-recovery-test-failover-to-azure.md)」をご覧ください。


## <a name="failover"></a>フェールオーバー
マシンの初期レプリケーションが完了したら、必要に応じて、フェールオーバーを呼び出すことができます。 Site Recovery ではさまざまな種類のフェールオーバーがサポートされています。これには、テスト フェールオーバー、計画されたフェールオーバー、計画されていないフェールオーバーなどがあります。
各種のフェールオーバーについて、またそれらを実行するタイミングや方法について詳しくは、[こちら](site-recovery-failover.md)をご覧ください。

> [!NOTE]
> 目的が Azure への仮想マシンの移行である場合は、[計画されたフェールオーバー操作](site-recovery-failover.md)を使用して Azure に仮想マシンを移行することをお勧めします。 移行済みのアプリケーションをテスト フェールオーバーを使用して Azure で検証したら、[移行の完了](#Complete-migration-of-your-virtual-machines-to-Azure)に関するセクションの手順に従って、仮想マシンの移行を完了します。 [コミット] または [削除] を実行する必要はありません。 移行の完了では、移行を完了し、仮想マシンの保護を削除し、マシンの Azure Site Recovery の課金を停止します。


### <a name="run-a-planned-failover"></a>計画されたフェールオーバーの実行
これは、コンプライアンス要件を満たすため、または計画的なメンテナンスの間に、既知の障害 (予想される電源障害や悪天候の予報など) に備えてワークロードを継続して実行できるように、必要なデータをフェールオーバーするために、選ぶ必要があります。 この手順では、復旧計画の、計画されたフェールオーバーを実行する方法について説明します。 別の方法として、[仮想マシン] タブで、単一の仮想マシンに対するフェールオーバーを実行することもできます。 開始する前に、フェールオーバーするすべての仮想マシンが、初期レプリケーションを完了していることを確認します。

1. **[復旧計画] > [recoveryplan_name]** を選択します。
2. [復旧計画] ブレードで、**[計画されたフェールオーバー]** をクリックします。
3. **[計画されたフェールオーバーの確認]**ページで、ソースとターゲットの場所を選択します。
4. データ損失を確実に防ぐために、計画されたフェールオーバーの開始時の最初のステップとして、仮想マシンをシャットダウンします。 フェールオーバーの進行状況は、 **[ジョブ]** タブで確認できます。 フェールオーバーで (仮想マシン、または復旧計画に含まれているスクリプトのいずれかにおいて) エラーが発生した場合は、復旧計画の計画されたフェールオーバーは停止します。 フェールオーバーは、もう一度開始することができます。
6. レプリカ仮想マシンは、作成後にコミット保留中の状態になります。 **[コミット]** をクリックして、フェールオーバーをコミットします。
7. レプリケーションが完了すると、仮想マシンがセカンダリの場所で起動します。


### <a name="run-an-unplanned-failover"></a>計画されていないフェールオーバーの実行
この方法は、停電やウイルスの攻撃などの予期しないインシデントが原因で、プライマリ サイトにアクセスできなくなった場合に備えて選択します。 この手順では、復旧計画の、"計画されていないフェールオーバー" を実行する方法について説明します。 別の方法として、[仮想マシン] タブで、単一の仮想マシンに対するフェールオーバーを実行することもできます。 開始する前に、フェールオーバーするすべての仮想マシンが、初期レプリケーションを完了していることを確認します。

1. **[復旧計画] > [recoveryplan_name]** を選択します。
2. [復旧計画] ブレードで、**[計画されていないフェールオーバー]** をクリックします。
3. **[計画されていないフェールオーバー]** ページで、ソースとターゲットの場所を選択します。
4. **[仮想マシンをシャットダウンして最新のデータを同期 (Shut down virtual machines and synchronize the latest data)]** を選択し、Site Recovery が保護された仮想マシンをシャットダウンしてデータを同期するように指定することで、最新バージョンのデータをフェールオーバーします。
5. フェールオーバーの後、仮想マシンはコミット保留中の状態です。  **[コミット]** をクリックして、フェールオーバーをコミットします。

[詳細情報](site-recovery-failover.md)

## <a name="complete-migration-of-your-virtual-machines-to-azure"></a>仮想マシンの Azure への移行の完了
> [!NOTE]
> 次の手順が適用されるのは、仮想マシンを Azure に移行する場合のみです。
>
>

1. [ここ](site-recovery-failover.md)で説明されている手順に従って、計画されたフェールオーバーを実行します。
2. **[レプリケートされたアイテム]** で仮想マシンを右クリックし、**[移行の完了]** を選びます。

    ![移行の完了](./media/site-recovery-hyper-v-site-to-azure/migrate.png)
3. **[OK]** をクリックして移行を完了します。 進行状況を追跡するには、VM をクリックしてプロパティを開くか、**[Site Recovery ジョブ]** の [移行の完了] ジョブを使います。

## <a name="monitor-your-deployment"></a>デプロイを監視する
Site Recovery デプロイメントの構成設定、状態、および正常性を監視する方法を次に示します。

1. コンテナー名をクリックして、 **[要点]** ダッシュボードにアクセスします。 このダッシュボードで、Site Recovery ジョブ、レプリケーションの状態、復旧計画、サーバーの状態、およびイベントを確認できます。  [要点] ダッシュボードをカスタマイズして、他の Site Recovery コンテナーや Backup コンテナーの状態など、ニーズに最適なタイルとレイアウトを表示できます。

    ![[要点]](./media/site-recovery-hyper-v-site-to-azure/essentials.png)
2. **[正常性]** タイルで、問題が発生しているサイト サーバーと、Site Recovery によって過去 24 時間以内に生成されたイベントを監視できます。
3. **[レプリケートされたアイテム]**、**[復旧計画]**、**[Site Recovery ジョブ]** の各タイルで、レプリケーションの管理と監視を実行できます。 **[ジョブ]** > **[Site Recovery ジョブ]** の順にクリックすると、ジョブの詳細を確認できます。

