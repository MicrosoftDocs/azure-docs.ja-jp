# 概要
## [Site Recovery とは](site-recovery-overview.md)
## [Site Recovery のしくみ](site-recovery-components.md)
## [Azure への Hyper-V のレプリケーションのしくみ](site-recovery-hyper-v-azure-architecture.md)
## [保護できるワークロード](site-recovery-workload.md)
## [Site Recovery のサポート マトリックス](site-recovery-support-matrix-to-azure.md)
## [FAQ](site-recovery-faq.md)
## [概要を見る](https://azure.microsoft.com/resources/videos/index/?services=recovery-manager)

# 作業の開始
## [VMware VM を Azure にレプリケートする](site-recovery-vmware-to-azure.md)
## [マルチテナント デプロイで VMware VM を Azure にレプリケートする (CSP)](site-recovery-multi-tenant-support-vmware-using-csp.md)
## [Hyper-V VM を Azure にレプリケートする (VMM 使用)](site-recovery-vmm-to-azure.md)
## [Hyper-V VM を Azure にレプリケート](site-recovery-hyper-v-site-to-azure.md)
## [VMware VM と物理サーバーをセカンダリ サイトにレプリケートする](site-recovery-vmware-to-vmware.md)
## [Hyper-V VM をセカンダリ サイトにレプリケートする (VMM 使用)](site-recovery-vmm-to-vmm.md)

# 方法
## プラン
### [デプロイメントの前提条件](site-recovery-prereq.md)
### [ネットワーク インフラストラクチャの考慮事項](site-recovery-network-design.md)
### [容量の計画と Azure への VMware レプリケーションのスケーリング](site-recovery-plan-capacity-vmware.md)
### [Azure への VMware レプリケーションのための Deployment Planner](site-recovery-deployment-planner.md)
### [Hyper-V レプリケーションのための Site Recovery Capacity Planner](site-recovery-capacity-planner.md)

## 構成
### [ソース環境をセットアップする](site-recovery-set-up-vmware-to-azure.md)
### [ターゲット環境をセットアップする](site-recovery-prepare-target-vmware-to-azure.md)
### [レプリケーションの設定を構成する](site-recovery-setup-replication-settings-vmware.md)
### [VMware のレプリケーション用にモビリティ サービスをデプロイする](site-recovery-vmware-to-azure-install-mob-svc.md)
#### [System Center Configuration Manager を使用してモビリティ サービスをデプロイする](site-recovery-install-mobility-service-using-sccm.md)
#### [Azure Automation DSC を使用してモビリティ サービスをデプロイする](site-recovery-automate-mobility-service-install.md)
### [Enable replication](site-recovery-replicate-vmware-to-azure.md)
## フェールオーバーとフェールバック
### [保護されたマシンのフェールオーバー](site-recovery-failover.md)
### [復旧計画を設定する](site-recovery-create-recovery-plans.md)
#### [復旧計画に Azure Runbook を追加する](site-recovery-runbook-automation.md)
### [テスト フェールオーバーの実行](site-recovery-test-failover-to-azure.md)
### [フェールオーバー後のマシンの再保護](site-recovery-how-to-reprotect.md)
### [Azure からのフェールバック](site-recovery-failback-azure-to-vmware.md)

## 移行
### [Azure への移行](site-recovery-migrate-to-azure.md)
### [Azure リージョンの間で移行](site-recovery-migrate-azure-to-azure.md)
### [AWS Windows インスタンスの Azure への移行](site-recovery-migrate-aws-to-azure.md)
## ワークロード
### [Active Directory と DNS](site-recovery-active-directory.md)
### [SQL Server](site-recovery-sql.md)
### [SharePoint](site-recovery-workload.md#protect-sharepoint)
### [Dynamics AX](site-recovery-workload.md#protect-dynamics-ax)
### [RDS](site-recovery-workload.md#protect-rds)
### [Exchange](site-recovery-workload.md#protect-exchange)
### [SAP](site-recovery-workload.md#protect-sap)
### [その他のワークロード](site-recovery-workload.md#workload-summary)
## レプリケーションの自動化
### [Azure への Hyper-V のレプリケーションの自動化 (VMM なし)](site-recovery-deploy-with-powershell-resource-manager.md)
### [Azure への Hyper-V のレプリケーションの自動化 (VMM を使用)](site-recovery-vmm-to-azure-powershell-resource-manager.md)
### [セカンダリ サイトへの Hyper-V のレプリケーションの自動化 (VMM を使用)](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
## 管理
### [レプリケーション設定の編集](site-recovery-setup-replication-settings-vmware.md#edit-replication-policy.md)
### [Azure でのプロセス サーバーの管理](site-recovery-vmware-setup-azure-ps-resource-manager.md)
### [構成サーバーの管理](site-recovery-vmware-to-azure-manage-configuration-server.md)
### [スケールアウト プロセス サーバーの管理](site-recovery-vmware-to-azure-manage-scaleout-process-server.md)
### [vCenter サーバーの管理](site-recovery-vmware-to-azure-manage-vCenter.md)
### [サーバーの削除と保護の無効化](site-recovery-manage-registration-and-protection.md)
## [監視とトラブルシューティング](site-recovery-monitoring-and-troubleshooting.md)

# リファレンス
## [PowerShell](/powershell/resourcemanager/azurerm.siterecovery/v3.2.0/azurerm.siterecovery)
## [PowerShell クラシック](/powershell/servicemanagement/azure.siterecovery/v3.1.0/azure.siterecovery)
## [REST ()](https://msdn.microsoft.com/en-us/library/mt750497)

# 関連項目
## [Azure Automation](/azure/automation/)

# リソース
## [ラーニング パス](https://azure.microsoft.com/documentation/learning-paths/site-recovery/)
## [フォーラム](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hypervrecovmgr)
## [ブログ](http://azure.microsoft.com/blog/tag/azure-site-recovery/)
## [料金](https://azure.microsoft.com/pricing/details/site-recovery/)
## [サービスの更新情報](https://azure.microsoft.com/updates/?product=site-recovery)
