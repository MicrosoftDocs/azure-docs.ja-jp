---
title: Azure Backup のサポート マトリックス
description: Azure Backup サービスのサポート設定と制限事項の概要を説明します。
ms.topic: conceptual
ms.date: 02/17/2019
ms.custom: references_regions
ms.openlocfilehash: 655a90ebc3164f076c333760beffcef5acefcd91
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103225549"
---
# <a name="support-matrix-for-azure-backup"></a>Azure Backup のサポート マトリックス

[Azure Backup](backup-overview.md) を使用すると、Microsoft Azure クラウド プラットフォームにデータをバックアップできます。 この記事では、Azure Backup のシナリオとデプロイの一般的なサポート設定と制限事項について説明します。

他に以下のサポート マトリックスを使用できます。

- [Azure 仮想マシン (VM) のバックアップ](backup-support-matrix-iaas.md)のサポート マトリックス
- [System Center Data Protection Manager (DPM)/Microsoft Azure Backup Server (MABS)](backup-support-matrix-mabs-dpm.md) を使用したバックアップのサポート マトリックス
- [Microsoft Azure Recovery Services (MARS) エージェント](backup-support-matrix-mars-agent.md)を使用したバックアップのサポート マトリックス

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="vault-support"></a>コンテナーのサポート

Azure Backup は Recovery Services コンテナーを使用して、各種のワークロード (Azure VM、Azure VM の SQL、Azure VM の SAP HANA、Azure ファイル共有、および Azure Backup エージェント、Azure Backup Server、および System Center DPM を使用したオンプレミスのワークロード) を調整および管理します。 また、Recovery Services コンテナーを使用して、これらのワークロードのバックアップ データを格納します。

次の表では、Recovery Services コンテナーの機能について説明します。

**機能** | **詳細**
--- | ---
**サブスクリプション内のコンテナー数** | 1 つのサブスクリプションで、最大 500 個の Recovery Services コンテナー。
**コンテナー内のマシン数** | すべてのワークロード (Azure VM、SQL Server VM、MABS サーバーなど) で最大2000 個のデータソースを 1 つのコンテナーで保護できます。<br><br>1 つのコンテナーで、最大 1,000 台の Azure VM。<br/><br/> 1 つのコンテナーに最大で 50 MABS を登録することができます。
**[データ ソース]** | 個々の[データ ソース](./backup-azure-backup-faq.md#how-is-the-data-source-size-determined)の最大サイズは 54,400 GB です。 この制限は、Azure VM のバックアップには適用されません。 コンテナーにバックアップできるデータの総量には、制限は適用されません。
**コンテナーへのバックアップ回数** | **Azure VM:** 1 日あたり 1 回。<br/><br/>**DPM/MABS で保護されたマシン:** 1 日に 2 回。<br/><br/> **MARS エージェントを使用し直接バックアップされるマシン:** 1 日に 3 回。
**コンテナー間のバックアップ** | バックアップは 1 つのリージョン内です。<br/><br/> バックアップする VM が含まれるすべての Azure リージョンにコンテナーが必要です。 異なるリージョンにバックアップすることはできません。
**コンテナーの移動** | 異なるサブスクリプション間で、または同じサブスクリプション内のリソース グループ間で、[コンテナーを移動](./backup-azure-move-recovery-services-vault.md)できます。 ただし、リージョン間でのコンテナーの移動はサポートされていません。
**コンテナー間のデータの移動** | コンテナー間でのバックアップ データの移動はサポートされていません。
**コンテナー ストレージの種類の変更** | コンテナー ストレージのレプリケーションの種類 (geo 冗長ストレージまたはローカル冗長ストレージのいずれか) は、バックアップを格納する前に変更できます。 コンテナーでバックアップが開始された後は、レプリケーションの種類を変更できません。
**ゾーン冗長ストレージ (ZRS)** | 英国南部 (UKS) リージョンと東南アジア (SEA) リージョンで利用できます。
**プライベート エンドポイント** | Recovery Services コンテナーのプライベート エンドポイントを作成するための要件については、[こちらのセクション](https://docs.microsoft.com/azure/backup/private-endpoints#before-you-start)を参照してください。  

## <a name="on-premises-backup-support"></a>オンプレミス バックアップのサポート

オンプレミス マシンをバックアップしたい場合に何がサポートされるかを以下に示します。

**マシン** | **バックアップされる項目** | **場所** | **機能**
--- | --- | --- | ---
**MARS エージェントを使用した Windows マシンの直接バックアップ** | ファイル、フォルダー、システム状態 | Recovery Services コンテナーへのバックアップ。 | 1 日に 3 回のバックアップ<br/><br/> アプリ対応のバックアップなし<br/><br/> ファイル、フォルダー、ボリュームの復元
**MARS エージェントを使用した Linux マシンの直接バックアップ** | バックアップはサポートされていません
**DPM へのバックアップ** | ファイル、フォルダー、ボリューム、システム状態、アプリ データ | ローカル DPM ストレージにバックアップします。 DPM はその後、コンテナーにバックアップされます。 | アプリ対応のスナップショット<br/><br/> バックアップと回復の完全な細分性<br/><br/> VM (Hyper-V または VMware) での Linux のサポート<br/><br/> Oracle はサポートされていません
**MABS へのバックアップ** | ファイル、フォルダー、ボリューム、システム状態、アプリ データ | MABS ローカル ストレージにバックアップします。 MABS はその後、コンテナーにバックアップされます。 | アプリ対応のスナップショット<br/><br/> バックアップと回復の完全な細分性<br/><br/> VM (Hyper-V または VMware) での Linux のサポート<br/><br/> Oracle はサポートされていません

## <a name="azure-vm-backup-support"></a>Azure VM バックアップのサポート

### <a name="azure-vm-limits"></a>Azure VM の制限

**制限** | **詳細**
--- | ---
**Azure VM のデータ ディスク数** | [Azure VM バックアップのサポート マトリックス](./backup-support-matrix-iaas.md#vm-storage-support)を確認してください。
**Azure VM のデータ ディスク サイズ** | 個々のディスク サイズは最大 32 TB で、VM 内のすべてのディスクに対して最大 256 TB となります。

### <a name="azure-vm-backup-options"></a>Azure VM のバックアップ オプション

Azure VM をバックアップしたい場合に何がサポートされるかを以下に示します。

**マシン** | **バックアップされる項目** | **場所** | **機能**
--- | --- | --- | ---
**VM 拡張機能を使用した Azure VM のバックアップ** | VM 全体 | コンテナーへのバックアップ。 | VM のバックアップを有効にするときにインストールされる拡張機能。<br/><br/> 1 日 1 回のバックアップ。<br/><br/> Windows VM の場合はアプリ対応バックアップ、Linux VM の場合はファイル整合性バックアップ。 Linux マシンでは、カスタム スクリプトを使用して、アプリ整合性を構成できます。<br/><br/> VM またはディスクの復元。<br/><br/>[Active Directory ドメイン コントローラーのバックアップおよび復元](active-directory-backup-restore.md)がサポートされています。<br><br> Azure VM は、オンプレミスの場所へはバックアップできません。
**MARS エージェントを使用する Azure VM のバックアップ** | ファイル、フォルダー、システム状態 | コンテナーへのバックアップ。 | 1 日に 3 回のバックアップ。<br/><br/> VM 全体ではなく特定のファイルまたはフォルダーをバックアップしたい場合は、MARS エージェントを VM 拡張機能と共に実行できます。
**Azure VM (DPM あり)** | ファイル、フォルダー、ボリューム、システム状態、アプリ データ | DPM が実行されている Azure VM のローカル ストレージへのバックアップ。 DPM はその後、コンテナーにバックアップされます。 | アプリ対応のスナップショット。<br/><br/> バックアップと回復の完全な細分性。<br/><br/> VM (Hyper-V または VMware) で Linux がサポートされています。<br/><br/> Oracle はサポートされていません。
**Azure VM (MABS あり)** | ファイル、フォルダー、ボリューム、システム状態、アプリ データ | MABS が実行されている Azure VM のローカル ストレージへのバックアップ。 MABS はその後、コンテナーにバックアップされます。 | アプリ対応のスナップショット。<br/><br/> バックアップと回復の完全な細分性。<br/><br/> VM (Hyper-V または VMware) で Linux がサポートされています。<br/><br/> Oracle はサポートされていません。

## <a name="linux-backup-support"></a>Linux のバックアップ サポート

Linux マシンをバックアップをしたい場合に何がサポートされるかを以下に示します。

**バックアップの種類** | **Linux (Azure での動作保証済み)**
--- | ---
**Linux を実行しているオンプレミスのマシンの直接バックアップ** | サポートされていません。 MARS エージェントは Windows マシンにのみインストールできます。
**エージェント拡張機能を使用した Linux を実行している Azure VM のバックアップ** | [カスタム スクリプト](backup-azure-linux-app-consistent.md)を使用したアプリ整合性バックアップ。<br/><br/> ファイルレベルの回復。<br/><br/> 復旧ポイントまたはディスクから VM を作成することによる復元。
**DPM を使用した、Linux を実行しているオンプレミス マシンでのバックアップ** | Hyper-V および VMware 上の Linux ゲスト VM のファイル整合性バックアップ。<br/><br/> Hyper-V および VMware Linux ゲスト VM の VM の復元。
**MABS を使用した、Linux を実行しているオンプレミス マシンでのバックアップ** | Hyper-V および VMware 上の Linux ゲスト VM のファイル整合性バックアップ。<br/><br/> Hyper-V および VMware Linux ゲスト VM の VM の復元。
**MABS または DPM を使用した Linux Azure VM のバックアップ** | サポートされていません。

## <a name="daylight-saving-time-support"></a>夏時間のサポート

Azure Backup では、Azure VM のバックアップでの夏時間への時計の自動調整はサポートしていません。 バックアップ時刻の繰り上げや繰り下げは行われません。 バックアップが希望時刻に実行されるようにするには、必要に応じてバックアップ ポリシーを手動で変更してください。

## <a name="disk-deduplication-support"></a>ディスクの重複除去のサポート

ディスクの重複除去のサポートは、次のとおりです。

- Windows を実行している Hyper-V VM のバックアップに DPM または MABS を使用している場合、オンプレミスでディスクの重複除去がサポートされます。 Windows Server では、VM にバックアップ ストレージとして接続されている仮想ハード ディスク (VHD) の重複除去を (ホスト レベルで) 実行します。
- Azure では、どの Backup コンポーネントでも重複除去はサポートされていません。 DPM および MABS が Azure にデプロイされている場合、VM にアタッチされているストレージ ディスクでは重複除去はされません。

## <a name="security-and-encryption-support"></a>セキュリティと暗号化のサポート

Azure Backup では、転送中のデータと保存データの暗号化をサポートしています。

### <a name="network-traffic-to-azure"></a>Azure へのネットワーク トラフィック

- サーバーから Recovery Services コンテナーへのバックアップ トラフィックは、Advanced Encryption Standard 256 を使用して暗号化されます。
- バックアップ データは、セキュリティで保護された HTTPS リンク経由で送信されます。

### <a name="data-security"></a>データのセキュリティ

- バックアップ データは、暗号化された形式で Recovery Services コンテナーに格納されます。
- MARS エージェントを使用してオンプレミスのサーバーからデータをバックアップする場合、データは Azure Backup にアップロードする前にパスフレーズを使用して暗号化され、Azure Backup からダウンロードされた後に初めて暗号化が解除されます。
- Azure VM をバックアップする場合は、仮想マシン *内* で暗号化を設定する必要があります。
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

## <a name="cross-region-restore"></a>リージョンをまたがる復元

Azure Backup に、データの可用性と回復性の機能を強化するために、リージョンをまたがる復元機能が追加されました。これにより、セカンダリ リージョンにデータを完全に復元することができます。 この機能を構成するには、[リージョンをまたがる復元の設定に関する記事](backup-create-rs-vault.md#set-cross-region-restore)を参照してください。 この機能は、次の管理の種類でサポートされています。

| バックアップの管理の種類 | サポートされています                                                    | サポートされているリージョン |
| ---------------------- | ------------------------------------------------------------ | ----------------- |
| Azure VM               | マネージド ディスクとアンマネージド ディスク両方の Azure VM でサポートされています。 クラシックVM に関してはサポートされていません。 | フランス中部、オーストラリア中部、南アフリカ北部、アラブ首長国連邦北部、スイス北部、ドイツ中西部、ノルウェー東部を除くすべての Azure パブリック リージョンおよびソブリン リージョンで利用できます。 <br>これらのリージョンでの使用については、[AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) までお問い合わせください。 |
| SQL/SAP HANA | プレビュー段階                                                      | フランス中部、オーストラリア中部、南アフリカ北部、アラブ首長国連邦北部、スイス北部、ドイツ中西部、ノルウェー東部を除くすべての Azure パブリック リージョンおよびソブリン リージョンで利用できます。 <br>これらのリージョンでの使用については、[AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) までお問い合わせください。 |
| MARS エージェント/オンプレミス  | いいえ                                                           | 該当なし               |
| AFS (Azure ファイル共有)                 | いいえ                                                           | 該当なし               |

## <a name="next-steps"></a>次のステップ

- Azure VM バックアップの[サポート マトリックスを確認します](backup-support-matrix-iaas.md)。

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
