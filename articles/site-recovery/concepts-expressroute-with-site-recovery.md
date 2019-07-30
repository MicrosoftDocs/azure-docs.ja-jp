---
title: Azure ExpressRoute と Azure Site Recovery を使ったディザスター リカバリーと移行について | Microsoft Docs
description: Azure ExpressRoute と Azure Site Recovery サービスを使ってディザスター リカバリーと移行を行う方法を説明します。
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 6/27/2019
ms.author: mayg
ms.openlocfilehash: 35fa26112a6026ab05bd59b38621de7ee802c715
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491904"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Azure ExpressRoute と Azure Site Recovery

Microsoft Azure ExpressRoute を利用すれば、接続プロバイダーが提供するプライベート接続で、オンプレミスのネットワークを Microsoft Cloud に拡張できます。 ExpressRoute では、Microsoft Azure、Office 365、Dynamics 365 などの Microsoft クラウド サービスへの接続を確立できます。

この記事では、ディザスター リカバリーと移行のために、どのように Azure ExpressRoute と Azure Site Recovery を使用できるかを説明します。

## <a name="expressroute-circuits"></a>ExpressRoute 回線

ExpressRoute 回線は、接続プロバイダー経由のオンプレミス インフラストラクチャと Microsoft クラウド サービス間の論理接続を表します。 複数の ExpressRoute 回線を注文することができます。 回線をそれぞれ同じリージョンや異なるリージョンに配置したり、異なる接続プロバイダーを経由して社内に接続したりすることができます。 ExpressRoute 回線の詳細については、[ここ](../expressroute/expressroute-circuit-peerings.md)をご覧ください。

ExpressRoute 回線には、複数のルーティング ドメインが関連付けられています。 ExpressRoute のルーティング ドメインの詳細と比較については、[ここ](../expressroute/expressroute-circuit-peerings.md#peeringcompare)をご覧ください。

## <a name="on-premises-to-azure-replication-with-expressroute"></a>ExpressRoute を使用したオンプレミスから Azure へのレプリケーション

Azure Site Recovery により、オンプレミスの [HYPER-V 仮想マシン](hyper-v-azure-architecture.md)、[VMware 仮想マシン](vmware-azure-architecture.md)、および[物理サーバー](physical-azure-architecture.md)のディザスター リカバリーと Azure への移行が可能になります。 オンプレミスから Azure へのすべてのシナリオで、レプリケーション データは Azure Storage アカウントに送信され、格納されます。 レプリケーション中に仮想マシンの料金は発生しません。 Azure へのフェールオーバーを実行すると、Site Recovery は Azure IaaS 仮想マシンを自動的に作成します。

Site Recovery は、パブリック エンドポイント経由で、ターゲット Azure リージョンの Azure ストレージ アカウントまたはレプリカ マネージド ディスクにデータをレプリケートします。 Site Recovery のレプリケーション トラフィックに ExpressRoute を使用するために、[Microsoft ピアリング](../expressroute/expressroute-circuit-peerings.md#microsoftpeering)または既存の[パブリック ピアリング](../expressroute/expressroute-circuit-peerings.md#publicpeering) (新規作成では非推奨) を利用できます。 Microsoft ピアリングは、レプリケーション用に推奨されるルーティング ドメインです。 プライベート ピアリングを介したレプリケーションはサポートされていません。

構成サーバーの[ネットワークの要件](vmware-azure-configuration-server-requirements.md#network-requirements)も確実に満たされるようにします。 Site Recovery レプリケーションを調整する場合、構成サーバーが特定の URL に接続する必要があります。 この接続には ExpressRoute は使用できません。 

オンプレミスでプロキシを使用しているときに、レプリケーションのトラフィックで ExpressRoute を使用したい場合は、構成サーバーとプロセス サーバーにプロキシ バイパスの一覧を構成する必要があります。 次の手順に従ってください。

- システム ユーザーのコンテンツにアクセスするための PsExec ツールを[こちら](https://aka.ms/PsExec)からダウンロードします。
- psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe" をコマンド ラインで実行して、インターネット エクスプローラーでシステム ユーザーのコンテンツを開きます。
- IE にプロキシ設定を追加する
- バイパスの一覧に、Azure ストレージの URL (*.blob.core.windows.net) を追加します。

これにより、レプリケーション トラフィックのみが ExpressRoute を通り、一方で通信はプロキシを通過できます。

仮想マシンまたはサーバーが Azure 仮想ネットワークにフェールオーバーした後は、[プライベート ピアリング](../expressroute/expressroute-circuit-peerings.md#privatepeering)を使ってアクセスできます。 

組み合わせたシナリオは次の図のように表されます。![ExpressRoute を使用してオンプレミスから Azure へ](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>ExpressRoute を使用した Azure から Azure へのレプリケーション

Azure Site Recovery によって、[Azure 仮想マシン](azure-to-azure-architecture.md)のディザスター リカバリーが可能になります。 Azure 仮想マシンが [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md) を使用するかどうかに応じて、レプリケーション データは、Azure Storage アカウントまたはターゲット Azure リージョンのレプリカ マネージド ディスクに送信されます。 レプリケーションのエンドポイントはパブリックですが、Azure VM のレプリケーション トラフィックは、ソース仮想ネットワークがどの Azure リージョンに存在するかに関わらず、既定ではインターネットを経由しません。 0\.0.0.0/0 アドレス プレフィックスの Azure の既定のシステム ルートを [カスタム ルート](../virtual-network/virtual-networks-udr-overview.md#custom-routes)でオーバーライドし、VM トラフィックをオンプレミス ネットワーク仮想アプライアンス (NVA) に転送することもできますが、この構成は Site Recovery レプリケーションにはお勧めしません。 カスタム ルートを使用している場合、レプリケーション トラフィックが Azure 境界から外に出ないように、"ストレージ" 用の仮想ネットワーク内に[仮想ネットワーク サービス エンドポイントを作成する](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage)ことをお勧めします。

Azure VM のディザスター リカバリーの場合、既定では、レプリケーションのために ExpressRoute は必要ありません。 仮想マシンが Azure リージョンにフェールオーバーした後は、[プライベート ピアリング](../expressroute/expressroute-circuit-peerings.md#privatepeering)を使ってアクセスできます。

既に ExpressRoute を使用してオンプレミスのデータ センターからソース リージョンの Azure VM に接続している場合は、フェールオーバーのターゲット リージョンで ExpressRoute 接続を再確立することを計画できます。 同じ ExpressRoute 回線を使用して新しい仮想ネットワーク接続経由でターゲット リージョンに接続することも、ディザスター リカバリー用の別個の ExpressRoute 回線と接続を利用することもできます。 考えられるさまざまなシナリオの説明については、[ここ](azure-vm-disaster-recovery-with-expressroute.md#fail-over-azure-vms-when-using-expressroute)をご覧ください。

[ここ](../site-recovery/azure-to-azure-support-matrix.md#region-support)で説明しているように、同じ地理クラスター内で、Azure 仮想マシンを任意の Azure リージョンにレプリケートできます。 選択したターゲット Azure リージョンがソースと同じ地理的リージョン内にない場合は、ExpressRoute Premium を有効にすることが必要な場合があります。 詳しくは、「[ExpressRoute の場所](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region)」と「[ExpressRoute の価格](https://azure.microsoft.com/pricing/details/expressroute/)」をご覧ください。

## <a name="next-steps"></a>次の手順
- [ExpressRoute 回線](../expressroute/expressroute-circuit-peerings.md)について詳しく学習する。
- [ExpressRoute ルーティング ドメイン](../expressroute/expressroute-circuit-peerings.md#peeringcompare)について詳しく学習する。
- [ExpressRoute の場所](../expressroute/expressroute-locations.md)について詳しく学習する。
- [ExpressRoute を使用した Azure 仮想マシン](azure-vm-disaster-recovery-with-expressroute.md)のディザスター リカバリーについて詳しく学習する。
