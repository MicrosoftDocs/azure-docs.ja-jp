---
title: Site Recovery を使用した Azure へのディザスター リカバリー時の VMware VM と物理サーバーのフェールオーバーとフェールバック | Microsoft Docs
description: Azure Site Recovery を使用した Azure へのディザスター リカバリー時に VMware VM と物理サーバーを Azure にフェールオーバーする方法とオンプレミス サイトにフェールバックする方法について説明します。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 9206e751fadab7a09c696fbe262aecdde002ae74
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797667"
---
# <a name="fail-over-and-fail-back-vmware-vms"></a>VMware VM のフェールオーバーとフェールバック

この記事では、オンプレミスの VMware VM を Azure の [Azure Site Recovery](site-recovery-overview.md) サービスにフェールオーバーする方法について説明します。 

これは、オンプレミスのマシンを対象に Azure へのディザスター リカバリーを設定する方法について説明するシリーズの 5 番目のチュートリアルです。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * VMware VM のプロパティで Azure の要件に準拠していることを確認する
> * Azure へのフェールオーバーを実行する


> [!NOTE]
> チュートリアルでは、シナリオの最も簡単なデプロイ パスを示します。 可能であれば既定のオプションを使い、すべての可能な設定とパスを示してはいません。 フェールオーバーの詳細については、[この記事を参照してください](site-recovery-failover.md)。

## <a name="before-you-start"></a>開始する前に
前のチュートリアルを完了します。

1. VMware VM、Hyper-V VM、および物理マシンから Azure へのオンプレミス ディザスター リカバリー用に [Azure を設定](tutorial-prepare-azure.md)したことを確認します。
2. オンプレミスの [VMware](vmware-azure-tutorial-prepare-on-premises.md) または [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) 環境をディザスター リカバリー用に準備します。 物理サーバーのディザスター リカバリーを設定している場合は、[サポート マトリックス](vmware-physical-secondary-support-matrix.md)を確認してください。
3. [VMware VM](vmware-azure-tutorial.md)、[Hyper-V VM](hyper-v-azure-tutorial.md)、または[物理マシン](physical-azure-disaster-recovery.md)のディザスター リカバリーを設定します。
4. [ディザスター リカバリー訓練](tutorial-dr-drill-azure.md)を実行して、すべてが予想どおりに動作することを確認します。


## <a name="failover-and-failback"></a>フェールオーバーとフェールバック

フェールオーバーとフェールバックには 4 つの段階があります。

1. **Azure にフェールオーバーする**:オンプレミスのプライマリ サイトがダウンしたときに、マシンを Azure にフェールオーバーします。 フェールオーバー後、レプリケートされたデータから Azure VM が作成されます。
2. **Azure VM を再保護する**:Azure で Azure VM を再保護します。これにより、それらでオンプレミスの VMware VM へのレプリケートが開始されます。 再保護中は、データ一貫性を保証できるようにオンプレミス VM を無効にします。
3. **オンプレミスにフェールオーバーする**:オンプレミス サイトが稼働を開始したら、フェールオーバーを実行して Azure からフェールバックします。
4. **オンプレミス VM を再保護する**:データがフェールバックされたら、フェールバック先のオンプレミスの VM を再保護します。これにより、それらで Azure へのレプリケートが開始されます。

## <a name="verify-vm-properties"></a>VM のプロパティを確認する

フェールオーバーを実行する前に、VM のプロパティを確認し、VM が [Azure の要件](vmware-physical-azure-support-matrix.md#replicated-machines)を満たしていることを確認します。

次のようにプロパティを確認します。

1. **[保護されているアイテム]** で、**[レプリケートされたアイテム]** をクリックし、VM をクリックします。

2. **[レプリケートされたアイテム]** ウィンドウには、VM 情報、正常性状態、および最新の使用可能な復旧ポイントの概要が表示されます。 **[プロパティ]** をクリックすると、詳細が表示されます。

3. **[コンピューティングとネットワーク]** で、Azure 名、リソース グループ、ターゲット サイズ、[可用性セット](../virtual-machines/windows/tutorial-availability-sets.md)、およびマネージド ディスクの設定を変更できます

4. ネットワーク設定 (フェールオーバー後に Azure VM が配置されるネットワークやサブネット、割り当てられる IP アドレスなど) を表示および変更できます。

5. **[ディスク]** で、VM のオペレーティング システム ディスクとデータ ディスクに関する情報を確認できます。

## <a name="run-a-failover-to-azure"></a>Azure へのフェールオーバーを実行する

1. **[設定]** > **[レプリケートされたアイテム]** で、対象の VM、**[フェールオーバー]** の順にクリックします。
2. **[フェールオーバー]** で、フェールオーバー先の**復旧ポイント**を選択します。 次のいずれかのオプションを使うことができます。
   - **[最新]**: 最初に、Site Recovery に送信されるすべてのデータを処理します。 フェールオーバー後に作成された Azure VM は、フェールオーバーがトリガーされた時点で Site Recovery にレプリケートされたすべてのデータを保持しているため、RPO (目標復旧時点) を最も低くすることができます。
   - **最後に処理があった時点**:Site Recovery によって処理された最新の復旧ポイントに VM をフェールオーバーします。 このオプションを使用すると、未処理のデータの処理に時間がかからないため、RTO (目標復旧時間) を低くできます。
   - **最新のアプリ整合性**:Site Recovery によって処理されたアプリ整合性の最新の復旧ポイントに VM をフェールオーバーします。
   - **カスタム**:復旧ポイントを指定します。

3. フェールオーバーをトリガーする前にソース VM のシャットダウンを試行するには、**[フェールオーバーを開始する前にマシンをシャットダウンします]** を選択します。 仮にシャットダウンが失敗したとしても、フェールオーバーは続行されます。 フェールオーバーの進行状況は **[ジョブ]** ページで確認できます。

一部のシナリオでは、フェールオーバーが完了するまでに、さらに約 8 ～ 10 分の処理が必要です。 以下の場合は、テスト フェールオーバーの時間が長くなることがあります。
- 9.8 よりも前のバージョンのモビリティ サービスを稼働している VMware VM
- 物理サーバー
- VMware Linux VM
- 物理サーバーとして保護されている Hyper-V VM
- DHCP サービスが有効になっていない VMware VM
- 次のブート ドライバーがない VMware VM: storvsc、vmbus、storflt、intelide、atapi。

> [!WARNING]
> **進行中のフェールオーバーを取り消さないでください**: フェールオーバーが開始される前に、VM のレプリケーションが停止されます。 進行中のフェールオーバーをキャンセルすると、フェールオーバーは停止しますが、VM が再びレプリケートされることはありません。

## <a name="connect-to-failed-over-vm"></a>フェールオーバーされた VM に接続する

1. フェールオーバー後に RDP または SSH を使用して Azure VM に接続する場合は、[これらの要件を確認](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)してください。
2. フェールオーバー後は、VM に移動し、それに[接続する](../virtual-machines/windows/connect-logon.md)ことで検証します。
3. フェールオーバー後に別の復旧ポイントを使用する場合は、**[復旧ポイントの変更]** を使用します。 次の手順でフェールオーバーをコミットした後、このオプションは使用できなくなります。
4. 検証が終わったら、**[コミット]** をクリックして、フェールオーバー後の VM の復旧ポイントを最終処理します。
5. コミット後、他の使用可能なすべての復旧ポイントが削除されます。 これでフェールオーバーは完了です。

>[!TIP]
> フェールオーバー後に接続の問題が発生した場合は、この[トラブルシューティング ガイド](site-recovery-failover-to-azure-troubleshoot.md)に従ってください。

## <a name="next-steps"></a>次の手順

フェールオーバー後、Azure VM をオンプレミスに再保護します。 次に、VM を再保護してオンプレミス サイトにレプリケートした後、準備が整ったら Azure からフェールバックします。

> [!div class="nextstepaction"]
> [Azure VM を再保護する](vmware-azure-reprotect.md)
> [Azure からフェールバックする](vmware-azure-failback.md) 
