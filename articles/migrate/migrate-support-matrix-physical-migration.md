---
title: Azure Migrate での物理サーバーの移行のサポート
description: Azure Migrate での物理サーバーの移行のサポートについて説明します。
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/14/2020
ms.openlocfilehash: 4ebc363f29ed8956d10e91f41be1d257cbcc492f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98703888"
---
# <a name="support-matrix-for-physical-server-migration"></a>物理サーバーの移行のサポート マトリックス

この記事では、以下を使用して物理サーバーを Azure に移行する場合のサポート設定と制限事項について説明します: [Azure Migrate:Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) Azure への移行のための物理サーバーの評価に関する情報を探している場合は、[評価のサポート マトリックス](migrate-support-matrix-physical.md)を確認してください。

## <a name="migrating-machines-as-physical"></a>物理としてのコンピューターの移行

エージェント ベースのレプリケーションを使用して、オンプレミスのコンピューターを物理サーバーとして移行できます。 このツールを使用すると、さまざまなマシンを Azure に移行できます。

- オンプレミスの物理サーバー。
- Xen や KVM などのプラットフォームによって仮想化された VM。
- Hyper-V VM または VMware VM。何らかの理由で、標準の [Hyper-V](tutorial-migrate-hyper-v.md) または [VMware](server-migrate-overview.md) フローを使用したくない場合。
- プライベート クラウドで実行されている VM。
- アマゾン ウェブ サービス (AWS) や Google Cloud Platform (GCP) などのパブリック クラウドで実行されている VM。


## <a name="migration-limitations"></a>移行の制限

レプリケーションのために一度に最大 10 台のコンピューターを選択できます。 より多くのマシンを移行する場合は、10 個単位のグループでレプリケートします。


## <a name="physical-server-requirements"></a>物理サーバーの要件

この表では、エージェント ベースの移行を使用して移行する物理サーバーのサポートを要約しています。

**サポート** | **詳細**
--- | ---
**マシンのワークロード** | Azure Migrate は、サポートされているマシンで実行されている任意のワークロード (Active Directory、SQL サーバーなど) の移行をサポートします。
**オペレーティング システム** | 最新情報については、Site Recovery に関する[オペレーティング システムのサポート](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)を確認してください。 Azure Migrate では、同じオペレーティング システムのサポートが提供されます。
**Linux ファイル システムとゲストのストレージ** | 最新情報については、Site Recovery に関する [Linux ファイル システムのサポート](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)を確認してください。 Azure Migrate では、同じ Linux ファイル システムのサポートが提供されます。
**ネットワークとストレージ** | 最新情報については、Site Recovery の[ネットワーク](../site-recovery/vmware-physical-azure-support-matrix.md#network)と[ストレージ](../site-recovery/vmware-physical-azure-support-matrix.md#storage)の前提条件を確認してください。 Azure Migrate の場合も、ネットワークとストレージの要件は同じです。
**Azure の要件** | 最新情報については、Site Recovery に使用する [Azure ネットワーク](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)、[ストレージ](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)、[コンピューティング](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute)の要件を確認してください。 Azure Migrate には、物理サーバーの移行のための同じ要件があります。
**モビリティ サービス** | 移行する各コンピューターにモビリティ サービス エージェントがインストールされている必要があります。
**UEFI ブート** | サポートされています。 UEFI ベースのマシンは、Azure 第 2 世代 VM に移行されます。  <br/><br/> OS ディスクには最大 4 つのパーティションが必要で、ボリュームは NTFS でフォーマットされている必要があります。
**UEFI - セキュア ブート**         | 移行はサポートされません。
**ターゲット ディスク** | コンピューターは、Azure のマネージド ディスク (Standard HDD、Standard SSD、Premium SSD) にのみ移行できます。
**ディスク サイズ** | 2 TB の OS ディスク。データ ディスク用に 32 TB。
**ディスクの制限** |  コンピューターあたり最大 63 台のディスク。
**暗号化されたディスクまたはボリューム** |  移行では、暗号化されたディスク/ボリュームを含むコンピューターはサポートされません。
**共有ディスク クラスター** | サポートされていません。
**独立ディスク** | サポートされています。
**パススルー ディスク** | サポートされています。
**NFS** | コンピューター上のボリュームとしてマウントされた NFS ボリュームはレプリケートされません。
**iSCSI ターゲット** | エージェントレス移行では、iSCSI ターゲットを含むコンピューターはサポートされません。
**マルチパス IO** | サポートされていません。
**チーミングされた NIC** | サポートされていません。
**IPv6** | サポートされていません。



## <a name="replication-appliance-requirements"></a>レプリケーション アプライアンスの要件

物理サーバー上で手動でレプリケーション アプライアンスを設定する場合は、そのサーバーが、この表に要約されている要件に従っていることを確認してください。 Azure Migrate ハブで提供されている OVA テンプレートを使用して、Azure Migrate レプリケーション アプライアンスを VMware VM として設定すると、そのアプライアンスは Windows Server 2016 で設定されるため、サポート要件に従います。 

- [レプリケーション アプライアンスの要件](migrate-replication-appliance.md#appliance-requirements)を確認してください。
- アプライアンスに MySQL がインストールされている必要があります。 [インストール オプション](migrate-replication-appliance.md#mysql-installation)を確認してください。
- レプリケーション アプライアンスがアクセスする必要のある [URL](migrate-replication-appliance.md#url-access) を確認してください。

## <a name="azure-vm-requirements"></a>Azure VM の要件

Azure にレプリケートされたオンプレミス VM はすべて、この表にまとめられている Azure VM の要件を満たしている必要があります。 Site Recovery がレプリケーションの前提条件確認を実行するとき、満たされていない要件がある場合には確認が失敗します。

**コンポーネント** | **必要条件** | **詳細**
--- | --- | ---
ゲスト オペレーティング システム | サポートされているオペレーティング システムを確認してください。<br/> サポートされているオペレーティング システム上で実行されているすべてのワークロードを移行できます。 | サポートされていない場合、確認は失敗します。
ゲスト オペレーティング システムのアーキテクチャ | 64 ビット。 | サポートされていない場合、確認は失敗します。
オペレーティング システムのディスク サイズ | 最大 2,048 GB。 | サポートされていない場合、確認は失敗します。
オペレーティング システムのディスク数 | 1 | サポートされていない場合、確認は失敗します。
データ ディスク数 | 64 以下。 | サポートされていない場合、確認は失敗します。
データ ディスク サイズ | 最大 32 TB | サポートされていない場合、確認は失敗します。
ネットワーク アダプター | 複数のアダプターがサポートされます。 |
共有 VHD | サポートされていません。 | サポートされていない場合、確認は失敗します。
FC ディスク | サポートされていません。 | サポートされていない場合、確認は失敗します。
BitLocker | サポートされていません。 | マシンのレプリケーションを有効にする前に、BitLocker を無効にする必要があります。
VM 名 | 1 から 63 文字。<br/> 名前に使用できるのは、英文字、数字、およびハイフンのみです。<br/><br/> マシン名の最初と最後は、文字か数字とする必要があります。 |  Site Recovery でマシンのプロパティの値を更新します。
移行後の接続 - Windows | 移行後に、Windows が実行されている Azure VM に接続するには:<br/> - 移行前に、オンプレミスの VM で RDP を有効にします。 TCP と UDP の規則が **[パブリック]** プロファイルに追加されていることを確認し、 **[Windows ファイアウォール]**  >  **[許可されたアプリ]** で、すべてのプロファイルで RDP が許可されていることを確認します。<br/> サイト間 VPN アクセスの場合は、RDP を有効にし、 **[Windows ファイアウォール]**  ->  **[許可されたアプリおよび機能]** で **ドメイン ネットワークとプライベート ネットワーク** の RDP を許可します。 さらに、オペレーティング システムの SAN ポリシーが **[OnlineAll]** に設定されていることを確認します。 [詳細については、こちらを参照してください](prepare-for-migration.md)。 |
移行後の接続 - Linux | 移行後に、SSH を使用して Azure VM に接続するには:<br/> 移行前に、オンプレミスのマシンで、Secure Shell サービスが [開始] に設定されていることと、ファイアウォール規則で SSH 接続が許可されていることを確認します。<br/> フェールオーバー後の Azure VM で、フェールオーバーされた VM とその接続先の Azure サブネットのネットワーク セキュリティ グループ規則について、SSH ポートへの受信接続を許可します。 さらに、VM のパブリック IP アドレスを追加します。 |  


## <a name="next-steps"></a>次のステップ

物理サーバーを[移行](tutorial-migrate-physical-virtual-machines.md)する。
