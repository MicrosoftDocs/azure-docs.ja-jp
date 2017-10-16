---
title: "Azure Site Recovery を使用して オンプレミスのコンピューターの Azure へのディザスター リカバリー訓練を実行する | Microsoft Docs"
description: "Azure Site Recovery を使用した オンプレミスから Azure へのディザスター リカバリー訓練の実行について説明します"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: ddd17921-68f4-41c7-ba4c-b767d36f1733
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/18/2017
ms.author: raynew
ms.openlocfilehash: 15e4487217ec21bb33380422640cb19dfcbcee39
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Azure へのディザスター リカバリー訓練を実行する

このチュートリアルでは、テスト フェールオーバーを使用したオンプレミス コンピューターの Azure へのディザスター リカバリー訓練を実行する方法を示します。 訓練では、データ損失のないレプリケーション戦略を検証します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * テスト フェールオーバー用の分離されたネットワークを設定する
> * フェールオーバー後に Azure VM に接続するための準備をする
> * 1 台のコンピューターのテスト フェールオーバーを実行する

これは、シリーズの 5 番目のチュートリアルです。 このチュートリアルでは、前のチュートリアルで以下のタスクがすでに完了していることを前提としています。

1. [Azure を準備する](tutorial-prepare-azure.md)
2. [オンプレミスの VMware を準備する](tutorial-prepare-on-premises-vmware.md)
3. [ディザスター リカバリーを設定する](tutorial-vmware-to-azure.md)

## <a name="verify-vm-properties"></a>VM のプロパティを確認する

テスト フェールオーバーを実行する前に、VM のプロパティで、VM が [Azure の要件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)に準拠していることを確認します。

1. **[保護されているアイテム]** で、**[レプリケートされたアイテム]** をクリックし、VM をクリックします。
2. **[レプリケートされたアイテム]** ウィンドウには、VM 情報、正常性状態、および最新の使用可能な復旧ポイントの概要が表示されます。 **[プロパティ]** をクリックすると、詳細が表示されます。
3. **[コンピューティングとネットワーク]** で、Azure 名、リソース グループ、ターゲット サイズ、[可用性セット](../virtual-machines/windows/tutorial-availability-sets.md)、および管理ディスクの設定を変更できます。
4. ネットワーク設定 (フェールオーバー後に Azure VM が配置されるネットワークやサブネット、割り当てられる IP アドレスなど) を表示および変更できます。
5. **[ディスク]** で、VM のオペレーティング システム ディスクとデータ ディスクに関する情報を確認できます。

## <a name="run-a-test-failover-for-a-single-vm"></a>1 台の VM のテスト フェールオーバーを実行する

テスト フェールオーバーを実行すると、次の処理が発生します。

1. フェールオーバーを実行するために必要なすべての条件が満たされていることを確認する前提条件チェックが実行されます。
2. Azure VM を作成できるように、フェールオーバーによってデータが処理されます。 最新の復旧ポイントを選択した場合は、データから復旧ポイントが作成されます。
3. 前の手順で処理されたデータを使用して、Azure VM が作成されます。

次のようにテスト フェールオーバーを実行します。

1. **[設定]** > **[レプリケートされたアイテム]** で、VM をクリックし、**[+ テスト フェールオーバー]** をクリックします。

2. フェールオーバーで使用する復旧ポイントを選択します。
    - **[Latest processed]\(最新の処理\)**: Site Recovery によって処理された最新の復旧ポイントに VM をフェールオーバーします。 タイム スタンプが表示されます。 このオプションを使用すると、データの処理に時間がかからないため、RTO (目標復旧時間) が低くなります。
    - **[Latest app-consistent]\(最新のアプリ整合性\)** : このオプションは、すべての VM を最新のアプリ整合性の復旧ポイントにフェールオーバーします。 タイム スタンプが表示されます。
    - **[カスタム]** : 任意の復旧ポイントを選択します。
3. **[テスト フェールオーバー]** で、フェールオーバー後に Azure VM が接続するターゲット Azure ネットワークを選択します。
4. **[OK]** をクリックすると、フェールオーバーが開始されます。 VM をクリックしてそのプロパティを開くことで、進行状況を追跡できます。 または、コンテナー名の **[テスト フェールオーバー]** ジョブをクリックし、**[設定]** > **[ジョブ]** >
   **[Site Recovery ジョブ]** をクリックできます。
5. フェールオーバーの完了後、レプリカの Azure VM は、Azure Portal の **[仮想マシン]** に表示されます。 VM が適切なサイズであること、適切なネットワークに接続されていること、および実行されていることを確認します。
6. これで、Azure 内のレプリケートされた VM に接続できるはずです。
7. テスト フェールオーバー中に作成された VM を削除するには、復旧計画で **[テスト フェールオーバーのクリーンアップ]** をクリックします。 **[メモ]** を使用して、テスト フェールオーバーに関連する観察結果をすべて記録し、保存します。

一部のシナリオでは、フェールオーバーが完了するまでに、さらに約 8 ～ 10 分の処理が必要です。 VMware Linux マシン、DHCP サービスが有効でない VMware VM、ブート ドライバー storvsc、vmbus、storftt、intelide、atapi を持たない VMware VM については、テスト フェールオーバーの時間が長くなることあります。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [オンプレミス の VMware VM のフェールオーバーとフェールバックを実行する](tutorial-vmware-to-azure-failover-failback.md)
