---
title: フェールオーバー クラスター インスタンス
description: Azure Virtual Machines 上の SQL Server を使用したフェールオーバー クラスター インスタンス (FCI) について説明します。
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
ms.openlocfilehash: e5862daa21f8bf0075bb1dee567cbe887ec32d72
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653275"
---
# <a name="failover-cluster-instances-with-sql-server-on-azure-virtual-machines"></a>Azure Virtual Machines 上の SQL Server を使用したフェールオーバー クラスター インスタンス
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

この記事では、Azure Virtual Machines (VM) 上の SQL Server に対してフェールオーバー クラスター インスタンス (FCI) を使用する場合の機能の違いについて説明します。 

## <a name="overview"></a>概要

Azure VM 上の SQL Server では、Windows Server フェールオーバー クラスタリング (WSFC) の機能を使用して、サーバー インスタンス レベルでの冗長性によるローカル高可用性を実現します。すなわち、フェールオーバー クラスター インスタンスです。 FCI は、WSFC (または単にクラスター) ノード全体に、場合によっては複数のサブネットにまたがってインストールされる SQL Server の 1 つのインスタンスです。 ネットワーク上では、FCI は 1 台のコンピューター上で実行されている SQL Server のインスタンスのように見えます。 ただし、現在の WSFC ノードが使用できなくなった場合、FCI によって 1 つのノードから別のノードへのフェールオーバーが提供されます。

この記事の残りの部分では、Azure VM 上の SQL Server と共に使用する場合のフェールオーバー クラスター インスタンスの違いに焦点を当てます。 フェールオーバー クラスタリング テクノロジの詳細については、次を参照してください。 

- [Windows クラスター テクノロジ](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server フェールオーバー クラスター インスタンス](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

## <a name="quorum"></a>Quorum

Azure Virtual Machines 上の SQL Server を使用するフェールオーバー クラスター インスタンスでは、クラスター クォーラム用のディスク監視、クラウド監視、またはファイル共有監視の使用がサポートされています。

詳細については、[Azure で SQL Server VM を使用する場合のクォーラムのベスト プラクティス](hadr-cluster-best-practices.md#quorum)に関する記事をご覧ください。 


## <a name="storage"></a>ストレージ

従来のオンプレミスのクラスター化環境では、Windows フェールオーバー クラスターによって、両方のノードから共有記憶域としてアクセス可能な記憶域ネットワーク (SAN) が使用されます。 SQL Server ファイルは共有記憶域でホストされ、アクティブなノードだけが一度にそのファイルにアクセスできます。 

Azure VM 上の SQL Server には、SQL Server フェールオーバー クラスター インスタンスをデプロイするための共有記憶域ソリューションとして、さまざまなオプションが用意されています。 

||[Azure 共有ディスク](../../../virtual-machines/windows/disks-shared.md)|[Premium ファイル共有](../../../storage/files/storage-how-to-create-premium-fileshare.md) |[記憶域スペース ダイレクト (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)|
|---------|---------|---------|---------|
|**OS の最小バージョン**| All |Windows Server 2012|Windows Server 2016|
|**SQL Server の最小バージョン**|All|SQL Server 2012|SQL Server 2016|
|**サポートされる VM の可用性** |近接配置グループを含む可用性セット |可用性セットと可用性ゾーン|可用性セット |
|**FileStream のサポート**|はい|いいえ|はい |
|**Azure BLOB キャッシュ**|いいえ|いいえ|はい|

このセクションの残りの部分では、Azure VM 上の SQL Server で使用できる各記憶域オプションの利点と制限事項を一覧表示します。 

### <a name="azure-shared-disks"></a>Azure 共有ディスク

[Azure 共有ディスク](../../../virtual-machines/windows/disks-shared.md)は [Azure マネージド ディスク](../../../virtual-machines/managed-disks-overview.md)の機能です。 Windows Server フェールオーバー クラスタリングでは、フェールオーバー クラスター インスタンスでの Azure 共有ディスクの使用がサポートされています。 

**サポートされる OS**:All   
**サポートされる SQL バージョン**:All     

**利点**: 
- 高可用性とディザスター リカバリー (HADR) のアーキテクチャをそのまま維持しながら Azure に移行することを検討しているアプリケーションに役立ちます。 
- SCSI 永続的な予約 (SCSI PR) のサポートにより、クラスター化されたアプリケーションをそのまま Azure に移行できます。 
- すべてのバージョンの SQL Server に対して共有 Azure Premium SSD が、SQL Server 2019 に対して共有 Azure Ultra Disk Storage がサポートされます。 
- 1 つの共有ディスクを使用することも、複数の共有ディスクをストライプして共有記憶域プールを作成することもできます。 
- FileStream がサポートされます。


**制限事項**: 
- 仮想マシンは、同じ可用性セットおよび同じ近接配置グループに配置する必要があります。
- 可用性ゾーンはサポートされていません。
- Premium SSD ディスクのキャッシュはサポートされていません。
 
開始するには、[Azure 共有ディスクを使用した SQL Server フェールオーバー クラスター インスタンス](failover-cluster-instance-azure-shared-disks-manually-configure.md)に関する記事をご覧ください。 

### <a name="storage-spaces-direct"></a>記憶域スペース ダイレクト

[記憶域スペース ダイレクト](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)は、Azure Virtual Machines でのフェールオーバー クラスタリングでサポートされている Windows Server の機能です。 ソフトウェアベースの仮想 SAN が提供されます。

**サポートされる OS**:Windows Server 2016 以降   
**サポートされる SQL バージョン**:SQL Server 2016 以降   


**メリット:** 
- 十分なネットワーク帯域幅があれば、堅牢で高パフォーマンスの共有記憶域ソリューションを実現できます。 
- Azure BLOB キャッシュがサポートされるため、キャッシュからローカルで読み取りを行うことができます。 (更新プログラムは両方のノードに同時にレプリケートされます。) 
- FileStream がサポートされます。 

**制限事項:**
- Windows Server 2016 以降でのみ使用できます。 
- 可用性ゾーンはサポートされていません。
- 両方の仮想マシンに同じディスク容量を接続する必要があります。 
- 進行中のディスクのレプリケーションのため、高パフォーマンスを実現するには高いネットワーク帯域幅が必要です。 
- 各 VM に記憶域が接続されるため、より大きな VM サイズと 2 倍の記憶域の料金が必要です。 

開始するには、[記憶域スペース ダイレクトを使用した SQL Server フェールオーバー クラスター インスタンス](failover-cluster-instance-storage-spaces-direct-manually-configure.md)に関する記事をご覧ください。 

### <a name="premium-file-share"></a>Premium ファイル共有

[Premium ファイル共有](../../../storage/files/storage-how-to-create-premium-fileshare.md)は [Azure Files](../../../storage/files/index.yml) の機能です。 Premium ファイル共有は SSD ベースであり、待ち時間が一貫して低くなります。 これらは、Windows Server 2012 以降での SQL Server 2012 以降のフェールオーバー クラスター インスタンスとの使用が完全にサポートされています。 Premium ファイル共有を使用すると柔軟性が向上しまず。ダウンタイムなしでファイル共有のサイズを変更したりスケーリングしたりできるためです。

**サポートされる OS**:Windows Server 2012 以降   
**サポートされる SQL バージョン**:SQL Server 2012 以降   

**メリット:** 
- 複数の可用性ゾーンに分散している仮想マシンの共有記憶域ソリューションのみ。 
- 1 桁の待ち時間とバースト可能な I/O パフォーマンスを備えたフル マネージド ファイル システム。 

**制限事項:**
- Windows Server 2012 以降でのみ使用できます。 
- FileStream はサポートされていません。 


開始するには、[Premium ファイル共有を使用した SQL Server フェールオーバー クラスター インスタンス](failover-cluster-instance-premium-file-share-manually-configure.md)に関する記事をご覧ください。 

### <a name="partner"></a>Partner

パートナー クラスタリング ソリューションとサポートされている記憶域があります。 

**サポートされる OS**:All   
**サポートされる SQL バージョン**:All   

1 つの例では、記憶域として SIOS DataKeeper が使用されます。 詳細については、[フェールオーバー クラスタリングと SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/) に関するブログ記事を参照してください。

### <a name="iscsi-and-expressroute"></a>iSCSI と ExpressRoute

Azure ExpressRoute を介して iSCSI ターゲットの共有ブロック記憶域を公開することもできます。 

**サポートされる OS**:All   
**サポートされる SQL バージョン**:All   

たとえば、NetApp Private Storage (NPS) は、ExpressRoute と Equinix を使用して iSCSI ターゲットを Azure VM に公開します。

Microsoft パートナーの共有記憶域とデータ レプリケーション ソリューションの場合、フェールオーバーでのデータ アクセスに関する問題についてはベンダーにお問い合わせください。

## <a name="connectivity"></a>接続

Azure Virtual Machines 上の SQL Server を使用するフェールオーバー クラスター インスタンスでは、[分散ネットワーク名 (DNN)](hadr-distributed-network-name-dnn-configure.md) または[仮想ネットワーク名 (VNN) と Azure Load Balancer](hadr-vnn-azure-load-balancer-configure.md) を使用して、現在どのノードでクラスター化されたリソースが所有されているかに関係なく、SQL Server インスタンスにトラフィックをルーティングします。 特定の機能と DNN を SQL Server FCI と共に使用する場合は、追加の考慮事項があります。 詳細については、[DNN と SQL Server FCI の相互運用性](failover-cluster-instance-dnn-interoperability.md)に関する記事をご覧ください。 

クラスター接続オプションの詳細については、[HADR 接続を Azure VM 上の SQL Server にルーティングする方法](hadr-cluster-best-practices.md#connectivity)に関する記事をご覧ください。 

## <a name="limitations"></a>制限事項

Azure Virtual Machines 上の SQL Server を使用するフェールオーバー クラスター インスタンスについて、次の制限事項を考慮してください。 

### <a name="lightweight-resource-provider"></a>軽量リソース プロバイダー   
現時点では、Azure 仮想マシン上の SQL Server フェールオーバー クラスター インスタンスは、[SQL Server IaaS Agent 拡張機能](sql-server-iaas-agent-extension-automate-management.md)の[軽量管理モード](sql-vm-resource-provider-register.md#management-modes)でのみサポートされています。 完全拡張機能モードから軽量モードに変更するには、対応する VM の **SQL 仮想マシン** リソースを削除し、それらを軽量モードで SQL VM リソース プロバイダーに登録します。 Azure portal を使用して **SQL 仮想マシン** リソースを削除するときに、正しい仮想マシンの横のチェック ボックスをオフにします。 

完全拡張機能では、自動バックアップ、パッチ適用、高度なポータル管理などの機能がサポートされます。 エージェントを軽量管理モードで再インストールすると、これらの機能は SQL Server VM で動作しなくなります。

### <a name="msdtc"></a>MSDTC   
Azure Virtual Machines では、クラスター化共有ボリューム (CSV) および [Azure Standard Load Balancer](../../../load-balancer/load-balancer-standard-overview.md) 上の記憶域を備えた Windows Server 2019 で、MSDTC がサポートされています。

Azure Virtual Machines では、次の理由により、Windows Server 2016 以前では MSDTC がサポートされていません。

- クラスター化された MSDTC リソースは、共有ストレージを使用するように構成することはできません。 Windows Server 2016 では、MSDTC リソースを作成した場合、ストレージが使用可能であっても、使用可能な共有ストレージは 1 つも表示されません。 この問題は、Windows Server 2019 で修正済みです。
- Basic Load Balance は、RPC ポートを処理しません。


## <a name="next-steps"></a>次のステップ

[クラスター構成のベスト プラクティス](hadr-cluster-best-practices.md)を確認した後、[FCI 用に SQL Server VM を準備する](failover-cluster-instance-prepare-vm.md)ことができます。 

詳細については、次を参照してください。 

- [Windows クラスター テクノロジ](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server フェールオーバー クラスター インスタンス](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

