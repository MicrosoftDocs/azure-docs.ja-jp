---
title: Azure Migrate での VMware 移行のサポート
description: Azure Migrate での VMware VM 移行のサポートについて説明します。
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 19252a058fd26da6bddf64ad7af132a12cd1e140
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88869101"
---
# <a name="support-matrix-for-vmware-migration"></a>VMware 移行のサポートマトリックス

この記事では、[Azure Migrate を使用した VMware VM 移行サポートの設定と制限事項について説明します。Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) Azure に移行するための VMware VM 評価に関する情報をお探しの場合は、[評価サポートマトリックス](migrate-support-matrix-vmware.md)を確認してください。


## <a name="migration-options"></a>移行オプション

VMware VM は、次のいくつかの方法で移行できます。

- **エージェントレスの移行を使用する**:VM に何もインストールする必要なく VM を移行します。 エージェントレスで移行するには、[Azure Migrate アプライアンス](migrate-appliance.md)をデプロイします。
- **エージェントベースの移行を使用する**:レプリケーションのために VM 上にエージェントをインストールします。 エージェントベースで移行するには、[レプリケーション アプライアンス](migrate-replication-appliance.md)をデプロイします。

[この記事](server-migrate-overview.md)を参照し、あなたが使用したい方法の詳細を確認してください。

## <a name="migration-limitations"></a>移行の上限

- レプリケーションでは、一度に最大 10 個の VM を選択できます。 より多くのマシンを移行する場合は、10 個単位のグループでレプリケートします。
- VMware のエージェントレス移行では、最大 300 個のレプリケーションを同時に実行できます。

## <a name="agentless-migration"></a>エージェントレス型移行 

このセクションでは、エージェントレス移行の要件について概要を説明します。

### <a name="vmware-requirements-agentless"></a>VMware の要件 (エージェントレス)

次の表は、VMware ハイパーバイザーの要件をまとめたものです。

**VMware** | **詳細**
--- | ---
**VMware vCenter サーバー** | バージョン 5.5、6.0、6.5、または 6.7。
**VMware vSphere ESXI ホスト** | バージョン 5.5、6.0、6.5、または 6.7。
**vCenter Server のアクセス許可** | エージェントレスの移行では、[Migrate Appliance](migrate-appliance.md) を使用します。 アプライアンスには、vCenter Server で次のアクセス許可が必要です。<br/><br/> - **Datastore.Browse**:VM のログ ファイルの閲覧を許可して、スナップショットの作成と削除のトラブルシューティングを行います。<br/><br/> - **Datastore.LowLevelFileOperations**: データストア ブラウザーでの読み取り、書き込み、削除、名前変更の各操作を許可して、スナップショットの作成と削除のトラブルシューティングを行います。<br/><br/> - **VirtualMachine.Configuration.DiskChangeTracking**:VM ディスクに対する変更のトラッキングの有効化/無効化を許可し、スナップショット間における変更済みのデータ ブロックをプルします。<br/><br/> - **VirtualMachine.Configuration.DiskLease**:VM のディスクのリース操作を許可し、VMware vSphere Virtual Disk Development Kit (VDDK) を使用してディスクを読み取ります。<br/><br/> - **VirtualMachine.Provisioning.DiskAccess**: (特に vSphere 6.0 以降) VDDK を使用したディスクのランダム読み取りアクセス用に VM 上のディスクを開くことを許可します。<br/><br/> - **VirtualMachine.Provisioning.ReadOnlyDiskAccess**: VM 上のディスクを開くことを許可し、VDDK を使用してディスクを読み取ります。<br/><br/> - **VirtualMachine.Provisioning.DiskRandomAccess**: VM 上のディスクを開くことを許可し、VDDK を使用してディスクを読み取ります。<br/><br/> - **VirtualMachine.Provisioning.VirtualMachineDownload**: VM に関連付けられたファイルの読み取り操作を許可し、エラーが発生した場合にログをダウンロードして、トラブルシューティングを行います。<br/><br/> - **VirtualMachine.SnapshotManagement\*** : レプリケーション用に VM スナップショットの作成と管理を許可します。<br/><br/> - **Virtual Machine.Interaction.Power Off**:Azure への移行中に VM の電源をオフにすることを許可します。



### <a name="vm-requirements-agentless"></a>VM の要件 (エージェントレス)

次の表は、VMware VM のエージェントレス移行の要件をまとめたものです。

**サポート** | **詳細**
--- | ---
**サポートされているオペレーティング システム** | Azure でサポートされている [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) および [Linux](../virtual-machines/linux/endorsed-distros.md) オペレーティング システムを移行できます。
**Azure での Windows VM** | 場合によっては、移行前に VM に[いくつかの変更を加える](prepare-for-migration.md#verify-required-changes-before-migrating)必要があります。 
**Azure での Linux VM** | 一部の VM は、Azure で実行できるように変更が必要な場合があります。<br/><br/> Linux の場合、Azure Migrate によって、次のオペレーティング システム用に自動的に変更が行われます。<br/> - Red Hat Enterprise Linux 6.5+、7.0+<br/> - CentOS 6.5+、7.0+</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS、16.04LTS、18.04LTS<br/> - Debian 7、8。 その他のオペレーティング システムの場合は、手動で[必要な変更](prepare-for-migration.md#verify-required-changes-before-migrating)を行います。
**Linux ブート** | /boot が専用パーティションに存在する場合は、OS ディスク上に存在する必要があり、複数のディスクに分散していてはいけません。<br/> /boot がルート (/) パーティションに含まれている場合は、"/" パーティションは OS ディスク上に存在する必要があり、他のディスクにまたがっていてはいけません。
**UEFI ブート** | UEFI ブートを使用した VM の移行はサポートされません。
**ディスク サイズ** | 2 TB の OS ディスク。データ ディスク用に 8 TB。
**ディスクの制限** |  VM あたり最大 60 台のディスク。
**暗号化されたディスクまたはボリューム** | 暗号化されたディスクまたはボリュームを含む VM の移行はサポートされません。
**共有ディスク クラスター** | サポートされていません。
**独立ディスク** | サポートされていません。
**RDM またはパススルー ディスク** | RDM またはパススルー ディスクが VM に存在する場合、これらのディスクは Azure にレプリケートされません。
**NFS** | VM 上のボリュームとしてマウントされた NFS ボリュームはレプリケートされません。
**iSCSI ターゲット** | iSCSI ターゲットを含む VM は、エージェントレス移行ではサポートされていません。
**マルチパス IO** | サポートされていません。
**ストレージ vMotion** | サポートされていません。 VM でストレージ vMotion を使用している場合、レプリケーションは機能しません。
**チーミングされた NIC** | サポートされていません。
**IPv6** | サポートされていません。
**ターゲット ディスク** | VM は、Azure 内のマネージド ディスク (Standard HDD、Premium SSD) にのみ移行できます。
**同時レプリケーション** | vCenter Server あたり 300 台の VM。 それ以上ある場合は、300 台単位のバッチで移行します。


### <a name="appliance-requirements-agentless"></a>アプライアンスの要件 (エージェントレス)

エージェントレスの移行では、[Azure Migrate アプライアンス](migrate-appliance.md)を使用します。 アプライアンスを VMware VM としてデプロイするには、OVA テンプレートを使用するか、vCenter Server にインポートするか、[PowerShell スクリプト](deploy-appliance-script.md)を使用します。

- VMware の[アプライアンスの要件](migrate-appliance.md#appliance---vmware)を確認してください。
- アプライアンスが[パブリック](migrate-appliance.md#public-cloud-urls)および [Government](migrate-appliance.md#government-cloud-urls) クラウドでアクセスする必要がある URL について確認します。
- Azure Government では、[スクリプトを使用して](deploy-appliance-script-government.md)アプライアンスをデプロイする必要があります。

### <a name="port-requirements-agentless"></a>ポートの要件 (エージェントレス)

**[デバイス]** | **接続**
--- | ---
アプライアンス | ポート 443 で、レプリケートされたデータを Azure にアップロードしたり、レプリケーションや移行のために Azure Migrate と通信を行ったりするためのアウトバウンド接続。
vCenter サーバー | ポート 443 で、アプライアンスがレプリケーションを調整 (スナップショットの作成、データのコピー、スナップショットのリリース) するためのインバウンド接続
vSphere/ESXI ホスト | TCP ポート 902 で、アプライアンスがスナップショットからデータをレプリケートするためのインバウンド接続。

## <a name="agent-based-migration"></a>エージェントベースの移行 


このセクションでは、エージェントベース移行の要件について概要を説明します。


### <a name="vmware-requirements-agent-based"></a>VMware の要件 (エージェントベース)

この表は、VMware 仮想化サーバーに対する評価のサポートと制限をまとめたものです。

**VMware の要件** | **詳細**
--- | ---
**VMware vCenter サーバー** | バージョン 5.5、6.0、6.5、または 6.7。
**VMware vSphere ESXI ホスト** | バージョン 5.5、6.0、6.5、または 6.7。
**vCenter Server のアクセス許可** | vCenter Server 用の読み取り専用アカウント。

### <a name="vm-requirements-agent-based"></a>VM の要件 (エージェントベース)

この表は、エージェントベースの移行を使用して移行させたい VMware VM 向けの VMware VM サポートをまとめたものです。

**サポート** | **詳細**
--- | ---
**マシンのワークロード** | Azure Migrate は、サポートされているマシンで実行されている任意のワークロード (Active Directory、SQL サーバーなど) の移行をサポートします。
**オペレーティング システム** | 最新情報については、Site Recovery に関する[オペレーティング システムのサポート](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)を確認してください。 Azure Migrate でも同じ VM オペレーティング システムがサポートされます。
**Linux ファイル システムとゲストのストレージ** | 最新情報については、Site Recovery に関する [Linux ファイル システムのサポート](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)を確認してください。 Azure Migrate でも同じ Linux ファイル システムがサポートされます。
**ネットワークとストレージ** | 最新情報については、Site Recovery の[ネットワーク](../site-recovery/vmware-physical-azure-support-matrix.md#network)と[ストレージ](../site-recovery/vmware-physical-azure-support-matrix.md#storage)の前提条件を確認してください。 Azure Migrate の場合も、ネットワークとストレージの要件は同じです。
**Azure の要件** | 最新情報については、Site Recovery に使用する [Azure ネットワーク](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)、[ストレージ](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)、[コンピューティング](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute)の要件を確認してください。 VMware の移行に関する要件は、Azure Migrate の場合も同じです。
**モビリティ サービス** | 移行対象となる各 VM に、Mobility Service エージェントがインストールされている必要があります。
**UEFI ブート** | サポートされています。
**ターゲット ディスク** | VM は、Azure 内のマネージド ディスク (Standard HDD、Premium SSD) にのみ移行できます。
**ディスク サイズ** | 2 TB の OS ディスク。データ ディスク用に 8 TB。
**ディスクの制限** |  VM あたり最大 63 台のディスク。
**暗号化されたディスクまたはボリューム** | 暗号化されたディスクまたはボリュームを含む VM の移行はサポートされません。
**共有ディスク クラスター** | サポートされていません。
**独立ディスク** | サポートされています。
**パススルー ディスク** | サポートされています。
**NFS** | VM 上のボリュームとしてマウントされた NFS ボリュームはレプリケートされません。
**iSCSI ターゲット** | サポートされています。
**マルチパス IO** | サポートされていません。
**ストレージ vMotion** | サポートされています
**チーミングされた NIC** | サポートされていません。
**IPv6** | サポートされていません。




### <a name="appliance-requirements-agent-based"></a>アプライアンスの要件 (エージェントベース)

Azure Migrate ハブで提供されている OVA テンプレートを使用してレプリケーション アプライアンスを設定すると、そのアプライアンスは Windows Server 2016 を実行し、サポート要件に準拠しています。 物理サーバーにレプリケーション アプライアンスを手動で設定する場合は、サーバーが要件に準拠していることを確認してください。

- VMware の[レプリケーション アプライアンスの要件](migrate-replication-appliance.md#appliance-requirements)を確認してください。
- アプライアンスに MySQL がインストールされている必要があります。 [インストール オプション](migrate-replication-appliance.md#mysql-installation)を確認してください。
- レプリケーション アプライアンスが[パブリック](migrate-replication-appliance.md#url-access)および [Government](migrate-replication-appliance.md#azure-government-url-access) クラウドでアクセスする必要がある URL について確認します。
- レプリケーション アプライアンスがアクセスする必要のある[ポート](migrate-replication-appliance.md#port-access)を確認します。

### <a name="port-requirements-agent-based"></a>ポートの要件 (エージェントベース)

**[デバイス]** | **接続**
--- | ---
VM | VM 上で実行される Mobility Service は、レプリケーション管理のために、インバウンド ポート HTTPS 443 でオンプレミスのレプリケーション アプライアンス (構成サーバー) と通信します。<br/><br/> VM は、受信ポート HTTPS 9443 でレプリケーション データを (構成サーバー マシン上で実行されている) プロセス サーバーに送信します。 このポートは変更可能です。
レプリケーション アプライアンス | レプリケーション アプライアンスは、アウトバウンド ポート HTTPS 443 経由で Azure によるレプリケーションを調整します。
プロセス サーバー | プロセス サーバーは、レプリケーション データを受信し、データを最適化して暗号化し、アウトバウンド ポート 443 経由で Azure ストレージに送信します。<br/> 既定では、プロセス サーバーはレプリケーション アプライアンスで実行されます。

## <a name="azure-vm-requirements"></a>Azure VM の要件

(エージェントレスまたはエージェントベースの移行を使用して) Azure にレプリケートされたオンプレミス VM はすべて、この表にまとめられている Azure VM の要件を満たしている必要があります。 

**コンポーネント** | **必要条件** 
--- | --- | ---
ゲスト オペレーティング システム | 移行のためにサポートされている VMware VM オペレーティン グシステムを検証します。<br/> サポートされているオペレーティング システム上で実行されているすべてのワークロードを移行できます。 
ゲスト オペレーティング システムのアーキテクチャ | 64 ビット。 
オペレーティング システムのディスク サイズ | 最大 2,048 GB。 
オペレーティング システムのディスク数 | 1 
データ ディスク数 | 64 以下。 
データ ディスク サイズ | 最大 8,095 GB
ネットワーク アダプター | 複数のアダプターがサポートされます。
共有 VHD | サポートされていません。 
FC ディスク | サポートされていません。 
BitLocker | サポートされていません。<br/><br/> マシンを移行する前に、BitLocker を無効にする必要があります。
VM 名 | 1 から 63 文字。<br/><br/> 名前に使用できるのは、英文字、数字、およびハイフンのみです。<br/><br/> マシン名の最初と最後は、文字か数字とする必要があります。 
移行後の接続 - Windows | 移行後に、Windows が実行されている Azure VM に接続するには:<br/><br/> - 移行前に、オンプレミス VM で RDP を有効にします。<br/><br/> TCP と UDP の規則が **[パブリック]** プロファイルに追加されていることを確認し、 **[Windows ファイアウォール]**  >  **[許可されたアプリ]** で、すべてのプロファイルで RDP が許可されていることを確認します。<br/><br/> サイト間 VPN アクセスの場合は、RDP を有効にし、 **[Windows ファイアウォール]**  ->  **[許可されたアプリおよび機能]** で**ドメイン ネットワークとプライベート ネットワーク**の RDP を許可します。<br/><br/> さらに、オペレーティング システムの SAN ポリシーが **[OnlineAll]** に設定されていることを確認します。 [詳細については、こちらを参照してください](prepare-for-migration.md)。
移行後の接続 - Linux | 移行後に、SSH を使用して Azure VM に接続するには:<br/><br/> 移行前に、オンプレミスのマシンで、Secure Shell サービスが [開始] に設定されていることと、ファイアウォール規則で SSH 接続が許可されていることを確認します。<br/><br/> フェールオーバー後の Azure VM で、フェールオーバーされた VM とその接続先の Azure サブネットのネットワーク セキュリティ グループ規則について、SSH ポートへの受信接続を許可します。<br/><br/> さらに、VM のパブリック IP アドレスを追加します。  


## <a name="next-steps"></a>次のステップ

VMware 移行のオプションを[選択](server-migrate-overview.md)します。
