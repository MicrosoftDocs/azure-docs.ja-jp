---
title: Azure Site Recovery を使用した Hyper-V VM から Azure へのディザスター リカバリーのサポート
description: Azure Site Recovery を使用して、Azure への Hyper-V VM のディザスター リカバリーを行う場合にサポートされるコンポーネントと要件の概要について説明します。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 7/14/2020
ms.author: raynew
ms.openlocfilehash: 53967ab0bec9488691ff60cdabb8fedbb6b9730e
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386709"
---
# <a name="support-matrix-for-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>オンプレミス Hyper-V VM から Azure へのディザスター リカバリーのサポート マトリックス

この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用して、オンプレミス Hyper-V VM の Azure へのディザスター リカバリーを行う場合にサポートされるコンポーネントと設定の概要について説明します。

>[!NOTE]
> Site Recovery では、ソース マシンのディザスター リカバリーがセットアップされているターゲット リージョンから顧客データを移動または格納することはありません。 お客様は、必要に応じて、別のリージョンから Recovery Services コンテナーを選択することもできます。 Recovery Services コンテナーにはメタデータが含まれますが、実際の顧客データは含まれません。

## <a name="supported-scenarios"></a>サポートされるシナリオ

**シナリオ** | **詳細**
--- | ---
Hyper-V (Virtual Machine Manager あり) <br> <br>| System Center Virtual Machine Manager ファブリックで管理されている Hyper-V ホスト上で実行されている VM の場合、Azure へのディザスター リカバリーを実行できます。<br/><br/> このシナリオは、Azure Portal または PowerShell を使用して展開できます。<br/><br/> Hyper-V ホストが Virtual Machine Manager で管理されている場合は、セカンダリ オンプレミス サイトへのディザスター リカバリーを実行することもできます。 このシナリオの詳細については、[こちらのチュートリアル](hyper-v-vmm-disaster-recovery.md)を参照してください。
Hyper-V (Virtual Machine Manager なし) | Virtual Machine Manager によって管理されていない Hyper-V ホスト上で実行されている VM の場合、Azure へのディザスター リカバリーを実行できます。<br/><br/> このシナリオは、Azure Portal または PowerShell を使用して展開できます。

## <a name="on-premises-servers"></a>オンプレミスのサーバー

**サーバー** | **必要条件** | **詳細**
--- | --- | ---
Hyper-V (Virtual Machine Manager なしで実行) |  Windows Server 2019、Windows Server 2016、最新の更新プログラムが適用された Windows Server 2012 R2 <br/><br/> **注:** これらのオペレーティング システムの Server コア インストールもサポートされています。 | 既に Windows Server 2012 R2 と Azure Site Recovery または SCVMM 2012 R2 と Azure Site Recovery を構成済みで、OS のアップグレードを予定している場合は、ガイダンス [ドキュメント](upgrade-2012R2-to-2016.md)に従ってください。
Hyper-V (Virtual Machine Manager ありで実行) | Virtual Machine Manager 2019、Virtual Machine Manager 2016、Virtual Machine Manager 2012 R2 <br/><br/> **注:** これらのオペレーティング システムの Server コア インストールもサポートされています。  | Virtual Machine Manager を使用する場合は、Windows Server 2019 ホストは、Virtual Machine Manager 2019 で管理する必要があります。 同様に、Windows Server 2016 ホストは、Virtual Machine Manager 2016 によって管理されている必要があります。

> [!NOTE]
> オンプレミス サーバーに .NET Framework 4.6.2 以降が存在することを確認してください。

## <a name="replicated-vms"></a>レプリケートされた VM


次の表は、VM のサポートをまとめた一覧です。 Site Recovery は、サポートされているオペレーティング システム上で実行されているすべてのワークロードをサポートします。

 **コンポーネント** | **詳細**
--- | ---
VM 構成 | Azure にレプリケートする VM は、[Azure の要件](#azure-vm-requirements)を満たしている必要があります。
ゲスト オペレーティング システム | [Azure がサポートする](../cloud-services/cloud-services-guestos-update-matrix.md#family-5-releases)任意のゲスト OS。<br/><br/> Windows Server 2016 の Nano Server はサポートされていません。


## <a name="vmdisk-management"></a>VM/ディスク管理

**操作** | **詳細**
--- | ---
レプリケートされた Hyper-V VM のディスク サイズの変更 | サポートされていません。 レプリケーションを無効にし、変更を行った後、VM のレプリケーションを再び有効にします。
レプリケートされた Hyper-V VM でのディスクの追加 | サポートされていません。 レプリケーションを無効にし、変更を行った後、VM のレプリケーションを再び有効にします。

## <a name="hyper-v-network-configuration"></a>Hyper-V のネットワーク構成

**コンポーネント** | **Hyper-V (Virtual Machine Manager あり)** | **Hyper-V (Virtual Machine Manager なし)**
--- | --- | ---
ホスト ネットワーク: NIC チーミング | はい | はい
ホスト ネットワーク: VLAN | はい | ○
ホスト ネットワーク: IPv4 | はい | ○
ホスト ネットワーク: IPv6 | いいえ | ×
ゲスト VM ネットワーク: NIC チーミング | いいえ | いいえ
ゲスト VM ネットワーク: IPv4 | はい | ○
ゲスト VM ネットワーク: IPv6 | いいえ | [はい]
ゲスト VM ネットワーク: 静的 IP (Windows) | ○ | ○
ゲスト VM ネットワーク: 静的 IP (Linux) | × | いいえ
ゲスト VM ネットワーク: マルチ NIC | はい | はい
HTTPS プロキシ | いいえ | ×
Site Recovery サービスへの Private Link アクセス | はい。 [詳細については、こちらを参照してください](hybrid-how-to-enable-replication-private-endpoints.md)。 | はい。 [詳細については、こちらを参照してください](hybrid-how-to-enable-replication-private-endpoints.md)。




## <a name="azure-vm-network-configuration-after-failover"></a>Azure VM ネットワーク構成 (フェールオーバー後)

**コンポーネント** | **Hyper-V (Virtual Machine Manager あり)** | **Hyper-V (Virtual Machine Manager なし)**
--- | --- | ---
Azure ExpressRoute | はい | はい
ILB | はい | はい
ELB | はい | はい
Azure の Traffic Manager | はい | はい
マルチ NIC | はい | はい
予約済み IP | ○ | はい
IPv4 | ○ | ○
送信元 IP アドレスを保持する | ○ | ○
Azure 仮想ネットワーク サービス エンドポイント<br/> (Azure Storage ファイアウォールなし) | ○ | ○
高速ネットワーク | いいえ | ×


## <a name="hyper-v-host-storage"></a>Hyper-V ホスト ストレージ

**Storage** | **Hyper-V (Virtual Machine Manager あり)** | **Hyper-V (Virtual Machine Manager なし)**
--- | --- | --- 
NFS | N/A | N/A
SMB 3.0 | はい | ○
SAN (ISCSI) | ○ | ○
マルチパス (MPIO) 以下でテスト済み:<br></br> Microsoft DSM、EMC PowerPath 5.7 SP4、EMC PowerPath DSM for CLARiiON | ○ | はい

## <a name="hyper-v-vm-guest-storage"></a>Hyper-V VM ゲスト ストレージ

**Storage** | **Hyper-V (Virtual Machine Manager あり)** | **Hyper-V (Virtual Machine Manager なし)**
--- | --- | ---
VMDK | N/A | N/A
VHD/VHDX | はい | はい
Generation 2 VM | ○ | ○
EFI/UEFI<br></br>Azure 内の移行された VM は、自動的に BIOS ブート VM に変換されます。 VM では、Windows Server 2012 以降のみが実行されている必要があります。 OS ディスクには最大 5 つのパーティションが必要であり、OS ディスクのサイズは 300 GB 未満にする必要があります。| ○ | ○
共有クラスター ディスク | いいえ | いいえ
暗号化されたディスク | × | いいえ
NFS | N/A | N/A
SMB 3.0 | × | ×
RDM | NA | NA
1 TB より大きいディスク | はい、最大 4,095 GB | はい、最大 4,095 GB
ディスク:4K 論理および物理セクター | サポートされない: Gen 1/Gen 2 | サポートされない: Gen 1/Gen 2
ディスク:4K 論理および 512 バイトの物理セクター | はい |  ○
論理ボリューム管理 (LVM)。 LVM は、データ ディスクでのみサポートされています。 Azure からは OS ディスクが 1 つだけ提供されます。 | はい | はい
ストライピングされたディスクのボリューム > 1 TB | はい | はい
記憶域スペース | × | ×
ディスクのホット アド/削除 | × | ×
ディスクの除外 | ○ | はい
マルチパス (MPIO) | ○ | はい

## <a name="azure-storage"></a>Azure Storage

**コンポーネント** | **Hyper-V (Virtual Machine Manager あり)** | **Hyper-V (Virtual Machine Manager なし)**
--- | --- | ---
ローカル冗長ストレージ | ○ | ○
geo 冗長ストレージ | ○ | ○
読み取りアクセス geo 冗長ストレージ | はい | はい
クール ストレージ | × | ×
ホット ストレージ| × | いいえ
ブロック blob | いいえ | ×
保存時の暗号化 (SSE)| ○ | ○
保存時の暗号化 (CMK) <br></br> (マネージド ディスクへのフェールオーバーの場合のみ)| はい (PowerShell Az 3.3.0 モジュール以降を使用) | はい (PowerShell Az 3.3.0 モジュール以降を使用)
保存時の二重暗号化 <br></br> (マネージド ディスクへのフェールオーバーの場合のみ) <br></br> [Windows](../virtual-machines/windows/disk-encryption.md) および [Linux](../virtual-machines/linux/disk-encryption.md) でサポートされているリージョンの詳細について参照してください | はい (PowerShell Az 3.3.0 モジュール以降を使用) | はい (PowerShell Az 3.3.0 モジュール以降を使用)
Premium Storage | ○ | はい
Standard Storage | はい | ○
Import/Export サービス | × | いいえ
ファイアウォールが有効になっている Azure Storage アカウント | はい。 ターゲット ストレージとキャッシュの場合。 | はい。 ターゲット ストレージとキャッシュの場合。
ストレージ アカウントの変更 | いいえ。 レプリケーションを有効にすると、ターゲット Azure Storage アカウントは変更できません。 変更するには、ディザスター リカバリーを無効にしてから再び有効にします。 | いいえ
転送オプションのセキュリティ保護 | はい


## <a name="azure-compute-features"></a>Azure コンピューティング機能

**機能** | **Hyper-V (Virtual Machine Manager あり)** | **Hyper-V (Virtual Machine Manager なし)**
--- | --- | ---
可用性セット | ○ | ○
ハブ | はい | はい  
マネージド ディスク | はい、フェールオーバー用です。<br/><br/> マネージド ディスクのフェールバックはサポートされません。 | はい、フェールオーバー用です。<br/><br/> マネージド ディスクのフェールバックはサポートされません。

## <a name="azure-vm-requirements"></a>Azure VM の要件

Azure にレプリケートするオンプレミス VM は、この表にまとめられている Azure VM の要件を満たしている必要があります。

**コンポーネント** | **必要条件** | **詳細**
--- | --- | ---
ゲスト オペレーティング システム | Site Recovery では、[Azure でサポートされている](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc794868(v=ws.10))すべてのオペレーティング システムがサポートされます。  | サポートされていない場合、前提条件の確認は失敗します。
ゲスト オペレーティング システムのアーキテクチャ | 32 ビット (Windows Server 2008)/64 ビット | サポートされていない場合、前提条件の確認は失敗します。
オペレーティング システムのディスク サイズ | 第 1 世代の VM では最大 2,048 GB です。<br/><br/> 第 2 世代の VM では最大 300 GB です。  | サポートされていない場合、前提条件の確認は失敗します。
オペレーティング システムのディスク数 | 1 | サポートされていない場合、前提条件の確認は失敗します。
データ ディスク数 | 16 以下  | サポートされていない場合、前提条件の確認は失敗します。
データ ディスク VHD のサイズ | 最大 4,095 GB | サポートされていない場合、前提条件の確認は失敗します。
ネットワーク アダプター | 複数のアダプターがサポートされます。 |
共有 VHD | サポートされていません | サポートされていない場合、前提条件の確認は失敗します。
FC ディスク | サポートされていません | サポートされていない場合、前提条件の確認は失敗します。
ハード ディスク フォーマット | VHD <br/><br/> VHDX | Azure にフェールオーバーすると、Site Recovery によって自動的に VHDX が VHD に変換されます。 オンプレミスにフェールバックした場合、仮想マシンは引き続き VHDX 形式を使用します。
BitLocker | サポートされていません | VM のレプリケーションを有効にする前に、BitLocker を無効にする必要があります。
VM 名 | 1 ～ 63 文字で指定します。 名前に使用できるのは、英文字、数字、およびハイフンのみです。 VM 名の最初と最後は、文字か数字とする必要があります。 | Site Recovery の VM プロパティ値を更新します。
VM の種類 | 第 1 世代<br/><br/> 第 2 世代 -- Windows | OS ディスクの種類が基本 (VHDX としてフォーマットされている 1 つまたは 2 つのデータ ボリュームが含まれる) でディスク容量が 300 GB 未満の第 2 世代の VM はサポートされています。<br></br>第 2 世代の Linux VM はサポートされていません。 [詳細については、こちらを参照してください](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)。|

## <a name="recovery-services-vault-actions"></a>Recovery Services コンテナーのアクション

**操作** |  **Hyper-V (VMM あり)** | **Hyper-V (VMM なし)**
--- | --- | ---
リソース グループ間の資格情報コンテナーの移動<br/><br/> サブスクリプション内およびサブスクリプション間 | いいえ | ×
リソース グループ間でストレージ、ネットワーク、Azure VM を移動<br/><br/> サブスクリプション内およびサブスクリプション間 | いいえ | いいえ

> [!NOTE]
> Hyper-VM をオンプレミスから Azure にレプリケートするときに、ある特定の環境 (Hyper-V サイトまたは VMM を持つ Hyper-V のいずれか該当する方) からレプリケート先として使用できる AD テナントは 1 つだけです。


## <a name="provider-and-agent"></a>プロバイダーとエージェント

デプロイがこの記事の設定と互換性を持たせるには、最新のプロバイダーとエージェントのバージョンを実行してください。

**名前** | **説明** | **詳細**
--- | --- | --- 
Azure Site Recovery プロバイダー | オンプレミスのサーバーと Azure の間の通信を調整します <br/><br/> Hyper-V (Virtual Machine Manager あり): Virtual Machine Manager サーバーにインストールします<br/><br/> Hyper-V (Virtual Machine Manager なし): Hyper-V ホストにインストールします| 最新バージョン: 5.1.2700.1 (Azure portal から入手可能)<br/><br/> [最新の機能と修正](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
Microsoft Azure Recovery Services エージェント | Hyper-V VM と Azure の間のレプリケーションを調整します<br/><br/> オンプレミスの Hyper-V サーバーにインストールされます (Virtual Machine Manager の有無にかかわらず) | ポータルから入手可能な最新のエージェント






## <a name="next-steps"></a>次のステップ
オンプレミス Hyper-V VM のディザスター リカバリーのために [Azure を準備する](tutorial-prepare-azure.md)方法を学びます。
