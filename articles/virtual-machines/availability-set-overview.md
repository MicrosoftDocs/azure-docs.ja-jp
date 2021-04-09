---
title: 可用性セットの概要
description: Azure の可用性セットについて
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: c0bbc218723ea75064d9eb6f9c5f8f8c0d18a10b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102510409"
---
# <a name="availability-sets-overview"></a>可用性セットの概要

この記事では、Azure 仮想マシン (VM) の可用性機能の概要について説明します。

## <a name="what-is-an-availability-set"></a>可用性セットとは 

可用性セットは VM の論理グループで、これによって Azure は、冗長性と可用性を提供するためにアプリケーションが構築された方法を理解することができます。 高可用性アプリケーションを提供し、[99.95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) に適合するために、1 つの可用性セット内に 2 つ以上の VM を作成することをお勧めします。 可用性セット自体にはコストはかかりません。料金は、作成した各 VM インスタンスに対してのみ発生します。

## <a name="how-do-availability-sets-work"></a>可用性セットのしくみ
基盤となる Azure プラットフォームにより、可用性セット内の各仮想マシンに **更新ドメイン** と **障害ドメイン** が割り当てられます。 所定の可用性セットに対して、同時に再起動される仮想マシンのグループと物理ハードウェアを示す、ユーザーが構成できない 5 つの更新ドメインが既定で割り当てられます (その後、最大 20 の更新ドメインを提供できるように Resource Manager のデプロイを増やすことができます)。 1 つの可用性セット内に 5 つ以上の仮想マシンが構成されているとき、6 つ目の仮想マシンは 1 つ目の仮想マシンと同じ更新ドメイン内に配置され、7 つ目は 2 つ目の仮想マシンと同じ更新ドメイン内に配置されるという方法で処理されます。 計画的メンテナンス中は、更新ドメインの再起動が順番に処理されない場合がありますが、一度に再起動される更新ドメインは 1 つのみです。 再起動された更新ドメインには、別の更新ドメインでメンテナンスが開始されるまでに、復旧するための時間として 30 分が与えられます。

障害ドメインは電源とネットワーク スイッチを共有する仮想マシンのグループを定義します。 既定では、可用性セット内に構成された仮想マシンは、Resource Manager のデプロイ用に最大 3 つの障害ドメインに分けられます。 仮想マシンを可用性セットに配置しても、アプリケーションがオペレーティング システムやアプリケーションの障害から保護されるわけではありませんが、潜在的な物理ハードウェア障害、ネットワーク障害、または電力の中断の影響を低下させることができます。

![可用性セット](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)


[Azure Managed Disks](./faq-for-disks.md) を使用している VM の場合、VM は管理対象の可用性セットを使用している場合にマネージド ディスクの障害ドメインに合わせて配置されます。 この配置により、VM に接続されたすべてのマネージド ディスクは必ず同じマネージド ディスクの障害ドメイン内にあります。 

管理対象の可用性セットには、マネージド ディスクを持つ VM だけを作成できます。 マネージド ディスクの障害ドメインの数はリージョンによって異なり、マネージド ディスクの障害ドメインはリージョンあたり 2 つまたは 3 つになります。 

![可用性セットの管理](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

## <a name="next-steps"></a>次のステップ
可用性と冗長性の機能を使って、Azure 環境を構築できるようになりました。 ベスト プラクティスに関する情報については、[Azure の可用性のベスト プラクティス](/azure/architecture/checklist/resiliency-per-service)に関するページをご覧ください。

