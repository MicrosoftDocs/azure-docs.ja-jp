---
title: Azure Logic Apps のコネクタ
description: Azure Logic Apps のコネクタ (組み込み、マネージド、オンプレミス、統合アカウント、およびエンタープライズ コネクタなど) についてワークフローを自動化する
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.date: 08/23/2018
ms.openlocfilehash: 59c8effb4c5feae99755b7937f4796e8f11fde46
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58895885"
---
# <a name="connectors-for-azure-logic-apps"></a>Azure Logic Apps のコネクタ

コネクタは、Azure Logic Apps からその他のアプリ、サービス、システム、プロトコル、およびプラットフォームでの、イベント、データ、およびアクションへのすばやいアクセスを提供します。 ロジック アプリでコネクタを使用して、クラウドおよびオンプレミス アプリの機能を拡張すると、タスクの実行時に、作成したデータや既に所有しているデータを使用できます。

Logic Apps が提供する[コネクタは 200 を超え](https://docs.microsoft.com/connectors)ますが、この記事では、人気が高く、より多く使用されているコネクタについて説明します。これらのコネクタは、データと情報を処理するために、何千ものアプリ、および何百万もの実行で正常に使用されているものです。 すべてのコネクタと、トリガー、アクション、制限など各コネクタの参照情報については、[コネクタの概要](https://docs.microsoft.com/connectors)に関するページにあるコネクタ参照ページをご覧ください。 [トリガーとアクション](#triggers-actions)の詳細も確認してください。

> [!NOTE]
> コネクタがないサービスや API を統合するには、HTTP などのプロトコル経由で直接サービスを呼び出すか、[カスタム コネクタ](#custom)を作成します。

コネクタは、組み込みトリガーおよびアクションとして、またはマネージド コネクタとして利用できます。

* [**組み込み**](#built-ins):これらの組み込みアクションおよびトリガーは、Azure Logic Apps に "ネイティブ" であり、カスタム スケジュールに基づいて実行するロジック アプリ、他のエンドポイントと通信するロジック アプリ、要求を受信して応答するロジック アプリ、さらに Azure 関数、Azure API アプリ (Web アプリ)、Azure API Management で管理および公開される独自の API、要求を受信できるネストされたロジック アプリを呼び出すロジック アプリを作成するのに役立ちます。 ロジック アプリのワークフローを編成および制御することができ、データの処理も行う組み込みアクションを使用することもできます。

* **マネージド コネクタ**: Microsoft によってデプロイされ、管理されるこれらのコネクタは、Office 365、Azure Blob Storage、SQL Server、Salesforce などの他のサービスやシステムにアクセスするためのトリガーとアクションを提供します。 コネクタによっては、Azure Logic Apps で管理される接続を最初に作成することが必要なものもあります。 マネージド コネクタは、次の 3 つのグループに編成されます。

  |   |   |
  |---|---|
  | [**マネージド API コネクタ**](#managed-api-connectors) | Azure Blob Storage、Office 365、Dynamics、Power BI、OneDrive、Salesforce、SharePoint Online などのサービスを使用するロジック アプリを作成します。 | 
  | [**オンプレミス コネクタ**](#on-premises-connectors) | [オンプレミス データ ゲートウェイ][gateway-doc]をインストールして設定した後、これらのコネクタを使用すると、ロジック アプリは、SQL Server、SharePoint Server、Oracle DB、ファイル共有などのオンプレミス システムにアクセスできます。 | 
  | [**統合アカウント コネクタ**](#integration-account-connectors) | 統合アカウントを作成して料金を支払うと使用可能になります。これらのコネクタは、XML を変換および検証したり、フラット ファイルをエンコードおよびデコードしたり、AS2、EDIFACT、および X12 プロトコルを使用して企業間 (B2B) メッセージを処理したりします。 | 
  | [**エンタープライズ コネクタ**](#enterprise-connectors) | SAP や IBM MQ などのエンタープライズ システムへのアクセスを提供します。追加のコストが必要です。 |
  ||| 

  たとえば、Microsoft BizTalk Server を使っている場合、ロジック アプリから [BizTalk Server コネクタ](#on-premises-connectors)を使用してご利用の BizTalk Server に接続し、通信を行うことができます。 
  [統合アカウント コネクタ](#integration-account-connectors)を使用すると、ロジック アプリで BizTalk のような操作を拡張または実行できます。 

> [!NOTE]
> すべてのコネクタと、アクション、トリガー (Swagger 記述により定義)、制限など各コネクタの参照情報は、[コネクタの概要](/connectors/)に関するページにある一覧をご覧ください。 料金については、「[Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps/)」と「[Logic Apps の料金モデル](../logic-apps/logic-apps-pricing.md)」を参照してください。 

<a name="built-ins"></a>

## <a name="built-ins"></a>組み込み

Logic Apps は、組み込みのトリガーとアクションを備えているため、スケジュールベースのワークフローを作成したり、ロジック アプリが他のアプリやサービスと通信することを可能にしたりできます。また、ロジック アプリでワークフローを制御したり、データを管理または操作したりすることもできます。 

|   |   |   |   | 
|---|---|---|---| 
| [![API アイコン][schedule-icon]<br/>**スケジュール**][recurrence-doc] | - **[繰り返し]** トリガーを使用して、ロジック アプリを指定のスケジュールで実行します (スケジュールには、基本的な繰り返しから複雑な繰り返しまで指定できます)。 <p>- **[遅延]** アクションを使用して、ロジック アプリを指定の期間、一時停止します。 <p>- **[延期期限]** アクションを使用して、ロジック アプリを指定の日時まで一時停止します。 | [![API アイコン][http-icon]<br/>**HTTP**][http-doc] | HTTP、HTTP + Swagger、および HTTP + Webhook 用のトリガーとアクションの両方を使用して、HTTP 経由で任意のエンドポイントと通信します。 | 
| [![API アイコン][http-request-icon]<br/>**要求**][http-request-doc] | - 他のアプリやサービスからロジック アプリを呼び出し可能にしたり、Event Grid リソース イベントでロジック アプリをトリガーしたり、**[要求]** トリガーによる Azure Security Center アラートへの応答でロジック アプリをトリガーしたりします。 <p>- **[応答]** アクションを使用して、アプリやサービスに応答を送信します。 | [![API アイコン][batch-icon]<br/>**バッチ**][batch-doc] | - **[メッセージのバッチ処理]** トリガーを使用して、メッセージをバッチで処理します。 <p>- **[バッチへのメッセージの送信]** アクションを使用して、既存のバッチ トリガーを持つロジック アプリを呼び出します。 | 
| [![API アイコン][azure-functions-icon]<br/>**Azure Functions**][azure-functions-doc] | カスタム コード スニペット (C# または Node.js) をロジック アプリから実行する Azure 関数を呼び出します。 | [![API アイコン][azure-api-management-icon]</br>**Azure API Management**][azure-api-management-doc] | Azure API Management で管理および発行する独自の API によって定義されたトリガーとアクションを呼び出します。 | 
| [![API アイコン][azure-app-services-icon]<br/>**Azure App Service**][azure-app-services-doc] | Azure App Service でホストされている Azure API アプリまたは Web アプリを呼び出します。 Swagger が含まれている場合、これらのアプリで定義されているトリガーとアクションは、他のファースト クラスのトリガーおよびアクションのように表示されます。 | [![API アイコン][azure-logic-apps-icon]<br/>**Azure<br/>Logic Apps**][nested-logic-app-doc] | [要求] トリガーで開始する他のロジック アプリを呼び出します。 | 
||||| 

### <a name="control-workflow"></a>ワークフローを制御する

ロジック アプリのワークフローでアクションを構成および制御するための組み込みアクションがあります。

|   |   |   |   | 
|---|---|---|---| 
| [![組み込みアイコン][condition-icon]<br/>**条件**][condition-doc] | 条件を評価し、条件が true と false のいずれであるかに基づいて、さまざまなアクションを実行します。 | [![組み込みアイコン][for-each-icon]</br>**[For each]\(各項目に対して実行\)**][for-each-doc] | 配列内のすべての項目に対して同じアクションを実行します。 | 
| [![組み込みアイコン][scope-icon]<br/>**[Scope]\(スコープ\)**][scope-doc] | アクションを*スコープ*にグループ化します。スコープ内のアクションが実行を完了すると、スコープ独自のステータスが取得されます。 | [![組み込みアイコン][switch-icon]</br>**切り替え**][switch-doc] | アクションを*ケース*にグループ化します。既定のケースを除き、ケースには、一意の値が割り当てられます。 割り当てられた値が式、オブジェクト、またはトークンの結果に一致するケースのみを実行します。 一致が存在しない場合は、既定のケースを実行します。 | 
| [![組み込みアイコン][terminate-icon]<br/>**終了**][terminate-doc] | アクティブに実行中のロジック アプリ ワークフローを停止します。 | [![組み込みアイコン][until-icon]<br/>**期限**][until-doc] | 指定された条件が true になるまで、または特定の状態が変化するまで、アクションを繰り返します。 | 
||||| 

### <a name="manage-or-manipulate-data"></a>データを管理または操作する

データ出力とその形式を操作するための組み込みアクションを次に示します。  

|   |   | 
|---|---| 
| [![組み込みアイコン][data-operations-icon]<br/>**データの操作**][data-operations-doc] | データの操作を実行します。 <p>- **作成**:さまざまな型の複数の入力から単一の出力を作成します。 <br>- **CSV テーブルの作成**:JSON オブジェクトの配列からコンマ区切り値 (CSV) テーブルを作成します。 <br>- **HTML テーブルの作成**:JSON オブジェクトの配列から HTML テーブルを作成します。 <br>- **配列のフィルター処理**:別の配列内にある、条件を満たす項目から配列を作成します。 <br>- **結合**:配列内のすべての項目から 1 つの文字列を作成し、それらの項目を指定の区切り記号で区切ります。 <br>- **JSON の解析**:JSON コンテンツ内のプロパティとその値からわかりやすいトークンを作成し、ユーザーがそれらのプロパティをワークフロー内で使用できるようにします。 <br>- **選択**:別の配列内の項目または値を変換し、それらの項目を指定のプロパティにマッピングすることにより、JSON オブジェクトで配列を作成します。 | 
| ![組み込みアイコン][date-time-icon]<br/>**日付/時刻** | タイムスタンプを使用して操作を実行します。 <p>- **時間への追加**:指定した数の単位をタイムスタンプに追加します。 <br>- **タイム ゾーンの変換**:タイムスタンプをソース タイム ゾーンからターゲット タイム ゾーンに変換します。 <br>- **現在の時刻**:現在のタイムスタンプを文字列として返します。 <br>- **未来の時間の取得**:現在のタイムスタンプに指定した時刻単位を加えて返します。 <br>- **過去の時間の取得**:現在のタイムスタンプから指定した時刻単位を引いて返します。 <br>- **時間からの減算**:タイムスタンプから時間単位数を減算します。 |
| [![組み込みアイコン][variables-icon]<br/>**変数**][variables-doc] | 変数の操作を実行します。 <p>- **配列変数に追加**:変数によって保管される配列内に、値を最後の項目として挿入します。 <br>- **文字列変数に追加**:変数によって保管される文字列内に、値を最後の文字として挿入します。 <br>- **変数の値を減らす**:変数の値を一定値だけ減らします。 <br>- **変数の値を増やす**:変数の値を一定値だけ増やします。 <br>- **変数を初期化する**:変数を作成し、そのデータ型と初期値を宣言します。 <br>- **変数の設定**:既存の変数に異なる値を代入します。 |
|  |  | 

<a name="managed-api-connectors"></a>

## <a name="managed-api-connectors"></a>マネージド API コネクタ

次のサービスやシステムでタスク、プロセス、およびワークフローを自動化するためによく使用されているコネクタを示します。

|   |   |   |   | 
|---|---|---|---| 
| [![API アイコン][azure-service-bus-icon]<br/>**Azure Service Bus**][azure-service-bus-doc] | Logic Apps で最もよく使用されているコネクタを使用して非同期メッセージ、セッション、およびトピック サブスクリプションを管理します。 | [![API アイコン][sql-server-icon]<br/>**SQL Server**][sql-server-doc] | オンプレミスの SQL Server、またはクラウド内の Azure SQL Database に接続し、レコードの管理、ストアド プロシージャの実行、クエリの実行を行えるようにします。 | 
| [![API アイコン][office-365-outlook-icon]<br/>**Office 365<br/>Outlook**][office-365-outlook-doc] | Office 365 メール アカウントに接続し、メール、タスク、カレンダー イベント、会議、連絡先、要求などを作成したり、管理したりできるようにします。 | [![API アイコン][azure-blob-storage-icon]<br/>**Azure Blob<br/>Storage**][azure-blob-storage-doc] | ストレージ アカウントに接続し、BLOB コンテンツを作成したり、管理したりできるようにします。 | 
| [![API アイコン][sftp-icon]<br/>**SFTP**][sftp-doc] | インターネットからアクセス可能な SFTP サーバーに接続し、ファイルやフォルダーを操作できるようにします。 | [![API アイコン][sharepoint-online-icon]<br/>**SharePoint<br/>Online**][sharepoint-online-doc] | SharePoint Online に接続し、ファイル、添付ファイル、フォルダーなどを管理できるようにします。 | 
| [![API アイコン][dynamics-365-icon]<br/>**Dynamics 365<br/>CRM Online**][dynamics-365-doc] | Dynamics 365 アカウントに接続し、レコードや項目などを作成したり、管理したりできるようにします。 | [![API アイコン][ftp-icon]<br/>**FTP**][ftp-doc] | インターネットからアクセス可能な FTP サーバーに接続し、ファイルやフォルダーを操作できるようにします。 | 
| [![API アイコン][salesforce-icon]<br/>**Salesforce**][salesforce-doc] | Salesforce アカウントに接続し、レコード、ジョブ、オブジェクトなどの項目を作成したり、管理したりできるようにします。 | [![API アイコン][twitter-icon]<br/>**Twitter**][twitter-doc] | Twitter アカウントに接続し、ツイート、フォロワー、タイムラインなどを管理できるようにします。 ツイートは、SQL、Excel、または SharePoint に保存します。 | 
| [![API アイコン][azure-event-hubs-icon]<br/>**Azure Event Hubs**][azure-event-hubs-doc] | イベント ハブを通じてイベントを使用したり、発行したりします。 たとえば、ロジック アプリから Event Hubs を使用して出力を取得し、リアルタイム分析プロバイダーに送信できます。 | [![API アイコン][azure-event-grid-icon]<br/>**Azure Event**</br>**Grid**][azure-event-grid-doc] | Azure やサードパーティのリソースが変更されたときなどに、Event Grid が発行するイベントを監視します。 | 
||||| 

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>オンプレミス コネクタ 

ここでは、よく使用されるコネクタのうち、オンプレミス システムのデータやリソースにアクセスできるものを紹介します。 オンプレミス システムへの接続を作成するには、最初に[オンプレミス データ ゲートウェイのダウンロード、インストール、設定][gateway-doc]を行う必要があります。 このゲートウェイは、セキュリティで保護された通信チャネルを提供します。必要なネットワーク インフラストラクチャを設定する必要はありません。 

|   |   |   |   |   | 
|---|---|---|---|---| 
| ![API アイコン][biztalk-server-icon]<br/>**BizTalk**</br> **サーバー** | [![API アイコン][file-system-icon]<br/>**ファイル</br> システム**][file-system-doc] | [![API アイコン][ibm-db2-icon]<br/>**IBM DB2**][ibm-db2-doc] | [![API アイコン][ibm-informix-icon]<br/>**IBM**</br> **Informix**][ibm-informix-doc] | ![API アイコン][mysql-icon]<br/>**MySQL** | 
| [![API アイコン][oracle-db-icon]<br/>**Oracle DB**][oracle-db-doc] | ![API アイコン][postgre-sql-icon]<br/>**PostgreSQL** | [![API アイコン][sharepoint-server-icon]<br/>**SharePoint</br> Server**][sharepoint-server-doc] | [![API アイコン][sql-server-icon]<br/>**SQL</br> Server**][sql-server-doc] | ![API アイコン][teradata-icon]<br/>**Teradata** | 
||||| 

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>統合アカウント コネクタ 

ロジック アプリを使用して企業間 (B2B) ソリューションを作成するためのコネクタを次に示します。この場合、Azure の Enterprise Integration Pack (EIP) で利用可能な[統合アカウント](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)を作成して、その料金を支払う必要があります。 このアカウントを使用すると、パーティー、取引先契約、マップ、スキーマ、証明書などの B2B 成果物を作成したり、保管したりできます。 これらの成果物を使用するには、ロジック アプリを統合アカウントに関連付けます。 現在、BizTalk Server を使用している場合は、これらのコネクタが既になじみがあるように見えるかもしれません。

|   |   |   |   | 
|---|---|---|---| 
| [![API アイコン][as2-icon]<br/>**[AS2</br> decoding]\(AS2 のデコード\)**][as2-decode-doc] | [![API アイコン][as2-icon]<br/>**[AS2</br> encoding]\(AS2 のエンコード\)**][as2-encode-doc] | [![API アイコン][edifact-icon]<br/>**[EDIFACT</br> decoding]\(EDIFACT のデコード\)**][edifact-decode-doc] | [![API アイコン][edifact-icon]<br/>**[EDIFACT</br> encoding]\(EDIFACT のエンコード\)**][edifact-encode-doc] | 
| [![API icon][flat-file-decode-icon]<br/>**フラット ファイルの</br>デコード**][flat-file-decode-doc] | [![API アイコン][flat-file-encode-icon]<br/>**フラット ファイルの</br> エンコード**][flat-file-encode-doc] | [![API アイコン][integration-account-icon]<br/>**統合<br/>アカウント**][integration-account-doc] | [![API アイコン][liquid-icon]<br/>**[Liquid**</br>**transforms]\(Liquid 変換\)**][json-liquid-transform-doc] | 
| [![API アイコン][x12-icon]<br/>**[X12</br> decoding]\(X12 のデコード\)**][x12-decode-doc] | [![API アイコン][x12-icon]<br/>**[X12</br> encoding]\(X12 のエンコード\)**][x12-encode-doc] | [![API アイコン][xml-transform-icon]<br/>**[XML**</br>**transforms]\(XML 変換\)**][xml-transform-doc] | [![API アイコン][xml-validate-icon]<br/>**[XML <br/>validation]\(XML 検証\)**][xml-validate-doc] |  
||||| 

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>エンタープライズ コネクタ

ロジック アプリは、SAP や IBM MQ などのエンタープライズ システムにアクセスできます。

|   |   | 
|---|---| 
| [![API アイコン][ibm-mq-icon]<br/>**IBM MQ**][ibm-mq-doc] | [![API アイコン][sap-icon]<br/>**SAP**][sap-connector-doc] |
||| 

<a name="triggers-actions"></a>

## <a name="triggers-and-actions---more-info"></a>トリガーとアクション - 詳細情報

コネクタは、*トリガー*、*アクション*、またはその両方を提供できます。 *トリガー*はすべてのロジック アプリの最初の手順であり、通常トリガーを起動し、ロジック アプリの実行を開始するイベントを指定します。 たとえば、FTP コネクタには、"ファイルが追加されるか、変更されたとき" にロジック アプリを起動するトリガーがあります。 一部のトリガーは、指定されたイベントまたはデータを定期的にチェックし、指定されたイベントやデータを検出したときに起動します。 他のトリガーは、待機し、特定のイベントが発生した場合や新しいデータを使用できるようになった場合にただちに起動します。 トリガーは、ロジック アプリに必要なデータも渡します。 ロジック アプリでは、ワークフロー全体でそのデータを読み取り、使用することができます。
たとえば、Twitter コネクタには、ロジック アプリのワークフローにツイートの内容を渡す "新しいツイートが投稿されたら" トリガーがあります。 

トリガーの起動後、Azure Logic Apps は、ロジック アプリのインスタンスを作成し、ロジック アプリのワークフローで*アクション*の実行を開始します。 アクションは、ロジック アプリのワークフローでトリガーを追跡し、タスクを実行する手順です。 たとえば、SQL データベースから顧客データを取得し、後のアクションでそのデータを処理するロジック アプリを作成できます。 

次に、Azure Logic Apps が提供する一般的な種類のトリガーを示します。

* *繰り返しトリガー*:このトリガーは、指定したスケジュールで実行され、特定のサービスまたはシステムに緊密に関連付けられません。

* *ポーリング トリガー*:このトリガーは、指定したスケジュールに基づいて、特定のサービスまたはシステムを定期的にポーリングし、新しいデータまたは特定のイベントが発生したかどうかをチェックします。 新しいデータが使用可能になるか、または特定のイベントが発生した場合、トリガーはロジック アプリの新しいインスタンスを実行し、それにより、入力として渡されたデータを使用できるようになります。

* *プッシュ トリガー*:このトリガーは、新しいデータまたはイベントの発生を待機し、リッスンします。 新しいデータが使用可能になるか、またはイベントが発生した場合、トリガーはロジック アプリの新しいインスタンスを作成して実行し、それにより、入力として渡されたデータを使用できるようになります。

<a name="custom"></a>

## <a name="connector-configuration"></a>コネクタの構成

各コネクタのトリガーとアクションは、ユーザーが構成できる独自のプロパティを提供します。 多くのコネクタでは、ロジック アプリでトリガーやアクションを使用する前に、最初に対象のサービスまたはシステムへの*接続*を作成し、認証資格情報またはその他の構成の詳細を指定する必要があります。 たとえば、ユーザーに代わってデータにアクセスしたり、投稿したりするために Twitter アカウントへの接続を承認する必要があります。 

OAuth を使用するコネクタの場合、接続の作成は、Office 365、Salesforce、GitHub などのサービスへのサインインを意味し、そこでアクセス トークンが暗号化され、Azure シークレット ストアに安全に保存されます。 FTP や SQL などのその他のコネクタでは、サーバーのアドレス、ユーザー名、およびパスワードなどの構成の詳細を含む接続が必要です。 この接続構成の詳細も暗号化され、安全に保存されます。 

接続は、サービスまたはシステムが許可する限り、ターゲットのサービスまたはシステムにアクセスすることができます。 Office 365 や Dynamics などの Azure Active Directory (AD) の OAuth 接続を使用するサービスについては、Azure Logic Apps がアクセス トークンを無制限に更新します。 その他のサービスでは、Azure Logic Apps が更新せずにトークンを使用できる期間に制限が加えられる場合があります。 一般に、一部のアクションでは、パスワードの変更などのすべてのアクセス トークンが無効になります。

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>カスタム API とコネクタ

カスタム コードを実行する API や、コネクタとして使用できない API を呼び出すには、[カスタム API Apps を作成](../logic-apps/logic-apps-create-api-app.md)して、Logic Apps プラットフォームを拡張します。 "*任意の*" REST または SOAP ベース API 用の[カスタム コネクタを作成](../logic-apps/custom-connector-overview.md)することもできます。その場合、これらの API は、Azure サブスクリプションの任意のロジック アプリで使用できるようになります。
Azure 内でカスタム API Apps またはコネクタを公開し、誰でも使用できるようにするには、[Microsoft の認定を受けるためにコネクタを提出](../logic-apps/custom-connector-submit-certification.md)してください。

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。

* Azure Logic Apps とコネクタに関するアイデアの投稿や投票を行うには、[Logic Apps ユーザー フィードバック サイト](https://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次の手順

* [コネクタの一覧](https://docs.microsoft.com/connectors)を確認する
* [初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [ロジック アプリのカスタム コネクタを作成する](https://docs.microsoft.com/connectors/custom-connectors/)
* [ロジック アプリ用のカスタム API を作成する](../logic-apps/logic-apps-create-api-app.md)

<!--Misc doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "オンプレミス データ ゲートウェイを使用して、ロジック アプリからオンプレミスのデータに接続する"

<!--Built-ins doc links-->
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Azure Functions を使用してロジック アプリを統合します。"
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "API を管理および発行するための Azure API Management サービス インスタンスを作成します。"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "App Service API Apps を使用してロジック アプリを統合します。"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Service Bus キューとトピックからメッセージを送信したり、Service Bus キューとサブスクリプションからメッセージを受信したりします。"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "グループ、またはバッチとしてメッセージを処理します。"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "条件を評価し、条件が true と false のいずれであるかに基づいて、さまざまなアクションを実行します。"
[delay-doc]: ./connectors-native-delay.md "遅延アクションを実行します。"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "配列内のすべての項目に対して同じアクションを実行します。"
[http-doc]: ./connectors-native-http.md "HTTP コネクタを使用して HTTP 呼び出しを実行します。"
[http-request-doc]: ./connectors-native-reqres.md "HTTP 要求と HTTP 応答のアクションをロジック アプリに追加します。"
[http-response-doc]: ./connectors-native-reqres.md "HTTP 要求と HTTP 応答のアクションをロジック アプリに追加します。"
[http-swagger-doc]: ./connectors-native-http-swagger.md "HTTP + Swagger コネクタを使用して HTTP 呼び出しを実行します。"
[http-webook-doc]: ./connectors-native-webhook.md "HTTP webhook のアクションとトリガーをロジック アプリに追加します。"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "入れ子になったワークフローを使用してロジック アプリを統合します。"
[query-doc]: ./connectors-native-query.md "クエリ アクションで、配列の選択とフィルター処理を行います。"
[recurrence-doc]:  ./connectors-native-recurrence.md "ロジック アプリの定期的なアクションをトリガーします。"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "アクションをグループに編成します。グループ内のアクションの実行が完了すると、グループ独自のステータスが取得されます。" 
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "アクションをケースに編成します。ケースには、一意の値が割り当てられます。値が式、オブジェクト、またはトークンの結果に一致するケースのみを実行します。一致が存在しない場合は、既定のケースを実行します。"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "アクティブに実行中のロジック アプリのワークフローを停止またはキャンセルします。"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "指定された条件が true になるまで、または特定の状態が変化するまで、アクションを繰り返します。"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "配列のフィルター処理や CSV と HTML のテーブルの作成などのデータ操作を実行します。"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "変数に対する操作を実行します。たとえば、文字列または配列の変数に対する初期化、設定、増分、減分、追加などです。"

<!--Managed API doc links-->
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Azure Blob Storage コネクタを使用して BLOB コンテナーのファイルを管理します。"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Azure やサードパーティのリソースが変更されたときなどに、Event Grid が発行するイベントを監視します。"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Azure Event Hubs に接続します。ロジック アプリと Event Hubs の間でイベントを送受信します。"
[box-doc]: ./connectors-create-api-box.md "Box に接続します。ファイルのアップロード、取得、削除、一覧表示などを実行します。"
[dropbox-doc]: ./connectors-create-api-dropbox.md "Dropbox に接続します。ファイルのアップロード、取得、削除、一覧表示などを実行します。"
[dynamics-365-doc]: ./connectors-create-api-crmonline.md "CRM Online データを操作できるように Dynamics CRM Online に接続します。"
[facebook-doc]: ./connectors-create-api-facebook.md "Facebook に接続します。タイムラインへの投稿、ページ フィードの取得などを行います。"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "オンプレミスのファイル システムに接続します。"
[ftp-doc]: ./connectors-create-api-ftp.md "FTP/FTPS サーバーに接続して、ファイルのアップロード、取得、削除などの FTP タスクを実行できます。"
[github-doc]: ./connectors-create-api-github.md "GitHub に接続して、問題を追跡できます。"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Google カレンダーに接続してカレンダーを管理できます。"
[google-drive-doc]: ./connectors-create-api-googledrive.md "データを操作できるように GoogleDrive に接続します。"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "シートを変更できるように Google スプレッドシートに接続します。"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "タスクを管理できるように Google タスクに接続します。"
[ibm-db2-doc]: ./connectors-create-api-db2.md "クラウドまたはオンプレミスの IBM DB2 に接続します。行の更新、テーブルの取得などを行います。"
[ibm-informix-doc]: ./connectors-create-api-informix.md "クラウドまたはオンプレミスの Informix に接続します。行の読み取り、テーブルの一覧表示などを実行します。"
[ibm-mq-doc]: ./connectors-create-api-mq.md "オンプレミスまたは Azure 内の IBM MQ に接続し、メッセージを送受信します。"
[instagram-doc]: ./connectors-create-api-instagram.md "Instagram に接続します。イベントをトリガーしたり、イベントに対するアクションを実行したりします。"
[mailchimp-doc]: ./connectors-create-api-mailchimp.md "MailChimp アカウントに接続します。メールの管理と自動化を行います。"
[mandrill-doc]: ./connectors-create-api-mandrill.md "通信のために Mandrill に接続します。"
[microsoft-translator-doc]: ./connectors-create-api-microsofttranslator.md "Microsoft Translator に接続します。テキストの翻訳、言語の検出などを行います。" 
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Office 365 アカウントに接続します。電子メールの送受信、予定表と連絡先の管理などを行います。"
[office-365-users-doc]: ./connectors-create-api-office365-users.md 
[office-365-video-doc]: ./connectors-create-api-office365-video.md "Office 365 ビデオのビデオ情報、ビデオのリストとチャンネル、再生 URL を取得します。"
[onedrive-doc]: ./connectors-create-api-onedrive.md "個人用の Microsoft OneDrive に接続します。ファイルのアップロード、削除、一覧表示などを実行します。"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "ビジネス用の Microsoft OneDrive に接続します。ファイルのアップロード、削除、一覧表示などを実行します。"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Oracle データベースに接続して、行の追加、挿入、削除などを実行します。"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Outlook メールボックスに接続します。電子メール、予定表、連絡先の管理などを実行できます。"
[project-online-doc]: ./connectors-create-api-projectonline.md "Microsoft Project Online に接続します。プロジェクト、タスク、リソースなどを管理できます。"
[rss-doc]: ./connectors-create-api-rss.md "フィード アイテムを発行および取得したり、RSS フィードに新しいアイテムが発行されたときに操作をトリガーしたりします。"
[salesforce-doc]: ./connectors-create-api-salesforce.md "Salesforce アカウントに接続します。アカウント、潜在顧客、営業案件などを管理します。"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "オンプレミスの SAP システムに接続します。"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "SendGrid に接続します。メールの送信と受信者リストの管理を行います。"
[sftp-doc]: ./connectors-create-api-sftp.md "SFTP アカウントに接続します。ファイルのアップロード、取得、削除などを実行します。"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "SharePoint オンプレミス サーバーに接続します。ドキュメント、リスト アイテムなどを管理します。"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "SharePoint Online に接続します。ドキュメント、リスト アイテムなどを管理します。"
[slack-doc]: ./connectors-create-api-slack.md "Slack に接続し、Slack チャンネルにメッセージを投稿します。"
[smtp-doc]: ./connectors-create-api-smtp.md "SMTP サーバーに接続し、添付ファイルを含むメールを送信します。"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "通信のために SparkPost に接続します。"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Azure SQL Database または SQL Server に接続します。SQL データベース テーブルのエントリを作成、更新、取得、削除できます。"
[trello-doc]: ./connectors-create-api-trello.md "Trello に接続します。プロジェクトを管理し、どのようなものでも整理してだれとでも共有できます。"
[twilio-doc]: ./connectors-create-api-twilio.md "Twilio に接続します。メッセージの送信と取得、利用可能な番号の取得、着信電話番号の管理などを実行します。"
[twitter-doc]: ./connectors-create-api-twitter.md "Twitter に接続します。タイムラインの取得やツイートの投稿などを実行します。"
[wunderlist-doc]: ./connectors-create-api-wunderlist.md "Wunderlist に接続します。タスクと To Do リスクの管理、常に同期されたデジタル ライフの実現を行います。"
[yammer-doc]: ./connectors-create-api-yammer.md "Yammer に接続します。メッセージの投稿、新しいメッセージの取得などを実行します。"
[youtube-doc]: ./connectors-create-api-youtube.md "YouTube に接続します。ビデオとチャンネルを管理します。"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "エンタープライズ統合 AS2 について説明します。"
[as2-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-as2-decode.md "エンタープライズ統合 AS2 デコードについて説明します。"
[as2-encode-doc]:../logic-apps/logic-apps-enterprise-integration-as2-encode.md "エンタープライズ統合 AS2 エンコードについて説明します。"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "エンタープライズ統合 EDIFACT デコードについて説明します。"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "エンタープライズ統合 EDIFACT エンコードについて説明します。"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "エンタープライズ統合フラット ファイルについて説明します。"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "エンタープライズ統合フラット ファイルについて説明します。"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "統合アカウントのスキーマ、マップ、パートナーなどを検索します。"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Liquid を使用した JSON 変換について説明します"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "エンタープライズ統合 X12 について説明します。"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "エンタープライズ統合 X12 デコードについて説明します。"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "エンタープライズ統合 X12 エンコードについて説明します。"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "エンタープライズ統合変換について説明します。"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "エンタープライズ統合 XML 検証について説明します。"

<!-- Built-ins icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Managed API icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[box-icon]: ./media/apis-list/box.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dropbox-icon]: ./media/apis-list/dropbox.png
[dynamics-365-icon]: ./media/apis-list/dynamics-crm-online.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[facebook-icon]: ./media/apis-list/facebook.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mailchimp-icon]: ./media/apis-list/mailchimp.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[microsoft-translator-icon]: ./media/apis-list/microsoft-translator.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[office-365-users-icon]: ./media/apis-list/office-365-users.png
[office-365-video-icon]: ./media/apis-list/office-365-video.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[rss-icon]: ./media/apis-list/rss.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[trello-icon]: ./media/apis-list/trello.png
[twilio-icon]: ./media/apis-list/twilio.png
[twitter-icon]: ./media/apis-list/twitter.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[visual-studio-team-services-icon]: ./media/apis-list/visual-studio-team-services.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[wunderlist-icon]: ./media/apis-list/wunderlist.png
[yammer-icon]: ./media/apis-list/yammer.png
[youtube-icon]: ./media/apis-list/youtube.png

<!-- Enterprise Integration Pack icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png
