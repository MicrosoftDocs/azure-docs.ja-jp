---
title: "Azure Site Recovery を使用して Azure にオンプレミス Hyper-V VM のディザスター リカバリーを設定する |Microsoft Docs"
description: "Azure Site Recovery サービスを使用して Azure にオンプレミス Hyper-V VM のディザスター リカバリーを設定する方法について説明します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 45e9b4dc-20ca-4c14-bee3-cadb8d9b8b24
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: raynew
ms.openlocfilehash: 96e5027adfb443aba18895213e8d83894e3f060a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Azure にオンプレミス Hyper-V VM のディザスター リカバリーを設定する

[Azure Site Recovery](site-recovery-overview.md) サービスは、オンプレミス マシンと Azure Virtual Machines (VM) のレプリケーション、フェールオーバー、フェールバックを管理し、調整することでディザスター リカバリー戦略に貢献します。

このチュートリアルでは、Azure にオンプレミス Hyper-V VM のディザスター リカバリーを設定する方法を紹介します。 このチュートリアルは、System Center Virtual Machine Manager (VMM) クラウドで管理される Hyper-V VM と管理されない Hyper-V VM に関連します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure とオンプレミスの前提条件を設定する
> * Site Recovery の Recovery Services コンテナーを作成する 
> * ソース レプリケーション環境とターゲット レプリケーション環境を設定する 
> * ネットワーク マッピングを設定する (Hyper-V が System Center VMM によって管理される場合)
> * レプリケーション ポリシーを作成する
> * VM のレプリケーションを有効にする


## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

- [シナリオのアーキテクチャとコンポーネント](concepts-hyper-v-to-azure-architecture.md)を理解している。
- すべてのコンポーネントの[サポート要件](site-recovery-support-matrix-to-azure.md)を確認すること。
- レプリケートする VM が [Azure VM 要件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)に準拠していること。
- 容量計画を調べるには:
    - [Hyper-V Capacity Planner ツール](http://aka.ms/asr-capacity-planner-excel)を利用し、チャーン レートを調べます。
    - [Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel) を利用し、ソース環境、見積もり帯域幅、ターゲット要件を分析します。
- Azure を準備します。 Azure サブスクリプション、Azure 仮想ネットワーク、ストレージ アカウントが必要です。
- オンプレミス Hyper-V ホストと、関連する場合、VMM サーバーを準備します。





### <a name="set-up-an-azure-account"></a>Azure アカウントを設定する

Microsoft [Azure アカウント](http://azure.microsoft.com/)を取得します。

- アカウントがなくても、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)を使用できます。
- [Site Recovery の価格](site-recovery-faq.md#pricing)について理解し、[価格の詳細](https://azure.microsoft.com/pricing/details/site-recovery/)を確認します。
- Site Recovery の[サポート対象のリージョン](https://azure.microsoft.com/pricing/details/site-recovery/)を見つけます。

### <a name="verify-azure-account-permissions"></a>Azure アカウントのアクセス許可を確認する

VM のレプリケートに必要なアクセス許可が Azure アカウントにあることを確認します。

- Azure にマシンをレプリケートするために必要な[アクセス許可](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)を確認する
- [ロールベースのアクセス許可](../active-directory/role-based-access-control-configure.md)を確認し、変更します。 


### <a name="set-up-an-azure-network"></a>Azure ネットワークをセットアップする

[Azure ネットワーク](../virtual-network/virtual-network-get-started-vnet-subnet.md)をセットアップします。

- Azure VM は、フェールオーバー後に作成されたときに、このネットワークに配置されます。
- ネットワークは、Recovery Services コンテナーと同じリージョンにある必要があります。


### <a name="set-up-an-azure-storage-account"></a>Azure Storage アカウントを設定

[Azure ストレージ アカウント](../storage/common/storage-create-storage-account.md#create-a-storage-account)を設定します。

- Site Recovery は、オンプレミスのマシンを Azure Storage にレプリケートします。 Azure VM は、フェールオーバーの発生後にストレージから作成されます。
- ストレージ アカウントは、Recovery Services コンテナーと同じリージョンに存在する必要があります。
- このストレージ アカウントには、Standard または [Premium](../storage/common/storage-premium-storage.md) を使用できます。
- Premium アカウントを設定する場合、ログ データ用の Standard アカウントが別途必要になります。

### <a name="prepare-hyper-v-hosts"></a>Hyper-V ホストを準備する

1. Hyper-V hosts が[サポート要件](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)を満たすことを確認します。
2. ホストが次の URL にアクセスできることを確認します。

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - IP アドレスベースのファイアウォール規則がある場合、その規則で Azure への通信を許可します。
    - [Azure データセンターの IP の範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)と HTTPS (443) ポートを許可します。
    - ご利用のサブスクリプションの Azure リージョンと米国西部の IP アドレス範囲を許可します (Access Control と ID 管理に使用されます)。

### <a name="prepare-vmm-servers"></a>VMM サーバーを準備する

Hyper-V ホストが VMM で管理される場合、オンプレミス VMM サーバーを用意する必要があります。 

- VMM サーバーが[サポート要件](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)を満たすことを確認します。
- VMM サーバーに少なくとも 1 つのクラウドと、1 つまたは複数のホスト グループがあることを確認します。 VM を実行する Hyper-V ホストはクラウドに置いてください。
- VMM サーバーにはインターネット アクセスが必要です。次の URL にアクセスできるようにします。

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - IP アドレスベースのファイアウォール規則がある場合、その規則で Azure への通信を許可します。
    - [Azure データセンターの IP の範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)と HTTPS (443) ポートを許可します。
    - ご利用のサブスクリプションの Azure リージョンと米国西部の IP アドレス範囲を許可します (Access Control と ID 管理に使用されます)。
- ネットワーク マッピング用に VMM サーバーを準備します。


#### <a name="prepare-vmm-for-network-mapping"></a>ネットワーク マッピング用に VMM を準備する

VMM を使用している場合、[ネットワーク マッピング](site-recovery-network-mapping.md)は、オンプレミス VMM VM ネットワークと Azure 仮想ネットワークの間でマッピングを行います。 マッピングによって、フェールバック後に作成されるとき、Azure VM は正しいネットワークに接続されます。

次の手順に従って、ネットワーク マッピング用に VMM を準備します。

1. Hyper-V ホストが置かれているクラウドに関連付けられている [VMM 論理ネットワーク](https://docs.microsoft.com/system-center/vmm/network-logical)があることを確認します。
2. 論理ネットワークにリンクされている [VM ネットワーク](https://docs.microsoft.com/system-center/vmm/network-virtual)があることを確認します。
3. VM ネットワークに VM を接続します。

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="select-a-protection-goal"></a>保護の目標を選択する

レプリケート対象とレプリケート場所を選択します。

1. コンテナーで、**[Site Recovery]**、 > **[インフラストラクチャの準備]**、 > **[保護の目標]** の順にクリックします。
2. **[保護の目標]** で、**[Azure へ]**> **[Yes, with Hyper-V]\(はい、Hyper-V を使用する\)** を選択します。 
    - Hyper-V ホストが VMM によって管理されていない場合、**[いいえ]** を選択し、VMM を使用していないことを確定します。
    - ホストが VMM クラウドで管理されている場合、**[はい]** を選択します。

## <a name="set-up-the-source-environment"></a>ソース環境をセットアップする

ソース環境をセットアップするとき、Azure Site Recovery Provider と Azure Recovery Services エージェントをインストールし、オンプレミス サーバーをコンテナーに登録します。 

1. **[インフラストラクチャの準備]** で、**[ソース]** をクリックします。 
    - VMM を使用していない場合、**[+Hyper-V サイト]** をクリックし、サイト名を追加します。 次に **[+Hyper-V Server]** をクリックし、ホストまたはクラスターをサイトに追加します。
    - VMM を使用している場合、**[ソースの準備]** で **[+ VMM]** をクリックして VMM サーバーを追加します。 **[サーバーの追加]** で、**[System Center VMM サーバー]** に **[構成サーバー]** が表示されていることを確認します。
2. 環境に合わせてプロバイダーとエージェント コンポーネントをダウンロードします。
    - Hyper-V だけの場合、プロバイダー インストール ファイルをダウンロードします。 各 Hyper-V ホストでファイルを実行し、プロバイダーとエージェントの両方をインストールします。
        
        ![VMM なしのプロバイダー](./media/tutorial-hyper-v-to-azure/download-no-vmm.png)
    
    - Hyper-V と VMM の場合、プロバイダーとエージェントを別々にダウンロードします。 VMM サーバーでプロバイダー インストールを実行します。 各 Hyper-V ホストでエージェント インストールを実行します。
    
        ![プロバイダー、エージェント、VMM](./media/tutorial-hyper-v-to-azure/download-vmm.png)
    
3. コンテナー登録キーをダウンロードします。 プロバイダーをセットアップする際に、これが必要になります。 キーは生成後 5 日間有効です。

### <a name="install-components"></a>コンポーネントをインストールする

表に従い、コンポーネントをインストールします。 

**コンポーネント** | **詳細** | **Hyper-V のみ** | **Hyper-V (VMM あり)**
--- | --- | --- | ---
**Azure Site Recovery プロバイダー** | Azure へのレプリケーションを調整する | 各 Hyper-V ホストにインストールする | VMM サーバーにインストールする
**Recovery Services エージェント** | データ レプリケーションを処理する | 各 Hyper-V ホストにインストールする | Hyper-V ホストにインストールする


#### <a name="install-the-provider-on-hyper-v-without-vmm"></a>Hyper-V (VMM なし) にプロバイダーをインストールする

Hyper-V サイトに追加した各 Hyper-V ホストにプロバイダーをインストールします。 Hyper-V クラスターにインストールする場合は、各クラスター ノードでセットアップを実行します。 各 Hyper-V クラスター ノードにインストールして登録すると、VM をノード間で移行しても保護が維持されます。

1. **[Microsoft Update]** で更新プログラムを登録すると、Microsoft Update ポリシーに従ってプロバイダーの更新プログラムがインストールされます。
2. **[インストール]** で、プロバイダーの既定のインストール先をそのまま使用するか、インストール先を変更して、**[インストール]** をクリックします。
4. **[資格情報コンテナー設定]** で **[参照]** をクリックして、ダウンロードしたコンテナー キー ファイルを選択します。 Azure Site Recovery のサブスクリプション、コンテナー名、Hyper-V サーバーが属している Hyper-V サイトを指定します。
5. **[プロキシの設定]** で、VMM サーバーまたは Hyper-V ホストで実行されているプロバイダーがインターネット経由で Site Recovery に接続する方法を指定します。
    * 直接接続する場合は、**[プロキシを使用せずに直接 Azure Site Recovery に接続する]** を選択します。
    * プロキシには、**[プロキシ サーバーを使用して Azure Site Recovery に接続する]** を選択します。 必要に応じて、プロキシ アドレス、ポート、資格情報を指定します。
6. サーバーがコンテナーに登録されたら、**[完了]** をクリックします。

Azure Site Recovery によって Hyper-V サーバーからメタデータが取得され、**[Site Recovery インフラストラクチャ]** の  > **[Hyper-V ホスト]** にサーバーが表示されます。


#### <a name="install-the-recovery-services-agent-on-hyper-v-host-without-vmm"></a>Hyper-V ホスト (VMM なし) に Recovery Services エージェントをインストールする

デプロイで VMM を使用している場合、Hyper-V ホストごとに Recovery Services エージェント セットアップを実行します。

1. セットアップ ウィザードの **[前提条件の確認]** で **[次へ]** をクリックします。 不足している前提条件があると自動的にインストールされます。

    ![Recovery Services エージェントの前提条件](./media/tutorial-hyper-v-to-azure/hyperv-agent-prerequisites.png)
3. **[インストール設定]** で、インストール先とキャッシュの場所を既定のまま使用するか変更します。 キャッシュ ドライブには 5 GB 以上のストレージが必要です。 600 GB 以上の空き領域があるドライブをお勧めします。 その後、 **[インストール]**をクリックします。
4. **[インストール]** で、インストールが完了したら、**[閉じる]** をクリックしてウィザードを完了します。

##### <a name="set-up-internet-access-via-a-proxy"></a>プロキシ経由のインターネット アクセスをセットアップする

Hyper-V ホスト上の Recovery Services エージェントは、VM レプリケーションのために、Azure にインターネット アクセスする必要があります。 プロキシを介してインターネットにアクセスしている場合は、次のようにセットアップします。

1. Hyper-V ホストの Microsoft Azure Backup MMC スナップインを開きます。 既定では、Microsoft Azure Backup のショートカットが、デスクトップか C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin にあります。
2. スナップインで **[プロパティの変更]** をクリックします。
3. **[プロキシの構成]** タブで、プロキシ情報を指定します。

    ![Register MARS Agent](./media/tutorial-hyper-v-to-azure/mars-proxy.png)
4. エージェントが[所要の URL](#prepare-hyper-v-hosts) にアクセスできることを確認します。

#### <a name="install-the-provider-on-the-vmm-server-hyper-v-with-vmm"></a>VMM サーバーにプロバイダーをインストールする (VMM のある Hyper-V)

1. **[Microsoft Update]** で更新プログラムを登録すると、Microsoft Update ポリシーに従ってプロバイダーの更新プログラムがインストールされます。
2. **[インストール]** で、プロバイダーの既定のインストール先をそのまま使用するか、インストール先を変更して、**[インストール]** をクリックします。 
3. VMM サーバーをコンテナーに登録します。 **[資格情報コンテナー設定]** で **[参照]** をクリックして、ダウンロードしたコンテナー キー ファイルを選択します。 Azure Site Recovery サブスクリプションとコンテナー名を指定します。

    ![サーバー登録](./media/tutorial-hyper-v-to-azure/provider-vault-settings.png)

4. **[プロキシの設定]** で、VMM サーバーまたは Hyper-V ホストで実行されているプロバイダーがインターネット経由で Site Recovery に接続する方法を指定します。

    * 直接接続する場合は、**[プロキシを使用せずに直接 Azure Site Recovery に接続する]** を選択します。
    * プロキシには、**[プロキシ サーバーを使用して Azure Site Recovery に接続する]** を選択します。 必要に応じて、プロキシ アドレス、ポート、資格情報を指定します。
    - 指定したプロキシの資格情報を使用して VMM RunAs アカウント (DRAProxyAccount) が自動的に作成されます。 このアカウントが正しく認証されるようにプロキシ サーバーを構成します。 RunAs アカウントの設定を変更できます (VMM コンソール> **[設定]** > **[セキュリティ]** > **[RunAs アカウント]**)。 VMM サービスを再起動して変更を更新します。
5. **[データの暗号化]** で、Azure に送信されるすべてのデータを暗号化するか指定します。 このオプションを選択した場合、Site Recovery は証明書を発行します。 後でデータを復号するときにこの証明書が必要になります。
6. **[VMM サーバー]**に、コンテナーで VMM サーバーを識別するための表示名を入力します。 クラスター構成で、VMM クラスターのロール名を指定します。 **[Synchronize cloud metadata with the vault]\(クラウド メタデータとコンテナーを同期する\)** で、VMM サーバー上のすべてのクラウドのメタデータをコンテナーと同期するかどうかを選択します。 この操作は、各サーバーで 1 回のみ実行する必要があります。 一部のクラウドを同期しない場合は、この設定をオフのままにして、VMM コンソールのクラウドのプロパティで各クラウドを個別に同期できます。

登録が完了すると、Azure Site Recovery によってサーバーからメタデータが取得され、**[Site Recovery インフラストラクチャ]** に VMM サーバーが表示されます。






## <a name="set-up-the-target-environment"></a>ターゲット環境をセットアップする

ターゲット リソースを選択して確認します。 

1. **[インフラストラクチャの準備]** > **[ターゲット]** の順にクリックします。
2. サブスクリプションと、フェールオーバー後に Azure VM が作成されるリソース グループを選択します。 Azure で VM に使用するデプロイ モデルを選択します。

3. Site Recovery によって、互換性のある Azure ストレージ アカウントとネットワークが 1 つ以上あるかどうかが確認されます。

## <a name="configure-network-mapping-with-vmm"></a>ネットワーク マッピングを構成する (VMM あり)

VMM を使用している場合、ネットワーク マッピングを設定します。

1. **[Site Recovery Infrastructure (Site Recovery インフラストラクチャ)]** > **[ネットワーク マッピング]** > **[ネットワーク マッピング]** の順にクリックし、**[+ ネットワーク マッピング]** アイコンをクリックします。
2. **[ネットワーク マッピングの追加]** で、ソース VMM サーバーを選択します。 ターゲットとして **[Azure]** を選択します。
3. サブスクリプションとフェールオーバー後のデプロイメント モデルを確認します。
4. **[ソース ネットワーク]** で、ソース オンプレミス VM ネットワークを選択します。
5. **[ターゲット ネットワーク]**で、フェールオーバー後の作成時、レプリカ Azure VM が配置される Azure ネットワークを選択します。 次に、 **[OK]**をクリックします

    ![ネットワーク マッピング](./media/tutorial-hyper-v-to-azure/network-mapping-vmm.png)

## <a name="create-a-replication-policy"></a>レプリケーション ポリシーを作成する

1. **[インフラストラクチャの準備]**、** > [レプリケーションの設定]**、 > **[+ 作成と関連付け]** の順にクリックします。
2. **[ポリシーの作成と関連付け]**で、ポリシー名を指定します。
3. **[コピーの頻度]**で、初期レプリケーションの後、差分データをレプリケートする頻度 (30 秒ごと、5 分ごと、または 15 分ごと) を指定します。

    > [!NOTE]
    >  Premium Storage にレプリケートするときには、30 秒の頻度はサポートされません。 上限は、Premium Storage によってサポートされる BLOB ごとのスナップショットの数 (100) によって決まります。 [詳細情報](../storage/common/storage-premium-storage.md#snapshots-and-copy-blob)

4. **[復旧ポイントの保持期間]**で、各復旧ポイントのリテンション期間の長さを時間単位で指定します。 保護されたマシンはこの期間内のどのポイントにも復旧できます。
5. **[アプリ整合性スナップショットの頻度]** で、アプリケーション整合性スナップショットを含む復旧ポイントの作成頻度 (1 ～ 12 時間) を指定します。 Hyper-V では、2 種類のスナップショットが使用されます。
    - **標準スナップショット**: 仮想マシン全体の増分スナップショットを提供します。
    - **アプリ整合性スナップショット**: VM 内でアプリケーション データのある時点のスナップショット ボリューム シャドウ コピー サービス (VSS) によって、スナップショットの作成時、アプリの整合性が維持されます。 アプリ整合性スナップショットを有効にすると、ソース VM のアプリ パフォーマンスに影響が出ます。 構成する追加の復旧ポイントより少ない値を設定してください。
6. **[初期レプリケーションの開始時刻]**で、初期レプリケーションを開始する時刻を指定します。 レプリケーションはご利用のインターネット帯域幅で行われるため、トラフィックの多い時間帯を避けるようにスケジュールを設定することをお勧めします。
7. **[Azure に格納されるデータの暗号化]**で、Azure ストレージに格納されるデータを暗号化するかどうかを指定します。 次に、 **[OK]**をクリックします

    ![Replication policy](./media/tutorial-hyper-v-to-azure/replication-policy.png)


新しいポリシーを作成すると、自動的に VMM クラウドまたは Hyper-V サイトに関連付けられます。

## <a name="enable-replication"></a>Enable replication


1. **[アプリケーションをレプリケートする]** > **[ソース]** の順にクリックします。 
2. **[ソース]** で、Hyper-V サイト/VMM サーバー/クラウドを選択します。 次に、 **[OK]**をクリックします
3. **[ターゲット]** で、ターゲットとしての Azure、コンテナー サブスクリプション、フェールオーバー後に Azure で使用するモデルを確認します。
4. レプリケートされたデータのストレージ アカウントと、フェールオーバー後に Azure VM が配置される Azure ネットワークを選択します。
5. **[仮想マシン]** の  > **[選択]** で、レプリケートする VM を選択します。 次に、 **[OK]**をクリックします

 **[ジョブ]** > **[Site Recovery ジョブ]** の順にクリックして、**[保護を有効にする]** アクションの進行状況を追跡できます。 **[保護の最終処理]** ジョブが完了すると、初期レプリケーションが完了し、仮想マシンがフェールオーバーを実行できる状態になります。

## <a name="next-steps"></a>次のステップ
[ディザスター リカバリーのテストを実行する](tutorial-dr-drill-azure.md)
