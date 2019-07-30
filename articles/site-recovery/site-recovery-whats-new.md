---
title: Azure Site Recovery の最新情報 | Microsoft Docs
description: Azure Site Recovery で導入された新機能の概要を紹介します
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: raynew
ms.openlocfilehash: 0bfaf848b0b700c8d745acf8c6a63c898dc91140
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272876"
---
# <a name="whats-new-in-site-recovery"></a>Site Recovery の最新情報

[Azure Site Recovery](site-recovery-overview.md) サービスは、継続的に更新され、改善されています。 最新情報を入手し続ける助けになるように、この記事では、最新のリリース、新機能、および新しいコンテンツに関する情報を提供します。 このページは定期的に更新されます。

Site Recovery の機能について提案がおありの場合は、ぜひ[フィードバックをお聞かせください](https://feedback.azure.com/forums/256299-site-recovery)。


## <a name="updates-june-2019"></a>更新プログラム (2019年 6 月)

### <a name="update-rollup-37"></a>更新プログラム ロールアップ 37

[更新プログラム ロールアップ 37](https://support.microsoft.com/help/4508614/) では、次の更新プログラムが提供されます。

**Update** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)
**問題の修正/改善点** | さまざまな修正プログラムと機能強化 (詳細はロールアップを参照)


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/物理サーバーのディザスター リカバリー

今月追加された機能は、表にまとめられています。

**機能** | **詳細**
--- | ---
**GPT パーティション** | 更新プログラム ロールアップ 37 以降 (モビリティ サービス バージョン 9.25.5241.1) では、UEFI で最大 5 つの GPT パーティションがサポートされます。 この更新プログラム以前は、4 つサポートされていました。



## <a name="updates-may-2019"></a>更新プログラム (2019 年 5 月)

### <a name="update-rollup-36"></a>更新プログラム ロールアップ 36

[更新プログラム ロールアップ 36](https://support.microsoft.com/help/4503156) では、次の更新プログラムが提供されます。

**Update** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)
**問題の修正/改善点** | さまざまな修正プログラムと機能強化 (詳細はロールアップを参照)

### <a name="azure-vm-disaster-recovery"></a>Azure VM のディザスター リカバリー

今月追加された機能は、表にまとめられています。

**機能** | **詳細**
--- | ---
**追加されたディスクのレプリケート** | 既にディザスター リカバリーが有効になっている Azure VM に追加されたデータ ディスクのレプリケーションを有効にします。 [詳細情報](azure-to-azure-enable-replication-added-disk.md)。
**自動更新** | ディザスター リカバリーが有効になっている Azure VM で実行されるモビリティ サービス拡張機能の自動更新を構成するときに、Site Recovery によって作成される既定のアカウントを使用する代わりに、既存の Automation アカウントを選択できるようになりました。 [詳細情報](azure-to-azure-autoupdate.md)。


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/物理サーバーのディザスター リカバリー

今月追加された機能は、表にまとめられています。

**機能** | **詳細**
--- | ---
**プロセス サーバーの監視** | オンプレミスの VMware VM と物理サーバーのディザスター リカバリーについて、強化されたサーバー正常性レポートおよびアラート機能により、プロセス サーバーの問題を監視してトラブルシューティングします。 [詳細情報](vmware-physical-azure-monitor-process-server.md)。 





## <a name="updates-march-2019"></a>更新プログラム (2019 年 3 月)

### <a name="update-rollup-35"></a>更新プログラム ロールアップ 35

[更新プログラム ロールアップ 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) では、次の更新プログラムが提供されます。

**Update** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)
**問題の修正/改善点** | さまざまな修正プログラムと機能強化 (詳細はロールアップを参照)

### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/物理サーバーのディザスター リカバリー

今月追加された機能は、表にまとめられています。

**機能** | **詳細**
--- | ---
**マネージド ディスク** | オンプレミスの VMware VM および物理サーバーが、Azure のマネージド ディスクに直接レプリケートされるようになりました。 オンプレミス データは Azure のキャッシュ ストレージ アカウントに送信され、復旧ポイントはターゲットの場所のマネージド ディスクで作成されます。 これにより、複数のターゲット ストレージ アカウントを管理する必要がなくなります。
**構成サーバー** | Site Recovery で、複数の NIC がある構成サーバーがサポートされるようになりました。 コンテナーに構成サーバーを登録する前に、構成サーバー VM にさらにアダプターを追加する必要があります。 後で追加する場合は、サーバーをコンテナーに再登録する必要があります。


## <a name="updates-february-2019"></a>更新プログラム (2019 年 2 月)

### <a name="update-rollup-34"></a>更新プログラム ロールアップ 34 

[更新プログラム ロールアップ 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) は次の更新プログラムを提供します。

**Update** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)。
**問題の修正/改善点** | さまざまな修正と改善点 (詳細はロールアップを参照)。


### <a name="update-rollup-33"></a>更新プログラム ロールアップ 33 

[更新プログラム ロールアップ 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) は次の更新プログラムを提供します。

**Update** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)。
**問題の修正/改善点** | さまざまな修正と改善点 (詳細はロールアップを参照)。


### <a name="azure-vm-disaster-recovery"></a>Azure VM のディザスター リカバリー 
今月追加された機能は、表にまとめられています。

**機能** | **詳細**
--- | ---
**ネットワーク マッピング** | Azure VM ディザスター リカバリーで、レプリケーションを有効にすると、利用可能な任意のターゲット ネットワークを使用できるようになりました。 
**Standard SSD** | [Standard SSD ディスク](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)を使用して Azure VM のディザスター リカバリーを設定できるようになりました。
**記憶域スペース ダイレクト** | Azure VM アプリで実行されるアプリのディザスター リカバリーを、高可用性を実現するために[記憶域スペース ダイレクト](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)を使用して設定できるようになりました。  Site Recovery と共に記憶域スペース ダイレクト (S2D) を使用することで、Azure VM ワークロードを包括的に保護することができます。 S2D では、Azure でゲスト クラスターをホストすることができます。 これは特に、SAP ASCS レイヤー、SQL Server、スケールアウト ファイル サーバーなど、重要なアプリケーションを VM でホストするときに便利です。


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/物理サーバーのディザスター リカバリー
今月追加された機能は、表にまとめられています。

**機能** | **詳細**
--- | ---
**Linux BRTFS ファイル システム** | Site Recovery では、BRTFS ファイル システムでの VMware VM のレプリケーションがサポートされるようになりました。 次のような場合、レプリケーションはサポートされません。<br/><br/>- レプリケーションを有効にした後、BTRFS ファイル システムのサブボリュームが変更されている。<br/><br/>- ファイル システムが複数のディスクに分散されている。<br/><br/>- BTRFS ファイル システムで RAID がサポートされている。
**Windows Server 2019** | Windows Server 2019 を実行しているコンピューターのサポートが追加されました。


## <a name="updates-january-2019"></a>更新プログラム (2019 年 1 月)


### <a name="accelerated-networking-azure-vms"></a>高速ネットワーク (Azure VM)

高速ネットワークによって、VM へのシングル ルート I/O 仮想化 (SR-IOV) が可能になり、ネットワークのパフォーマンスが向上します。 Azure VM のレプリケーションを有効にすると、Site Recovery は、高速ネットワークが有効になっているかどうかを検出します。 有効な場合はフェールオーバー後に、[Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) と [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms) の両方について、Site Recovery によってターゲットのレプリカ Azure VM 上に高速ネットワークが自動的に構成されます。

[詳細情報](azure-vm-disaster-recovery-with-accelerated-networking.md)。

### <a name="update-rollup-32"></a>更新プログラム ロールアップ 32 

[更新プログラム ロールアップ 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) では、次の更新プログラムが提供されます。

**Update** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)。
**問題の修正/改善点** | さまざまな修正と改善点 (詳細はロールアップを参照)。

### <a name="azure-vm-disaster-recovery"></a>Azure VM のディザスター リカバリー

今月追加された機能は、表にまとめられています。

**機能** | **詳細**
--- | ---
**Linux のサポート** | RedHat Workstation 6/7、および Ubuntu、Debian、SUSE 用の新しいカーネル バージョンのサポートが追加されました。
**記憶域スペース ダイレクト** | Site Recovery では、記憶域スペース ダイレクト (S2D) を使用する Azure VM がサポートされます。

### <a name="vmware-vmsphysical-servers-disaster-recovery"></a>VMware VM/物理サーバーのディザスター リカバリー

今月追加された機能は、表にまとめられています。
 
**機能** | **詳細**
--- | ---
**Linux のサポート** | Redhat Enterprise Linux 7.6、RedHat Workstation 6/7、Oracle Linux 6.10/7.6、Ubuntu、Debian、SUSE 用の新しいカーネル バージョンのサポートが追加されました。


### <a name="update-rollup-31"></a>更新プログラム ロールアップ 31 

[更新プログラム ロールアップ 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) は次の更新プログラムを提供します。

**Update** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)。
**問題の修正/改善点** | さまざまな修正と改善点 (詳細はロールアップを参照)。

### <a name="vmware-vmsphysical-servers-replication"></a>VMware VM/物理サーバーのレプリケーション 
今月追加された機能は、表にまとめられています。
**機能** | **詳細**
--- | ---
**Linux のサポート** | Oracle Linux 6.8 と 6.9/7.0、および UEK5 カーネルのサポートが追加されました。
**LVM** | LVM ボリュームと LVM2 ボリュームのサポートが追加されました。<br/><br/> ディスク パーティションや LVM ボリューム上の /boot ディレクトリがサポートされるようになりました。
**Directories** | 個別のパーティション、または同一のシステム ディスク上にないファイル システムとして設定された以下のディレクトリのサポートが追加されました。<br/><br/> /(ルート)、/boot、/usr、/usr/local、/var、/etc
**Windows Server 2008** | ダイナミック ディスクのサポートが追加されました。
**フェールオーバー** | storvsc と vsbus がブート ドライバーではない VMware VM のフェールオーバー時間が改善されました。
**UEFI のサポート** | Azure VM は UEFI というブートの種類をサポートしていません。 UEFI が設定されたオンプレミスの物理サーバーを、Site Recovery を使用して Azure に移行できるようになりました。 Site Recovery は、移行の前にブートの種類を BIOS に変換することにより、このようなサーバーを移行します。 Site Recovery では、以前は、VM の場合のみこの変換をサポートしていました。 現在は、Windows Server 2012 移行を実行する物理サーバーもサポートされています。

### <a name="azure-vm-disaster-recovery"></a>Azure VM のディザスター リカバリー
今月追加された機能は、表にまとめられています。

**機能** | **詳細**
--- | ---
**Linux のサポート** | Oracle Linux 6.8 と 6.9/7.0、および UEK5 カーネルのサポートが追加されました。
**Linux BRTFS ファイル システム** | Azure VM でサポートされるようになりました。
**可用性ゾーンにある Azure VM** | 可用性ゾーンにデプロイされた Azure VM の別のリージョンへのレプリケーションを有効にできます。 Azure VM のレプリケーションを有効にして、フェールオーバーのターゲットを、1 つの VM インスタンス、可用性セット内の VM、または可用性ゾーン内の VM に設定できるようになりました。 この設定は、レプリケーションに影響を与えません。 お知らせを[読む](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/)
**ファイアウォールが有効なストレージ (ポータル/PowerShell)** | [ファイアウォールが有効なストレージ アカウント](https://docs.microsoft.com/azure/storage/common/storage-network-security)のサポートが追加されました。<br/><br/> ファイアウォールが有効なストレージ アカウント上のアンマネージド ディスクを持つ Azure VM を、ディザスター リカバリーのために別の Azure リージョンにレプリケートできます。<br/><br/> ファイアウォールが有効なストレージ アカウントを、アンマネージド ディスクのターゲット ストレージ アカウントとして使用できます。<br/><br/> ポータルでサポートされ、PowerShell の使用がサポートされます。

## <a name="updates-december-2018"></a>更新プログラム (2018 年 12 月)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>モビリティ サービスの自動更新 (Azure VM)

Site Recovery では、モビリティ サービス拡張機能に対する自動更新のオプションが追加されました。 モビリティ サービス拡張機能は、Site Recovery によってレプリケートされる各 Azure VM にインストールされます。 レプリケーションを有効にするときには、拡張機能に対する更新の管理を Site Recovery に許可するかどうかを選択します。

更新では VM の再起動は必要とされず、レプリケーションに影響はありません。 [詳細情報](azure-to-azure-autoupdate.md)。

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Azure VM ディザスター リカバリー用の料金計算ツール

Azure VM のディザスター リカバリーでは、VM のライセンス コストのほか、ネットワークとストレージのコストが発生します。 Azure では、これらのコストの把握に役立つ[料金計算ツール](https://aka.ms/a2a-cost-estimator)を提供しています。 Site Recovery では、6 個の VM、12 個の Standard HDD ディスク、6 個の Premium SSD ディスクを使用する 3 層アプリに基づいたサンプル デプロイに値段を付ける、[価格見積もりの例](https://aka.ms/a2a-cost-estimator)が提供されるようになりました。

- このサンプルでは、1 日に Standard では 10 GB、Premium では 20 GB のデータ変更を想定しています。
- 特定のデプロイについては、変数を変更してコストを見積もることができます。
- VM の数、マネージド ディスクの数と種類、VM 全体で予想される総データ変更率を指定します。
- さらに、帯域幅コストを見積もるための圧縮率を適用できます。

お知らせを[読む](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)


## <a name="updates-october-2018"></a>更新プログラム (2018 年 10 月)

### <a name="update-rollup-30"></a>更新プログラム ロールアップ 30 

[更新プログラム ロールアップ 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) は次の更新プログラムを提供します。

**Update** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)。
**問題の修正/改善点** | さまざまな修正と改善点 (詳細はロールアップを参照)。

### <a name="azure-vm-disaster-recovery"></a>Azure VM のディザスター リカバリー
今月追加された機能は、表にまとめられています。

**機能** | **詳細**
--- | ---
**リージョンのサポート** | オーストラリア中部 1 およびオーストラリア中部 2 で Site Recovery のサポートが追加されました。
**ディスク暗号化のサポート** | Azure AD アプリを使用した、Azure Disk Encryption (ADE) で暗号化された Azure VM のディザスター リカバリーのサポートが追加されました。 [詳細情報](azure-to-azure-how-to-enable-replication-ade-vms.md)。
**ディスクの除外** | 初期化されていないディスクが、Azure VM のレプリケーション中に自動的に除外されるようになりました。
**ファイアウォールが有効なストレージ (PowerShell)** | [ファイアウォールが有効なストレージ アカウント](https://docs.microsoft.com/azure/storage/common/storage-network-security)のサポートが追加されました。<br/><br/> ファイアウォールが有効なストレージ アカウント上のアンマネージド ディスクを持つ Azure VM を、ディザスター リカバリーのために別の Azure リージョンにレプリケートできます。<br/><br/> ファイアウォールが有効なストレージ アカウントを、アンマネージド ディスクのターゲット ストレージ アカウントとして使用できます。<br/><br/> PowerShell のみの使用がサポートされるようになりました。


### <a name="update-rollup-29"></a>更新プログラム ロールアップ 29 

[更新プログラム ロールアップ 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) は次の更新プログラムを提供します。

**Update** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)。
**問題の修正/改善点** | さまざまな修正と改善点 (詳細はロールアップを参照)。


## <a name="updates-august-2018"></a>更新プログラム (2018 年 8 月)

### <a name="update-rollup-28"></a>更新プログラム ロールアップ 28 

[更新プログラム ロールアップ 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) は次の更新プログラムを提供します。

**Update** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)。
**問題の修正/改善点** | さまざまな修正と改善点 (詳細はロールアップを参照)。

### <a name="azure-vms-disaster-recovery"></a>Azure VM のディザスター リカバリー 
今月追加された機能は、表にまとめられています。

**機能** | **詳細**
--- | ---
**Linux のサポート** | RedHat Enterprise Linux 6.10; CentOS 6.10 のサポートが追加されました。<br/><br/>
**クラウドのサポート** | ドイツのクラウドで Azure VM のディザスター リカバリーがサポートされるようになりました。
**サブスクリプション間のディザスター リカバリー** | 同じ Azure Active Directory テナント内の異なるサブスクリプションで、別のリージョンに Azure VM をレプリケートすることがサポートされるようになりました。 [詳細情報](https://aka.ms/cross-sub-blog)。

### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware VM/物理サーバーのディザスター リカバリー 
今月追加された機能は、表にまとめられています。

**機能** | **詳細**
--- | ---
**Linux のサポート** | RedHat Enterprise Linux 6.10、CentOS 6.10 のサポートが追加されました。<br/><br/> レガシ BIOS 互換モードで GUID パーティション テーブル (GPT) のパーティション スタイルを使用する Linux ベースの VM がサポートされるようになりました。 詳細については、[Azure VM の FAQ](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks) を確認してください。 
**移行後の VM のディザスター リカバリー** | Azure に移行されたオンプレミスの VMware VM のセカンダリ リージョンへのディザスター リカバリーの有効化がサポートされます。レプリケーションを有効にする前に VM でモビリティ サービスをアンインストールする必要はありません。
**Windows Server 2008** | Windows Server 2008 R2/2008 64 ビット版および 32 ビット版を実行するマシンの移行がサポートされるようになりました。<br/><br/> 移行のみ (レプリケーションとフェールオーバー)。 フェールバックはサポートされていません。

## <a name="updates-july-2018"></a>更新プログラム (2018 年 7 月)

### <a name="update-rollup-27-july-2018"></a>更新プログラム ロールアップ 27 (2018 年 7 月)

[更新プログラム ロールアップ 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) は次の更新プログラムを提供します。

**Update** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)。
**問題の修正/改善点** | さまざまな修正と改善点 (詳細はロールアップを参照)。

### <a name="azure-vms-disaster-recovery"></a>Azure VM のディザスター リカバリー 

今月追加された機能は、表にまとめられています。

**機能** | **詳細**
--- | ---
**Linux のサポート** | Red Hat Enterprise Linux 7.5 のサポートが追加されました。

### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware VM/物理サーバーのディザスター リカバリー 

今月追加された機能は、表にまとめられています。

**機能** | **詳細**
--- | ---
**Linux のサポート** | Red Hat Enterprise Linux 7.5、SUSE Linux Enterprise Server 12 のサポートが追加されました。



## <a name="next-steps"></a>次の手順

[Azure の更新情報](https://azure.microsoft.com/updates/?product=site-recovery)に関するページで、更新プログラムの最新情報を常に把握してください。
