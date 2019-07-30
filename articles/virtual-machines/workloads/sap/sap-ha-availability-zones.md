---
title: Azure Availability Zones での SAP ワークロードの構成 | Microsoft Docs
description: Azure Availability Zones を使用した SAP NetWeaver のための高可用性のアーキテクチャとシナリオ
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ead1dfdce4bf3a803eee46a536dc7062626640d9
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234237"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Azure Availability Zones での SAP ワークロードの構成
[Azure Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview) は、Azure で提供されている高可用性機能の 1 つです。 Availability Zones により、Azure での SAP ワークロードの全体的な可用性が向上します。 この機能は、既に一部の[Azure リージョン](https://azure.microsoft.com/global-infrastructure/regions/)で利用可能になっています。 今後、さらに多くのリージョンで利用できるようになります。

SAP の高可用性の基本的なアーキテクチャを次の図に示します。

![標準の高可用性構成](./media/sap-ha-availability-zones/standard-ha-config.png)

SAP のアプリケーション レイヤーは、1 つの Azure [可用性セット](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)にデプロイされます。 SAP セントラル サービスの高可用性を確保するために、別々の可用性セットに 2 つの VM をデプロイできます。 インフラストラクチャやソフトウェアの問題に備える自動フェールオーバーの高可用性フレームワークとして、Windows Server フェールオーバー クラスタリングまたは Pacemaker (Linux) を使用します。 このようなデプロイの詳細については、以下を参照してください。

- [クラスター共有ディスクを使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [ファイル共有を使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [SUSE Linux Enterprise Server for SAP Applications 上の Azure VM での SAP NetWeaver の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Red Hat Enterprise Linux での SAP NetWeaver のための Azure Virtual Machines 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

同様のアーキテクチャが、SAP NetWeaver、S/4HANA、または Hybris システムの DBMS レイヤーにも適用されます。 インフラストラクチャーまたはソフトウェアの障害から保護するために、フェールオーバー クラスター ソリューションを使用して、アクティブ/パッシブ モードで DBMS レイヤーをデプロイします。 このフェールオーバー クラスター ソリューションは、DBMS 固有のフェールオーバー フレームワーク、Windows Server フェールオーバー クラスタリング、または Pacemaker の場合があります。

Azure Availability Zones を使用して同じアーキテクチャをデプロイするには、前述のアーキテクチャに対していくつかの変更を行う必要があります。 この記事では、それらの変更について説明します。

## <a name="considerations-for-deploying-across-availability-zones"></a>Availability Zones 間でのデプロイに関する考慮事項


Availability Zones を使用する場合、次の点に考慮してください。

- 1 つの Azure リージョン内の異なる Availability Zones 間の距離に関する保証はありません。
- Availability Zones は、理想的な DR ソリューションではありません。 自然災害により、電力設備に対する大きな損害を含め、世界のリージョンで広範囲にわたる損害が発生する場合があります。 異なるゾーン間の距離が、適切な DR ソリューションを構成するのに十分な長さではない場合があります。
- Availability Zones 間のネットワーク待ち時間は、すべての Azure リージョンで同じではありません。 あるゾーンからアクティブ DBMS VM へのネットワーク待ち時間が許容可能なものであるため、SAP アプリケーション レイヤーをさまざまなゾーンにわたってデプロイして実行できる場合があります。 しかし、一部の Azure リージョンでは、異なるゾーンにデプロイされたアクティブ DBMS VM と SAP アプリケーション インスタンスとの間の待ち時間が、SAP ビジネス プロセスにとって許容できないものである場合があります。 そのような場合は、アプリケーションのアクティブ/アクティブ アーキテクチャまたはアクティブ/パッシブ アーキテクチャ (ゾーンをまたがるネットワーク待ち時間が長すぎる場合) で、デプロイ アーキテクチャを異なるものにする必要があります。
- Availability Zones を使用する場所を決定する際はゾーン間のネットワークの待ち時間に基づいて決定してください。 ネットワーク待ち時間は、次の 2 つの点で重要な役割を果たします。
    - 同期レプリケーションが必要な 2 つの DBMS インスタンス間の待ち時間。 ネットワークの待ち時間が長いほど、ワークロードのスケーラビリティに影響する可能性が高くなります。
    - アクティブな DBMS インスタンスを含むゾーンで SAP ダイアログ インスタンスを実行している VM と、別のゾーンの同様の VM との間のネットワーク待ち時間の違い。 この違いが大きいほど、DBMS を含むゾーン、または別のゾーンのどちらで実行しているかに応じて、ビジネス プロセスとバッチ ジョブの実行時間への影響も大きくなります。

Azure VM を複数の Availability Zones にデプロイして、同じ Azure リージョン内でフェールオーバー ソリューションを確立する場合、いくつかの制限が適用されます。

- Azure Availability Zones にデプロイするときは [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) を使用する必要があります。 
- 物理ゾーンに対するゾーン列挙のマッピングは、Azure サブスクリプションごとに固定されています。 さまざまなサブスクリプションを使用して SAP システムをデプロイする場合は、サブスクリプションごとに最適なゾーンを定義する必要があります。
- [Azure 近接通信配置グループ](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)を使用しない場合、1 つの Azure Availability Zone 内に Azure 可用性セットをデプロイすることはできません。 SAP DBMS レイヤーとセントラル サービスを複数のゾーンにわたってデプロイすると同時に、可用性セットを使用して SAP アプリケーション レイヤーをデプロイし、さらに VM の近接通信も実現する方法については、「[SAP アプリケーションで最適なネットワーク待ち時間を実現する Azure 近接通信配置グループ](sap-proximity-placement-scenarios.md)」の記事で説明しています。 Azure 近接通信配置グループを利用していない場合は、仮想マシンのデプロイ フレームワークとして、いずれかを選択する必要があります。
- [Azure Basic Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) を使用して Windows Server フェールオーバー クラスタリングまたは Linux Pacemaker に基づくフェールオーバー クラスター ソリューションを作成することはできません。 代わりに、[Azure Standard Load Balancer SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) を使用する必要があります。



## <a name="the-ideal-availability-zones-combination"></a>Availability Zones の最適な組み合わせ
Availability Zones を使用する方法を決定する前に、次の事項を判断する必要があります。

- Azure リージョンの 3 つのゾーン間のネットワーク待ち時間。 これにより、ゾーンをまたいだネットワーク トラフィックでネットワーク待ち時間が最も短いゾーンを選択することができます。
- 選択した 1 つのゾーン内の VM 間の待ち時間と、選択した 2 つのゾーン間のネットワーク待ち時間の差。
- デプロイする必要がある VM の種類が、選択した 2 つのゾーンで使用可能かどうかの判断。 一部の VM、特に M シリーズの VM では、一部の SKU が 3 つのゾーンのうち 2 つのゾーンでしか使用できない状況が発生する場合があります。

## <a name="network-latency-between-and-within-zones"></a>ゾーン間とゾーン内のネットワーク待ち時間
異なるゾーン間の待ち時間を判断するには、以下のことを行う必要があります。

- DBMS インスタンスに使用する VM SKU を 3 つのゾーンすべてにデプロイします。 この測定を実行するときは、[Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) が有効になっていることを確認します。
- ネットワーク待ち時間が最も短い 2 つのゾーンがわかったら、アプリケーション レイヤー VM として使用する VM SKU の別の 3 つの VM を 3 つの Availability Zones にデプロイします。 選択した 2 つの DBMS ゾーン内の 2 つの DBMS VM に対して、ネットワーク待ち時間を測定します。 
- 測定ツールとして **niping** を使用します。 SAP 提供のこのツールの説明は、SAP サポート ノート [#500235](https://launchpad.support.sap.com/#/notes/500235) と [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) をご覧ください。 待ち時間測定用に記載されているコマンドに注目してください。 **ping** は Azure Accelerated Networking コード パスでは機能しないので、ping の使用は推奨されません。

測定値と Availability Zones での VM SKU の可用性に基づいて、いくつかの決定を行う必要があります。

- DBMS レイヤーの理想的なゾーンを定義する。
- ゾーン内とゾーンをまたいだ場合のネットワーク待ち時間の違いに基づいて、アクティブ SAP アプリケーション レイヤーを 1 つのゾーン、2 つのゾーン、または 3 つ全部のゾーンのいずれに分散するかを決定する。
- アプリケーションの観点から、アクティブ/パッシブまたはアクティブ/アクティブのどちらの構成をデプロイするかを決定する。 (これらの構成については、この記事で後述します)。

これらの決定を行うときは、SAP ノート [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) に記載されている SAP のネットワーク待ち時間に関する推奨事項も考慮してください。

> [!IMPORTANT]
> 行った測定と決定は、測定に使用した Azure サブスクリプションに対して有効です。 別の Azure サブスクリプションを使用する場合は、測定を繰り返す必要があります。 列挙されたゾーンのマッピングは、別の Azure サブスクリプションでは異なることがあります。


> [!IMPORTANT]
> 前述の測定は、[Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview) をサポートするすべての Azure リージョンで異なる結果になることが予想されます。 ネットワーク待ち時間の要件が同じでも、ゾーン間でネットワーク待ち時間が異なる可能性があるため、異なる Azure リージョンでは異なるデプロイ戦略の採用が必要になる場合があります。 一部の Azure リージョンでは、3 つの異なるゾーン間のネットワーク待ち時間が大きく異なる可能性があります。 他のリージョンでは、3 つの異なるゾーン間のネットワーク待ち時間がより均一になる可能性があります。 常に 1 ～ 2 ミリ秒のネットワーク待ち時間があるという主張は正しくありません。 Azure リージョン内の Availability Zones 間のネットワーク待ち時間を一般化することはできません。

## <a name="activeactive-deployment"></a>アクティブ/アクティブのデプロイ
このデプロイ アーキテクチャがアクティブ/アクティブと呼ばれるのは、2 つまたは 3 つのゾーンにアクティブな SAP アプリケーション サーバーをデプロイするためです。 エンキュー レプリケーションを使用する SAP セントラル サービス インスタンスは、2 つのゾーン間にデプロイされます。 DBMS レイヤーについても同じで、SAP セントラル サービスと同じゾーンにデプロイされます。

この構成を検討する際は、リージョン内で、ワークロードと同期 DBMS レプリケーションについて許容されるゾーン間のネットワーク待ち時間を提供する 2 つの Availability Zones を見つける必要があります。 また、選択したゾーン内のネットワーク待ち時間とゾーン間のネットワーク待ち時間の差が、大きすぎないようにする必要があります。 これは、ジョブが DBMS サーバーを含むゾーン内で実行されているか、ゾーンをまたいで実行されているかによって、ビジネス プロセスやバッチ ジョブの実行時間の変動率があまり大きくならないようにするためです。 ある程度の変動は許容されますが、相違の要因ではありません。

2 つのゾーン間のアクティブ/アクティブ デプロイの簡略化されたスキーマは次のようになります。

![アクティブ/アクティブ ゾーンのデプロイ](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

この構成には、次の考慮事項が適用されます。

- [Azure 近接通信配置グループ](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)を使用しない場合、可用性セットは Azure Availability Zones にデプロイできないため、Azure Availability Zones をすべての VM に対する障害ドメインおよび更新ドメインとして扱います。
- DBMS レイヤーとセントラル サービス用にゾーン デプロイを組み合わせたい一方で、アプリケーション レイヤーには Azure 可用性セットを使用したい場合は、「[SAP アプリケーションで最適なネットワーク待ち時間を実現する Azure 近接通信配置グループ](sap-proximity-placement-scenarios.md)」の記事で説明しているように、Azure 近接通信グループを使用する必要があります。
- SAP セントラル サービスおよび DBMS レイヤーのフェールオーバー クラスターのロード バランサーには、[Standard SKU Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) を使用する必要があります。 Basic Load Balancer は、ゾーンをまたいでは機能しません。
- SAP システムをホストするためにデプロイした Azure 仮想ネットワークは、そのサブネットと共にゾーンをまたいで拡大されます。 ゾーンごとに仮想ネットワークを分ける必要はありません。
- デプロイするすべての仮想マシンで、[Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) を使用する必要があります。 アンマネージド ディスクは、ゾーン ベースのデプロイにはサポートされていません。
- Azure Premium Storage と [Ultra SSD ストレージ](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd)では、どのような種類のゾーン間ストレージ レプリケーションもサポートされていません。 重要なデータのレプリケートは、アプリケーション (DBMS または SAP セントラル サービス) で行う必要があります。
- 共有ディスク (Windows)、CIFS 共有 (Windows)、または NFS 共有 (Linux)である共有 sapmnt ディレクトリについても同じです。 ゾーン間でこれらの共有ディスクまたは共有をレプリケートするテクノロジを使用する必要があります。 次のテクノロジがサポートされています。
  - Windows では、「[Azure のクラスター共有ディスクを使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)」に記載されている、SIOS Datakeeper を使用するクラスター ソリューション。
  - SUSE linux では、「[SUSE Linux Enterprise Server 上の Azure VM での NFS の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)」に記載されているように構築された NFS 共有。
    
    現時点では、「[Windows フェールオーバー クラスターと SAP ASCS/SCS インスタンスのファイル共有を使用して SAP の高可用性向けの Azure インフラストラクチャを準備します](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)」に記載されているように、Microsoft スケールアウト ファイル サーバーを使用するソリューションは、ゾーンをまたいでサポートされません。
- 3 番目のゾーンは、[SUSE Linux Pacemaker クラスター](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device)または追加のアプリケーション インスタンスをビルドする場合に、SBD デバイスをホストするために使用されます。
- 重要なビジネス プロセスの実行時間の整合性を達成するには、SAP バッチ サーバー グループ、SAP ログオン グループ、または RFC グループを使用して、アクティブな DBMS インスタンスを含むゾーン内のアプリケーション インスタンスに、特定のバッチ ジョブとユーザーを直接送ることができます。 ただし、ゾーン ベースのフェールオーバーの場合は、アクティブ DB VM を含むゾーン内の VM で実行されているインスタンスにこれらのグループを手動で移動する必要があります。  
- 各ゾーンに休止状態のダイアログ インスタンスをデプロイすることをお勧めします。 これは、アプリケーション インスタンスの一部で使用されているゾーンが稼働していない場合に、以前のリソース容量にすぐに回復できるようにするためです。


## <a name="activepassive-deployment"></a>アクティブ/パッシブ デプロイ
1 つのゾーン内のネットワーク待ち時間とゾーンをまたいだネットワーク トラフィックの待ち時間の間に許容できる差が見つからない場合、SAP アプリケーション レイヤーの観点から、アクティブ/パッシブ特性を持つアーキテクチャをデプロイできます。 *アクティブな*ゾーンを定義します。これは、完全なアプリケーション レイヤーをデプロイし、アクティブな DBMS と SAP セントラル サービス インスタンスの両方を実行するゾーンです。 このような構成では、ジョブがアクティブな DBMS インスタンスを含むゾーンで実行されるかどうかによって、ビジネス トランザクションとバッチ ジョブで実行時間に極端な違いが出ないようにする必要があります。

このアーキテクチャの基本的なレイアウトを次に示します。

![アクティブ/パッシブ ゾーンのデプロイ](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

この構成には、次の考慮事項が適用されます。

- 可用性セットを Azure Availability Zones にデプロイすることはできません。 それを補うために、「[SAP アプリケーションで最適なネットワーク待ち時間を実現する Azure 近接通信配置グループ](sap-proximity-placement-scenarios.md)」の記事で説明されているように Azure 近接通信配置グループを使用できます。
- このアーキテクチャを使用する場合は、状態を注意深く監視し、デプロイしたアプリケーション レイヤーと同じゾーン内にアクティブな DBMS と SAP セントラル サービスのインスタンスを維持する必要があります。 SAP セントラル サービスまたは DBMS インスタンスのフェールオーバーの場合は、できるだけ早く SAP アプリケーション レイヤーがデプロイされたゾーンに手動でフェールバックできるようにする必要があります。
- SAP セントラル サービスおよび DBMS レイヤーのフェールオーバー クラスターのロード バランサーには、[Standard SKU Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) を使用する必要があります。 Basic Load Balancer は、ゾーンをまたいでは機能しません。
- SAP システムをホストするためにデプロイした Azure 仮想ネットワークは、そのサブネットと共にゾーンをまたいで拡大されます。 ゾーンごとに仮想ネットワークを分ける必要はありません。
- デプロイするすべての仮想マシンで、[Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) を使用する必要があります。 アンマネージド ディスクは、ゾーン ベースのデプロイにはサポートされていません。
- Azure Premium Storage と [Ultra SSD ストレージ](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd)では、どのような種類のゾーン間ストレージ レプリケーションもサポートされていません。 重要なデータのレプリケートは、アプリケーション (DBMS または SAP セントラル サービス) で行う必要があります。
- 共有ディスク (Windows)、CIFS 共有 (Windows)、または NFS 共有 (Linux)である共有 sapmnt ディレクトリについても同じです。 ゾーン間でこれらの共有ディスクまたは共有をレプリケートするテクノロジを使用する必要があります。 次のテクノロジがサポートされています。
    - Windows では、「[Azure のクラスター共有ディスクを使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)」に記載されている、SIOS Datakeeper を使用するクラスター ソリューション。
    - SUSE linux では、「[SUSE Linux Enterprise Server 上の Azure VM での NFS の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)」に記載されているように構築された NFS 共有。
    
  現時点では、「[Windows フェールオーバー クラスターと SAP ASCS/SCS インスタンスのファイル共有を使用して SAP の高可用性向けの Azure インフラストラクチャを準備します](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)」に記載されているように、Microsoft スケールアウト ファイル サーバーを使用するソリューションは、ゾーンをまたいでサポートされません。
- 3 番目のゾーンは、[SUSE Linux Pacemaker クラスター](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device)または追加のアプリケーション インスタンスをビルドする場合に、SBD デバイスをホストするために使用されます。
- ゾーンで障害が発生した場合にアプリケーション リソースを開始できるよう、(DBMS の観点から) パッシブ ゾーンに休止中の VM をデプロイする必要があります。
    - [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) は現在、ゾーン間で休止中の VM にアクティブな VM をレプリケートすることはできません。 
- ゾーン障害が発生した場合に 2 番目のゾーンで SAP アプリケーション レイヤーを自動的に開始できるオートメーションに投資する必要があります。

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>高可用性とディザスター リカバリーの構成の組み合わせ
Microsoft は、Azure リージョン内のさまざまな Azure Availability Zones をホストする施設間の地理的距離に関する情報を共有していません。 それでも、一部のお客様は、ゾーンを使用して HA と DR の構成を組み合わせることで、回復ポイントの目標 (RPO) をゼロにすることを約束しています。 これは、ディザスター リカバリーの場合であっても、コミットされたデータベース トランザクションが失われてはならないことを意味します。 

> [!NOTE]
> このような構成は特定の状況でのみ使用することが推奨されます。 たとえば、セキュリティ上またはコンプライアンス上の理由でデータを Azure リージョンの外部に出せない場合に使用することができます。 

このような構成の 1 つの例を次に示します。

![ゾーン内の高可用性 DR の組み合わせ](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

この構成には、次の考慮事項が適用されます。

- Availability Zones をホストしている施設の間に大きな距離があるか、ユーザーが特定の Azure リージョン内にとどまることを余儀なくされているものと想定します。 可用性セットを Azure Availability Zones にデプロイすることはできません。 それを補うために、「[SAP アプリケーションで最適なネットワーク待ち時間を実現する Azure 近接通信配置グループ](sap-proximity-placement-scenarios.md)」の記事で説明されているように Azure 近接通信配置グループを使用できます。
- このアーキテクチャを使用する場合は、状態を注意深く監視し、デプロイしたアプリケーション レイヤーと同じゾーン内にアクティブな DBMS と SAP セントラル サービスのインスタンスを維持する必要があります。 SAP セントラル サービスまたは DBMS インスタンスのフェールオーバーの場合は、できるだけ早く SAP アプリケーション レイヤーがデプロイされたゾーンに手動でフェールバックできるようにする必要があります。
- アクティブな QA アプリケーション インスタンスを実行する VM に、運用アプリケーションのインスタンスがプレインストールされている必要があります。
- ゾーンの障害が発生した場合は、QA アプリケーションのインスタンスをシャットダウンし、代わりに運用インスタンスを開始します。 これを行うには、アプリケーション インスタンスの仮想名を使用する必要があることに注意してください。
- SAP セントラル サービスおよび DBMS レイヤーのフェールオーバー クラスターのロード バランサーには、[Standard SKU Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) を使用する必要があります。 Basic Load Balancer は、ゾーンをまたいでは機能しません。
- SAP システムをホストするためにデプロイした Azure 仮想ネットワークは、そのサブネットと共にゾーンをまたいで拡大されます。 ゾーンごとに仮想ネットワークを分ける必要はありません。
- デプロイするすべての仮想マシンで、[Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) を使用する必要があります。 アンマネージド ディスクは、ゾーン ベースのデプロイにはサポートされていません。
- Azure Premium Storage と [Ultra SSD ストレージ](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd)では、どのような種類のゾーン間ストレージ レプリケーションもサポートされていません。 重要なデータのレプリケートは、アプリケーション (DBMS または SAP セントラル サービス) で行う必要があります。
- 共有ディスク (Windows)、CIFS 共有 (Windows)、または NFS 共有 (Linux)である共有 sapmnt ディレクトリについても同じです。 ゾーン間でこれらの共有ディスクまたは共有をレプリケートするテクノロジを使用する必要があります。 次のテクノロジがサポートされています。
    - Windows では、「[Azure のクラスター共有ディスクを使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)」に記載されている、SIOS Datakeeper を使用するクラスター ソリューション。
    - SUSE linux では、「[SUSE Linux Enterprise Server 上の Azure VM での NFS の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)」に記載されているように構築された NFS 共有。

  現時点では、「[Windows フェールオーバー クラスターと SAP ASCS/SCS インスタンスのファイル共有を使用して SAP の高可用性向けの Azure インフラストラクチャを準備します](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)」に記載されているように、Microsoft スケールアウト ファイル サーバーを使用するソリューションは、ゾーンをまたいでサポートされません。
- 3 番目のゾーンは、[SUSE Linux Pacemaker クラスター](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device)または追加のアプリケーション インスタンスをビルドする場合に、SBD デバイスをホストするために使用されます。





## <a name="next-steps"></a>次の手順
以下に、Azure Availability Zones 間でデプロイするための次の手順を示します。

- [Azure のクラスター共有ディスクを使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Windows フェールオーバー クラスターと SAP ASCS/SCS インスタンスのファイル共有を使用して SAP の高可用性向けの Azure インフラストラクチャを準備します](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






