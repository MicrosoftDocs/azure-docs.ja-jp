---
title: Azure Site Recovery で Hyper-V VM から Azure へのフェールオーバーを設定する
description: Azure Site Recovery で Hyper-V VM を Azure にフェールオーバーする方法について説明します。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/16/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b46a2ea12a697afde8223cc3595365c1286512c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86132458"
---
# <a name="fail-over-hyper-v-vms-to-azure"></a>Hyper-V VM を Azure にフェールオーバーする

このチュートリアルでは、[Azure Site Recovery](site-recovery-overview.md) で Hyper-V VM を Azure にフェールオーバーする方法について説明します。 フェールオーバーした後、オンプレミス サイトが使用可能になったときにオンプレミス サイトにフェールバックします。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Hyper-V VM のプロパティで Azure の要件に準拠していることを確認する。
> * 特定の VM を Azure にフェールオーバーする。


このチュートリアルはシリーズで 5 番目のチュートリアルです。 このチュートリアルでは、前のチュートリアルで以下のタスクが既に完了していることを前提としています。    

1. [Azure を準備する](tutorial-prepare-azure.md)
2. [オンプレミスの Hyper-V を準備する](./hyper-v-prepare-on-premises-tutorial.md)
3. [Hyper-V VM](./hyper-v-azure-tutorial.md) または [System Center VMM クラウドで管理される Hyper-V VM](./hyper-v-vmm-azure-tutorial.md) のディザスター リカバリーをセットアップする
4. [ディザスター リカバリーのテストを実行する](tutorial-dr-drill-azure.md)

さまざまな種類のフェールオーバーの[詳細を参照してください](failover-failback-overview.md#types-of-failover)。 1 つの復旧計画で複数の VM をフェールオーバーする場合は、[この記事](site-recovery-failover.md)を参照してください。

## <a name="prepare-for-failover"></a>フェールオーバーを準備する 
VM にスナップショットがないことと、フェールバック中にオンプレミスの VM をオフにすることを確認します。 これは、レプリケーション中のデータの整合性を確保するのに役立ちます。 フェールバック中にオンプレミスの VM を有効にしないでください。 

フェールオーバーとフェールバックには 3 つの段階があります。

1. **Azure にフェールオーバーする**: オンプレミス サイトの Hyper-V を Azure にフェールオーバーします。
2. **オンプレミスにフェールオーバーする**: オンプレミス サイトが使用可能な場合に、Azure VM をオンプレミス サイトにフェールオーバーします。 Azure からオンプレミスへのデータの同期が開始され、完了すると、オンプレミスの VM が起動します。  
3. **オンプレミス VM のレプリケートを反転する**: オンプレミスにフェールオーバーされたら、オンプレミスの VM のレプリケートを反転して、Azure へのレプリケートを開始します。

## <a name="verify-vm-properties"></a>VM のプロパティを確認する

フェールオーバー前に、VM のプロパティで、VM が [Azure の要件](hyper-v-azure-support-matrix.md#replicated-vms)を満たしていることを確認します。

**[保護されているアイテム]** で、 **[レプリケートされたアイテム]** をクリックし、VM をクリックします。

1. **[レプリケートされたアイテム]** ウィンドウには、VM 情報、正常性状態、および最新の使用可能な復旧ポイントの概要が表示されます。 **[プロパティ]** をクリックすると、詳細が表示されます。

1. **[コンピューティングとネットワーク]** で、Azure 名、リソース グループ、ターゲット サイズ、[可用性セット](../virtual-machines/windows/tutorial-availability-sets.md)、およびマネージド ディスクの設定を変更できます。

1. ネットワーク設定 (フェールオーバー後に Azure VM が配置されるネットワークやサブネット、割り当てられる IP アドレスなど) を表示および変更できます。

1. **[ディスク]** で、VM のオペレーティング システム ディスクとデータ ディスクに関する情報を確認できます。

## <a name="fail-over-to-azure"></a>Azure にフェールオーバーする

1. **[設定]**  >  **[レプリケートされたアイテム]** で、対象の VM、 **[フェールオーバー]** の順にクリックします。
2. **[フェールオーバー]** で、 **[最新]** 復旧ポイントを選択します。 
3. **[フェールオーバーを開始する前にマシンをシャットダウンします]** を選択します。 Site Recovery は、フェールオーバーを開始する前にソース VM をシャットダウンしようとします。 仮にシャットダウンが失敗したとしても、フェールオーバーは続行されます。 フェールオーバーの進行状況は **[ジョブ]** ページで確認できます。
4. フェールオーバーを確認したら、**[コミット]** をクリックします。 これにより、利用可能なすべての復旧ポイントが削除されます。

> [!WARNING]
> **進行中のフェールオーバーをキャンセルしないでください**。進行中のフェールオーバーをキャンセルすると、フェールオーバーは停止しますが、VM が再びレプリケートされることはありません。

## <a name="connect-to-failed-over-vm"></a>フェールオーバーされた VM に接続する

1. リモート デスクトップ プロトコル (RDP) と Secure Shell (SSH) を使用して、フェールオーバー後の Azure VM に接続する場合は、[要件が満たされていることを確認](failover-failback-overview.md#connect-to-azure-after-failover)します。
2. フェールオーバー後は、VM に移動し、それに[接続する](../virtual-machines/windows/connect-logon.md)ことで検証します。
3. フェールオーバー後に別の復旧ポイントを使用する場合は、**[復旧ポイントの変更]** を使用します。 次の手順でフェールオーバーをコミットした後、このオプションは使用できなくなります。
4. 検証後に、**[コミット]** を選択して、フェールオーバー後の VM の復旧ポイントを最終処理します。
5. コミット後、他の使用可能なすべての復旧ポイントが削除されます。 この手順でフェールオーバーが完了します。

>[!TIP]
> フェールオーバー後に接続の問題が発生した場合は、[トラブルシューティング ガイド](site-recovery-failover-to-azure-troubleshoot.md)に従ってください。


## <a name="next-steps"></a>次のステップ

フェールオーバー後に、Azure からオンプレミスにレプリケートされるように Azure VM を再保護します。 次に、VM を再保護してオンプレミス サイトにレプリケートした後、準備が整ったら Azure からフェールバックします。
