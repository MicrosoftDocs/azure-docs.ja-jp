---
title: Azure Availability Zones での SAP ワークロードの構成 | Microsoft Docs
description: Azure Availability Zones を使用した SAP NetWeaver のための高可用性のアーキテクチャとシナリオ
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/29/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 842c56ef1fb6f68c3d8b82e2633d9a604db9fde2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101671632"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Azure Availability Zones での SAP ワークロードの構成
Azure 可用性セットでのさまざまな SAP アーキテクチャ レイヤーのデプロイに加えて、最近導入された [Azure Availability Zones](../../../availability-zones/az-overview.md) を SAP ワークロードのデプロイに使用することもできます。 Azure 可用性ゾーンは次のように定義されます。"リージョン内の一意の物理的な場所。 それぞれのゾーンは、独立した電源、冷却手段、ネットワークを備えた 1 つまたは複数のデータセンターで構成されています。" Azure Availability Zones は、すべてのリージョンで使用できるわけではありません。 Availability Zones が提供される Azure リージョンについては、[Azure リージョン マップ](https://azure.microsoft.com/global-infrastructure/geographies/)を確認してください。 このマップでは、Availability Zones が提供されているか、または提供予定と発表されているリージョンが示されます。 

一般的な SAP NetWeaver または S/4HANA アーキテクチャにおいては、次の 3 つの異なるレイヤーを保護する必要があります。

- SAP アプリケーション レイヤー。これは 1 台～数十台の VM である場合があります。 複数の VM が同じホスト サーバー上にデプロイされる可能性を最小限に抑える必要があります。 また、ネットワーク待ち時間を許容可能なウィンドウ内に維持するために、それらの VM を DBMS レイヤーから許容可能な距離内に配置する必要があります。
- SAP ASCS/SCS レイヤー。これは SAP NetWeaver および S/4HANA アーキテクチャ内の単一障害点を表します。 通常は、フェールオーバー フレームワークによってカバーされる 2 台の VM があります。 したがって、これらの VM を、異なるインフラストラクチャの障害ドメインと更新ドメインに割り当てる必要があります。
- SAP DBMS レイヤー。これも単一障害点を表します。 通常は、フェールオーバー フレームワークによってカバーされる 2 台の VM で構成されます。 したがって、これらの VM を、異なるインフラストラクチャの障害ドメインと更新ドメインに割り当てる必要があります。 例外は、3 台以上の VM を使用できる SAP HANA のスケールアウト デプロイです。

重要な VM を可用性セットを使用してデプロイする場合と、Availability Zones を使用してデプロイする場合の主な違いは次のとおりです。

- 可用性セットを使用したデプロイでは、セット内の VM が単一のゾーンまたはデータセンター (特定のリージョンに対してどちらでも適用できます) 内に配置されます。 その結果、可用性セットを使用したデプロイは、そのゾーンのデータセンター全体に影響する電源、冷却手段、またはネットワークの問題からは保護されません。 プラス面としては、VM が、そのゾーンまたはデータセンター内の更新ドメインと障害ドメインに応じて配置されます。 特に、1 つの可用性セットにつき 2 台の VM を保護する SAP ASCS または DBMS レイヤーでは、障害ドメインと更新ドメインに応じた配置により、両方の VM が同じホスト ハードウェア上にデプロイされるのを防ぐことができます。 
- Azure Availability Zones を使用して VM をデプロイし、異なるゾーン (現時点では最大 3 つ) を選択すると、それらの VM を異なる物理的な場所にデプロイすることになり、そのゾーンのデータセンター全体に影響する電源、冷却手段、またはネットワークの問題からの保護が強化されます。 ただし、同じ VM ファミリの複数の VM を同じ可用性ゾーンにデプロイする場合は、それらの VM が同じホスト上にデプロイされるのを防ぐことはできません。 そのため、Availability Zones を使用したデプロイは、通常はそれぞれ 2台の VM が存在する SAP ASCS および DBMS レイヤーに最適です。 VM 数が 2 台を大幅に超える可能性がある SAP アプリケーション レイヤーでは、別のデプロイ モデルへのフォールバックが必要になる場合があります (後述)。

複数の Azure 可用性ゾーンにわたるデプロイは、1 台の重要な VM の障害への対応、または重要な VM におけるソフトウェア修正プログラムのためのダウンタイムの削減に加えて、1 つまたは複数の Azure データセンターの可用性に影響する可能性がある大規模なインフラストラクチャの問題からの保護を目的として行う必要があります。 

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
- [Azure 近接通信配置グループ](../../co-location.md)を使用しない場合、1 つの Azure Availability Zone 内に Azure 可用性セットをデプロイすることはできません。 SAP DBMS レイヤーとセントラル サービスを複数のゾーンにわたってデプロイすると同時に、可用性セットを使用して SAP アプリケーション レイヤーをデプロイし、さらに VM の近接通信も実現する方法については、「[SAP アプリケーションで最適なネットワーク待ち時間を実現する Azure 近接通信配置グループ](sap-proximity-placement-scenarios.md)」の記事で説明しています。 Azure 近接配置グループを使用していない場合は、仮想マシン用のデプロイ フレームワークとして、いずれかを選択する必要があります。
- [Azure Basic Load Balancer](../../../load-balancer/load-balancer-overview.md) を使用して Windows Server フェールオーバー クラスタリングまたは Linux Pacemaker に基づくフェールオーバー クラスター ソリューションを作成することはできません。 代わりに、[Azure Standard Load Balancer SKU](../../../load-balancer/load-balancer-standard-availability-zones.md) を使用する必要があります。



## <a name="the-ideal-availability-zones-combination"></a>Availability Zones の最適な組み合わせ
複数のゾーンにわたって SAP NetWeaver または S/4HANA システムをデプロイする場合は、次の 2 つの原則的なアーキテクチャをデプロイできます。

- アクティブ/アクティブ:ASCS/SCS を実行する VM のペアと DBMS レイヤーを実行する VM のペアが、2 つのゾーンに分散されます。 SAP アプリケーション レイヤーを実行する VM の数が、その 2 つのゾーンに同数ずつデプロイされます。 1 台の DBMS または ASCS/SCS VM がフェールオーバーすると、開いているアクティブなトランザクションの一部がロールバックされる可能性があります。 しかし、ユーザーはログインした状態のままとなります。 アクティブな DBMS VM とアプリケーション インスタンスがどちらのゾーンで実行されているかは、あまり重要ではありません。 このアーキテクチャは、複数のゾーンにわたるデプロイに対して推奨されるアーキテクチャです。
- アクティブ/パッシブ:ASCS/SCS を実行する VM のペアと DBMS レイヤーを実行する VM のペアが、2 つのゾーンに分散されます。 SAP アプリケーション レイヤーを実行する VM の数が、いずれか一方の可用性ゾーンにデプロイされます。 アプリケーション レイヤーは、アクティブな ASCS/SCS および DBMS インスタンスと同じゾーン内で実行されます。 このデプロイ アーキテクチャは、異なるゾーン間のネットワーク待ち時間が、アプリケーション レイヤーを複数のゾーンに分散するには長すぎる場合に使用します。 代わりに、SAP アプリケーション レイヤーをアクティブな ASCS/SCS および/または DBMS インスタンスと同じゾーン内で実行する必要があります。 1 台の ASCS/SCS または DBMS VM がセカンダリ ゾーンにフェールオーバーすると、ネットワーク待ち時間が長くなり、スループットが低下する可能性があります。 前にフェールオーバーした VM をできるだけ早くフェールバックして、前のスループット レベルに戻す必要があります。 ゾーン障害が発生した場合は、アプリケーション レイヤーをセカンダリ ゾーンにフェールオーバーする必要があります。 これは、ユーザーが完全なシステム シャットダウンとして経験するアクティビティです。 一部の Azure リージョンでは、このアーキテクチャが、Availability Zones を使用する場合の唯一の実行可能なアーキテクチャとなります。 ASCS/SCS または DBMS VM がセカンダリ ゾーンにフェールオーバーすることによって考えられる影響を受け入れられない場合は、可用性セットを使用したデプロイを引き続き使用する方がよいでしょう。


そのため、Availability Zones の使用方法を決定する前に、次の事項を判断する必要があります。

- Azure リージョンの 3 つのゾーン間のネットワーク待ち時間。 リージョンのゾーン間のネットワーク待ち時間を把握することにより、ゾーンをまたいだネットワーク トラフィックでネットワーク待ち時間が最も短いゾーンを選択できます。
- 選択した 1 つのゾーン内の VM 間の待ち時間と、選択した 2 つのゾーン間のネットワーク待ち時間の差。
- デプロイする必要がある VM の種類が、選択した 2 つのゾーンで使用可能かどうかの判断。 一部の VM、特に M シリーズの VM では、一部の SKU が 3 つのゾーンのうち 2 つのゾーンでしか使用できない状況が発生する場合があります。

## <a name="network-latency-between-and-within-zones"></a>ゾーン間とゾーン内のネットワーク待ち時間
異なるゾーン間の待ち時間を判断するには、以下のことを行う必要があります。

- DBMS インスタンスに使用する VM SKU を 3 つのゾーンすべてにデプロイします。 この測定を実行するときは、[Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) が有効になっていることを確認します。
- ネットワーク待ち時間が最も短い 2 つのゾーンがわかったら、アプリケーション レイヤー VM として使用する VM SKU の別の 3 つの VM を 3 つの Availability Zones にデプロイします。 選択した 2 つの DBMS ゾーン内の 2 つの DBMS VM に対して、ネットワーク待ち時間を測定します。 
- 測定ツールとして **`niping`** を使用します。 SAP 提供のこのツールの説明は、SAP サポート ノート [#500235](https://launchpad.support.sap.com/#/notes/500235) と [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) をご覧ください。 待ち時間測定用に記載されているコマンドに注目してください。 **ping** は Azure Accelerated Networking コード パスでは機能しないので、ping の使用は推奨されません。

これらのテストを手動で実行する必要はありません。 説明されている待機時間のテストを自動化する、PowerShell プロシージャの[可用性ゾーンの待機時間テスト](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test)を使用できます。 

測定値と Availability Zones での VM SKU の可用性に基づいて、いくつかの決定を行う必要があります。

- DBMS レイヤーの理想的なゾーンを定義する。
- ゾーン内とゾーンをまたいだ場合のネットワーク待ち時間の違いに基づいて、アクティブ SAP アプリケーション レイヤーを 1 つのゾーン、2 つのゾーン、または 3 つ全部のゾーンのいずれに分散するかを決定する。
- アプリケーションの観点から、アクティブ/パッシブまたはアクティブ/アクティブのどちらの構成をデプロイするかを決定する。 (これらの構成については、この記事で後述します)。

これらの決定を行うときは、SAP ノート [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) に記載されている SAP のネットワーク待ち時間に関する推奨事項も考慮してください。

> [!IMPORTANT]
> 行った測定と決定は、測定に使用した Azure サブスクリプションに対して有効です。 別の Azure サブスクリプションを使用する場合は、測定を繰り返す必要があります。 列挙されたゾーンのマッピングは、別の Azure サブスクリプションでは異なることがあります。


> [!IMPORTANT]
> 前述の測定は、[Availability Zones](https://azure.microsoft.com/global-infrastructure/geographies/) をサポートするすべての Azure リージョンで異なる結果になることが予想されます。 ネットワーク待ち時間の要件が同じでも、ゾーン間でネットワーク待ち時間が異なる可能性があるため、異なる Azure リージョンでは異なるデプロイ戦略の採用が必要になる場合があります。 一部の Azure リージョンでは、3 つの異なるゾーン間のネットワーク待ち時間が大きく異なる可能性があります。 他のリージョンでは、3 つの異なるゾーン間のネットワーク待ち時間がより均一になる可能性があります。 常に 1 ～ 2 ミリ秒のネットワーク待ち時間があるという主張は正しくありません。 Azure リージョン内の Availability Zones 間のネットワーク待ち時間を一般化することはできません。

## <a name="activeactive-deployment"></a>アクティブ/アクティブのデプロイ
このデプロイ アーキテクチャがアクティブ/アクティブと呼ばれるのは、2 つまたは 3 つのゾーンにアクティブな SAP アプリケーション サーバーをデプロイするためです。 エンキュー レプリケーションを使用する SAP セントラル サービス インスタンスは、2 つのゾーン間にデプロイされます。 DBMS レイヤーについても同じで、SAP セントラル サービスと同じゾーンにデプロイされます。 この構成を検討する際は、リージョン内で、ワークロードと同期 DBMS レプリケーションについて許容されるゾーン間のネットワーク待ち時間を提供する 2 つの Availability Zones を見つける必要があります。 また、選択したゾーン内のネットワーク待ち時間とゾーン間のネットワーク待ち時間の差が、大きすぎないようにする必要があります。 

SAP アーキテクチャの性質として、異なる方法で構成しない限り、ユーザーとバッチ ジョブを別々のアプリケーション インスタンスで実行できるという点があります。 これがアクティブ/アクティブ デプロイに及ぼす副作用は、バッチ ジョブが、アクティブな DBMS と同じゾーン内で実行されるかどうかに関係なく、任意の SAP アプリケーション インスタンスによって実行される可能性があるということです。 異なるゾーン間と 1 つのゾーン内とでネットワーク待ち時間にあまり差がない場合は、バッチ ジョブの実行時間の差は重要ではないかもしれません。 しかし、ゾーン間とゾーン内のネットワーク トラフィックのネットワーク待ち時間の差が大きいほど、DBMS インスタンスがアクティブではないゾーン内でジョブが実行された場合のバッチ ジョブの実行時間への影響が大きくなる可能性があります。 許容できる実行時間の差を判断するのはお客様です。 複数のゾーンにわたるトラフィックで許容できるネットワーク待ち時間についても同様です。

異なる可用性ゾーンにわたってデプロイされたアプリケーション レイヤー内で、実行時間とスループットに大きな差が生じることなくアクティブ/アクティブ デプロイが可能な Azure リージョンには、たとえば次のものがあります。

- 米国西部 2 (3 つのゾーンすべて)
- 米国東部 2 (3 つのゾーンすべて)
- 米国中部 (3 つのゾーンすべて)
- 北ヨーロッパ (3 つのゾーンすべて)
- 西ヨーロッパ (3 つのゾーンのうち 2 つ)
- 米国東部 (3 つのゾーンのうち 2 つ)
- 米国中南部 (3 つのゾーンのうち 2 つ)
- 英国南部 (3 つのゾーンのうち 2 つ)

複数のゾーンにまたがるこの SAP デプロイ アーキテクチャが推奨されない Azure リージョンは次のとおりです。

- フランス中部 
- 南アフリカ北部
- カナダ中部
- 東日本

お客様が実行時間の差をどの程度許容されるかによって、これ以外のリージョンも対象となる可能性があります。


2 つのゾーン間のアクティブ/アクティブ デプロイの簡略化されたスキーマは次のようになります。

![アクティブ/アクティブ ゾーンのデプロイ](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

この構成には、次の考慮事項が適用されます。

- [Azure 近接通信配置グループ](../../co-location.md)を使用しない場合、可用性セットは Azure Availability Zones にデプロイできないため、Azure Availability Zones をすべての VM に対する障害ドメインおよび更新ドメインとして扱います。
- DBMS レイヤーとセントラル サービス用にゾーン デプロイを組み合わせたい一方で、アプリケーション レイヤーには Azure 可用性セットを使用したい場合は、「[SAP アプリケーションで最適なネットワーク待ち時間を実現する Azure 近接通信配置グループ](sap-proximity-placement-scenarios.md)」の記事で説明しているように、Azure 近接通信グループを使用する必要があります。
- SAP セントラル サービスおよび DBMS レイヤーのフェールオーバー クラスターのロード バランサーには、[Standard SKU Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md) を使用する必要があります。 Basic Load Balancer は、ゾーンをまたいでは機能しません。
- SAP システムをホストするためにデプロイした Azure 仮想ネットワークは、そのサブネットと共にゾーンをまたいで拡大されます。 ゾーンごとに仮想ネットワークを分ける必要はありません。
- デプロイするすべての仮想マシンで、[Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) を使用する必要があります。 アンマネージド ディスクは、ゾーン ベースのデプロイにはサポートされていません。
- Azure Premium Storage、[Ultra SSD ストレージ](../../disks-types.md#ultra-disk)、および ANF では、どのような種類のゾーン間ストレージ レプリケーションもサポートされていません。 重要なデータのレプリケートは、アプリケーション (DBMS または SAP セントラル サービス) で行う必要があります。
- 共有ディスク (Windows)、CIFS 共有 (Windows)、または NFS 共有 (Linux)である共有 sapmnt ディレクトリについても同じです。 ゾーン間でこれらの共有ディスクまたは共有をレプリケートするテクノロジを使用する必要があります。 次のテクノロジがサポートされています。
  - Windows では、「[Azure のクラスター共有ディスクを使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する](./sap-high-availability-guide-wsfc-shared-disk.md)」に記載されている、SIOS Datakeeper を使用するクラスター ソリューション。
  - SUSE linux では、「[SUSE Linux Enterprise Server 上の Azure VM での NFS の高可用性](./high-availability-guide-suse-nfs.md)」に記載されているように構築された NFS 共有。
    
    現時点では、「[Windows フェールオーバー クラスターと SAP ASCS/SCS インスタンスのファイル共有を使用して SAP の高可用性向けの Azure インフラストラクチャを準備します](./sap-high-availability-infrastructure-wsfc-file-share.md)」に記載されているように、Microsoft スケールアウト ファイル サーバーを使用するソリューションは、ゾーンをまたいでサポートされません。
- 3 番目のゾーンは、[SUSE Linux Pacemaker クラスター](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device)をビルドし、Azure Fencing Agent ではなく SBD デバイスを使用する場合に、SBD デバイスをホストするために使用されます。 または、追加のアプリケーション インスタンスに使用されます。
- 重要なビジネス プロセスの実行時間の整合性を達成するには、SAP バッチ サーバー グループ、SAP ログオン グループ、または RFC グループを使用して、アクティブな DBMS インスタンスを含むゾーン内のアプリケーション インスタンスに、特定のバッチ ジョブとユーザーを直接送ることができます。 ただし、ゾーン ベースのフェールオーバーの場合は、アクティブ DB VM を含むゾーン内の VM で実行されているインスタンスにこれらのグループを手動で移動する必要があります。  
- 各ゾーンに休止状態のダイアログ インスタンスをデプロイすることをお勧めします。 

> [!IMPORTANT]
> このアクティブ/アクティブ シナリオでは、帯域幅に対する追加料金が 2020 年 4 月 1 日以降に Microsoft から発表されます。 「[帯域幅の料金詳細](https://azure.microsoft.com/pricing/details/bandwidth/)」のドキュメントを確認してください。 SAP アプリケーション レイヤーと SAP DBMS レイヤー間のデータ転送には、非常に大きな負荷がかかります。 そのため、アクティブ/アクティブ シナリオでは、かなりのコストがかかる可能性があります。 常にこちらの記事を確認して、正確なコストを把握してください 


## <a name="activepassive-deployment"></a>アクティブ/パッシブ デプロイ
1 つのゾーン内のネットワーク待ち時間とゾーンをまたいだネットワーク トラフィックの待ち時間の間に許容できる差が見つからない場合、SAP アプリケーション レイヤーの観点から、アクティブ/パッシブ特性を持つアーキテクチャをデプロイできます。 *アクティブな* ゾーンを定義します。これは、完全なアプリケーション レイヤーをデプロイし、アクティブな DBMS と SAP セントラル サービス インスタンスの両方を実行するゾーンです。 このような構成では、ジョブがアクティブな DBMS インスタンスを含むゾーンで実行されるかどうかによって、ビジネス トランザクションとバッチ ジョブで実行時間に極端な違いが出ないようにする必要があります。

異なるゾーンにわたるこの種類のデプロイ アーキテクチャが適している Azure リージョンは次のとおりです。

- 東南アジア
- オーストラリア東部
- ブラジル南部
- ドイツ中西部
- 南アフリカ北部
- フランス中部 
- カナダ中部
- 東日本


このアーキテクチャの基本的なレイアウトを次に示します。

![アクティブ/パッシブ ゾーンのデプロイ](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

この構成には、次の考慮事項が適用されます。

- 可用性セットを Azure Availability Zones にデプロイすることはできません。 それを補うために、「[SAP アプリケーションで最適なネットワーク待ち時間を実現する Azure 近接通信配置グループ](sap-proximity-placement-scenarios.md)」の記事で説明されているように Azure 近接通信配置グループを使用できます。
- このアーキテクチャを使用する場合は、状態を注意深く監視し、デプロイしたアプリケーション レイヤーと同じゾーン内にアクティブな DBMS と SAP セントラル サービスのインスタンスを維持する必要があります。 SAP セントラル サービスまたは DBMS インスタンスのフェールオーバーの場合は、できるだけ早く SAP アプリケーション レイヤーがデプロイされたゾーンに手動でフェールバックできるようにする必要があります。
- SAP セントラル サービスおよび DBMS レイヤーのフェールオーバー クラスターのロード バランサーには、[Standard SKU Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md) を使用する必要があります。 Basic Load Balancer は、ゾーンをまたいでは機能しません。
- SAP システムをホストするためにデプロイした Azure 仮想ネットワークは、そのサブネットと共にゾーンをまたいで拡大されます。 ゾーンごとに仮想ネットワークを分ける必要はありません。
- デプロイするすべての仮想マシンで、[Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) を使用する必要があります。 アンマネージド ディスクは、ゾーン ベースのデプロイにはサポートされていません。
- Azure Premium Storage、[Ultra SSD ストレージ](../../disks-types.md#ultra-disk)、および ANF では、どのような種類のゾーン間ストレージ レプリケーションもサポートされていません。 重要なデータのレプリケートは、アプリケーション (DBMS または SAP セントラル サービス) で行う必要があります。
- 共有ディスク (Windows)、CIFS 共有 (Windows)、または NFS 共有 (Linux)である共有 sapmnt ディレクトリについても同じです。 ゾーン間でこれらの共有ディスクまたは共有をレプリケートするテクノロジを使用する必要があります。 次のテクノロジがサポートされています。
    - Windows では、「[Azure のクラスター共有ディスクを使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する](./sap-high-availability-guide-wsfc-shared-disk.md)」に記載されている、SIOS Datakeeper を使用するクラスター ソリューション。
    - SUSE linux では、「[SUSE Linux Enterprise Server 上の Azure VM での NFS の高可用性](./high-availability-guide-suse-nfs.md)」に記載されているように構築された NFS 共有。
    
  現時点では、「[Windows フェールオーバー クラスターと SAP ASCS/SCS インスタンスのファイル共有を使用して SAP の高可用性向けの Azure インフラストラクチャを準備します](./sap-high-availability-infrastructure-wsfc-file-share.md)」に記載されているように、Microsoft スケールアウト ファイル サーバーを使用するソリューションは、ゾーンをまたいでサポートされません。
- 3 番目のゾーンは、[SUSE Linux Pacemaker クラスター](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device)をビルドし、Azure Fencing Agent ではなく SBD デバイスを使用する場合に、SBD デバイスをホストするために使用されます。 または、追加のアプリケーション インスタンスに使用されます。
- ゾーンで障害が発生した場合にアプリケーション リソースを開始できるよう、(DBMS の観点から) パッシブ ゾーンに休止中の VM をデプロイする必要があります。
    - [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) は現在、ゾーン間で休止中の VM にアクティブな VM をレプリケートすることはできません。 
- ゾーン障害が発生した場合に 2 番目のゾーンで SAP アプリケーション レイヤーを自動的に開始できるオートメーションに投資する必要があります。

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>高可用性とディザスター リカバリーの構成の組み合わせ
Microsoft は、Azure リージョン内のさまざまな Azure Availability Zones をホストする施設間の地理的距離に関する情報を共有していません。 それでも、一部のお客様は、ゾーンを使用して HA と DR の構成を組み合わせることで、回復ポイントの目標 (RPO) をゼロにすることを約束しています。 RPO がゼロということは、ディザスター リカバリーの場合であっても、コミットされたデータベース トランザクションが失われてはならないことを意味します。 

> [!NOTE]
> このような構成は特定の状況でのみ使用することが推奨されます。 たとえば、セキュリティ上またはコンプライアンス上の理由でデータを Azure リージョンの外部に出せない場合に使用することができます。 

このような構成の 1 つの例を次に示します。

![ゾーン内の高可用性 DR の組み合わせ](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

この構成には、次の考慮事項が適用されます。

- Availability Zones をホストしている施設の間に大きな距離があるか、ユーザーが特定の Azure リージョン内にとどまることを余儀なくされているものと想定します。 可用性セットを Azure Availability Zones にデプロイすることはできません。 それを補うために、「[SAP アプリケーションで最適なネットワーク待ち時間を実現する Azure 近接通信配置グループ](sap-proximity-placement-scenarios.md)」の記事で説明されているように Azure 近接通信配置グループを使用できます。
- このアーキテクチャを使用する場合は、状態を注意深く監視し、デプロイしたアプリケーション レイヤーと同じゾーン内にアクティブな DBMS と SAP セントラル サービスのインスタンスを維持する必要があります。 SAP セントラル サービスまたは DBMS インスタンスのフェールオーバーの場合は、できるだけ早く SAP アプリケーション レイヤーがデプロイされたゾーンに手動でフェールバックできるようにする必要があります。
- アクティブな QA アプリケーション インスタンスを実行する VM に、運用アプリケーションのインスタンスがプレインストールされている必要があります。
- ゾーンの障害が発生した場合は、QA アプリケーションのインスタンスをシャットダウンし、代わりに運用インスタンスを開始します。 これを行うには、アプリケーション インスタンスの仮想名を使用する必要があります。
- SAP セントラル サービスおよび DBMS レイヤーのフェールオーバー クラスターのロード バランサーには、[Standard SKU Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md) を使用する必要があります。 Basic Load Balancer は、ゾーンをまたいでは機能しません。
- SAP システムをホストするためにデプロイした Azure 仮想ネットワークは、そのサブネットと共にゾーンをまたいで拡大されます。 ゾーンごとに仮想ネットワークを分ける必要はありません。
- デプロイするすべての仮想マシンで、[Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) を使用する必要があります。 アンマネージド ディスクは、ゾーン ベースのデプロイにはサポートされていません。
- Azure Premium Storage と [Ultra SSD ストレージ](../../disks-types.md#ultra-disk)では、どのような種類のゾーン間ストレージ レプリケーションもサポートされていません。 重要なデータのレプリケートは、アプリケーション (DBMS または SAP セントラル サービス) で行う必要があります。
- 共有ディスク (Windows)、CIFS 共有 (Windows)、または NFS 共有 (Linux)である共有 sapmnt ディレクトリについても同じです。 ゾーン間でこれらの共有ディスクまたは共有をレプリケートするテクノロジを使用する必要があります。 次のテクノロジがサポートされています。
    - Windows では、「[Azure のクラスター共有ディスクを使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する](./sap-high-availability-guide-wsfc-shared-disk.md)」に記載されている、SIOS Datakeeper を使用するクラスター ソリューション。
    - SUSE linux では、「[SUSE Linux Enterprise Server 上の Azure VM での NFS の高可用性](./high-availability-guide-suse-nfs.md)」に記載されているように構築された NFS 共有。

  現時点では、「[Windows フェールオーバー クラスターと SAP ASCS/SCS インスタンスのファイル共有を使用して SAP の高可用性向けの Azure インフラストラクチャを準備します](./sap-high-availability-infrastructure-wsfc-file-share.md)」に記載されているように、Microsoft スケールアウト ファイル サーバーを使用するソリューションは、ゾーンをまたいでサポートされません。
- 3 番目のゾーンは、[SUSE Linux Pacemaker クラスター](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device)をビルドし、Azure Fencing Agent ではなく SBD デバイスを使用する場合に、SBD デバイスをホストするために使用されます。 





## <a name="next-steps"></a>次のステップ
以下に、Azure Availability Zones 間でデプロイするための次の手順を示します。

- [Azure のクラスター共有ディスクを使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する](./sap-high-availability-guide-wsfc-shared-disk.md)
- [Windows フェールオーバー クラスターと SAP ASCS/SCS インスタンスのファイル共有を使用して SAP の高可用性向けの Azure インフラストラクチャを準備します](./sap-high-availability-infrastructure-wsfc-file-share.md)