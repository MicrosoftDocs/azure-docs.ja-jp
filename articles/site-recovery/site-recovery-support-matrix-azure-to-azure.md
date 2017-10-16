---
title: "Azure 間でのレプリケートに関する Azure Site Recovery のサポート マトリックス | Microsoft Docs"
description: "ディザスター リカバリー (DR) のニーズに応じて、リージョン間で Azure 仮想マシン (VM) を Azure Site Recovery でレプリケートするときのサポートされるオペレーティング システムと構成についてまとめます。"
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/31/2017
ms.author: sujayt
ms.openlocfilehash: f52520f88d36914d4cad7c8aea4fb21e44b3cc9d
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-site-recovery-support-matrix-for-replicating-from-azure-to-azure"></a>Azure 間でのレプリケートに関する Azure Site Recovery のサポート マトリックス


>[!NOTE]
>
> Azure 仮想マシンの Site Recovery レプリケーションは現在プレビューの段階です。

この記事では、リージョン間で Azure 仮想マシンをレプリケートおよび復旧するときの、Azure Site Recovery のサポートされる構成とコンポーネントをまとめます。

## <a name="user-interface-options"></a>ユーザー インターフェイスのオプション

**ユーザー インターフェイス** |  **サポートされるかどうか**
--- | ---
**Azure ポータル** | サポートされています
**クラシック ポータル** | サポートされていません
**PowerShell** | 現在、サポートされていません
**REST API** | 現在、サポートされていません
**CLI** | 現在、サポートされていません


## <a name="resource-move-support"></a>リソースの移動のサポート

**リソースの移動の種類** | **サポートされるかどうか** | **解説**  
--- | --- | ---
**リソース グループ間の資格情報コンテナーの移動** | サポートされていません |リソース グループ間で Recovery Services コンテナーを移動できません。
**リソース グループ間のコンピューティング、ストレージ、およびネットワークの移動** | サポートされていません |レプリケーションを有効にした後、仮想マシン (またはストレージ、ネットワークなどの関連するコンポーネント) を移動する場合は、レプリケーションを無効にしてから、仮想マシンのレプリケーションを再度有効にする必要があります。



## <a name="support-for-deployment-models"></a>デプロイ モデルのサポート

**デプロイ モデル** | **サポートされるかどうか** | **解説**  
--- | --- | ---
**クラシック** | サポートされています | クラシック仮想マシンをレプリケートした場合は、クラシック仮想マシンとしてのみ復旧できます。 その仮想マシンを、Resource Manager 仮想マシンとして復旧することはできません。 仮想ネットワークを使用しないで、Azure リージョンに直接クラシック VM をデプロイする場合は、サポートされていません。
**Resource Manager** | サポートされています |

>[!NOTE]
>
> 1. ディザスター リカバリー シナリオで、あるサブスクリプションから別のサブスクリプションに Azure 仮想マシンをレプリケートする処理はサポートされていません。
> 2. サブスクリプション全体の Azure 仮想マシンの移行はサポートされていません。
> 3. 同じリージョン内の Azure 仮想マシンの移行はサポートされていません。
> 4. クラシック デプロイ モデルから Resource Manager デプロイ モデルへの Azure 仮想マシンの移行はサポートされていません。

## <a name="support-for-replicated-machine-os-versions"></a>レプリケートされるマシンの OS バージョンのサポート

次のサポートは、ここで示す OS で実行される任意のワークロードに適用可能です。

#### <a name="windows"></a>Windows

- Windows Server 2016 (Server Core およびデスクトップ エクスペリエンス搭載サーバー)*
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1 以降

>[!NOTE]
>
> \* Windows Server 2016 の Nano Server はサポートされていません。

#### <a name="linux"></a>Linux

- Red Hat Enterprise Linux 6.7、6.8、6.9、7.0、7.1、7.2、7.3
- CentOS 6.5、6.6、6.7、6.8、6.9、7.0、7.1、7.2、7.3
- Ubuntu 14.04 LTS Server[ (サポートされるカーネルのバージョン)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Ubuntu 16.04 LTS Server[ (サポートされるカーネルのバージョン)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Debian 7
- Debian 8
- Red Hat 互換カーネルまたは Unbreakable Enterprise Kernel リリース 3 (UEK3) を実行している Oracle Enterprise Linux 6.4、6.5
- SUSE Linux Enterprise Server 11 SP3
- SUSE Linux Enterprise Server 11 SP4

(レプリケートするマシンの SLES 11 SP3 から SLES 11 SP4 へのアップグレードはサポートされていません。 レプリケートされるマシンが SLES 11SP3 から SLES 11 SP4 にアップグレードされた場合は、アップグレード後にレプリケーションを無効にし、再度マシンを保護する必要があります。)

>[!NOTE]
>
> パスワード ベースの認証とログインを使用しており、cloud-init パッケージを使用してクラウド仮想マシンを構成する Ubuntu サーバーでは、(cloudinit 構成に応じて) フェールオーバー時にパスワード ベースのログインが無効になっている場合があります。パスワード ベースのログインは、Azure ポータルでフェールオーバーされた仮想マシンの設定メニュー ([サポート + トラブルシューティング] セクションにある) からパスワードをリセットして、仮想マシンで再度有効にすることができます。

### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Azure 仮想マシン用のサポートされる Ubuntu カーネル バージョン

**リリース** | **モビリティ サービス バージョン** | **カーネル バージョン** |
--- | --- | --- |
14.04 LTS | 9.9 | 3.13.0-24-generic ～ 3.13.0-117-generic、<br/>3.16.0-25-generic ～ 3.16.0-77-generic、<br/>3.19.0-18-generic ～ 3.19.0-80-generic、<br/>4.2.0-18-generic ～ 4.2.0-42-generic、<br/>4.4.0-21-generic ～ 4.4.0-75-generic |
14.04 LTS | 9.10 | 3.13.0-24-generic ～ 3.13.0-121-generic、<br/>3.16.0-25-generic ～ 3.16.0-77-generic、<br/>3.19.0-18-generic ～ 3.19.0-80-generic、<br/>4.2.0-18-generic ～ 4.2.0-42-generic、<br/>4.4.0-21-generic ～ 4.4.0-81-generic |
14.04 LTS | 9.11 | 3.13.0-24-generic ～ 3.13.0-125-generic、<br/>3.16.0-25-generic ～ 3.16.0-77-generic、<br/>3.19.0-18-generic ～ 3.19.0-80-generic、<br/>4.2.0-18-generic ～ 4.2.0-42-generic、<br/>4.4.0-21-generic ～ 4.4.0-83-generic、 |
16.04 LTS | 9.10 | 4.4.0-21-generic ～ 4.4.0-81-generic、<br/>4.8.0-34-generic ～ 4.8.0-56-generic、<br/>4.10.0-14-generic ～ 4.10.0-24-generic |
16.04 LTS | 9.11 | 4.4.0-21-generic ～ 4.4.0-83-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-27-generic |

## <a name="supported-file-systems-and-guest-storage-configurations-on-azure-virtual-machines-running-linux-os"></a>Linux OS を実行している Azure 仮想マシンでサポートされるファイル システムおよびゲスト ストレージ構成

* ファイル システム: ext3、ext4、ReiserFS (Suse Linux Enterprise Server のみ)、XFS
* ボリューム マネージャー: LVM2
* マルチパス ソフトウェア: デバイス マッパー

## <a name="region-support"></a>リージョンのサポート

同じ地理クラスター内の 2 つのリージョン間で VM をレプリケートして、復旧できます。

**地理クラスター** | **Azure リージョン**
-- | --
アメリカ | カナダ東部、カナダ中部、米国中南部、米国中西部、米国東部、米国東部 2、米国西部、米国西部 2、米国中部、米国中北部
ヨーロッパ | 英国西部、英国南部、北ヨーロッパ、西ヨーロッパ
アジア | インド南部、インド中部、東南アジア、東アジア、東日本、西日本、韓国中部、韓国南部
オーストラリア   | オーストラリア東部、オーストラリア南東部

>[!NOTE]
>
> ブラジル南部リージョンについては、米国中南部、米国中西部、米国東部、米国東部 2、米国西部、米国西部 2、米国中北部のいずれかのリージョンに対してのみレプリケートとフェールオーバー、およびフェールバックが可能です。


## <a name="support-for-compute-configuration"></a>コンピューティング構成のサポート

**構成** | **サポートされるかどうか** | **解説**
--- | --- | ---
サイズ | 少なくとも 2 つの CPU コアと 1 GB の RAM を備えた任意の Azure VM サイズ | [Azure 仮想マシンのサイズ](../virtual-machines/windows/sizes.md)に関するページをご覧ください
可用性セット | サポートされています | ポータルで既定のオプションを使用して "レプリケーションの有効化" 手順を実行する場合、可用性セットは、ソース リージョンの構成に基づいて自動的に作成されます。 ターゲットの可用性セットは、[レプリケートされたアイテム] > [設定] > [コンピューティングとネットワーク] > [可用性セット] でいつでも変更できます。
Hybrid Use Benefit (HUB) VM | サポートされています | ソース VM で HUB ライセンスが有効になっている場合、テスト フェールオーバーまたはフェールオーバー VM でも HUB ライセンスが使用されます。
仮想マシン スケール セット | サポートされていません |
Azure ギャラリー イメージ - Microsoft が公開 | サポートされています | Site Recovery によってサポートされるオペレーティング システムで VM が実行されている場合に、サポートされます
Azure ギャラリー イメージ - サード パーティが公開 | サポートされています | Site Recovery によってサポートされるオペレーティング システムで VM が実行されている場合に、サポートされます。
カスタム イメージ - サード パーティが公開 | サポートされています | Site Recovery によってサポートされるオペレーティング システムで VM が実行されている場合に、サポートされます。
Site Recovery を使用して移行された VM | サポートされています | Site Recovery を使用して Azure に移行された VMware/物理マシンの場合は、古いバージョンのモビリティ サービスをアンインストールし、マシンを再起動してから、他の Azure リージョンにレプリケートする必要があります。

## <a name="support-for-storage-configuration"></a>ストレージ構成のサポート

**構成** | **サポートされるかどうか** | **解説**
--- | --- | ---
OS ディスクの最大サイズ | 1023 GB | ｢[VM で使用されるディスク](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)」を参照してください。
データ ディスクの最大サイズ | 1023 GB | ｢[VM で使用されるディスク](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)」を参照してください。
データ ディスクの数 | 特定の Azure VM サイズでサポートされている最大数 64 | [Azure 仮想マシンのサイズ](../virtual-machines/windows/sizes.md)に関するページをご覧ください
一時ディスク | 常にレプリケーションから除外 | 一時ディスクは常にレプリケーションから除外されます。 Azure ガイダンスに従って、一時ディスクには永続データを配置しないでください。 詳細については、[Azure VM の一時ディスク](../virtual-machines/windows/about-disks-and-vhds.md#temporary-disk)に関する記事をご覧ください。
ディスク上のデータの変更率 | ディスクあたり最大 6 MBps | ディスク上の平均データ変更率が継続的に 6 MBps を超えると、レプリケーションが追いつきません。 ただし、データの急激な増加が時折しか発生せず、データ変更率が一時的に 6 MBps を超えてから低下する場合は、レプリケーションは追いつきます。 この場合、復旧ポイントは、少し後ろにずれることがあります。
Standard Storage アカウントのディスク | サポートされています |
Premium Storage アカウントのディスク | サポートされています | VM のディスクが Premium Storage アカウントと Standard Storage ストレージ アカウントに分散している場合は、ディスクごとに異なるターゲット ストレージ アカウントを選択して、ターゲット リージョンのストレージ構成を確実に同じできます。
Standard 管理ディスク | サポートされていません |  
Premium 管理ディスク | サポートされていません |
記憶域スペース | サポートされています |         
保存時の暗号化 (SSE) | サポートされています | キャッシュおよびターゲット ストレージ アカウントごとに、SSE 対応ストレージ アカウントを選択できます。     
Azure Disk Encryption (ADE) | サポートされていません |
ディスクのホット アド/削除 | サポートされていません | VM 上でデータ ディスクを追加または削除する場合は、レプリケーションを無効にしてから、もう一度 VM に対してレプリケーションを有効にする必要があります。
ディスクの除外 | サポートされていません|   一時ディスクは既定で除外されます。
LRS | サポートされています |
GRS | サポートされています |
RA-GRS | サポートされています |
ZRS | サポートされていません |  
クールおよびホット ストレージ | サポートされていません | 仮想マシン ディスクは、クールおよびホット ストレージではサポートされません

>[!IMPORTANT]
> ご使用のソース Azure 仮想マシンでパフォーマンスの問題が発生しないように、必ず[ストレージのガイダンス](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks)に従ってください。 既定の設定に従うと、ソースの構成に基づいて、必要なストレージ アカウントが Site Recovery によって作成されます。 設定をカスタマイズし、独自の設定を選択する場合は、ソース VM として必ず (../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) に従ってください。

## <a name="support-for-network-configuration"></a>ネットワーク構成のサポート
**構成** | **サポートされるかどうか** | **解説**
--- | --- | ---
ネットワーク インターフェイス (NIC) | 特定の Azure VM サイズでサポートされている NIC の最大数 | テスト フェールオーバーまたはフェールオーバー操作の一環として VM が作成されるときに、NIC が作成されます。 フェールオーバー VM 上の NIC 数は、レプリケーションを有効にしたときの、ソース VM の NIC 数によって決まります。 レプリケーションを有効にした後に NIC を追加/削除しても、フェールオーバー VM の NIC 数には影響しません。
インターネット Load Balancer | サポートされています | 復旧計画の Azure 自動化スクリプトを使用して、構成済みロード バランサーを関連付ける必要があります。
内部ロード バランサー | サポートされています | 復旧計画の Azure 自動化スクリプトを使用して、構成済みロード バランサーを関連付ける必要があります。
パブリック IP| サポートされています | 復旧計画の Azure 自動化スクリプトを使用して、既存のパブリック IP を NIC に関連付けるか、パブリック IP を作成して NIC に関連付ける必要があります。
NIC の NSG (Resource Manager)| サポートされています | 復旧計画の Azure 自動化スクリプトを使用して、NSG を NIC に関連付ける必要があります。  
サブネットの NSG (Resource Manager とクラシック)| サポートされています | 復旧計画の Azure 自動化スクリプトを使用して、NSG を NIC に関連付ける必要があります。
VM の NSG (クラシック)| サポートされています | 復旧計画の Azure 自動化スクリプトを使用して、NSG を NIC に関連付ける必要があります。
予約済み IP (静的 IP)/発信元 IP を保持 | サポートされています | ソース VM の NIC の IP 構成が静的で、ターゲット サブネットで同じ IP を使用できる場合、その IP は、フェールオーバー VM に割り当てられます。 ターゲット サブネットで同じ IP を使用できない場合は、そのサブネット内の使用可能な IP の 1 つがこの VM 用に予約されます。 任意の固定 IP は、[レプリケートされたアイテム] > [設定] > [コンピューティングとネットワーク] > [ネットワーク インターフェイス] で指定できます。 NIC を選択し、任意のサブネットと IP を指定できます。
動的 IP| サポートされています | ソース VM の NIC の IP 構成が動的である場合は、フェールオーバー VM の NIC も既定で動的になります。 任意の固定 IP は、[レプリケートされたアイテム] > [設定] > [コンピューティングとネットワーク] > [ネットワーク インターフェイス] で指定できます。 NIC を選択し、任意のサブネットと IP を指定できます。
Traffic Manager 統合 | サポートされています | Traffic Manager は、トラフィックがソース リージョンのエンドポイントに定期的にルーティングされるように、また、ターゲット リージョンのエンドポイントにフェールオーバー時にルーティングされるように、事前に構成できます。
Azure 管理 DNS | サポートされています |
[カスタム DNS]  | サポートされています |    
非認証プロキシ | サポートされています | [ネットワーク ガイダンスのドキュメント](site-recovery-azure-to-azure-networking-guidance.md)を参照してください。    
認証済みプロキシ | サポートされていません | VM が送信接続に認証済みプロキシを使用している場合は、Azure Site Recovery でレプリケートできません。    
オンプレミスでのサイト間 VPN (ExpressRoute あり/なし)| サポートされています | Site Recovery トラフィックがオンプレミスにルーティングされないように、UDR と NSG が構成されていることを確認します。 [ネットワーク ガイダンスのドキュメント](site-recovery-azure-to-azure-networking-guidance.md)を参照してください。  
VNet 間接続 | サポートされています | [ネットワーク ガイダンスのドキュメント](site-recovery-azure-to-azure-networking-guidance.md)を参照してください。  


## <a name="next-steps"></a>次のステップ
- [Azure VM のレプリケートに関するネットワーク ガイダンス](site-recovery-azure-to-azure-networking-guidance.md)の詳細を確認する
- [Azure VM をレプリケート](site-recovery-azure-to-azure.md)してワークロードの保護を開始する
