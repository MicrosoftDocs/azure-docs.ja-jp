---
title: "Azure Site Recovery を使用してオンプレミスの VMware VM を Azure にレプリケートするための Azure リソースを準備する | Microsoft Docs"
description: "Azure Site Recovery を使用してオンプレミスの VMware VM を Azure にレプリケートする操作を開始する前に Azure で行う必要がある準備について説明します"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 4e320d9b-8bb8-46bb-ba21-77c5d16748ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 40abff72278c9f8d9f701023fd473fe52c17b421
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---
# <a name="step-5-prepare-azure-resources-for-vmware-replication-to-azure"></a>手順 5: Azure への VMware レプリケーション用の Azure リソースを準備する


この記事の手順に従って、[Azure Site Recovery](site-recovery-overview.md) サービスを使用してオンプレミス マシンを Azure にレプリケートできるように Azure リソースを準備します。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。

## <a name="before-you-start"></a>開始する前に

[前提条件](vmware-walkthrough-prerequisites.md)を必ず確認してください。

## <a name="set-up-an-azure-account"></a>Azure アカウントを設定する

- [Microsoft Azure アカウント](http://azure.microsoft.com/)を取得します。
- アカウントがなくても、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)を使用できます。
- Site Recovery でサポートされるリージョンについては、[Azure Site Recovery の価格の詳細](https://azure.microsoft.com/pricing/details/site-recovery/)に関するページの利用可能なリージョンに関するセクションをご確認ください。
- [Site Recovery の価格](site-recovery-faq.md#pricing)について理解し、[価格の詳細](https://azure.microsoft.com/pricing/details/site-recovery/)を確認します。



## <a name="set-up-an-azure-network"></a>Azure ネットワークをセットアップ

- Azure ネットワークをセットアップします。 Azure VM は、フェールオーバー後に作成されたときに、このネットワークに配置されます。
- Azure Portal の Site Recovery では、[Resource Manager](../resource-manager-deployment-model.md) またはクラシック モードでセットアップされたネットワークを使用できます。
- ネットワークは、Recovery Services コンテナーと同じリージョンにある必要があります。
- [仮想ネットワークの価格](https://azure.microsoft.com/pricing/details/virtual-network/)を確認します。
- フェールオーバー後の [Azure VM の接続](site-recovery-network-design.md)の詳細を確認します。


## <a name="set-up-an-azure-storage-account"></a>Azure Storage アカウントを設定

- Site Recovery は、オンプレミスのマシンを Azure Storage にレプリケートします。 Azure VM は、フェールオーバーの発生後にストレージから作成されます。
- レプリケートされるデータ用に [Azure ストレージ アカウント](../storage/common/storage-create-storage-account.md#create-a-storage-account)をセットアップします。
- Azure ポータルの Site Recovery では、Resource Manager またはクラシック モードで設定されたストレージ アカウントを使用できます。
- このストレージ アカウントには、Standard または [Premium](../storage/common/storage-premium-storage.md) を使用できます。
- Premium アカウントを設定する場合は、ログ データ用の Standard アカウントも別途必要になります。


## <a name="next-steps"></a>次のステップ

[手順 6: VMware リソースを準備する](vmware-walkthrough-prepare-vmware.md)方法に関するページに進む

