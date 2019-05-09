---
title: Azure Site Recovery を使用して Azure にオンプレミス Hyper-V VM (VMM なし) のディザスター リカバリーを設定する |Microsoft Docs
description: Azure Site Recovery サービスを使用して Azure にオンプレミス Hyper-V VM (VMM なし) のディザスター リカバリーを設定する方法について説明します。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: da643a4d7a1dc74385b3854c1952af5ba93bd241
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59358097"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Azure にオンプレミス Hyper-V VM のディザスター リカバリーを設定する

c

> [!div class="checklist"]
> * レプリケーションのソースとターゲットを選択します。
> * ソースのレプリケーション環境 (オンプレミスの Site Recovery コンポーネントなど) と、ターゲットのレプリケーション環境を設定します。
> * レプリケーション ポリシーを作成します。
> * VM のレプリケーションを有効にします。

> [!NOTE]
> チュートリアルでは、シナリオの最も簡単なデプロイ パスを示します。 可能であれば既定のオプションを使い、すべての可能な設定とパスを示してはいません。 詳細な手順については、Site Recovery の目次のハウツー セクションにある記事を参照してください。

## <a name="before-you-begin"></a>開始する前に
これは、シリーズ 3 番目のチュートリアルです。 このチュートリアルでは、前のチュートリアルで以下のタスクがすでに完了していることを前提としています。

1. [Azure を準備する](tutorial-prepare-azure.md)
2. [オンプレミスの Hyper-V を準備する](tutorial-prepare-on-premises-hyper-v.md)



## <a name="select-a-replication-goal"></a>レプリケーションの目標を選ぶ

1. **[Recovery Services コンテナー]** で、コンテナーを選択します。 コンテナー **ContosoVMVault** を前のチュートリアルで準備しました。
2. **[作業の開始]** で、**[Site Recovery]** をクリックします。 次に、**[インフラストラクチャの準備]** をクリックします。
3. **[保護の目標]** > **[マシンのある場所]** で、**[オンプレミス]** を選択します。
4. **[マシンをどこにレプリケートしますか]** で、**[To Azure]\(Azure\)** を選択します。
5. **[マシンは仮想化されていますか]** で、**[はい。Hyper-V を使用します]** を選択します。
6. **[Are you using System Center VMM to manage your Hyper-V hosts]\(System Center VMM を使用して Hyper-V ホストを管理しますか\)** で、**[いいえ]** を選択します。 次に、 **[OK]** をクリックします

    ![レプリケーションの目標](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>展開の計画を確認する

1. 大規模なデプロイを計画している場合は、**[デプロイ計画]** で、ページ上のリンクから Hyper-V 用の Deployment Planner をダウンロードします。 Hyper-V のデプロイ計画の[詳細を確認](hyper-v-deployment-planner-overview.md)してください。
2. このチュートリアルでは、Deployment Planner は必要ありません。 **[デプロイ計画は完了していますか]** で、**[後で実行する]** を選択します。 次に、 **[OK]** をクリックします

    ![展開の計画](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>ソース環境をセットアップする

ソース環境を設定するには、Hyper-V サイトを作成し、レプリケートする VM を含む Hyper-V ホストをそのサイトに追加します。 次に、Azure Site Recovery Provider と Azure Recovery Services エージェントをダウンロードして各ホストにインストールし、コンテナーに Hyper-V サイトを登録します。 

1. **[インフラストラクチャの準備]** で、**[ソース]** をクリックします。
2. **[ソースの準備]** で、**[+Hyper-V サイト]** をクリックします。
3. **[Hyper-V サイトの作成]** で、サイト名を指定します。 ここでは、**ContosoHyperVSite** を使用しています。

    ![Hyper-V サイト](./media/hyper-v-azure-tutorial/hyperv-site.png)

3. サイトが作成されたら、**[ソースの準備]** > **[手順 1: Hyper-V サイトを選択する]** の順に選択し、作成したサイトを選択します。
4. **[+Hyper-V Server]** をクリックします。

    ![Hyper-V サーバー](./media/hyper-v-azure-tutorial/hyperv-server.png)

4. Microsoft Azure Site Recovery Provider のインストーラーをダウンロードします。
6. コンテナー登録キーをダウンロードします。 このキーは、プロバイダーをインストールするのに必要です。 キーは生成後 5 日間有効です。

    ![プロバイダーのダウンロード](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>プロバイダーをインストールする

ダウンロードしたセットアップ ファイル (AzureSiteRecoveryProvider.exe) を、Hyper-V サイトに追加する各 Hyper-V ホストにインストールします。 セットアップによって Azure Site Recovery プロバイダーと Recovery Services エージェントが各 Hyper-V ホストにインストールされます。

1. セットアップ ファイルを実行します。
2. Azure Site Recovery プロバイダーのセットアップ ウィザードの **[Microsoft Update]** で、Microsoft Update を使用してプロバイダーの更新プログラムを確認するように選択します。
3. **[インストール]** で、プロバイダーとエージェントの既定のインストール先のままにして **[インストール]** をクリックします。
4. インストール後、Microsoft Azure Site Recovery 登録ウィザードの **[資格情報コンテナー設定]** で **[参照]** をクリックし、**[キー ファイル]** でダウンロードしたコンテナー キー ファイルを選択します。 
5. Azure Site Recovery のサブスクリプション、コンテナー名 (**ContosoVMVault**)、Hyper-V サーバーが属している Hyper-V サイト (**ContosoHyperVSite**) を指定します。
6. **[プロキシ設定]** で、**[プロキシを使用せずに直接 Azure Site Recovery に接続する]** を選択します。
7. **[登録]** で、サーバーがコンテナーに登録されたら **[完了]** をクリックします。

Azure Site Recovery によって Hyper-V サーバーからメタデータが取得され、**[Site Recovery インフラストラクチャ]** の  > **[Hyper-V ホスト]** にサーバーが表示されます。 このプロセスには最大 30 分かかる可能性があります。        

#### <a name="install-on-hyper-v-core-server"></a>Hyper-V コア サーバーにインストールする

Hyper-V コア サーバーを稼働している場合は、セットアップ ファイルをダウンロードして次の手順を実行します。

1. 以下を実行して、AzureSiteRecoveryProvider.exe からローカル ディレクトリにファイルを展開します

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2.  `.\setupdr.exe /i を実行します。 結果は %Programdata%\ASRLogs\DRASetupWizard.log に記録されます。

3.  次のコマンドを使用してサーバーを登録します。

    ```
    cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```
 

## <a name="set-up-the-target-environment"></a>ターゲット環境をセットアップする

ターゲット リソースを選択して確認します。 

1. **[インフラストラクチャの準備]** > **[ターゲット]** の順にクリックします。
2. サブスクリプションと、フェールオーバー後に Azure VM が作成されるリソース グループ **ContosoRG** を選択します。
3. **Resource Manager"** デプロイメント モデルを選択します。

Site Recovery によって、互換性のある Azure ストレージ アカウントとネットワークが 1 つ以上あるかどうかが確認されます。


## <a name="set-up-a-replication-policy"></a>レプリケーション ポリシーを設定する

1. **[インフラストラクチャの準備]** > **[レプリケーションの設定]** > **[+ 作成と関連付け]** の順にクリックします。
2. **[ポリシーの作成と関連付け]** で、ポリシー名を指定します。 ここでは、**ContosoReplicationPolicy** を使用しています。
3. このチュートリアルでは、既定の設定のままにします。
    - **[コピーの頻度]** は、(初期レプリケーション後に) 差分データをレプリケートする頻度を示します (既定では 5 分ごと)。
    - **[復旧ポイントの保持期間]** は、復旧ポイントが 2 時間保持されるように設定されています。
    - **[アプリ整合性スナップショットの頻度]** は、アプリ整合性スナップショットを含む復旧ポイントが 1 時間ごとに作成されるように設定されています。
    - **[初期レプリケーションの開始時刻]** は、初期レプリケーションを直ちに開始するように設定されています。
4. ポリシーが作成されたら、**[OK]** をクリックします。 新しいポリシーを作成すると、指定した Hyper-V サイト (このチュートリアルでは **ContosoHyperVSite**) に自動的に関連付けられます

    ![Replication policy](./media/hyper-v-azure-tutorial/replication-policy.png)


## <a name="enable-replication"></a>レプリケーションを有効にする


1. **[アプリケーションのレプリケート]** で、**[ソース]** をクリックします。 
2. **[ソース**] で "**ContosoHyperVSite**" サイトを選択します。 次に、 **[OK]** をクリックします
3. **[ターゲット]** で、Azure がレプリケーション対象であること、コンテナーのサブスクリプション、および**Resource Manager** モデルを確認します。
4. チュートリアルの設定を使用している場合は、レプリケートされるデータのために前のチュートリアルで作成した **contosovmsacct1910171607** ストレージ アカウントと (フェールオーバー後に Azure VM が配置される) **ContosoASRnet** ネットワークを選択します。
5. **[仮想マシン]** > **[選択]** で、レプリケートする VM を選択します。 次に、 **[OK]** をクリックします

   **[ジョブ]** > **[Site Recovery ジョブ]** の順にクリックして、**[保護を有効にする]** アクションの進行状況を追跡できます。 **[保護の最終処理]** ジョブが完了すると、初期レプリケーションが完了し、仮想マシンがフェールオーバーを実行できる状態になります。

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [ディザスター リカバリーのテストを実行する](tutorial-dr-drill-azure.md)
