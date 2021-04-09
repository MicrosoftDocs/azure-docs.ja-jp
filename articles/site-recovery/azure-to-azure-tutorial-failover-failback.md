---
title: Azure Site Recovery を使用して Azure VM をセカンダリ リージョンにフェールオーバーしてディザスター リカバリーを行うチュートリアル。
description: このチュートリアルでは、Azure Site Recovery サービスを使用して Azure VM をフェールオーバーする方法と、ディザスター リカバリー用のセカンダリ Azure リージョンにレプリケートされた Azure VM を再保護する方法について説明します。
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: 99263c83d25542073d63c1cba394a147bd5b2170
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392780"
---
# <a name="tutorial-fail-over-azure-vms-to-a-secondary-region"></a>チュートリアル:Azure VM をセカンダリ リージョンにフェールオーバーする

[Azure Site Recovery](site-recovery-overview.md) によるディザスター リカバリーが有効になった Azure VM をセカンダリ Azure リージョンにフェールオーバーする方法について説明します。 フェールオーバー後、今度はターゲット リージョンからプライマリ リージョンに VM をレプリケートするように、ターゲット リージョンの VM を再保護します。 この記事では、次のことについて説明します。

> [!div class="checklist"]
> * 前提条件を確認する
> * VM 設定を確認する
> * セカンダリ リージョンへのフェールオーバーを実行する
> * プライマリ リージョンへの VM のレプリケート バックを開始する。


> [!NOTE]
> このチュートリアルでは、最小限の手順で VM をフェールオーバーする方法を紹介しています。 すべての設定を使ってフェールオーバーを実行したい場合は、Azure VM の[ネットワーク](azure-to-azure-about-networking.md)、[自動化](azure-to-azure-powershell.md)、[トラブルシューティング](azure-to-azure-troubleshoot-errors.md)についての理解を深めてください。



## <a name="prerequisites"></a>前提条件

このチュートリアルの前に、次の作業を済ませておく必要があります。

1. 少なくとも 1 つの Azure VM に対してレプリケーションを設定します。 まだ済んでいない場合には、このシリーズの[最初のチュートリアルを完了](azure-to-azure-tutorial-enable-replication.md)してください。
2. レプリケートされた VM について、[ディザスター リカバリーの訓練を実施](azure-to-azure-tutorial-dr-drill.md)することをお勧めします。 完全なフェールオーバーの前に訓練を実施しておくことで、すべての処理が想定どおりに行われることを、運用環境に影響を与えることなく確認できます。 


## <a name="verify-the-vm-settings"></a>VM 設定を確認する

1. コンテナー > **[レプリケートされたアイテム]** で、VM を選択します。

    ![概要ページで VM のプロパティを開くためのオプション](./media/azure-to-azure-tutorial-failover-failback/vm-settings.png)

2. フェールオーバーを実行する前に、VM が保護されていること、また正常な状態であることを、VM の **[概要]** ページで確認します。
    ![VM のプロパティと状態を確認するためのページ](./media/azure-to-azure-tutorial-failover-failback/vm-state.png)

3. フェールオーバーを実行する前に、次の点をチェックします。
    - サポート対象の [Windows](azure-to-azure-support-matrix.md#windows) または [Linux](azure-to-azure-support-matrix.md#replicated-machines---linux-file-systemguest-storage) オペレーティング システムが VM で実行されていること。
    - VM が[コンピューティング](azure-to-azure-support-matrix.md#replicated-machines---compute-settings)、[ストレージ](azure-to-azure-support-matrix.md#replicated-machines---storage)、[ネットワーク](azure-to-azure-support-matrix.md#replicated-machines---networking)の要件に適合していること。

## <a name="run-a-failover"></a>フェールオーバーの実行


1. VM の **[概要]** ページで **[フェールオーバー]** を選択します。

    ![レプリケートされたアイテムの [フェールオーバー] ボタン](./media/azure-to-azure-tutorial-failover-failback/failover-button.png)

3. **[フェールオーバー]** で、復旧ポイントを選択します。 その復旧ポイントのデータを使用して、ターゲット リージョンに Azure VM が作成されます。
  
   - **最後に処理があった時点**:Site Recovery によって処理された最新の復旧ポイントが使用されます。 タイム スタンプが表示されます。 データの処理に時間がかからないため、目標復旧時間 (RTO) は短くなります。
   -  **Latest**:Site Recovery に送信されたすべてのデータを処理して、各 VM の復旧ポイントを作成してから、それにフェールオーバーします。 フェールオーバーがトリガーされた時点で、Site Recovery にすべてのデータがレプリケートされるため、回復ポイントの目標 (RPO) は最も短くなります。
   - **最新のアプリ整合性**:このオプションでは、最新のアプリ整合性の復旧ポイントに VM がフェールオーバーされます。 タイム スタンプが表示されます。
   - **Custom**:特定の復旧ポイントにフェールオーバーします。 カスタムは、フェールオーバーする VM が 1 つだけであって、復旧計画を使用しない場合にのみ使用できます。

    > [!NOTE]
    > レプリケーションを有効にした後で VM にディスクを追加した場合、リカバリーに使用できるディスクがレプリケーション ポイントに表示されます。 たとえば、2 つ目のディスクを追加する前に作成されたレプリケーション ポイントには、"1/2 台のディスク" として表示されます。

4. Site Recovery でフェールオーバーを開始する前にソース VM のシャットダウンを試みる場合は、 **[フェールオーバーを開始する前にマシンをシャットダウンします]** を選択します。 シャットダウンはデータ損失を防止するのに役立ちます。 仮にシャットダウンが失敗したとしても、フェールオーバーは続行されます。 

    ![[フェールオーバーの設定] ページ](./media/azure-to-azure-tutorial-failover-failback/failover-settings.png)    

3. フェールオーバーを開始するには、**[OK]** を選択します。
4. [通知] でフェールオーバーを監視します。

    ![進行状況の通知](./media/azure-to-azure-tutorial-failover-failback/notification-failover-start.png) ![成功の通知](./media/azure-to-azure-tutorial-failover-failback/notification-failover-finish.png)     

5. フェールオーバー後、ターゲット リージョンに作成された Azure VM が **[仮想マシン]** に表示されます。 VM が実行中であること、また適切なサイズに設定されていることを確認します。 VM に対して異なる復旧ポイントを使用したい場合は、 **[要点]** ページの **[復旧ポイントの変更]** を選択します。
6. フェールオーバーされた VM に問題がなければ、概要ページの **[コミット]** を選択してフェールオーバーを完了します。

    ![[コミット] ボタン](./media/azure-to-azure-tutorial-failover-failback/commit-button.png) 

7. **[コミット]** の **[OK]** を選択して確定します。 その VM に関して利用可能な復旧ポイントがすべて Site Recovery から削除され、以後、復旧ポイントを変更することはできなくなります。

8. コミットの進行状況は [通知] で監視します。

    ![コミットの進行状況の通知](./media/azure-to-azure-tutorial-failover-failback/notification-commit-start.png) ![コミットの成功の通知](./media/azure-to-azure-tutorial-failover-failback/notification-commit-finish.png)    

9. Site Recovery では、フェールオーバー後にソース VM がクリーンアップされません。 その操作は手動で行う必要があります。


## <a name="reprotect-the-vm"></a>VM を再保護する

フェールオーバー後は、セカンダリ リージョン内の VM を再保護することで、その VM がプライマリ リージョンにレプリケート バックされます。 

1. VM の **[状態]** が *[フェールオーバーがコミットされました]* になっていることを確認したうえで開始します。
2. プライマリ リージョンが利用可能であること、また、そのリージョンに VM を作成するためのアクセス許可が自分にあることを確認します。
3. VM の **[概要]** ページで **[再保護]** を選択します。

   ![VM の再保護を有効にするためのボタン。](./media/azure-to-azure-tutorial-failover-failback/reprotect-button.png)

4. **[再保護]** で、レプリケーション方向 (セカンダリ リージョンからプライマリ リージョン) を確認し、プライマリ リージョンのターゲット設定を確認します。 新規としてマークされているリソースが、Site Recovery により再保護操作の一環として作成されます。

     ![再保護の設定ページ](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

6. **[OK]** を選択して再保護プロセスを開始します。 このプロセスでは、ターゲットの場所に初期データが送信され、その後、VM の差分情報がターゲットにレプリケートされます。
7. [通知] で再保護の進行状況を監視します。 

    ![再保護の進行状況の通知](./media/azure-to-azure-tutorial-failover-failback/notification-reprotect-start.png) ![再保護の成功の通知](./media/azure-to-azure-tutorial-failover-failback/notification-reprotect-finish.png)
    

## <a name="next-steps"></a>次の手順

このチュートリアルでは、プライマリ リージョンからセカンダリ リージョンに VM をフェールオーバーした後、プライマリ リージョンへの VM のレプリケート バックを開始しました。 これで、セカンダリ リージョンからプライマリ リージョンへのフェールバックができるようになりました。

> [!div class="nextstepaction"]
> [プライマリ リージョンにフェールバックする](azure-to-azure-tutorial-failback.md)
