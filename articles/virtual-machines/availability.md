---
title: 可用性のオプション
description: Azure で仮想マシンを実行するための可用性機能について説明します
author: cynthn
ms.author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: 3826019a19aa744cb03737ed7f0c985f8063a1aa
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815608"
---
# <a name="availability-options-for-virtual-machines-in-azure"></a>Azure の仮想マシンの可用性オプション

この記事では、Azure 仮想マシン (VM) の可用性機能の概要について説明します。

## <a name="high-availability"></a>高可用性

ワークロードは、通常は複数の仮想マシンに分散されて、高いスループットとパフォーマンスを獲得し、更新やその他のイベントによって VM が影響を受ける場合に冗長性を確保します。 

高可用性を実現するために Azure が提供するオプションはいくつかあります。 まず、基本的な構成要素について説明します。 

### <a name="availability-zones"></a>可用性ゾーン

[可用性ゾーン](../availability-zones/az-overview.md)は、VM 上のアプリケーションとデータの可用性を維持するための制御レベルを拡張します。 可用性ゾーンとは、Azure リージョン内の物理的に独立したゾーンのことです。 サポートされている Azure リージョンごとに 3 つの可用性ゾーンがあります。 

可用性ゾーンはそれぞれ異なる電源、ネットワーク、および冷却装置を持ちます。 複数のゾーンにレプリケートされた VM を使用するソリューションを構築することで、1 つのデータセンターで障害が発生してもアプリケーションとデータを保護することができます。 1 つのゾーンが侵害された場合、レプリケートされたアプリとデータが別のゾーンですぐに利用可能になります。 

![可用性ゾーン](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

可用性ゾーンに [Windows](./windows/create-powershell-availability-zone.md) または [Linux](./linux/create-cli-availability-zone.md) の VM をデプロイする方法の詳細をご覧ください。


### <a name="fault-domains"></a>障害ドメイン

障害ドメインは、オンプレミスのデータ センター内のラックのように、共通の電源やネットワーク スイッチを共有する基盤となるハードウェアの論理グループです。 

### <a name="update-domains"></a>更新ドメイン

更新ドメインは、メンテナンスや再起動が同時に行われる可能性のある、基盤となるハードウェアの論理グループです。 

これにより、Azure プラットフォームに定期メンテナンスを実施している間もアプリケーションのインスタンスが常に 1 つは稼働している状態を確保することができます。 メンテナンス中は更新ドメインの再起動が順番に処理されない場合がありますが、一度に再起動される更新ドメインは 1 つのみです。


## <a name="virtual-machines-scale-sets"></a>Virtual Machines スケール セット 

Azure Virtual Machine Scale Sets では、負荷分散が行われる VM のグループを作成して管理することができます。 需要または定義されたスケジュールに応じて、VM インスタンスの数を自動的に増減させることができます。 スケール セットは、アプリケーションの高可用性を実現します。また、多数の VM の一元的な管理、構成、更新を可能にします。 高可用性アプリケーションを提供し、[99.95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) に適合するために、1 つのスケール セット内に 2 つ以上の VM を作成することをお勧めします。 スケール セット自体にはコストはかかりません。料金は、作成した各 VM インスタンスに対してのみ発生します。 1 つの VM が [Azure Premium SSD](./disks-types.md#premium-ssd) を使用している場合、Azure SLA は計画外メンテナンス イベントに適用されます。 スケール セット内の仮想マシンは、複数の更新ドメインと障害ドメインにデプロイして、データセンターの障害や、計画済みまたは計画外のメンテナンス イベントによる障害に対する可用性と復元性を最大にすることができます。 スケール セット内の仮想マシンは、1 つの可用性ゾーン (リージョン) にデプロイすることもできます。 可用性ゾーンのデプロイ オプションは、オーケストレーション モードによって異なる場合があります。

**障害ドメインと更新ドメイン**

仮想マシン スケール セットを使用すると、障害ドメインと更新ドメインを調整することで、高可用性向けの設計が簡単になります。 スケール セットの障害ドメインの数を定義するだけで済みます。 スケール セットで使用できる障害ドメインの数は、リージョンによって異なる場合があります。 [Azure での仮想マシンの可用性の管理](./windows/manage-availability.md)に関する記事を参照してください。


## <a name="availability-sets"></a>可用性セット
可用性セットはデータセンター内の VM の論理グループで、これによって Azure は、冗長性と可用性を提供するためにアプリケーションが構築された方法を理解することができます。 高可用性アプリケーションを提供し、[99.95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) に適合するために、1 つの可用性セット内に 2 つ以上の VM を作成することをお勧めします。 可用性セット自体にはコストはかかりません。料金は、作成した各 VM インスタンスに対してのみ発生します。 1 つの VM が [Azure Premium SSD](./disks-types.md#premium-ssd) を使用している場合、Azure SLA は計画外メンテナンス イベントに適用されます。

可用性セットでは、VM はこれらの障害ドメイン間で自動的に分散されます。 これにより、物理ハードウェアの障害、ネットワークの停止、または停電が発生した場合の影響を抑えることが可能になります。

[Azure Managed Disks](./faq-for-disks.md) を使用している VM の場合、VM は管理対象の可用性セットを使用している場合にマネージド ディスクの障害ドメインに合わせて配置されます。 この配置により、VM に接続されたすべてのマネージド ディスクは必ず同じマネージド ディスクの障害ドメイン内にあります。 

管理対象の可用性セットには、マネージド ディスクを持つ VM だけを作成できます。 マネージド ディスクの障害ドメインの数はリージョンによって異なり、マネージド ディスクの障害ドメインはリージョンあたり 2 つまたは 3 つになります。 [Linux VM](./linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) または [Windows VM](./windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) のこれらのマネージド ディスク障害ドメインの詳細を参照してください。

![可用性セットの管理](./media/virtual-machines-common-manage-availability/md-fd-updated.png)


また、可用性セット内の VM も、更新ドメイン間で自動的に分散されます。 

![可用性セット](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="next-steps"></a>次のステップ
可用性と冗長性の機能を使って、Azure 環境を構築できるようになりました。 ベスト プラクティスに関する情報については、[Azure の可用性のベスト プラクティス](/azure/architecture/checklist/resiliency-per-service)に関するページをご覧ください。