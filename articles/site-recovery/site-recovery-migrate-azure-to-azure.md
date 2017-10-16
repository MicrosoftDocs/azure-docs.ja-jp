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
ms.date: 08/31/2017
ms.author: raynew
ms.openlocfilehash: 86806c5dbafc1fd88c434dcee6292683d050cd2a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Azure Site Recovery を使用した Azure リージョン間での Azure IaaS 仮想マシンの移行
## <a name="overview"></a>Overview
Azure Site Recovery へようこそ。 この記事は、Azure VM を Azure リージョン間で移行する場合に役立ちます。
>[!NOTE]
>
> ディザスター リカバリーや移行のニーズに応じて Azure VM を別のリージョンにレプリケートする場合については、[こちらのドキュメント](site-recovery-azure-to-azure.md)を参照してください。 Azure 仮想マシンの Site Recovery レプリケーションは現在プレビューの段階です。

開始する前に、次のことに注意してください。

* Azure には、リソースの作成と操作に関して、Azure Resource Manager とクラシックの 2 種類のデプロイメント モデルがあります。 また、Azure にも 2 つのポータルがあります。クラシック デプロイメント モデルをサポートする Azure クラシック ポータルと、両方のデプロイメント モデルをサポートする Azure ポータルです。 Site Recovery を Resource Manager とクラシックのどちらで構成するかに関係なく、移行の基本的な手順は同じです。 ただし、この記事の UI の説明とスクリーンショットは、Azure ポータルに適用されます。



コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。

## <a name="prerequisites"></a>前提条件
このデプロイで必要なものを次に示します。

* **IaaS 仮想マシン**: 移行する VM。 これらの VM を物理コンピューターとして扱って移行します。

## <a name="deployment-steps"></a>デプロイメントの手順
このセクションでは、新しい Azure ポータルでのデプロイの手順について説明します。

1. [コンテナーを作成します](site-recovery-azure-to-azure.md#create-a-recovery-services-vault)。
2. 移行する VM の[レプリケーションを有効にして](site-recovery-azure-to-azure.md)、ソースとして Azure を選択します。
  >[!NOTE]
  >
  > 現在、管理対象ディスクを使用する Azure VM のネイティブ レプリケーションはサポートされていません。 管理対象ディスクを使用して VM を移行するには、[こちらのドキュメント](site-recovery-vmware-to-azure.md)の「物理から Azure」オプションを使用できます。
3. [フェールオーバーを実行します](site-recovery-failover.md)。 初期レプリケーションが完了したら、Azure リージョン間でフェールオーバーを実行できます。 必要に応じて、復旧計画を作成し、フェールオーバーを実行して、複数の仮想マシンをリージョン間で移行できます。 [こちら](site-recovery-create-recovery-plans.md) をご覧ください。

## <a name="next-steps"></a>次のステップ
その他のレプリケーション シナリオの詳細については、 [Azure Site Recovery の概要](site-recovery-overview.md)
