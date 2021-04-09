---
title: Azure Site Recovery を使用して Hyper-V (VMM 使用) のディザスター リカバリーを設定する
description: Site Recovery を使用して、Azure に System Center VMM クラウドのオンプレミス Hyper-V VM のディザスター リカバリーを設定する方法について説明します。
ms.topic: tutorial
ms.date: 03/19/2020
ms.custom: MVC
ms.openlocfilehash: c806f968bc6530879f64ddbf6fd4c7d45aa7a8d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89442822"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Azure に VMM クラウドのオンプレミス Hyper-V VM のディザスター リカバリーを設定する

このチュートリアルでは、System Center Virtual Machine Manager (VMM) によって管理されたオンプレミスの Hyper-V 仮想マシン (VM) から Azure へのレプリケーションを、[Azure Site Recovery](site-recovery-overview.md) を使用して実現する方法について説明します。 VMM を使用していない場合は、[こちらのチュートリアルに従ってください](hyper-v-azure-tutorial.md)。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * レプリケーションのソースとターゲットを選択します。
> * ソースのレプリケーション環境 (オンプレミスの Site Recovery コンポーネントを含む) と、ターゲットのレプリケーション環境を設定します。
> * VMM VM ネットワークと Azure 仮想ネットワーク間にネットワーク マッピングを設定します。
> * レプリケーション ポリシーを作成します。
> * VM のレプリケーションを有効にします。

> [!NOTE]
> チュートリアルでは、シナリオの最も簡単なデプロイ パスを示します。 可能であれば既定のオプションを使い、すべての可能な設定とパスを示してはいません。 詳細な手順については、「[Site Recovery のドキュメント](./index.yml)」の **ハウツー ガイド** セクションにある記事を確認してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルは、既に次のチュートリアルが済んでいることを前提としています。

1. [Azure を準備する](tutorial-prepare-azure.md)
1. [オンプレミスの Hyper-V を準備する](hyper-v-prepare-on-premises-tutorial.md)

## <a name="select-a-replication-goal"></a>レプリケーションの目標を選ぶ

1. Azure portal から **[Recovery Services コンテナー]** に移動し、[Azure の準備](tutorial-prepare-azure.md#create-a-recovery-services-vault)に関するチュートリアルで作成した **ContosoVMVault** コンテナーを選択します。
1. **[作業の開始]** から **[Site Recovery]**  >  **[インフラストラクチャの準備]** の順に選択し、次の設定を構成します。
    1. **[保護の目標]**  >  **[マシンのある場所]** で、 **[オンプレミス]** を選択します。
    1. **[マシンをどこにレプリケートしますか?]** で、 **[To Azure]\(Azure\)** を選択します。
    1. **[マシンは仮想化されていますか?]** で、 **[はい。Hyper-V を使用します]** を選択します。
    1. **[Are you using System Center VMM to manage your Hyper-V hosts?]\(System Center VMM を使用して Hyper-V ホストを管理しますか?\)** で、 **[はい]** を選択します。
1. **[OK]** を選択します。

   ![レプリケーションの目標](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>展開の計画を確認する

1. 大規模なデプロイを計画している場合は、 **[デプロイ計画]** で、ページ上のリンクから Hyper-V 用の Deployment Planner をダウンロードします。 Hyper-V のデプロイ計画の[詳細を確認](hyper-v-deployment-planner-overview.md)してください。
1. このチュートリアルでは、Deployment Planner は必要ありません。 **[デプロイ計画は完了していますか?]** で、 **[後で実行する]** を選択してから、 **[OK]** を選択します。

## <a name="set-up-the-source-environment"></a>ソース環境をセットアップする

ソース環境を設定するときに、Azure Site Recovery プロバイダーを VMM サーバーにインストールし、そのサーバーをコンテナーに登録します。 Azure Recovery Services エージェントを各 Hyper-V ホストにインストールします。

1. **[インフラストラクチャの準備]** で **[ソース]** を選択します。
1. **[ソースの準備]** で **[+ VMM]** を選択して、VMM サーバーを追加します。 **[サーバーの追加]** で、 **[System Center VMM サーバー]** に **[構成サーバー]** が表示されていることを確認します。
1. Microsoft Azure Site Recovery Provider のインストーラーをダウンロードします。
1. コンテナー登録キーをダウンロードします。 このキーは、プロバイダーのセットアップを実行するときに必要です。 キーは生成後 5 日間有効です。
1. Microsoft Azure Recovery Services エージェントのインストーラーをダウンロードします。

   ![プロバイダー、登録キー、エージェントのダウンロード](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>VMM サーバーにプロバイダーをインストールする

1. Azure Site Recovery Provider のセットアップ ウィザードの **[Microsoft Update]** で、 Microsoft Update を使用して Provider の更新プログラムを確認するよう選択します。
1. **[インストール]** では、 Provider の既定のインストール先のままにして **[インストール]** を選択します。
1. インストール後、Microsoft Azure Site Recovery 登録ウィザードで **[資格情報コンテナー設定]** 、 **[参照]** の順に選択し、 **[キー ファイル]** でダウンロードしたコンテナー キー ファイルを選択します。
1. Azure Site Recovery サブスクリプションとコンテナー名 (**ContosoVMVault**) を指定します。 コンテナーで VMM サーバーを識別できるように、VMM サーバーにわかりやすい名前を指定します。
1. **[プロキシの設定]** で **[プロキシを使用せずに直接 Azure Site Recovery に接続する]** を選択します。
1. データの暗号化で使用する証明書の場所は、既定の場所をそのまま使用します。 暗号化されたデータは、フェールオーバー時に暗号化が解除されます。
1. **[クラウドのメタデータの同期]** で **[クラウドのメタ データを Site Recovery ポータルと同期させます]** を選択します。 この操作は、各サーバーで 1 回のみ実行する必要があります。 次に、 **[登録]** を選択します。
1. サーバーがコンテナーに登録されたら、 **[完了]** を選択します。

登録が完了すると、Azure Site Recovery によってサーバーからメタデータが取得され、 **[Site Recovery インフラストラクチャ]** に VMM サーバーが表示されます。

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>Recovery Services エージェントを Hyper-V ホストにインストールする

レプリケーション対象の VM が含まれる各 Hyper-V ホストにエージェントをインストールします。

Microsoft Azure Recovery Services Agent セットアップ ウィザードで、次の設定を構成します。

1. **[前提条件の確認]** : **[次へ]** を選択します。 不足している前提条件があると自動的にインストールされます。
1. **[インストールの設定]** : インストール場所をそのまま使用します。 その後、 **[インストール]** を選択します。

    >[!NOTE]
    >Azure Site Recovery の場合、**キャッシュの場所** は必要ありません。

1. **[インストール]** : インストールが完了したら、 **[閉じる]** を選択してウィザードを終了します。

   ![エージェントのインストール](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>ターゲット環境をセットアップする

1. **[インフラストラクチャの準備]**  >  **[ターゲット]** の順に選択します。
1. サブスクリプションと、フェールオーバー後に Azure VM が作成されるリソース グループ (**ContosoRG**) を選択します。
1. **Resource Manager** デプロイ モデルを選択します。

Site Recovery によって、互換性のある Azure ストレージ アカウントとネットワークが 1 つ以上あるかどうかが確認されます。

## <a name="configure-network-mapping"></a>ネットワーク マッピングの構成

1. **[Site Recovery インフラストラクチャ]**  >  **[ネットワーク マッピング]**  >  **[ネットワーク マッピング]** の順にクリックし、 **[+ ネットワーク マッピング]** アイコンを選択します。
1. **[ネットワーク マッピングの追加]** で、 **ソース System Center VMM** サーバーを選択します。 **[ターゲット]** には [Azure] を選択します。
1. サブスクリプションとフェールオーバー後のデプロイメント モデルを確認します。
1. **[ソース ネットワーク]** で、 ソース オンプレミス VM ネットワークを選択します。
1. **[ターゲット ネットワーク]** で、 フェールオーバー後に作成されるレプリカ Azure VM の配置先となる Azure ネットワークを選択します。 **[OK]** をクリックします。

   ![ネットワーク マッピング](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>レプリケーション ポリシーを設定する

1. **[インフラストラクチャの準備]**  >  **[レプリケーションの設定]**  >  **[+ 作成と関連付け]** の順に選択します。
1. **[ポリシーの作成と関連付け]** で、ポリシー名を指定します。 ここでは、**ContosoReplicationPolicy** を使用しています。
1. 既定の設定をそのままにして **[OK]** を選択します。
   - **[コピーの頻度]** は、初期レプリケーション後に差分データを 5 分ごとにレプリケートするように設定されています。
   - **[復旧ポイントの保持期間]** は、各復旧ポイントが 2 時間保持されることを示します。
   - **[アプリ整合性スナップショットの頻度]** は、アプリ整合性スナップショットを含む復旧ポイントが 1 時間ごとに作成されるように設定されています。
   - **[初期レプリケーションの開始時刻]** には、初期レプリケーションがすぐに開始されることが示されています。
   - **[Azure に格納されるデータの暗号化]** は、既定値 (**[オフ]**) に設定され、Azure の保存データは暗号化されていないことを示します。
1. ポリシーが作成されたら、 **[OK]** を選択します。 新しいポリシーを作成すると、自動的に VMM クラウドに関連付けられます。

## <a name="enable-replication"></a>レプリケーションを有効にする

1. **[アプリケーションのレプリケート]** で **[ソース]** を選択します。
1. **ソース**。 VMM クラウドを選択します。 **[OK]** をクリックします。
1. **[ターゲット]** で、 ターゲット (Azure) とコンテナー サブスクリプションを確認し、**Resource Manager** モデルを選択します。
1. **contosovmsacct1910171607** ストレージ アカウントと、**ContosoASRnet** Azure ネットワークを選択します。
1. **[仮想マシン]**  >  **[選択]** で、 レプリケートする VM を選択します。 **[OK]** をクリックします。

   **[ジョブ]**  >  **[Site Recovery ジョブ]** の順にクリックして、 **[保護を有効にする]** アクションの進行状況を追跡できます。 **[保護の最終処理]** ジョブが完了した後、初期レプリケーションが完了し、VM がフェールオーバーを実行できる状態になります。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ディザスター リカバリーのテストを実行する](tutorial-dr-drill-azure.md)
