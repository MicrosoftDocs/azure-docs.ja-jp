---
title: Azure Site Recovery を使用して Azure に VMM クラウドのオンプレミス Hyper-V VM のディザスター リカバリーを設定する | Microsoft Docs
description: Azure Site Recovery サービスを使用して、Azure に System Center VMM クラウドのオンプレミス Hyper-V VM のディザスター リカバリーを設定する方法について説明します。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 64559f653ba8a466de7bec10db34383b508e3e4b
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361292"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Azure に VMM クラウドのオンプレミス Hyper-V VM のディザスター リカバリーを設定する

この記事では、[Azure Site Recovery](site-recovery-overview.md) サービスを使って、Azure へのディザスター リカバリーのために、System Center Virtual Machine Manager (VMM) で管理されているオンプレミスの Hyper-V VM のレプリケーションを有効にする方法について説明します。 VMM を使用していない場合は、[こちらのチュートリアルに従ってください](hyper-v-azure-tutorial.md)。

これは、オンプレミスの VMware VM を対象に Azure へのディザスター リカバリーを設定する方法について説明するシリーズの 3 番目のチュートリアルです。 前のチュートリアルでは、Azure へのディザスター リカバリー用に[オンプレミスの Hyper-V 環境を準備](hyper-v-prepare-on-premises-tutorial.md)しました。 

このチュートリアルでは、以下の内容を学習します。


> [!div class="checklist"]
> * レプリケーションのソースとターゲットを選択します。
> * ソースのレプリケーション環境 (オンプレミスの Site Recovery コンポーネントなど) と、ターゲットのレプリケーション環境を設定する。
> * VMM VM ネットワークと Azure 仮想ネットワーク間でネットワーク マッピングを設定する。
> * レプリケーション ポリシーを作成する
> * VM のレプリケーションを有効にする


> [!NOTE]
> チュートリアルでは、シナリオの最も簡単なデプロイ パスを示します。 可能であれば既定のオプションを使い、すべての可能な設定とパスを示してはいません。 詳細な手順については、Site Recovery の目次のハウツー セクションにある記事を参照してください。

## <a name="before-you-begin"></a>開始する前に

これは、シリーズ 3 番目のチュートリアルです。 このチュートリアルでは、前のチュートリアルで以下のタスクがすでに完了していることを前提としています。

1. [Azure を準備する](tutorial-prepare-azure.md)
2. [オンプレミスの Hyper-V を準備する](tutorial-prepare-on-premises-hyper-v.md) これは、シリーズの 3 番目のチュートリアルです。 このチュートリアルでは、前のチュートリアルで以下のタスクがすでに完了していることを前提としています。


## <a name="select-a-replication-goal"></a>レプリケーションの目標を選ぶ

1. **[Recovery Services コンテナー]** で、コンテナーを選択します。 コンテナー **ContosoVMVault** を前のチュートリアルで準備しました。
2. **[作業の開始]** で、**[Site Recovery]** をクリックします。 次に、**[インフラストラクチャの準備]** をクリックします。
3. **[保護の目標]** > **[マシンのある場所]** で、**[オンプレミス]** を選択します。
4. **[マシンをどこにレプリケートしますか?]** で、**[To Azure]\(Azure\)** を選択します。
5. **[マシンは仮想化されていますか?]** で、**[はい。Hyper-V を使用します]** を選択します。
6. **[Are you using System Center VMM]\(System Center VMM を使用していますか\)** で **[はい]** を選びます。 次に、 **[OK]** をクリックします

    ![レプリケーションの目標](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)


## <a name="confirm-deployment-planning"></a>展開の計画を確認する

1. 大規模なデプロイを計画している場合は、**[デプロイ計画]** で、ページ上のリンクから Hyper-V 用の Deployment Planner をダウンロードします。 Hyper-V のデプロイ計画の[詳細を確認](hyper-v-deployment-planner-overview.md)してください。
2. このチュートリアルでは、Deployment Planner は必要ありません。 **[デプロイ計画は完了していますか?]** で、**[後で実行する]** を選択します。 次に、 **[OK]** をクリックします


## <a name="set-up-the-source-environment"></a>ソース環境をセットアップする

ソース環境を設定するときに、Azure Site Recovery プロバイダーを VMM サーバーにインストールし、そのサーバーをコンテナーに登録します。 Azure Recovery Services エージェントを各 Hyper-V ホストにインストールします。 

1. **[インフラストラクチャの準備]** で、**[ソース]** をクリックします。
2. **[ソースの準備]** で **[+ VMM]** をクリックして、VMM サーバーを追加します。 **[サーバーの追加]** で、**[System Center VMM サーバー]** に **[構成サーバー]** が表示されていることを確認します。
3. Microsoft Azure Site Recovery Provider のインストーラーをダウンロードします。
4. コンテナー登録キーをダウンロードします。 プロバイダーをセットアップする際に、これが必要になります。 キーは生成後 5 日間有効です。
5. Microsoft Azure Recovery Services エージェントのインストーラーをダウンロードします。

    ![ダウンロード](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>VMM サーバーにプロバイダーをインストールする

1. Azure Site Recovery プロバイダーのセットアップ ウィザードの **[Microsoft Update]** で、Microsoft Update を使用してプロバイダーの更新プログラムを確認するように選択します。
2. **[インストール]** で、既定の Provider のインストール先のままにして **[インストール]** をクリックします。 
3. インストール後、Microsoft Azure Site Recovery 登録ウィザードの **[資格情報コンテナー設定]** で **[参照]** をクリックし、**[キー ファイル]** でダウンロードしたコンテナー キー ファイルを選択します。
4. Azure Site Recovery サブスクリプションとコンテナー名 (**ContosoVMVault**) を指定します。 コンテナーで VMM サーバーを識別できるように、VMM サーバーにわかりやすい名前を指定します。
5. **[プロキシ設定]** で、**[プロキシを使用せずに直接 Azure Site Recovery に接続する]** を選択します。
6. データの暗号化で使用する証明書の場所は、既定の場所をそのまま使用します。 暗号化されたデータは、フェールオーバー時に暗号化が解除されます。
7. **[クラウドのメタデータの同期]** で、**[Sync cloud meta data to Site Recovery portal]\(Site Recovery ポータルにクラウド メタ データを同期する\)** を選択します。 この操作は、各サーバーで 1 回のみ実行する必要があります。 次に、**[登録]** をクリックします。
8. サーバーがコンテナーに登録されたら、**[完了]** をクリックします。

登録が完了すると、Azure Site Recovery によってサーバーからメタデータが取得され、**[Site Recovery インフラストラクチャ]** に VMM サーバーが表示されます。

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>Recovery Services エージェントを Hyper-V ホストにインストールする

レプリケーション対象の VM が含まれる各 Hyper-V ホストにエージェントをインストールします。

1. Microsoft Azure Recovery Services Agent セットアップ ウィザードの **[前提条件の確認]** で、**[次へ]** をクリックします。 不足している前提条件があると、それが自動的にインストールされます。
2. **[インストール設定]** では、インストール先とキャッシュの場所の既定値を使用します。 キャッシュ ドライブには 5 GB 以上のストレージが必要です。 600 GB 以上の空き領域があるドライブをお勧めします。 その後、 **[インストール]** をクリックします。
3. **[インストール]** で、インストールが完了したら、**[閉じる]** をクリックしてウィザードを完了します。

    ![エージェントのインストール](./media/hyper-v-vmm-azure-tutorial/mars-install.png)
    

## <a name="set-up-the-target-environment"></a>ターゲット環境をセットアップする

1. **[インフラストラクチャの準備]** > **[ターゲット]** の順にクリックします。
2. サブスクリプションと、フェールオーバー後に Azure VM が作成されるリソース グループ (**ContosoRG**) を選択します。
3. **Resource Manager**デプロイ モデルを選択します。

Site Recovery によって、互換性のある Azure ストレージ アカウントとネットワークが 1 つ以上あるかどうかが確認されます。


## <a name="configure-network-mapping"></a>ネットワーク マッピングの構成

1. **[Site Recovery Infrastructure (Site Recovery インフラストラクチャ)]** > **[ネットワーク マッピング]** > **[ネットワーク マッピング]** の順にクリックし、**[+ ネットワーク マッピング]** アイコンをクリックします。
2. **[ネットワーク マッピングの追加]** で、ソース VMM サーバーを選択します。 ターゲットとして **[Azure]** を選択します。
3. サブスクリプションとフェールオーバー後のデプロイメント モデルを確認します。
4. **[ソース ネットワーク]** で、ソース オンプレミス VM ネットワークを選択します。
5. **[ターゲット ネットワーク]** で、フェールオーバー後の作成時、レプリカ Azure VM が配置される Azure ネットワークを選択します。 次に、 **[OK]** をクリックします

    ![ネットワーク マッピング](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>レプリケーション ポリシーを設定する

1. **[インフラストラクチャの準備]** > **[レプリケーションの設定]** > **[+ 作成と関連付け]** の順にクリックします。
2. **[ポリシーの作成と関連付け]** で、ポリシー名を指定します。 ここでは、**ContosoReplicationPolicy** を使用しています。
3. 既定の設定のままにして **[OK]** をクリックします。
    - **[コピーの頻度]** は、差分データを (初期レプリケーション後) 5 分ごとにレプリケートするように設定されています。
    - **[復旧ポイントの保持期間]** は、各復旧ポイントが 2 時間保持されることを示します。
    - **[アプリ整合性スナップショットの頻度]** は、アプリ整合性スナップショットを含む復旧ポイントが 1 時間ごとに作成されるように設定されています。
    - **[初期レプリケーションの開始時刻]** は、初期レプリケーションを直ちに開始するように設定されています。
    - **[Azure に格納されるデータの暗号化]** - 既定の設定の **[オフ]** では、Azure の保存データは暗号化されません。
4. ポリシーが作成されたら、**[OK]** をクリックします。 新しいポリシーを作成すると、自動的に VMM クラウドに関連付けられます。

## <a name="enable-replication"></a>レプリケーションを有効にする

1. **[アプリケーションのレプリケート]** で、**[ソース]** をクリックします。 
2. **[ソース]** で、VMM クラウドを選択します。 次に、 **[OK]** をクリックします
3. **[ターゲット]** で、Azure がターゲットのコンテナー サブスクリプションであることを確認して、**Resource Manager** モデルを選択します。
4. **contosovmsacct1910171607** ストレージ アカウントと、**ContosoASRnet** Azure ネットワークを選択します。
5. **[仮想マシン]** > **[選択]** で、レプリケートする VM を選択します。 次に、 **[OK]** をクリックします

   **[ジョブ]** > **[Site Recovery ジョブ]** の順にクリックして、**[保護を有効にする]** アクションの進行状況を追跡できます。 **[保護の最終処理]** ジョブが完了すると、初期レプリケーションが完了し、VM がフェールオーバーを実行できる状態になります。



## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [ディザスター リカバリー訓練を実行する](tutorial-dr-drill-azure.md)
