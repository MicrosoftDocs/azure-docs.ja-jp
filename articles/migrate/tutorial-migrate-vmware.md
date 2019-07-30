---
title: エージェントレスの Azure Migrate Server Migration を使用してオンプレミスの VMware VM を Azure に移行する | Microsoft Docs
description: Azure Migrate を使用して、オンプレミスの VMware VM の Azure へのエージェントレス移行を実行する方法について説明します。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 885c877f219f59ab5049cf7b8e01243077d6d3eb
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348384"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>VMware VM を Azure に移行する (エージェントレス)

この記事では、Azure Migrate Server Migration ツールによるエージェントレス移行を使用して、オンプレミスの VMware VM を Azure に移行する方法について説明します。

[Azure Migrate](migrate-services-overview.md) では、オンプレミスのアプリとワークロード、および AWS または GCP VM インスタンスの検出、評価、Azure への移行を追跡するための中央ハブが提供されます。 このハブには、評価および移行のための Azure Migrate ツールのほか、サードパーティの独立系ソフトウェア ベンダー (ISV) のオファリングが用意されています。

これはシリーズの 3 番目のチュートリアルであり、Azure Migrate の Server Assessment と Server Migration を使用して VMware VM を評価し、Azure に移行する方法を示します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 移行のために VM を準備します。
> * Azure Migrate Server Migration ツールを追加します。
> * 移行したい VM を検出します。
> * VM のレプリケートを開始します。
> * テスト移行を実行して、すべてが想定どおりに動作していることを確認します。
> * 完全な VM 移行を実行します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。

## <a name="migration-methods"></a>移行の方法

Azure Migrate Server Migration ツールを使用して、VMware VM を Azure に移行できます。 このツールには、VMware VM の移行に関していくつかのオプションが用意されています。

- エージェントレス レプリケーションを使用した移行。 何もインストールすることなく VM を移行します。
- レプリケーション用のエージェントを使用した移行。 レプリケーションのためにエージェントを VM にインストールします。

エージェントレスとエージェントベース、いずれの移行を使用するかを決定するには、これらの記事を確認してください。

- エージェントレス移行の[しくみを学習](server-migrate-overview.md)し、[制限事項を確認](server-migrate-overview.md#agentless-migration-limitations)してください。
- エージェントベースの方法を使用したい場合は、[この記事をお読みください](tutorial-migrate-vmware-agent.md)。

## <a name="prerequisites"></a>前提条件

このチュートリアルを始める前に、次の準備が必要です。

1. VMware の移行アーキテクチャを[理解](migrate-architecture.md)します。
2. このシリーズの[最初のチュートリアルを完了](tutorial-prepare-vmware.md)して、移行のために Azure と VMware を設定します。 具体的には、このチュートリアルでは以下を行う必要があります。
    - 移行のために [Azure を準備する](tutorial-prepare-vmware.md#prepare-azure)。
    - 移行のために[オンプレミス環境を準備する](tutorial-prepare-vmware.md#prepare-for-agentless-vmware-migration)。
    
3. Azure に移行する前に、Azure Migrate Server Assessment を使用して VMware VM を評価してみることをお勧めします。 評価を設定するには、このシリーズの [2 番目のチュートリアルを完了](tutorial-assess-vmware.md)します。 VM を評価したくない場合、このチュートリアルはスキップできます。 評価を試してみることをお勧めしますが、移行を試みる前に評価を実行しなければならないわけではありません。



## <a name="add-the-azure-migrate-server-migration-tool"></a>Azure Migrate Server Migration ツールを追加する

2 番目のチュートリアルに従って VMware VM を評価していない場合は、[これらの手順に従って](how-to-add-tool-first-time.md) Azure Migrate プロジェクトを設定し、Azure Migrate Server Migration ツールを選択する必要があります。 

2 番目のチュートリアルに従って Azure Migrate プロジェクトを既に設定してある場合は、次のとおりに Azure Migrate Server Migration ツールを追加します。

1. Azure Migrate プロジェクトで、 **[概要]** をクリックします。 
2. **[サーバーの検出、評価、移行]** で、 **[サーバーの評価と移行]** をクリックします。

     ![サーバーの評価と移行](./media/tutorial-migrate-vmware/assess-migrate.png)

3. **[移行ツール]** で、 **[Click here to add a migration tool when you are ready to migrate]\(移行する準備ができたら、ここをクリックして移行ツールを追加してください\)** を選択します。

    ![ツールの選択](./media/tutorial-migrate-vmware/select-migration-tool.png)

4. ツールの一覧で、次を選択します: **[Azure Migrate: Server Migration]**  >  **[ツールを追加]**

    ![Server Migration ツール](./media/tutorial-migrate-vmware/server-migration-tool.png)

## <a name="set-up-the-azure-migrate-appliance"></a>Azure Migrate アプライアンスを設定する

Azure Migrate Server Migration では、軽量の VMware VM アプライアンスを実行します。 このアプライアンスによって VM の検出が実行され、VM のメタデータとパフォーマンス データが Azure Migrate Server Migration に送信されます。 Azure Migrate Server Assessment ツールでも、同じアプライアンスが使用されます。

2 番目のチュートリアルに従って VMware VM を評価した場合は、そのチュートリアルの中でアプライアンスを既に設定してあります。 そのチュートリアルに従っていない場合は、ここでアプライアンスを設定する必要があります。 このためには、次の手順に従います。 

- OVA テンプレート ファイルをダウンロードし、それを vCenter Server にインポートします。
- アプライアンスを作成し、それが Azure Migrate Server Assessment に接続できることを確認します。 
- アプライアンスを初めて構成し、Azure Migrate プロジェクトに登録します。

[こちらの記事](how-to-set-up-appliance-vmware.md)の手順に従って、アプライアンスを設定します。


## <a name="prepare-vms-for-migration"></a>移行のために VM を準備する

VM を Azure に確実に移行できるよう、Azure Migrate では VM にいくつかの変更を行う必要があります。

- 一部の[オペレーティング システム](server-migrate-overview.md#agentless-migration-limitations)では、これらの変更が Azure Migrate によって自動的に行われます。
- これらのいずれのオペレーティング システムも備えていない VM を移行する場合は、手順に従って VM を準備してください。
- 移行を開始する前にこれらの変更を行うことが重要です。 変更を行う前に VM を移行すると、Azure で VM が起動しない可能性があります。
- オンプレミスの VM に対して行う構成変更は、VM のレプリケーションが有効になった後に、Azure にレプリケートされます。 変更が確実にレプリケートされるよう、移行先の復旧ポイントは、オンプレミスで行われた構成変更の時点よりも後にしてください。


### <a name="prepare-windows-server-vms"></a>Windows Server VM の準備

**アクション** | **詳細** | **手順**
--- | --- | ---
Azure VM 内の Windows ボリュームで、オンプレミスの VM と同じドライブ文字の割り当てが使用されるようにする。 | SAN ポリシーを Online All に構成します。 | 1.管理者アカウントで VM にサインインし、コマンド ウィンドウを開きます。<br/> 2.「**diskpart**」と入力して、Diskpart ユーティリティを実行します。<br/> 手順 3.「**SAN POLICY=OnlineAll**」と入力します<br/> 4.「Exit」と入力して Diskpart を終了し、コマンド プロンプトを閉じます。
Azure VM で Azure シリアル アクセス コンソールを有効にする | これはトラブルシューティングに役立ちます。 VM を再起動する必要はありません。 ディスク イメージを使用して Azure VM が起動します。これは、新しい VM の再起動に相当します。 | [これらの手順](https://docs.microsoft.com/azure/virtual-machines/windows/serial-console)に従って、有効化してください。
Hyper-V ゲスト統合をインストールする | Windows Server 2003 を実行中のマシンを移行する場合は、Hyper-V ゲスト統合サービスを VM のオペレーティング システムにインストールしてください。 | [詳細情報](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services)。
リモート デスクトップ | VM でリモート デスクトップを有効にして、どのネットワーク プロファイルでも Windows ファイアウォールがリモート デスクトップ アクセスをブロックしていないことを確認します。 | [詳細情報](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access)。

### <a name="prepare-linux-vms"></a>Linux VM の準備

**アクション** | **詳細** 
--- | --- | ---
Hyper-V Linux 統合サービスをインストールする | Linux ディストリビューションの新しいバージョンには、ほとんどの場合、既定でこれが含まれています。
必要な Hyper-V ドライバーが含まれるように Linux の init イメージをリビルドする | これにより、Azure で確実に VM が起動します。これが必要なのは一部のディストリビューションだけです。
Azure シリアル コンソールのログ記録を有効にする | これはトラブルシューティングに役立ちます。 VM を再起動する必要はありません。 ディスク イメージを使用して Azure VM が起動します。これは、新しい VM の再起動に相当します。<br/> [これらの手順](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console)に従って、有効化してください。
デバイスのマップ ファイルを更新する | 永続的なデバイス識別子を使用するよう、デバイス名とボリュームの関連付けが含まれているデバイスのマップ ファイルを更新します
fstab エントリを更新する | 永続的なボリューム識別子を使用するよう、エントリを更新します。
Udev ルールを削除する | MAC アドレスなどに基づいてインターフェイス名を予約する Udev ルールを削除します。
ネットワーク インターフェイスを更新する | DHCP に基づいて IP アドレスを受け取るよう、ネットワーク インターフェイスを更新します。
SSH を有効にする | SSH を有効にして、再起動時に自動的に開始するよう sshd サービスを設定してください。<br/> 受信 SSH 接続要求が、OS ファイアウォールまたはスクリプト実行可能なルールによってブロックされないようにします。

Azure での Linux VM の実行については、それらの手順について説明している[こちらの記事に従って](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic)ください。いくつかの人気のある Linux ディストリビューションについては、手順を収集してください。  


## <a name="replicate-vms"></a>VM をレプリケートする

検出が完了したら、Azure への VMware VM のレプリケーションを開始できます。

1. Azure Migrate プロジェクトの **[サーバー]** の **[Azure Migrate: Server Migration]** で、 **[レプリケート]** をクリックします。

    ![VM をレプリケートする](./media/tutorial-migrate-vmware/select-replicate.png)

2. **[レプリケート]** > **[ソースの設定]**  >  **[マシンは仮想化されていますか?]** で、 **[はい (VMware vSphere の場合)]** を選択します。
3. **[オンプレミスのアプライアンス]** で、自分が設定した Azure Migrate アプライアンスの名前、 **[OK]** の順に選択します。 

    ![ソースの設定](./media/tutorial-migrate-vmware/source-settings.png)

    - この手順では、チュートリアルの完了時にアプライアンスを既に設定してあることを想定しています。
    - アプライアンスを設定していない場合は、[こちらの記事](how-to-set-up-appliance-vmware.md)の手順に従ってください。

4. **[仮想マシン]** で、レプリケートしたいマシンを選択します。
    - VM の評価を実行した場合は、評価結果から VM のサイズ設定とディスクの種類 (Premium または Standard) の推奨事項を適用できます。 これを行うには、 **[Azure Migrate Assessment から移行設定をインポートしますか?]** で **[はい]** オプションを選択します。
    - 評価を実行しなかった場合、または評価の設定を使用しない場合は、 **[いいえ]** オプションを選択します。
    - 評価の使用を選択した場合は、VM グループと評価名を選択します。

    ![評価の選択](./media/tutorial-migrate-vmware/select-assessment.png)

5. **[仮想マシン]** で、必要に応じて VM を検索し、移行したい各 VM を確認します。 その後、 **[次へ:ターゲット設定]** をクリックします。

    ![VM を選択する](./media/tutorial-migrate-vmware/select-vms.png)

6. **[ターゲット設定]** で、サブスクリプションと、移行先となるターゲット リージョンを選択し、移行後に Azure VM が配置されるリソース グループを指定します。 **[仮想ネットワーク]** で、移行後に Azure VM の参加先となる Azure VNet およびサブネットを選択します。
7. **[Azure ハイブリッド特典]** で、

    - Azure ハイブリッド特典を適用しない場合は、 **[いいえ]** を選択します。 その後、 **[次へ]** をクリックします。
    - アクティブなソフトウェア アシュアランスまたは Windows Server のサブスクリプションの対象となっている Windows Server マシンがあり、移行中のマシンにその特典を適用する場合は、 **[はい]** を選択します。 その後、 **[次へ]** をクリックします。

    ![ターゲットの設定](./media/tutorial-migrate-vmware/target-settings.png)

8. **[コンピューティング]** で、VM の名前、サイズ、OS ディスクの種類、可用性セットを確認します。 VM は [Azure の要件](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements)に準拠している必要があります。

    - **VM サイズ**: 評価の推奨事項を使用している場合は、[VM サイズ] ドロップダウンに推奨サイズが表示されます。 それ以外の場合は、Azure Migrate によって、Azure サブスクリプション内の最も近いサイズが選択されます。 または、 **[Azure VM サイズ]** でサイズを手動で選択します。 
    - **OS ディスク**:VM の OS (ブート) ディスクを指定します。 OS ディスクは、オペレーティング システムのブートローダーとインストーラーがあるディスクです。 
    - **可用性セット**:移行後に VM を Azure 可用性セットに配置する必要がある場合は、セットを指定します。 このセットは、移行用に指定するターゲット リソース グループ内に存在する必要があります。

    ![VM コンピューティングの設定](./media/tutorial-migrate-vmware/compute-settings.png)

9. **[ディスク]** で、VM ディスクを Azure にレプリケートするかどうかを指定し、Azure でのディスクの種類 (Standard SSD か HDD、または Premium マネージド ディスク) を選択します。 その後、 **[次へ]** をクリックします。
    - レプリケーションからディスクを除外できます。
    - ディスクは除外すると、移行後に Azure VM 上に存在しなくなります。 

    ![ディスク](./media/tutorial-migrate-vmware/disks.png)

10. **[レプリケーションの確認と開始]** で、設定を確認し、 **[レプリケート]** をクリックして、サーバーの初期レプリケーションを開始します。

> [!NOTE]
> レプリケーションを開始する前であれば、 **[管理]**  >  **[マシンのレプリケート]** でレプリケーションの設定をいつでも更新できます。 レプリケーションの開始後は、設定を変更することができません。

### <a name="provisioning-for-the-first-time"></a>初回のプロビジョニング

これが Azure Migrate プロジェクトでレプリケートする初めての VM である場合、Azure Migrate Server Migration によって、プロジェクトと同じリソース グループにこれらのリソースが自動的にプロビジョニングされます。

- **サービス バス**: Azure Migrate Server Migration では、サービス バスを使用して、レプリケーション オーケストレーション メッセージをアプライアンスに送信します。
- **ゲートウェイ ストレージ アカウント**: Server Migration では、ゲートウェイ ストレージ アカウントを使用して、レプリケートされる VM に関する状態情報を格納します。
- **ログ ストレージ アカウント**: Azure Migrate アプライアンスでは、VM のレプリケーション ログをログ ストレージ アカウントにアップロードします。 Azure Migrate により、レプリケーション情報がレプリカ マネージド ディスクに適用されます。
- **キー コンテナー**: Azure Migrate アプライアンスでは、キー コンテナーを使用して、サービス バスの接続文字列と、レプリケーションで使用されるストレージ アカウントのアクセス キーを管理します。 準備を行ったときに、キー コンテナーがストレージ アカウントにアクセスするために必要なアクセス許可を設定したはずです。 [これらのアクセス許可を確認してください](tutorial-prepare-vmware.md#assign-role-assignment-permissions)。   


## <a name="track-and-monitor"></a>追跡して監視する


- **[レプリケート]** をクリックすると、レプリケーションの開始ジョブが開始されます。 
- レプリケーションの開始ジョブが正常に終了すると、マシンで Azure への初期レプリケーションが開始されます。
- 初期レプリケーション中に、VM スナップショットが作成されます。 スナップショットのディスク データは、Azure のレプリカ マネージド ディスクにレプリケートされます。
- 初期レプリケーションが完了すると、差分レプリケーションが開始されます。 オンプレミスのディスクに対する増分変更は、Azure のレプリカ ディスクに定期的にレプリケートされます。

ジョブの状態は、ポータルの通知で追跡できます。

レプリケーションの状態を監視するには、**サーバーをレプリケートしています** をクリックします (**Azure Migrate: Server Migration** 内)。
![レプリケーションの監視](./media/tutorial-migrate-vmware/replicating-servers.png)




## <a name="run-a-test-migration"></a>テスト移行を実行する


差分レプリケーションが開始されるとき、Azure への完全な移行を実行する前に、VM のテスト移行を実行できます。 各マシンで少なくとも 1 回は、移行前にこれを実行することを強くお勧めします。

- テスト移行を実行すると、移行が想定どおりに動作することが確認されます。オンプレミスのマシンに影響はなく、稼働状態が維持され、レプリケーションが続行されます。 
- テスト移行では、レプリケートされたデータを使用して Azure VM を作成することによって、移行がシミュレートされます (通常は、自分の Azure サブスクリプション内の非運用 VNet に移行されます)。
- レプリケートされたテスト Azure VM を使用して、移行を検証し、アプリのテストを実行して、完全な移行前に問題に対処することができます。

テスト移行を実行するには、次のようにします。


1. **[移行の目標]**  >  **[サーバー]**  >  **[Azure Migrate: Server Migration]** で、 **[移行されたサーバーをテストします]** をクリックします。

     ![移行されたサーバーのテスト](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. テストする VM を右クリックし、 **[テスト移行]** をクリックします。

    ![テスト移行](./media/tutorial-migrate-vmware/test-migrate.png)

3. **[テスト移行]** で、移行後に Azure VM が配置される Azure VNet を選択します。 非運用環境の VNet を使用することをお勧めします。
4. **テスト移行**ジョブが開始されます。 ポータルの通知でジョブを監視します。
5. 移行の完了後、Azure portal の **[仮想マシン]** で、移行された Azure VM を確認します。 マシン名には、サフィックス **-Test** が含まれています。
6. テストが完了したら、 **[マシンのレプリケート]** で Azure VM を右クリックし、 **[テスト移行をクリーンアップ]** をクリックします。

    ![移行のクリーンアップ](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>VM の移行

テスト移行が想定どおりに動作することを確認したら、オンプレミスのマシンを移行できます。

1. Azure Migrate プロジェクトの **[サーバー]**  >  **[Azure Migrate: Server Migration]** で、 **[サーバーをレプリケートしています]** をクリックします。

    ![サーバーをレプリケートしています](./media/tutorial-migrate-vmware/replicate-servers.png)

2. **[マシンのレプリケート]** で VM を右クリックし、 **[移行]** を選択します。
3. **[移行]**  >  **[仮想マシンをシャットダウンし、データ損失のない計画された移行を実行しますか]** で、 **[はい]**  >  **[OK]** の順に選択します。
    - 既定では、Azure Migrate によってオンプレミスの VM がシャットダウンされ、前回のレプリケーションが発生した後に発生したすべての VM の変更を同期するためにオンデマンド レプリケーションが実行されます。 こうすることで、データ損失がなくなります。
    - VM をシャットダウンしたくない場合は、 **[いいえ]** を選択します
4. VM に対して移行ジョブが開始されます。 Azure 通知でジョブを追跡します。
5. ジョブが完了したら、 **[仮想マシン]** ページで VM を表示して管理できます。

## <a name="complete-the-migration"></a>移行を完了する

1. 移行が完了したら、VM を右クリックして、 **[移行を停止する]** を選択します。 これで、オンプレミスのマシンのレプリケーションが停止し、VM のレプリケーション状態情報がクリーンアップされます。
2. Azure VM の [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) または [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) エージェントを、移行されたマシンにインストールします。
3. データベース接続文字列、および Web サーバー構成の更新など、移行後のアプリの微調整を実行します。
4. Azure で現在実行されている移行後のアプリケーション上で、最終的なアプリケーションと移行の受け入れのテストを実行します。
5. 移行された Azure VM インスタンスにトラフィックを切り替えます。
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
