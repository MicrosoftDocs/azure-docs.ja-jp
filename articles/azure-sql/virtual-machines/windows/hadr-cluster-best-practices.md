---
title: クラスター構成のベスト プラクティス
description: Azure Virtual Machines 上の SQL Server に対して高可用性とディザスター リカバリー (HADR) を構成する場合に、サポートされるクラスター構成について説明します (サポートされるクォーラムまたは接続ルーティングのオプションなど)。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: de773bb2188f09822cae59ce42924a9a49f8087e
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285630"
---
# <a name="cluster-configuration-best-practices-sql-server-on-azure-vms"></a>クラスター構成のベスト プラクティス (Azure VM 上の SQL Server)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure Virtual Machines (VM) 上の SQL Server を使用して高可用性とディザスター リカバリー (HADR) を実現するには、クラスターが使用されます。 

この記事では、[フェールオーバー クラスター インスタンス (FCI)](failover-cluster-instance-overview.md) と[可用性グループ](availability-group-overview.md)の両方について、それらを Azure VM 上の SQL Server と共に使用する場合のクラスター構成のベスト プラクティスについて説明します。 


## <a name="networking"></a>ネットワーク

サーバー (クラスター ノード) ごとに 1 つの NIC と、1 つのサブネットを使用します。 Azure ネットワークは物理的な冗長を備えているので、Azure 仮想マシンのゲスト クラスターに NIC とサブネットを追加する必要はありません。 クラスター検証レポートには、ノードは 1 つのネットワーク上でのみ到達可能であることを警告するメッセージが表示されます。 Azure 仮想マシンのゲスト フェールオーバー クラスターでは、この警告を無視できます。

## <a name="quorum"></a>Quorum

2 ノード クラスターは[クォーラム リソース](/windows-server/storage/storage-spaces/understand-quorum)なしでも機能しますが、実稼働サポートを受けるにはクォーラム リソースを使用することが必須です。 クラスターの検証で、クォーラム リソースを使用していないクラスターが合格することはありません。 

技術的には、3 ノード クラスターの場合、クォーラム リソースなしでも 1 つのノードの損失 (2 ノードにダウンします) を乗り切ることができます。 しかし、クラスターが 2 ノードにダウンした後は、次が発生するリスクがあります。 

- **領域のパーティション分割** (スプリット ブレイン):サーバー、NIC、またはスイッチ上の問題により、ネットワーク上で各クラスター ノードが分離されます。 
- **時間のパーティション分割** (アムネジア):クラスターにノードが追加または再追加され、クラスター グループまたはクラスターの役割の所有権の不適切な要求が試行されます。 

クォーラム リソースにより、これらのいずれの問題からもクラスターが保護されます。 

次の表に、使用できるクォーラム オプションを、Azure VM での使用が推奨される順序で示します。推奨されている選択肢はディスク監視です。 


||[ディスク監視](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |[クラウド監視](/windows-server/failover-clustering/deploy-cloud-witness)  |[ファイル共有監視](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |
|---------|---------|---------|---------|
|**サポートされる OS**| All |Windows Server 2016 以降| Windows Server 2012 以降|




### <a name="disk-witness"></a>ディスク監視

ディスク監視は、クラスターの使用可能記憶域グループ内にある小規模なクラスター化されたディスクです。 このディスクは可用性が高く、ノード間でフェールオーバーできます。 クラスター データベースのコピーが含まれ、通常 1 GB 未満の既定のサイズを持ちます。 ディスク監視は、クラウド監視やファイル共有監視とは異なり、時間のパーティション分割の問題を解決できるため、Azure VM の推奨されるクォーラム オプションとなっています。 

Azure 共有ディスクをディスク監視として構成します。 

開始するには、[ディスク監視の構成](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)に関する記事をご覧ください。


**サポートされる OS**:All   


### <a name="cloud-witness"></a>クラウド監視

クラウド監視はフェールオーバー クラスターのクォーラム監視の一種であり、Microsoft Azure を使用してクラスター クォーラムでの投票が提供されます。 既定のサイズは約 1 MB で、タイム スタンプだけが含まれます。 クラウド監視は、複数のサイト、複数のゾーン、および複数のリージョンでのデプロイに最適です。

開始するには、[クラウド監視の構成](/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp)に関する記事をご覧ください。


**サポートされる OS**:Windows Server 2016 以降   


### <a name="file-share-witness"></a>ファイル共有監視

ファイル共有監視は、通常 Windows Server を実行しているファイル サーバー上で構成される SMB ファイル共有です。 クラスタリング情報は witness.log ファイルに保持されますが、クラスター データベースのコピーは格納されません。 Azure では、[Azure ファイル共有](../../../storage/files/storage-how-to-create-file-share.md)を構成してファイル共有監視として使用することができます。または、別の仮想マシン上のファイル共有を使用することもできます。

Azure ファイル共有を使用する予定の場合は、[Premium ファイル共有のマウント](failover-cluster-instance-premium-file-share-manually-configure.md#mount-premium-file-share)に使用するのと同じプロセスでマウントできます。 

開始するには、[ファイル共有監視の構成](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)に関する記事をご覧ください。


**サポートされる OS**:Windows Server 2012 以降   

## <a name="connectivity"></a>接続

従来のオンプレミス ネットワーク環境では、SQL Server のフェールオーバー クラスター インスタンスは、1 台のコンピューター上で実行されている SQL Server の 1 つのインスタンスのように見えます。 フェールオーバー クラスター インスタンスはノードからノードにフェールオーバーするため、そのインスタンスの仮想ネットワーク名 (VNN) によって統一された接続ポイントが提供され、現在アクティブになっているノードを認識することなくアプリケーションから SQL Server インスタンスに接続できるようになります。 フェールオーバーが発生した場合、仮想ネットワーク名は開始後の新しいアクティブ ノードに登録されます。 この処理は、SQL Server に接続しているクライアントまたはアプリケーションからは透過的に実行されます。これにより、障害発生中にクライアントまたはアプリケーションで発生するダウンタイムを最小限に抑えることができます。 

Azure Load Balancer または分散ネットワーク名 (DNN) と共に VNN を使用して、Azure VM 上の SQL Server を使用するフェールオーバー クラスター インスタンスの VNN にトラフィックをルーティングします。 現在、DNN 機能は Windows Server 2016 (またはそれ以降) の仮想マシン上の SQL Server 2019 CU2 以降でのみ使用できます。 

次の表は、HADR 接続のサポート性を比較したものです。 

| |**仮想ネットワーク名 (VNN)**  |**分散ネットワーク名 (DNN)**  |
|---------|---------|---------|
|**OS の最小バージョン**| Windows Server 2012 | Windows Server 2016|
|**SQL Server の最小バージョン** |SQL Server 2012 |SQL Server 2019 CU2|
|**サポートされる HADR ソリューション** | フェールオーバー クラスター インスタンス <br/> 可用性グループ | フェールオーバー クラスター インスタンス|


### <a name="virtual-network-name-vnn"></a>仮想ネットワーク名 (VNN)

Azure では仮想 IP アクセス ポイントの動作が異なるため、FCI ノードの IP アドレスにトラフィックをルーティングするように [Azure Load Balancer](../../../load-balancer/index.yml) を構成する必要があります。 Azure 仮想マシンでは、ロード バランサーによって、クラスター化された SQL Server リソースが依存する VNN の IP アドレスが保持されます。 ロード バランサーでは、フロント エンドに到着する受信フローが分散され、バックエンド プールによって定義されたインスタンスにそのトラフィックがルーティングされます。 トラフィック フローを構成するには、負荷分散規則と正常性プローブを使用します。 SQL Server FCI の場合、バックエンド プールのインスタンスは SQL Server を実行する Azure 仮想マシンです。 

ロード バランサーを使用している場合は、フェールオーバーにわずかな遅延が発生します。正常性プローブでは、既定で 10 秒ごとにアライブ チェックが行われるためです。 

作業を開始するには、[FCI 用に Azure Load Balancer を構成する](hadr-vnn-azure-load-balancer-configure.md)方法をご確認ください。 

**サポートされる OS**:Windows Server 2012 以降   
**サポートされる SQL バージョン**:SQL Server 2012 以降   
**サポートされる HADR ソリューション**:フェールオーバー クラスター インスタンス、可用性グループ 


### <a name="distributed-network-name-dnn"></a>分散ネットワーク名 (DNN)

分散ネットワーク名は、SQL Server 2019 CU2 の新しい Azure 機能です。 DNN によって、ロード バランサーを使用せずに SQL Server クライアントから SQL Server フェールオーバー クラスター インスタンスに接続するための別の方法が提供されます。 

DNN リソースを作成すると、クラスターではその DNS 名がクラスター内のすべてのノードの IP アドレスにバインドされます。 SQL クライアントでは、フェールオーバー クラスター インスタンスが現在実行されているノードを見つけるために、この一覧に含まれている各 IP アドレスへの接続が試行されます。 接続文字列に `MultiSubnetFailover=True` を指定することで、このプロセスを高速化できます。 この設定では、すべての IP アドレスを並行して試行するようにプロバイダーが指示されます。これにより、クライアントは瞬時に FCI に接続できます。 

次の理由により、可能な場合は、ロード バランサーよりも優先して分散ネットワーク名を使用することをお勧めします。 
- ロード バランサーのリソースを維持する必要がなくなるため、エンド ツー エンド ソリューションはより堅牢になります。 
- ロード バランサーのプローブを排除すると、フェールオーバー時間を最小限に抑えられます。 
- DNN を使用すると、Azure VM 上の SQL Server を使用するフェールオーバー クラスター インスタンスのプロビジョニングと管理が簡単になります。 

ほとんどの SQL Server 機能は、FCI で透過的に機能します。 このような場合は、既存の VNN の DNS 名を DNN の DNS 名に置き換えるか、DNN の値を既存の VNN の DNS 名に設定するだけで済みます。 ただし、一部のサーバー側コンポーネントには、VNN 名を DNN 名にマップするネットワークの別名が必要です。 特定のケースでは、DNN の DNS 名を明示的に使用することが必要になる場合があります。サーバー側の構成で特定の URL を定義する場合などです。 

作業を開始するには、[FCI 用に DNN リソースを構成する](hadr-distributed-network-name-dnn-configure.md)方法をご確認ください。 

**サポートされる OS**:Windows Server 2016 以降   
**サポートされる SQL バージョン**:SQL Server 2019 以降   
**サポートされる HADR ソリューション**:フェールオーバー クラスター インスタンスのみ


## <a name="limitations"></a>制限事項

FCI または可用性グループと Azure Virtual Machines 上の SQL Server を使用する場合は、次の制限事項を考慮してください。 

### <a name="msdtc"></a>MSDTC 
Azure Virtual Machines では、クラスター化共有ボリューム (CSV) および [Azure Standard Load Balancer](../../../load-balancer/load-balancer-standard-overview.md) 上の記憶域を備えた Windows Server 2019 で、Microsoft 分散トランザクション コーディネーター (MSDTC) がサポートされています。

Azure Virtual Machines では、次の理由により、Windows Server 2016 以前では MSDTC がサポートされていません。

- クラスター化された MSDTC リソースは、共有ストレージを使用するように構成することはできません。 Windows Server 2016 では、MSDTC リソースを作成した場合、ストレージが使用可能であっても、使用可能な共有ストレージは 1 つも表示されません。 この問題は、Windows Server 2019 で修正済みです。
- Basic Load Balance は、RPC ポートを処理しません。


## <a name="next-steps"></a>次のステップ

ご自分のソリューションに適したベスト プラクティスを決定した後は、まず [FCI 用の SQL Server VM を準備します](failover-cluster-instance-prepare-vm.md)。 [Azure CLI](availability-group-az-cli-configure.md) または [Azure クイックスタート テンプレート](availability-group-quickstart-template-configure.md)を使用して可用性グループを作成することもできます。 

