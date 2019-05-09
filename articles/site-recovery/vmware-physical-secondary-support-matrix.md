---
title: Azure Site Recovery を使用した VMware VM または物理サーバーのセカンダリ VMware サイトへのディザスター リカバリーのサポート マトリックス | Microsoft Docs
description: Azure Site Recovery を使用して VMware VM および物理サーバーをセカンダリ サイトにディザスター リカバリーする場合のサポートについてまとめています。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: raynew
ms.openlocfilehash: b316d6a8293d9f23eb89e8b6fffedac316759df4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59360145"
---
# <a name="support-matrix-for-disaster-recovery-of-vmware-vms-and-physical-servers-to-a-secondary-site"></a>VMware VM および物理サーバーのセカンダリ サイトへのディザスター リカバリーのサポート マトリックス

この記事では、VMware VM または Windows/Linux 物理サーバーをセカンダリ VMware サイトにディザスター リカバリーするために、[Azure Site Recovery](site-recovery-overview.md) サービスを使用する際のサポートについてまとめています。

- VMware VM または物理サーバーを Azure にレプリケートする場合は、[このサポート マトリックス](vmware-physical-azure-support-matrix.md)を確認してください。
- Hyper-V VM をセカンダリ サイトにレプリケートする場合は、[このサポート マトリックス](hyper-v-azure-support-matrix.md)を確認してください。

> [!NOTE]
> オンプレミスの VMware VM と物理サーバーのレプリケーションは、InMage Scout によって提供されます。 InMage Scout は、Azure Site Recovery サービスのサブスクリプションに含まれています。


## <a name="host-servers"></a>ホスト サーバー

**オペレーティング システム** | **詳細**
--- | ---
vCenter サーバー | vCenter 5.5、6.0、6.5<br/><br/> 6.0 または 6.5 を実行する場合、5.5 機能のみがサポートされることに注意してください。


## <a name="replicated-vm-support"></a>レプリケートされた VM のサポート

次の表は、Site Recovery 使用時にレプリケートされるマシンのオペレーティング システムのサポートをまとめたものです。 すべてのワークロードは、サポートされるオペレーティング システム上で実行できます。

**オペレーティング システム** | **詳細**
--- | ---
Windows Server | 64 ビット Windows Server 2016、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1 以降。
Linux | Red Hat Enterprise Linux 6.7、6.8、6.9、7.1、7.2 <br/><br/> Centos 6.5、6.6、6.7、6.8、6.9、7.0、7.1、7.2 <br/><br/> Red Hat 互換カーネルまたは Unbreakable Enterprise Kernel リリース 3 (UEK3) を実行している Oracle Enterprise Linux 6.4、6.5、6.8 <br/><br/> SUSE Linux Enterprise Server 11 SP3、11 SP4 


## <a name="linux-machine-storage"></a>Linux マシンのストレージ

次のストレージを使用する Linux マシンのみでリプリケート可能です。

- ファイル システム (EXT3、ETX4、ReiserFS、XFS)。
- マルチパス ソフトウェア: デバイス マッパー。
- ボリューム マネージャー (LVM2)。
- HP CCISS コントローラー ストレージを使用する物理サーバーはサポートされていません。
- ReiserFS ファイル システムは、SUSE Linux Enterprise Server 11 SP3 でのみサポートされています。

## <a name="network-configuration---hostguest-vm"></a>ネットワークの構成 - ホスト/ゲスト VM

**構成** | **サポートされています**  
--- | --- 
ホスト - NIC チーミング | はい 
ホスト - VLAN | はい 
ホスト - IPv4 | はい 
ホスト - IPv6 | いいえ  
ゲスト VM - NIC チーミング | いいえ 
ゲスト VM - IPv4 | はい
ゲスト VM - IPv6 | いいえ 
Guest VM - Windows/Linux - 静的 IP アドレス | はい
ゲスト VM - マルチ NIC | はい


## <a name="storage"></a>Storage

### <a name="host-storage"></a>ホスト ストレージ

**ストレージ (ホスト)** | **サポートされています** 
--- | --- 
NFS | はい 
SMB 3.0 | 該当なし 
SAN (ISCSI) | はい 
マルチパス (MPIO) | はい 

### <a name="guest-or-physical-server-storage"></a>ゲストまたは物理サーバーのストレージ

**構成** | **サポートされています** 
--- | --- 
VMDK | はい 
VHD/VHDX | 該当なし 
第 2 世代 VM | 該当なし 
共有クラスター ディスク | はい 
暗号化されたディスク | いいえ  
UEFI| はい 
NFS | いいえ  
SMB 3.0 | いいえ  
RDM | はい 
1 TB より大きいディスク | はい 
ストライピングされたディスクのボリューム > 1 TB<br/><br/> LVM | はい 
記憶域 | いいえ  
ディスクのホット アド/削除 | はい 
ディスクの除外 | はい 
マルチパス (MPIO) | 該当なし 

## <a name="vaults"></a>資格情報コンテナー

**アクション** | **サポートされています** 
--- | --- 
リソース グループ間 (サブスクリプション内またはサブスクリプション間) での資格情報コンテナーの移動 | いいえ  
リソース グループ間 (サブスクリプション内またはサブスクリプション間) でのストレージ、ネットワーク、Azure VM の移動 | いいえ  

## <a name="mobility-service-and-updates"></a>モビリティ サービスと更新プログラム

モビリティ サービスは、オンプレミスの VMware サーバー/物理サーバーとセカンダリ サイトの間のレプリケーションを調整します。 レプリケーションを設定するときは、モビリティ サービスとその他のコンポーネントが最新バージョンになっていることを確認する必要があります。

| **Update** | **詳細** |
| --- | --- |
|Scout 更新プログラム | Scout 更新プログラムは累積的です。 <br/><br/> 最新の Scout 更新プログラムの[詳細とダウンロード](vmware-physical-secondary-disaster-recovery.md#updates) |
|コンポーネント更新プログラム | Scout 更新プログラムには、RX サーバー、構成サーバー、プロセス サーバーとマスター ターゲット サーバー、vContinuum サーバー、および保護するソース サーバーを含む、すべてのコンポーネントの更新プログラムが含まれます。<br/><br/> [詳細情報](vmware-physical-secondary-disaster-recovery.md#download-and-install-component-updates)。|


## <a name="next-steps"></a>次の手順

[InMage Scout ユーザー ガイド](https://aka.ms/asr-scout-user-guide) をダウンロードする

- [VMM クラウドの Hyper-V VM をセカンダリ サイトにレプリケートする](tutorial-vmm-to-vmm.md)
- [VMware VM と物理サーバーをセカンダリ サイトにレプリケートする](tutorial-vmware-to-vmware.md)
