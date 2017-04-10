---
title: "Azure Site Recovery によるセカンダリ サイトへのレプリケーションのサポート マトリックス | Microsoft Docs"
description: "Azure Site Recovery でサポートされているオペレーティング システムとコンポーネントの概要について説明します"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/08/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 2541236d84100ed7889d06f9b0580fcbc55ecfdb
ms.openlocfilehash: f9443b633601272c79739c92995d53ba1a7d2b4e


---
# <a name="support-matrix-for-replication-to-a-secondary-site-with-azure-site-recovery"></a>Azure Site Recovery によるセカンダリ サイトへのレプリケーションのサポート マトリックス

> [!div class="op_single_selector"]
> * [Azure へのレプリケート](site-recovery-support-matrix-to-azure.md)
> * [オンプレミスの保存先へのレプリケート](site-recovery-support-matrix-to-sec-site.md)

この記事では、Azure Site Recovery を使用してセカンダリのオンプレミス サイトにレプリケートする際のサポートについて説明します。

## <a name="deployment-options"></a>デプロイ オプション

**デプロイ** | **VMware/物理サーバー** | **Hyper-V (VMM なし)** | **Hyper-V (VMM あり)**
--- | --- | --- | ---
**Azure Portal** | オンプレミスの VMware VM をセカンダリ VMware サイトにレプリケートします。<br/><br/> [InMage Scout ユーザー ガイド](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf)をダウンロードします (Azure Portal では入手できません)。 | サポートされていません | VMM クラウドのオンプレミスの Hyper-V VM をセカンダリ VMM クラウドにレプリケートします。<br/><br/> 標準の Hyper-V レプリケーションのみ。 SAN はサポートされていません。
**クラシック ポータル** | メンテナンス モードのみ。 新しい資格情報コンテナーを作成することはできません。 | サポートされていません | メンテナンス モードのみ
**PowerShell** | サポートされていません | サポートされていません | サポートされています

## <a name="on-premises-servers"></a>オンプレミスのサーバー

### <a name="virtualization-servers"></a>仮想化サーバー

**デプロイ** | **サポート**
--- | ---
**VMware VM/物理サーバー** | vSphere 6.0、5.5、または 5.1 (最新の更新プログラムをインストール済み)
**Hyper-V (VMM あり)** | VMM 2016 と VMM 2012 R2

  >[!Note]
  > Windows Server 2016 ホストと 2012 R2 ホストが混在する VMM 2016 クラウドは、現在サポートされていません。

### <a name="host-servers"></a>ホスト サーバー

**デプロイ** | **サポート**
--- | ---
**VMware VM/物理サーバー** | vCenter 5.5 または 6.0 (5.5 の機能のみをサポート) 
**Hyper-V (VMM なし)** | セカンダリ サイトへのレプリケートの場合は、サポートされる構成ではありません
**Hyper-V (VMM あり)** | Windows Server 2016、最新の更新プログラムが適用された Windows Server 2012 R2。<br/><br/> Windows Server 2016 ホストは VMM 2016 によって管理する必要があります。

## <a name="support-for-replicated-machine-os-versions"></a>レプリケートされるマシンの OS バージョンのサポート
次の表は、Azure Site Recovery 使用時に発生する可能性のある、さまざまなデプロイ シナリオにおけるオペレーティング システムのサポートをまとめたものです。 このサポートは、示された OS で実行される任意のワークロードに適用可能です。

**VMware/物理サーバー** | **Hyper-V (VMM あり)**
--- | --- | ---
64 ビット Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1 以降<br/><br/> Red Hat Enterprise Linux 6.7、7.1、7.2 <br/><br/> CentOS 6.5、6.6、6.7、7.0、7.1、7.2 <br/><br/> Red Hat 互換カーネルまたは Unbreakable Enterprise Kernel リリース 3 (UEK3) を実行している Oracle Enterprise Linux 6.4、6.5 <br/><br/> SUSE Linux Enterprise Server 11 SP3 | [Hyper-V でサポートされている](https://technet.microsoft.com/library/mt126277.aspx)任意のゲスト オペレーティング システム

>[!Note]
>ファイル システム (EXT3、ETX4、ReiserFS、XFS)、マルチパス ソフトウェア デバイス マッパー、ボリューム マネージャー (LVM2) の各ストレージを使用する Linux マシンでのみレプリケート可能です。
>HP CCISS コントローラー ストレージを使用する物理サーバーはサポートされていません。
>ReiserFS ファイル システムは、SUSE Linux Enterprise Server 11 SP3 でのみサポートされています。

## <a name="network-configuration"></a>ネットワーク構成

### <a name="hosts"></a>ホスト

**構成** | **VMware/物理サーバー** | **Hyper-V (VMM あり)**
--- | --- | ---
NIC チーミング | あり | はい
VLAN | あり | あり
IPv4 | はい | はい
IPv6 | なし | いいえ

### <a name="guest-vms"></a>ゲスト VM

**構成** | **VMware/物理サーバー** | **Hyper-V (VMM あり)**
--- | --- | ---
NIC チーミング | なし | なし
IPv4 | はい | はい
IPv6 | なし | なし
静的 IP (Windows) | あり | あり
静的 IP (Linux) | はい | はい
マルチ NIC | はい | はい


## <a name="storage"></a>Storage

### <a name="host-storage"></a>ホスト ストレージ

**ストレージ (ホスト)** | **VMware/物理サーバー** | **Hyper-V (VMM あり)**
--- | --- | ---
NFS | はい | 該当なし
SMB 3.0 | 該当なし | はい
SAN (ISCSI) | あり | はい
マルチパス (MPIO) | あり | はい

### <a name="guest-or-physical-server-storage"></a>ゲストまたは物理サーバーのストレージ

**構成** | **VMware/物理サーバー** | **Hyper-V (VMM あり)**
--- | --- | ---
VMDK | はい | 該当なし
VHD/VHDX | 該当なし | はい (最大 16 個のディスク)
第 2 世代 VM | 該当なし | はい
共有クラスター ディスク | はい  | なし
暗号化されたディスク | いいえ | いいえ
UEFI| なし | 該当なし
NFS | なし | いいえ
SMB 3.0 | なし | なし
RDM | はい | 該当なし
1 TB より大きいディスク | なし | はい
ストライピングされたディスクのボリューム > 1 TB<br/><br/> LVM | はい | はい
記憶域 | なし | あり
ディスクのホット アド/削除 | なし | いいえ
ディスクの除外 | なし | なし
マルチパス (MPIO) | 該当なし | はい

## <a name="vaults"></a>資格情報コンテナー

**アクション** | **VMware/物理サーバー** | **Hyper-V (VMM あり)**
--- | --- | ---
リソース グループ間 (サブスクリプション内またはサブスクリプション間) での資格情報コンテナーの移動 | いいえ | いいえ
リソース グループ間 (サブスクリプション内またはサブスクリプション間) でのストレージ、ネットワーク、Azure VM の移動 | なし | いいえ

## <a name="provider-and-agent"></a>プロバイダーとエージェント

**名前** | **説明** | **最新バージョン** | **詳細**
--- | --- | --- | --- | ---
**Azure Site Recovery プロバイダー** | オンプレミスのサーバーと Azure の間の通信を調整します <br/><br/> オンプレミス VMM サーバー、または Hyper-V サーバー (VMM サーバーがない場合) にインストールされます | 5.1.19 ([ポータルから使用可能](http://aka.ms/downloaddra)) | [最新の機能と修正](https://support.microsoft.com/kb/3155002)
**モビリティ サービス** | オンプレミスの VMware サーバー/物理サーバーとセカンダリ サイトの間のレプリケーションを調整します<br/><br/> レプリケートする VMware VM または物理サーバーにインストールされます  | 該当なし (ポータルから使用可能) | 該当なし


## <a name="next-steps"></a>次のステップ

[デプロイメントの前提条件](site-recovery-prereq.md)を確認してください。



<!--HONumber=Feb17_HO2-->


