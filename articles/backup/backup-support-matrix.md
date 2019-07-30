---
title: Azure Backup のサポート マトリックス
description: Azure Backup サービスのサポート設定と制限事項の概要を説明します。
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: a6b7dfe8fb8ade7f84f41fb5602aff68b4f52cf2
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464832"
---
# <a name="azure-backup-support-matrix"></a>Azure Backup のサポート マトリックス

[Azure Backup](backup-overview.md) を使用すると、Microsoft Azure クラウド プラットフォームにデータをバックアップできます。 この記事では、Azure Backup のシナリオとデプロイの一般的なサポート設定と制限事項について説明します。

他に以下のサポート マトリックスを使用できます。

- [Azure 仮想マシン (VM) のバックアップ](backup-support-matrix-iaas.md)のサポート マトリックス
- [System Center Data Protection Manager (DPM)/Microsoft Azure Backup Server (MABS)](backup-support-matrix-mabs-dpm.md) を使用したバックアップのサポート マトリックス
- [Microsoft Azure Recovery Services (MARS) エージェント](backup-support-matrix-mars-agent.md)を使用したバックアップのサポート マトリックス

## <a name="vault-support"></a>コンテナーのサポート

Azure Backup では、Recovery Services コンテナーを使用して、バックアップを調整および管理しています。 また、コンテナーを使用してバックアップ データを格納しています。

次の表では、Recovery Services コンテナーの機能について説明します。

**機能** | **詳細**
--- | ---
**サブスクリプション内のコンテナー数** | 1 つのサブスクリプションで、最大 500 個の Recovery Services コンテナー。
**コンテナー内のマシン数** | 1 つのコンテナーで、最大 1,000 台の Azure VM。<br/><br/> 1 つのコンテナーに最大で 50 MABS を登録することができます。
**コンテナー ストレージ内のデータ ソース量** | 最大 54,400 GB。 Azure VM のバックアップに関する制限はありません。
**コンテナーへのバックアップ回数** | **Azure VM:** 1 日 1 回。<br/><br/>**DPM/MABS で保護されたマシン:** 1 日に 2 回。<br/><br/> **MARS エージェントを使用し直接バックアップされるマシン:** 1 日に 3 回。
**コンテナー間のバックアップ** | バックアップは 1 つのリージョン内です。<br/><br/> バックアップする VM が含まれるすべての Azure リージョンにコンテナーが必要です。 異なるリージョンにバックアップすることはできません。
**コンテナーの移動** | 異なるサブスクリプション間で、または同じサブスクリプション内のリソース グループ間で、[コンテナーを移動](https://review.docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault)できます。
**コンテナー間のデータの移動** | コンテナー間でのバックアップ データの移動はサポートされていません。
**コンテナー ストレージの種類の変更** | コンテナー ストレージのレプリケーションの種類 (geo 冗長ストレージまたはローカル冗長ストレージのいずれか) は、バックアップを格納する前に変更できます。 コンテナーでバックアップが開始された後は、レプリケーションの種類を変更できません。

## <a name="on-premises-backup-support"></a>オンプレミス バックアップのサポート

オンプレミス マシンをバックアップしたい場合に何がサポートされるかを以下に示します。

**マシン** | **バックアップされる項目** | **Location** | **機能**
--- | --- | --- | ---
**MARS エージェントを使用した Windows マシンの直接バックアップ** | ファイル、フォルダー、システム状態 | Recovery Services コンテナーへのバックアップ。 | 1 日に 3 回のバックアップ<br/><br/> アプリ対応のバックアップなし<br/><br/> ファイル、フォルダー、ボリュームの復元
**MARS エージェントを使用した Linux マシンの直接バックアップ** | バックアップはサポートされていません
**DPM へのバックアップ** | ファイル、フォルダー、ボリューム、システム状態、アプリ データ | ローカル DPM ストレージにバックアップします。 DPM はその後、コンテナーにバックアップされます。 | アプリ対応のスナップショット<br/><br/> バックアップと回復の完全な細分性<br/><br/> VM (Hyper-V または VMware) での Linux のサポート<br/><br/> Oracle はサポートされていません
**MABS へのバックアップ** | ファイル、フォルダー、ボリューム、システム状態、アプリ データ | MABS ローカル ストレージにバックアップします。 MABS はその後、コンテナーにバックアップされます。 | アプリ対応のスナップショット<br/><br/> バックアップと回復の完全な細分性<br/><br/> VM (Hyper-V または VMware) での Linux のサポート<br/><br/> Oracle はサポートされていません

## <a name="azure-vm-backup-support"></a>Azure VM バックアップのサポート

### <a name="azure-vm-limits"></a>Azure VM の制限

**制限** | **詳細**
--- | ---
**Azure VM のデータ ディスク数** | 最大 16 個
**Azure VM のデータ ディスク サイズ** | ディスクごとに最大で 4,095 GB

### <a name="azure-vm-backup-options"></a>Azure VM のバックアップ オプション

Azure VM をバックアップしたい場合に何がサポートされるかを以下に示します。

**マシン** | **バックアップされる項目** | **Location** | **機能**
--- | --- | --- | ---
**VM 拡張機能を使用した Azure VM のバックアップ** | VM 全体 | コンテナーへのバックアップ。 | VM のバックアップを有効にするときにインストールされる拡張機能。<br/><br/> 1 日 1 回のバックアップ。<br/><br/> Windows VM の場合はアプリ対応バックアップ、Linux VM の場合はファイル整合性バックアップ。 Linux マシンでは、カスタム スクリプトを使用して、アプリ整合性を構成できます。<br/><br/> VM またはディスクの復元。<br/><br/> Azure VM は、オンプレミスの場所へはバックアップできません。
**MARS エージェントを使用する Azure VM のバックアップ** | ファイル、フォルダー、システム状態 | コンテナーへのバックアップ。 | 1 日に 3 回のバックアップ。<br/><br/> VM 全体ではなく特定のファイルまたはフォルダーをバックアップしたい場合は、MARS エージェントを VM 拡張機能と共に実行できます。
**Azure VM (DPM あり)** | ファイル、フォルダー、ボリューム、システム状態、アプリ データ | DPM が実行されている Azure VM のローカル ストレージへのバックアップ。 DPM はその後、コンテナーにバックアップされます。 | アプリ対応のスナップショット。<br/><br/> バックアップと回復の完全な細分性。<br/><br/> VM (Hyper-V または VMware) で Linux がサポートされています。<br/><br/> Oracle はサポートされていません。
**Azure VM (MABS あり)** | ファイル、フォルダー、ボリューム、システム状態、アプリ データ | MABS が実行されている Azure VM のローカル ストレージへのバックアップ。 MABS はその後、コンテナーにバックアップされます。 | アプリ対応のスナップショット。<br/><br/> バックアップと回復の完全な細分性。<br/><br/> VM (Hyper-V または VMware) で Linux がサポートされています。<br/><br/> Oracle はサポートされていません。

## <a name="linux-backup-support"></a>Linux のバックアップ サポート

Linux マシンをバックアップをしたい場合に何がサポートされるかを以下に示します。

**バックアップの種類** | **Linux (Azure での動作保証済み)**
--- | ---
**Linux を実行しているオンプレミスのマシンの直接バックアップ** | サポートされていません。 MARS エージェントは Windows マシンにのみインストールできます。
**エージェント拡張機能を使用した Linux を実行している Azure VM のバックアップ** | [カスタム スクリプト](backup-azure-linux-app-consistent.md)を使用したアプリ整合性バックアップ。<br/><br/> ファイルレベルの回復。<br/><br/> 復旧ポイントまたはディスクから VM を作成することによる復元。
**DPM を使用した、Linux を実行しているオンプレミスまたは Azure VM のバックアップ** | Hyper-V および VMWare 上の Linux Guest VM のファイル整合性バックアップ。<br/><br/> Hyper-V および VMWare Linux ゲスト VM の VM の復元。<br/><br/> ファイル整合性バックアップは Azure VM では利用できません。
**MABS を使用した、Linux を実行しているオンプレミス マシンまたは Azure VM のバックアップ** | Hyper-V および VMWare 上の Linux Guest VM のファイル整合性バックアップ。<br/><br/> Hyper-V および VMWare Linux ゲスト VM の VM の復元。<br/><br/> ファイル整合性バックアップは Azure VM では利用できません。

## <a name="daylight-saving-time-support"></a>夏時間のサポート

Azure Backup では、Azure VM のバックアップでの夏時間への時計の自動調整はサポートしていません。 必要に応じて手動でバックアップ ポリシーを変更します。

## <a name="disk-deduplication-support"></a>ディスクの重複除去のサポート

ディスクの重複除去のサポートは、次のとおりです。

- Windows を実行している Hyper-V VM のバックアップに DPM または MABS を使用している場合、オンプレミスでディスクの重複除去がサポートされます。 Windows Server では、VM にバックアップ ストレージとして接続されている仮想ハード ディスク (VHD) の重複除去を (ホスト レベルで) 実行します。
- Azure では、どの Backup コンポーネントでも重複除去はサポートされていません。 DPM および MABS が Azure にデプロイされている場合、VM にアタッチされているストレージ ディスクでは重複除去はされません。

## <a name="security-and-encryption-support"></a>セキュリティと暗号化のサポート

Azure Backup では、転送中のデータと保存データの暗号化をサポートしています。

### <a name="network-traffic-to-azure"></a>Azure へのネットワーク トラフィック

- サーバーから Recovery Services コンテナーへのバックアップ トラフィックは、Advanced Encryption Standard 256 を使用して暗号化されます。
- バックアップ データは、セキュリティで保護された HTTPS リンク経由で送信されます。
- バックアップ データは、暗号化された形式で Recovery Services コンテナーに格納されます。
- このデータのロックを解除するパスフレーズを持っているのはお客様だけです。 Microsoft は、どの時点でも、バックアップ データの暗号化を解除できません。

    > [!WARNING]
    > コンテナーの設定後、お客様だけが暗号化キーにアクセスできます。 Microsoft がコピーを保持することはなく、キーにアクセスすることもできません。 キーを紛失した場合、Microsoft はバックアップ データを復旧できません。

### <a name="data-security"></a>データのセキュリティ

- Azure VM をバックアップする場合は、仮想マシン*内*で暗号化を設定する必要があります。
- Azure Backup は Azure Disk Encryption をサポートしており、Windows 仮想マシンでは BitLocker が、Linux 仮想マシンでは **dm-crypt** が使用されます。
- Azure Backup のバックエンドでは [Azure Storage Service Encryption](../storage/common/storage-service-encryption.md) が使用されており、これによって保存データが保護されます。

**マシン** | **転送中** | **保存時**
--- | --- | ---
**オンプレミスの Windows マシン (DPM または MABS なし)** | ![はい][green] | ![はい][green]
**Azure VM** | ![はい][green] | ![はい][green]
**オンプレミスの Windows マシンまたは Azure VM (DPM あり)** | ![はい][green] | ![はい][green]
**オンプレミスの Windows マシンまたは Azure VM (MABS あり)** | ![はい][green] | ![はい][green]

## <a name="compression-support"></a>圧縮のサポート

Backup では、次の表にまとめられているように、バックアップ トラフィックの圧縮をサポートしています。

- Azure VM では、ストレージ ネットワーク経由で Azure ストレージ アカウントから直接データが VM 拡張機能によって読み取られます。そのため、このトラフィックを圧縮する必要はありません。
- DPM または MABS を使用する場合は、データをバックアップする前に圧縮して、帯域幅を節約できます。

**マシン** | **MABS または DPM に圧縮 (TCP)** | **コンテナーに圧縮 (HTTPS)**
--- | --- | ---
**オンプレミスの Windows マシンの直接バックアップ** | NA | ![はい][green]
**VM 拡張機能を使用した Azure VM のバックアップ** | NA | NA
**MABS/DPM を使用したオンプレミス/Azure マシンへのバックアップ** | ![はい][green] | ![はい][green]

## <a name="retention-limits"></a>保有の制限

**設定** | **制限**
--- | ---
**保護されたインスタンス (マシンまたはワークロード) ごとの最大復旧ポイント数** | 9,999
**復旧ポイントの最大有効期限** | 制限なし
**DPM または MABS へのバックアップの最大頻度** | SQL Server の場合は 15 分ごと<br/><br/> 他のワークロードでは 1 時間に 1 回
**コンテナーへのバックアップの最大頻度** | **オンプレミスの Windows マシンまたは MARS を実行する Azure VM:** 1 日に 3 回<br/><br/> **DPM/MABS:** 1 日に 2 回<br/><br/> **Azure VM バックアップ:** 1 日 1 回
**復旧ポイントの保持期間** | 毎日、毎週、毎月、毎年
**最大保有期間** | バックアップ頻度次第
**DPM または MABS ディスクの復旧ポイント数** | ファイル サーバーの場合 64 個、アプリ サーバーの場合 448 個。 <br/><br/>テープの復旧ポイント数は、オンプレミス DPM に対しては無制限。

## <a name="next-steps"></a>次の手順

- Azure VM バックアップの[サポート マトリックスを確認します](backup-support-matrix-iaas.md)。

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
