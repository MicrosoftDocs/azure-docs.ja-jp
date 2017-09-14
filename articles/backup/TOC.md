
# 概要
## [Azure Backup とは](backup-introduction-to-azure-backup.md)

# 作業開始
## [Azure VM のバックアップ](backup-azure-vms-first-look-arm.md)
## [Windows Server または Windows コンピューターのバックアップ](backup-try-azure-backup-in-10-mins.md)
## [VMware サーバーのバックアップ](backup-azure-backup-server-vmware.md)

# 方法

## Azure Backup Server
### [Azure Backup Server の保護マトリックス](backup-mabs-protection-matrix.md)
### インストールまたはアップグレード
#### [Azure Portal での Azure Backup Server ワークロードの準備](backup-azure-microsoft-azure-backup.md)
#### [クラシック ポータルでの Azure Backup Server ワークロードの準備](backup-azure-microsoft-azure-backup-classic.md)
#### [Azure Backup Server へのストレージの追加](backup-mabs-add-storage.md)
#### [Azure Backup Server の v.2 へのアップグレード](backup-mabs-upgrade-to-v2.md)
#### [Azure Backup Server の無人インストール](backup-mabs-unattended-install.md)
### ワークロードの保護
#### [Azure Backup Server を使用した VMware サーバーのバックアップ](backup-azure-backup-server-vmware.md)
#### [Azure Backup Server を使用した Exchange のバックアップ](backup-azure-exchange-mabs.md)
#### [Azure Backup Server を使用した SharePoint ファームのバックアップ](backup-azure-backup-sharepoint-mabs.md)
#### [Azure Backup Server を使用した SQL のバックアップ](backup-azure-sql-mabs.md)
#### [システムの状態とベア メタル回復の保護](backup-mabs-system-state-and-bmr.md)
### [Azure Backup Server からのデータ復旧](backup-azure-alternate-dpm-server.md)

## Azure VM
### VM を準備する
#### [Resource Manager によりデプロイされた仮想マシンの準備](backup-azure-arm-vms-prepare.md)
#### [Linux VM のアプリケーション整合性バックアップ](backup-azure-linux-app-consistent.md)
#### [Azure 仮想マシンの準備](backup-azure-vms-prepare.md)
### 環境を計画する
#### [VM のバックアップ インフラストラクチャの計画](backup-azure-vms-introduction.md)
### VM をバックアップする
#### [Recovery Services 資格情報コンテナーへの Azure 仮想マシンのバックアップ](backup-azure-arm-vms.md)
#### [暗号化された仮想マシンのバックアップ](backup-azure-vms-encryption.md)
#### [Azure 仮想マシンのバックアップ](backup-azure-vms.md)
### VM の管理と監視
#### [Azure Portal での Azure VM のバックアップの管理](backup-azure-manage-vms.md)
#### [Azure Portal での Azure VM のバックアップに関するアラートの監視](backup-azure-monitor-vms.md)
#### [クラシック ポータルでの Azure VM のバックアップの管理と監視](backup-azure-manage-vms-classic.md)
### VM からのデータの復元
#### [Azure VM のバックアップからファイルを回復する](backup-azure-restore-files-from-vm.md)
#### [Resource Manager でデプロイされた VM の Azure Portal での復元](backup-azure-arm-restore-vms.md)
#### [暗号化された仮想マシンの復元](backup-azure-vms-encryption.md)
#### [Azure での仮想マシンの復元](backup-azure-restore-vms.md)
#### [暗号化された VM の Key Vault のキーとシークレットを復元](backup-azure-restore-key-secret.md)

## Azure Backup のレポートを構成する
### [Azure Backup レポートの構成](backup-azure-configure-reports.md)
### [Azure Backup レポートのデータ モデル](backup-azure-reports-data-model.md)
### [Azure Backup の Log Analytics データ モデル](backup-azure-log-analytics-data-model.md)

## Data Protection Manager
### [Azure Portal での DPM ワークロードの準備](backup-azure-dpm-introduction.md)
### [クラシック ポータルでの DPM ワークロードの準備](backup-azure-dpm-introduction-classic.md)
### [System Center DPM を使った Exchange サーバーのバックアップ](backup-azure-backup-exchange-server.md)
### [別の DPM サーバーへのデータ復旧](backup-azure-alternate-dpm-server.md)
### [DPM を使った SQL Server ワークロードのバックアップ](backup-azure-backup-sql.md)
### [DPM を使った SharePoint ファームのバックアップ](backup-azure-backup-sharepoint.md)

## PowerShell の使用
### [Azure Portal での Azure VM](backup-azure-vms-automation.md)
### [クラシック ポータルでの Azure VM](backup-azure-vms-classic-automation.md)
### [Azure Portal での DPM](backup-dpm-automation.md)
### [クラシック ポータルでの DPM](backup-dpm-automation-classic.md)
### [Azure Portal での Windows Server](backup-client-automation.md)
### [クラシック ポータルでの Windows Server](backup-client-automation-classic.md)

## Azure SQL Database
### [長期のバックアップ リテンション期間の構成](../sql-database/sql-database-configure-long-term-retention.md?toc=%2fazure%2fbackup%2ftoc.json)
### [Recovery Services コンテナー内のバックアップの表示](../sql-database/sql-database-view-backups-in-vault.md?toc=%2fazure%2fbackup%2ftoc.json)
### [長期のバックアップ リテンション期間からの復元](../sql-database/sql-database-restore-from-long-term-retention.md?toc=%2fazure%2fbackup%2ftoc.json)
### [長期的な Azure SQL バックアップの削除](../sql-database/sql-database-long-term-retention-delete.md?toc=%2fazure%2fbackup%2ftoc.json)

## Windows Server
### [Windows Server のファイルとフォルダーのバックアップ](backup-configure-vault.md)
### [Windows Server のシステムの状態のバックアップ](backup-azure-system-state.md)
### [Azure から Windows Server へのファイルの回復](backup-azure-restore-windows-server.md)
### [Windows Server のシステムの状態の復元](backup-azure-restore-system-state.md)
### [Recovery Services 資格情報コンテナーの監視と管理](backup-azure-manage-windows-server.md)
### クラシック ポータルを使用したバックアップと復元
#### [クラシック デプロイメント モデルを使用した Windows Server](backup-configure-vault-classic.md)
#### [クラシック デプロイメント モデルを使用した Backup 資格情報コンテナーの管理](backup-azure-manage-windows-server-classic.md)
#### [クラシック デプロイメント モデルを使用した Windows Server へのファイルの回復](backup-azure-restore-windows-server-classic.md)

## Recovery Services コンテナー
### [Recovery Services コンテナーの概要](backup-azure-recovery-services-vault-overview.md)
### [Backup コンテナーを Recovery Services コンテナーにアップグレードする](backup-azure-upgrade-backup-to-recovery-services.md)
### [Recovery Services コンテナーの削除](backup-azure-delete-vault.md)

## トラブルシューティング
### [Azure Portal での Azure VM のバックアップに関する問題](backup-azure-vms-troubleshoot.md)
### [クラシック ポータルでの Azure VM のバックアップに関する問題](backup-azure-vms-troubleshoot-classic.md)
### [Azure VM のバックアップの失敗: スナップショットの状態について VM エージェントと通信できませんでした - スナップショット VM サブタスクがタイムアウトしました](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)
### [Azure Backup でファイルとフォルダーのバックアップが遅い](backup-azure-troubleshoot-slow-backup-performance-issue.md)
### [Azure Backup Server のトラブルシューティング](backup-azure-mabs-troubleshoot.md)

# 概念

## FAQ
### [Recovery Services コンテナーに関する FAQ](backup-azure-backup-faq.md)
### [Azure VM バックアップに関する FAQ](backup-azure-vm-backup-faq.md)
### [Azure Backup エージェントを使用したファイル フォルダー バックアップに関する FAQ](backup-azure-file-folder-backup-faq.md)

## [ロールベースのアクセス制御](backup-rbac-rs-vault.md)
## [ハイブリッド バックアップのセキュリティ](backup-azure-security-feature.md)
## [オフライン バックアップの構成](backup-azure-backup-import-export.md)
## [テープ ライブラリの交換](backup-azure-backup-cloud-as-tape.md)


# リファレンス
## [PowerShell](/powershell/module/azurerm.recoveryservices.backup)
## [.NET](/dotnet/api/microsoft.azure.management.recoveryservices.backup)

# リソース
## [Azure のロードマップ](https://azure.microsoft.com/roadmap/)
## [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=windowsazureonlinebackup)
## [料金](https://azure.microsoft.com/pricing/details/backup/)
## [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)
## [サービスの更新情報](https://azure.microsoft.com/updates/?product=backup)
## [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=backup)
