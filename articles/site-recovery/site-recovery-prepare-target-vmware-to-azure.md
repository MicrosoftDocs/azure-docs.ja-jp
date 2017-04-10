---
title: "ターゲットを準備する (VMware から Azure) | Microsoft Docs"
description: "この記事では、VMware の仮想マシンを Azure にレプリケートする前に Azure 環境を準備する方法について説明します。"
services: site-recovery
documentationcenter: 
author: bsiva
manager: abhemraj
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 2/11/2017
ms.author: bsiva
translationtype: Human Translation
ms.sourcegitcommit: b7f26df96ddedb579cf5d9d20fee6b2599e762e0
ms.openlocfilehash: 555ea8eed17e42019513029f7b3ac2167f002578

---

# <a name="prepare-target-vmware-to-azure"></a>ターゲットを準備する (VMware から Azure)
> [!div class="op_single_selector"]
> * [VMware 仮想マシン](./site-recovery-prepare-target-vmware-to-azure.md)
> * [物理サーバー](./site-recovery-prepare-target-physical-to-azure.md)

この記事では、VMware の仮想マシンを Azure にレプリケートする前に Azure 環境を準備する方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事では、以下を前提としています。
- VMware の仮想マシンを保護するための Recovery Services コンテナーを作成してあること。 Recovery Services コンテナーは [Azure Portal](http://portal.azure.com "Azure Portal") から作成できます。
- VMware の仮想マシンを Azure にレプリケートできるように[オンプレミス環境をセットアップ](./site-recovery-set-up-vmware-to-azure.md)してあること。

## <a name="prepare-target"></a>ターゲットを準備する

**ステップ 1: 保護の目標の選択**と**ステップ 2: ソースの準備**を完了した後は、**ステップ 3: ターゲットの準備**を行います。

![ターゲットを準備する](./media/site-recovery-prepare-target-vmware-to-azure/prepare-target-vmware-to-azure.png)

1. **サブスクリプション:** ドロップダウン メニューから、仮想マシンのレプリケート先のサブスクリプションを選びます。
2. **デプロイメント モデル:** デプロイメント モデルを選びます (クラシックまたは Resource Manager)。

選んだデプロイメント モデルに基づいて検証が実行され、仮想マシンのレプリケートおよびフェールオーバー先のターゲット サブスクリプションに、互換性のあるストレージ アカウントと仮想ネットワークが少なくとも&1; つはあることが確認されます。

検証が正常に完了した後、[OK] をクリックして次の手順に進みます。

互換性のある Resource Manager ストレージ アカウントまたは仮想ネットワークがない場合、またはさらに追加したい場合は、ブレードの上部にある **[+ ストレージ アカウント]** または **[+ ネットワーク]** ボタンをクリックして追加できます。

## <a name="next-steps"></a>次のステップ
[レプリケーションの設定を構成する](./site-recovery-setup-replication-settings-vmware.md)。



<!--HONumber=Feb17_HO2-->


