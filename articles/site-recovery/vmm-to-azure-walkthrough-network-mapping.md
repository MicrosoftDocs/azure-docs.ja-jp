---
title: "Azure Site Recovery を使用して VMM クラウド内の HYPER-V を Azure にレプリケートするためのネットワーク マッピングを構成する |Microsoft Docs"
description: "この記事では、Azure Site Recovery を使用して、VMM クラウド内の Hyper-V VM を Azure にレプリケートする場合のネットワーク マッピングを構成する方法について説明します"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: ed6f73d8baea5af0d2aa5f0ae885f305911ccc82
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="step-9-configure-network-mapping-for-hyper-v-replication-with-vmm-to-azure"></a>手順 9: HYPER-V の Azure へのレプリケーション (VMM あり) のネットワーク マッピングを構成する

[ソースとターゲットのレプリケーション設定](vmm-to-azure-walkthrough-source-target.md)を設定した後で、この記事に従って、オンプレミスの VMM VM ネットワークと Azure ネットワーク間にマップするネットワーク マッピングを構成します。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。

## <a name="before-you-start"></a>開始する前に

- [ネットワーク マッピング](vmm-to-azure-walkthrough-network.md#network-mapping-for-replication-to-azure)の詳細を説明します。
- [ネットワーク マッピング用に VMM を準備します](vmm-to-azure-walkthrough-network.md#prepare-vmm-for-network-mapping)。 
- VMM サーバー上の仮想マシンが VM ネットワークに接続されており、1 つ以上の Azure 仮想ネットワークが作成されていることを確認してください。 1 つの Azure ネットワークに対して複数の VM ネットワークをマップできます。

## <a name="configure-mapping"></a>マッピングを構成する

マッピングは次のように構成します。

1. **[Site Recovery Infrastructure (Site Recovery インフラストラクチャ)]** > **[ネットワーク マッピング]** > **[ネットワーク マッピング]** の順にクリックし、**[+ ネットワーク マッピング]** アイコンをクリックします。

    ![ネットワーク マッピング](./media/vmm-to-azure-walkthrough-network-mapping/network-mapping1.png)
2. **[ネットワーク マッピングの追加]** で、ソース VMM サーバーを選択し、ターゲットとして **[Azure]** を選択します。
3. サブスクリプションとフェールオーバー後のデプロイメント モデルを確認します。
4. **[ソース ネットワーク]**で、VMM サーバーに関連付けられている一覧から、マップするソースのオンプレミス VM ネットワークを選択します。
5. **[ターゲット ネットワーク]**で、レプリカの Azure VM が作成されたときに配置される Azure ネットワークを選択します。 次に、 **[OK]**をクリックします

    ![ネットワーク マッピング](./media/vmm-to-azure-walkthrough-network-mapping/network-mapping2.png)

ネットワーク マッピングが開始されると、次のように動作します。

* ソースの VM ネットワーク上の既存の VM は、マッピングが開始されるとターゲット ネットワークに接続します。 ソースの VM ネットワークに接続する新しい VM は、レプリケーションが実行されると、マップされた Azure ネットワークに接続します。
* 既存のネットワーク マッピングを変更すると、レプリカの仮想マシンは新しい設定を使用して接続します。
* ターゲット ネットワークに複数のサブネットがあり、そのサブネットのいずれかが、ソースの仮想マシンが配置されているサブネットと同じ名前である場合、フェールオーバー後、レプリカの仮想マシンはそのターゲット サブネットに接続します。
* ターゲットのサブネットで名前が一致するものがなければ、仮想マシンはネットワークの最初のサブネットに接続します。



## <a name="next-steps"></a>次のステップ

「[手順 10: レプリケーション ポリシーを作成する](vmm-to-azure-walkthrough-replication.md)」に進みます。
