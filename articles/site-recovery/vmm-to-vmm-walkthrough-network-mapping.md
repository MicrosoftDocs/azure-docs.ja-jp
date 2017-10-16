---
title: "Azure Site Recovery を使用してセカンダリ サイトへの Hyper-V VM レプリケーション用のネットワークをマッピングする | Microsoft Docs"
description: "Azure Site Recovery を使用して、Hyper-V VM をセカンダリ VMM サイトにレプリケーションする場合のネットワークのマッピング方法について説明します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 461b7c1c-ef61-4005-aeec-2324e727a3d0
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 606e2d3d0e31b9d80200105e812f9d7bbbcf939c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="step-7-map-networks-for-hyper-v-vm-replication-to-a-secondary-site"></a>手順 7: セカンダリ サイトへの Hyper-V VM のレプリケーション用のネットワークをマッピングする


Hyper-V 仮想マシン (VM) をセカンダリ System Center Virtual Machine Manager (VMM) サイトにレプリケートするための[ソースとターゲットの設定](vmm-to-vmm-walkthrough-source-target.md)を設定した後で、この記事の内容を使用して、[Azure Site Recovery](site-recovery-overview.md) を使用し、セカンダリ サイトへの HYPER-V 仮想マシン (VM) のレプリケーション用のネットワーク マッピングを構成します。

この記事に関するコメントは、この記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。


## <a name="before-you-start"></a>開始する前に

- 始める前に[ネットワーク マッピング](vmm-to-vmm-walkthrough-network.md#network-mapping-overview)について学びます。
- VMM サーバー上の仮想マシンが VM ネットワークに接続されていることを確認します。

## <a name="configure-network-mapping"></a>ネットワーク マッピングの構成

1. **[ネットワーク マッピング]** > **[ネットワーク マッピング]** で、**[+ ネットワーク マッピング]** をクリックします。
2. **[ネットワーク マッピングの追加]** タブで、ソース VMM サーバーとターゲット VMM サーバーを選択します。 VMM サーバーに関連付けられている VM ネットワークが取得されます。
3. **[ソース ネットワーク]**で、プライマリ VMM サーバーに関連付けられている VM ネットワークの一覧から、使用するネットワークを選択します。
4. **[ターゲット ネットワーク]** で、セカンダリ VMM サーバーで使用するネットワークを選択します。 次に、 **[OK]**をクリックします

    ![ネットワーク マッピング](./media/vmm-to-vmm-walkthrough-network-mapping/network-mapping2.png)

ネットワーク マッピングが開始されると、次のように動作します。

* ソース VM ネットワークに対応する既存のレプリカの全仮想マシンが、ターゲット VM ネットワークに接続します。
* ソース VM ネットワークに接続する新しい仮想マシンは、レプリケーション後、マップされたターゲット ネットワークに接続します。
* 既存のマッピングを新しいネットワークで変更すると、レプリカの仮想マシンは新しい設定で接続されます。
* ターゲット ネットワークに複数のサブネットがあり、そのサブネットのいずれかが、ソースの仮想マシンが配置されているサブネットと同じ名前である場合、フェールオーバー後、レプリカの仮想マシンはそのターゲット サブネットに接続されます。 ターゲットのサブネットで名前が一致するものがなければ、仮想マシンはネットワークの最初のサブネットに接続されます。



## <a name="next-steps"></a>次のステップ

「[手順 8: レプリケーション ポリシーを構成する](vmm-to-vmm-walkthrough-replication.md)」に進みます。