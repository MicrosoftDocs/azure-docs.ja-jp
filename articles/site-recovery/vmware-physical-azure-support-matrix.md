---
title: Azure Site Recovery を使用した VMware VM および物理サーバーの Azure へのディザスター リカバリーに関するサポート マトリックス | Microsoft Docs
description: Azure Site Recovery を使用して VMware VM および物理サーバーを Azure にディザスター リカバリーする処理をサポートしているオペレーティング システムとコンポーネントの概要について説明します。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: raynew
ms.openlocfilehash: 199f9508b599e2f946404446a23e9608bb969ba7
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58649460"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>VMware VM および物理サーバーの Azure へのディザスター リカバリーのサポート マトリックス

この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用して、VMware VM の Azure へのディザスター リカバリーを行う場合にサポートされるコンポーネントと設定の概要について説明します。

最も簡単なデプロイ シナリオで Azure Site Recovery の使用を開始するには、こちらの[チュートリアル](tutorial-prepare-azure.md)を参照してください。 Azure Site Recovery アーキテクチャの詳細については、[こちら](vmware-azure-architecture.md)をご覧ください。

## <a name="replication-scenario"></a>レプリケーション シナリオ

**シナリオ** | **詳細**
--- | ---
VMware VM | オンプレミス VMware VM の Azure へのレプリケーション。 このシナリオは、Azure portal または [PowerShell](vmware-azure-disaster-recovery-powershell.md) を使用してデプロイできます。
物理サーバー | オンプレミスの Windows または Linux の物理サーバーから Azure へのレプリケーション。 このシナリオは、Azure Portal で展開できます。

## <a name="on-premises-virtualization-servers"></a>オンプレミスの仮想化サーバー

**サーバー** | **要件** | **詳細**
--- | --- | ---
VMware | vCenter Server 6.7、6.5、6.0、5.5、または vSphere 6.7、6.5、6.0、5.5 | vCenter サーバーを使用することをお勧めします。<br/><br/> vSphere ホストと vCenter サーバーはプロセス サーバーと同じネットワーク内に存在することが推奨されます。 既定では、プロセス サーバー コンポーネントは構成サーバーで実行されるため、専用のプロセス サーバーを設定していなければ、これがその中に構成サーバーを設定するネットワークになります。
物理 | 該当なし

## <a name="site-recovery-configuration-server"></a>Site Recovery 構成サーバー

構成サーバーはオンプレミスのマシンで、構成サーバー、プロセス サーバー、マスター ターゲット サーバーを含む Site Recovery のコンポーネントを実行します。 VMware のレプリケーションの場合は、VMware VM を作成する OVF テンプレートを使用して、すべての要件を含む構成サーバーを設定します。 物理サーバーのレプリケーションの場合は、構成サーバーのマシンを手動で設定します。

**コンポーネント** | **要件**
--- |---
CPU コア数 | 8
RAM | 16 GB
ディスクの数 | ディスク 3 台<br/><br/> ディスクには、OS ディスク、プロセス サーバーのキャッシュ ディスク、フェールバック用リテンション ドライブが含まれます。
ディスクの空き領域 | プロセス サーバーのキャッシュのために必要な 600 GB の領域。
ディスクの空き領域 | リテンション ドライブのために必要な 600 GB の領域。
オペレーティング システム  | Windows Server 2012 R2 または Windows Server 2016 |
オペレーティング システムのロケール | 英語 (en-us)
PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0") は、バージョン [9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) 以降の構成サーバーには必要ありません。
Windows Server の役割 | 以下は有効にしません: <br/> - Active Directory Domain Services <br/>- インターネット インフォメーション サービス <br/> - Hyper-V |
グループ ポリシー| 以下は有効にしません: <br/> - コマンド プロンプトへのアクセス禁止。 <br/> - レジストリ編集ツールへのアクセス禁止。 <br/> - ファイル添付の信頼ロジック。 <br/> - スクリプト実行の有効化。 <br/> [詳細情報](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | 以下を実行します。<br/><br/> - 既定の Web サイトが事前に存在しないようにする <br/> - [匿名認証](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx)を有効にする <br/> - [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 設定を有効にする  <br/> - ポート 443 でリッスンしている既存の Web サイト/アプリがないようにする<br/>
NIC の種類 | VMXNET3 (VMware VM としてデプロイされている場合)
IP アドレスの種類 | 静的
ポート | コントロール チャネルのオーケストレーションに使用される 443<br/>データ転送に使用される 9443

## <a name="replicated-machines"></a>レプリケートされるマシン

Site Recovery は、サポートされているマシンで実行されているすべてのワークロードのレプリケーションをサポートします。

**コンポーネント** | **詳細**
--- | ---
マシンの設定 | Azure にレプリケートするマシンは、[Azure の要件](#azure-vm-requirements)を満たしている必要があります。
マシンのワークロード | Site Recovery は、サポートされているマシンで実行されている任意のワークロード (たとえば Active Directory、SQL サーバーなど) のレプリケーションをサポートします。 [詳細情報](https://aka.ms/asr_workload)。
Windows オペレーティング システム | 64 ビット Windows Server 2016 (Server Core、サーバーおよびデスクトップ エクスペリエンス)、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1 以降。 </br></br>  [Windows Server 2008 SP2 以降 - 32 ビットおよび 64 ビット](migrate-tutorial-windows-server-2008.md) (移行のみ)。 </br></br> Windows 2016 の Nano Server はサポートされていません。
Linux オペレーティング システムのアーキテクチャ | 64 ビット システムのみがサポートされています。 32 ビット システムはサポートされていません
Linux オペレーティング システム | Red Hat Enterprise Linux:5.2 から 5.11<b>\*\*</b>、6.1 から 6.10<b>\*\*</b>、7.0 から 7.6 <br/><br/>CentOS:5.2 から 5.11<b>\*\*</b>、6.1 から 6.10<b>\*\*</b>、7.0 から 7.6 <br/><br/>Ubuntu 14.04 LTS サーバー[ (サポートされるカーネルのバージョン)](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16.04 LTS サーバー[ (サポートされるカーネルのバージョン)](#ubuntu-kernel-versions)<br/><br/>Debian 7/Debian 8[ (サポートされるカーネルのバージョン)](#debian-kernel-versions)<br/><br/>SUSE Linux Enterprise Server 12 SP1、SP2、SP3 [ (サポートされるカーネルのバージョン)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/><br/>SUSE Linux Enterprise Server 11 SP3<b>\*\*</b> または SUSE Linux Enterprise Server 11 SP4 * </br></br>Red Hat 互換カーネルまたは Unbreakable Enterprise カーネル リリース 3 (UEK3) を実行している Oracle Linux 6.4、6.5、6.6、6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5 <br/><br/></br>-レプリケートされたマシンの SUSE Linux Enterprise Server 11 SP3 から SP4 へのアップグレードはサポートされていません。 アップグレードするには、いったんレプリケーションを無効にし、アップグレードの後に再び有効にします。</br></br> - Azure での Linux およびオープン ソース テクノロジのサポートについて詳しくは、[こちら](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure)をご覧ください。 Site Recovery では、Azure で Linux サーバーを実行するためにフェールオーバーが調整されます。 ただし Linux ベンダーによっては、サポート終了前のディストリビューション バージョンしかサポート対象に含まれない場合もあります。<br/><br/> - Linux ディストリビューションでは、ディストリビューションのマイナー バージョン リリース/更新の一部である stock カーネルのみがサポートされます。<br/><br/> - 保護されているマシンの Linux ディストリビューションのメジャー バージョン間のアップグレードはサポートされていません。 アップグレードするには、いったんレプリケーションを無効にしてオペレーティング システムをアップグレードしてから、レプリケーションを再び有効にします。<br/><br/> - Azure でマシンが起動するには、Red Hat Enterprise Linux 5.2 から 5.11 または CentOS 5.2 から 5.11 を実行しているサーバーに [Linux Integration Services (LIS) コンポーネント](https://www.microsoft.com/download/details.aspx?id=55106)がインストールされている必要があります。


### <a name="ubuntu-kernel-versions"></a>Ubuntu カーネルのバージョン


**サポートされているリリース** | **Azure Site Recovery モビリティ サービスのバージョン** | **カーネル バージョン** |
--- | --- | --- |
14.04 LTS | [9.23][9.23 UR] | 3.13.0-24-generic から 3.13.0-165-generic、<br/>3.16.0-25-generic ～ 3.16.0-77-generic、<br/>3.19.0-18-generic ～ 3.19.0-80-generic、<br/>4.2.0-18-generic ～ 4.2.0-42-generic、<br/>4.4.0-21-generic から 4.4.0-142-generic、<br/>4.15.0-1023-azure から 4.15.0-1037-azure |
14.04 LTS | [9.22][9.22 UR] | 3.13.0-24-generic から 3.13.0-164-generic、<br/>3.16.0-25-generic ～ 3.16.0-77-generic、<br/>3.19.0-18-generic ～ 3.19.0-80-generic、<br/>4.2.0-18-generic ～ 4.2.0-42-generic、<br/>4.4.0-21-generic から 4.4.0-140-generic、<br/>4.15.0-1023-azure から 4.15.0-1036-azure |
14.04 LTS | [9.21][9.21 UR] | 3.13.0-24-generic から 3.13.0-163-generic、<br/>3.16.0-25-generic ～ 3.16.0-77-generic、<br/>3.19.0-18-generic ～ 3.19.0-80-generic、<br/>4.2.0-18-generic ～ 4.2.0-42-generic、<br/>4.4.0-21-generic から 4.4.0-140-generic、<br/>4.15.0-1023-azure から 4.15.0-1035-azure |
14.04 LTS | [9.20][9.20 UR] | 3.13.0-24-generic から 3.13.0-153-generic、<br/>3.16.0-25-generic ～ 3.16.0-77-generic、<br/>3.19.0-18-generic ～ 3.19.0-80-generic、<br/>4.2.0-18-generic ～ 4.2.0-42-generic、<br/>4.4.0-21-generic から 4.4.0-138-generic、<br/>4.15.0-1023-azure から 4.15.0-1025-azure |
|||
16.04 LTS | [9.23][9.23 UR] | 4.4.0-21-generic から 4.4.0-142-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-45-generic、<br/>4.15.0-13-generic から 4.15.0-45-generic<br/>4.11.0-1009-azure ～ 4.11.0-1016-azure、<br/>4.13.0-1005-azure から 4.13.0-1018-azure <br/>4.15.0-1012-azure から 4.15.0-1037-azure|
16.04 LTS | [9.22][9.22 UR] | 4.4.0-21-generic から 4.4.0-140-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-45-generic、<br/>4.15.0-13-generic から 4.15.0-43-generic<br/>4.11.0-1009-azure ～ 4.11.0-1016-azure、<br/>4.13.0-1005-azure から 4.13.0-1018-azure <br/>4.15.0-1012-azure から 4.15.0-1036-azure|
16.04 LTS | [9.21][9.21 UR] | 4.4.0-21-generic から 4.4.0-140-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-45-generic、<br/>4.15.0-13-generic から 4.15.0-42-generic<br/>4.11.0-1009-azure ～ 4.11.0-1016-azure、<br/>4.13.0-1005-azure から 4.13.0-1018-azure <br/>4.15.0-1012-azure から 4.15.0-1035-azure|
16.04 LTS | [9.20][9.20 UR] | 4.4.0-21-generic から 4.4.0-138-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-45-generic、<br/>4.15.0-13-generic から 4.15.0-38-generic<br/>4.11.0-1009-azure ～ 4.11.0-1016-azure、<br/>4.13.0-1005-azure から 4.13.0-1018-azure <br/>4.15.0-1012-azure から 4.15.0-1025-azure|

### <a name="debian-kernel-versions"></a>Debian カーネルのバージョン


**サポートされているリリース** | **Azure Site Recovery モビリティ サービスのバージョン** | **カーネル バージョン** |
--- | --- | --- |
Debian 7 | [9.20][9.20 UR]、[9.21][9.21 UR][9.22][9.22 UR]、[9.23][9.23 UR]| 3.2.0-4-amd64 から 3.2.0-6-amd64、3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.20][9.20 UR]、[9.21][9.21 UR]、[9.22][9.22 UR]、[9.23][9.23 UR] | 3.16.0-4-amd64 から 3.16.0-7-amd64、4.9.0-0.bpo.4-amd64 から 4.9.0-0.bpo.8-amd64 |


### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 のサポートされるカーネルのバージョン

**リリース** | **モビリティ サービス バージョン** | **カーネル バージョン** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3) | [9.23][9.23 UR] | SP1 3.12.49-11-default から 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default から 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default から 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default から 4.4.121-92.101-default</br></br>SP3 4.4.73-5-default から 4.4.162-94.79-default |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3) | [9.22][9.22 UR] | SP1 3.12.49-11-default から 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default から 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default から 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default から 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default から 4.4.162-94.72-default |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3) | [9.21][9.21 UR] | SP1 3.12.49-11-default から 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default から 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default から 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default から 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default から 4.4.156-94.72-default |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3) | [9.20][9.20 UR] | SP1 3.12.49-11-default から 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default から 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default から 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default から 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default から 4.4.156-94.64-default |

## <a name="linux-file-systemsguest-storage"></a>Linux ファイル システム/ゲストのストレージ

**コンポーネント** | **サポートされています**
--- | ---
ファイル システム | ext3、ext4、XFS
ボリューム マネージャー | [バージョン 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) より前 <br/> 1.LVM2 がサポートされています。 <br/> 2.LVM は、データ ディスクでのみサポートされています。 <br/> 手順 3.Azure VM には、OS ディスクが 1 つだけあります。<br/><br/>[バージョン 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) 以降では、LVM と LVM2 がサポートされています。
準仮想化ストレージ デバイス | 準仮想化ドライバーによってエクスポートされたデバイスはサポートされません。
マルチ キュー ブロック IO デバイス | サポートされていません。
HP CCISS ストレージ コントローラーを使用する物理サーバー | サポートされていません。
デバイス/マウント ポイントの名前付け規則 | デバイス名またはマウント ポイント名は、一意である必要があります。 大文字と小文字の区別なしで同じ名前を持つデバイス/マウント ポイントが複数存在しないことを確認します。 </br> 例:同じ仮想マシンの 2 つのデバイスに *device1* および *Device1* という名前を付けることは許可されません。
ディレクトリ | [バージョン 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) より前 <br/> 1./ (ルート)、/boot、/usr、/usr/local、/var、/etc の各ディレクトリ (個別のパーティション/ファイルシステムとしてセットアップされた場合) はすべて、ソース サーバーの同じ OS ディスク上に存在する必要があります。</br>2. /boot はディスク パーティション上にあり、LVM ボリュームではないことが必要です。<br/><br/> [バージョン 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) 以降では、上記の制限は適用されません。 複数のディスクにまたがる LVM ボリュームでの /boot はサポートされていません。
ブート ディレクトリ | 仮想マシン上の複数のブート ディスクは、サポートされていません <br/><br/> ブート ディスクのないマシンは保護できません

空き領域要件 | /root パーティションで 2 GB <br/><br/> インストール フォルダー XFSv5 で 250 MB | メタデータ チェックサムなど、XFS ファイル システム上の XFSv5 機能は、モビリティ サービスのバージョン 9.10 以降でサポートされます。 xfs_info ユーティリティを使用して、パーティションの XFS スーパーブロックを確認します。 ftype が 1 に設定されている場合は、XFSv5 の機能が使用されます。

## <a name="vmdisk-management"></a>VM/ディスク管理

**アクション** | **詳細**
--- | ---
レプリケートされた VM のディスク サイズの変更 | サポートされています。
レプリケートされた VM のディスクの追加 | VM のレプリケーションを無効にし、ディスクを追加してから、レプリケーションを再び有効にします。 レプリケート VM へのディスクの追加は現在サポートされていません。

## <a name="network"></a>ネットワーク

**コンポーネント** | **サポートされています**
--- | ---
ホスト ネットワークの NIC チーミング | VMware VM でサポートされています。 <br/><br/>物理マシンのレプリケーションではサポートされません。
ホスト ネットワークの VLAN | はい。
ホスト ネットワークの IPv4 | はい。
ホスト ネットワークの IPv6 | いいえ。
ゲスト/サーバー ネットワークの NIC チーミング | いいえ。
ゲスト/サーバー ネットワークの IPv4 | はい。
ゲスト/サーバー ネットワークの IPv6 | いいえ。
ゲスト/サーバー ネットワークの静的 IP (Windows) | はい。
ゲスト/サーバー ネットワークの静的 IP (Linux) | はい。 <br/><br/>フェールバックで DHCP を使用するように VM が構成されます。
ゲスト/サーバー ネットワークのマルチ NIC | はい。


## <a name="azure-vm-network-after-failover"></a>Azure VM ネットワーク (フェールオーバー後)

**コンポーネント** | **サポートされています**
--- | ---
Azure ExpressRoute | はい
ILB | はい
ELB | はい
Azure の Traffic Manager | はい
マルチ NIC | はい
予約済み IP アドレス | はい
IPv4 | はい
送信元 IP アドレスを保持する | はい
Azure Virtual Network サービス エンドポイント<br/> | はい
高速ネットワーク | いいえ 

## <a name="storage"></a>Storage
**コンポーネント** | **サポートされています**
--- | ---
ダイナミック ディスク | オペレーティング システム ディスクは、ベーシック ディスクである必要があります。 <br/><br/>データ ディスクは、ダイナミック ディスクにすることができます
Docker ディスク構成 | いいえ 
ホスト NFS | VMware = はい<br/><br/> 物理サーバー = いいえ
ホスト SAN (iSCSI/FC) | はい
ホスト vSAN | VMware = はい<br/><br/> 物理サーバー = 該当なし
ホスト マルチパス (MPIO) | はい - テスト環境: Microsoft DSM、EMC PowerPath 5.7 SP4、EMC PowerPath DSM for CLARiiON
ホストの仮想ボリューム (VVols) | VMware = はい<br/><br/> 物理サーバー = 該当なし
ゲスト/サーバー VMDK | はい
ゲスト/サーバー EFI/UEFI| 一部 (Windows Server 2012 以降の Azure への移行) <br/><br/> この表の下部の注意事項をご覧ください
ゲスト/サーバー共有クラスター ディスク | いいえ 
ゲスト/サーバー暗号化ディスク | いいえ 
ゲスト/サーバー NFS | いいえ 
ゲスト/サーバー SMB 3.0 | いいえ 
ゲスト/サーバー RDM | はい<br/><br/> 物理サーバー = 該当なし
ゲスト/サーバー ディスク > 1 TB | はい<br/><br/>最大 4,095 GB<br/><br/> ディスクは 1024 MB 以上である必要があります。
4K 論理および 4K 物理セクター サイズのゲスト/サーバー ディスク | はい
4K 論理および 512 バイト物理セクター サイズのゲスト/サーバー ディスク | はい
ストライピングされたディスクのゲスト/サーバー ボリューム > 4 TB <br/><br/>論理ボリューム管理 (LVM)| はい
ゲスト/サーバー - 記憶域スペース | いいえ 
ゲスト/サーバー ディスクのホット アド/削除 | いいえ 
ゲスト/サーバー - ディスクの除外 | はい
ゲスト/サーバー マルチパス (MPIO) | いいえ 

> [!NOTE]
> Windows Server 2012 以降を実行する VMware 仮想マシンの UEFI ブートは、Azure に移行できます。 次の制限事項が適用されます。
>
> - Azure への移行のみがサポートされています。 オンプレミスの VMware サイトへのフェールバックはサポートされていません。
> - サーバーには、OS ディスクに 4 個を超えるパーティションを持たせないでください。
> - バージョン 9.13 以降のモビリティ サービスが必要です。

## <a name="azure-storage"></a>Azure Storage

**コンポーネント** | **サポートされています**
--- | ---
ローカル冗長ストレージ | はい
geo 冗長ストレージ | はい
読み取りアクセス geo 冗長ストレージ | はい
クール ストレージ | いいえ 
ホット ストレージ| いいえ 
ブロック blob | いいえ 
保存時の暗号化 (Storage サービスの暗号化)| はい
Premium Storage | はい
インポート/エクスポート サービス | いいえ 
ターゲット ストレージ/キャッシュ ストレージ アカウント (レプリケーション データの保存に使用) で構成された仮想ネットワークの Azure Storage ファイアウォール | はい
汎用目的 V2 ストレージ アカウント (ホット層とクール層の両方) | いいえ 

## <a name="azure-compute"></a>Azure コンピューティング

**機能** | **サポートされています**
--- | ---
可用性セット | はい
可用性ゾーン | いいえ 
ハブ | はい
マネージド ディスク | はい

## <a name="azure-vm-requirements"></a>Azure VM の要件

Azure にレプリケートするオンプレミス VM は、この表にまとめられている Azure VM の要件を満たしている必要があります。 Site Recovery が前提条件確認を実行するとき、満たされていない要件がある場合には確認が失敗します。

**コンポーネント** | **要件** | **詳細**
--- | --- | ---
ゲスト オペレーティング システム | レプリケートするマシンの[サポート対象のオペレーティング システム](#replicated-machines)を確認します。 | サポートされていない場合、確認は失敗します。
ゲスト オペレーティング システムのアーキテクチャ | 64 ビット。 | サポートされていない場合、確認は失敗します。
オペレーティング システムのディスク サイズ | 最大 2,048 GB。 | サポートされていない場合、確認は失敗します。
オペレーティング システムのディスク数 | 1 | サポートされていない場合、確認は失敗します。
データ ディスク数 | 64 以下。 | サポートされていない場合、確認は失敗します。
データ ディスク サイズ | 最大 4,095 GB | サポートされていない場合、確認は失敗します。
ネットワーク アダプター | 複数のアダプターがサポートされます。 |
共有 VHD | サポートされていません。 | サポートされていない場合、確認は失敗します。
FC ディスク | サポートされていません。 | サポートされていない場合、確認は失敗します。
BitLocker | サポートされていません。 | マシンのレプリケーションを有効にする前に、BitLocker を無効にする必要があります。 |
VM 名 | 1 から 63 文字。<br/><br/> 名前に使用できるのは、英文字、数字、およびハイフンのみです。<br/><br/> マシン名の最初と最後は、文字か数字とする必要があります。 |  Site Recovery でマシンのプロパティの値を更新します。

## <a name="azure-site-recovery-churn-limits"></a>Azure Site Recovery のチャーンの制限

以下の表は、Azure Site Recovery の制限を示したものです。 上記の制限は、弊社のテストに基づいて公開されていますが、アプリケーション I/O として想定されるすべての組み合わせを網羅したものではありません。 実際の結果は、ご使用のアプリケーションで発生するさまざまな I/O によって異なることが考えられます。 最良の結果を得るためには、[Deployment Planner ツールを実行](site-recovery-deployment-planner.md)し、テスト フェールオーバーを実行して広範なアプリケーション テストを実行し、アプリケーションの実際のパフォーマンスを把握することを強くお勧めします。

**レプリケーション先のストレージ** | **レプリケーション元の平均ディスク I/O サイズ** |**レプリケーション元ディスクの平均データ変更頻度** | **レプリケーション元ディスクの 1 日あたりのデータ変更頻度合計**
---|---|---|---
Standard Storage | 8 KB | 2 MB/秒 | (ディスクあたり) 168 GB
Premium P10 または P15 ディスク | 8 KB  | 2 MB/秒 | (ディスクあたり) 168 GB
Premium P10 または P15 ディスク | 16 KB | 4 MB/秒 |  (ディスクあたり) 336 GB
Premium P10 または P15 ディスク | 32 KB 以上 | 8 MB/秒 | (ディスクあたり) 672 GB
Premium P20、P30、P40、または P50 ディスク | 8 KB    | 5 MB/s | (ディスクあたり) 421 GB
Premium P20、P30、P40、または P50 ディスク | 16 KB 以上 |20 MB/秒 | (ディスクあたり) 1,684 GB

**ソース データ変更頻度** | **上限**
---|---
VM あたりの平均データ変更頻度| 25 MB/秒
VM 上の全ディスクにおけるデータ変更頻度のピーク | 54 MB/秒
プロセス サーバーでサポートされる 1 日あたりのデータ変更頻度の上限 | 2 TB

前述の数値は、I/O のオーバーラップを 30% とした場合の平均値です。 Site Recovery は、オーバーラップ比に基づくより高いスループットと、より大きな書き込みサイズ、そして実ワークロード I/O 動作を扱うことができます。 上記の数値には、標準的なバックログとして約 5 分が想定されています。 つまりデータはアップロード後 5 分以内に処理されて復旧ポイントが作成されます。

## <a name="vault-tasks"></a>資格情報コンテナーのタスク

**アクション** | **サポートされています**
--- | ---
リソース グループ間の資格情報コンテナーの移動<br/><br/> サブスクリプション内およびサブスクリプション間 | いいえ 
リソース グループ間でストレージ、ネットワーク、Azure VM を移動<br/><br/> サブスクリプション内およびサブスクリプション間 | いいえ 


## <a name="download-latest-azure-site-recovery-components"></a>Azure Site Recovery コンポーネントのダウンロード

**Name** | **説明** | **最新バージョンのダウンロード手順**
--- | --- | ---
構成サーバー | オンプレミスの VMware サーバーと Azure の間の通信を調整します  <br/><br/> オンプレミスの VMware サーバーにインストールされます | 詳しくは、[新規インストール](vmware-azure-deploy-configuration-server.md)および[既存のコンポーネントから最新バージョンへのアップグレード](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)に関するガイダンスをご覧ください。
プロセス サーバー|構成サーバーに既定でインストールされます。 レプリケーション データを受信し、そのデータをキャッシュ、圧縮、暗号化によって最適化して、Azure Storage に送信します。 デプロイの拡大に合わせて、増大するレプリケーション トラフィックの処理を実行する独立したプロセス サーバーを追加できます。| 詳しくは、[新規インストール](vmware-azure-set-up-process-server-scale.md)および[既存のコンポーネントから最新バージョンへのアップグレード](vmware-azure-manage-process-server.md#upgrade-a-process-server)に関するガイダンスをご覧ください。
モビリティ サービス | オンプレミスの VMware サーバー/物理サーバーと Azure/セカンダリ サイトの間のレプリケーションを調整します<br/><br/> レプリケートする VMware VM または物理サーバーにインストールされます | 詳しくは、[新規インストール](vmware-azure-install-mobility-service.md)および[既存のコンポーネントから最新バージョンへのアップグレード](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal)に関するガイダンスをご覧ください。

最新の機能について詳しくは、[最新のリリース ノート](https://aka.ms/ASR_latest_release_notes)をご覧ください。


## <a name="next-steps"></a>次の手順
VMware VM のディザスター リカバリーのために Azure を準備する[方法を学びます](tutorial-prepare-azure.md)。

[9.23 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
