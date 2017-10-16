---
title: "Azure Site Recovery を使用して Azure に Hyper-V VM (System Center VMM あり) をレプリケートするために Azure リソースを準備する | Microsoft Docs"
description: "Azure Site Recovery を使用して Hyper-V VM (System Center VMM あり) を Azure にレプリケートする操作を開始する前に Azure で行う必要がある準備について説明します"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 1568bdc3-e767-477b-b040-f13699ab5644
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: 63b005f37ab5e15e8a1b4645446d65f1529f1bbd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="step-5-prepare-azure-resources-for-hyper-v-replication-with-vmm-to-azure"></a>手順 5: Azure への Hyper-V レプリケーション (VMM あり) 用の Azure リソースを準備する

[ネットワーク要件](vmm-to-azure-walkthrough-network.md)を確認した後で、この記事の手順に従って Azure リソースを準備して、[Azure Site Recovery](site-recovery-overview.md) サービスを使用して、System Center Virtual Machine Manager (VMM) クラウド内のオンプレミスの Hyper-V VM を Azure にレプリケートできるようにします。

コメントはこの記事の末尾に投稿し、技術的な質問は [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。


## <a name="set-up-an-azure-account"></a>Azure アカウントを設定する

- [Microsoft Azure アカウント](http://azure.microsoft.com/)を取得します。
- アカウントがなくても、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)を使用できます。
- Site Recovery でサポートされるリージョンについては、[Azure Site Recovery の価格の詳細](https://azure.microsoft.com/pricing/details/site-recovery/)に関するページの利用可能なリージョンに関するセクションをご確認ください。
- [Site Recovery の価格](site-recovery-faq.md#pricing)について理解し、[価格の詳細](https://azure.microsoft.com/pricing/details/site-recovery/)を確認します。
- Azure アカウントに Azure VM を作成するための正しい[アクセス許可](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)があることを確認します。 Azure のロールベースのアクセス制御の[詳細について学習します](../active-directory/role-based-access-built-in-roles.md)。


## <a name="set-up-an-azure-network"></a>Azure ネットワークをセットアップする

- [Azure ネットワーク](../virtual-network/virtual-network-get-started-vnet-subnet.md)をセットアップします。 Azure VM は、フェールオーバー後に作成されたときに、このネットワークに配置されます。
- ネットワークは、Recovery Services コンテナーと同じリージョンにある必要があります。
- Azure Portal の Site Recovery では、[Resource Manager](../resource-manager-deployment-model.md) またはクラシック モードで設定されたネットワークを使用できます。
- ネットワークをセットアップしてから、以下の作業を開始することをお勧めします。 行わない場合は、Site Recovery のデプロイ中に行う必要があります。
- [仮想ネットワークの価格](https://azure.microsoft.com/pricing/details/virtual-network/)について理解します。


## <a name="set-up-an-azure-storage-account"></a>Azure Storage アカウントを設定

- Site Recovery は、オンプレミスのマシンを Azure Storage にレプリケートします。 Azure VM は、フェールオーバーの発生後にストレージから作成されます。
- Azure にレプリケートされたデータを保持するために、Standard/Premium [Azure Storage アカウント](../storage/common/storage-create-storage-account.md#create-a-storage-account)をセットアップします。
- [Premium Storage](../storage/common/storage-premium-storage.md) は、通常、IO を集中的に行うワークロードをホストするために常に高い IO パフォーマンスと低遅延時間が必要になる仮想マシンで使用します。
- レプリケートされたデータを Premium アカウントを使用して保存する場合は、オンプレミスのデータの継続的な変更をキャプチャするレプリケーション ログを保存するために、Standard ストレージ アカウントも必要になります。
- フェールオーバーされた Azure VM に使用するリソース モデルに応じて、アカウントを [Resource Manager モード](../storage/common/storage-create-storage-account.md)または[クラシック モード](../storage/common/storage-create-storage-account.md)でセットアップします。
- ストレージ アカウントをセットアップしてから、以下の作業を開始することをお勧めします。 行わない場合は、Site Recovery のデプロイ中に行う必要があります。 アカウントは、Recovery Services コンテナーと同じリージョンに存在する必要があります。
- 同じサブスクリプション内のリソース グループ間、または異なるサブスクリプション間で、Site Recovery で使用されるストレージ アカウントを移動することはできません。


## <a name="next-steps"></a>次のステップ

[手順 6: VMM を準備する](vmm-to-azure-walkthrough-vmm-hyper-v.md)方法に関するページに進む
