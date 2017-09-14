---
title: "Azure リージョン間での Azure IaaS VM の移行 | Microsoft Docs"
description: "Azure Site Recovery を使用して、異なる Azure リージョン間で Azure IaaS 仮想マシンを移行します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8a29e0d9-0010-4739-972f-02b8bdf360f6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: ef2972c077a2b1dd2b2fd6ce53cc6560520ea870
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Azure Site Recovery を使用した Azure リージョン間での Azure IaaS 仮想マシンの移行
## <a name="overview"></a>Overview
Azure Site Recovery へようこそ。 この記事は、Azure VM を Azure リージョン間で移行する場合に役立ちます。 開始する前に、次のことに注意してください。

* Azure には、リソースの作成と操作に関して、Azure Resource Manager とクラシックの 2 種類のデプロイメント モデルがあります。 また、Azure にも 2 つのポータルがあります。クラシック デプロイメント モデルをサポートする Azure クラシック ポータルと、両方のデプロイメント モデルをサポートする Azure ポータルです。 Site Recovery を Resource Manager とクラシックのどちらで構成するかに関係なく、移行の基本的な手順は同じです。 ただし、この記事の UI の説明とスクリーンショットは、Azure ポータルに適用されます。
* **現時点では、リージョン間の一方向の移行だけが可能です。VM を Azure リージョン間でフェールオーバーできますが、フェールバックすることはできません。**

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。

## <a name="prerequisites"></a>前提条件
このデプロイで必要なものを次に示します。

* **IaaS 仮想マシン**: 移行する VM。 これらの VM を物理コンピューターとして扱って移行します。

## <a name="deployment-steps"></a>デプロイメントの手順
このセクションでは、新しい Azure ポータルでのデプロイの手順について説明します。

1. [コンテナーを作成します](site-recovery-vmware-to-azure.md)。
2. [レプリケーションを有効にします](site-recovery-vmware-to-azure.md)。 移行する VM のレプリケーションを有効にして、ソースとして Azure を選択します。 
3. [ 計画されていないフェールオーバーを実行します](site-recovery-failover.md)。 初期レプリケーションが完了したら、Azure リージョン間で計画されていないフェールオーバーを実行できます。 必要に応じて、復旧計画を作成し、計画されていないフェールオーバーを実行して、複数の仮想マシンをリージョン間で移行できます。 [こちら](site-recovery-create-recovery-plans.md) をご覧ください。

## <a name="next-steps"></a>次のステップ
その他のレプリケーション シナリオの詳細については、 [Azure Site Recovery の概要](site-recovery-overview.md)

