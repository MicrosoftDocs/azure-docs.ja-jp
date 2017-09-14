---
title: "Azure Site Recovery を使用した Azure への Hyper-V VM (VMM あり) のレプリケーションに関するレプリケーション ポリシーを設定する | Microsoft Docs"
description: "Azure Site Recovery を使用した Azure への Hyper-V VM (VMM あり) のレプリケーションに関するレプリケーション ポリシーを設定する方法を説明します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: ee808b20-324b-4198-b831-edb65b95e8b7
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 592e1c3f647e5b1f1d9aa776657e8f89b60349e1
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---
# <a name="step-10-set-up-a-replication-policy-for-hyper-v-vm-replication-with-vmm-to-azure"></a>手順 10: Azure への Hyper-V VM (VMM あり) のレプリケーションに関するレプリケーション ポリシーを設定する


[ネットワーク マッピング](vmm-to-azure-walkthrough-network-mapping.md)を設定したら、この記事の手順に従って、System Center Virtual Machine Manager (VMM) で管理されているオンプレミスの Hyper-V 仮想マシンを、Azure Portal の [Azure Site Recovery](site-recovery-overview.md) を使用して Azure にレプリケートするためのレプリケーション ポリシーを構成します。

この記事に関するコメントは、この記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。



## <a name="create-a-policy"></a>ポリシーの作成

1. 新しいレプリケーション ポリシーを作成するには、**[インフラストラクチャの準備]** > **[レプリケーションの設定]** > **[+ 作成と関連付け]** の順にクリックします。

    ![ネットワーク](./media/vmm-to-azure-walkthrough-replication/gs-replication.png)
2. **[ポリシーの作成と関連付け]**で、ポリシー名を指定します。
3. **[コピーの頻度]**で、初期レプリケーションの後、差分データをレプリケートする頻度 (30 秒ごと、5 分ごと、または 15 分ごと) を指定します。

    > [!NOTE]
    >  Premium Storage にレプリケートするときには、30 秒の頻度はサポートされません。 上限は、Premium Storage によってサポートされる blob ごとのスナップショットの数 (100) によって決まります。 [詳細情報](../storage/common/storage-premium-storage.md#snapshots-and-copy-blob)

4. **[復旧ポイントの保持期間]**で、各復旧ポイントのリテンション期間の長さを時間単位で指定します。 保護されたマシンはこの期間内のどのポイントにも復旧できます。
5. **[アプリ整合性スナップショットの頻度]** で、アプリケーション整合性スナップショットを含む復旧ポイントの作成頻度 (1 ～ 12 時間) を指定します。 Hyper-V では 2 種類のバックアップを使用します。1 つは標準バックアップで、仮想マシン全体の増分バックアップを実行します。もう 1 つは、アプリケーション整合性スナップショットで、仮想マシン内部のアプリケーション データの特定の時点のスナップショットを作成します。 アプリケーション整合性スナップショットでは、ボリューム シャドウ コピー サービス (VSS) を使用して、スナップショットを作成するときにアプリケーションを一貫性のある状態に保ちます。 アプリケーション整合性スナップショットを有効にすると、ソースの仮想マシンで実行するアプリケーションのパフォーマンスに影響があります。 設定する値は、追加で構成する復旧ポイントの数より少ない数にしてください。
6. **[初期レプリケーションの開始時刻]**で、初期レプリケーションを開始する時刻を指定します。 レプリケーションはご利用のインターネット帯域幅で行われるため、トラフィックの多い時間帯を避けるようにスケジュールを設定することをお勧めします。
7. **[Azure に格納されるデータの暗号化]**で、Azure ストレージに格納されるデータを暗号化するかどうかを指定します。 次に、 **[OK]**をクリックします

    ![Replication policy](./media/vmm-to-azure-walkthrough-replication/gs-replication2.png)
8. 新しいポリシーを作成すると、自動的に VMM クラウドに関連付けられます。 **[OK]**をクリックします。 追加の VMM クラウド (およびその内部にある VM) を、このレプリケーション ポリシーに関連付けるには、**[レプリケーション]** > ポリシー名 > **[Associate VMM Cloud (VMM クラウドを関連付ける)]** の順に選択します。

    ![Replication policy](./media/vmm-to-azure-walkthrough-replication/policy-associate.png)



## <a name="next-steps"></a>次のステップ

[手順 11: レプリケーションを有効にする](vmm-to-azure-walkthrough-enable-replication.md)方法に関するページに進む

