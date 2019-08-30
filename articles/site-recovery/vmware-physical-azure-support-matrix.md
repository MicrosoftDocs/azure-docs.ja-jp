---
title: Azure Site Recovery を使用した VMware VM および物理サーバーの Azure へのディザスター リカバリーに関するサポート マトリックス | Microsoft Docs
description: Azure Site Recovery を使用して VMware VM および物理サーバーを Azure にディザスター リカバリーする場合のサポートについてまとめています。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: raynew
ms.openlocfilehash: fd24d0d9f05855cf22da547f95b16da0a8d2c788
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617641"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>VMware VM および物理サーバーの Azure へのディザスター リカバリーのサポート マトリックス

この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用して、VMware VM と物理サーバーの Azure へのディザスター リカバリーを行う場合にサポートされるコンポーネントと設定の概要について説明します。

- VMware VM/物理サーバー ディザスター リカバリーのアーキテクチャの[詳細](vmware-azure-architecture.md)についてご確認ください。
- [チュートリアル](tutorial-prepare-azure.md)に従ってディザスター リカバリーをお試しください。

## <a name="deployment-scenarios"></a>デプロイメント シナリオ

**シナリオ** | **詳細**
--- | ---
VMware VM のディザスター リカバリー | オンプレミス VMware VM の Azure へのレプリケーション。 このシナリオは、Azure portal または [PowerShell](vmware-azure-disaster-recovery-powershell.md) を使用してデプロイできます。
物理サーバーのディザスター リカバリー | オンプレミスの Windows または Linux の物理サーバーから Azure へのレプリケーション。 このシナリオは、Azure Portal で展開できます。

## <a name="on-premises-virtualization-servers"></a>オンプレミスの仮想化サーバー

**サーバー** | **要件** | **詳細**
--- | --- | ---
vCenter Server | バージョン 6.7、6.5、6.0、5.5 | ディザスター リカバリーのデプロイでは vCenter サーバーを使用することをお勧めします。
vSphere ホスト | バージョン 6.7、6.5、6.0、5.5 | vSphere ホストと vCenter サーバーはプロセス サーバーと同じネットワーク内に存在することが推奨されます。 既定では、プロセス サーバーは構成サーバーで実行されます。 [詳細情報](vmware-physical-azure-config-process-server-overview.md)。


## <a name="site-recovery-configuration-server"></a>Site Recovery 構成サーバー

構成サーバーはオンプレミスのマシンで、構成サーバー、プロセス サーバー、マスター ターゲット サーバーを含む Site Recovery のコンポーネントを実行します。

- VMware VM の場合は、VMware VM を作成する OVF テンプレートをダウンロードすることで構成サーバーを設定します。
- 物理サーバーの場合は、構成サーバーのマシンを手動で設定します。

**コンポーネント** | **要件**
--- |---
CPU コア数 | 8
RAM | 16 GB
ディスクの数 | ディスク 3 台<br/><br/> ディスクには、OS ディスク、プロセス サーバーのキャッシュ ディスク、フェールバック用リテンション ドライブが含まれます。
ディスクの空き領域 | プロセス サーバーのキャッシュ用に 600 GB の領域。
ディスクの空き領域 | リテンション ドライブ用に 600 GB の領域。
オペレーティング システム  | Windows Server 2012 R2 またはデスクトップ エクスペリエンス搭載 Windows Server 2016 |
オペレーティング システムのロケール | 英語 (en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | バージョン [9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) 以降の構成サーバーの場合は不要です。 
Windows Server の役割 | Active Directory Domain Services、インターネット インフォメーション サービス (IIS)、Hyper-V は有効にしないでください。 
グループ ポリシー| - コマンド プロンプトへのアクセス禁止。 <br/> - レジストリ編集ツールへのアクセス禁止。 <br/> - ファイル添付の信頼ロジック。 <br/> - スクリプト実行の有効化。 <br/> - [詳細情報](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | 以下を実行します。<br/><br/> - 既定の Web サイトが事前に存在しないようにする <br/> - [匿名認証](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx)を有効にする <br/> - [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 設定を有効にする  <br/> - ポート 443 でリッスンしている既存の Web サイト/アプリがないようにする<br/>
NIC の種類 | VMXNET3 (VMware VM としてデプロイされている場合)
IP アドレスの種類 | 静的
Port | コントロール チャネルのオーケストレーションに使用される 443<br/>データ転送用の 9443

## <a name="replicated-machines"></a>レプリケートされるマシン

Site Recovery は、サポートされているマシンで実行されているすべてのワークロードのレプリケーションをサポートします。

**コンポーネント** | **詳細**
--- | ---
マシンの設定 | Azure にレプリケートするマシンは、[Azure の要件](#azure-vm-requirements)を満たしている必要があります。
マシンのワークロード | Site Recovery は、サポートされているマシンで実行されているすべてのワークロードのレプリケーションをサポートします。 [詳細情報](https://aka.ms/asr_workload)。
Windows | - Windows Server 2019 ([更新プログラム ロールアップ 34](https://support.microsoft.com/help/4490016) からサポート (モビリティ サービスのバージョン 9.22) 以降。<br/> - Windows Server 2016 (64 ビット サーバー コア、デスクトップ エクスペリエンス搭載サーバー)<br/> - Windows Server 2012 R2、Windows Server 2012<br/> - Windows Server 2008 R2 SP1 以降。<br/> - Windows Server 2008、SP2 以降の 64 および 32 ビット]。 移行についてのみサポートされています。 [詳細情報](migrate-tutorial-windows-server-2008.md)。<br/> - Windows 10、Windows 8.1、Windows 8、Windows 7 64 ビット ([更新プログラム ロールアップ 36](https://support.microsoft.com/help/4503156) からサポート (モビリティ サービスのバージョン 9.22 以降)。 Windows 7 RTM はサポートされていません。 
Linux | 64 ビット システムのみがサポートされています。 32 ビット システムはサポートされていません。<br/><br/>すべての Linux サーバーには [Linux Integration Services (LIS) コンポーネント](https://www.microsoft.com/download/details.aspx?id=55106)がインストールされている必要があります。 テスト フェールオーバー/フェールオーバー後に Azure でサーバーを起動するために必要です。 LIS コンポーネントがない場合、Azure で起動するマシンのレプリケーションを有効にする前に、必ず[コンポーネント](https://www.microsoft.com/download/details.aspx?id=55106)をインストールしてください。 <br/><br/> Site Recovery では、Azure で Linux サーバーを実行するためにフェールオーバーが調整されます。 ただし Linux ベンダーによっては、サポート終了前のディストリビューション バージョンしかサポート対象に含まれない場合もあります。<br/><br/> Linux ディストリビューションでは、ディストリビューションのマイナー バージョン リリース/更新の一部である stock カーネルのみがサポートされます。<br/><br/> 保護されているマシンの Linux ディストリビューションのメジャー バージョン間のアップグレードはサポートされていません。 アップグレードするには、いったんレプリケーションを無効にしてオペレーティング システムをアップグレードしてから、レプリケーションを再び有効にします。<br/><br/> Azure での Linux およびオープン ソース テクノロジのサポートについて詳しくは、[こちら](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure)をご覧ください。
Linux Red Hat Enterprise | 5.2 から 5.11</b><br/> 6.1 から 6.10</b><br/> 7.0 から 7.6<br/> <br/> Red Hat Enterprise Linux 5.2 から 5.11 および 6.1 から 6.10 を実行しているサーバーには [Linux Integration Services (LIS) コンポーネント](https://www.microsoft.com/download/details.aspx?id=55106)が事前インストールされていません。 Azure で起動するマシンのレプリケーションを有効にする前に、必ず[コンポーネント](https://www.microsoft.com/download/details.aspx?id=55106)をインストールしてください。
Linux:CentOS | 5.2 から 5.11</b><br/> 6.1 から 6.10</b><br/> 7.0 から 7.6<br/> <br/> CentOS 5.2 から 5.11 および 6.1 から 6.10 を実行しているサーバーには [Linux Integration Services (LIS) コンポーネント](https://www.microsoft.com/download/details.aspx?id=55106)が事前インストールされていません。 Azure で起動するマシンのレプリケーションを有効にする前に、必ず[コンポーネント](https://www.microsoft.com/download/details.aspx?id=55106)をインストールしてください。
Ubuntu | Ubuntu 14.04 LTS サーバー [(サポートされるカーネルのバージョンを確認してください)](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16.04 LTS サーバー [(サポートされるカーネルのバージョンを確認してください)](#ubuntu-kernel-versions)
Debian | Debian 7/Debian 8 [(サポートされるカーネルのバージョンを確認してください)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1、SP2、SP3、SP4 [(サポートされるカーネルのバージョンを確認してください)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3 または SUSE Linux Enterprise Server 11 SP4<br/> レプリケートされたマシンの SUSE Linux Enterprise Server 11 SP3 から SP4 へのアップグレードはサポートされていません。 アップグレードするには、レプリケーションを無効にし、アップグレードの後に再び有効にします。
Oracle Linux | 6.4、6.5、6.6、6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5、7.6<br/><br/> Red Hat と互換可能なカーネルまたは Unbreakable Enterprise カーネル リリース 3、4、5 (UEK3、UEK4、UEK5) を実行している 


### <a name="ubuntu-kernel-versions"></a>Ubuntu カーネルのバージョン


**サポートされているリリース** | **モビリティ サービス バージョン** | **カーネル バージョン** |
--- | --- | --- |
14.04 LTS | [9.27][9.27 UR]| 3.13.0-24-generic から 3.13.0-170-generic、<br/>3.16.0-25-generic ～ 3.16.0-77-generic、<br/>3.19.0-18-generic ～ 3.19.0-80-generic、<br/>4.2.0-18-generic ～ 4.2.0-42-generic、<br/>4.4.0-21-generic から 4.4.0-148-generic、<br/>4.15.0-1023-azure から 4.15.0-1045-azure |
14.04 LTS | [9.26][9.26 UR]| 3.13.0-24-generic から 3.13.0-170-generic、<br/>3.16.0-25-generic ～ 3.16.0-77-generic、<br/>3.19.0-18-generic ～ 3.19.0-80-generic、<br/>4.2.0-18-generic ～ 4.2.0-42-generic、<br/>4.4.0-21-generic から 4.4.0-148-generic、<br/>4.15.0-1023-azure から 4.15.0-1045-azure |
14.04 LTS | [9.25][9.25 UR]  | 3.13.0-24-generic から 3.13.0-169-generic、<br/>3.16.0-25-generic ～ 3.16.0-77-generic、<br/>3.19.0-18-generic ～ 3.19.0-80-generic、<br/>4.2.0-18-generic ～ 4.2.0-42-generic、<br/>4.4.0-21-generic から 4.4.0-146-generic、<br/>4.15.0-1023-azure から 4.15.0-1042-azure |
14.04 LTS | [9.24][9.24 UR] | 3.13.0-24-generic から 3.13.0-167-generic、<br/>3.16.0-25-generic ～ 3.16.0-77-generic、<br/>3.19.0-18-generic ～ 3.19.0-80-generic、<br/>4.2.0-18-generic ～ 4.2.0-42-generic、<br/>4.4.0-21-generic から 4.4.0-143-generic、<br/>4.15.0-1023-azure から 4.15.0-1040-azure |
|||
16.04 LTS | [9.27][9.27 UR] | 4.4.0-21-generic から 4.4.0-154-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-45-generic、<br/>4.15.0-13-generic から 4.15.0-54-generic<br/>4.11.0-1009-azure ～ 4.11.0-1016-azure、<br/>4.13.0-1005-azure から 4.13.0-1018-azure <br/>4.15.0-1012-azure から 4.15.0-1050-azure|
16.04 LTS | [9.26][9.26 UR] | 4.4.0-21-generic から 4.4.0-148-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-45-generic、<br/>4.15.0-13-generic から 4.15.0-50-generic<br/>4.11.0-1009-azure ～ 4.11.0-1016-azure、<br/>4.13.0-1005-azure から 4.13.0-1018-azure <br/>4.15.0-1012-azure から 4.15.0-1045-azure|
16.04 LTS | [9.25][9.25 UR] | 4.4.0-21-generic から 4.4.0-146-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-45-generic、<br/>4.15.0-13-generic から 4.15.0-48-generic<br/>4.11.0-1009-azure ～ 4.11.0-1016-azure、<br/>4.13.0-1005-azure から 4.13.0-1018-azure <br/>4.15.0-1012-azure から 4.15.0-1042-azure|
16.04 LTS | [9.24][9.24 UR] | 4.4.0-21-generic から 4.4.0-143-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-45-generic、<br/>4.15.0-13-generic から 4.15.0-46-generic<br/>4.11.0-1009-azure ～ 4.11.0-1016-azure、<br/>4.13.0-1005-azure から 4.13.0-1018-azure <br/>4.15.0-1012-azure から 4.15.0-1040-azure|

### <a name="debian-kernel-versions"></a>Debian カーネルのバージョン


**サポートされているリリース** | **モビリティ サービス バージョン** | **カーネル バージョン** |
--- | --- | --- |
Debian 7 | [9.24][9.24 UR]、[9.25][9.25 UR]、[9.26][9.26 UR]、[9.27][9.27 UR]| 3.2.0-4-amd64 から 3.2.0-6-amd64、3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.27][9.27 UR] | 3.16.0-4-amd64 から 3.16.0-9-amd64、4.9.0-0.bpo.4-amd64 から 4.9.0-0.bpo.9-amd64 |
Debian 8 | [9.24][9.24 UR]、[9.25][9.25 UR]、[9.26][9.26 UR] | 3.16.0-4-amd64 から 3.16.0-8-amd64、4.9.0-0.bpo.4-amd64 から 4.9.0-0.bpo.8-amd64 |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 のサポートされるカーネルのバージョン

**リリース** | **モビリティ サービス バージョン** | **カーネル バージョン** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3、SP4) | [9.27][9.27 UR] | SP1 3.12.49-11-default から 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default から 3.12.74-60.64.115-default</br></br> SP2 4.4.21-69-default から 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default から 4.4.121-92.114-default</br></br>SP3 4.4.73-5-default から 4.4.180-94.97-default</br></br>SP3 4.4.138-4.7-azure から 4.4.180-4.31-azure</br></br>SP4 4.12.14-94.41-default から 4.12.14-95.19-default</br>SP4 4.12.14-6.3-azure から 4.12.14-6.15-azure |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3、SP4) | [9.26][9.26 UR] | SP1 3.12.49-11-default から 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default から 3.12.74-60.64.110-default</br></br> SP2 4.4.21-69-default から 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default から 4.4.121-92.109-default</br></br>SP3 4.4.73-5-default から 4.4.178-94.91-default</br></br>SP3 4.4.138-4.7-azure から 4.4.178-4.28-azure</br></br>SP4 4.12.14-94.41-default から 4.12.14-95.16-default</br>SP4 4.12.14-6.3-azure から 4.12.14-6.9-azure |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3、SP4) | [9.25][9.25 UR] | SP1 3.12.49-11-default から 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default から 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default から 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default から 4.4.121-92.104-default</br></br>SP3 4.4.73-5-default から 4.4.176-94.88-default</br></br>SP3 4.4.138-4.7-azure から 4.4.176-4.25-azure</br></br>SP4 4.12.14-94.41-default から 4.12.14-95.13-default</br>SP4 4.12.14-6.3-azure から 4.12.14-6.9-azure |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3、SP4) | [9.24][9.24 UR] | SP1 3.12.49-11-default から 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default から 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default から 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default から 4.4.121-92.101-default</br></br>SP3 4.4.73-5-default から 4.4.175-94.79-default</br></br>SP4 4.12.14-94.41-default から 4.12.14-95.6-default |


## <a name="linux-file-systemsguest-storage"></a>Linux ファイル システム/ゲストのストレージ

**コンポーネント** | **サポートされています**
--- | ---
ファイル システム | ext3、ext4、XFS
ボリューム マネージャー | - LVM はサポートされています。<br/> - LVM での/boot は [更新プログラム ロールアップ 31](https://support.microsoft.com/help/4478871/) (モビリティ サービスのバージョン 9.20) 以降でサポートされています。 モビリティ サービスの以前のバージョンではサポートされていません。<br/> - 複数の OS ディスクはサポートされていません。
準仮想化ストレージ デバイス | 準仮想化ドライバーによってエクスポートされたデバイスはサポートされません。
マルチ キュー ブロック IO デバイス | サポートされていません。
HP CCISS ストレージ コントローラーを使用する物理サーバー | サポートされていません。
デバイス/マウント ポイントの名前付け規則 | デバイス名またはマウント ポイント名は、一意である必要があります。<br/> 2 つのデバイス/マウント ポイントに大文字と小文字を区別した名前を付けていないことを確認します。 たとえば、同じ VM のデバイスに *device1* と *Device1* という名前を付けることはサポートされていません。
ディレクトリ | バージョン 9.20 ([更新プログラム ロールアップ 31](https://support.microsoft.com/help/4478871/) でリリース) より前のモビリティ サービスのバージョンを実行している場合、次の制限事項が適用されます。<br/><br/> - / (ルート)、/boot、/usr、/usr/local、/var、/etc の各ディレクトリ (個別のパーティション/ファイルシステムとしてセットアップされた場合) は、ソース サーバーの同じ OS ディスク上に存在する必要があります。</br> - /boot ディレクトリはディスク パーティション上にあり、LVM ボリュームではないことが必要です。<br/><br/> バージョン 9.20 以降、これらの制限事項は適用されません。 
ブート ディレクトリ | - GPT パーティション形式のブート ディスクは使用できません。 これは Azure アーキテクチャの制限です。 GPT ディスクは、データ ディスクとしてサポートされています。<br/><br/> VM での複数のブート ディスクはサポートされていません。<br/><br/> - 複数のディスクにまたがる LVM ボリュームでの /boot はサポートされていません。<br/> - ブート ディスクのないマシンをレプリケートすることはできません。
空き領域要件| /rootパーティションで 2GB <br/><br/> インストール フォルダーで 250MB
XFSv5 | メタデータ チェックサムなど、XFS ファイル システム上の XFSv5 機能はサポートされています (モビリティ サービスのバージョン 9.10 以降)。<br/> xfs_info ユーティリティを使用して、パーティションの XFS スーパーブロックを確認します。 `ftype` が 1 に設定されている場合は、XFSv5 の機能が使用されます。
BTRFS | \- BTRFS は[更新プログラム ロールアップ 34](https://support.microsoft.com/help/4490016) (モビリティ サービスのバージョン 9.22) 以降でサポートされています。 次の場合、BTRFS はサポートされません。<br/><br/> - 保護を有効にした後、BTRFS ファイル システムのサブボリュームが変更されている。</br> - BTRFS ファイル システムが複数のディスクに分散されている。</br> - BTRFS ファイル システムで RAID がサポートされている。

## <a name="vmdisk-management"></a>VM/ディスク管理

**アクション** | **詳細**
--- | ---
レプリケートされた VM のディスク サイズの変更 | サポートされています。
レプリケートされた VM のディスクの追加 | サポートされていません。<br/> VM のレプリケーションを無効にし、ディスクを追加してから、レプリケーションを再び有効にします。

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
Azure 仮想ネットワーク サービス エンドポイント<br/> | はい
Accelerated Networking | いいえ

## <a name="storage"></a>Storage
**コンポーネント** | **サポートされています**
--- | ---
ダイナミック ディスク | OS ディスクは、ベーシック ディスクである必要があります。 <br/><br/>データ ディスクは、ダイナミック ディスクにすることができます
Docker ディスク構成 | いいえ
ホスト NFS | VMware = はい<br/><br/> 物理サーバー = いいえ
ホスト SAN (iSCSI/FC) | はい
ホスト vSAN | VMware = はい<br/><br/> 物理サーバー = 該当なし
ホスト マルチパス (MPIO) | はい - テスト環境: Microsoft DSM、EMC PowerPath 5.7 SP4、EMC PowerPath DSM for CLARiiON
ホストの仮想ボリューム (VVols) | VMware = はい<br/><br/> 物理サーバー = 該当なし
ゲスト/サーバー VMDK | はい
ゲスト/サーバー共有クラスター ディスク | いいえ
ゲスト/サーバー暗号化ディスク | いいえ
ゲスト/サーバー NFS | いいえ
ゲスト/サーバー iSCSI | 移行の場合 - はい<br/>ディザスター リカバリーの場合 - いいえ、iSCSI は接続されたディスクとして VM にフェールバックされます
ゲスト/サーバー SMB 3.0 | いいえ
ゲスト/サーバー RDM | はい<br/><br/> 物理サーバー = 該当なし
ゲスト/サーバー ディスク > 1 TB | はい。ディスクは 1,024 MB 以上である必要があります。<br/><br/>マネージド ディスクにレプリケートする場合は最大 8,192 GB (9.26 バージョン以降)<br></br> ストレージ アカウントにレプリケートする場合は最大 4,095 GB
4K 論理および 4K 物理セクター サイズのゲスト/サーバー ディスク | いいえ
4K 論理および 512 バイト物理セクター サイズのゲスト/サーバー ディスク | いいえ
ストライピングされたディスクのゲスト/サーバー ボリューム > 4 TB <br/><br/>論理ボリューム管理 (LVM)| はい
ゲスト/サーバー - 記憶域スペース | いいえ
ゲスト/サーバー ディスクのホット アド/削除 | いいえ
ゲスト/サーバー - ディスクの除外 | はい
ゲスト/サーバー マルチパス (MPIO) | いいえ
ゲスト/サーバー GPT パーティション | \- 5 個のパーティションが[更新プログラム ロールアップ 37](https://support.microsoft.com/help/4508614/) (モビリティ サービスのバージョン 9.25) 以降でサポートされています。 以前は 4 個までサポートしていました。
ゲスト/サーバー EFI/UEFI ブート | - バージョン 9.13 以降のモビリティ サービスを実行している場合、サポートされます。<br/> - Windows Server 2012 以降を実行している VMware VM または物理サーバーを Azure に移行する場合にサポートされます。<br/> - VM は移行の場合にのみレプリケートできます。 オンプレミスへのフェールバックはサポートされていません。<br/> - NTFS のみがサポートされます。 <br/> - セキュリティで保護された UEFI ブートの種類はサポートされていません。 <br/> - ディスクのセクター サイズは、物理セクターごとに 512 バイトとする必要があります。

## <a name="replication-channels"></a>レプリケーション チャネル

|**レプリケーションの種類**   |**サポートされています**  |
|---------|---------|
|オフロード データ転送 (ODX)    |       いいえ  |
|オフライン シード処理        |   いいえ      |
| Azure Data Box | いいえ

## <a name="azure-storage"></a>Azure Storage

**コンポーネント** | **サポートされています**
--- | ---
ローカル冗長ストレージ | はい
geo 冗長ストレージ | はい
読み取りアクセス geo 冗長ストレージ | はい
クール ストレージ | いいえ
ホット ストレージ| いいえ
ブロック blob | いいえ
保存時の暗号化 (SSE)| はい
Premium Storage | はい
インポート/エクスポート サービス | いいえ
VNet 用 Azure Storage ファイアウォール | はい。<br/> ターゲット ストレージ/キャッシュ ストレージ アカウント (レプリケーション データの保存に使用) で構成されたもの。
汎用目的 V2 ストレージ アカウント (ホット層とクール層) | はい (V1 に比べて V2 のトランザクション コストは非常に大きくなります)

## <a name="azure-compute"></a>Azure コンピューティング

**機能** | **サポートされています**
--- | ---
可用性セット | はい
可用性ゾーン | いいえ
ハブ | はい
マネージド ディスク | はい

## <a name="azure-vm-requirements"></a>Azure VM の要件

Azure にレプリケートされたオンプレミス VM は、この表にまとめられている Azure VM の要件を満たしている必要があります。 Site Recovery がレプリケーションの前提条件確認を実行するとき、満たされていない要件がある場合には確認が失敗します。

**コンポーネント** | **要件** | **詳細**
--- | --- | ---
ゲスト オペレーティング システム | レプリケートするマシンの[サポート対象のオペレーティング システム](#replicated-machines)を確認します。 | サポートされていない場合、確認は失敗します。
ゲスト オペレーティング システムのアーキテクチャ | 64 ビット。 | サポートされていない場合、確認は失敗します。
オペレーティング システムのディスク サイズ | 最大 2,048 GB。 | サポートされていない場合、確認は失敗します。
オペレーティング システムのディスク数 | 1 | サポートされていない場合、確認は失敗します。
データ ディスク数 | 64 以下。 | サポートされていない場合、確認は失敗します。
データ ディスク サイズ | マネージド ディスクにレプリケートする場合は最大 8,192 GB (9.26 バージョン以降)<br></br>ストレージ アカウントにレプリケートする場合は最大 4,095 GB| サポートされていない場合、確認は失敗します。
ネットワーク アダプター | 複数のアダプターがサポートされます。 |
共有 VHD | サポートされていません。 | サポートされていない場合、確認は失敗します。
FC ディスク | サポートされていません。 | サポートされていない場合、確認は失敗します。
BitLocker | サポートされていません。 | マシンのレプリケーションを有効にする前に、BitLocker を無効にする必要があります。 |
VM 名 | 1 から 63 文字。<br/><br/> 名前に使用できるのは、英文字、数字、およびハイフンのみです。<br/><br/> マシン名の最初と最後は、文字か数字とする必要があります。 |  Site Recovery でマシンのプロパティの値を更新します。

## <a name="churn-limits"></a>チャーンの制限

以下の表は、Azure Site Recovery の制限を示したものです。 
- これらの制限は、Microsoft のテストに基づいて公開されていますが、アプリ I/O として想定されるすべての組み合わせを網羅したものではありません。
- 実際の結果は、ご使用のアプリケーションで発生するさまざまな I/O によって異なることが考えられます。
- 最良の結果を得るためには、[Deployment Planner ツール](site-recovery-deployment-planner.md)を実行し、テスト フェールオーバーを使用して広範なアプリケーション テストを実行し、アプリの実際のパフォーマンスを把握することを強くお勧めします。

**レプリケーション ターゲット** | **レプリケーション元の平均ディスク I/O サイズ** |**レプリケーション元ディスクの平均データ変更頻度** | **レプリケーション元ディスクの 1 日あたりのデータ変更頻度合計**
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

- 前述の数値は、I/O のオーバーラップを 30% とした場合の平均値です。
- Site Recovery は、オーバーラップ比に基づくより高いスループットと、より大きな書き込みサイズ、そして実ワークロード I/O 動作を扱うことができます。
- これらの数値には、標準的なバックログとして約 5 分が想定されています。 つまりデータはアップロード後 5 分以内に処理されて復旧ポイントが作成されます。

## <a name="vault-tasks"></a>資格情報コンテナーのタスク

**アクション** | **サポートされています**
--- | ---
リソース グループ間の資格情報コンテナーの移動 | いいえ
サブスクリプション内およびサブスクリプション間でコンテナーの移動 | いいえ
リソース グループ間でストレージ、ネットワーク、Azure VM を移動 | いいえ
サブスクリプション内およびサブスクリプション間でストレージ、ネットワーク、Azure VM を移動 | いいえ


## <a name="obtain-latest-components"></a>最新のコンポーネントを入手する

**Name** | **説明** | **詳細**
--- | --- | ---
構成サーバー | オンプレミスにインストール済み。<br/> オンプレミスの VMware サーバーまたは物理マシンと Azure の間の通信を調整します。 | 構成サーバーに関する- [詳細情報](vmware-physical-azure-config-process-server-overview.md)をご覧ください。<br/> 最新バージョンへのアップグレードに関する- [詳細情報](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)をご覧ください。<br/> 構成サーバーの設定に関する- [詳細情報](vmware-azure-deploy-configuration-server.md)をご覧ください。 
プロセス サーバー | 構成サーバーに既定でインストールされます。<br/> レプリケーション データを受信し、そのデータをキャッシュ、圧縮、暗号化によって最適化して、Azure に送信します。<br/> デプロイの拡大に合わせて、増大するレプリケーション トラフィックの処理を実行するプロセス サーバーを追加できます。 | プロセス サーバーに関する- [詳細情報](vmware-physical-azure-config-process-server-overview.md)をご覧ください。<br/> 最新バージョンへのアップグレードに関する- [詳細情報](vmware-azure-manage-process-server.md#upgrade-a-process-server)をご覧ください。<br/> スケールアウト プロセス サーバーの設定に関する- [詳細情報](vmware-physical-large-deployment.md#set-up-a-process-server)をご覧ください。
モビリティ サービス | レプリケートする VMware VM または物理サーバーにインストールされます。<br/> オンプレミスの VMware サーバー/物理サーバーと Azure の間のレプリケーションを調整します。| モビリティ サービスに関する- [詳細情報](vmware-physical-mobility-service-overview.md)をご覧ください。<br/> 最新バージョンへのアップグレードに関する- [詳細情報](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal)をご覧ください。<br/> 



## <a name="next-steps"></a>次の手順
VMware VM のディザスター リカバリーのために Azure を準備する[方法を学びます](tutorial-prepare-azure.md)。

[9.27 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.26 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
