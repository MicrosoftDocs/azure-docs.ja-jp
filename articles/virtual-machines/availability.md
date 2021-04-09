---
title: Azure Virtual Machines の可用性オプション
description: Azure で仮想マシンを実行するための可用性オプションについて説明します
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 03/08/2021
ms.reviewer: cynthn
ms.openlocfilehash: 1ea87d40430dbf3edabd557b80ab1456b49f4605
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102507876"
---
# <a name="availability-options-for-azure-virtual-machines"></a>Azure Virtual Machines の可用性オプション
この記事では、Azure 仮想マシン (VM) の可用性オプションの概要を説明します。

## <a name="availability-zones"></a>可用性ゾーン

[可用性ゾーン](../availability-zones/az-overview.md?context=/azure/virtual-machines/context/context)は、VM 上のアプリケーションとデータの可用性を維持するための制御レベルを拡張します。 可用性ゾーンとは、Azure リージョン内の物理的に独立したゾーンのことです。 サポートされている Azure リージョンごとに 3 つの可用性ゾーンがあります。 

可用性ゾーンはそれぞれ異なる電源、ネットワーク、および冷却装置を持ちます。 複数のゾーンにレプリケートされた VM を使用するソリューションを設計することで、1 つのデータセンターで障害が発生してもアプリケーションとデータを保護することができます。 1 つのゾーンが侵害された場合、レプリケートされたアプリとデータが別のゾーンですぐに利用可能になります。 

## <a name="availability-sets"></a>可用性セット
[可用性セット](availability-set-overview.md)は VM の論理グループで、これによって Azure は、冗長性と可用性を提供するためにアプリケーションが構築された方法を理解することができます。 高可用性アプリケーションを提供し、[99.95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) に適合するために、1 つの可用性セット内に 2 つ以上の VM を作成することをお勧めします。 可用性セット自体にはコストはかかりません。料金は、作成した各 VM インスタンスに対してのみ発生します。


## <a name="virtual-machines-scale-sets"></a>Virtual Machines スケール セット 

[Azure Virtual Machine スケール セット](../virtual-machine-scale-sets/overview.md?context=/azure/virtual-machines/context/context)では、負荷分散が行われる VM のグループを作成して管理することができます。 需要または定義されたスケジュールに応じて、VM インスタンスの数を自動的に増減させることができます。 スケール セットは、アプリケーションの高可用性を実現します。また、多数の VM の一元的な管理、構成、更新を可能にします。 高可用性アプリケーションを提供し、[99.95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) に適合するために、1 つのスケール セット内に 2 つ以上の VM を作成することをお勧めします。 スケール セット自体にはコストはかかりません。料金は、作成した各 VM インスタンスに対してのみ発生します。

スケール セット内の仮想マシンは、1 つの可用性ゾーン (リージョン) にデプロイすることもできます。 可用性ゾーンのデプロイ オプションは、[.オーケストレーション モード](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md?context=/azure/virtual-machines/context/context)によって異なる場合があります。

## <a name="load-balancer"></a>Load Balancer
[Azure Load Balancer](../load-balancer/load-balancer-overview.md) と可用性ゾーンまたは可用性セットを結合することで、アプリケーションの回復性を最大化できます。 Azure Load Balanceは、複数の仮想マシンにトラフィックを振り分けます。 当社の標準層の仮想マシンには Azure Load Balanceが含まれています。 すべての仮想マシン層に Azure Load Balancer が含まれているわけではありません。 仮想マシンの負荷分散の詳細については、[Linux](linux/tutorial-load-balancer.md) または [Windows](windows/tutorial-load-balancer.md) の **仮想マシンの負荷分散** に関する記事をご覧ください。


## <a name="azure-storage-redundancy"></a>Azure Storage の冗長性
Azure Storage では、計画されたイベントや計画外のイベント (一時的なハードウェア障害、ネットワークの停止または停電、大規模な自然災害など) から保護するため、常にデータの複数のコピーが格納されます。 冗長性を確保することで、障害が発生した場合でも、ストレージ アカウントが可用性と耐久性に関する目標を確実に達成できます。

ご自身のシナリオに最適な冗長性オプションを決定する際には、低コストと高可用性のトレードオフを検討してください。 どの冗長性オプションを選択するかの判断に役立つ要因は次のとおりです。
- プライマリ リージョンでのデータのレプリケート方法
- 地域災害から保護するため、プライマリ リージョンから地理的に離れている 2 番目のリージョンにデータをレプリケートするかどうか
- プライマリ リージョンが何らかの理由で使用できなくなった場合に、アプリケーションがセカンダリ リージョンのレプリケートされたデータへの読み取りアクセスを必要とするかどうか

詳細については、「[Azure Storage の冗長性](../storage/common/storage-redundancy.md)」を参照してください。

## <a name="azure-site-recovery"></a>Azure Site Recovery
組織は、計画済みまたは計画外の停止が発生した場合のために、お使いのデータの安全性を確保し、お使いのアプリやワークロードをオンラインにし、ビジネス継続性とディザスター リカバリー (BCDR) の戦略を導入する必要があります。

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) により、機能停止中でもビジネス アプリとワークロードの実行状態を維持することで、ビジネス継続性を確保できます。 Site Recovery は、物理マシンと仮想マシン (VM) で実行中のワークロードを、プライマリ サイトからセカンダリ ロケーションにレプリケートします。 プライマリ サイトで障害が発生した場合は、セカンダリ ロケーションにフェールオーバーし、そこからアプリにアクセスします。 プライマリの場所が再度実行中になったら、そこにフェールバックできます。

Site Recovery で対応できるレプリケーションは次のとおりです。
- Azure リージョン間で Azure VM をレプリケートする。
- オンプレミス VM、Azure Stack VM、物理サーバー。

## <a name="next-steps"></a>次のステップ
- [可用性ゾーンに仮想マシンを作成する](/linux/create-cli-availability-zone.md)
- [可用性セットに仮想マシンを作成する](/linux/tutorial-availability.md)
- [仮想マシン スケール セットを作成する](../virtual-machine-scale-sets/quick-create-portal.md)