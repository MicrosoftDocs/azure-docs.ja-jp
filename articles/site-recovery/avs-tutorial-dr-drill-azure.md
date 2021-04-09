---
title: Azure Site Recovery を使用して Azure VMware Solution から Azure へのディザスター リカバリー訓練を実行する
description: Azure Site Recovery を使用して、Azure VMware Solution プライベート クラウドから Azure へのディザスター リカバリー訓練を実行する方法について説明します。
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 46d5e1cf773a24b032874ee54021c780e1f361af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91814235"
---
# <a name="run-a-disaster-recovery-drill-from-azure-vmware-solution-to-azure"></a>Azure VMware Solution から Azure へのディザスター リカバリー訓練を実行する

この記事では、[Azure Site Recovery](site-recovery-overview.md) サービスを使用して、Azure VMware Solution VM の Azure へのディザスター リカバリー訓練を実行する方法について説明します。 訓練では、データ損失のないレプリケーション戦略を検証します。


これは、Azure VMware Solution マシンの Azure へのディザスター リカバリーを設定する方法を説明するシリーズの 4 番目のチュートリアルです。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]

> * テスト フェールオーバー用の分離されたネットワークを設定する
> * フェールオーバー後に Azure VM に接続するための準備をする
> * 1 台のマシンのテスト フェールオーバーを実行する。

> [!NOTE]
> チュートリアルでは、シナリオの最も簡単なデプロイ パスを示します。 可能であれば既定のオプションを使い、すべての可能な設定とパスを示してはいません。 ディザスター リカバリー訓練手順の詳細については、[この記事を参照](site-recovery-test-failover-to-azure.md)してください。

## <a name="before-you-start"></a>開始する前に

前のチュートリアルを完了します。

1. Azure へのディザスター リカバリー用に [Azure を設定](avs-tutorial-prepare-azure.md)済みであることを確認します。
2. [こちらの手順](avs-tutorial-prepare-avs.md)に従って、Azure へのディザスター リカバリー用に Azure VMware Solution デプロイを準備します。
3. Azure VMware Solution VM のディザスター リカバリーを[設定](avs-tutorial-replication.md)します。
 

## <a name="verify-vm-properties"></a>VM のプロパティを確認する

テスト フェールオーバーを実行する前に、VM のプロパティを確認し、[VMware VM](vmware-physical-azure-support-matrix.md#replicated-machines) が Azure の要件に準拠していることを確認します。

1. **[保護された項目]** で、**[レプリケートされたアイテム]** をクリックし、VM をクリックします。
2. **[レプリケートされたアイテム]** ウィンドウには、VM 情報、正常性状態、および最新の使用可能な復旧ポイントの概要が表示されます。 **[プロパティ]** をクリックすると、詳細が表示されます。
3. **[コンピューティングとネットワーク]** で、Azure 名、リソース グループ、ターゲット サイズ、可用性セット、およびマネージド ディスクの設定を変更できます。
4. ネットワーク設定 (フェールオーバー後に Azure VM が配置されるネットワークやサブネット、割り当てられる IP アドレスなど) を表示および変更できます。
5. **[ディスク]** で、VM のオペレーティング システム ディスクとデータ ディスクに関する情報を確認できます。

## <a name="create-a-network-for-test-failover"></a>テスト フェールオーバー用のネットワークの作成

テスト フェールオーバー用に、各 VM の **[Compute and Network] \(コンピューティングとネットワーク)** 設定で、運用復旧サイト ネットワークから分離されたネットワークを選択することをお勧めします。 既定では、作成した Azure 仮想ネットワークは、他のネットワークから分離されます。 テスト ネットワークは、運用ネットワークを模倣したものにする必要があります。

- テスト ネットワークでは、運用ネットワークと同じ数のサブネットが必要です。 サブネットは、同じ名前を持つ必要があります。
- テスト ネットワークでは、同じ IP アドレス クラスとサブネット範囲を使用する必要があります。
- テスト ネットワークの DNS を、 **[Compute and Network] \(コンピューティングとネットワーク)** 設定で DNS VM に指定した IP アドレスで更新します。 詳細については、[Active Directory 用のテスト フェールオーバーの考慮事項](site-recovery-active-directory.md#test-failover-considerations)を参照してください。

## <a name="run-a-test-failover-for-a-single-vm"></a>1 台の VM のテスト フェールオーバーを実行する

テスト フェールオーバーを実行すると、次の処理が発生します。

1. フェールオーバーを実行するために必要なすべての条件が満たされていることを確認する前提条件チェックが実行されます。
2. Azure VM を作成できるように、フェールオーバーによってデータが処理されます。 最新の復旧ポイントを選択した場合は、データから復旧ポイントが作成されます。
3. 前の手順で処理されたデータを使用して、Azure VM が作成されます。

次のようにテスト フェールオーバーを実行します。

1. **[設定]**  >  **[レプリケートされたアイテム]** で、VM をクリックし、 **[+ テスト フェールオーバー]** をクリックします。
2. このチュートリアルでは **[最後に処理があった時点]** 復旧ポイントを選択します。 この場合、VM が、利用可能な最新のポイントインタイムにフェールオーバーされます。 タイム スタンプが表示されます。 このオプションを使用すると、データの処理に時間がかからないため、RTO (目標復旧時間) が低くなります。
3. **[テスト フェールオーバー]** で、フェールオーバー後に Azure VM が接続するターゲット Azure ネットワークを選択します。
4. **[OK]** をクリックすると、フェールオーバーが開始されます。 VM をクリックしてそのプロパティを開くことで、進行状況を追跡できます。 または、コンテナー名の **[テスト フェールオーバー]** ジョブをクリックし、**[設定]** > **[ジョブ]** >
    **[Site Recovery ジョブ]** をクリックできます。
5. フェールオーバーの完了後、レプリカの Azure VM は、Azure Portal の **[仮想マシン]** に表示されます。 VM が適切なサイズであること、適切なネットワークに接続されていること、および実行されていることを確認します。
6. これで、Azure 内のレプリケートされた VM に接続できるはずです。
7. テスト フェールオーバー中に作成された VM を削除するには、VM で **[テスト フェールオーバーのクリーンアップ]** をクリックします。 **[メモ]** を使用して、テスト フェールオーバーに関連する観察結果をすべて記録し、保存します。

一部のシナリオでは、フェールオーバーが完了するまでに、さらに約 8 ～ 10 分の処理が必要です。 VMware Linux マシン、DHCP サービスが有効でない VMware VM、ブート ドライバー storvsc、vmbus、storftt、intelide、atapi を持たない VMware VM については、テスト フェールオーバーの時間が長くなることあります。

## <a name="connect-after-failover"></a>フェールオーバー後に接続する

フェールオーバー後に RDP/SSH を使用して Azure VM に接続する場合は、[接続の準備](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)をします。 フェールオーバー後に接続の問題が発生した場合は、この[トラブルシューティング ガイド](site-recovery-failover-to-azure-troubleshoot.md)に従ってください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [フェールオーバーの実行](avs-tutorial-failover.md)

