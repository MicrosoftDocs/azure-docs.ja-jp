---
title: チュートリアル - Azure Site Recovery を使用して Windows VM のディザスター リカバリーを設定する
description: Azure Site Recovery サービスを使用して、別の Azure リージョンへの Windows VM のディザスター リカバリーを有効にする方法について説明します。
author: rayne-wiselman
ms.service: virtual-machines
ms.collection: windows
ms.subservice: recovery
ms.topic: tutorial
ms.date: 11/05/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: e9f44ea2af832729a47bf4b719b90f9b14e401b9
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555858"
---
# <a name="tutorial-enable-disaster-recovery-for-windows-vms"></a>チュートリアル:Windows VM のディザスター リカバリーを有効にする

このチュートリアルでは、Windows を実行する Azure VM のディザスター リカバリーを設定する方法について説明します。 この記事では、次のことについて説明します:

> [!div class="checklist"]
> * Windows VM のディザスター リカバリーを有効にする
> * ディザスター リカバリー訓練の実行
> * 訓練後に VM のレプリケートを停止する

VM のレプリケーションを有効にすると、Site Recovery Mobility Service 拡張機能が VM にインストールされ、[Azure Site Recovery](../../site-recovery/site-recovery-overview.md) に登録されます。 レプリケーション中、VM ディスクへの書き込みは、ソース リージョンのキャッシュ ストレージ アカウントに送信されます。 データは、そこからターゲット リージョンに送信され、そのデータから復旧ポイントが生成されます。  ディザスター リカバリーの過程で VM をフェールオーバーすると、復旧ポイントを使用してターゲット リージョンに VM が復元されます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。

## <a name="prerequisites"></a>前提条件

1. ご利用の Azure サブスクリプションからターゲット リージョンに VM を作成できることを確認します。 無料の Azure アカウントを作成したばかりであれば、ご自身がサブスクリプションの管理者であり、必要なアクセス許可を持っています。
2. サブスクリプションの管理者でなければ、管理者に、次のロールの割り当てを依頼します。
    - "仮想マシン共同作成者" 組み込みロール。または次の作業を行うための個別のアクセス許可。
        - 選択した仮想ネットワーク内に VM を作成する。
        - Azure Storage アカウントに書き込む。
        - Azure マネージド ディスクに書き込む。
    - コンテナーにおける Site Recovery の操作を管理するための "Site Recovery 共同作成者" 組み込みロール。 
3. Windows Server 2012 以降を実行する Windows VM の使用をお勧めします。 このチュートリアルの目的上、VM ディスクは暗号化しないでください。
4. VM のアウトバウンド接続で URL ベースのプロキシが使用されている場合、次の URL にアクセスできることを確認します。 認証済みプロキシの使用はサポートされていません。

    **名前** | **パブリック クラウド** | **政府機関向けクラウド** | **詳細**
    --- | --- | --- | ---
    ストレージ | `*.blob.core.windows.net` | `*.blob.core.usgovcloudapi.net`| VM からソース リージョンのキャッシュ ストレージ アカウントへのデータの書き込み。 
    Azure AD  | `login.microsoftonline.com` | `login.microsoftonline.us`| Site Recovery サービスの URL に対する承認と認証。 
    レプリケーション | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`  |VM と Site Recovery サービスの通信。 
    Service Bus | `*.servicebus.windows.net` | `*.servicebus.usgovcloudapi.net` | VM から Site Recovery への監視データおよび診断データの書き込み。 

4. ネットワーク セキュリティ グループ (NSG) を使用して VM のネットワーク トラフィックを制限している場合は、次のサービス タグ (IP アドレスのグループ) を使用して VM のアウトバウンド接続 (HTTPS 443) を許可する NSG ルールを作成します。 ルールは、まずテスト NSG で試してください。

    **Tag** | **許可** 
    --- | --- 
    Storage タグ | VM からキャッシュ ストレージ アカウントへのデータの書き込みを許可します。
    Azure AD タグ | Azure AD に対応するすべての IP アドレスへのアクセスを許可します。
    EventsHub タグ | Site Recovery 監視へのアクセスを許可します。
    AzureSiteRecovery タグ | 任意のリージョンの Site Recovery Service へのアクセスを許可します。
    GuestAndHybridManagement | レプリケーションが有効な VM 上で稼働している Site Recovery モビリティ エージェントを自動的にアップグレードしたい場合に使用します。
5.  Windows VM に最新の Windows 更新プログラムをインストールして、VM のルート証明書を最新の状態にします。
 
## <a name="enable-disaster-recovery"></a>ディザスター リカバリーを有効にする

1. Azure portal で、VM プロパティ ページを開きます。
2. **[操作]** で、 **[ディザスター リカバリー]** を選択します。
3. **[基本]**  >  **[ターゲット リージョン]** で、VM のレプリケート先とするリージョンを選択します。 ソース リージョンとターゲット リージョンは、同じ Azure Active Directory テナントに存在している必要があります。
4. **[レプリケーションを確認して開始する]** をクリックします。

    :::image type="content" source="./media/tutorial-disaster-recovery/disaster-recovery.png" alt-text="VM プロパティの [ディザスター リカバリー] ページでレプリケーションを有効にする。":::

5. **[レプリケーションの確認と開始]** で、設定を確認します。

    - **[ターゲット設定]** 。 既定では、Site Recovery がソース設定をミラーリングすることによってターゲット リソースが作成されます。
    - **[ストレージの設定] の [キャッシュ ストレージ アカウント]** 。 ソース リージョンのストレージ アカウントが使用されます。 ソース VM の変更は、このアカウントにキャッシュされた後、ターゲットの場所にレプリケートされます。
    - **[ストレージの設定] の [Replica disk]\(レプリカ ディスク\)** 。 既定では、ソース VM のマネージド ディスクをミラーリングした同じストレージ タイプ (Standard または Premium) のレプリカ マネージド ディスクが Site Recovery によってターゲット リージョンに作成されます。
    - **[レプリケーションの設定]** 。 コンテナーの詳細が表示されます。また、Site Recovery によって作成された復旧ポイントが 24 時間保持される旨が表示されます。
    - **[拡張機能の設定]** 。 レプリケートする VM にインストールされた Site Recovery Mobility Service 拡張機能への更新が Site Recovery によって管理される旨が表示されます。 更新プロセスは、表示されている Azure Automation アカウントによって管理されます。

    :::image type="content" source="./media/tutorial-disaster-recovery/settings-summary.png" alt-text="ターゲットとレプリケーション設定の概要を表示するページ。":::

2. **[レプリケーションの開始]** を選択します。 デプロイが開始され、Site Recovery がターゲット リソースの作成を開始します。 レプリケーションの進行状況は、[通知] で監視できます。

    :::image type="content" source="./media/tutorial-disaster-recovery/notifications.png" alt-text="レプリケーションの進行状況の通知。":::

## <a name="check-vm-status"></a>VM の状態を確認する

レプリケーション ジョブが完了したら、VM のレプリケーションの状態を確認できます。

1. VM のプロパティ ページを開きます。
2. **[操作]** で、 **[ディザスター リカバリー]** を選択します。
3. **[要点]** セクションを展開し、コンテナー、レプリケーション ポリシー、ターゲット設定の既定値を確認します。
4. **[正常性と状態]** で、VM のレプリケーション状態、エージェントのバージョン、フェールオーバーの準備、最新の復旧ポイントに関する情報を把握します。 

    :::image type="content" source="./media/tutorial-disaster-recovery/essentials.png" alt-text="VM のディザスター リカバリーの [要点] ビュー。":::

5. **[インフラストラクチャ ビュー]** で、ソース VM とターゲット VM、マネージド ディスク、キャッシュ ストレージ アカウントの概要を視覚的に把握します。

    :::image type="content" source="./media/tutorial-disaster-recovery/infrastructure.png" alt-text="VM のディザスター リカバリーのインフラストラクチャのビジュアル マップ。":::


## <a name="run-a-drill"></a>訓練を実施する

ディザスター リカバリーが正しく機能することを確認するための訓練を実施します。 テスト フェールオーバーを実行すると、進行中のレプリケーションや運用環境に影響を及ぼすことなく、VM のコピーが作成されます。 

1. VM のディザスター リカバリー ページで **[テスト フェールオーバー]** を選択します。
2. **[テスト フェールオーバー]** の [復旧ポイント] は、既定の設定である **[Latest processed (low RPO)]\(最後に処理があった時点 (低 RPO)\)** のままにします。

   これは回復ポイントの目標 (RPO) が最も短くなるオプションであり、一般に、ターゲット VM のスピンアップにかかる時間が最短となります。 これは、Site Recovery サービスに送信されたすべてのデータを最初に処理して、各 VM の復旧ポイントを作成してから、それにフェールオーバーします。 この復旧ポイントには、フェールオーバーがトリガーされたときに Site Recovery にレプリケートされたすべてのデータが含まれます。

3. フェールオーバー後に VM が配置される仮想ネットワークを選択します。 

     :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-settings.png" alt-text="テスト フェールオーバーのオプションを設定するためのページ。":::

4. テスト フェールオーバーの処理が開始されます。 進行状況は [通知] で監視できます。

    :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-notification.png" alt-text="テスト フェールオーバーの通知。"::: 
    
   テスト フェールオーバーが完了すると、 **[要点]** ページに表示される VM の状態が *[テスト フェールオーバーのクリーンアップが保留中]* になります。 



## <a name="clean-up-resources"></a>リソースをクリーンアップする

訓練が終わると、Site Recovery によって自動的に VM がクリーンアップされます。

1. 自動クリーンアップを開始するには、 **[テスト フェールオーバーのクリーンアップ]** を選択します。

    :::image type="content" source="./media/tutorial-disaster-recovery/start-cleanup.png" alt-text="[要点] ページでクリーンアップを開始する。"::: 

2. **[テスト フェールオーバーのクリーンアップ]** で、フェールオーバーに関して何か記録しておきたいメモがあれば入力し、 **[テストが完了しました。テスト フェールオーバー仮想マシンを削除してください]** を選択します。 **[OK]** をクリックします。

    :::image type="content" source="./media/tutorial-disaster-recovery/delete-test.png" alt-text="メモを記録してテスト VM を削除するためのページ。"::: 

7. 削除処理が開始されます。 進行状況は [通知] で監視できます。

    :::image type="content" source="./media/tutorial-disaster-recovery/delete-test-notification.png" alt-text="テスト VM の削除を監視するための [通知]。"::: 

### <a name="stop-replicating-the-vm"></a>VM のレプリケートを停止する

ディザスター リカバリーの訓練が完了したら、続けて完全なフェールオーバーを試してみることをお勧めします。 完全なフェールオーバーを実行したくなければ、レプリケーションを無効にすることができます。 次の処理が実行されます。

- Site Recovery のレプリケートされたマシンの一覧から VM を削除します。
- VM に対する Site Recovery の課金を停止します。
- ソース レプリケーションの設定を自動的にクリーンアップします。

レプリケーションを停止するには、次の手順に従います。

1. VM のディザスター リカバリー ページで **[レプリケーションの無効化]** を選択します。
2. **[レプリケーションの無効化]** で、レプリケーションを無効にする理由を選択します。 **[OK]** をクリックします。

    :::image type="content" source="./media/tutorial-disaster-recovery/disable-replication.png" alt-text="レプリケーションを無効にして理由を入力するためのページ。"::: 


レプリケーションの過程で VM にインストールされた Site Recovery 拡張機能は、自動的には削除されません。 VM のレプリケーションを無効にし、その後もレプリケートする予定がない場合は、次の手順に従って Site Recovery 拡張機能を手動で削除できます。 

1. [VM] > **[設定]**  >  **[拡張機能]** に移動します。
2. **[拡張機能]** ページで、Linux に該当する *Microsoft.Azure.RecoveryServices* の各エントリを選択します。
3. 拡張機能のプロパティ ページで、 **[アンインストール]** を選択します。

   :::image type="content" source="./media/tutorial-disaster-recovery/uninstall-extension.png" alt-text="Site Recovery VM 拡張機能をアンインストールするためのページ。":::



## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure VM のディザスター リカバリーを構成し、ディザスター リカバリーの訓練を実施しました。 この後は、VM に対して完全なフェールオーバーを実行できます。

> [!div class="nextstepaction"]
> [VM を別のリージョンにフェールオーバーする](../../site-recovery/azure-to-azure-tutorial-dr-drill.md)
