---
title: Azure Site Recovery を使用してディザスター リカバリー中に Azure VM をプライマリ リージョンにフェールバックするチュートリアル。
description: Azure Site Recovery を使用して Azure VM をプライマリ リージョンにフェールバックする方法についてのチュートリアル。
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: 5c127010a7988bf08c77340a4fc10bb32dc76f87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93393950"
---
# <a name="tutorial-fail-back-azure-vm-to-the-primary-region"></a>チュートリアル:Azure VM をプライマリ リージョンにフェールバックする

Azure VM をセカンダリ Azure リージョンにフェールオーバーした後、このチュートリアルに従い、[Azure Site Recovery](site-recovery-overview.md) を使用して、プライマリ Azure リージョンに VM をフェールバックします。  この記事では、次のことについて説明します。

> [!div class="checklist"]
> 
> * 前提条件を確認する。
> * セカンダリ リージョンの VM をフェールバックする。
> * プライマリ VM をセカンダリ リージョンに再保護する。
> 
> [!NOTE]
> このチュートリアルでは、最小限の手順でフェールバックする方法を紹介しています。 すべての設定を使ってフェールオーバーを実行したい場合は、Azure VM の[ネットワーク](azure-to-azure-about-networking.md)、[自動化](azure-to-azure-powershell.md)、[トラブルシューティング](azure-to-azure-troubleshoot-errors.md)についての理解を深めてください。



## <a name="prerequisites"></a>前提条件

このチュートリアルの前に、次の作業を済ませておく必要があります。

1. 少なくとも 1 つの Azure VM を対象に[レプリケーションを設定](azure-to-azure-tutorial-enable-replication.md)し、その VM に対する[ディザスター リカバリー訓練](azure-to-azure-tutorial-dr-drill.md)を行います。
2. プライマリ リージョンからセカンダリ リージョンに [VM をフェールオーバー](azure-to-azure-tutorial-failover-failback.md)し、セカンダリ リージョンからプライマリ リージョンにレプリケートするように VM を再保護します。 
3. プライマリ リージョンが使用できることと、そこに新しいリソースを作成してアクセスできることを確認します。

## <a name="fail-back-to-the-primary-region"></a>プライマリ リージョンにフェールバックする

VM が再保護された後で、必要に応じてプライマリ リージョンにフェールバックすることができます。

1. コンテナー > **[レプリケートされたアイテム]** で、VM を選択します。

2. フェールオーバーを実行する前に、VM が正常であること、また同期が完了していることを VM の概要ページで確認します。 VM は "*保護済み*" の状態になっている必要があります。

    ![保護された状態の VM を示す VM の概要ページ](./media/azure-to-azure-tutorial-failback/protected-state.png)

3. 概要ページで **[フェールオーバー]** を選択します。 今回、テスト フェールオーバーを実行していないので確認を求められます。

    [テスト フェールオーバーなしでフェールオーバーを実行することへの同意を示すページ](./media/azure-to-azure-tutorial-failback/no-test.png)

4. **[フェールオーバー]** で、セカンダリからプライマリの方向に注意し、復旧ポイントを選択します。 このポイントのデータを使用して、ターゲット (プライマリ リージョン) に Azure VM が作成されます。
   - **最後に処理があった時点**:Site Recovery によって処理された最新の復旧ポイントが使用されます。 タイム スタンプが表示されます。 データの処理に時間がかからないため、目標復旧時間 (RTO) は短くなります。
   -  **Latest**:Site Recovery に送信されたすべてのデータを処理して、各 VM の復旧ポイントを作成してから、それにフェールオーバーします。 フェールオーバーがトリガーされた時点で、Site Recovery にすべてのデータがレプリケートされるため、回復ポイントの目標 (RPO) は最も短くなります。
   - **最新のアプリ整合性**:このオプションでは、最新のアプリ整合性の復旧ポイントに VM がフェールオーバーされます。 タイム スタンプが表示されます。
   - **Custom**:特定の復旧ポイントにフェールオーバーします。 カスタムは、フェールオーバーする VM が 1 つだけであって、復旧計画を使用しない場合にのみ使用できます。

    > [!NOTE]
    > VM のレプリケーションを有効にした後にディスクを追加した VM をフェールオーバーする場合、レプリケーション ポイントで復旧に使用できるディスクが表示されます。 たとえば、2 つ目のディスクを追加する前に作成されたレプリケーション ポイントには、"1/2 台のディスク" として表示されます。

4. Site Recovery でフェールオーバーを開始する前にソース VM のシャットダウンを試みる場合は、 **[フェールオーバーを開始する前にマシンをシャットダウンします]** を選択します。 シャットダウンはデータ損失を防止するのに役立ちます。 仮にシャットダウンが失敗したとしても、フェールオーバーは続行されます。 

    ![[フェールオーバーの設定] ページ](./media/azure-to-azure-tutorial-failback/failover.png)    

3. フェールオーバーを開始するには、**[OK]** を選択します。
4. [通知] でフェールオーバーを監視します。

    ![フェールオーバーの進行状況の通知](./media/azure-to-azure-tutorial-failback/notification-progress.png)  
    ![フェールオーバーの成功の通知](./media/azure-to-azure-tutorial-failback/notification-success.png)   

## <a name="reprotect-vms"></a>VM の再保護

プライマリ リージョンに VM をフェールバックしたら、それらの VM を再保護する必要があります。そうすることで、再びセカンダリ リージョンへのレプリケートが開始されます。

1. VM の **[概要]** ページで **[再保護]** を選択します。

    ![プライマリ リージョンから再保護するためのボタン](./media/azure-to-azure-tutorial-failback/reprotect.png)  

2. プライマリ リージョンのターゲット設定を確認します。 新規としてマークされているリソースが、Site Recovery により再保護操作の一環として作成されます。
3. **[OK]** を選択して再保護プロセスを開始します。 このプロセスでは、ターゲットの場所に初期データが送信され、その後、VM の差分情報がターゲットにレプリケートされます。

     ![レプリケーション設定を表示するページ](./media/azure-to-azure-tutorial-failback/replication-settings.png) 

4. [通知] で再保護の進行状況を監視します。 

    ![再保護の進行状況の通知](./media/azure-to-azure-tutorial-failback/notification-reprotect-start.png) [再保護の進行状況の通知](./media/azure-to-azure-tutorial-failback/notification-reprotect-finish.png)
    
  

## <a name="clean-up-resources"></a>リソースをクリーンアップする

マネージド ディスクが使用されている VM については、フェールバックが完了し、プライマリからセカンダリへのレプリケーションを行うように VM が再保護されると、セカンダリ ディザスター リカバリー リージョンのマシンが Site Recovery によって自動的にクリーンアップされます。 セカンダリ リージョン内の VM や NIC を手動で削除する必要はありません。 アンマネージド ディスクを使用する VM はクリーンアップされません。

フェールバック後にレプリケーションを完全に無効にすれば、Site Recovery によって、その保護の対象となっているマシンがクリーンアップされます。 この場合は、マネージド ディスクを使用していない VM のディスクもクリーンアップされます。 
 
## <a name="next-steps"></a>次の手順

このチュートリアルでは、セカンダリ リージョンからプライマリ リージョンに VM をフェールバックしました。 これが一連の作業の最後のステップとなります。つまり、VM のレプリケーションを有効にして、ディザスター リカバリー訓練を実施し、プライマリ リージョンからセカンダリ リージョンにフェールオーバーして、最後にフェールバックを実行しました。

> [!div class="nextstepaction"]
> この後は、[オンプレミス VM](vmware-azure-tutorial-prepare-on-premises.md) を対象に、Azure へのディザスター リカバリーを試してみましょう。

