# アーキテクチャ

## クラウドの基礎

### [耐障害性のあるアプリケーションの設計](guidance-resiliency-overview.md)
#### [回復性のチェックリスト](guidance-resiliency-checklist.md)
#### [障害モードの分析](guidance-resiliency-failure-mode-analysis.md)
#### [障害復旧](..\resiliency\resiliency-disaster-recovery-azure-applications.md)
#### [障害復旧と高可用性](..\resiliency\resiliency-disaster-recovery-high-availability-azure-applications.md)
#### [高可用性](..\resiliency\resiliency-high-availability-azure-applications.md)
#### [高可用性のチェック リスト](..\resiliency\resiliency-high-availability-checklist.md)
#### [Azure サービスに固有の回復性ガイダンス](..\resiliency\resiliency-service-guidance-index.md)
#### [データの破損または偶発的な削除からの復旧](..\resiliency\resiliency-technical-guidance-recovery-data-corruption.md)
#### [ローカル障害からの復旧](..\resiliency\resiliency-technical-guidance-recovery-local-failures.md)
#### [リージョン全体のサービス中断からの復旧](..\resiliency\resiliency-technical-guidance-recovery-loss-azure-region.md)
#### [オンプレミスから Azure への復旧](..\resiliency\resiliency-technical-guidance-recovery-on-premises-azure.md)
#### [Azure の回復性技術ガイダンス](..\resiliency\resiliency-technical-guidance.md)


## 参照アーキテクチャ

### [Azure での VM ワークロード の実行](guidance-ra-compute.md)
#### [Azure での Linux VM の実行](guidance-compute-single-vm-linux.md)
#### [Azure での Windows VM の実行](guidance-compute-single-vm.md)
#### [スケーラビリティと可用性のために Azure で複数の VM を実行する](guidance-compute-multi-vm.md)
#### [N 層アーキテクチャの Linux VM を実行する](guidance-compute-n-tier-vm-linux.md)
#### [N 層アーキテクチャの Windows VM を実行する](guidance-compute-n-tier-vm.md)
#### [高可用性のために複数のリージョンで Linux VM を実行する](guidance-compute-multiple-datacenters-linux.md)
#### [高可用性のために複数のリージョンで Windows VM を実行する](guidance-compute-multiple-datacenters.md)

### [オンプレミス ネットワークを Azure に接続する](guidance-ra-hybrid-networking.md)
#### [Azure ExpressRoute を使用したハイブリッド ネットワーク アーキテクチャ](guidance-hybrid-network-expressroute.md)
#### [Azure とオンプレミスの VPN を使用したハイブリッド ネットワーク アーキテクチャ](guidance-hybrid-network-vpn.md)
#### [高可用性ハイブリッド ネットワーク アーキテクチャ](guidance-hybrid-network-expressroute-vpn-failover.md)

### [Azure でのクラウド境界の保護](guidance-ra-network-security.md)
#### [Azure でのセキュリティ保護されたハイブリッド ネットワーク アーキテクチャ](guidance-iaas-ra-secure-vnet-hybrid.md)
#### [Azure とインターネットの間の DMZ](guidance-iaas-ra-secure-vnet-dmz.md)

### [Azure での ID 管理](guidance-ra-identity.md)
#### [Azure Active Directory の実装](guidance-identity-aad.md)
#### [ADDS の Azure への拡張](guidance-identity-adds-extend-domain.md)
#### [Azure での ADDS リソース フォレストの作成](guidance-identity-adds-resource-forest.md)
#### [Azure での ADFS の実装](guidance-identity-adfs.md)

### [Azure App Service の Web アプリケーションのアーキテクチャ](guidance-ra-app-service.md)
#### [基本的な Web アプリケーション](guidance-web-apps-basic.md)
#### [高可用性を備えた Web アプリケーション](guidance-web-apps-multi-region.md)
#### [Web アプリケーションでのスケーラビリティの向上](guidance-web-apps-scalability.md)


## クラウド アプリケーションのベスト プラクティス

### [API 設計ガイダンス](..\best-practices-api-design.md)
### [API 実装ガイダンス](..\best-practices-api-implementation.md)
### [自動スケール ガイダンス](..\best-practices-auto-scaling.md)
### [可用性のチェックリスト](..\best-practices-availability-checklist.md)
### [バックグラウンド ジョブのガイダンス](..\best-practices-background-jobs.md)
### [Azure のペアになっているリージョン](..\best-practices-availability-paired-regions.md)
### [キャッシュに関するガイダンス](..\best-practices-caching.md)
### [コンテンツ配信ネットワークのガイダンス](..\best-practices-cdn.md)
### [データのパーティション分割のガイダンス](..\best-practices-data-partitioning.md)
### [監視と診断のガイダンス](..\best-practices-monitoring.md)
### [Microsoft クラウド サービスとネットワーク セキュリティ](..\best-practices-network-security.md)
### [Azure Resource Manager テンプレートの設計パターン](..\best-practices-resource-manager-design-templates.md)
### [Azure リソースの推奨される名前付け規則](guidance-naming-conventions.md)
### [Azure Resource Manager のセキュリティに関する考慮事項](..\best-practices-resource-manager-security.md)
### [Azure Resource Manager のテンプレートでの状態の共有](..\best-practices-resource-manager-state.md)
### [再試行の一般的なガイダンス](..\best-practices-retry-general.md)
### [再試行サービス固有のガイダンス](..\best-practices-retry-service-specific.md)
### [スケーラビリティのチェックリスト](..\best-practices-scalability-checklist.md)


## シナリオのガイド

### [Azure での Elasticsearch に関するガイダンス](guidance-elasticsearch.md)
#### [Azure での Elasticsearch の実行](guidance-elasticsearch-running-on-azure.md)
#### [データ インジェストのパフォーマンスのチューニング](guidance-elasticsearch-tuning-data-ingestion-performance.md)
#### [データの集計とクエリのパフォーマンスのチューニング](guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md)
#### [回復力と回復の構成](guidance-elasticsearch-configuring-resilience-and-recovery.md)
#### [パフォーマンス テスト環境の作成](guidance-elasticsearch-creating-performance-testing-environment.md)
#### [JMeter テスト計画の実装](guidance-elasticsearch-implementing-jmeter-test-plan.md)
#### [JMeter JUnit サンプラーのデプロイ](guidance-elasticsearch-deploying-jmeter-junit-sampler.md)
#### [自動回復性テストの実行](guidance-elasticsearch-running-automated-resilience-tests.md)
#### [自動 パフォーマンス テストの実行](guidance-elasticsearch-running-automated-performance-tests.md)

### [マルチテナント アプリケーションの ID 管理](guidance-multitenant-identity.md)
#### [概要](guidance-multitenant-identity-intro.md)
#### [Tailspin Surveys アプリケーション](guidance-multitenant-identity-tailspin.md)
#### [Azure AD および OpenID Connect を使用した認証](guidance-multitenant-identity-authenticate.md)
#### [要求ベースの ID](guidance-multitenant-identity-claims.md)
#### [サインアップおよびテナントのオンボーディング](guidance-multitenant-identity-signup.md)
#### [アプリケーション ロール](guidance-multitenant-identity-app-roles.md)
#### [ロールベースおよびリソースベースの承認](guidance-multitenant-identity-authorize.md)
#### [バックエンド web API のセキュリティ保護](guidance-multitenant-identity-web-api.md)
#### [アクセス トークンのキャッシュ](guidance-multitenant-identity-token-cache.md)
#### [顧客の AD FS とのフェデレーション](guidance-multitenant-identity-adfs.md)
#### [クライアント アサーションを使用したアクセス トークンの取得](guidance-multitenant-identity-client-assertion.md)
#### [Azure Key Vault を使用したアプリケーション シークレットの保護](guidance-multitenant-identity-keyvault.md)
#### [高可用性仮想アプライアンスのデプロイ](guidance-nva-ha.md)


<!--HONumber=Nov16_HO4-->


