---
title: クラスター構成のベスト プラクティス
description: Azure Virtual Machines 上の SQL Server に対して高可用性とディザスター リカバリー (HADR) を構成する場合に、サポートされるクラスター構成について説明します (サポートされるクォーラムまたは接続ルーティングのオプションなど)。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 4ab4e40e1dd4bbaf9ae73ab545285f5ae6261e27
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201772"
---
# <a name="cluster-configuration-best-practices-sql-server-on-azure-vms"></a>クラスター構成のベスト プラクティス (Azure VM 上の SQL Server)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure Virtual Machines (VM) 上の SQL Server を使用して高可用性とディザスター リカバリー (HADR) を実現するには、クラスターが使用されます。 

この記事では、[フェールオーバー クラスター インスタンス (FCI)](failover-cluster-instance-overview.md) と[可用性グループ](availability-group-overview.md)の両方について、それらを Azure VM 上の SQL Server と共に使用する場合のクラスター構成のベスト プラクティスについて説明します。 


## <a name="networking"></a>ネットワーク

サーバー (クラスター ノード) ごとに 1 つの NIC と、1 つのサブネットを使用します。 Azure ネットワークは物理的な冗長を備えているので、Azure 仮想マシンのゲスト クラスターに NIC とサブネットを追加する必要はありません。 クラスター検証レポートには、ノードは 1 つのネットワーク上でのみ到達可能であることを警告するメッセージが表示されます。 Azure 仮想マシンのゲスト フェールオーバー クラスターでは、この警告を無視できます。

### <a name="tuning-failover-cluster-network-thresholds"></a>フェールオーバー クラスター ネットワークのしきい値の調整

SQL Server AlwaysOn を使用して Azure VM で Windows フェールオーバー クラスター ノードを実行する場合は、クラスター設定をより緩やかな監視状態に変更することをお勧めします。  これにより、クラスターの安定性と信頼性が大幅に向上します。  この詳細については、「[IaaS と SQL AlwaysOn - フェールオーバー クラスター ネットワークのしきい値の調整](/windows-server/troubleshoot/iaas-sql-failover-cluster)」を参照してください。

## <a name="quorum"></a>Quorum

2 ノード クラスターは[クォーラム リソース](/windows-server/storage/storage-spaces/understand-quorum)なしでも機能しますが、実稼働サポートを受けるにはクォーラム リソースを使用することが必須です。 クラスターの検証で、クォーラム リソースを使用していないクラスターが合格することはありません。 

技術的には、3 ノード クラスターの場合、クォーラム リソースなしでも 1 つのノードの損失 (2 ノードにダウンします) を乗り切ることができます。 しかし、クラスターが 2 ノードまで下がると、ノードが失われたり通信エラーが発生したりした場合、スプリットブレイン シナリオを防ぐために、クラスター化されたリソースがオフラインになるリスクがあります。

クォーラム リソースを構成することで、1 つのノードのみをオンラインにしてクラスターのオンラインを続行できます。

次の表に、使用できるクォーラム オプションを、Azure VM での使用が推奨される順序で示します。推奨されている選択肢はディスク監視です。 


||[ディスク監視](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |[クラウド監視](/windows-server/failover-clustering/deploy-cloud-witness)  |[ファイル共有監視](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |
|---------|---------|---------|---------|
|**サポートされる OS**| All |Windows Server 2016 以降| All|


### <a name="disk-witness"></a>ディスク監視

ディスク監視は、クラスターの使用可能記憶域グループ内にある小規模なクラスター化されたディスクです。 このディスクは可用性が高く、ノード間でフェールオーバーできます。 クラスター データベースのコピーが含まれ、通常 1 GB 未満の既定のサイズを持ちます。 ディスク監視は、Azure 共有ディスク (または共有 SCSI、iSCSI、ファイバー チャネル SAN などの共有ディスク ソリューション) を使用するすべてのクラスターに最適なクォーラム オプションです。  クラスター共有ボリュームをディスク監視として使用することはできません。

Azure 共有ディスクをディスク監視として構成します。 

開始するには、[ディスク監視の構成](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)に関する記事をご覧ください。


**サポートされる OS**:All   


### <a name="cloud-witness"></a>クラウド監視

クラウド監視はフェールオーバー クラスターのクォーラム監視の一種であり、Microsoft Azure を使用してクラスター クォーラムでの投票が提供されます。 既定のサイズは約 1 MB で、タイム スタンプだけが含まれます。 クラウド監視は、複数のサイト、複数のゾーン、および複数のリージョンでのデプロイに最適です。

開始するには、[クラウド監視の構成](/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp)に関する記事をご覧ください。


**サポートされる OS**:Windows Server 2016 以降   


### <a name="file-share-witness"></a>ファイル共有監視

ファイル共有監視は、通常 Windows Server を実行しているファイル サーバー上で構成される SMB ファイル共有です。 クラスタリング情報は witness.log ファイルに保持されますが、クラスター データベースのコピーは格納されません。 Azure では、別の仮想マシンでファイル共有を構成することもできます。

開始するには、[ファイル共有監視の構成](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)に関する記事をご覧ください。


**サポートされる OS**:Windows Server 2012 以降   

## <a name="connectivity"></a>接続

従来のオンプレミス ネットワーク環境では、SQL Server のフェールオーバー クラスター インスタンスは、1 台のコンピューター上で実行されている SQL Server の 1 つのインスタンスのように見えます。 フェールオーバー クラスター インスタンスはノードからノードにフェールオーバーするため、そのインスタンスの仮想ネットワーク名 (VNN) によって統一された接続ポイントが提供され、現在アクティブになっているノードを認識することなくアプリケーションから SQL Server インスタンスに接続できるようになります。 フェールオーバーが発生した場合、仮想ネットワーク名は開始後の新しいアクティブ ノードに登録されます。 この処理は、SQL Server に接続しているクライアントまたはアプリケーションからは透過的に実行されます。これにより、障害発生中にクライアントまたはアプリケーションで発生するダウンタイムを最小限に抑えることができます。 同様に、トラフィックを適切なレプリカにルーティングするために、可用性グループ リスナーによって VNN が使用されます。 

Azure Load Balancer または分散ネットワーク名 (DNN) と共に VNN を使用して、Azure VM 上の SQL Server を使用するフェールオーバー クラスター インスタンスの VNN にトラフィックをルーティングするか、可用性グループ内の既存の VNN リスナーを置き換えます。 


次の表は、HADR 接続のサポート性を比較したものです。 

| |**仮想ネットワーク名 (VNN)**  |**分散ネットワーク名 (DNN)**  |
|---------|---------|---------|
|**OS の最小バージョン**| All | Windows Server 2016 |
|**SQL Server の最小バージョン** |All |SQL Server 2019 CU2 (FCI の場合)<br/> SQL Server 2019 CU8 (AG の場合)|
|**サポートされる HADR ソリューション** | フェールオーバー クラスター インスタンス <br/> 可用性グループ | フェールオーバー クラスター インスタンス <br/> 可用性グループ|


### <a name="virtual-network-name-vnn"></a>仮想ネットワーク名 (VNN)

Azure では仮想 IP アクセス ポイントの動作が異なるため、FCI ノードまたは可用性グループ リスナーの IP アドレスにトラフィックをルーティングするように [Azure Load Balancer](../../../load-balancer/index.yml) を構成する必要があります。 Azure 仮想マシンでは、ロード バランサーによって、クラスター化された SQL Server リソースが依存する VNN の IP アドレスが保持されます。 ロード バランサーでは、フロント エンドに到着する受信フローが分散され、バックエンド プールによって定義されたインスタンスにそのトラフィックがルーティングされます。 トラフィック フローを構成するには、負荷分散規則と正常性プローブを使用します。 SQL Server FCI の場合、バックエンド プールのインスタンスは SQL Server を実行する Azure 仮想マシンです。 

ロード バランサーを使用している場合は、フェールオーバーにわずかな遅延が発生します。正常性プローブでは、既定で 10 秒ごとにアライブ チェックが行われるためです。 

まず、[フェールオーバー クラスター インスタンス](failover-cluster-instance-vnn-azure-load-balancer-configure.md)または[可用性グループ](availability-group-vnn-azure-load-balancer-configure.md)用に Azure Load Balancer を構成する方法について説明します。

**サポートされる OS**:All   
**サポートされる SQL バージョン**:All   
**サポートされる HADR ソリューション**:フェールオーバー クラスター インスタンス、可用性グループ   


### <a name="distributed-network-name-dnn"></a>分散ネットワーク名 (DNN)

分散ネットワーク名は、SQL Server 2019 の新しい Azure 機能です。 ロード バランサーを使用せずに SQL Server クライアントから SQL Server フェールオーバー クラスター インスタンスまたは可用性グループに接続するための別の方法が、DNN によって提供されます。 

DNN リソースを作成すると、クラスターではその DNS 名がクラスター内のすべてのノードの IP アドレスにバインドされます。 接続先のリソースを見つけるために、SQL クライアントから、この一覧の各 IP アドレスへの接続が試みられます。  接続文字列に `MultiSubnetFailover=True` を指定することで、このプロセスを高速化できます。 この設定によって、プロバイダーはすべての IP アドレスを並行して試行するように指示されるので、クライアントから FCI またはリスナーへの瞬時の接続ができるようになります。 

次の理由により、可能な場合は、ロード バランサーよりも優先して分散ネットワーク名を使用することをお勧めします。 
- ロード バランサーのリソースを維持する必要がなくなるため、エンド ツー エンド ソリューションはより堅牢になります。 
- ロード バランサーのプローブを排除すると、フェールオーバー時間を最小限に抑えられます。 
- DNN を使用すると、Azure VM 上の SQL Server を使用するフェールオーバー クラスター インスタンスまたは可用性グループ リスナーのプロビジョニングと管理が簡単になります。 

DNN を使用すると、ほとんどの SQL Server 機能は FCI と可用性グループに対して透過的に機能しますが、特定の機能については、特別な考慮が必要となる場合があります。 詳細については、[FCI と DNN の相互運用性](failover-cluster-instance-dnn-interoperability.md)に関するページと、[AG と DNN の相互運用性](availability-group-dnn-interoperability.md)に関するページを参照してください。 

まず、[フェールオーバー クラスター インスタンス](failover-cluster-instance-distributed-network-name-dnn-configure.md)または[可用性グループ](availability-group-distributed-network-name-dnn-listener-configure.md)用に分散ネットワーク名リソースを構成する方法について説明します。

**サポートされる OS**:Windows Server 2016 以降   
**サポートされる SQL バージョン**:SQL Server 2019 CU2 (FCI) と SQL Server 2019 CU8 (AG)   
**サポートされる HADR ソリューション**:フェールオーバー クラスター インスタンス、可用性グループ   


## <a name="limitations"></a>制限事項

FCI または可用性グループと Azure Virtual Machines 上の SQL Server を使用する場合は、次の制限事項を考慮してください。 

### <a name="msdtc"></a>MSDTC 

Azure Virtual Machines では、クラスター共有ボリューム (CSV) と [Azure Standard Load Balancer](../../../load-balancer/load-balancer-overview.md) 上のストレージを使用する Windows Server 2019、または Azure 共有ディスクを使用する SQL Server VM で、Microsoft 分散トランザクション コーディネーター (MSDTC) がサポートされます。 

Azure Virtual Machines では、次の理由により、クラスター共有ボリュームを使用する Windows Server 2016 以前では、MSDTC はサポートされません。

- クラスター化された MSDTC リソースは、共有ストレージを使用するように構成することはできません。 Windows Server 2016 では、MSDTC リソースを作成した場合、ストレージが使用可能であっても、使用可能な共有ストレージは 1 つも表示されません。 この問題は、Windows Server 2019 で修正済みです。
- Basic Load Balance は、RPC ポートを処理しません。


## <a name="next-steps"></a>次のステップ

リューションに適したベストプラクティスを決定したら、[FCI 用に SQL Server VM を準備する](failover-cluster-instance-prepare-vm.md)か、あるいは [Azure portal](availability-group-azure-portal-configure.md)、[Azure CLI/PowerShell](./availability-group-az-commandline-configure.md)、または [Azure クイックスタート テンプレート](availability-group-quickstart-template-configure.md)を使用して、可用性グループを作成することから始めます。
