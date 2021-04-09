---
title: Azure Site Recovery で物理サーバーのターゲット環境を設定する
description: この記事では、Azure Site Recovery を使用した物理サーバーのディザスター リカバリーのためのターゲット Azure 環境を設定する方法について説明します。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 685f04b27cd315b04ef5c45e155e825eebe2747b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "73953898"
---
# <a name="prepare-target-vmware-to-azure"></a>ターゲットを準備する (VMware から Azure)

この記事では、Windows または Linux を実行している物理サーバー (x64)を Azure にレプリケートする前に、Azure 環境を準備する方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事は以下を前提としています。
- 物理サーバーを保護するための Recovery Services コンテナーを作成してあること。 Recovery Services コンテナーは [Azure portal](https://portal.azure.com "Azure portal") から作成できます。
- 物理サーバーを Azure にレプリケートできるように[オンプレミス環境をセットアップ](physical-azure-disaster-recovery.md)してあること。

## <a name="prepare-target"></a>ターゲットを準備する

**ステップ 1: 保護の目標の選択** と **ステップ 2: ソースの準備** を完了した後は、**ステップ 3: ターゲットの準備** を行います。

![ターゲットを準備する](./media/physical-azure-set-up-target/prepare-target-physical-to-azure.png)

1. **サブスクリプション:** ドロップダウン メニューから、物理サーバーのレプリケート先のサブスクリプションを選びます。
2. **デプロイメント モデル:** デプロイメント モデルを選びます (クラシックまたは Resource Manager)。

選んだデプロイメント モデルに基づいて検証が実行され、物理サーバーのレプリケートおよびフェールオーバー先のターゲット サブスクリプションに、互換性のあるストレージ アカウントと仮想ネットワークが少なくとも 1 つはあることが確認されます。

検証が正常に完了した後、[OK] をクリックして次の手順に進みます。

互換性のある Resource Manager ストレージ アカウントまたは仮想ネットワークがない場合は、ページの上部にある **[+ ストレージ アカウント]** または **[+ ネットワーク]** ボタンをクリックして作成できます。

## <a name="next-steps"></a>次のステップ
[レプリケーションの設定を構成する](vmware-azure-set-up-replication.md)。
