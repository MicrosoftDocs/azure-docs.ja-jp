
# 概要
## [Azure Backup とは](backup-introduction-to-azure-backup.md)

# 作業開始
## [ファイルとフォルダーのバックアップ](backup-try-azure-backup-in-10-mins.md)
## [Azure 仮想マシンのバックアップ](backup-azure-vms-first-look.md)
## [Azure VM の保護](backup-azure-vms-first-look-arm.md)

# 方法
## PowerShell の使用
### [Azure Portal での Azure VM](backup-azure-vms-automation.md)
### [クラシック ポータルでの Azure VM](backup-azure-vms-classic-automation.md)
### [Azure Portal での DPM](backup-dpm-automation.md)
### [クラシック ポータルでの DPM](backup-dpm-automation-classic.md)
### [Azure Portal での Windows Server](backup-client-automation.md)
### [クラシック ポータルでの Windows Server](backup-client-automation-classic.md)

## Azure Backup Server
### [Azure Portal での Azure Backup Server ワークロードの準備](backup-azure-microsoft-azure-backup.md)
### [クラシック ポータルでの Azure Backup Server ワークロードの準備](backup-azure-microsoft-azure-backup-classic.md)
### [Azure Backup Server を使用した Exchange のバックアップ](backup-azure-exchange-mabs.md)
### [Azure Backup Server を使用した SharePoint ファームのバックアップ](backup-azure-backup-sharepoint-mabs.md)
### [Azure Backup Server を使用した SQL のバックアップ](backup-azure-sql-mabs.md)

## Data Protection Manager
### [Azure Portal での DPM ワークロードの準備](backup-azure-dpm-introduction.md)
### [クラシック ポータルでの DPM ワークロードの準備](backup-azure-dpm-introduction-classic.md)
### [System Center DPM を使った Exchange サーバーのバックアップ](backup-azure-backup-exchange-server.md)
### [Backup 資格情報コンテナー内のデータを別の DPM サーバーに回復する](backup-azure-alternate-dpm-server.md)
### [DPM を使った SQL Server ワークロードのバックアップ](backup-azure-backup-sql.md)
### [DPM を使った SharePoint ファームのバックアップ](backup-azure-backup-sharepoint.md)

## Azure VM
### [Azure 仮想マシンの準備](backup-azure-vms-prepare.md)
### [Resource Manager によりデプロイされた仮想マシンの準備](backup-azure-arm-vms-prepare.md)
### [VM のバックアップ インフラストラクチャの計画](backup-azure-vms-introduction.md)
### [Backup 資格情報コンテナーへの Azure 仮想マシンのバックアップ](backup-azure-vms.md)
### [Recovery Services 資格情報コンテナーへの Azure 仮想マシンのバックアップ](backup-azure-arm-vms.md)
### [暗号化された仮想マシンのバックアップと復元](backup-azure-vms-encryption.md)
### [クラシック ポータルでの Azure VM のバックアップの管理と監視](backup-azure-manage-vms-classic.md)
### [Azure Portal での Azure VM のバックアップの管理](backup-azure-manage-vms.md)
### [Azure Portal での Azure VM のバックアップに関するアラートの監視](backup-azure-monitor-vms.md)
### [Azure VM のバックアップからファイルを回復する](backup-azure-restore-files-from-vm.md)
### [Azure での仮想マシンの復元](backup-azure-restore-vms.md)
### [Resource Manager でデプロイされた VM の Azure Portal での復元](backup-azure-arm-restore-vms.md)
### [Azure Backup を使用して暗号化された VM の Key Vault のキーとシークレットの復元](backup-azure-restore-key-secret.md)

## Azure SQL Database
### [長期のバックアップ リテンション期間の構成](../sql-database/sql-database-configure-long-term-retention.md?toc=%2fazure%2fbackup%2ftoc.json)
### [Recovery Services コンテナー内のバックアップの表示](../sql-database/sql-database-view-backups-in-vault.md?toc=%2fazure%2fbackup%2ftoc.json)
### [長期のバックアップ リテンション期間からの復元](../sql-database/sql-database-restore-from-long-term-retention.md?toc=%2fazure%2fbackup%2ftoc.json)
### [長期的な Azure SQL バックアップの削除](../sql-database/sql-database-long-term-retention-delete.md?toc=%2fazure%2fbackup%2ftoc.json)

## Windows のファイルとフォルダー
### [クラシック デプロイメント モデルを使用した Windows Server](backup-configure-vault-classic.md)
### [Resource Manager デプロイメント モデルを使用した Windows Server](backup-configure-vault.md)
### [クラシック デプロイメント モデルを使用した Backup 資格情報コンテナーの管理](backup-azure-manage-windows-server-classic.md)
### [Recovery Services 資格情報コンテナーの監視と管理](backup-azure-manage-windows-server.md)
### [Resource Manager デプロイメント モデルを使用した Windows Server へのファイルの回復](backup-azure-restore-windows-server.md)
### [クラシック デプロイメント モデルを使用した Windows Server へのファイルの回復](backup-azure-restore-windows-server-classic.md)

## [FAQ](backup-azure-backup-faq.md)

## トラブルシューティング
### [Azure Portal での Azure VM のバックアップに関する問題](backup-azure-vms-troubleshoot.md)
### [クラシック ポータルでの Azure VM のバックアップに関する問題](backup-azure-vms-troubleshoot-classic.md)
### [Azure Backup Server](backup-azure-mabs-troubleshoot.md)
### [Azure VM のバックアップの失敗: スナップショットの状態について VM エージェントと通信できませんでした - スナップショット VM サブタスクがタイムアウトしました](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)
### [Azure Backup でファイルとフォルダーのバックアップが遅い](backup-azure-troubleshoot-slow-backup-performance-issue.md)

# 概念
## [ロールベースのアクセス制御](backup-rbac-rs-vault.md)
## [ハイブリッド バックアップのセキュリティ](backup-azure-security-feature.md)
## [Azure Backup 資格情報コンテナーを削除する](backup-azure-delete-vault.md)
## [オフライン バックアップの構成](backup-azure-backup-import-export.md)
## [テープ ライブラリの交換](backup-azure-backup-cloud-as-tape.md)

# リファレンス
## [PowerShell](/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/azurerm.recoveryservices.backup)
## [.NET](/dotnet/api/microsoft.azure.management.recoveryservices.backup)

# リソース
## [料金](https://azure.microsoft.com/pricing/details/backup/)
## [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=windowsazureonlinebackup)
## [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=backup)
## [サービスの更新情報](https://azure.microsoft.com/updates/?product=backup)
