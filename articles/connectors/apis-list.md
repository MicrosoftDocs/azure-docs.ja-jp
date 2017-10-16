---
title: "Azure Logic Apps のコネクタ | Microsoft Docs"
description: "Microsoft が管理する、利用可能なすべてのコネクタから選択してロジック アプリを構築および作成する"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: f1f1fd50-b7f9-4d13-824a-39678619aa7a
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: mandia; ladocs
ms.openlocfilehash: c14ac7592efabfec8668d7437463e2d8771ee072
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2017
---
# <a name="connectors-list"></a>コネクタの一覧
> [!TIP]
> このトピックの「[A ～ Z の完全な一覧](#az)」には、Logic Apps で使用できるすべてのコネクタの一覧が示されています。 [コネクタの詳細情報](/connectors/)に関するページに、Swagger で定義されているトリガーとアクション、さらに各コネクタの制限が記載されています。

コネクタは、ロジック アプリを作成するときに不可欠な構成要素です。 これらのコネクタを使用することで、オンプレミスやクラウドのアプリケーションを実際に拡張して、作成したデータや既にあるデータに対してさまざまな操作を実行できます。 次のカテゴリのコネクタを利用できます。 

* **標準コネクタ**: ロジック アプリを使用する場合に、自動的に含まれ、使用できます。 例としては、Service Bus、Power BI、Oracle Database、OneDrive などがあります。

* **統合アカウント コネクタ**: 統合アカウントを購入した場合に使用できます。 これらのコネクタを使用すると、XML の変換と検証、AS2/X12/EDIFACT を使用した企業間メッセージの処理、フラット ファイルのエンコードとデコードを行うことができます。 BizTalk Server を利用しており、BizTalk ワークフローを Azure に拡張する場合は、これらのコネクタが適しています。  

    BizTalk Server には、ロジック アプリとの間で送受信を行う [Logic Apps アダプター](https://msdn.microsoft.com/library/mt787163.aspx)もあります。

* **エンタープライズ コネクタ**: MQ および SAP が含まれます。 追加の料金で利用できます。 

価格の詳細については、「[Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps/)」と[料金モデル](../logic-apps/logic-apps-pricing.md)に関する記事を参照してください。 

## <a name="popular-connectors"></a>人気のコネクタ
数千ものアプリケーションと何百万もの実行で、次のコネクタを使用して、データや情報が正常に処理されています。 次の表に、非常に人気があり、ユーザーがよく利用しているコネクタを示します。

| |  |  |  |
| --- | --- | --- | --- |
| [![API Icon][AzureBlobStorageicon]<br/>**Azure Blob<br/>Storage**][AzureBlobStoragedoc] | ストレージ アカウントを使用してタスクを自動化する場合は、このコネクタを検討してください。 CRUD (作成、読み取り、更新、削除) 操作をサポートしています。 | [![API アイコン][Azure-Functionsicon]<br/>**Azure Functions**][azure-functionsdoc] | C# または node.js のカスタム スニペットを実行する関数を作成し、ロジック アプリで使用できます。  |
| [![API アイコン][Dynamics-365icon]<br/>**Dynamics 365<br/>CRM Online**][Dynamics-365doc] | 人気の高いコネクタの 1 つです。 潜在顧客に関連するワークフローの自動化に役立つトリガーやアクションなどが用意されています。 | [![API アイコン][Event-Hubs-icon]<br/>**Event Hubs**][event-hubs-doc] | Event Hub のイベントを使用および発行します。 たとえば、ロジック アプリから Event Hubs を使用して出力を取得し、リアルタイム分析プロバイダーに送信できます。 |
| [![API Icon][FTPicon]<br/>**FTP**][FTPdoc] | FTP サーバーにインターネットからアクセスできる場合は、ファイルやフォルダーを使用するワークフローを自動化できます。 <br/><br/>SFTP コネクタを使用すると、SFTP も利用できます。 | [![API Icon][HTTPicon]<br/>**HTTP**][httpdoc] | ロジック アプリを使用して、HTTP 経由で任意のエンドポイントと通信できます。 |
| [![API Icon][Office-365-Outlookicon]<br/>**Office 365<br/>Outlook**][office365-outlookdoc] | 数多くのトリガーやアクションにより、ワークフロー内で Office 365 の電子メールやイベントを使用できます。 <br/><br/>このコネクタには、休暇申請や経費明細書などを承認するための*承認電子メール* アクションが用意されています。 <br/><br/>Office 365 ユーザーは、Office 365 ユーザー コネクタも使用できます。| [![API アイコン][HTTP-Requesticon]<br/>**要求 / 応答**][HTTP-Requestdoc] | このコネクタは、HTTPS URL を提供します。 ロジック アプリは、この URL への要求を受け取ると、起動します。 |
| [![API Icon][Salesforceicon]<br/>**Salesforce**][salesforcedoc] | Salesforce アカウントで簡単にサインインして、オブジェクト (例: 潜在顧客) などにアクセスできます。 |  [![API Icon][Service-Busicon]<br/>**Service Bus**][Service-Busdoc] | ロジック アプリにおいて最も人気の高いコネクタです。非同期メッセージングの処理、キュー、サブスクリプション、およびトピックを使用した発行/サブスクライブを行うためのトリガーとアクションが用意されています。 |
|  [![API アイコン][SharePointicon]<br/>**SharePoint<br/>Online**][SharePointdoc] | SharePoint を利用しており、自動化の恩恵を受けることができる場合は、このコネクタを検討することをお勧めします。 オンプレミスの SharePoint および SharePoint Online で使用できます。 | [![API Icon][SQL-Servericon]<br/>**SQL Server**][SQL-Serverdoc] | よく利用されているコネクタの 1 つです。オンプレミスの SQL Server および Azure SQL Database に接続できます。 | 
| [![API Icon][Twittericon]<br/>**Twitter**][Twitterdoc] | Twitter アカウントで簡単にサインインし、新しいツイートが投稿されたときにワークフローを開始できます。 その後、それらのツイートを SQL データベースまたは SharePoint リストに保存することができます。 | | | 

## <a name="integration-account-connectors"></a>統合アカウント コネクタ 

Enterprise Integration Pack (EIP) には、BizTalk Server コミュニティでよく知られているコネクタが含まれています。 [統合アカウント](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)を購入すると、次のコネクタも利用できます。 

|  |  |  |  |
| --- | --- | --- | --- |
| [![API アイコン][as2icon]<br/>**AS2</br>デコード**][as2decode] | [![API アイコン][as2icon]<br/>**AS2</br>エンコード**][as2encode] | [![API アイコン][x12icon]<br/>**EDIFACT</br>デコード**][EDIFACTdecode] | [![API アイコン][x12icon]<br/>**EDIFACT</br>エンコード**][EDIFACTencode] |
[![API アイコン][flatfileicon]<br/>**フラット ファイル</br>エンコード**][flatfiledoc] | [![API アイコン][flatfiledecodeicon]<br/>**フラット ファイル</br>デコード**][flatfiledecodedoc] | [![API アイコン][integrationaccounticon]<br/>**統合<br/>アカウント**][integrationaccountdoc] | [![API アイコン][xmltransformicon]<br/>**XML<br/>変換**][xmltransformdoc] |
| [![API アイコン][x12icon]<br/>**X12</br>デコード**][x12decode] | [![API アイコン][x12icon]<br/>**X12</br>エンコード**][x12encode] | [![API Icon][xmlvalidateicon]<br/>**XML <br/>検証**][xmlvalidatedoc] | |

## <a name="enterprise-connectors"></a>エンタープライズ コネクタ

ロジック アプリ内でエンタープライズ アプリケーションに接続します。

|  |  |
| --- | --- |
|[![API Icon][MQicon]<br/>**MQ**][mqdoc]|[![API アイコン][SAPicon]<br/>**SAP**][sapconnector]|


## <a name="az"></a>A ～ Z の完全な一覧

[コネクタの詳細情報](/connectors/)に関するページに、Swagger で定義されているトリガーとアクション、さらに各コネクタの制限が記載されています。

| | | | | | | | | | | | | |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| [**1**](#1) | [**A**](#a) | [**B**](#b) | [**C**](#c) | [**D**](#d) | [**E**](#e) | [**F**](#f) | [**G**](#g) | [**H**](#h) | [**I**](#i) | [**J**](#j) | [**L**](#l) | [**M**](#m) |
| [**N**](#n) | [**O**](#o) | [**P**](#p) | [**R**](#r) | [**S**](#s) | [**T**](#t) | [**U**](#u) | [**V**](#v) | [**W**](#w) | [**X**](#x) | [**Y**](#y) | [**Z**](#z) | | 

| | |
|---|---|
|<a name="1"></a>10to8 Appointment Scheduling<br/><br/><a name="a"></a>Act!<br/>Adobe Creative Cloud<br/>appFigures<br/>[AS2][as2doc]<br/>Asana<br/>Azure Active Directory (AD)<br/>Azure API Management<br/>Azure App Service<br/>Azure Application<br/>Azure Automation<br/>[Azure Blob Storage][azureblobstoragedoc]<br/>Azure Data Lake<br/>Azure DocumentDB (Cosmos DB)<br/>[Azure Functions][azure-functionsdoc]<br/>[Azure Logic Apps][nested-logic-appdoc]<br/>AzureML<br/>Azure キュー<br/>Azure リソース マネージャー<br/>[Azure SQL Database][sql-serverdoc]<br/><br/><a name="b"></a>Basecamp 2<br/>Basecamp 3<br/>Batch<br/>Benchmark Email<br/>Bing Search<br/>Bitbucket<br/>Bitly<br/>BizTalk Server<br/>Blogger<br/>Box<br/>Buffer<br/><br/><a name="c"></a>Calendly<br/>Campfire<br/>Capsule CRM<br/>Chatter<br/>Cognito Forms<br/>Cognitive Services Computer Vision API<br/>Cognitive Services Face API<br/>Cognitive Services LUIS<br/>Cognitive Services Text Analytics<br/>Common Data Service<br/>Content Conversion<br/>Control-Terminate<br/>[カスタム API/Web アプリ][api/web-appdoc]<br/><br/><a name="d"></a>データ操作<br/>[DB2][db2doc]<br/>Disqus<br/>DocuSign<br/>Do Until<br/>Dropbox<br/>[Dynamics 365 CRM Online][Dynamics-365doc]<br/>Dynamics 365 for Financials<br/>Dynamics 365 for Operations<br/>Dynamics NAV<br/><br/><a name="e"></a>Easy Redmine<br/>EDIFACT<br/>[Event Hubs][event-hubs-doc]<br/>Eventbrite<br/><br/><a name="f"></a>Facebook<br/>[ファイル システム][filesystemdoc]<br/>[フラット ファイル][flatfiledoc]<br/>FreshBooks<br/>Freshdesk<br/>FreshService<br/>[FTP][ftpdoc]<br/><br/><a name="g"></a>GitHub<br/>Gmail<br/>Google カレンダー<br/>Google コンタクト<br/>Google ドライブ<br/>Google スプレッドシート<br/>Google ToDo リスト<br/>GoToMeeting<br/>GoToTraining<br/>GoToWebinar<br/><br/><a name="h"></a>Harvest<br/>HelloSign<br/>HipChat<br/>[HTTP][httpdoc]<br/>[HTTP + Swagger][http-swaggerdoc]<br/>[HTTP Webhook][webhookdoc]<br/><br/><a name="i"></a>[Informix][informixdoc]<br/>Infusionsoft<br/>Inoreader<br/>Insightly<br/>Instagram<br/>Instapaper<br/>統合アカウント<br/>Intercom | <a name="j"></a>JotForm<br/>JIRA<br/><br/><a name="l"></a>LeanKit<br/>LiveChat<br/><br/><a name="m"></a>MailChimp<br/>Mandrill<br/>中<br/>Microsoft フォーム<br/>Microsoft Teams<br/>Microsoft Translator<br/>[MQ][mqdoc]<br/>MSN 天気<br/>Muhimbi PDF<br/>MySQL<br/><br/><a name="n"></a>Nexmo<br/><br/><a name="o"></a>[Office 365 Outlook][office365-outlookdoc]<br/>Office 365 ユーザー<br/>Office 365 Video<br/>OneDrive<br/>OneDrive for Business<br/>OneNote (Business)<br/>[Oracle Database][oracle-db-doc]<br/>Outlook Customer Manager<br/>Outlook タスク<br/>Outlook.com<br/><br/><a name="p"></a>PagerDuty<br/>Parserr<br/>Paylocity<br/>Pinterest<br/>Pipedrive<br/>Pivotal Tracker<br/>Planner<br/>PostgreSQL<br/>Power BI<br/>Project Online<br/><br/><a name="r"></a>Redmine<br/>[要求/応答][http-requestdoc]<br/>RSS<br/><br/><a name="s"></a>[Salesforce][salesforcedoc]<br/>[SAP アプリケーション サーバー][sapconnector]<br/>[SAP メッセージ サーバー][sapconnector]<br/>[スケジュール][recurrencedoc]<br/>Scope<br/>SendGrid<br/>メッセージをバッチに送信する<br/>[Service Bus][service-busdoc]<br/>SFTP<br/>[SharePoint Online][sharepointdoc]<br/>[SharePoint Server][sharepointserver]<br/>Slack<br/>Smartsheet<br/>SMTP<br/>SparkPost<br/>[SQL Server][sql-serverdoc]<br/>Stripe<br/>SurveyMonkey<br/>Switch Case<br/><br/><a name="t"></a>Teamwork Projects<br/>Teradata<br/>Todoist<br/>Toodledo<br/>[XML 変換][xmltransformdoc]<br/>Trello<br/>Twilio<br/>[Twitter][twitterdoc]<br/>Typeform<br/><br/><a name="u"></a>UserVoice<br/><br/><a name="v"></a>変数<br/>Vimeo<br/>Visual Studio Team Services<br/><br/><a name="w"></a>WebMerge<br/>WordPress<br/>Wunderlist<br/><br/><a name="x"></a>[X12][x12doc]<br/>[XML 検証][xmlvalidatedoc]<br/><br/><a name="y"></a>Yammer<br/>YouTube<br/><br/><a name="z"></a>Zendesk |

> [!TIP]
> Azure アカウントにサインアップせずに Azure Logic Apps を体験するには、[Logic Apps の試用](https://tryappservice.azure.com/?appservice=logic)に関するページをご覧ください。 短時間有効な簡易版のロジック アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## <a name="connectors-as-triggers-and-actions"></a>トリガーおよびアクションとしてのコネクタ

**トリガー**は、ロジック アプリのインスタンスを起動または実行します。 一部のコネクタには、特定のイベントが発生したときにアプリに通知するトリガーが用意されています。 たとえば、FTP コネクタには、ファイルが更新されたときにロジック アプリを起動する `OnUpdatedFile` トリガーがあります。 

ロジック アプリには、次の種類のトリガーが用意されています。  

* "*ポーリング トリガー*": このトリガーは、指定された頻度でサービスをポーリングして、新しいデータをチェックします。 

    新しいデータを利用できる場合、そのデータを入力値としてロジック アプリの新しいインスタンスが実行されます。 

* "*プッシュ トリガー*": このトリガーは、エンドポイント上のデータまたは発生するイベントをリッスンして、ロジック アプリの新しいインスタンスをトリガーします。

* *定期実行のトリガー*: このトリガーは、指定されたスケジュールに従ってロジック アプリのインスタンスを作成します。

コネクタには、ワークフローで使用できる**アクション**も用意されています。 たとえば、ロジック アプリでは、データを検索し、後でそのデータをロジック アプリで使用できます。 具体的には、SQL データベースから顧客データを検索し、その顧客データを使用して、ワークフローを構築できます。 

> [!TIP]
> トリガーとアクションの詳細については、[コネクタの概要](connectors-overview.md)に関する記事をご覧ください。 


## <a name="message-manipulation-actions"></a>メッセージ操作アクション

ロジック アプリには、ペイロード データを変更または操作できる組み込みのアクションが用意されています。 組み込みの**データ操作**コネクタには、次のアクションがあります。 

| | |
|---|---|
| **作成** | 後で使用するためや、ワークフローを構築する際に、値またはオブジェクトを作成または生成します。 たとえば、複数の手順からの値を使用して JSON オブジェクトを作成することや、後でロジック アプリの実行中に参照する定数を計算することができます。 |
| **CSV テーブルの作成**<br/>**HTML テーブルの作成** | 配列の結果セットを CSV テーブルまたは HTML テーブルに変換します。 たとえば、CRM の "レコードの一覧表示" アクションを追加し、今日追加されたレコードのフィルターを追加します。 その後、結果を HTML テーブルとして電子メールで送信します。 |
| **配列のフィルター処理** (クエリ) | 結果セットをフィルター処理して、関心のあるエントリを取り出します。 たとえば、`#Azure` を含むすべてのツイートを検索し、返されたツイートを "フィルター処理" して、`Tweeted_by_followers > 50` の結果のみを返します。 |
| **Join** | 何らかの区切り記号で配列を結合します。 たとえば、キー フレーズ検出操作では、キー フレーズの配列が返されます。 それらのキー フレーズを `,` などで "結合" できます。 そうすると、`["Some", "Phrase"]` ではなく、`"Some, Phrase"` になります。 |
| **JSON の解析** | デザイナーで JSON オブジェクトの値を解析およびアクセスします。 たとえば、Azure 関数で JSON ペイロードが返される場合、そのペイロードを解析して、後の別の手順で JSON プロパティにアクセスできます。 このアクションでは、実行時に JSON が指定したスキーマと一致することも検証します。 | 
| **選択肢** | 後続の処理のために配列の特定のプロパティを選択します。 SQL の "レコードの一覧表示" を行い、15 列が返された場合に、後続の処理のためにその一部の列を選択します。 出力は、選択したプロパティのみを含む配列です。 |

## <a name="custom-connectors-and-azure-certification"></a>カスタム コネクタと Azure の認定 

カスタム コードを実行する API や、コネクタとして使用できない API を呼び出すために、[カスタム コネクタとして REST ベースの API アプリを作成](../logic-apps/logic-apps-create-api-app.md)して、Logic Apps プラットフォームを拡張できます。 

カスタム API アプリを公開して Azure で使用できるようにする場合は、[Microsoft Azure Certified プログラム](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/)に推薦を送信します。

## <a name="get-help"></a>問い合わせ

[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)では、質問の投稿や質問への回答を行うことができるほか、他の Azure Logic Apps ユーザーがどのようなことを行っているかがわかります。

[Logic Apps ユーザー フィードバック サイト](http://aka.ms/logicapps-wish)でアイデアへの投票やアイデアの投稿を行って、Azure Logic Apps とコネクタの改善にご協力ください。

コネクタのトピックについて不足している情報や、重要と思われる情報がありましたらお知らせください。 既存のトピックに追加いたします。または、読者が独自に作成することもできます。 ドキュメントはオープン ソースであり、GitHub でホストされています。 まずは、[GitHub リポジトリ](https://github.com/Microsoft/azure-docs)にアクセスしてみましょう。 

## <a name="next-steps"></a>次のステップ
* [初めてのロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)
* [ロジック アプリ用のカスタム API を作成する](../logic-apps/logic-apps-create-api-app.md)
* [ロジック アプリを監視する](../logic-apps/logic-apps-monitor-your-logic-apps.md)

<!--Connectors Documentation-->

[api/web-appdoc]: ../logic-apps/logic-apps-custom-hosted-api.md "App Service API Apps を使用してロジック アプリを統合します。"
[azureblobstoragedoc]: ./connectors-create-api-azureblobstorage.md "Azure Blob Storage コネクタを使用して BLOB コンテナーのファイルを管理します。"
[azure-functionsdoc]: ../logic-apps/logic-apps-azure-functions.md "Azure Functions を使用してロジック アプリを統合します。"
[db2doc]: ./connectors-create-api-db2.md "クラウドまたはオンプレミスの IBM DB2 に接続します。行の更新、テーブルの取得などを行います。"
[Dynamics-365doc]: ./connectors-create-api-crmonline.md "CRM Online データを操作できるように Dynamics CRM Online に接続します。"
[event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Azure Event Hubs に接続します。ロジック アプリと Event Hubs の間でイベントを送受信します。"
[filesystemdoc]: ../logic-apps/logic-apps-using-file-connector.md "オンプレミスのファイル システムに接続します。"
[ftpdoc]: ./connectors-create-api-ftp.md "FTP/FTPS サーバーに接続して、ファイルのアップロード、取得、削除などの FTP タスクを実行できます。"
[httpdoc]: ./connectors-native-http.md "HTTP コネクタを使用して HTTP 呼び出しを実行します。"
[http-requestdoc]: ./connectors-native-reqres.md "HTTP 要求と HTTP 応答のアクションをロジック アプリに追加します。"
[http-swaggerdoc]: ./connectors-native-http-swagger.md "HTTP + Swagger コネクタを使用して HTTP 呼び出しを実行します。"
[informixdoc]: ./connectors-create-api-informix.md "クラウドまたはオンプレミスの Informix に接続します。行の読み取り、テーブルの一覧表示などを実行します。"
[nested-logic-appdoc]: ../logic-apps/logic-apps-http-endpoint.md "入れ子になったワークフローを使用してロジック アプリを統合します。"
[office365-outlookdoc]: ./connectors-create-api-office365-outlook.md "Office 365 アカウントに接続します。電子メールの送受信、予定表と連絡先の管理などを行います。"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Oracle データベースに接続して、行の追加、挿入、削除などを実行します。"
[mqdoc]: ./connectors-create-api-mq.md "MQ オンプレミスまたは Azure に接続し、メッセージを送受信します。"
[recurrencedoc]:  ./connectors-native-recurrence.md "ロジック アプリの定期的なアクションをトリガーします。"
[salesforcedoc]: ./connectors-create-api-salesforce.md "Salesforce アカウントに接続します。アカウント、潜在顧客、営業案件などを管理します。"
[sapconnector]: ../logic-apps/logic-apps-using-sap-connector.md "オンプレミスの SAP システムに接続します。"
[service-busdoc]: ./connectors-create-api-servicebus.md "Service Bus キューとトピックからメッセージを送信したり、Service Bus キューとサブスクリプションからメッセージを受信したりします。"
[sharepointdoc]: ./connectors-create-api-sharepointonline.md "SharePoint Online に接続します。ドキュメント、リスト アイテムなどを管理します。"
[sharepointserver]: ./connectors-create-api-sharepointserver.md "SharePoint オンプレミス サーバーに接続します。ドキュメント、リスト アイテムなどを管理します。"
[sql-serverdoc]: ./connectors-create-api-sqlazure.md "Azure SQL Database または SQL Server に接続します。SQL データベース テーブルのエントリを作成、更新、取得、削除できます。"
[twitterdoc]: ./connectors-create-api-twitter.md "Twitter に接続します。タイムラインの取得やツイートの投稿などを実行します。"
[webhookdoc]: ./connectors-native-webhook.md "webhook のアクションとトリガーをロジック アプリに追加します。"

[as2doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "エンタープライズ統合 AS2 について説明します。"
[x12doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "エンタープライズ統合 X12 について説明します。"
[flatfiledoc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "エンタープライズ統合フラット ファイルについて説明します。"
[flatfiledecodedoc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "エンタープライズ統合フラット ファイルについて説明します。"
[xmlvalidatedoc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "エンタープライズ統合 XML 検証について説明します。"
[xmltransformdoc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "エンタープライズ統合変換について説明します。"
[as2decode]: ../logic-apps/logic-apps-enterprise-integration-as2-decode.md "エンタープライズ統合 AS2 デコードについて説明します。"
[as2encode]:../logic-apps/logic-apps-enterprise-integration-as2-encode.md "エンタープライズ統合 AS2 エンコードについて説明します。"
[X12decode]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "エンタープライズ統合 X12 デコードについて説明します。"
[X12encode]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "エンタープライズ統合 X12 エンコードについて説明します。"
[EDIFACTdecode]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "エンタープライズ統合 EDIFACT デコードについて説明します。"
[EDIFACTencode]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "エンタープライズ統合 EDIFACT エンコードについて説明します。"
[integrationaccountdoc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "統合アカウントのスキーマ、マップ、パートナーなどを検索します。"


[boxDoc]: ./connectors-create-api-box.md "Box に接続します。ファイルのアップロード、取得、削除、一覧表示などを実行します。"
[delaydoc]: ./connectors-native-delay.md "遅延アクションを実行します。"
[dropboxdoc]: ./connectors-create-api-dropbox.md "Dropbox に接続します。ファイルのアップロード、取得、削除、一覧表示などを実行します。"
[facebookdoc]: ./connectors-create-api-facebook.md "Facebook に接続します。タイムラインへの投稿、ページ フィードの取得などを行います。"
[githubdoc]: ./connectors-create-api-github.md "GitHub に接続して、問題を追跡できます。"
[google-drivedoc]: ./connectors-create-api-googledrive.md "データを操作できるように GoogleDrive に接続します。"
[google-sheetsdoc]: ./connectors-create-api-googlesheet.md "シートを変更できるように Google スプレッドシートに接続します。"
[google-tasksdoc]: ./connectors-create-api-googletasks.md "タスクを管理できるように Google タスクに接続します。"
[google-calendardoc]: ./connectors-create-api-googlecalendar.md "Google カレンダーに接続してカレンダーを管理できます。"
[http-responsedoc]: ./connectors-native-reqres.md "HTTP 要求と HTTP 応答のアクションをロジック アプリに追加します。"
[instagramdoc]: ./connectors-create-api-instagram.md "Instagram に接続します。イベントをトリガーしたり、イベントに対するアクションを実行したりします。"
[mailchimpdoc]: ./connectors-create-api-mailchimp.md "MailChimp アカウントに接続します。メールの管理と自動化を行います。"
[mandrilldoc]: ./connectors-create-api-mandrill.md "通信のために Mandrill に接続します。"
[microsoft-translatordoc]: ./connectors-create-api-microsofttranslator.md "Microsoft Translator に接続します。テキストの翻訳、言語の検出などを行います。" 
[office365-usersdoc]: ./connectors-create-api-office365-users.md 
[office365-videodoc]: ./connectors-create-api-office365-video.md "Office 365 ビデオのビデオ情報、ビデオのリストとチャンネル、再生 URL を取得します。"
[onedrivedoc]: ./connectors-create-api-onedrive.md "個人用の Microsoft OneDrive に接続します。ファイルのアップロード、削除、一覧表示などを実行します。"
[onedrive-for-businessdoc]: ./connectors-create-api-onedriveforbusiness.md "ビジネス用の Microsoft OneDrive に接続します。ファイルのアップロード、削除、一覧表示などを実行します。"
[outlook.comdoc]: ./connectors-create-api-outlook.md "Outlook メールボックスに接続します。電子メール、予定表、連絡先の管理などを実行できます。"
[project-onlinedoc]: ./connectors-create-api-projectonline.md "Microsoft Project Online に接続します。プロジェクト、タスク、リソースなどを管理できます。"
[querydoc]: ./connectors-native-query.md "クエリ アクションで、配列の選択とフィルター処理を行います。"
[rssdoc]: ./connectors-create-api-rss.md "フィード アイテムを発行および取得したり、RSS フィードに新しいアイテムが発行されたときに操作をトリガーしたりします。"
[sendgriddoc]: ./connectors-create-api-sendgrid.md "SendGrid に接続します。電子メールの送信と受信者リストの管理を行います。"
[sftpdoc]: ./connectors-create-api-sftp.md "SFTP アカウントに接続します。ファイルのアップロード、取得、削除などを実行します。"
[slackdoc]: ./connectors-create-api-slack.md "Slack に接続し、Slack チャンネルにメッセージを投稿します。"
[smtpdoc]: ./connectors-create-api-smtp.md "SMTP サーバーに接続し、添付ファイルを含む電子メールを送信します。"
[sparkpostdoc]: ./connectors-create-api-sparkpost.md "通信のために SparkPost に接続します。"
[trellodoc]: ./connectors-create-api-trello.md "Trello に接続します。プロジェクトを管理し、どのようなものでも整理してだれとでも共有できます。"
[twiliodoc]: ./connectors-create-api-twilio.md "Twilio に接続します。メッセージの送信と取得、利用可能な番号の取得、着信電話番号の管理などを実行します。"
[wunderlistdoc]: ./connectors-create-api-wunderlist.md "Wunderlist に接続します。タスクと To Do リスクの管理、常に同期されたデジタル ライフの実現を行います。"
[yammerdoc]: ./connectors-create-api-yammer.md "Yammer に接続します。メッセージの投稿、新しいメッセージの取得などを実行します。"
[youtubedoc]: ./connectors-create-api-youtube.md "YouTube に接続します。ビデオとチャンネルを管理します。"


<!--Icon references-->
[appFiguresicon]: ./media/apis-list/appfigures.png
[Asanaicon]: ./media/apis-list/asana.png
[Azure-Automation-icon]: ./media/apis-list/azure-automation.png
[AzureBlobStorageicon]: ./media/apis-list/azureblob.png
[Azure-Data-Lake-icon]: ./media/apis-list/azure-data-lake.png
[Azure-DocumentDBicon]: ./media/apis-list/azure-documentdb.png
[Azure-MLicon]: ./media/apis-list/azureml.png
[Azure-Resource-Manager-icon]: ./media/apis-list/azure-resource-manager.png
[Azure-Queues-icon]: ./media/apis-list/azure-queues.png
[Basecamp-3icon]: ./media/apis-list/basecamp.png
[Bitbucket-icon]: ./media/apis-list/bitbucket.png
[Bitlyicon]: ./media/apis-list/bitly.png
[BizTalk-Servericon]: ./media/apis-list/biztalk.png
[Bloggericon]: ./media/apis-list/blogger.png
[Boxicon]: ./media/apis-list/box.png
[Campfireicon]: ./media/apis-list/campfire.png
[Cognitive-Services-Text-Analyticsicon]: ./media/apis-list/cognitiveservicestextanalytics.png
[DB2icon]: ./media/apis-list/db2.png
[Dropboxicon]: ./media/apis-list/dropbox.png
[Dynamics-365icon]: ./media/apis-list/dynamicscrmonline.png
[Dynamics-365-for-Financialsicon]: ./media/apis-list/madeira.png
[Dynamics-365-for-Operationsicon]: ./media/apis-list/dynamicsax.png
[Easy-Redmineicon]: ./media/apis-list/easyredmine.png
[Event-Hubs-icon]: ./media/apis-list/eventhubs.png
[Facebookicon]: ./media/apis-list/facebook.png
[FTPicon]: ./media/apis-list/ftp.png
[GitHubicon]: ./media/apis-list/github.png
[Google-Calendaricon]: ./media/apis-list/googlecalendar.png
[Google-Driveicon]: ./media/apis-list/googledrive.png
[Google-Sheetsicon]: ./media/apis-list/googlesheet.png
[Google-Tasksicon]: ./media/apis-list/googletasks.png
[HideKeyicon]: ./media/apis-list/hidekey.png
[HipChaticon]: ./media/apis-list/hipchat.png
[Informixicon]: ./media/apis-list/informix.png
[Insightlyicon]: ./media/apis-list/insightly.png
[Instagramicon]: ./media/apis-list/instagram.png
[Instapapericon]: ./media/apis-list/instapaper.png
[JIRAicon]: ./media/apis-list/jira.png
[MailChimpicon]: ./media/apis-list/mailchimp.png
[Mandrillicon]: ./media/apis-list/mandrill.png
[Microsoft-Translatoricon]: ./media/apis-list/microsofttranslator.png
[MQicon]: ./media/apis-list/mq.png
[Office-365-Outlookicon]: ./media/apis-list/office365.png
[Office-365-Usersicon]: ./media/apis-list/office365users.png
[Office-365-Videoicon]: ./media/apis-list/office365video.png
[OneDriveicon]: ./media/apis-list/onedrive.png
[OneDrive-for-Businessicon]: ./media/apis-list/onedriveforbusiness.png
[Oracle-DB-icon]: ./media/apis-list/oracle-db.png
[Outlook.comicon]: ./media/apis-list/outlook.png
[PagerDutyicon]: ./media/apis-list/pagerduty.png
[Pinteresticon]: ./media/apis-list/pinterest.png
[Project-Onlineicon]: ./media/apis-list/projectonline.png
[Redmineicon]: ./media/apis-list/redmine.png
[RSSicon]: ./media/apis-list/rss.png
[Common-Data-Serviceicon]: ./media/apis-list/runtimeservice.png
[Salesforceicon]: ./media/apis-list/salesforce.png
[SAPicon]: ./media/apis-list/sap.png
[SendGridicon]: ./media/apis-list/sendgrid.png
[Service-Busicon]: ./media/apis-list/servicebus.png
[SFTPicon]: ./media/apis-list/sftp.png
[SharePointicon]: ./media/apis-list/sharepointonline.png
[Slackicon]: ./media/apis-list/slack.png
[Smartsheeticon]: ./media/apis-list/smartsheet.png
[SMTPicon]: ./media/apis-list/smtp.png
[SparkPosticon]: ./media/apis-list/sparkpost.png
[SQL-Servericon]: ./media/apis-list/sql.png
[Todoisticon]: ./media/apis-list/todoist.png
[Trelloicon]: ./media/apis-list/trello.png
[Twilioicon]: ./media/apis-list/twilio.png
[Twittericon]: ./media/apis-list/twitter.png
[Vimeoicon]: ./media/apis-list/vimeo.png
[Visual-Studio-Team-Servicesicon]: ./media/apis-list/visualstudioteamservices.png
[WordPressicon]: ./media/apis-list/wordpress.png
[Wunderlisticon]: ./media/apis-list/wunderlist.png
[Yammericon]: ./media/apis-list/yammer.png
[YouTubeicon]: ./media/apis-list/youtube.png

<!-- Primitive Icons -->
[API/Web-Appicon]: ./media/apis-list/api.png
[Azure-Functionsicon]: ./media/apis-list/function.png
[Delayicon]: ./media/apis-list/delay.png
[FileSystemIcon]: ./media/apis-list/filesystem.png
[HTTPicon]: ./media/apis-list/http.png
[HTTP-Requesticon]: ./media/apis-list/request.png
[HTTP-Responseicon]: ./media/apis-list/response.png
[HTTP-Swaggericon]: ./media/apis-list/http_swagger.png
[Nested-Logic-Appicon]: ./media/apis-list/workflow.png
[Recurrenceicon]: ./media/apis-list/recurrence.png
[Queryicon]: ./media/apis-list/query.png
[Webhookicon]: ./media/apis-list/webhook.png

<!-- EIP Icons -->
[as2icon]: ./media/apis-list/as2.png
[x12icon]: ./media/apis-list/x12new.png
[flatfileicon]: ./media/apis-list/flatfileencoding.png
[flatfiledecodeicon]: ./media/apis-list/flatfiledecoding.png
[xmlvalidateicon]: ./media/apis-list/xmlvalidation.png
[xmltransformicon]: ./media/apis-list/xsltransform.png
[integrationaccounticon]: ./media/apis-list/integrationaccount.png
