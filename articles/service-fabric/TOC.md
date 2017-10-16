# [Service Fabric のドキュメント](/azure/service-fabric)
# 概要
## [Service Fabric とは](service-fabric-overview.md)

# クイック スタート
## [.NET アプリケーションの作成](service-fabric-quickstart-dotnet.md)
## [Linux コンテナー アプリケーションのデプロイ](service-fabric-quickstart-containers-linux.md)
## [Windows コンテナー アプリケーションのデプロイ](service-fabric-quickstart-containers.md)

# Tutorials (チュートリアル)
## .NET アプリのデプロイ
### [1 - .NET アプリケーションの構築](service-fabric-tutorial-create-dotnet-app.md)
### [2 - アプリケーションのデプロイ](service-fabric-tutorial-deploy-app-to-party-cluster.md)
### [3 - CI/CD の構成](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
### [4 - 監視と診断](service-fabric-tutorial-monitoring-aspnet.md)

## 既存の .NET アプリのコンテナー格納
### [1 - Azure でのセキュリティで保護されたクラスターの作成](service-fabric-tutorial-create-cluster-azure-ps.md)
### [2 - Docker Compose を使用した .NET アプリのデプロイ](service-fabric-host-app-in-a-container.md)
### [3 - コンテナーの監視](service-fabric-tutorial-monitoring-wincontainers.md)

## Linux コンテナー アプリの作成
### [1 - コンテナー イメージの作成](service-fabric-tutorial-create-container-images.md)
### [2 - コンテナーのパッケージ化とデプロイ](service-fabric-tutorial-package-containers.md)
### [3 - フェールオーバーとスケール](service-fabric-tutorial-containers-failover.md)

## クラスターの作成と管理
### 1 - Azure でのクラスターの作成
#### [1a - Windows クラスターの作成](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
#### [1b - Linux クラスターの作成](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
### [2 - Service Fabric での API Management のデプロイ](service-fabric-tutorial-deploy-api-management.md)

# サンプル
## [コード サンプル](https://azure.microsoft.com/resources/samples/?service=service-fabric)
## [Azure PowerShell](service-fabric-powershell-samples.md)
## [Service Fabric CLI](samples-cli.md)

# 概念
## [マイクロサービスの概要](service-fabric-overview-microservices.md)
## [全体像](service-fabric-content-roadmap.md)
## [アプリケーションのシナリオ](service-fabric-application-scenarios.md)
## [パターンとシナリオ](service-fabric-patterns-and-scenarios.md)
## [アーキテクチャ](service-fabric-architecture.md)
## [用語集](service-fabric-technical-overview.md)

## アプリケーションとサービスの構築
### サポートされるプログラミング モデル
#### [概要](service-fabric-choose-framework.md)
#### コンテナー
##### [概要](service-fabric-containers-overview.md)
##### [Docker Compose (プレビュー)](service-fabric-docker-compose.md)
##### [リソース ガバナンス](service-fabric-resource-governance.md)
#### Reliable Service
##### [概要](service-fabric-reliable-services-introduction.md)
##### [Reliable Services のライフサイクル - C#](service-fabric-reliable-services-lifecycle.md)
##### [Reliable Services のライフサイクル - Java](service-fabric-reliable-services-lifecycle-java.md)
##### [Reliable Collection](service-fabric-reliable-services-reliable-collections.md)
##### [Reliable Collection のガイドラインと推奨事項](service-fabric-reliable-services-reliable-collections-guidelines.md)
##### [Reliable Collection での作業](service-fabric-work-with-reliable-collections.md)
##### [トランザクションとロック](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
##### [Reliable Concurrent Queue](service-fabric-reliable-services-reliable-concurrent-queue.md)
##### [Reliable Collection のシリアル化](service-fabric-reliable-services-reliable-collections-serialization.md)
##### [Reliable State Manager と Reliable Collection の内部](service-fabric-reliable-services-reliable-collections-internals.md)
##### [詳細な使用方法](service-fabric-reliable-services-advanced-usage.md)

#### Reliable Actor
##### [概要](service-fabric-reliable-actors-introduction.md)
##### [アーキテクチャ](service-fabric-reliable-actors-platform.md)
##### [ライフサイクルとガベージ コレクション](service-fabric-reliable-actors-lifecycle.md)
##### [状態管理](service-fabric-reliable-actors-state-management.md)
##### [ポリモーフィズム](service-fabric-reliable-actors-polymorphism.md)
##### [再入](service-fabric-reliable-actors-reentrancy.md)
##### [型のシリアル化](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

### [アプリケーション モデル](service-fabric-application-model.md)
### [ホスティング モデル](service-fabric-hosting-model.md)

### サービス
#### [サービスのリソース](service-fabric-service-manifest-resources.md)
#### [サービスの状態](service-fabric-concepts-state.md)
#### [サービスのパーティション分割](service-fabric-concepts-partitioning.md)
#### [サービスの可用性](service-fabric-availability-services.md)
#### [レプリカとインスタンス](service-fabric-concepts-replica-lifecycle.md)
#### [再構成](service-fabric-concepts-reconfiguration.md)
#### サービス通信
##### [概要](service-fabric-connect-and-communicate-with-services.md)
##### [DNS サービス](service-fabric-dnsservice.md)
##### [リバース プロキシ](service-fabric-reverseproxy.md)
##### [セキュリティで保護された通信用のリバース プロキシの構成](service-fabric-reverseproxy-configure-secure-communication.md)
##### [リバース プロキシの診断](service-fabric-reverse-proxy-diagnostics.md)
### [アプリケーションのスケーラビリティ](service-fabric-concepts-scalability.md)
### [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

### [アプリケーション容量の計画](service-fabric-capacity-planning.md)

## アプリケーションの管理
### [概要](service-fabric-application-lifecycle.md)
### [ImageStoreConnectionString 設定](service-fabric-image-store-connection-string.md)
### アプリケーションのアップグレード
#### [概要](service-fabric-application-upgrade.md)
#### [構成](service-fabric-visualstudio-configure-upgrade.md)
#### [アプリケーション アップグレードのパラメーター](service-fabric-application-upgrade-parameters.md)
#### [アプリケーション アップグレードでのデータのシリアル化](service-fabric-application-upgrade-data-serialization.md)
#### [アプリケーション アップグレードの高度なトピック](service-fabric-application-upgrade-advanced.md)
### [Fault Analysis の概要](service-fabric-testability-overview.md)

## クラスターを作成および管理する
### [概要](service-fabric-deploy-anywhere.md)
### 計画と準備
#### [容量計画](service-fabric-cluster-capacity.md)
#### [障害復旧](service-fabric-disaster-recovery.md)
### [クラスターの説明](service-fabric-cluster-resource-manager-cluster-description.md)
### [クラスターのセキュリティ](service-fabric-cluster-security.md)
### [Linux と Windows での機能の違い](service-fabric-linux-windows-differences.md)
### Azure 上のクラスター
#### [ノードの種類と VM Scale Sets](service-fabric-cluster-nodetypes.md)
#### [クラスター ネットワークのパターン](service-fabric-patterns-networking.md)
### Cluster Resource Manager
#### [概要](service-fabric-cluster-resource-manager-introduction.md)
#### [アーキテクチャ](service-fabric-cluster-resource-manager-architecture.md)
#### [クラスターの説明](service-fabric-cluster-resource-manager-cluster-description.md)
#### [アプリケーション グループの概要](service-fabric-cluster-resource-manager-application-groups.md)
#### [Cluster Resource Manager 設定の構成](service-fabric-cluster-resource-manager-configure-services.md)
#### [リソース使用量メトリックス](service-fabric-cluster-resource-manager-metrics.md)
#### [サービス アフィニティの使用](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
#### [サービス配置ポリシー](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
#### [クラスターの管理](service-fabric-cluster-resource-manager-management-integration.md)
#### [クラスターの最適化](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
#### [クラスターの分散](service-fabric-cluster-resource-manager-balancing.md)
#### [調整](service-fabric-cluster-resource-manager-advanced-throttling.md)
#### [サービスの移動](service-fabric-cluster-resource-manager-movement-cost.md)

## [API Management との統合](service-fabric-api-management-overview.md)

## 監視と診断
### [概要](service-fabric-diagnostics-overview.md)
### [正常性モデル](service-fabric-health-introduction.md)
### [ステートフル Reliable Services における診断](service-fabric-reliable-services-diagnostics.md)
### [Reliable Actors の診断](service-fabric-reliable-actors-diagnostics.md)
### [Reliable Service Remoting のパフォーマンス カウンター](service-fabric-reliable-serviceremoting-diagnostics.md)

# ハウツー ガイド
## 開発環境を設定する
### [Windows](service-fabric-get-started.md)
### [Linux](service-fabric-get-started-linux.md)
### [Mac OS](service-fabric-get-started-mac.md)

## [Service Fabric CLI のセットアップ](service-fabric-cli.md)

## アプリケーションのビルド
### [Visual Studio での最初の C# アプリの作成](service-fabric-create-your-first-application-in-visual-studio.md)
### ゲスト実行可能サービスの構築
#### [Windows での Node.js アプリケーションのホスト](quickstart-guest-app.md)
#### [ゲスト実行可能ファイルをデプロイする](service-fabric-deploy-existing-app.md)
#### [複数のゲスト実行可能ファイルのデプロイ](service-fabric-deploy-multiple-apps.md)
### コンテナー サービスの構築
#### [Windows コンテナー アプリケーションの作成](service-fabric-get-started-containers.md)
#### [Linux コンテナー アプリケーションの作成](service-fabric-get-started-containers-linux.md)
#### [コンテナー セキュリティ](service-fabric-securing-containers.md)
#### [Docker Compose (プレビュー)](service-fabric-docker-compose.md)
#### [コンテナーとサービスのリソース ガバナンス](service-fabric-resource-governance.md)
#### [ボリュームとログ ドライバー](service-fabric-containers-volume-logging-drivers.md)
#### [コンテナー内のサービス](service-fabric-services-inside-containers.md)
#### [コンテナー ネットワーク モード](service-fabric-networking-modes.md)

### Reliable Services サービスの構築
#### [概要](service-fabric-reliable-services-introduction.md)
#### 概念
##### [Reliable Services のライフサイクル - C#](service-fabric-reliable-services-lifecycle.md)
##### [Reliable Services のライフサイクル - Java](service-fabric-reliable-services-lifecycle-java.md)

#### Reliable Collection
##### [Reliable Collection](service-fabric-reliable-services-reliable-collections.md)
##### [Reliable Collection のガイドラインと推奨事項](service-fabric-reliable-services-reliable-collections-guidelines.md)
##### [Reliable Collection での作業](service-fabric-work-with-reliable-collections.md)
##### [トランザクションとロック](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
##### [Reliable Concurrent Queue](service-fabric-reliable-services-reliable-concurrent-queue.md)
##### [Reliable Collection のシリアル化](service-fabric-reliable-services-reliable-collections-serialization.md)
##### [Reliable State Manager と Reliable Collection の内部](service-fabric-reliable-services-reliable-collections-internals.md)

#### 作業開始
##### [Windows での C# ](service-fabric-reliable-services-quick-start.md)
##### [Linux での Java](service-fabric-reliable-services-quick-start-java.md)
##### [Linux での C# アプリケーションの作成](service-fabric-create-your-first-linux-application-with-csharp.md)
#### サービスとの通信
##### [Reliable Services を使用した通信](service-fabric-reliable-services-communication.md)

##### [サービスのリモート処理 - C#](service-fabric-reliable-services-communication-remoting.md)
##### [サービスのリモート処理 - Java](service-fabric-reliable-services-communication-remoting-java.md)
##### [WCF](service-fabric-reliable-services-communication-wcf.md)
##### [通信のセキュリティ保護 - C#](service-fabric-reliable-services-secure-communication.md)
##### [通信のセキュリティ保護 - Java](service-fabric-reliable-services-secure-communication-java.md)

#### [構成](service-fabric-reliable-services-configuration.md)
#### [通知の送信](service-fabric-reliable-services-notifications.md)
#### [バックアップと復元](service-fabric-reliable-services-backup-restore.md)

### Reliable Actors サービスの構築
#### 作業開始
##### [Windows での C# ](service-fabric-reliable-actors-get-started.md)
##### [Linux での Java アクター](service-fabric-create-your-first-linux-application-with-java.md)
#### [通知の送信](service-fabric-reliable-actors-events.md)
#### [タイマーとアラームの設定](service-fabric-reliable-actors-timers-reminders.md)
#### [KvsActorStateProvider の構成](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [通信の設定の構成](service-fabric-reliable-actors-fabrictransportsettings.md)
#### [ReliableDictionaryActorStateProvider の構成](service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

### [Maven への対応を目的とした従来の Java アプリケーションの移行](service-fabric-migrate-old-javaapp-to-use-maven.md)

### [セキュリティで保護された通信用のリバース プロキシの構成](service-fabric-reverseproxy-configure-secure-communication.md)

### [ASP.NET Core サービスの構築](service-fabric-add-a-web-frontend.md)

### セキュリティを構成する
#### [アプリケーション シークレットの管理](service-fabric-application-secret-management.md)  
#### [アプリケーションのセキュリティ ポリシーの構成](service-fabric-application-runas-security.md)

## Windows 開発環境での作業
### [Visual Studio でのアプリケーションの管理](service-fabric-manage-application-in-visual-studio.md)
### [Visual Studio でセキュリティ保護された接続を構成する](service-fabric-visualstudio-configure-secure-connections.md)
### [複数環境向けアプリケーションの構成](service-fabric-manage-multiple-environment-app-configuration.md)
### [VS での .NET サービスのデバッグ](service-fabric-debugging-your-application.md)
### [一般的なエラーと例外](service-fabric-errors-and-exceptions.md)
### [ローカルでの監視と診断](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## Linux 開発環境での作業
### [Java 開発用の Eclipse プラグインの使用](service-fabric-get-started-eclipse.md)
### [Eclipse での Java サービスのデバッグ](service-fabric-debugging-your-application-java.md)
### [ローカルでの監視と診断](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

## [API Management と Service Fabric を Azure にデプロイする](service-fabric-api-management-quick-start.md)

## Cloud Services から移行する
### [Cloud Services と Service Fabric の比較](service-fabric-cloud-services-migration-differences.md)
### [Service Fabric への移行](service-fabric-cloud-services-migration-worker-role-stateless-service.md)
### [推奨プラクティス](/azure/architecture/service-fabric/migrate-from-cloud-services)

## アプリケーションのライフサイクルを管理する
### [アプリケーションのパッケージ化](service-fabric-package-apps.md)

### アプリケーションをデプロイまたは削除する
#### [ローカル クラスターへのアプリケーションのデプロイ](service-fabric-get-started-with-a-local-cluster.md)
#### [Azure PowerShell](service-fabric-deploy-remove-applications.md)
#### [Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
#### [Visual Studio](service-fabric-publish-app-remote-cluster.md)
#### [FabricClient API](service-fabric-deploy-remove-applications-fabricclient.md)

### アプリケーションのアップグレード
#### [Azure PowerShell を使用したアップグレード](service-fabric-application-upgrade-tutorial-powershell.md)
#### [Visual Studio を使用したアップグレード](service-fabric-application-upgrade-tutorial.md)
#### [アプリケーション アップグレードのトラブルシューティング](service-fabric-application-upgrade-troubleshooting.md)

### アプリケーションとサービスをテストする
#### [サービス間通信のテスト](service-fabric-testability-scenarios-service-communication.md)
#### エラーをシミュレートする
##### [制御された混乱の使用](service-fabric-controlled-chaos.md)
##### [テスト アクションの使用](service-fabric-testability-actions.md)
##### [ワークロード中](service-fabric-testability-workload-tests.md)
##### [テスト シナリオの使用](service-fabric-testability-scenarios.md)
##### [Node Transition API の使用](service-fabric-node-transition-apis.md)
#### [アプリケーションのロード テスト](service-fabric-vso-load-test.md)

### 継続的インテグレーションのセットアップ
#### [VSTS を使用した継続的インテグレーションのセットアップ](service-fabric-set-up-continuous-integration.md)
#### [Jenkins を使用した Linux Java アプリケーションのデプロイ](service-fabric-cicd-your-linux-java-application-with-jenkins.md)

## クラスターを作成および管理する
### Azure 上のクラスター
#### 作成
##### [Azure で初めてのクラスターを作成する](service-fabric-get-started-azure-cluster.md)
##### [Azure Portal](service-fabric-cluster-creation-via-portal.md)
##### [Azure リソース マネージャー](service-fabric-cluster-creation-via-arm.md)
#### スケール
##### [手動で](service-fabric-cluster-scale-up-down.md)
##### [プログラムで](service-fabric-cluster-programmatic-scaling.md)
#### [アップグレード](service-fabric-cluster-upgrade.md)
#### [アクセス制御の設定](service-fabric-cluster-security-roles.md)
#### [構成](service-fabric-cluster-fabric-settings.md)
#### [ロード バランサーでポートを開く](create-load-balancer-rule.md)
#### [クラスター証明書の管理](service-fabric-cluster-security-update-certs-azure.md)
#### [削除](service-fabric-cluster-delete.md)

### スタンドアロンのクラスター
#### [デプロイの計画と準備](service-fabric-cluster-standalone-deployment-preparation.md)
#### 作成
##### [初めてのスタンドアロン クラスターを作成する](service-fabric-get-started-standalone-cluster.md)
##### [オンプレミスでの作成](service-fabric-cluster-creation-for-windows-server.md)
##### [証明書を使用したセキュリティ保護](service-fabric-windows-cluster-x509-security.md)  
##### [Windows セキュリティを使用したセキュリティ保護](service-fabric-windows-cluster-windows-security.md)
##### [スタンドアロン パッケージの内容](service-fabric-cluster-standalone-package-contents.md)
#### [スケール](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [アクセス制御の設定](service-fabric-cluster-security-roles.md)
#### [構成](service-fabric-cluster-manifest.md)
#### [アップグレード](service-fabric-cluster-upgrade-windows-server.md)

### [クラスターの視覚化](service-fabric-visualizing-your-cluster.md)
### [セキュリティ保護されたクラスターへの接続](service-fabric-connect-to-secure-cluster.md)
### [クラスター ノードへのパッチの適用](service-fabric-patch-orchestration-application.md)

### クラスター リソースを管理および調整する
#### [Cluster Resource Manager の概要](service-fabric-cluster-resource-manager-introduction.md)
#### [Cluster Resource Manager のアーキテクチャ](service-fabric-cluster-resource-manager-architecture.md)
#### [クラスターの説明](service-fabric-cluster-resource-manager-cluster-description.md)
#### [アプリケーション グループの概要](service-fabric-cluster-resource-manager-application-groups.md)
#### [Cluster Resource Manager 設定の構成](service-fabric-cluster-resource-manager-configure-services.md)
#### [リソース使用量メトリックス](service-fabric-cluster-resource-manager-metrics.md)
#### [サービス アフィニティの使用](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
#### [サービス配置ポリシー](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
#### [クラスターの管理](service-fabric-cluster-resource-manager-management-integration.md)
#### [クラスターの最適化](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
#### [クラスターの分散](service-fabric-cluster-resource-manager-balancing.md)
#### [調整](service-fabric-cluster-resource-manager-advanced-throttling.md)
#### [サービスの移動](service-fabric-cluster-resource-manager-movement-cost.md)

## 監視と診断
### [アプリケーションの監視と診断](service-fabric-diagnostics-overview.md)
### イベントの生成
#### [プラットフォーム レベルのイベントの生成](service-fabric-diagnostics-event-generation-infra.md)
##### [操作チャネル](service-fabric-diagnostics-event-generation-operational.md)
##### [Reliable Services のイベント](service-fabric-reliable-services-diagnostics.md)
##### [Reliable Actors のイベント](service-fabric-reliable-actors-diagnostics.md)
##### [パフォーマンス メトリック](service-fabric-diagnostics-event-generation-perf.md)
#### [アプリケーション レベルのイベントの生成](service-fabric-diagnostics-event-generation-app.md)
### アプリケーションとクラスターの正常性の確認
#### [Service Fabric の正常性の監視](service-fabric-health-introduction.md)
#### [サービス正常性のレポートとチェック](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
#### [カスタム正常性レポートの追加](service-fabric-report-health.md)
#### [システム正常性レポートを使用したトラブルシューティング](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
#### [正常性レポートの確認](service-fabric-view-entities-aggregated-health.md)
#### [Windows Server コンテナーの監視](service-fabric-diagnostics-containers-windowsserver.md)
### 集計値
#### [EventFlow を使ったイベントの集計](service-fabric-diagnostics-event-aggregation-eventflow.md)
#### Azure 診断を使ったイベントの集計
##### [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
##### [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
### イベントの分析
#### [Application Insight を使ったイベントの分析](service-fabric-diagnostics-event-analysis-appinsights.md)
#### [OMS を使ったイベントの分析](service-fabric-diagnostics-event-analysis-oms.md)
### [ローカル クラスターのトラブルシューティング](service-fabric-troubleshoot-local-cluster-setup.md)

# リファレンス
## [Azure PowerShell](/powershell/module/azurerm.servicefabric/)
## [PowerShell](/powershell/module/servicefabric/?view=azureservicefabricps)
## [Azure CLI](/cli/azure/sf)
## [Service Fabric CLI](service-fabric-sfctl.md)
### [sfctl application](service-fabric-sfctl-application.md)
### [sfctl chaos](service-fabric-sfctl-chaos.md)
### [sfctl cluster](service-fabric-sfctl-cluster.md)
### [sfctl compose](service-fabric-sfctl-compose.md)
### [sfctl is](service-fabric-sfctl-is.md)
### [sfctl node](service-fabric-sfctl-node.md)
### [sfctl partition](service-fabric-sfctl-partition.md)
### [sfctl replica](service-fabric-sfctl-replica.md)
### [sfctl rpm](service-fabric-sfctl-rpm.md)
### [sfctl service](service-fabric-sfctl-service.md)
### [sfctl store](service-fabric-sfctl-store.md)
## [Java API](/java/api/overview/azure/servicefabric)
## [.NET](/dotnet/api/overview/azure/service-fabric?view=azure-dotnet)
## [REST ()](/rest/api/servicefabric)

# リソース
## [Azure のロードマップ](https://azure.microsoft.com/roadmap/)
## [一般的な質問](service-fabric-common-questions.md)
## [ラーニング パス](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
## [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)
## [料金](https://azure.microsoft.com/pricing/details/service-fabric/)
## [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)
## [コード サンプル](http://aka.ms/servicefabricsamples)
## [サポート オプション](service-fabric-support.md)
## [サービスの更新情報](https://azure.microsoft.com/updates/?product=service-fabric)
## [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=service-fabric)
