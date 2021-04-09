---
title: Azure Migrate での VMware 移行のサポート
description: Azure Migrate での VMware VM 移行のサポートについて説明します。
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 40fcdae9a94b2b48eb4c665f4e0c9c3e58962f4b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104576783"
---
# <a name="support-matrix-for-vmware-migration"></a>VMware 移行のサポートマトリックス

この記事では、[Azure Migrate を使用した VMware VM 移行サポートの設定と制限事項について説明します。Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) Azure に移行するための VMware VM 評価に関する情報をお探しの場合は、[評価サポートマトリックス](migrate-support-matrix-vmware.md)を確認してください。


## <a name="migration-options"></a>移行オプション

VMware VM は、次のいくつかの方法で移行できます。

- **エージェントレスの移行を使用する**:VM に何もインストールする必要なく VM を移行します。 エージェントレスで移行するには、[Azure Migrate アプライアンス](migrate-appliance.md)をデプロイします。
- **エージェントベースの移行を使用する**:レプリケーションのために VM 上にエージェントをインストールします。 エージェントベースで移行するには、[レプリケーション アプライアンス](migrate-replication-appliance.md)をデプロイします。

[この記事](server-migrate-overview.md)を参照し、あなたが使用したい方法の詳細を確認してください。

## <a name="agentless-migration"></a>エージェントレス型移行 

このセクションでは、エージェントレス VMware VM を Azure に移行する際の要件の概要を示します。

### <a name="vmware-requirements-agentless"></a>VMware の要件 (エージェントレス)

次の表は、VMware ハイパーバイザーの要件をまとめたものです。

**VMware** | **詳細**
--- | ---
**VMware vCenter サーバー** | バージョン 5.5、6.0、6.5、6.7、7.0。
**VMware vSphere ESXI ホスト** | バージョン 5.5、6.0、6.5、6.7、7.0。
**vCenter Server のアクセス許可** | エージェントレスの移行では、[Migrate Appliance](migrate-appliance.md) を使用します。 アプライアンスには、vCenter Server で次のアクセス許可が必要です。<br/><br/> - **Datastore.Browse** ([データストア] -> [データストアの参照]):VM のログ ファイルの閲覧を許可して、スナップショットの作成と削除のトラブルシューティングを行います。<br/><br/> - **Datastore.FileManagement** ([データストア] -> [低レベルのファイル操作]):データストア ブラウザーでの読み取り、書き込み、削除、名前変更の各操作を許可して、スナップショットの作成と削除のトラブルシューティングを行います。<br/><br/> - **VirtualMachine.Config.ChangeTracking** ([仮想マシン] -> [ディスク変更の追跡]):VM ディスクに対する変更のトラッキングの有効化/無効化を許可し、スナップショット間における変更済みのデータ ブロックをプルします。<br/><br/> - **VirtualMachine.Config.DiskLease** ([仮想マシン] -> [ディスクのリース]):VM のディスクのリース操作を許可し、VMware vSphere Virtual Disk Development Kit (VDDK) を使用してディスクを読み取ります。<br/><br/> - **VirtualMachine.Provisioning.DiskRandomRead** ([仮想マシン] -> [プロビジョニング] -> [読み取り専用ディスク アクセスの許可]):VM 上のディスクを開くことを許可し、VDDK を使用してディスクを読み取ります。<br/><br/> - **VirtualMachine.Provisioning.DiskRandomAccess** ([仮想マシン] -> [プロビジョニング] -> [ディスク アクセスの許可]):VM 上のディスクを開くことを許可し、VDDK を使用してディスクを読み取ります。<br/><br/> - **VirtualMachine.Provisioning.GetVmFiles** ([仮想マシン] -> [プロビジョニング] -> [仮想マシンのダウンロードの許可]):VM に関連付けられたファイルの読み取り操作を許可し、エラーが発生した場合にログをダウンロードして、トラブルシューティングを行います。<br/><br/> - **VirtualMachine.State.\* *_ ([仮想マシン] -> [スナップショット管理]):レプリケーション用に VM スナップショットの作成と管理を許可します。<br/><br/> - _* VirtualMachine.Interact.PowerOff** ([仮想マシン] -> [相互作用] -> [パワーオフ]):Azure への移行中に VM の電源をオフにすることを許可します。



### <a name="vm-requirements-agentless"></a>VM の要件 (エージェントレス)

次の表は、VMware VM のエージェントレス移行の要件をまとめたものです。

**サポート** | **詳細**
--- | ---
**サポートされているオペレーティング システム** | Azure でサポートされている [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) および [Linux](../virtual-machines/linux/endorsed-distros.md) オペレーティング システムを移行できます。
**Azure での Windows VM** | 場合によっては、移行前に VM に[いくつかの変更を加える](prepare-for-migration.md#verify-required-changes-before-migrating)必要があります。 
**Azure での Linux VM** | 一部の VM は、Azure で実行できるように変更が必要な場合があります。<br/><br/> Linux の場合、Azure Migrate によって、次のオペレーティング システム用に自動的に変更が行われます。<br/> - Red Hat Enterprise Linux 7.8、7.7、7.6、7.5、7.4、7.0、6.x<br/> - Cent OS 7.7、7.6、7.5、7.4、6.x</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - SUSE Linux Enterprise Server 15 SP1 <br/>- Ubuntu 19.04、19.10、14.04LTS、16.04LTS、18.04LTS<br/> - Debian 7、8、9 <br/> Oracle Linux 7.7、7.7-CI<br/> その他のオペレーティング システムの場合は、手動で[必要な変更](prepare-for-migration.md#verify-required-changes-before-migrating)を行います。
**Linux ブート** | /boot が専用パーティションに存在する場合は、OS ディスク上に存在する必要があり、複数のディスクに分散していてはいけません。<br/> /boot がルート (/) パーティションに含まれている場合は、"/" パーティションは OS ディスク上に存在する必要があり、他のディスクにまたがっていてはいけません。
**UEFI ブート** | サポートされています。 UEFI ベースの VM は、Azure 第 2 世代 VM に移行されます。 
**ディスク サイズ** | 2 TB の OS ディスク。データ ディスク用に 32 TB。
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
**ターゲット ディスク** | VM は、Azure のマネージド ディスク (Standard HDD、Standard SSD、Premium SSD) にのみ移行できます。
**同時レプリケーション** | 1 つのアプライアンスがある vCenter Server ごとに最大 300 の VM を同時にレプリケートします。 追加の[スケールアウト アプライアンス](./how-to-scale-out-for-migration.md)がデプロイされている場合、vCenter Server ごとに最大 500 の VM を同時にレプリケートします。 
**Azure VM エージェントの自動インストール (Windows および Linux エージェント)** | Windows Server 2008 R2 以降でサポートされています。 <br/> RHEL6、RHEL7、CentOS7、Ubuntu 14.04、Ubuntu 16.04、Ubuntu 18.04 でサポートされています。 これらの Linux オペレーティング システムに[必要なパッケージ](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux#requirements)の一覧をご確認ください。

> [!TIP]
>  Azure portal を使用すると、最大 10 個の VM を一度に選択してレプリケーションを構成できます。 より多くの VM をレプリケートするために、ポータルを使用して、レプリケートする VM を 10 個の VM の複数のバッチで追加するか、Azure Migrate PowerShell インターフェイスを使用してレプリケーションを構成することができます。 同時レプリケーションがサポートされている VM の最大数を超えて同時レプリケーションを構成しないようにしてください。

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
**UEFI ブート** | サポートされています。 UEFI ベースの VM は、Azure 第 2 世代 VM に移行されます。 
**UEFI - セキュア ブート**         | 移行はサポートされません。
**ターゲット ディスク** | VM は、Azure のマネージド ディスク (Standard HDD、Standard SSD、Premium SSD) にのみ移行できます。
**ディスク サイズ** | 2 TB の OS ディスク。データ ディスク用に 32 TB。
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
データ ディスク サイズ | 最大 32 TB
ネットワーク アダプター | 複数のアダプターがサポートされます。
共有 VHD | サポートされていません。 
FC ディスク | サポートされていません。 
BitLocker | サポートされていません。<br/><br/> マシンを移行する前に、BitLocker を無効にする必要があります。
VM 名 | 1 から 63 文字。<br/><br/> 名前に使用できるのは、英文字、数字、およびハイフンのみです。<br/><br/> マシン名の最初と最後は、文字か数字とする必要があります。 
移行後の接続 - Windows | 移行後に、Windows が実行されている Azure VM に接続するには:<br/><br/> - 移行前に、オンプレミス VM で RDP を有効にします。<br/><br/> TCP と UDP の規則が **[パブリック]** プロファイルに追加されていることを確認し、 **[Windows ファイアウォール]**  >  **[許可されたアプリ]** で、すべてのプロファイルで RDP が許可されていることを確認します。<br/><br/> サイト間 VPN アクセスの場合は、RDP を有効にし、 **[Windows ファイアウォール]**  ->  **[許可されたアプリおよび機能]** で **ドメイン ネットワークとプライベート ネットワーク** の RDP を許可します。<br/><br/> さらに、オペレーティング システムの SAN ポリシーが **[OnlineAll]** に設定されていることを確認します。 [詳細については、こちらを参照してください](prepare-for-migration.md)。
移行後の接続 - Linux | 移行後に、SSH を使用して Azure VM に接続するには:<br/><br/> 移行前に、オンプレミスのマシンで、Secure Shell サービスが [開始] に設定されていることと、ファイアウォール規則で SSH 接続が許可されていることを確認します。<br/><br/> フェールオーバー後の Azure VM で、フェールオーバーされた VM とその接続先の Azure サブネットのネットワーク セキュリティ グループ規則について、SSH ポートへの受信接続を許可します。<br/><br/> さらに、VM のパブリック IP アドレスを追加します。  


## <a name="next-steps"></a>次のステップ

VMware 移行のオプションを[選択](server-migrate-overview.md)します。
