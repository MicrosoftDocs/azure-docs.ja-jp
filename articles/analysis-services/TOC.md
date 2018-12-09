# [Analysis Services のドキュメント](index.md)

# 概要

## [Analysis Services とは](analysis-services-overview.md)

# クイック スタート

## [サーバーの作成 - ポータル](analysis-services-create-server.md)
## [サーバーの作成 - PowerShell](analysis-services-create-powershell.md)
## [サーバーのファイアウォールの構成 - ポータル](analysis-services-qs-firewall.md)

# チュートリアル

## [1 - ポータルからサンプル モデルを追加する](analysis-services-create-sample-model.md) 
## [2 - サーバー管理者とユーザー ロールを構成する](tutorials/analysis-services-tutorial-roles.md)
## [3 - Power BI Desktop を使用して接続する](tutorials/analysis-services-tutorial-pbid.md)

# 概念

## [認証とユーザーのアクセス許可](analysis-services-manage-users.md)
## [サービス プリンシパルによる自動化](analysis-services-service-principal.md)
## [クライアント ライブラリ](analysis-services-data-providers.md)
## [互換性レベル](analysis-services-compat-level.md)
## [オンプレミスのデータ ソースへの接続](analysis-services-gateway.md)
## [サーバーへの接続](analysis-services-connect.md)
## [高可用性](analysis-services-bcdr.md)
## [サポートされるデータ ソース](analysis-services-datasource.md)

# 操作方法 

## サーバー
### [バックアップと復元](analysis-services-backup.md)
### [サーバー名の別名の構成](analysis-services-server-alias.md)
### [スケールアウトの構成](analysis-services-scale-out.md)
### [オンプレミス データ ゲートウェイのインストールと構成](analysis-services-gateway-install.md)
### [Azure Virtual Network 上のデータソースに対してゲートウェイを使用する](analysis-services-vnet-gateway.md)
### [サーバーの管理](analysis-services-manage.md)
### [サーバー メトリックの監視](analysis-services-monitor.md)
### [診断ログのセットアップ](analysis-services-logging.md)
### [サービス プリンシパルを作成する - Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)
### [サービス プリンシパルを作成する - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
### [サーバー管理者ロールにサービス プリンシパルを追加する](analysis-services-addservprinc-admins.md)

## モデル
### [Power BI Desktop ファイルのインポート](analysis-services-import-pbix.md)
### [Azure Portal でのモデルの作成 (プレビュー)](analysis-services-create-model-portal.md)
### [Visual Studio からのデプロイ (SSDT)](analysis-services-deploy.md)
### [REST API を使用した更新](analysis-services-async-refresh.md)
### [Visual Studio でのデータ モデリング - Adventure Works (1400) チュートリアル](tutorials/aas-adventure-works-tutorial.md)
#### [1 - 表形式モデル プロジェクトを作成する](tutorials/aas-lesson-1-create-a-new-tabular-model-project.md)
#### [2 - データを取得する](tutorials/aas-lesson-2-get-data.md)
#### [3 - 日付テーブルとしてマークする](tutorials/aas-lesson-3-mark-as-date-table.md) 
#### [4 - リレーションシップを作成する](tutorials/aas-lesson-4-create-relationships.md) 
#### [5 - 計算列を作成する](tutorials/aas-lesson-5-create-calculated-columns.md)
#### [6 - メジャーを作成する](tutorials/aas-lesson-6-create-measures.md)  
#### [7 - 主要業績評価指標を作成する](tutorials/aas-lesson-7-create-key-performance-indicators.md)  
#### [8 - パースペクティブを作成する](tutorials/aas-lesson-8-create-perspectives.md) 
#### [9 - 階層を作成する](tutorials/aas-lesson-9-create-hierarchies.md) 
#### [10 - パーティションを作成する](tutorials/aas-lesson-10-create-partitions.md) 
#### [11 - ロールを作成する](tutorials/aas-lesson-11-create-roles.md)
#### [12 - Excel で分析する](tutorials/aas-lesson-12-analyze-in-excel.md)
#### [13 - デプロイする](tutorials/aas-lesson-13-deploy.md)
#### [補足のレッスン - 詳細行](tutorials/aas-supplemental-lesson-detail-rows.md)
#### [補足のレッスン - 動的なセキュリティ](tutorials/aas-supplemental-lesson-dynamic-security.md)
#### [補足のレッスン - 不規則階層](tutorials/aas-supplemental-lesson-ragged-hierarchies.md)  

## セキュリティ
### [データベース ユーザーの管理](analysis-services-database-users.md)
### [サーバー管理者の管理](analysis-services-server-admins.md)

## 接続
### [Excel を使用した接続](analysis-services-connect-excel.md)
### [Power BI を使用した接続](analysis-services-connect-pbi.md)
### [.odc ファイルの作成](analysis-services-odc.md)

# リファレンス

## [PowerShell](analysis-services-powershell.md)
## [REST](/rest/api/analysisservices)
## [DAX](https://msdn.microsoft.com/library/gg413422.aspx)
## [Power Query M](https://msdn.microsoft.com/library/mt211003.aspx)
## [Tabular Model Scripting Language (TMSL)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference)
## [表形式オブジェクト モデル (TOM)](https://docs.microsoft.com/sql/analysis-services/tabular-model-programming-compatibility-level-1200/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo)
## [リソースとオブジェクトの制限](analysis-services-capacity-limits.md)

# リソース

## [サンプル](analysis-services-samples.md)
## [Analysis Services チーム ブログ](https://blogs.msdn.microsoft.com/analysisservices/)
## [Azure Analysis Services フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureAnalysisServices)
## [Azure のロードマップ](https://azure.microsoft.com/roadmap/?category=intelligence-analytics)
## [Azure の状態](https://azure.microsoft.com/status/)
## [フィードバック](https://feedback.azure.com/forums/556165-azure-analysis-services)
## [料金](https://azure.microsoft.com/pricing/details/analysis-services/)
## [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)
## [StackOverflow](https://stackoverflow.com/questions/tagged/azure-analysis-services)
## [ビデオ](https://azure.microsoft.com/resources/videos/index/?services=analysis-services&sort=newest)










