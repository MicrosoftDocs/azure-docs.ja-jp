---
title: サポート マトリックス - Azure Site Recovery を使用したセカンダリ VMM サイトへの Hyper-V ディザスター リカバリー
description: Azure Site Recovery を使用した、セカンダリ サイトへの VMM クラウド内の Hyper-V VM のレプリケーションのサポートについてまとめます。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: af7baf413c9054ef3e5bf527851ac06c113cdce7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86131175"
---
# <a name="support-matrix-for-disaster-recovery-of-hyper-v-vms-to-a-secondary-site"></a>Hyper-V VM のセカンダリ サイトへのディザスター リカバリーのサポート マトリックス

この記事では、[Azure Site Recovery](site-recovery-overview.md) サービスを使用して、System Center Virtual Machine Manager (VMM) クラウドで管理されている Hyper-V VM をセカンダリ サイトにレプリケートするときに、サポートされるものをまとめます。 Hyper-V VM を Azure にレプリケートする場合は、[こちらのサポート マトリックス](hyper-v-azure-support-matrix.md)を確認してください。

> [!NOTE]
> Hyper-V ホストが VMM クラウドで管理されているときは、セカンダリ サイトにのみレプリケートできます。


## <a name="host-servers"></a>ホスト サーバー

**オペレーティング システム** | **詳細**
--- | ---
Windows Server 2012 R2 | サーバーでは、最新の更新プログラムが実行されている必要があります。
Windows Server 2016 |  Windows Server 2016 ホストと 2012 R2 ホストが混在する VMM 2016 クラウドは、現在サポートされていません。<br/><br/> System Center 2012 R2 VMM 2012 R2 から System Center 2016 にアップグレードされたデプロイは、現在サポートされていません。


## <a name="replicated-vm-support"></a>レプリケートされた VM のサポート

次の表は、Site Recovery 使用時にレプリケートされるマシンのオペレーティング システムのサポートをまとめたものです。 すべてのワークロードは、サポートされるオペレーティング システム上で実行できます。

**Windows バージョン** | **Hyper-V (VMM あり)**
--- | ---
Windows Server 2016 | Windows Server 2016 の [Hyper-V でサポートされている](/windows-server/virtualization/hyper-v/Supported-Windows-guest-operating-systems-for-Hyper-V-on-Windows)任意のゲスト オペレーティング システム 
Windows Server 2012 R2 | Windows Server 2012 R2 の [Hyper-V でサポートされている](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn792027%28v%3dws.11%29)任意のゲスト オペレーティング システム

## <a name="linux-machine-storage"></a>Linux マシンのストレージ

次のストレージを使用する Linux マシンのみでリプリケート可能です。

- ファイル システム (EXT3、ETX4、ReiserFS、XFS)。
- マルチパス ソフトウェア: デバイス マッパー。
- ボリューム マネージャー (LVM2)。
- HP CCISS コントローラー ストレージを使用する物理サーバーはサポートされていません。
- ReiserFS ファイル システムは、SUSE Linux Enterprise Server 11 SP3 でのみサポートされています。

## <a name="network-configuration---hostguest-vm"></a>ネットワークの構成 - ホスト/ゲスト VM

**Configuration** | **サポートされています**  
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


## <a name="storage"></a>ストレージ

### <a name="host-storage"></a>ホスト ストレージ

**ストレージ (ホスト)** | **サポートされています**
--- | --- 
NFS | 該当なし
SMB 3.0 |  はい
SAN (ISCSI) | はい
マルチパス (MPIO) | はい

### <a name="guest-or-physical-server-storage"></a>ゲストまたは物理サーバーのストレージ

**Configuration** | **サポートされています**
--- | --- | 
VMDK |  該当なし
VHD/VHDX | はい (最大 16 個のディスク)
第 2 世代 VM | はい
共有クラスター ディスク | いいえ
暗号化されたディスク | いいえ
UEFI| 該当なし
NFS | いいえ
SMB 3.0 | いいえ
RDM | 該当なし
1 TB より大きいディスク | はい
ストライピングされたディスクのボリューム > 1 TB<br/><br/> LVM | はい
記憶域スペース | はい
ディスクのホット アド/削除 | いいえ
ディスクの除外 | はい
マルチパス (MPIO) | はい

## <a name="vaults"></a>資格情報コンテナー

**操作** | **サポートされています**
--- | --- 
リソース グループ間 (サブスクリプション内またはサブスクリプション間) での資格情報コンテナーの移動 |  いいえ
リソース グループ間 (サブスクリプション内またはサブスクリプション間) でのストレージ、ネットワーク、Azure VM の移動 | いいえ

## <a name="azure-site-recovery-provider"></a>Azure Site Recovery プロバイダー

プロバイダーは VMM サーバー間の通信を調整します。 

**最新** | **更新プログラム**
--- | --- 
5.1.19 ([ポータルから入手可能](https://aka.ms/downloaddra) | [最新の機能と修正](https://support.microsoft.com/kb/3155002)



## <a name="next-steps"></a>次のステップ

[VMM クラウドの Hyper-V VM をセカンダリ サイトにレプリケートする](./hyper-v-vmm-disaster-recovery.md)
