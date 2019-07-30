---
title: Azure Site Recovery によってレプリケートされた Azure VM に追加されたディスクのレプリケーションを有効にする | Microsoft Docs
description: この記事では、Azure Site Recovery のディザスター リカバリーで可能な、Azure VM に追加されたディスクのレプリケーションを有効にする方法を説明します。
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: asgang
ms.openlocfilehash: 4a262a3a0c32516988890a6afc6eef34d8655c89
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671873"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>Azure VM に追加されたディスクのレプリケーションを有効にする


この記事は、[Azure Site Recovery](site-recovery-overview.md) を使用した別の Azure リージョンへのディザスター リカバリーで可能な、Azure VM に追加されたデータ ディスクのレプリケーションを有効にする方法を説明します。

VM に追加したディスクのレプリケーションの有効化は、マネージド ディスクがある Azure VM でサポートされます。

別の Azure リージョンにレプリケートする Azure VM に新しいディスクを追加すると、以下の処理が行われます。

-   VM のレプリケーション正常性では警告が表示され、ポータルのメモでは、1 つまたは複数のディスクが保護に使用できることが通知されます。
-   追加したディスクの保護を有効にした場合、ディスクの初期レプリケーション後に警告は表示されなくなります。
-   ディスクのレプリケーションを有効にしないことを選択した場合、この警告は無視できます。

![新しいディスクの追加](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>開始する前に

この記事では、ディスクを追加する VM にディザスター リカバリーが既にセットアップ済みであることが前提となっています。 まだである場合は、「[Azure VM のディザスター リカバリーを設定する](azure-to-azure-tutorial-enable-replication.md)」チュートリアルを参照してください。 

## <a name="enable-replication-for-an-added-disk"></a>追加したディスクのレプリケーションを有効にする 

追加したディスクのレプリケーションを有効にするには、次の操作を行います。

1. コンテナーの **[レプリケートされたアイテム]** で、ディスクを追加した VM をクリックします。
2. **[ディスク]** をクリックし、レプリケーションを有効にするデータ ディスクを選択します (これらのディスクの状態は**未保護**です。)。
3.  **[ディスクの詳細]** で、 **[レプリケーションを有効にする]** をクリックします。

    ![追加したディスクのレプリケーションを有効にする](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

レプリケーションの有効化ジョブを実行して初期レプリケーションが完了すると、ディスク問題のレプリケーション正常性についての警告は削除されます。



# <a name="next-steps"></a>次の手順

テスト フェールオーバーの実行に関する[詳細を確認](site-recovery-test-failover-to-azure.md)する。
