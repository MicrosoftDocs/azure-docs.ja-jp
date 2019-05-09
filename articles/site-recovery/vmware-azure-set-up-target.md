---
title: Azure への VMware レプリケーションのターゲット環境を準備する | Microsoft Docs
description: この記事では、Azure への VMware VM レプリケーションの Azure ターゲット環境を準備する方法について説明します。
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/03/2019
ms.author: mayg
ms.openlocfilehash: e75d4b1701944e206fcf6ded2dcb6d1e1fbc77cb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57900558"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>VMware VM または物理サーバーの Azure へのディザスター リカバリーのためのターゲット環境を準備する

この記事では、VMware 仮想マシンおよび物理サーバーの Azure へのレプリケーションを開始するための Azure ターゲット環境を準備する方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事は以下を前提としています。
- Recovery Services コンテナーを[Azure portal](https://portal.azure.com "Azure portal")上で作成し、ソース マシンを保護します。
- ソースの[VMware の仮想マシン](vmware-azure-set-up-source.md)および[物理サーバー](physical-azure-set-up-source.md)を Azure にレプリケートできるようにオンプレミス環境をセットアップします。

## <a name="prepare-target"></a>ターゲットを準備する

**ステップ 1:保護の目標の選択**と**ステップ 2:ソースの準備**を完了した後は、**ステップ 3:ターゲットの準備**を行います

![ターゲットを準備する](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **サブスクリプション:** ドロップダウン メニューから、仮想マシンおよび物理サーバーのレプリケート先のサブスクリプションを選びます。
2. **デプロイ モデル:** デプロイ モデルを選びます (クラシックまたは Resource Manager)

選んだデプロイ モデルに基づいて検証が実行され、ご自分の仮想マシンおよび物理サーバーのレプリケートおよびフェールオーバー先のターゲット サブスクリプションに、仮想ネットワークが少なくとも 1 つはあることが確認されます。

検証が正常に完了した後、[OK] をクリックして次の手順に進みます。

仮想ネットワークがない場合は、ページの上部にある **[+ ネットワーク]** ボタンをクリックして作成できます。

## <a name="next-steps"></a>次の手順
[レプリケーションの設定を構成する](vmware-azure-set-up-replication.md)。
