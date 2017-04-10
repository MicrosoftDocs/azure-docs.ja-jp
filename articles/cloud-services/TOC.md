# 概要
## [Cloud Services とは](cloud-services-choose-me.md)
## [クラウド サービスの構成ファイルとパッケージ化](cloud-services-model-and-package.md)

# 作業の開始
## [.NET クラウド サービスの例](cloud-services-dotnet-get-started.md)
## [Python for Visual Studio クラウド サービスの例](cloud-services-python-ptvs.md)
## [Microsoft HPC Pack を使用したハイブリッド HPC クラスターのセットアップ](cloud-services-setup-hybrid-hpcpack-cluster.md)

# 方法
## プラン
### [仮想マシン サイズ](cloud-services-sizes-specs.md)
### [更新プログラム](cloud-services-update-azure-service.md)

## 開発
### [PHP Web ロールと worker ロールの作成](../cloud-services-php-create-web-role.md)
### [Node.js アプリケーションの構築とデプロイ](cloud-services-nodejs-develop-deploy-app.md)
### [Express を使用した Node.js Web アプリケーションの構築](cloud-services-nodejs-develop-deploy-express-app.md)
### Storage と Visual Studio
#### [Blob Storage および接続されたサービス](../storage/vs-storage-cloud-services-getting-started-blobs.md)
#### [Queue Storage および接続されたサービス](../storage/vs-storage-cloud-services-getting-started-queues.md)
#### [Table Storage および接続されたサービス](../storage/vs-storage-cloud-services-getting-started-tables.md)
### 継続的なビルドおよびデプロイのためのパッケージの構成
#### [Visual Studio Team Services と Git](cloud-services-continuous-delivery-use-vso-git.md)
#### [Visual Studio Team Services](cloud-services-continuous-delivery-use-vso.md)
#### [TFS およびチームのビルド](cloud-services-dotnet-continuous-delivery.md)
### [ロールのトラフィック規則の構成](cloud-services-enable-communication-role-instances.md)
### [クラウド サービスのライフサイクル イベントの処理](cloud-services-role-lifecycle-dotnet.md)
### [Socket.io (Node.js)](cloud-services-nodejs-chat-app-socketio.md)
### [Twilio を使用した通話 (.NET)](../partner-twilio-cloud-services-dotnet-phone-call-web-role.md)
### [New Relic](../store-new-relic-cloud-services-dotnet-application-performance-management.md)

### スタートアップ タスクの構成
#### [スタートアップ タスクの作成](cloud-services-startup-tasks.md)
#### [一般的なスタートアップ タスク](cloud-services-startup-tasks-common.md)
#### [タスクを使用してクラウド サービス ロールで .NET をインストールする](cloud-services-dotnet-install-dotnet.md)

### リモート デスクトップの構成
#### [ポータル](cloud-services-role-enable-remote-desktop-new-portal.md)
#### [クラシック ポータル](cloud-services-role-enable-remote-desktop.md)
#### [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)

## デプロイ
### ポータルでのクラウド サービスの作成とデプロイ
#### [ポータル](cloud-services-how-to-create-deploy-portal.md)
#### [クラシック ポータル](cloud-services-how-to-create-deploy.md)
### [PowerShell での空のクラウド サービス コンテナーの作成](cloud-services-powershell-create-cloud-container.md)
### カスタム ドメイン名の構成
#### [ポータル](cloud-services-custom-domain-name-portal.md)
#### [クラシック ポータル](cloud-services-custom-domain-name.md)
### [クラウド サービス デプロイのステージング (Node.js)](cloud-services-nodejs-stage-application.md)
### [カスタム ドメイン コントローラーに接続する](cloud-services-connect-to-custom-domain.md)

## サービスの管理
### 一般的な管理タスク
#### [ポータル](cloud-services-how-to-manage-portal.md)
#### [クラシック ポータル](cloud-services-how-to-manage.md)
### クラウド サービスの構成
#### [ポータル](cloud-services-how-to-configure-portal.md)
#### [クラシック ポータル](cloud-services-how-to-configure.md)
### [Azure Automation を使用したクラウド サービスの管理](automation-manage-cloud-services.md)
### 自動スケールの構成
#### [ポータル](cloud-services-how-to-scale-portal.md)
#### [クラシック ポータル](cloud-services-how-to-scale.md)
### [Python を使用した Azure リソースの管理](cloud-services-python-how-to-use-service-management.md)

### [ゲスト OS の修正プログラム](cloud-services-guestos-msrc-releases.md)
### ゲスト OS の提供終了
#### [提供終了ポリシー](cloud-services-guestos-retirement-policy.md)
#### [ファミリ 1 の提供終了に関する通知](cloud-services-guestos-family1-retirement.md)
### [ゲスト OS のリリース ニュース](cloud-services-guestos-update-matrix.md)
### [Cloud Services ロール構成の XPath チート シート](cloud-services-role-config-xpath.md)

## 証明書の管理
### [Cloud Services と管理証明書](cloud-services-certs-create.md)
### SSL の構成 
#### [ポータル](cloud-services-configure-ssl-certificate-portal.md)
#### [クラシック ポータル](cloud-services-configure-ssl-certificate.md)

## 監視
### [クラウド サービスを監視する](cloud-services-how-to-monitor.md)
### [パフォーマンスのテスト](../vs-azure-tools-performance-profiling-cloud-services.md)
#### [Visual Studio プロファイラーを使用したテスト](cloud-services-performance-testing-visual-studio-profiler.md)
### 診断を有効化する
#### [PowerShell](cloud-services-diagnostics-powershell.md)
#### [.NET](cloud-services-dotnet-diagnostics.md)
#### [Visual Studio](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
### [Azure 診断でのパフォーマンス カウンターの使用](cloud-services-dotnet-diagnostics-performance-counters.md)
### [Azure Storage への診断データの保存と表示](cloud-services-dotnet-diagnostics-storage.md)
### [診断を使用したクラウド サービスのトレース](cloud-services-dotnet-diagnostics-trace-flow.md)
### [App Insights への診断データの送信](cloud-services-dotnet-diagnostics-applicationinsights.md)

## トラブルシューティング
### デバッグ 
#### [継続的な配信を使用したリモート デバッグの有効化](cloud-services-virtual-machines-dotnet-continuous-delivery-remote-debugging.md)
#### [クラウド サービスのオプション](../vs-azure-tools-debugging-cloud-services-overview.md)
#### [Visual Studio を使用したローカルのクラウド サービス](../vs-azure-tools-debug-cloud-services-virtual-machines.md)
#### [Visual Studio を使用した発行済みのクラウド サービス](../vs-azure-tools-intellitrace-debug-published-cloud-services.md)
### [クラウド サービスの割り当てエラー](cloud-services-allocation-failures.md)
### [クラウド サービス ロールがリサイクルされる一般的な原因](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md)
### [ロールに対する既定の一時フォルダーのサイズが小さすぎる](cloud-services-troubleshoot-default-temp-folder-size-too-small-web-worker-role.md)
### [一般的なデプロイの問題](cloud-services-troubleshoot-deployment-problems.md)
### [ロールが起動しない](cloud-services-troubleshoot-roles-that-fail-start.md)
### [復旧ガイダンス](cloud-services-disaster-recovery-guidance.md)
### [Cloud Services に関する FAQ](cloud-services-faq.md)

# リファレンス
## [.csdef XMLSchema](https://msdn.microsoft.com/library/azure/ee758711)
## [.cscfg XMLSchema](https://msdn.microsoft.com/library/azure/ee758710)
## [REST ()](https://msdn.microsoft.com/library/azure/ee460812)

# リソース
## [料金](https://azure.microsoft.com/pricing/details/cloud-services/)
## [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/en-us/home?forum=windowsazuredevelopment)
## [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=cloud-services)
## [サービスの更新情報](https://azure.microsoft.com/updates/?product=cloud-services&updatetype=&platform=)
## [ラーニング パス](https://azure.microsoft.com/documentation/learning-paths/cloud-services/)
