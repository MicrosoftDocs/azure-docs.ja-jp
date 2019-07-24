---
title: Azure Site Recovery の最新情報 | Microsoft Docs
description: Azure Site Recovery で導入された新機能の概要を紹介します
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: raynew
ms.openlocfilehash: fece1adc9ad2f5844a7d6cc1e0e9cc92d44de6d8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57892933"
---
# <a name="whats-new-in-site-recovery"></a>Site Recovery の最新情報

[Azure Site Recovery](site-recovery-overview.md) サービスは、継続的に更新され、改善されています。 最新情報を入手し続ける助けになるように、この記事では、最新のリリース、新機能、および新しいコンテンツに関する情報を提供します。 このページは定期的に更新されます。

Site Recovery の機能について提案がおありの場合は、ぜひ[フィードバックをお聞かせください](https://feedback.azure.com/forums/256299-site-recovery)。

## <a name="q1-2019"></a>Q1 2019 

### <a name="update-rollup-34-february-2019"></a>更新プログラム ロールアップ 34 (2019 年 2 月)

[更新プログラム ロールアップ 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) は次の更新プログラムを提供します。

**Update** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)
**問題の修正** | さまざまな修正プログラムと機能強化 (詳細はロールアップを参照)



### <a name="update-rollup-33-february-2019"></a>更新プログラム ロールアップ 33 (2019 年 2 月)

[更新プログラム ロールアップ 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) は次の更新プログラムを提供します。

**Update** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)
**問題の修正** | さまざまな修正プログラムと機能強化 (詳細はロールアップを参照)
**ネットワーク マッピング** | Azure VM ディザスター リカバリーで、レプリケーションを有効にすると、利用可能な任意のターゲット ネットワークを使用できるようになりました。 
**Standard SSD** | [Standard SSD ディスク](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)を使用して Azure VM のディザスター リカバリーを設定できるようになりました。
**記憶域スペース ダイレクト** | Azure VM アプリで実行されるアプリのディザスター リカバリーを、高可用性を実現するために[記憶域スペース ダイレクト](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)を使用して設定できるようになりました。
**BRTFS ファイル システム** | Azure VM に加えて、VMware VM でもサポートされるようになりました。<br/><br/> 次の場合はサポート対象外です。BTRFS ファイル システムのサブボリュームが、レプリケーションを有効にした後で変更されている場合、ファイル システムが複数のディスクにまたがっている場合、BTRFS ファイル システムが RAID をサポートする場合。



### <a name="update-rollup-32-january-2019"></a>更新プログラム ロールアップ 32 (2019 年 1 月)

[更新プログラム ロールアップ 31](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) は次の更新プログラムを提供します。

**Update** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)
**問題の修正** | さまざまな修正プログラムと機能強化 (詳細はロールアップを参照)
**Linux 用のディザスター リカバリー** | **Azure VM**: RedHat Workstation 6/7; Ubuntu、Debian、SUSE 用の新しいカーネル バージョンのサポート。<br/><br/> **VMware VM および物理サーバー**:Redhat Enterprise Linux 7.6; RedHat Workstation 6/7; Oracle Linux 6.10/7.6; Ubuntu、Debian、SUSE 用の新しいカーネル バージョンのサポート。


### <a name="update-rollup-31-january-2019"></a>更新プログラム ロールアップ 31 (2019 年 1 月)

[更新プログラム ロールアップ 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) は次の更新プログラムを提供します。

**Update** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)
**問題の修正** | さまざまな修正プログラムと機能強化 (詳細はロールアップを参照)
**Linux 用のディザスター リカバリー** | **Azure VM**: Oracle Linux 6.8 および 6.9/7.0; UEK5 カーネルのサポート。<br/><br/> **VMware VM および物理サーバー**:Oracle Linux 6.8 および 6.9/7.0; UEK5 カーネルのサポート。
**BRTFS ファイル システム** | Azure VM でサポートされるようになりました。
**LVM** | LVM ボリュームと LVM2 ボリュームのサポートが追加されました。<br/><br/> ディスク パーティションや LVM ボリューム上の /boot ディレクトリがサポートされています。
**Directories** | 個別のパーティション、または同一のシステム ディスク上にないファイル システムとして設定された、/(ルート)、/boot、/usr、/usr/local、/var、/etc ディレクトリのサポートが追加されました。
**Windows Server 2008** | ダイナミック ディスクのサポートが追加されました。
**VMware VM のフェールオーバー** | storvsc と vsbus がブート ドライバーではない VMware VM のフェールオーバー時間が改善されました。
**UEFI のサポート** | Azure VM は UEFI というブートの種類をサポートしていません。 UEFI が設定されたオンプレミスの物理サーバーを、Site Recovery を使用して Azure に移行できるようになりました。 Site Recovery は、移行の前にブートの種類を BIOS に変換することにより、このようなサーバーを移行します。 Site Recovery では、以前は、VM の場合のみこの変換をサポートしていました。 現在は、Windows Server 2012 移行を実行する物理サーバーもサポートされています。
**可用性ゾーンにある Azure VM** | 可用性ゾーンにデプロイされた Azure VM の別のリージョンへのレプリケーションを有効にできます。 Azure VM のレプリケーションを有効にして、フェールオーバーのターゲットを、1 つの VM インスタンス、可用性セット内の VM、または可用性ゾーン内の VM に設定できるようになりました。 この設定は、レプリケーションに影響を与えません。 お知らせを[読む](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/)


## <a name="q4-2018"></a>Q4 2018

### <a name="update-rollup-30-october-2018"></a>更新プログラム ロールアップ 30 (2018 年 10 月)

[更新プログラム ロールアップ 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) は次の更新プログラムを提供します。

**Update** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)
**問題の修正** | さまざまな修正プログラムと機能強化 (詳細はロールアップを参照)
**リージョンのサポート** | オーストラリア中部 1 およびオーストラリア中部 2 で Site Recovery のサポートが追加されました。
**ディスク暗号化のサポート** | Azure AD アプリを使用した、Azure Disk Encryption (ADE) で暗号化された Azure VM のディザスター リカバリーのサポートが追加されました。 [詳細情報](azure-to-azure-how-to-enable-replication-ade-vms.md)。
**ディスクの除外** | 初期化されていないディスクは、Azure VM のレプリケーション中に自動的に除外されるようになりました。
**ファイアウォールが有効なストレージ** | [ファイアウォールが有効なストレージ アカウント](https://docs.microsoft.com/azure/storage/common/storage-network-security)のサポートが追加されました。<br/><br/> ファイアウォールが有効なストレージ アカウント上のアンマネージド ディスクを持つ Azure VM を、ディザスター リカバリーのために別の Azure リージョンにレプリケートできます。<br/><br/> ファイアウォールが有効なストレージ アカウントを、アンマネージド ディスクのターゲット ストレージ アカウントとして使用できます。<br/><br/> PowerShell のみの使用がサポートされるようになりました。


### <a name="update-rollup-29-october-2018"></a>更新プログラム ロールアップ 29 (2018 年 10 月)

[更新プログラム ロールアップ 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) は次の更新プログラムを提供します。

**Update** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)
**問題の修正** | さまざまな修正プログラムと機能強化 (詳細はロールアップを参照)

### <a name="automatic-updates-for-the-mobility-service-extension"></a>モビリティ サービス拡張機能の自動更新

Site Recovery では、モビリティ サービス拡張機能に対する自動更新のオプションが追加されました。 モビリティ サービス拡張機能は、Site Recovery によってレプリケートされる各 Azure VM にインストールされます。 レプリケーションを有効にするときには、拡張機能に対する更新の管理を Site Recovery に許可するかどうかを選択します。 更新では VM の再起動は必要とされず、レプリケーションに影響はありません。 [詳細情報](azure-to-azure-autoupdate.md)。

### <a name="disaster-recovery-for-vms-using-accelerated-networking"></a>高速ネットワークを使用した VM のためのディザスター リカバリー

高速ネットワークによって、VM へのシングル ルート I/O 仮想化 (SR-IOV) が可能になり、ネットワークのパフォーマンスが向上します。 Azure VM のレプリケーションを有効にすると、Site Recovery は、高速ネットワークが有効になっているかどうかを検出します。 有効な場合はフェールオーバー後に、[Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) と [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms) の両方について、Site Recovery によってターゲットのレプリカ Azure VM 上に高速ネットワークが自動的に構成されます。 [詳細情報](azure-vm-disaster-recovery-with-accelerated-networking.md)。

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Azure VM ディザスター リカバリー用の料金計算ツール

Azure VM のディザスター リカバリーでは、VM のライセンス コストのほか、ネットワークとストレージのコストが発生します。 Azure では、これらのコストの把握に役立つ[料金計算ツール](https://aka.ms/a2a-cost-estimator)を提供しています。 Site Recovery では、6 個の VM、12 個の Standard HDD ディスク、6 個の Premium SSD ディスクを使用する 3 層アプリに基づいたサンプル デプロイに値段を付ける、[価格見積もりの例](https://aka.ms/a2a-cost-estimator)が提供されるようになりました。 このサンプルは、1 日に Standard では 10 GB、Premium では 20 GB のデータ変更を想定しています。 特定のデプロイについては、変数を変更してコストを見積もることができます。 VM の数、マネージド ディスクの数と種類、VM 全体で予想される総データ変更率を指定します。 さらに、帯域幅コストを見積もるための圧縮率を適用できます。 お知らせを[読む](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)



## <a name="q3-2018"></a>Q3 2018 


### <a name="update-rollup-28-august-2018"></a>更新プログラム ロールアップ 28 (2018 年 8 月)

[更新プログラム ロールアップ 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) は次の更新プログラムを提供します。

**Update** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)
**Linux 用のディザスター リカバリー** | **Azure VM**: RedHat Enterprise Linux 6.10; CentOS 6.10 のサポートが追加されました。<br/><br/> **VMware VM**:RedHat Enterprise Linux 6.10; CentOS 6.10。<br/><br/> レガシ BIOS 互換モードで GUID パーティション テーブル (GPT) のパーティション スタイルを使用する Linux ベースの VM がサポートされるようになりました。
**クラウドのサポート** | ドイツのクラウドで Azure VM のディザスター リカバリーがサポートされるようになりました。
**サブスクリプション間のディザスター リカバリー** | 同じ Azure Active Directory テナント内の異なるサブスクリプションで、別のリージョンに Azure VM をレプリケートすることがサポートされるようになりました。 [詳細情報](https://aka.ms/cross-sub-blog)。
**Windows Server 2008** | Windows Server 2008 R2/2008 64 ビット版および 32 ビット版を実行するマシンの移行がサポートされるようになりました。<br/><br/> 移行のみ (レプリケーションとフェールオーバー)。 フェールバックはサポートされていません。

### <a name="update-rollup-27-july-2018"></a>更新プログラム ロールアップ 27 (2018 年 7 月)

[更新プログラム ロールアップ 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) は次の更新プログラムを提供します。

**Update** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)
**Linux 用のディザスター リカバリー** | **Azure VM**: Red Hat Enterprise Linux 7.5<br/><br/> **VMware VM および物理サーバー**:Red Hat Enterprise Linux 7.5、SUSE Linux Enterprise Server 12




## <a name="next-steps"></a>次の手順

[Azure の更新情報](https://azure.microsoft.com/updates/?product=site-recovery)に関するページで、更新プログラムの最新情報を常に把握してください。




 









