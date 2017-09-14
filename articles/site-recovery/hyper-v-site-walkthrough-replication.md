---
title: "Azure Site Recovery を使用した Azure への Hyper-V VM のレプリケーション (System Center VMM なし) にレプリケーション ポリシーを設定する | Microsoft Docs"
description: "Azure Storage への Hyper-V VM のレプリケーションに関するレプリケーション ポリシーを設定するために必要な手順の概要を示します"
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 1777e0eb-accb-42b5-a747-11272e131a52
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: ca5bec5cf1152e6259b9fe7a869edd2d62b88e1a
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---

# <a name="step-9-set-up-a-replication-policy-for-hyper-v-vm-replication-to-azure"></a>手順 9: Azure への Hyper-V VM のレプリケーションに関するレプリケーション ポリシーを設定する

この記事では、Azure Portal の [Azure Site Recovery](site-recovery-overview.md) サービスを使用して Azure に Hyper-V VM (System Center VMM なし) をレプリケートする場合にレプリケーション ポリシーを設定する方法について説明します。


コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。

## <a name="about-snapshots"></a>スナップショットについて

Hyper-V では 2 種類のバックアップを使用します。1 つは標準バックアップで、仮想マシン全体の増分バックアップを実行します。もう 1 つは、アプリケーション整合性スナップショットで、仮想マシン内部のアプリケーション データの特定の時点のスナップショットを作成します。
    - アプリケーション整合性スナップショットでは、ボリューム シャドウ コピー サービス (VSS) を使用して、スナップショットを作成するときにアプリケーションを一貫性のある状態に保ちます。
    - アプリケーション整合性スナップショットを有効にすると、ソースの仮想マシンで実行されるアプリケーションのパフォーマンスに影響があります。 設定する値は、追加で構成する復旧ポイントの数より少ない数にしてください。

## <a name="set-up-a-replication-policy"></a>レプリケーション ポリシーを設定する

1. 新しいレプリケーション ポリシーを作成するには、**[インフラストラクチャの準備]** > **[レプリケーションの設定]** > **[+ 作成と関連付け]** の順にクリックします。

    ![ネットワーク](./media/hyper-v-site-walkthrough-replication/gs-replication.png)
2. **[ポリシーの作成と関連付け]**で、ポリシー名を指定します。
3. **[コピーの頻度]**で、初期レプリケーションの後、差分データをレプリケートする頻度 (30 秒ごと、5 分ごと、または 15 分ごと) を指定します。

    > [!NOTE]
    > Premium Storage にレプリケートするときには、30 秒の頻度はサポートされません。 上限は、Premium Storage によってサポートされる BLOB ごとのスナップショットの数 (100) によって決まります。 [詳細情報](../storage/common/storage-premium-storage.md#snapshots-and-copy-blob)。

4. **[復旧ポイントの保持期間]** で、各復旧ポイントのリテンション期間の長さ (時間単位) を指定します。 VM はこの期間内のどのポイントにも復旧できます。
5. **[アプリ整合性スナップショットの頻度]** で、アプリケーション整合性スナップショットを含む復旧ポイントの作成頻度 (1 - 12 時間) を指定します。
6. **[初期レプリケーションの開始時刻]** で、初期レプリケーションを開始する時刻を指定します。 レプリケーションはご利用のインターネット帯域幅で行われるため、トラフィックの多い時間帯を避けるようにスケジュールを設定することをお勧めします。 次に、 **[OK]**をクリックします

    ![Replication policy](./media/hyper-v-site-walkthrough-replication/gs-replication2.png)

新しいポリシーを作成すると、自動的に Hyper-V サイトに関連付けられます。 Hyper-V サイト (およびサイト内の VM) を複数のレプリケーション ポリシーに関連付けるには、**[レプリケーション]**、ポリシー名、**[Associate Hyper-V Site (Hyper-V サイトの関連付け)]** の順にクリックします。



## <a name="next-steps"></a>次のステップ

[手順 10: レプリケーションを有効にする](hyper-v-site-walkthrough-enable-replication.md)方法に関するページに進む

