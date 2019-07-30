---
title: Azure Migrate Server Migration を使用して Hyper-V VM を Azure に移行する | Microsoft Docs
description: この記事では、Azure Migrate Server Migration を使用して Hyper-V VM を Azure に移行する方法を説明します
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 4a88e9dddd492d5c24698bcde8c3a1fd942eaf66
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854189"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>Hyper-V VM を Azure に移行する 

この記事では、Azure Migrate Server Migration ツールのエージェントレス移行を使用して、オンプレミスの Hyper-V VM を Azure に移行する方法について説明します。

[Azure Migrate](migrate-services-overview.md) は、オンプレミスのアプリとワークロード、およびプライベート/パブリック クラウド VM の検出、評価、および Azure への移行を追跡するための中央ハブとなります。 このハブには、評価および移行のための Azure Migrate ツールのほか、サードパーティの独立系ソフトウェア ベンダー (ISV) のオファリングが用意されています。

このチュートリアルは、Azure Migrate の Server Assessment と Server Migration を使用して Hyper-V を評価し、Azure に移行する方法を示すシリーズの、3 番目のチュートリアルです。 このチュートリアルでは、以下の内容を学習します。


> [!div class="checklist"]
> * Azure とオンプレミスの Hyper-V 環境を準備する
> * ソース環境を設定し、レプリケーション アプライアンスをデプロイする。
> * ターゲット環境を設定する。
> * レプリケーションを有効にします。
> * テスト移行を実行して、すべて想定どおりに動作することを確認する。
> * Azure への完全な移行を実行する。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。


## <a name="prerequisites"></a>前提条件

このチュートリアルを始める前に、次の準備が必要です。

1. Hyper-V の移行のアーキテクチャを[確認](migrate-architecture.md)します。
2. このシリーズの[最初のチュートリアルを完了](tutorial-prepare-hyper-v.md)して、移行用に Azure と hyper-V を設定します。 最初のチュートリアルでは、次のことを行います。
    - 移行用に [Azure を準備する](tutorial-prepare-hyper-v.md#prepare-azure)。
    - 移行用に[オンプレミス環境を準備する](tutorial-prepare-hyper-v.md#prepare-for-hyper-v-migration)。
3. Azure に移行する前に、Azure Migrate Server Assessment を使用して、Hyper-V VM を評価することをお勧めします。 これを行うには、このシリーズの [2 番目のチュートリアルを完了](tutorial-assess-hyper-v.md)します。 評価を試してみることをお勧めしますが、VM を移行する前に評価を実行する必要はありません。
4. ご自分の Azure アカウントに仮想マシンの共同作成者ロールが割り当てられ、以下を行うためのアクセス許可を持っていることを確認します。

    - 選択したリソース グループ内に VM を作成する。
    - 選択した仮想ネットワーク内に VM を作成する。
    - Azure マネージド ディスクに書き込む。   
5. [Azure ネットワークをセットアップ](../virtual-network/manage-virtual-network.md#create-a-virtual-network)します。 Azure に移行すると、作成された Azure VM が移行の設定時に指定した Azure ネットワークに参加させられます。


## <a name="add-the-azure-migrate-server-migration-tool"></a>Azure Migrate Server Migration ツールを追加する

2 番目のチュートリアルに従って Hyper-V VM を評価していない場合は、[次の手順に従って](how-to-add-tool-first-time.md) Azure Migrate プロジェクトを設定し、Azure Migrate Server Migration ツールをプロジェクトに追加する必要があります。

2 番目のチュートリアルに従って、既に Azure Migrate プロジェクトをセットアップしてある場合は、次のように Azure Migrate Server Migration ツールを追加します。

1. Azure Migrate プロジェクトで、 **[概要]** をクリックします。 
2. **[サーバーの検出、評価、移行]** で、 **[サーバーの評価と移行]** をクリックします。
3. **[移行ツール]** で、 **[ここをクリックして、移行の準備が完了したときに移行ツールを追加します]** を選択します。

    ![ツールを選択する](./media/tutorial-migrate-hyper-v/select-migration-tool.png)

4. ツールの一覧で、 **[Azure Migrate: Server Migration]**  >  **[ツールを追加]** を選択します

    ![Server Migration ツール](./media/tutorial-migrate-hyper-v/server-migration-tool.png)


## <a name="set-up-the-azure-migrate-appliance"></a>Azure Migrate アプライアンスを設定する

Azure Migrate Server Migration では、軽量の Hyper-V VM アプライアンスが実行されます。

- このアプライアンスでは、VM の検出が実行されて、VM のメタデータとパフォーマンス データが Azure Migrate Server Migration に送信されます。
- Azure Migrate Server Assessment ツールでも、同じアプライアンスが使用されます。

アプライアンスを設定するには:
- 2 番目のチュートリアルに従って Hyper-V VM を評価した場合は、そのチュートリアルの間に既にアプライアンスを設定してあります。
- そのチュートリアルに従っていない場合は、ここでアプライアンスを設定する必要があります。 このためには、次の手順に従います。 

    - Azure portal から、圧縮された Hyper-V VHD をダウンロードします。
    - アプライアンスを作成し、Azure Migrate Server Assessment に接続できることを確認します。 
    - アプライアンスを初めて構成し、Azure Migrate プロジェクトに登録します。

    [こちらの記事](how-to-set-up-appliance-hyper-v.md)の手順に従って、アプライアンスを設定します。

## <a name="prepare-hyper-v-hosts"></a>Hyper-V ホストを準備する

1. Azure Migrate プロジェクトの **[サーバー]** で、 **[Azure Migrate: Server Migration]** の **[Discover]\(検出\)** をクリックします。
2. **[マシンの検出]**  >  **[マシンは仮想化されていますか?]** で、 **[はい。Hyper-V を使用します]** を選択します。
3. **[ターゲット リージョン]** で、マシンの移行先にする Azure リージョンを選択します。
6. **[移行先のリージョンが "<リージョン名>" であることを確認してください]** を選択します。
7. **[リソースの作成]** をクリックします。 これで、Azure Site Recovery コンテナーがバックグラウンドで作成されます。
    - Azure Migrate Server Migration を使用した移行を既に設定してある場合は、リソースが以前に設定されているため、このオプションは表示されません。
    - このボタンのクリック後は、このプロジェクトのターゲット リージョンを変更することはできません。
    - 後続のすべての移行は、このリージョンに対して行われます。
    
8. **[Hyper-V ホスト サーバーを準備する]** で、Hyper-V レプリケーション プロバイダーと登録キー ファイルをダウンロードします。
    - Azure Migrate Server Migration に Hyper-V ホストを登録するには、登録キーが必要です。
    - キーは生成後 5 日間有効です。

    ![プロバイダーとキーをダウンロードする](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

4. プロバイダー セットアップ ファイルと登録キー ファイルを、レプリケートする VM が実行されている各 Hyper-V ホスト (またはクラスター ノード) にコピーします。
5. 次の手順で説明するように、各ホストでプロバイダー セットアップ ファイルを実行します。
6. ホストにプロバイダーをインストールした後、 **[マシンの検出]** で **[Finalize registration]\(登録の完了\)** をクリックします。

    ![登録の終了処理](./media/tutorial-migrate-hyper-v/finalize-registration.png)

検出された VM が Azure Migrate Server Migration に表示されるまでに、登録を完了してから最大で 15 分かかることがあります。 VM が検出されると、 **[検出済みサーバー]** の数が増えます。

![検出済みサーバー](./media/tutorial-migrate-hyper-v/discovered-servers.png)

### <a name="register-hyper-v-hosts"></a>Hyper-V ホストを登録する

ダウンロードしたセットアップ ファイル (AzureSiteRecoveryProvider.exe) を、関連する各 Hyper-V ホストにインストールします。

1. 各ホストまたはクラスター ノードで、プロバイダー セットアップ ファイルを実行します。
2. プロバイダー セットアップ ウィザードの **[Microsoft Update]** で、Microsoft Update を使用してプロバイダーの更新プログラムを確認するように選択します。
3. **[インストール]** では、プロバイダーとエージェントの既定のインストール先のままにして **[インストール]** を選択します。
4. インストールした後、登録ウィザードの **[資格情報コンテナー設定]** で **[参照]** を選択し、 **[キー ファイル]** でダウンロードしたコンテナー キー ファイルを選択します。
5. **[プロキシの設定]** で、ホストで実行されているプロバイダーがインターネットに接続する方法を指定します。
    - アプライアンスがプロキシ サーバーの内側に配置されている場合は、プロキシ設定を指定する必要があります。
    - プロキシ名として、 **http://ip-address** または **http://FQDN** を指定します。 HTTPS プロキシ サーバーはサポートされていません。
   

6. プロバイダーが[必要な URL](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access) に到達できることを確認します。
7. **[登録]** で、ホストが登録された後、 **[完了]** をクリックします。

## <a name="replicate-hyper-v-vms"></a>Hyper-V VM をレプリケートする

検出が完了したら、Azure への Hyper-V VM のレプリケーションを開始できます。

1. Azure Migrate プロジェクトの **[サーバー]** の **[Azure Migrate: Server Migration]** の **[レプリケート]** をクリックします。
2. **[レプリケート]** の、 **[ソースの設定]**  >  **[マシンは仮想化されていますか?]** で、 **[はい。Hyper-V を使用します]** を選択します。 その後、 **[次へ:仮想マシン]** をクリックします。
3. **[仮想マシン]** で、レプリケートするマシンを選択します。
    - VM の評価を実行した場合は、評価結果から VM のサイズ設定とディスクの種類 (Premium/Standard) のレコメンデーションを適用できます。 これを行うには、 **[Azure Migrate Assessment から移行設定をインポートしますか?]** で **[はい]** オプションを選択します。
    - 評価を実行しなかった場合、または評価の設定を使用しない場合は、 **[いいえ]** オプションを選択します。
    - 評価の使用を選択した場合は、VM グループと評価名を選択します。

        ![評価を選択する](./media/tutorial-migrate-hyper-v/select-assessment.png)

4. **[仮想マシン]** で、必要に応じて VM を検索し、移行する各 VM を確認します。 その後、 **[次へ: ターゲット設定]** をクリックします。

    ![VM を選択する](./media/tutorial-migrate-hyper-v/select-vms.png)

5. **[ターゲット設定]** で、移行先のターゲット リージョン、サブスクリプション、移行後に Azure VM が配置されるリソース グループを選択します。
7. **[レプリケーション ストレージ アカウント]** で、Azure にレプリケートされたデータを格納する Azure ストレージ アカウントを選択します。
8. **[仮想ネットワーク]** で、移行後に Azure VM の参加先となる Azure VNet/サブネットを選択します。
9. **[Azure ハイブリッド特典]** で:

    - Azure ハイブリッド特典を適用しない場合は、 **[いいえ]** を選択します。 次に、 **[次へ]** をクリックします。
    - アクティブなソフトウェア アシュアランスまたは Windows Server のサブスクリプションの対象となっている Windows Server マシンがあり、移行中のマシンにその特典を適用する場合は、 **[はい]** を選択します。 その後、 **[次へ]** をクリックします。

    ![ターゲットの設定](./media/tutorial-migrate-hyper-v/target-settings.png)

10. **[コンピューティング]** で、VM の名前、サイズ、OS のディスクの種類、および可用性セットを確認します。 VM は [Azure の要件](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements)に準拠している必要があります。

    - **VM サイズ**: 評価のレコメンデーションを使用している場合は、[VM サイズ] ドロップダウンに推奨サイズが表示されます。 それ以外の場合は、Azure Migrate によって、Azure サブスクリプション内の最も近いサイズが選択されます。 または、 **[Azure VM サイズ]** で手動でサイズを選択します。 
    - **OS ディスク**:VM の OS (ブート) ディスクを指定します。 OS ディスクは、オペレーティング システムのブートローダーとインストーラーがあるディスクです。 
    - **可用性セット**:移行後に VM を Azure 可用性セットに配置する必要がある場合は、セットを指定します。 このセットは、移行用に指定するターゲット リソース グループ内に存在する必要があります。

    ![VM コンピューティングの設定](./media/tutorial-migrate-hyper-v/compute-settings.png)

11. **[ディスク]** で、VM ディスクを Azure にレプリケートするかどうかを指定し、Azure でのディスクの種類 (Standard SSD/HDD または Premium マネージド ディスク) を選択します。 その後、 **[次へ]** をクリックします。
    - レプリケーションからディスクを除外できます。
    - ディスクを除外すると、移行後は Azure VM 上に存在しなくなります。 

    ![ディスク](./media/tutorial-migrate-hyper-v/disks.png)

10. **[レプリケーションの確認と開始]** で、設定を確認し、 **[レプリケート]** をクリックして、サーバーの初期レプリケーションを開始します。

> [!NOTE]
> レプリケーションが開始する前にいつでも、 **[管理]**  >  **[マシンのレプリケート]** でレプリケーションの設定を更新できます。 レプリケーションの開始後は、設定を変更することはできません。

### <a name="provisioning-for-the-first-time"></a>初回のプロビジョニング

これが Azure Migrate プロジェクトでレプリケートする初めての VM の場合は、Azure Migrate Server Migration によって、プロジェクトと同じリソース グループに次のリソースが自動的にプロビジョニングされます。

- **サービス バス**: Azure Migrate Server Migration では、サービス バスを使用して、レプリケーション オーケストレーション メッセージがアプライアンスに送信されます。
- **ゲートウェイ ストレージ アカウント**: Server Migration では、ゲートウェイ ストレージ アカウントを使用して、レプリケートされる VM に関する状態情報が格納されます。
- **ログ ストレージ アカウント**: Azure Migrate アプライアンスでは、VM のレプリケーション ログがログ ストレージ アカウントにアップロードされます。 Azure Migrate により、レプリカ マネージド ディスクにレプリケーション情報が適用されます。
- **キー コンテナー**: Azure Migrate アプライアンスでは、キー コンテナーを使用して、サービス バスの接続文字列と、レプリケーションで使用されるストレージ アカウントのアクセス キーが管理されます。 準備のときに、キー コンテナーがストレージ アカウントにアクセスするために必要なアクセス許可を設定しておく必要があります。 [これらのアクセス許可を確認してください](tutorial-prepare-vmware.md#assign-role-assignment-permissions)。   


## <a name="track-and-monitor"></a>追跡して監視する


- **[レプリケート]** をクリックすると、レプリケーションの開始ジョブが開始されます。 
- レプリケーションの開始ジョブが正常に終了すると、マシンで Azure への初期レプリケーションが開始されます。
- 初期レプリケーションが完了すると、差分レプリケーションが開始されます。 オンプレミスのディスクに対する増分変更は、Azure に定期的にレプリケートされます。

ジョブの状態は、ポータルの通知で追跡できます。

レプリケーションの状態を監視するには、**サーバーをレプリケートしています** をクリックします (**Azure Migrate: Server Migration** 内)。
![レプリケーションの監視](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>テスト移行を実行する


差分レプリケーションが開始されるとき、Azure への完全な移行を実行する前に、VM のテスト移行を実行できます。 各マシンで少なくとも 1 回は、移行前にこれを実行することを強くお勧めします。

- テスト移行を実行すると、移行が想定どおりに動作することが確認されます。オンプレミスのマシンには影響はなく、稼働状態が維持され、レプリケーションが続行されます。 
- テスト移行では、レプリケートされたデータを使用して Azure VM を作成することによって、移行がシミュレートされます (通常は、自分の Azure サブスクリプション内の非運用 VNet に移行されます)。
- レプリケートされたテスト Azure VM を使用して、移行を検証し、アプリのテストを実行して、完全な移行前に問題に対処することができます。

テスト移行を実行するには、次のようにします。


1. **[移行の目標]**  >  **[サーバー]**  >  **[Azure Migrate: Server Migration]** で、 **[移行されたサーバーをテストします]** をクリックします。

     ![移行されたサーバーのテスト](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

2. テストする VM を右クリックし、 **[テスト移行]** をクリックします。

    ![テスト移行](./media/tutorial-migrate-hyper-v/test-migrate.png)

3. **[テスト移行]** で、移行後に Azure VM が配置される Azure VNet を選択します。 非運用環境の VNet を使用することをお勧めします。
4. **テスト移行**ジョブが開始されます。 ポータルの通知でジョブを監視します。
5. 移行の完了後、Azure portal の **[仮想マシン]** に、移行された Azure VM が表示されます。 マシン名には、サフィックス **-Test** が含まれています。
6. テストが完了したら、 **[マシンのレプリケート]** で Azure VM を右クリックし、 **[テスト移行をクリーンアップ]** をクリックします。

    ![移行のクリーンアップ](./media/tutorial-migrate-hyper-v/clean-up.png)


## <a name="migrate-vms"></a>VM の移行

テスト移行が想定どおりに動作することを確認したら、オンプレミスのマシンを移行できます。

1. Azure Migrate プロジェクトの **[サーバー]**  >  **[Azure Migrate: Server Migration]** で、 **[サーバーをレプリケートしています]** をクリックします。

    ![サーバーをレプリケートしています](./media/tutorial-migrate-hyper-v/replicate-servers.png)

2. **[マシンのレプリケート]** で VM を右クリックし、 **[移行]** を選択します。
3. **[移行]**  >  **[仮想マシンをシャットダウンし、データ損失のない計画された移行を実行しますか]** で、 **[はい]**  >  **[OK]** の順に選択します。
    - 既定では、Azure Migrate によってオンプレミスの VM がシャットダウンされ、前回のレプリケーションが発生した後に発生したすべての VM の変更を同期するためにオンデマンド レプリケーションが実行されます。 こうすることで、データ損失がなくなります。
    - VM をシャットダウンしたくない場合は、 **[いいえ]** を選択します
4. VM に対して移行ジョブが開始されます。 Azure 通知でジョブを追跡します。
5. ジョブが完了したら、 **[仮想マシン]** ページから VM を表示して管理できます。

## <a name="complete-the-migration"></a>移行を完了する

1. 移行が完了したら、VM を右クリックして、 **[移行を停止する]** を選択します。 これで、オンプレミスのマシンのレプリケーションが停止し、VM のレプリケーション状態情報がクリーンアップされます。
2. Azure VM の [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) または [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) エージェントを、移行されたマシンにインストールします。
3. データベース接続文字列、および Web サーバー構成の更新など、移行後のアプリの微調整を実行します。
4. Azure で現在実行されている移行後のアプリケーション上で、最終的なアプリケーションと移行の受け入れのテストを実行します。
5. 移行された Azure VM インスタンスへトラフィックを切り替えます。
6. ローカル VM インベントリからオンプレミスの VM を削除します。
7. ローカル バックアップからオンプレミスの VM を削除します。
8. Azure VM の新しい場所と IP アドレスを示すように内部ドキュメントを更新します。 

## <a name="post-migration-best-practices"></a>移行後のベスト プラクティス

- 復元性の向上:
    - Azure Backup サービスを使用して、Azure VM をバックアップすることで、データの安全性を保持します。 [詳細情報](../backup/quick-backup-vm-portal.md)。
    - Azure VM を Site Recovery のセカンダリ リージョンにレプリケートし、継続的にワークロードを実行して利用可能にします。 [詳細情報](../site-recovery/azure-to-azure-tutorial-enable-replication.md)。
- セキュリティの強化：
    - [Azure Security Center のジャスト イン タイム管理](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)を利用して、インバウンド トラフィック アクセスをロックダウンして制限します。
    - [ネットワーク セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/security-overview)を使って、ネットワーク トラフィックを管理エンドポイントに制限します。
    - [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) をデプロイして、ディスクをセキュリティ保護し、盗難や不正アクセスからデータを安全に保護します。
    - [IaaS リソースのセキュリティ保護](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/)に関する詳細を読み、[Azure Security Center](https://azure.microsoft.com/services/security-center/) を確認してください。
- 監視と管理：
-  [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) をデプロイして、リソースの使用率と消費量を監視します。


## <a name="next-steps"></a>次の手順

Azure クラウド導入フレームワークでの[クラウド移行の工程](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate)を調査します。
