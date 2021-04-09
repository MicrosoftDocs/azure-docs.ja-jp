---
title: Azure VM バックアップのサポート マトリックス
description: Azure Backup サービスを使用して Azure VM をバックアップする場合のサポート設定と制限事項について概説します。
ms.topic: conceptual
ms.date: 09/13/2019
ms.custom: references_regions
ms.openlocfilehash: 82de73944bd833ec5f921e07a29960ac6d175f8d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102609789"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Azure VM バックアップのサポート マトリックス

[Azure Backup サービス](backup-overview.md)を使用すると、オンプレミスのコンピューターとワークロード、および Azure 仮想マシン (VM) をバックアップできます。 この記事では、Azure Backup を使用して Azure VM をバックアップする場合のサポート設定と制限事項について概説します。

その他のサポート マトリックス:

- Azure Backup の[一般的なサポート マトリックス](backup-support-matrix.md)
- Azure Backup Server / System Center Data Protection Manager (DPM) を使用したバックアップの[サポート マトリックス](backup-support-matrix-mabs-dpm.md)
- Microsoft Azure Recovery Services (MARS) エージェントを使用したバックアップの[サポート マトリックス](backup-support-matrix-mars-agent.md)

## <a name="supported-scenarios"></a>サポートされるシナリオ

Azure Backup サービスを使用して Azure VM をどのようにバックアップおよび復元できるかを次に示します。

**シナリオ** | **Backup** | **エージェント** |**復元**
--- | --- | --- | ---
Azure VM の直接バックアップ  | VM 全体をバックアップします。  | Azure VM では、追加のエージェントは必要ありません。 Azure Backup によって、VM 上で実行している [Azure VM エージェント](../virtual-machines/extensions/agent-windows.md)に対して拡張機能がインストールされ、使用されます。 | 次のように復元します。<br/><br/> - **基本的な VM を作成する**。 これは、VM に複数の IP アドレスなどの特別な構成がない場合に便利です。<br/><br/> - **VM ディスクを復元する**。 ディスクを復元します。 次にそれを既存の VM にアタッチするか、PowerShell を使用してディスクから新しい VM を作成します。<br/><br/> - **VM ディスクを交換する**。 VM が存在し、マネージド ディスク (未暗号化) を使用している場合、ディスクを復元し、それを使用して VM 上の既存のディスクを交換することができます。<br/><br/> - **特定のファイル/フォルダーを復元する**。 VM 全体ではなく、VM のファイルやフォルダーを復元できます。
Azure VM の直接バックアップ (Windows のみ)  | 特定のファイル、フォルダー、ボリュームをバックアップします。 | [Azure Recovery Services エージェント](backup-azure-file-folder-backup-faq.md)をインストールします。<br/><br/> Azure VM エージェントのバックアップ拡張機能と共に MARS エージェントを実行して、ファイル/フォルダー レベルで VM をバックアップできます。 | 特定のフォルダー/ファイルを復元します。
バックアップ サーバーに Azure VM をバックアップする  | ファイル/フォルダー/ボリューム、システム状態/ベア メタル ファイル、アプリ データを System Center DPM または Microsoft Azure Backup Server (MABS) にバックアップします。<br/><br/> その後、DPM/MABS がバックアップ コンテナーにバックアップします。 | VM に DPM/MABS 保護エージェントをインストールします。 MARS エージェントは DPM/MABS にインストールされます。| ファイル/フォルダー/ボリューム、システム状態/ベア メタル ファイル、アプリ データを復元します。

[バックアップ サーバーを使用した](backup-architecture.md#architecture-back-up-to-dpmmabs)バックアップと[サポート要件](backup-support-matrix-mabs-dpm.md)の詳細をご確認ください。

## <a name="supported-backup-actions"></a>サポートされているバックアップ アクション

**操作** | **サポート**
--- | ---
シャットダウン状態/オフライン状態の VM をバックアップする | サポートされています。<br/><br/> スナップショットは、アプリ整合性ではなく、クラッシュ整合性のみです。
マネージド ディスクへの移行後にディスクをバックアップする | サポートされています。<br/><br/> バックアップは引き続き機能します。 必要な操作はありません。
リソース グループのロックを有効にした後、マネージド ディスクをバックアップする | サポートされていません。<br/><br/> Azure Backup は古い復元ポイントを削除できないので、復元ポイントの上限に達するとバックアップが失敗するようになります。
VM のバックアップ ポリシーを変更する | サポートされています。<br/><br/> VM は、新しいポリシーのスケジュールおよびリテンション期間の設定を使用してバックアップされます。 リテンション期間の設定が延長されている場合、既存の復旧ポイントがマークされ、保持されます。 短縮されている場合、既存の復旧ポイントは次のクリーンアップ ジョブで取り除かれ、最終的に削除されます。
バックアップ ジョブを取り消す| スナップショットの処理中にサポートされます。<br/><br/> スナップショットがコンテナーに転送されているときはサポートされません。
別のリージョンまたはサブスクリプションに VM をバックアップする |サポートされていません。<br><br>正常にバックアップするには、仮想マシンがバックアップ用のコンテナーと同じサブスクリプションに含まれている必要があります。
1 日あたりのバックアップ回数 (Azure VM 拡張機能を使用した場合) | 1 日あたり 1 回のスケジュール済みバックアップ。<br/><br/>Azure Backup サービスでは、1 日あたり最大 3 回のオンデマンド バックアップと、スケジュール済みバックアップを追加で 1 回サポートします。
1 日あたりのバックアップ回数 (MARS エージェントを使用した場合) | 1 日あたり 3 回のスケジュール済みバックアップ。
1 日あたりのバックアップ回数 (DPM/MABS を使用した場合) | 1 日あたり 2 回のスケジュール済みバックアップ。
毎月/毎年のバックアップ| Azure VM 拡張機能を使用してバックアップする場合、サポートされません。 毎日および毎週のみサポートされます。<br/><br/> 毎月/毎年のリテンション期間の間、毎日/毎週のバックアップを保持するようにポリシーを設定できます。
クロックの自動調整 | サポートされていません。<br/><br/> Azure Backup では、VM をバックアップするとき、夏時間変更に合わせた自動調整は行われません。<br/><br/>  必要に応じて手動でポリシーを変更してください。
[ハイブリッド バックアップのセキュリティ機能](./backup-azure-security-feature.md) |セキュリティ機能の無効化はサポートされていません。
コンピューター時間が変更された VM をバックアップする | サポートされていません。<br/><br/> その VM のバックアップを有効にした後にマシンの時間が将来の日付/時刻に変更されている場合。ただし、時間の変更が元に戻された場合でも、バックアップの成功は保証されません。

## <a name="operating-system-support-windows"></a>オペレーティング システムのサポート (Windows)

Windows Azure VM をバックアップする場合にサポートされるオペレーティング システムについて次の表にまとめます。

**シナリオ** | **OS のサポート**
--- | ---
Azure VM エージェント拡張機能を使用したバックアップ | - Windows 10 クライアント (64 ビットのみ) <br/><br/>- Windows Server 2019 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2016 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2012 R2 (Datacenter/Standard) <br/><br/> Windows Server 2012 (Datacenter /Standard) <br/><br/> - Windows Server 2008 R2 (RTM および SP1 Standard)  <br/><br/> - Windows Server 2008 (64 ビットのみ)
MARS エージェントを使用したバックアップ | [サポートされている](backup-support-matrix-mars-agent.md#supported-operating-systems)オペレーティング システム。
DPM/MABS を使用したバックアップ | [MABS](backup-mabs-protection-matrix.md) および [DPM](/system-center/dpm/dpm-protection-matrix) を使用したバックアップでサポートされるオペレーティング システム。

Azure Backup は 32 ビットのオペレーティング システムをサポートしていません。

## <a name="support-for-linux-backup"></a>Linux バックアップのサポート

Linux マシンをバックアップをしたい場合に何がサポートされるかを以下に示します。

**操作** | **サポート**
--- | ---
Linux Azure VM エージェントを使用した Linux Azure VM のバックアップ | ファイル整合性バックアップ。<br/><br/> [カスタム スクリプト](backup-azure-linux-app-consistent.md)を使用したアプリ整合性バックアップ。<br/><br/> 復元する際に、新しい VM を作成したり、ディスクを復元し、それを使用して VM を作成したり、ディスクを復元し、それを使用して既存の VM 上のディスクを交換したりすることができます。 個々のファイルとフォルダーも復元できます。
MARS エージェントを使用した Linux Azure VM のバックアップ | サポートされていません。<br/><br/> MARS エージェントをインストールできるのは Windows マシンだけです。
DPM/MABS を使用した Linux Azure VM のバックアップ | サポートされていません。
Docker マウント ポイントを使用して Linux Azure VM をバックアップする | 現時点では、Azure Backup は毎回異なるパスにマウントされるため、Docker マウント ポイントの除外をサポートしていません。

## <a name="operating-system-support-linux"></a>オペレーティング システムのサポート (Linux)

Azure VM Linux のバックアップでは、Azure Backup は、[Azure で承認されている一連の Linux ディストリビューション](../virtual-machines/linux/endorsed-distros.md)をサポートしています。 次のことを考慮してください。

- Azure Backup は Core OS Linux をサポートしていません。
- Azure Backup は 32 ビットのオペレーティング システムをサポートしていません。
- 他の個人所有の Linux ディストリビューションは、VM 上で [Linux 用の Azure VM エージェント](../virtual-machines/extensions/agent-linux.md)が動作し、かつ Python がサポートされていれば使用できます。
- Azure Backup は、プロキシが構成された Linux VM を、Python バージョン 2.7 がインストールされていない場合はサポートしていません。
- Azure Backup では、ストレージまたはその他の NFS サーバーから Linux または Windows コンピューターにマウントされた NFS ファイルのバックアップはサポートされません。 VM にローカルに接続されているディスクのみがバックアップされます。

## <a name="backup-frequency-and-retention"></a>バックアップの頻度とリテンション期間

**設定** | **制限**
--- | ---
保護されたインスタンス (マシン/ワークロード) あたりの最大復旧ポイント数 | 9999。
復旧ポイントの最大有効期限 | 制限なし (99 年)。
コンテナーへのバックアップの最大頻度 (Azure VM 拡張機能) | 1 日あたり 1 回。
コンテナーへのバックアップの最大頻度 (MARS エージェント) | 1 日あたり 3 回のバックアップ。
DPM または MABS へのバックアップの最大頻度 | SQL Server の場合は 15 分ごと。<br/><br/> 他のワークロードでは 1 時間に 1 回。
復旧ポイントの保持期間 | 日、週、月、年単位。
最大保有期間 | バックアップ頻度による。
DPM または MABS ディスクの復旧ポイント数 | ファイル サーバーの場合 64 個、アプリ サーバーの場合 448 個。<br/><br/> テープの復旧ポイント数は、オンプレミス DPM に対しては無制限。

## <a name="supported-restore-methods"></a>サポートされる復元方法

**復元オプション** | **詳細**
--- | ---
**新しい VM を作成する** | 基本的な VM を復元ポイントからすばやく作成し、起動して実行します。<br/><br/> VM の名前を指定し、配置先のリソース グループと仮想ネットワーク (VNet) を選択して、復元された VM のストレージ アカウントを指定することができます。 新しい VM は、ソース VM と同じリージョンに作成する必要があります。
**ディスクを復元する** | 新しい VM を作成するために使用できる VM ディスクを復元します。<br/><br/> Azure Backup は、VM のカスタマイズと作成に役立つテンプレートを提供します。 <br/><br> 復元ジョブによって生成されるテンプレートをダウンロードして使用することで、カスタム VM 設定を指定したり、VM を作成したりできます。<br/><br/> ディスクは、指定したリソース グループにコピーされます。<br/><br/> または、ディスクを既存の VM に接続することや、PowerShell を使用して新しい VM を作成することもできます。<br/><br/> このオプションは、VM をカスタマイズする場合や、バックアップの時点では存在していなかった構成設定を追加する場合や、テンプレートまたは PowerShell を使用して構成する必要がある設定を追加する場合に役立ちます。
**既存のものを置き換える** | ディスクを復元し、それを使用して既存の VM 上のディスクを置き換えることができます。<br/><br/> 現在の VM が存在する必要があります。 削除されている場合、このオプションは使用できません。<br/><br/> ディスクを交換する前に、Azure Backup によって既存の VM のスナップショットが取得され、指定したステージングの場所に格納されます。 VM に接続されている既存のディスクが、選択した復元ポイントを使用して置き換えられます。<br/><br/> スナップショットはコンテナーにコピーされ、アイテム保持ポリシーに従って保持されます。 <br/><br/> ディスクの交換操作の後、元のディスクはリソース グループに保持されます。 元のディスクが必要ない場合は、それを手動で削除することを選択できます。 <br/><br/>[既存のものを置き換える] は、暗号化されていないマネージド VM と[カスタム イメージを使用して作成された](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/) VM でサポートされます。 アンマネージド ディスクと VM、クラシック VM、[一般化された VM](../virtual-machines/windows/capture-image-resource.md) ではサポートされません。<br/><br/> 復元ポイントにあるディスクの数が現在の VM よりも多い (または少ない) 場合、復元ポイントのディスク数だけが VM 構成に反映されます。<br><br> 既存のものの置き換えは、[ユーザー割り当てマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) や [Key Vault](../key-vault/general/overview.md) など、リンクされたリソースを持つ VM でもサポートされます。
**リージョンをまたがる (セカンダリ リージョン)** | リージョンをまたがる復元を使用すると、Azure VM をセカンダリ リージョン ([Azure のペアになっているリージョン](../best-practices-availability-paired-regions.md#what-are-paired-regions)) に復元できます。<br><br> セカンダリ リージョンにバックアップが実行されている場合は、選択されている回復ポイントのすべての Azure VM を復元できます。<br><br> この機能は、次のオプションで使用できます。<br> <li> [VM を作成します](./backup-azure-arm-restore-vms.md#create-a-vm) <br> <li> [ディスクを復元する](./backup-azure-arm-restore-vms.md#restore-disks) <br><br> 現時点では、[[既存のディスクの置き換え]](./backup-azure-arm-restore-vms.md#replace-existing-disks) オプションはサポートされていません。<br><br> アクセス許可<br> セカンダリ リージョンでの復元操作は、バックアップ管理者とアプリ管理者が実行できます。

## <a name="support-for-file-level-restore"></a>ファイル レベルの復元のサポート

**復元** | **サポートされています**
--- | ---
オペレーティング システム間でファイルを復元する | バックアップ VM と同じ (または互換性のある) OS を使用する任意のマシンでファイルを復元できます。 [互換性のある OS の表](backup-azure-restore-files-from-vm.md#step-3-os-requirements-to-successfully-run-the-script)を参照してください。
暗号化された VM からファイルを復元する | サポートされていません。
ネットワーク制限付きのストレージ アカウントからファイルを復元する | サポートされていません。
Windows 記憶域スペースを使用して VM でファイルを復元する | 同じ VM での復元はサポートされていません。<br/><br/> 代わりに、互換性のある VM でファイルを復元します。
LVM/RAID アレイを使用して Linux VM でファイルを復元する | 同じ VM での復元はサポートされていません。<br/><br/> 互換性のある VM で復元を行います。
特別なネットワーク設定でファイルを復元する | 同じ VM での復元はサポートされていません。 <br/><br/> 互換性のある VM で復元を行います。
共有ディスク、一時ドライブ、重複除去されたディスク、ウルトラ ディスク、および書き込みアクセラレータが有効なディスクからファイルを復元する | 復元はサポートされていません。 <br/><br/>「[Azure VM ストレージのサポート](#vm-storage-support)」を参照してください。

## <a name="support-for-vm-management"></a>VM 管理のサポート

VM ディスクの追加や交換など、VM 管理タスク中のバックアップのサポートについて次の表にまとめます。

**復元** | **サポートされています**
--- | ---
サブスクリプション/リージョン/ゾーン間で復元する | サポートされていません。
既存の VM に復元する | ディスクの交換オプションを使用します。
Azure Storage Service Encryption (SSE) に対して有効になっているストレージ アカウントを使用してディスクを復元する | サポートされていません。<br/><br/> SSE が有効になっていないアカウントに復元します。
混合ストレージ アカウントに復元する |サポートされていません。<br/><br/> ストレージ アカウントの種類に基づいて、復元されるすべてのディスクは Premium または Standard になり、混合することはありません。
VM を可用性セットに直接復元する | マネージド ディスクでは、ディスクを復元し、テンプレートで可用性セット オプションを使用できます。<br/><br/> アンマネージド ディスクでは、サポートされていません。 アンマネージド ディスクでは、ディスクを復元し、可用性セットで VM を作成します。
マネージド VM にアップグレードした後、アンマネージド VM のバックアップを復元する| サポートされています。<br/><br/> ディスクを復元し、その後、マネージド VM を作成できます。
VM がマネージド ディスクに移行されたときよりも前の復元ポイントにその VM を復元する | サポートされています。<br/><br/> アンマネージド ディスク (既定) に復元し、復元したディスクをマネージド ディスクに変換し、そのマネージド ディスクを使用して VM を作成します。
削除された VM を復元する | サポートされています。<br/><br/> 復旧ポイントから VM を復元できます。
ドメイン コントローラー VM を復元する  | サポートされています。 詳細については、「[ドメイン コントローラー VM を復元する](backup-azure-arm-restore-vms.md#restore-domain-controller-vms)」を参照してください。
別の仮想ネットワークに VM を復元する |サポートされています。<br/><br/> 仮想ネットワークは同一のサブスクリプションとリージョン内に存在する必要があります。

## <a name="vm-compute-support"></a>VM コンピューティングのサポート

**Compute** | **サポート**
--- | ---
VM サイズ |少なくとも 2 つの CPU コアと 1 GB の RAM を備えた任意の Azure VM サイズ。<br/><br/> [詳細情報。](../virtual-machines/sizes.md)
[可用性セット](../virtual-machines/availability.md#availability-sets)で VM をバックアップする | サポートされています。<br/><br/> VM をすばやく作成するオプションを使用して、可用性セットで VM を復元することはできません。 代わりに、VM を復元する場合は、ディスクを復元し、それを使用して VM をデプロイするか、ディスクを復元し、それを使用して既存のディスクを交換します。
[Hybrid Use Benefit (HUB)](../virtual-machines/windows/hybrid-use-benefit-licensing.md) を使用してデプロイ済みの VM をバックアップする | サポートされています。
[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images) からデプロイされた VM をバックアップする<br/><br/> (Microsoft、サード パーティによって公開) |サポートされています。<br/><br/> VM はサポートされているオペレーティング システムを実行している必要があります。<br/><br/> VM でファイルを復元する場合、(古いまたは新しい OS ではなく) 互換性のある OS に対してのみ復元できます。 VM としてバックアップされた Azure Marketplace VM は、購入情報が必要であるため、復元されません。 ディスクとしてのみ復元されます。
カスタム イメージ (サード パーティ) からデプロイ済みの VM をバックアップする |サポートされています。<br/><br/> VM はサポートされているオペレーティング システムを実行している必要があります。<br/><br/> VM でファイルを復元する場合、(古いまたは新しい OS ではなく) 互換性のある OS に対してのみ復元できます。
Azure に移行済みの VM をバックアップする| サポートされています。<br/><br/> VM をバックアップするには、移行済みマシンに VM エージェントをインストールする必要があります。
マルチ VM 整合性をバックアップする | Azure Backup では、複数の VM 間でのデータとアプリケーションの整合性は提供されません。
[[診断設定]](../azure-monitor/essentials/platform-logs-overview.md) でバックアップする  | サポートされていません。 <br/><br/> 診断設定を使った Azure VM の復元が [[新規作成]](backup-azure-arm-restore-vms.md#create-a-vm) オプションを使用してトリガーされた場合、復元は失敗します。
ゾーン固定 VM の復元 | サポートされています (2019 年 1 月以降にバックアップされた、[可用性ゾーン](https://azure.microsoft.com/global-infrastructure/availability-zones/)が使用可能な VM の場合)。<br/><br/>現在は、VM で固定されているものと同じゾーンへの復元がサポートされています。 ただし、障害が原因でゾーンが使用できない場合、復元は失敗します。
Gen2 VM | サポートされています <br> Azure Backup では、[Gen2 VM](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/) のバックアップと復元がサポートされます。 これらの VM は、復旧ポイントから復元される場合、[Gen2 VM](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/) として復元されます。
ロックされた Azure VM のバックアップ | アンマネージド VM では、サポートされていません。 <br><br> マネージド VM ではサポートされています。
[スポット VM](../virtual-machines/spot-vms.md) | サポートされていません。 Azure Backup では、Spot VM が通常の Azure VM として復元されます。
[Azure Dedicated Host](../virtual-machines/dedicated-hosts.md) | サポートされています
スタンドアロン Azure VM の Windows 記憶域スペース構成 | サポートされています

## <a name="vm-storage-support"></a>VM ストレージのサポート

**コンポーネント** | **サポート**
--- | ---
Azure VM のデータ ディスク数 | 最大 32 のディスクを使用した Azure VM のバックアップのサポート。<br><br> アンマネージド ディスクまたはクラシック VM を使用した Azure VM のバックアップは、最大 16 台のディスクしかサポートされません。
データ ディスク サイズ | 個々のディスク サイズは最大 32 TB で、VM 内のすべてのディスクに対して最大 256 TB となります。
ストレージの種類 | Standard HDD、Standard SSD、Premium SSD。
マネージド ディスク | サポートされています。
暗号化されたディスク | サポートされています。<br/><br/> Azure Disk Encryption が有効になっている Azure VM を (Azure AD アプリを使用して、または使用せずに) バックアップできます。<br/><br/> 暗号化された VM は、ファイルまたはフォルダー レベルで復旧することはできません。 VM 全体を復旧する必要があります。<br/><br/> Azure Backup によって既に保護されている VM で暗号化を有効にできます。
書き込みアクセラレータが有効になっているディスク | 2020 年 11 月 23 日の時点では、限定された数のサブスクリプションに対して、韓国中部 (KRC) および南アフリカ北部 (SAN) リージョンでのみサポートされています。 これらのサポートされるサブスクリプションでは、Azure Backup により、バックアップ中に Write Accelarted (WA) が有効になっているディスクを使用する仮想マシンがバックアップされます。<br><br>サポートされていないリージョンでは、WA が有効になっている仮想マシンのスナップショットを作成するために、VM でインターネット接続が必要です。<br><br> **重要な注意**: これらのサポートされないリージョンでは、WA ディスクを使用する仮想マシンは、正常なバックアップのためにインターネット接続を必要とします (これらのディスクがバックアップから除外されている場合でも)。
重複除去された VM/ディスクのバックアップと復元 | Azure Backup では、重複除去はサポートされていません。 詳細については、こちらの[記事](./backup-support-matrix.md#disk-deduplication-support)を参照してください <br/> <br/>  - Azure Backup では、Recovery Services コンテナー内の VM 全体で重複除去されることはありません <br/> <br/>  - 復元中に重複除去状態の VM がある場合、コンテナーで形式が認識されないため、ファイルを復元することはできません。 ただし、完全な VM 復元は正常に実行できます。
保護された VM にディスクを追加する | サポートされています。
保護された VM でディスクのサイズを変更する | サポートされています。
共有ストレージ| クラスターの共有ボリューム (CSV) またはスケールアウト ファイル サーバーを使用した VM のバックアップはサポートされていません。 バックアップ中に CSV ライターが失敗する可能性があります。 また、復元時に CSV ボリュームを含むディスクが起動しない可能性があります。
[共有ディスク](../virtual-machines/disks-shared-enable.md) | サポートされていません。
Ultra SSD ディスク | サポートされていません。 詳細については、[制限](selective-disk-backup-restore.md#limitations)に関するページを参照してください。
[一時ディスク](../virtual-machines/managed-disks-overview.md#temporary-disk) | 一時ディスクは Azure Backup ではバックアップされません。
NVMe/エフェメラル ディスク | サポートされていません。

## <a name="vm-network-support"></a>VM ネットワークのサポート

**コンポーネント** | **サポート**
--- | ---
ネットワーク インターフェイス (NIC) の数 | 特定の Azure VM サイズでサポートされている NIC の最大数まで。<br/><br/> 復元処理中に VM が作成されると、NIC が作成されます。<br/><br/> 復元された VM の NIC の数は、保護を有効にしたときの VM の NIC の数を反映します。 保護を有効にした後で NIC を削除しても、数には影響しません。
外部/内部ロード バランサー |サポートされています。 <br/><br/> 特別なネットワーク設定での VM の復元について[詳細を確認](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations)してください。
複数の予約済み IP アドレス |サポートされています。 <br/><br/> 特別なネットワーク設定での VM の復元について[詳細を確認](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations)してください。
複数のネットワーク アダプターを持つ VM| サポートされています。 <br/><br/> 特別なネットワーク設定での VM の復元について[詳細を確認](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations)してください。
パブリック IP アドレスを持つ VM| サポートされています。<br/><br/> 既存のパブリック IP アドレスを NIC に関連付けるか、復元後にアドレスを作成して NIC に関連付けます。
NIC/サブネットでのネットワーク セキュリティ グループ (NSG) |サポートされています。
静的 IP アドレス | サポートされていません。<br/><br/> 復元ポイントから作成された新しい VM には、動的 IP アドレスが割り当てられます。<br/><br/> クラシック VM では、予約済み IP アドレスを持ち、エンドポイントが定義されていない VM はバックアップできません。
動的 IP アドレス |サポートされています。<br/><br/> ソース VM の NIC が動的 IP アドレスを使用する場合、既定では、復元された VM の NIC も動的 IP アドレスを使用します。
Azure の Traffic Manager| サポートされています。<br/><br/>バックアップされた VM が Traffic Manager にある場合、復元された VM を同じ Traffic Manager インスタンスに手動で追加します。
Azure DNS |サポートされています。
[カスタム DNS] |サポートされています。
HTTP プロキシを介したアウトバウンド接続 | サポートされています。<br/><br/> 認証済みプロキシはサポートされていません。
仮想ネットワーク サービス エンドポイント| サポートされています。<br/><br/> ファイアウォールと仮想ネットワークのストレージ アカウントの設定で、すべてのネットワークからのアクセスを許可する必要があります。

## <a name="vm-security-and-encryption-support"></a>VM のセキュリティと暗号化のサポート

Azure Backup では、転送中と保存時のデータの暗号化をサポートしています。

Azure へのネットワーク トラフィック:

- サーバーから Recovery Services コンテナーへのバックアップ トラフィックは、Advanced Encryption Standard 256 を使用して暗号化されます。
- バックアップ データは、セキュリティで保護された HTTPS リンク経由で送信されます。
- バックアップ データは、暗号化された形式で Recovery Services コンテナーに格納されます。
- このデータのロックを解除する暗号化キーを持っているのはお客様だけです。 Microsoft は、どの時点でも、バックアップ データの暗号化を解除できません。

  > [!WARNING]
  > コンテナーを設定すると、設定したユーザーのみが暗号化キーにアクセスできます。 Microsoft がコピーを保持することはなく、キーにアクセスすることもできません。 キーを紛失した場合、Microsoft はバックアップ データを復旧できません。

データのセキュリティ:

- Azure VM をバックアップする場合、仮想マシンの *内部* で暗号化を設定する必要があります。
- Azure Backup は Azure Disk Encryption をサポートしており、Windows 仮想マシンでは BitLocker が、Linux 仮想マシンでは **dm-crypt** が使用されます。
- Azure Backup のバックエンドでは [Azure Storage Service Encryption](../storage/common/storage-service-encryption.md) が使用されており、これによって保存データが保護されます。

**マシン** | **転送中** | **保存時**
--- | --- | ---
オンプレミスの Windows マシン (DPM または MABS なし) | ![はい][green] | ![はい][green]
Azure VM | ![はい][green] | ![はい][green]
オンプレミス VM または Azure VM (DPM あり) | ![はい][green] | ![はい][green]
オンプレミス VM または Azure VM (MABS あり) | ![はい][green] | ![はい][green]

## <a name="vm-compression-support"></a>VM の圧縮のサポート

Backup では、次の表にまとめられているように、バックアップ トラフィックの圧縮をサポートしています。 次のことを考慮してください。

- Azure VM では、ストレージ ネットワーク経由で Azure ストレージ アカウントから直接データが VM 拡張機能によって読み取られます。 このトラフィックを圧縮する必要はありません。
- DPM や MABS を使用する場合は、DPM や MABS にデータをバックアップする前に圧縮することにより、帯域幅を節約できます。

**マシン** | **MABS または DPM に圧縮 (TCP)** | **コンテナーに圧縮 (HTTPS)**
--- | --- | ---
オンプレミスの Windows マシン (DPM または MABS なし) | NA | ![はい][green]
Azure VM | NA | NA
オンプレミス VM または Azure VM (DPM あり) | ![はい][green] | ![はい][green]
オンプレミス VM または Azure VM (MABS あり) | ![はい][green] | ![はい][green]

## <a name="next-steps"></a>次のステップ

- [Azure VM をバックアップする](backup-azure-arm-vms-prepare.md)。
- バックアップ サーバーなしで、[Windows マシンを直接バックアップ](tutorial-backup-windows-server-to-azure.md)します。
- Azure へのバックアップのために [MABS を設定](backup-azure-microsoft-azure-backup.md)してから、MABS にワークロードをバックアップします。
- Azure へのバックアップのために [DPM を設定](backup-azure-dpm-introduction.md)してから、DPM にワークロードをバックアップします。

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png