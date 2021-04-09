---
title: Azure Site Recovery で追加した Azure VM ディスクのレプリケーションを有効にする
description: この記事では、Azure Site Recovery のディザスター リカバリーで可能な、Azure VM に追加されたディスクのレプリケーションを有効にする方法を説明します。
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2019
ms.openlocfilehash: 6cbbe63d7968816de78256f5a8408517bb8da278
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "75973792"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>Azure VM に追加されたディスクのレプリケーションを有効にする


この記事は、[Azure Site Recovery](site-recovery-overview.md) を使用した別の Azure リージョンへのディザスター リカバリーで可能な、Azure VM に追加されたデータ ディスクのレプリケーションを有効にする方法を説明します。

VM に追加したディスクのレプリケーションの有効化は、マネージド ディスクがある Azure VM でサポートされます。

別の Azure リージョンにレプリケートする Azure VM に新しいディスクを追加すると、以下の処理が行われます。

-   VM のレプリケーション正常性では警告が表示され、ポータルのメモでは、1 つまたは複数のディスクが保護に使用できることが通知されます。
-   追加したディスクの保護を有効にした場合、ディスクの初期レプリケーション後に警告は表示されなくなります。
-   ディスクのレプリケートを有効にしないことを選択した場合は、この警告の無視を選択できます。

![新しいディスクの追加](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>開始する前に

この記事では、ディスクを追加する VM にディザスター リカバリーが既にセットアップ済みであることが前提となっています。 まだである場合は、「[Azure VM のディザスター リカバリーを設定する](azure-to-azure-tutorial-enable-replication.md)」チュートリアルを参照してください。

## <a name="enable-replication-for-an-added-disk"></a>追加したディスクのレプリケーションを有効にする

追加されたディスクのレプリケーションを有効にするには、次の手順を実行します。

1. コンテナーの **[レプリケートされたアイテム]** で、ディスクを追加した VM をクリックします。
2. **[ディスク]** をクリックし、レプリケーションを有効にするデータ ディスクを選択します (これらのディスクの状態は **未保護** です。)。
3.  **[ディスクの詳細]** で、 **[レプリケーションを有効にする]** をクリックします。

    ![追加されたディスクのレプリケーションを有効にする](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

レプリケーションの有効化ジョブを実行して初期レプリケーションが完了すると、ディスク問題のレプリケーション正常性についての警告は削除されます。



## <a name="next-steps"></a>次のステップ

テスト フェールオーバーの実行に関する[詳細を確認](site-recovery-test-failover-to-azure.md)する。
