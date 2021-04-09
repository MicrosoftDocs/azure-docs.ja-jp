---
title: Azure Resource Mover を使用して別のリージョンに Azure VM を移動するためのサポート マトリックス
description: Azure Resource Mover を使用してリージョン間で Azure VM を移動するためのサポートを確認します。
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: how-to
ms.date: 02/08/2021
ms.author: raynew
ms.openlocfilehash: 3022b2d4954ffaef71e17ed28dd9b6f141d4da70
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99980492"
---
# <a name="support-for-moving-azure-vms-between-azure-regions"></a>Azure リージョン間で Azure VM を移動するためのサポート

この記事では、Resource Mover を使用して Azure リージョン間で仮想マシンと関連するネットワーク リソースを移動する際のサポートと前提条件について説明します。

> [!IMPORTANT]
> Azure Resource Mover は、現在プレビュー段階にあります。


## <a name="windows-vm-support"></a>Windows VM のサポート

Resource Mover では、次の Windows オペレーティング システムを実行する Azure VM がサポートされます。

**オペレーティング システム** | **詳細**
--- | ---
Windows Server 2019 | Server Core、Server with Desktop Experience でサポートされています。
Windows Server 2016  | サーバー コア、デスクトップ エクスペリエンス搭載サーバーに対してサポートされます。
Windows Server 2012 R2 | サポートされています。
Windows Server 2012 | サポートされています。
Windows Server 2008 R2 with SP1/SP2 | サポートされています。<br/><br/> Windows Server 2008 R2 SP1/SP2 を実行するコンピューターの場合、Windows の[サービス スタック更新プログラム (SSU)](https://support.microsoft.com/help/4490628) と [SHA-2 更新プログラム](https://support.microsoft.com/help/4474419)をインストールする必要があります。  SHA-1 は 2019 年 9 月からはサポートされておらず、SHA-2 コード署名が有効になっていない場合、エージェント拡張機能は正常にインストールまたはアップグレードされません。 SHA-2 のアップグレードと要件についての詳細は、[こちら](https://aka.ms/SHA-2KB)でご確認ください。
Windows 10 (x64) | サポートされています。
Windows 8.1 (x64) | サポートされています。
Windows 8 (x64) | サポートされています。
Windows 7 (x64) with SP1 以降 | Windows 7 SP1 を実行するコンピューターに、Windows の[サービス スタック更新プログラム (SSU)](https://support.microsoft.com/help/4490628) と [SHA-2 更新プログラム](https://support.microsoft.com/help/4474419)をインストールします。  SHA-1 は2019 年 9 月からはサポートされていないため、SHA-2 コード署名が有効になっていない場合、"準備" 手順は成功しません。 SHA-2 のアップグレードと要件についての詳細は、[こちら](https://aka.ms/SHA-2KB)でご確認ください。


## <a name="linux-vm-support"></a>Linux VM のサポート

Resource Mover では、次の Linux オペレーティング システムを実行する Azure VM がサポートされます。

**オペレーティング システム** | **詳細**
--- | ---
Red Hat Enterprise Linux | 6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5、7.6、[7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery)、[8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)8.1
CentOS | 6.5、6.6、6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5、7.6、7.7、8.0、8.1
Ubuntu 14.04 LTS Server | [サポートされるカーネル バージョン](#supported-ubuntu-kernel-versions)
Ubuntu 16.04 LTS Server | [サポートされるカーネル バージョン](#supported-ubuntu-kernel-versions)<br/><br/> パスワード ベースの認証とサインインを使用しており、cloud-init パッケージを使用してクラウド VM を構成する Ubuntu サーバーでは、(cloudinit 構成に応じて) フェールオーバー時にパスワード ベースのサインインが無効になっている場合があります。 パスワード ベースのサインインは、Azure portal で、フェールオーバーされた VM の [サポート] > [トラブルシューティング] > [設定] メニューからパスワードをリセットすることで、その仮想マシンで再度有効にできます。
Ubuntu 18.04 LTS Server | [サポートされるカーネル バージョン](#supported-ubuntu-kernel-versions)。
Debian 7 | [サポートされるカーネル バージョン](#supported-debian-kernel-versions)。
Debian 8 | [サポートされるカーネル バージョン](#supported-debian-kernel-versions)。
SUSE Linux Enterprise Server 12 | SP1、SP2、SP3、SP4 [サポートされるカーネル バージョン](#supported-suse-linux-enterprise-server-12-kernel-versions)
SUSE Linux Enterprise Server 15 | 15 および 15 SP1。 [(サポートされるカーネル バージョン)](#supported-suse-linux-enterprise-server-15-kernel-versions)
SUSE Linux Enterprise Server 11 | SP3
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4、6.5、6.6、6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5、7.6、[7.7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) <br/><br/> Red Hat と互換可能なカーネルまたは Unbreakable Enterprise カーネル リリース 3、4、5 (UEK3、UEK4、UEK5) を実行している


### <a name="supported-ubuntu-kernel-versions"></a>サポートされる Ubuntu カーネル バージョン

**リリース** | **カーネル バージョン** 
--- | --- 
14.04 LTS |  3.13.0-24-generic から 3.13.0-170-generic、<br/>3.16.0-25-generic ～ 3.16.0-77-generic、<br/>3.19.0-18-generic ～ 3.19.0-80-generic、<br/>4.2.0-18-generic ～ 4.2.0-42-generic、<br/>4.4.0-21-generic から 4.4.0-148-generic、<br/>4.15.0-1023-azure から 4.15.0-1045-azure 
16.04 LTS |  4.4.0-21-generic から 4.4.0-171-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-45-generic、<br/>4.15.0-13-generic から 4.15.0-74-generic<br/>4.11.0-1009-azure ～ 4.11.0-1016-azure、<br/>4.13.0-1005-azure から 4.13.0-1018-azure <br/>4.15.0-1012-azure から 4.15.0-1066-azure
18.04 LTS | 4.15.0-20-generic から 4.15.0-74-generic </br> 4.18.0-13-generic から 4.18.0-25-generic </br> 5.0.0-15-generic から 5.0.0-37-generic </br> 5.3.0-19-generic から 5.3.0-24-generic </br> 4.15.0-1009-azure から 4.15.0-1037-azure </br> 4.18.0-1006-azure から 4.18.0-1025-azure </br> 5.0.0-1012-azure から 5.0.0-1028-azure </br> 5.3.0-1007-azure から 5.3.0-1009-azure


### <a name="supported-debian-kernel-versions"></a>サポートされる Debian カーネル バージョン 

**リリース** |  **カーネル バージョン** 
--- |  --- 
Debian 7 |  3.2.0-4-amd64 から 3.2.0-6-amd64、3.16.0-0.bpo.4-amd64 
Debian 8 |  3.16.0-4-amd64 から 3.16.0-10-amd64、4.9.0-0.bpo.4-amd64 から 4.9.0-0.bpo.11-amd64 
Debian 8 |  3.16.0-4-amd64 から 3.16.0-10-amd64、4.9.0-0.bpo.4-amd64 から 4.9.0-0.bpo.9-amd64

### <a name="supported-suse-linux-enterprise-server-12-kernel-versions"></a>サポートされる SUSE Linux Enterprise Server 12 カーネル バージョン 

**リリース** | **カーネル バージョン** 
--- |  --- 
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3、SP4) |  すべての [SUSE 12 SP1、SP2、SP3、SP4 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.4.138-4.7-azure から 4.4.180-4.31-azure、</br>4.12.14-6.3-azure から 4.12.14-6.34-azure  


### <a name="supported-suse-linux-enterprise-server-15-kernel-versions"></a>サポートされる SUSE Linux Enterprise Server 15 カーネル バージョン

**リリース** | **カーネル バージョン** |
--- |  --- |
SUSE Linux Enterprise Server 15 および 15 SP1 |  すべての SUSE 15 および 15 ストック カーネルがサポートされます。</br></br> 4.12.14-5.5-azure から 4.12.14-8.22-azure |

## <a name="supported-linux-file-systemguest-storage"></a>サポートされる Linux ファイル システム/ゲスト ストレージ

* ファイル システム: ext3、ext4、XFS、BTRFS
* ボリューム マネージャー:LVM2
* マルチパス ソフトウェア:デバイス マッパー


## <a name="supported-vm-compute-settings"></a>サポートされる VM コンピューティング設定

**設定** | **サポート** | **詳細**
--- | --- | ---
サイズ | 少なくとも 2 つの CPU コアと 1 GB の RAM を備えた任意の Azure VM サイズ | [Azure 仮想マシンのサイズ](../virtual-machines/sizes-general.md)を確認してください。
可用性セット | サポートされています | サポートされています。
可用性ゾーン | サポートされています | ターゲット リージョンのサポートによってはサポートされます。
Azure ギャラリー イメージ (Microsoft が公開) | サポートされています | サポート対象のオペレーティング システムで VM が実行されている場合にサポートされます。
Azure ギャラリー イメージ (サード パーティが公開)  | サポートされています | サポート対象のオペレーティング システムで VM が実行されている場合にサポートされます。
カスタム イメージ (サード パーティが公開)| サポートされています | サポート対象のオペレーティング システムで VM が実行されている場合にサポートされます。
Site Recovery を使用する VM | サポートされていません | バックエンドで Site Recovery を使用して、VM のリージョン間でリソースを移動します。 Site Recovery を既に使用している場合は、レプリケーションを無効にしてから準備プロセスを開始します。
Azure RBAC ポリシー | サポートされていません | VM の Azure ロールベースのアクセス制御 (Azure RBAC) ポリシーは、ターゲット リージョンの VM にコピーされません。
拡張機能 | サポートされていません | 拡張機能はターゲット リージョンの VM にコピーされません。 移動の完了後に手動でインストールします。


## <a name="supported-vm-storage-settings"></a>サポートされる VM ストレージ設定

この表は、Azure VM の OS ディスク、データ ディスク、一時ディスクに対するサポートをまとめたものです。 [マネージド ディスク](../virtual-machines/disks-scalability-targets.md)でのパフォーマンスの問題を回避するため、VM ディスクの制限とターゲットを監視することが重要です。

> [!NOTE]
> ターゲット VM のサイズは、ソース VM と同じかそれ以上である必要があります。 検証に使用されるパラメーターは、データ ディスクの数、NIC の数、使用可能な CPU、GB 単位のメモリです。 不適切な場合は、エラーが発行されます。


**コンポーネント** | **サポート** | **詳細**
--- | --- | ---
OS ディスクの最大サイズ | 2048 GB | VM ディスクに関する[詳細を表示します](../virtual-machines/managed-disks-overview.md)。
一時ディスク | サポートされていません | 一時ディスクは準備プロセスから常に除外されます。<br/><br/> 一時ディスクに永続データを格納しないでください。 [詳細については、こちらを参照してください](../virtual-machines/managed-disks-overview.md#temporary-disk)。
データ ディスクの最大サイズ | マネージド ディスクの場合は 8,192 GB
データ ディスクの最小サイズ |  マネージド ディスクの場合は 2 GB |
データ ディスクの最大数 | 最大 64 (特定の Azure VM サイズでのサポートに従います) | VM サイズに関する[詳細を表示します](../virtual-machines/sizes.md)。
データ ディスクの変更レート | Premium ストレージではディスクあたり最大 10 MBps。 Standard ストレージではディスクあたり最大 2 MBps。 | ディスクでの平均データ変更レートが継続的に最大値より高い場合、準備が追いつかなくなります。<br/><br/>  ただし、上限の超過が散発的である場合は、準備が追いつきます。しかし、復旧ポイントがわずかに遅延することもあります。
データ ディスク (Standard ストレージ アカウント) | サポートされていません。 | ストレージの種類をマネージド ディスクに変更してから、VM を移動してみてください。
データ ディスク (Premium ストレージ アカウント) | サポートされていません | ストレージの種類をマネージド ディスクに変更してから、VM を移動してみてください。
マネージド ディスク (Standard) | サポートされています  |
マネージド ディスク (Premium) | サポートされています |
Standard SSD | サポートされています |
第 2 世代 (UEFI ブート) | サポートされています
ブート診断ストレージ アカウント | サポートされていません | ターゲット リージョンに VM を移動した後で再度有効にします。
Azure Disk Encryption が有効になっている VM | サポートされています | [詳細情報](tutorial-move-region-encrypted-virtual-machines.md)
カスタマー マネージド キーによるサーバー側暗号化を使用する VM | サポートされています | [詳細情報](tutorial-move-region-encrypted-virtual-machines.md)

### <a name="limits-and-data-change-rates"></a>制限とデータ変化率

次の表は、テストに基づく制限をまとめたものです。 これらは、考えられるすべてのアプリケーション I/O の組み合わせに対応しているわけではありません。 実際の結果は、ご使用のアプリケーションで発生するさまざまな I/O によって異なります。 ディスクごとのデータ チャーンと VM ごとのデータ チャーンという考慮すべき 2 つの制限があります。

**ストレージ ターゲット** | **レプリケーション元の平均ディスク I/O** |**レプリケーション元ディスクの平均データ変更頻度** | **レプリケーション元ディスクの 1 日あたりのデータ変更頻度合計**
---|---|---|---
Standard Storage | 8 KB    | 2 MB/秒 | (ディスクあたり) 168 GB
Premium P10 または P15 ディスク | 8 KB    | 2 MB/秒 | (ディスクあたり) 168 GB
Premium P10 または P15 ディスク | 16 KB | 4 MB/秒 |    (ディスクあたり) 336 GB
Premium P10 または P15 ディスク | 32 KB 以上 | 8 MB/秒 | (ディスクあたり) 672 GB
Premium P20、P30、P40、または P50 ディスク | 8 KB    | 5 MB/s | (ディスクあたり) 421 GB
Premium P20、P30、P40、または P50 ディスク | 16 KB 以上 |20 MB/秒 | (ディスクあたり) 1,684 GB

## <a name="supported-vm-networking-settings"></a>サポートされる VM ネットワーク設定

**設定** | **サポート** | **詳細**
--- | --- | ---
NIC | サポートされています | ターゲット リージョンで既存のリソースを指定するか、準備プロセス中に新しいリソースを作成します。 
内部ロード バランサー | サポートされています | ターゲット リージョンで既存のリソースを指定するか、準備プロセス中に新しいリソースを作成します。  
パブリック ロード バランサー | サポートされています | ターゲット リージョンで既存のリソースを指定するか、準備プロセス中に新しいリソースを作成します。  
パブリック IP アドレス | サポートされています | ターゲット リージョンで既存のリソースを指定するか、準備プロセス中に新しいリソースを作成します。<br/><br/> パブリック IP アドレスはリージョン固有であり、移動後のターゲット リージョンには保持されません。 ターゲットの場所でネットワーク設定 (負荷分散規則を含む) を変更する場合は、この点に注意してください。
ネットワーク セキュリティ グループ | サポートされています | ターゲット リージョンで既存のリソースを指定するか、準備プロセス中に新しいリソースを作成します。  
予約済みの (静的) IP アドレス | サポートされています | 現在、これは構成できません。 既定値はソース値です。 <br/><br/> ソース VM で NIC が静的 IP を使用していて、ターゲット サブネットで同じ IP アドレスを使用できる場合、そのアドレスはターゲット VM に割り当てられます。<br/><br/> ターゲット サブネットで同じ IP アドレスを使用できない場合、VM 移動の開始は失敗します。
動的 IP アドレス | サポートされています | 現在、これは構成できません。 既定値はソース値です。<br/><br/> ソース VM で NIC に動的 IP アドレス指定が設定されている場合、ターゲット VM の NIC も既定で動的になります。
IP 構成 | サポートされています | 現在、これは構成できません。 既定値はソース値です。

## <a name="outbound-access-requirements"></a>送信アクセスの要件

移動する Azure VM には、送信アクセスが必要です。


### <a name="url-access"></a>URL アクセス

 アウトバウンド接続を制御するために URL ベースのファイアウォール プロキシを使用している場合、以下の URL へのアクセスを許可してください。

**名前** | **Azure パブリック クラウド** | **詳細** 
--- | --- | --- 
ストレージ | `*.blob.core.windows.net`  | ソース リージョンのキャッシュ ストレージ アカウントに、VM からデータが書き込まれるよう許可します。 
Azure Active Directory | `login.microsoftonline.com`  | Site Recovery サービス URL に対する承認と認証を提供します。 
レプリケーション | `*.hypervrecoverymanager.windowsazure.com` | VM と Site Recovery サービスの通信を許可します。 
Service Bus | `*.servicebus.windows.net` | VM による Site Recovery の監視および診断データの書き込みを許可します。 

## <a name="nsg-rules"></a>NSG ルール
ネットワーク セキュリティ グループ (NSG) ルールを使用して送信接続を制御する場合は、これらの[サービス タグ](../virtual-network/service-tags-overview.md) ルールを作成します。 各ルールで、HTTPS (443) での送信アクセスを許可する必要があります。
- ソース リージョンのストレージ タグ ルールを作成します。
- 任意のリージョンの Site Recovery サービスへのアクセスを許可する *AzureSiteRecovery* タグ ルールを作成します。 このタグは、次の他のタグに依存しているため、これらのためのルールを作成する必要があります。
    - *AzureActiveDirectory*
    - **EventHub*
    - *AzureKeyVault*
    - *GuestAndHybridManagement*
- 非運用環境でルールをテストすることをお勧めします。 [例をいくつか確認してください](../site-recovery/azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)。 


## <a name="next-steps"></a>次の手順

Resource Mover を使用して、別のリージョンへの [Azure VM の移動](tutorial-move-region-virtual-machines.md)を試行する。
