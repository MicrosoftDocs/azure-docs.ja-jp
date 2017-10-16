# 概要
## [ロジック アプリとは](logic-apps-what-are-logic-apps.md)
## [Enterprise Integration Pack を使用した B2B](logic-apps-enterprise-integration-overview.md)
## [コネクタ](../connectors/apis-list.md)

# 作業開始
## [初めてのロジック アプリの作成](logic-apps-create-a-logic-app.md)
## [ロジック アプリのテンプレート](logic-apps-use-logic-app-templates.md)
## [テンプレートからのロジック アプリの作成](logic-apps-arm-provision.md)
## [Logic Apps への MABS アプリの移行](logic-apps-move-from-mabs.md)

# 方法
## 構築
### [Visual Studio でロジック アプリをビルドしてデプロイする](logic-apps-deploy-from-vs.md)
### [条件を追加してワークフローを実行する](logic-apps-use-logic-app-features.md)
### [Switch ステートメントを追加する](logic-apps-switch-case.md)
### [Azure Functions を使用してカスタム コードを追加する](logic-apps-azure-functions.md)
### [ループ、スコープ、データの分割処理](logic-apps-loops-and-scopes.md)
### [メッセージをバッチ処理する](logic-apps-batch-process-send-receive-messages.md)
### [ロジック アプリの定義を作成する](logic-apps-author-definitions.md)
### [ロジック アプリを呼び出し、トリガーし、入れ子にする](logic-apps-http-endpoint.md)
### カスタム コネクタを開発する
#### [カスタム コネクタの概要](custom-connector-overview.md)
#### [Web API からコネクタを作成する](custom-connector-build-web-api-app-tutorial.md)
#### [Azure AD を使用してコネクタをセキュリティで保護する](custom-connector-azure-active-directory-authentication.md)
#### [Postman で API を記述する](custom-connector-api-postman-collection.md)
#### [カスタム コネクタの OpenAPI を拡張する](custom-connector-openapi-extensions.md)
#### [コネクタを登録する](logic-apps-custom-connector-register.md)
#### [コネクタの認定を受ける](custom-connector-submit-certification.md)
#### [カスタム コネクタに関する FAQ](custom-connector-faq.md)
### カスタム API を開発する
#### [ロジック アプリ用のカスタム API を作成する](logic-apps-create-api-app.md)
#### [カスタム API の呼び出しをセキュリティで保護する](logic-apps-custom-api-authentication.md)
#### [カスタム API をデプロイして呼び出す](logic-apps-custom-api-host-deploy-call.md)
### [エラーと例外を処理する](logic-apps-exception-handling.md)
### [コンテンツ タイプを処理する](logic-apps-content-type.md)
### [ロジック アプリをセキュリティで保護する](logic-apps-securing-a-logic-app.md)
### [トラブルシューティング](logic-apps-diagnosing-failures.md)
### [制限と構成](logic-apps-limits-and-config.md)

## Enterprise Integration Pack を使用した B2B
### [統合アカウント](logic-apps-enterprise-integration-create-integration-account.md)
### [パートナー](logic-apps-enterprise-integration-partners.md)
### [アグリーメント](logic-apps-enterprise-integration-agreements.md)
### [B2B の処理](logic-apps-enterprise-integration-b2b.md)
### [EDI メッセージをバッチとして送信する](logic-apps-scenario-edi-send-batch-messages.md)
### [XML の処理](logic-apps-enterprise-integration-xml.md)
### [フラット ファイルの処理](logic-apps-enterprise-integration-flatfile.md)
### [XML を検証する](logic-apps-enterprise-integration-xml-validation.md)
### [XML 検証用のスキーマを追加する](logic-apps-enterprise-integration-schemas.md)
### [XML を変換する](logic-apps-enterprise-integration-transform.md)
### [XML 変換のためのマップを追加する](logic-apps-enterprise-integration-maps.md)
### [B2B セキュリティ用の証明書を追加する](logic-apps-enterprise-integration-certificates.md)
### [アーティファクトのメタデータを格納する](logic-apps-enterprise-integration-metadata.md)
### [AS2 のエンタープライズ統合](logic-apps-enterprise-integration-as2.md)
### [AS2 のエンコード](logic-apps-enterprise-integration-as2-encode.md)
### [AS2 のデコード](logic-apps-enterprise-integration-as2-decode.md)
### [EDIFACT のエンタープライズ統合](logic-apps-enterprise-integration-edifact.md)
### [EDIFACT のエンコード](logic-apps-enterprise-integration-edifact-encode.md)
### [EDIFACT のデコード](logic-apps-enterprise-integration-edifact-decode.md)
### [UNH2.5 セグメントを持つ EDIFACT ドキュメントを処理する方法](logic-apps-enterprise-integration-edifact_inputfile_unh2.5.md)
### [X12 のエンタープライズ統合](logic-apps-enterprise-integration-x12.md)
### [X12 のエンコード](logic-apps-enterprise-integration-x12-encode.md)
### [X12 のデコード](logic-apps-enterprise-integration-x12-decode.md)
### [障害復旧](logic-apps-enterprise-integration-b2b-business-continuity.md)
### [エラーと解決策](logic-apps-enterprise-integration-b2b-list-errors-solutions.md)

## オンプレミスのデータへのアクセス
### [オンプレミスのデータに接続する](logic-apps-gateway-connection.md)
### [データ ゲートウェイをインストールする](logic-apps-gateway-install.md)

## 自動化とデプロイ
### [自動デプロイ テンプレートを作成する](logic-apps-create-deploy-template.md)
### [Visual Studio から発行する](logic-apps-deploy-from-vs.md)

## 管理と監視
### [Visual Studio でロジック アプリを管理する](logic-apps-manage-from-vs.md)
### [統合アカウントを管理する](logic-apps-enterprise-integration-accounts.md)
### [ロジック アプリの状態の監視](logic-apps-monitor-your-logic-apps.md)
### [OMS と Log Analytics でロジック アプリの状態を監視する](logic-apps-monitor-your-logic-apps-oms.md)
### [B2B メッセージを監視する](logic-apps-monitor-b2b-message.md)
#### [OMS で B2B メッセージを追跡する](logic-apps-track-b2b-messages-omsportal.md)
#### [OMS に対して B2B メッセージを照会する](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)
#### [AS2 の追跡スキーマ](logic-apps-track-integration-account-as2-tracking-schemas.md)
#### [X12 の追跡スキーマ](logic-apps-track-integration-account-x12-tracking-schema.md)
#### [カスタム追跡スキーマ](logic-apps-track-integration-account-custom-tracking-schema.md)

## [例、シナリオ、チュートリアル](logic-apps-examples-and-scenarios.md)
### [サーバーなしのソーシャル ダッシュ ボードを作成する](logic-apps-scenario-social-serverless.md)
### [Azure Functions を使用してロジック アプリを呼び出す](logic-apps-scenario-function-sb-trigger.md)
### [エラーと例外の処理を追加する](logic-apps-scenario-error-and-exception-handling.md)
### [B2B の処理](logic-apps-enterprise-integration-b2b.md) 

## 価格と課金
### [料金](https://azure.microsoft.com/pricing/details/logic-apps/)
### [使用量の測定](logic-apps-pricing.md)

# サーバーレス
## 概要
### [Azure でのサーバーレス](logic-apps-serverless-overview.md)
## 作業の開始
### [Visual Studio でサーバーレス アプリを作成する](logic-apps-serverless-get-started-vs.md)
## シナリオとチュートリアル
### [カスタマー フィードバックを収集する](logic-apps-scenario-social-serverless.md)
### [自動メール送信](https://channel9.msdn.com/Blogs/Microsoft-Integration/Build-and-Deploy-Serverless-Part-1)
### [B2B/EDI 注文処理](logic-apps-enterprise-integration-overview.md)

# リファレンス
## [ワークフロー定義言語](logic-apps-workflow-definition-language.md)
## [ワークフローのアクションとトリガー](logic-apps-workflow-actions-triggers.md)
## [REST API](/rest/api/logic/)
## [Azure PowerShell](/powershell/module/azurerm.logicapp)
## コネクタ: 詳細のトピック
### [AS2](logic-apps-enterprise-integration-as2.md)
### [Azure Blob Storage](../connectors/connectors-create-api-azureblobstorage.md)
### [Azure Functions](logic-apps-azure-functions.md)
### [Azure Logic Apps](logic-apps-http-endpoint.md)
### [Azure SQL Database](../connectors/connectors-create-api-sqlazure.md)
### [カスタム API/Web アプリ](logic-apps-create-api-app.md)
### [DB2](../connectors/connectors-create-api-db2.md)
### [Dynamics 365 CRM Online](../connectors/connectors-create-api-crmonline.md)
### [EDIFACT](logic-apps-enterprise-integration-edifact.md)
### [Event Hubs](../connectors/connectors-create-api-azure-event-hubs.md)
### [ファイル システム](logic-apps-using-file-connector.md)
### [フラット ファイル](logic-apps-enterprise-integration-flatfile.md)
### [FTP](../connectors/connectors-create-api-ftp.md)
### [HTTP](../connectors/connectors-native-http.md)
### [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)
### [HTTP Webhook](../connectors/connectors-native-webhook.md)
### [Informix](../connectors/connectors-create-api-informix.md)
### [統合アカウント](logic-apps-enterprise-integration-metadata.md)
### [MQ](../connectors/connectors-create-api-mq.md)
### [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md)
### [Oracle Database](../connectors/connectors-create-api-oracledatabase.md)
### [要求/応答](../connectors/connectors-native-reqres.md)
### [Salesforce](../connectors/connectors-create-api-salesforce.md)
### [SAP](logic-apps-using-sap-connector.md)
### [スケジュール](../connectors/connectors-native-recurrence.md)
### [Service Bus](../connectors/connectors-create-api-servicebus.md)
### [SharePoint Online](../connectors/connectors-create-api-sharepointonline.md)
### [SharePoint Server](../connectors/connectors-create-api-sharepointonline.md)
### [SQL Server](../connectors/connectors-create-api-sqlazure.md)
### [XML を変換する](logic-apps-enterprise-integration-transform.md)
### [Twitter](../connectors/connectors-create-api-twitter.md)
### [X12](logic-apps-enterprise-integration-x12.md)
### [XML の検証](logic-apps-enterprise-integration-xml-validation.md)

## [コネクタ: 詳細](https://docs.microsoft.com/connectors/)
## スキーマの履歴
### [一般公開](logic-apps-schema-2016-04-01.md)
### [プレビュー](logic-apps-schema-2015-08-01.md)

# リソース
## [Azure のロードマップ](https://azure.microsoft.com/roadmap/)
## [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azurelogicapps)
## [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)
## [サービスの更新情報](https://azure.microsoft.com/updates/?product=logic-apps)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-logic-apps)
