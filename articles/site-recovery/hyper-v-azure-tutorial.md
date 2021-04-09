---
title: Azure Site Recovery を使用して Hyper-V のディザスター リカバリーを設定する
description: Site Recovery を使用して Azure にオンプレミス Hyper-V VM (VMM なし) のディザスター リカバリーを設定する方法について学習します。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 5ce98c785700301bba92926d7d5a243b614eca7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87504230"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Azure にオンプレミス Hyper-V VM のディザスター リカバリーを設定する

[Azure Site Recovery](site-recovery-overview.md) サービスは、オンプレミスのマシンと Azure 仮想マシン (VM) のレプリケーション、フェールオーバー、およびフェールバックの管理と調整を行うことでディザスター リカバリー戦略に貢献します。

これは、シリーズ 3 番目のチュートリアルです。 Azure にオンプレミス Hyper-V VM のディザスター リカバリーを設定する方法が示されます。 このチュートリアルは、Microsoft System Center Virtual Machine Manager (VMM) で管理されない Hyper-V VM に適用されます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * レプリケーションのソースとターゲットを選択します。
> * ソースのレプリケーション環境 (オンプレミスの Site Recovery コンポーネントを含む) と、ターゲットのレプリケーション環境を設定します。
> * レプリケーション ポリシーを作成します。
> * VM のレプリケーションを有効にします。

> [!NOTE]
> チュートリアルでは、シナリオの最も簡単なデプロイ パスを示します。 可能であれば既定のオプションを使い、すべての可能な設定とパスを示してはいません。 詳細な手順については、「[Site Recovery のドキュメント](./index.yml)」の **ハウツー ガイド** セクションにある記事を確認してください。



## <a name="before-you-begin"></a>開始する前に

これは、シリーズ 3 番目のチュートリアルです。 前のチュートリアルで以下のタスクが既に完了していることが前提となります。

1. [Azure を準備する](tutorial-prepare-azure.md)
2. [オンプレミスの Hyper-V を準備する](./hyper-v-prepare-on-premises-tutorial.md)

## <a name="select-a-replication-goal"></a>レプリケーションの目標を選ぶ

1. Azure portal で、 **[Recovery Services コンテナー]** に移動し、コンテナーを選択します。 コンテナー **ContosoVMVault** を前のチュートリアルで準備しました。
2. **[作業の開始]** で、 **[Site Recovery]** を選択してから、 **[インフラストラクチャの準備]** を選択します。
3. **[保護の目標]**  >  **[マシンのある場所]** で、 **[オンプレミス]** を選択します。
4. **[マシンをどこにレプリケートしますか?]** で、 **[To Azure]\(Azure\)** を選択します。
5. **[マシンは仮想化されていますか?]** で、 **[はい。Hyper-V を使用します]** を選択します。
6. **[Are you using System Center VMM to manage your Hyper-V hosts?]\(System Center VMM を使用して Hyper-V ホストを管理しますか?\)** で、 **[いいえ]** を選択します。
7. **[OK]** を選択します。

    ![[インフラストラクチャの準備] の [保護の目標] のオプションのスクリーンショット。](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>展開の計画を確認する

1. 大規模なデプロイを計画している場合は、 **[デプロイ計画]** で、ページ上のリンクから Hyper-V 用の Deployment Planner をダウンロードします。 Hyper-V のデプロイ計画の[詳細を確認](hyper-v-deployment-planner-overview.md)してください。
2. このチュートリアルでは、Deployment Planner は必要ありません。 **[デプロイ計画は完了していますか?]** で、 **[後で実行する]** を選択してから、 **[OK]** を選択します。

    ![[インフラストラクチャの準備] の [デプロイ計画] のオプションのスクリーンショット。](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>ソース環境をセットアップする

ソース環境を設定するには、Hyper-V サイトを作成し、レプリケートする VM を含む Hyper-V ホストをそのサイトに追加します。 その後、Azure Site Recovery Provider と Azure Recovery Services エージェントをダウンロードして各ホストにインストールし、コンテナーに Hyper-V サイトを登録します。

1. **[インフラストラクチャの準備]** で、 **[ソース]** を選択します。
2. **[ソースの準備]** で、 **[+ Hyper-V サイト]** を選択します。
3. **[Hyper-V サイトの作成]** で、サイト名を指定します。 ここでは、**ContosoHyperVSite** を使用しています。

    ![[インフラストラクチャの準備] での Hyper-V サイトの選択のスクリーンショット。](./media/hyper-v-azure-tutorial/hyperv-site.png)

4. サイトが作成されたら、 **[ソースの準備]**  >  **[手順 1: Hyper-V サイトを選択する]** の順に選択し、作成したサイトを選択します。
5. **[+ Hyper-V Server]** を選択します。

    ![[インフラストラクチャの準備] での Hyper-V サーバーの選択のスクリーンショット。](./media/hyper-v-azure-tutorial/hyperv-server.png)

6. Microsoft Azure Site Recovery Provider のインストーラーをダウンロードします。
7. コンテナー登録キーをダウンロードします。 このキーは、プロバイダーをインストールするのに必要です。 キーは生成後 5 日間有効です。

    ![プロバイダーと登録キーをダウンロードするためのオプションのスクリーンショット。](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>プロバイダーをインストールする

ダウンロードしたセットアップ ファイル (AzureSiteRecoveryProvider.exe) を、Hyper-V サイトに追加する各 Hyper-V ホストにインストールします。 セットアップによって Azure Site Recovery プロバイダーと Recovery Services エージェントが各 Hyper-V ホストにインストールされます。

1. セットアップ ファイルを実行します。
2. Azure Site Recovery プロバイダーのセットアップ ウィザードの **[Microsoft Update]** で、Microsoft Update を使用してプロバイダーの更新プログラムを確認するように選択します。
3. **[インストール]** では、プロバイダーとエージェントの既定のインストール先のままにして **[インストール]** を選択します。
4. インストール後、Microsoft Azure Site Recovery 登録ウィザードの **[資格情報コンテナー設定]** で **[参照]** を選択し、 **[キー ファイル]** でダウンロードしたコンテナー キー ファイルを選びます。
5. Azure Site Recovery のサブスクリプション、コンテナー名 (**ContosoVMVault**)、Hyper-V サーバーが属している Hyper-V サイト (**ContosoHyperVSite**) を指定します。
6. **[プロキシ設定]** で、 **[プロキシを使用せずに直接 Azure Site Recovery に接続する]** を選択します。
7. **[登録]** では、サーバーがコンテナーに登録された後、 **[完了]** を選択します。

Azure Site Recovery によって Hyper-V サーバーからメタデータが取得され、 **[Site Recovery インフラストラクチャ]** の  >  **[Hyper-V ホスト]** にサーバーが表示されます。 このプロセスには最大 30 分かかる可能性があります。

#### <a name="install-the-provider-on-a-hyper-v-core-server"></a>Hyper-V コア サーバーにプロバイダーをインストールする

Hyper-V コア サーバーを実行している場合は、セットアップ ファイルをダウンロードして、これらの手順に従います。

1. このコマンドを実行して、AzureSiteRecoveryProvider.exe からローカル ディレクトリにファイルを抽出します。

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2. `.\setupdr.exe /i` を実行します。 結果は %Programdata%\ASRLogs\DRASetupWizard.log に記録されます。

3. このコマンドを実行してサーバーを登録します。

    ```
    cd  "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```

## <a name="set-up-the-target-environment"></a>ターゲット環境をセットアップする

ターゲット リソースを選択して確認します。

1. **[インフラストラクチャの準備]**  >  **[ターゲット]** の順に選択します。
2. サブスクリプションと、フェールオーバー後に Azure VM が作成されるリソース グループ **ContosoRG** を選択します。
3. **Resource Manager"** デプロイメント モデルを選択します。

Site Recovery によって、互換性のある Azure ストレージ アカウントとネットワークが 1 つ以上あるかどうかが確認されます。

## <a name="set-up-a-replication-policy"></a>レプリケーション ポリシーを設定する

1. **[インフラストラクチャの準備]**  >  **[レプリケーションの設定]**  >  **[+ 作成と関連付け]** の順に選択します。
2. **[ポリシーの作成と関連付け]** で、ポリシー名を指定します。 ここでは、**ContosoReplicationPolicy** を使用しています。
3. このチュートリアルでは、既定の設定のままにします。
    - **[コピーの頻度]** には、(初期レプリケーション後に) 差分データをレプリケートする頻度が示されています。 既定の頻度は 5 分ごとです。
    - **[復旧ポイントの保持期間]** は、復旧ポイントが 2 時間保持されるように設定されています。 Hyper-V ホストでホストされている仮想マシンを保護する場合、リテンション期間の最大許容値は 24 時間です。
    - **[アプリ整合性スナップショットの頻度]** は、アプリ整合性スナップショットを含む復旧ポイントが 1 時間ごとに作成されるように設定されています。
    - **[初期レプリケーションの開始時刻]** には、初期レプリケーションがすぐに開始されることが示されています。
4. ポリシーが作成されたら、 **[OK]** を選択します。 新しいポリシーを作成すると、指定された Hyper-V サイトに自動的に関連付けられます。 このチュートリアルでは、それは **ContosoHyperVSite** です。

    ![Replication policy](./media/hyper-v-azure-tutorial/replication-policy.png)

## <a name="enable-replication"></a>レプリケーションを有効にする

1. **[アプリケーションのレプリケート]** で、 **[ソース]** を選択します。
2. **[ソース**] で "**ContosoHyperVSite**" サイトを選択します。 **[OK]** をクリックします。
3. **[ターゲット]** で、ターゲット (Azure)、コンテナーのサブスクリプション、および **Resource Manager** デプロイ モデルを確認します。
4. チュートリアルの設定を使用している場合は、レプリケートされるデータのために前のチュートリアルで作成された **contosovmsacct1910171607** ストレージ アカウントを選択します。 また、フェールオーバー後に Azure VM が配置される、**ContosoASRnet** ネットワークを選択します。
5. **[仮想マシン]**  >  **[選択]** で、レプリケートする VM を選択します。 **[OK]** をクリックします。

   **[ジョブ]**  >  **[Site Recovery ジョブ]** の順にクリックして、 **[保護を有効にする]** アクションの進行状況を追跡できます。 **[保護の最終処理]** ジョブが完了した後、初期レプリケーションが完了し、VM がフェールオーバーを実行できる状態になります。

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [ディザスター リカバリーのテストを実行する](tutorial-dr-drill-azure.md)
