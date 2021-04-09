---
title: Azure Logic Apps の Web API と REST API の作成
description: Azure Logic Apps でシステム統合のために API、サービス、またはシステムを呼び出す Web API と REST API を作成します
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/26/2017
ms.openlocfilehash: 3fe98160cc10eb3607b8309a9a263d63380dcfb5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89073218"
---
# <a name="create-custom-apis-you-can-call-from-azure-logic-apps"></a>Azure Logic Apps から呼び出しできるカスタム API の作成

Azure Logic Apps が提供する[数百個の組み込みコネクタ](../connectors/apis-list.md)をロジック アプリ ワークフローで利用できますが、コネクタとして利用できない API、システム、サービスを呼び出したい場合があります。 ロジック アプリで使用するアクションとトリガーを提供する独自の API を作成できます。 ロジック アプリのワークフローから呼び出すことができる独自の API は、次のような理由で作成する場合もあります。

* 現行システム統合やデータ統合のワークフローを拡張する。
* 仕事または個人的な作業の管理にサービスを利用する顧客を支援する。
* サービスが届く範囲を拡大する。サービスを見つけやすくする。サービスの用途を拡大する。

基本的に、コネクタはプラグ可能インターフェイスの REST、ドキュメント用の [Swagger メタデータ形式](https://swagger.io/specification/)、さらにデータ交換形式として JSON を利用する Web API です。 コネクタは HTTP エンドポイント経由で通信する REST API であるため、コネクタの構築には、.NET、Java、Python、Node.js など、任意の言語を利用できます。 [Azure App Service](../app-service/overview.md) で API をホストすることもできます。Azure App Service は、最も効果的で簡単、かつ拡張可能な方法で API ホスティングを提供する PaaS (サービスとしてのプラットフォーム) です。 

カスタム API をロジック アプリで利用するために、API はロジック アプリ ワークフローで特定のタスクを実行する [*アクション*](./logic-apps-overview.md#logic-app-concepts)を提供できます。 API は、新しいデータ、またはあるイベントが指定の条件を満たすときにロジック アプリ ワークフローを開始する [*トリガー*](./logic-apps-overview.md#logic-app-concepts)として機能させることもできます。 このトピックでは、API のアクションやトリガーを構築するときに採用できる、動作に基づく共通パターンについて説明します。

API は [Azure App Service](../app-service/overview.md) でホストできます。Azure App Service は、高い拡張性と容易な API ホスティングを提供するサービスとしてのプラットフォーム (PaaS) です。

> [!TIP] 
> API は Web アプリとしてデプロイできますが、API アプリとしてデプロイすることを検討してください。クラウドやオンプレミスで API を構築、ホスト、利用するとき、作業が簡単になります。 API のコードを変更する必要はありません。コードを API アプリにデプロイするだけです。 たとえば、次の言語で作成された API アプリをビルドする方法について確認してください。 
> 
> * [ASP.NET](../app-service/quickstart-dotnetcore.md)。 
> * [Java](../app-service/quickstart-java.md)
> * [Node.js](../app-service/quickstart-nodejs.md)
> * [PHP](../app-service/quickstart-php.md)
> * [Python](../app-service/quickstart-python.md)
> * [Ruby](../app-service/quickstart-ruby.md)
>
> ロジック アプリ用の API アプリ サンプルについては、[Azure Logic Apps GitHub リポジトリ](https://github.com/logicappsio)をご覧ください。

## <a name="how-do-custom-apis-differ-from-custom-connectors"></a>カスタム API とカスタム コネクタの違い

カスタム API と[カスタム コネクタ](../logic-apps/custom-connector-overview.md)は、プラグ可能インターフェイスの REST、ドキュメント用の [Swagger メタデータ形式](https://swagger.io/specification/)、さらにデータ交換形式として JSON を利用する Web API です。 また、これらの API とコネクタは HTTP エンドポイント経由で通信する REST API であるため、カスタム API とコネクタの構築には、.NET、Java、Python、Node.js など、任意の言語を利用できます。

カスタム API を使用すると、コネクタではない API を呼び出すことができ、HTTP + Swagger、Azure API Management、または App Services で呼び出せるエンドポイントを提供できます。 カスタム コネクタはカスタム API と同じように動作しますが、次の属性も備えています。

* Azure の Logic Apps コネクタ リソースとして登録されます。
* Logic Apps デザイナーで Microsoft が管理するコネクタに並んでアイコンと共に表示されます。
* ロジック アプリがデプロイされているリージョンで同じ Azure Active Directory テナントと Azure サブスクリプションを所有しているコネクタの作成者とロジック アプリ ユーザーのみが利用できます。

また、登録されたコネクタの Microsoft 認定を申請することもできます。 このプロセスは、登録されたコネクタが一般的な使用基準を満たしていることを確認し、Power Automate および Microsoft Power Apps のユーザーがそれらのコネクタを利用できるようにします。

カスタム コネクタの詳細については、次のトピックを参照してください 

* [カスタム コネクタの概要](../logic-apps/custom-connector-overview.md)
* [Web API からのカスタム コネクタの作成](/connectors/custom-connectors/create-web-api-connector)
* [Register custom connectors in Azure Logic Apps (Azure Logic Apps でのカスタム コネクタの登録)](/connectors/custom-connectors/)

## <a name="helpful-tools"></a>便利なツール

API の操作やパラメーターを説明する [Swagger ドキュメント](https://swagger.io/specification/)も API に含まれるとき、カスタム API はロジック アプリと最も効果的に連動します。
[Swashbuckle](https://github.com/domaindrivendev/Swashbuckle) のようなライブラリの多くは、Swagger ファイルを自動的に生成できます。 表示名やプロパティの種類などに関して Swagger ファイルに注釈を付けるために、[TRex](https://github.com/nihaue/TRex) を利用することもできます。Swagger ファイルとロジック アプリが効果的に連動します。

<a name="actions"></a>

## <a name="action-patterns"></a>アクション パターン

ロジック アプリがタスクを実行するには、カスタム API で [*アクション*](./logic-apps-overview.md#logic-app-concepts)を提供する必要があります。 API の各操作はアクションにマッピングされます。 基本アクションは、HTTP 要求を受け取り、HTTP 応答を返すコントローラーです。 そのため、たとえば、あるロジック アプリは HTTP 要求を Web アプリまたは API アプリに送信します。 次に、アプリはロジック アプリが処理できるコンテンツと共に HTTP 応答を返します。

標準的アクションの場合、API に HTTP 要求メソッドを記述し、そのメソッドについて Swagger ファイルで説明できます。 その後、[HTTP アクション](../connectors/connectors-native-http.md)または [HTTP + Swagger](../connectors/connectors-native-http-swagger.md) アクションで API を直接呼び出すことができます。 既定では、応答は[要求のタイムアウト期限](./logic-apps-limits-and-config.md)内に返す必要があります。 

![標準的アクション パターン](./media/logic-apps-create-api-app/standard-action.png)

<a name="pattern-overview"></a> API が時間のかかるタスクを完了するまでロジック アプリに待機させるには、API はこのトピックで説明する[非同期ポーリング パターン](#async-pattern)または[非同期 webhook パターン](#webhook-actions)に従う必要があります。 これらのパターンのさまざまな動作を視覚化する際、類推が役立ちます。ケーキ屋さんにケーキを特注する過程を想像してください。 ポーリング パターンの場合、20 分ごとにケーキ屋さんに電話し、ケーキができているか確認する動作を再現します。 webhook パターンの場合、ケーキができたときに電話できるようにケーキ屋さんがあなたの電話番号を尋ねる動作を再現します。

サンプルが必要であれば、[Logic Apps GitHub リポジトリ](https://github.com/logicappsio)にアクセスしてください。 アクションの使用状況測定については、[こちら](logic-apps-pricing.md)をご覧ください。

<a name="async-pattern"></a>

### <a name="perform-long-running-tasks-with-the-polling-action-pattern"></a>ポーリング アクション パターンで長時間タスクを実行する

[要求のタイムアウト期限](./logic-apps-limits-and-config.md)より長く実行される可能性があるタスクを API に実行させるには、非同期ポーリング パターンを利用できます。 このパターンでは、API は別個のスレッドで作業しますが、ロジック アプリ エンジンとの有効な接続を維持します。 この方法では、API が作業を完了する前にロジック アプリがタイムアウトしたり、ワークフローの次の段階に進んだりすることがありません。

一般的なパターン:

1. API が要求を受け取り、作業を開始したことをエンジンが認識していることを確認します。
2. エンジンがその後、ジョブの状態確認を要求したら、API がタスクを完了するタイミングをエンジンに知らせます。
3. ロジック アプリのワークフローが続行できるように、関連データをエンジンに返します。

<a name="bakery-polling-action"></a> ここで、前のパン屋さんの類推をポーリング パターンに適用します。パン屋さんに電話し、特注ケーキの配送を注文するところを想像してください。 ケーキ作りの過程には時間がかかりますが、ケーキ屋さんがケーキを作っている間、あなたは電話の前で待ちたくありません。 そこでケーキ屋さんが注文を確定したら、あなたは 20 分ごとに電話をかけることにします。ケーキの状態を確認するための電話です。 20 分後、あなたはケーキ屋さんに電話しますが、ケーキはまだできておらず、さらに 20 分後に電話して欲しいと言われます。 この往復プロセスは、あなたが電話し、ケーキ屋さんがあなたにケーキができたので届けると伝えるまで続きます。 

それでは、このポーリング パターンをマッピングしましょう。 ケーキ屋さんはカスタム API です。顧客であるあなたはロジック アプリ エンジンです。 エンジンが API を呼び出し、要求を伝えると、API は要求を確定し、エンジンがジョブの状態を確認できる時間間隔を応答として返します。 エンジンはジョブの状態確認を続け、API が応答でジョブが完了したと伝えたらデータをロジック アプリに返します。ロジック アプリはワークフローを続行します。 

![ポーリング アクション パターン](./media/logic-apps-create-api-app/custom-api-async-action-pattern.png)

API が従う手順を API の観点から説明すると次のようになります。

1. API は HTTP 要求を受け取って作業を開始すると、この手順の後半で説明する `location` ヘッダーを付けた HTTP `202 ACCEPTED` 応答をすぐに返します。 この応答により、API が要求を受け、要求ペイロード (データ入力) を受け取り、現在処理中であることをロジック アプリ エンジンは知ります。 
   
   `202 ACCEPTED` 応答には次のヘッダーを含める必要があります。
   
   * *必須*:ロジック アプリ エンジンが API のジョブの状態を確認できる URL の絶対パスを指定する `location` ヘッダー

   * *省略可能*:待機時間を秒単位で指定する `retry-after` ヘッダー。この時間が経過しないとエンジンは `location` URL にジョブの状態を確認できません。 

     既定では、エンジンは 20 秒ごとに確認します。 別の時間間隔を指定するには、`retry-after` ヘッダーと次のポーリングまでの秒数を追加します。

2. 指定した時間が経過したら、ロジック アプリ エンジンは `location` URL に問い合わせ、ジョブの状態を確認します。 API は次のような確認を実行し、次のような応答を返す必要があります。
   
   * ジョブが完了したら、HTTP `200 OK` 応答と応答ペイロード (次の手順の入力) を返します。

   * ジョブがまだ処理中の場合、別の HTTP `202 ACCEPTED` 応答を返します。ただし、ヘッダーは元の応答と同じです。

API がこのパターンに従うとき、ロジック アプリ ワークフロー定義で何もしなくてもジョブの状態確認が続行します。 エンジンは HTTP `202 ACCEPTED` 応答と有効な `location` ヘッダーを受け取ると、非同期パターンに従い、API が 202 以外の応答を返すまで `location` ヘッダーを確認します。

> [!TIP]
> 非同期パターンの例が必要であれば、[ここ](https://github.com/logicappsio/LogicAppsAsyncResponseSample)で GitHub の非同期コントローラー応答サンプルをご覧ください。

<a name="webhook-actions"></a>

### <a name="perform-long-running-tasks-with-the-webhook-action-pattern"></a>webhook アクション パターンで長時間タスクを実行する

代替として、長時間タスクや非同期処理に webhook パターンを使用できます。 このパターンでは、API からの "コールバック" があるまでロジック アプリは一時停止し、待機します。コールバックの後に処理を完了し、ワークフローを続行します。 このコールバックは、イベントの発生時にメッセージを URL に送信する HTTP POST です。 

<a name="bakery-webhook-action"></a> ここで、前のパン屋さんの類推を webhook パターンに適用します。パン屋さんに電話し、特注ケーキの配送を注文するところを想像してください。 ケーキ作りの過程には時間がかかりますが、ケーキ屋さんがケーキを作っている間、あなたは電話の前で待ちたくありません。 そこでケーキ屋さんがあなたの注文を確定するとき、あなたはケーキ屋さんに電話番号を伝えます。ケーキができたときに電話できるようにです。 ケーキ屋さんは注文の品ができたとあなたに伝え、品物を届けます。

この webhook パターンをマッピングするとき、ケーキ屋さんはカスタム API です。顧客であるあなたはロジック アプリ エンジンです。 エンジンは API を呼び出し、要求と "コールバック" URL を与えます。
ジョブが完了すると、API は URL を利用し、エンジンにジョブ完了を通知し、ロジック アプリにデータを返します。ロジック アプリはワークフローを続行します。 

このパターンでは、コントローラーに `subscribe` と `unsubscribe` という 2 つのエンドポイントを設定します。

*  `subscribe` エンドポイント:ワークフローで API のアクションまで実行が到達すると、ロジック アプリ エンジンは `subscribe` エンドポイントを呼び出します。 この手順では、ロジック アプリは API が保存するコールバック URL を作成し、作業の完了時に API からコールバックが届くまで待機します。 API は URL に HTTP POST でコールバックし、ロジック アプリの入力として返すコンテンツやヘッダーがあれば、それを渡します。

* `unsubscribe` エンドポイント:ロジック アプリの実行が取り消された場合、ロジック アプリ エンジンは `unsubscribe` エンドポイントを呼び出します。 API はコールバック URL を登録解除し、プロセスがあれば必要に応じて停止できます。

![webhook アクション パターン](./media/logic-apps-create-api-app/custom-api-webhook-action-pattern.png)

現在のところ、ロジック アプリ デザイナーでは、Swagger で webhook エンドポイントを検出できません。 そこでこのパターンの場合、[**webhook** アクション](../connectors/connectors-native-webhook.md)を追加し、要求の URL、ヘッダー、本文を指定する必要があります。 「[ワークフローのアクションとトリガー](logic-apps-workflow-actions-triggers.md#apiconnection-webhook-action)」も参照してください。 webhook パターンのサンプルについては、[ここ](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs)で GitHub の webhook トリガー サンプルを参照してください。

その他のヒントと注意事項のいくつかを次に示します。

* コールバック URL を渡すために、必要に応じて、前のいずれかのフィールドで `@listCallbackUrl()` ワークフロー機能を利用できます。

* ロジック アプリとサブスクライブしているサービスの両方を所有している場合、コールバック URL の呼び出し後に `unsubscribe` エンドポイントを呼び出す必要はありません。 それ以外の場合、Logic Apps ランタイムは `unsubscribe` エンドポイントを呼び出して、予定されている呼び出しがすべて終了したことを伝え、サーバー側でリソースをクリーンアップできるようにする必要があります。

<a name="triggers"></a>

## <a name="trigger-patterns"></a>トリガー パターン

カスタム API は、新しいデータ、またはあるイベントが指定の条件を満たすときにロジック アプリを開始する [*トリガー*](./logic-apps-overview.md#logic-app-concepts)として機能させることができます。 このトリガーはサービス エンドポイントの新しいデータまたはイベントを定期的に確認するか、待ち受けることができます。 新しいデータ、またはあるイベントが指定の条件を満たした場合、トリガーが発動し、トリガーを待ち受けていたロジック アプリが起動します。 この方法でロジック アプリを開始するには、API で [*ポーリング トリガー*](#polling-triggers) パターンまたは [*webhook トリガー*](#webhook-triggers) パターンを利用します。 これらのパターンは、[ポーリング アクション](#async-pattern)と [webhook アクション](#webhook-actions)のそれに似ています。 トリガーの使用状況測定については、[こちら](logic-apps-pricing.md)をご覧ください。

<a name="polling-triggers"></a>

### <a name="check-for-new-data-or-events-regularly-with-the-polling-trigger-pattern"></a>ポーリング トリガー パターンで新しいデータまたはイベントを定期的に確認する

*ポーリング トリガー* は、このトピックの前半で説明した [ポーリング アクション](#async-pattern)に似た動作をします。 ロジック アプリ エンジンはトリガー エンドポイントに定期的に問い合わせ、新しいデータまたはイベントがないか確認します。 指定の条件に一致する新しいデータまたはあるイベントをエンジンが見つけると、トリガーが発動します。 データを入力として処理するロジック アプリ インスタンスをエンジンが作成します。 

![ポーリング トリガー パターン](./media/logic-apps-create-api-app/custom-api-polling-trigger-pattern.png)

> [!NOTE]
> ロジック アプリ インスタンスが作成されなくても、ポーリング要求はアクション実行として数えられます。 同じデータが複数回処理されることを防止するには、すでに読まれ、ロジック アプリに渡されたデータをトリガーは消去する必要があります。

ポーリング トリガーの手順を API の観点から説明すると次のようになります。

| 新しいデータまたはイベントが見つかったか?  | API 応答 | 
| ------------------------- | ------------ |
| Found | 応答ペイロード (次の手順の入力) と共に HTTP `200 OK` 状態を返します。 <br/>この応答でロジック アプリ インスタンスが作成され、ワークフローが開始されます。 | 
| 見つかりません | `location` ヘッダーと `retry-after` ヘッダーと共に HTTP `202 ACCEPTED` 状態を返します。 <br/>トリガーの場合、`location` ヘッダーに `triggerState` クエリ パラメーターも含める必要があります。このクエリ パラメーターは通常、"timestamp" です。 API はこの識別子を利用し、ロジック アプリがトリガーがされた最後の時間を追跡記録できます。 | 
||| 

たとえば、新しいファイルがないか、サービスに定期的に確認するには、次の動作を含むポーリング トリガーを構築できます。

| 要求に `triggerState` が含まれていますか? | API 応答 | 
| -------------------------------- | -------------| 
| いいえ | HTTP `202 ACCEPTED` 状態と `location` ヘッダーを返します。ヘッダーの `triggerState` を現在の時刻に設定し、`retry-after` 間隔を 15 秒に設定します。 | 
| はい | `triggerState` の `DateTime` 後に追加されたファイルがないか、サービスに確認します。 | 
||| 

| 見つかったファイルの数 | API 応答 | 
| --------------------- | -------------| 
| 1 つのファイル | HTTP `200 OK` 状態とコンテンツ ペイロードを返し、返すファイルの `triggerState` を `DateTime` に更新し、`retry-after` 間隔を 15 秒に設定します。 | 
| 複数のファイル | 一度に 1 つのファイルと HTTP `200 OK` 状態を返し、`triggerState` を更新し、`retry-after` 間隔を 0 秒に設定します。 </br>これらの手順で、さらに多くのデータが利用できること、エンジンは `location` ヘッダーの URL からデータをすぐに要求しなければならないことがエンジンに知らされます。 | 
| ファイルなし | HTTP `202 ACCEPTED` 状態を返します。`triggerState` は変更しません。`retry-after` 間隔を 15 秒に設定します。 | 
||| 

> [!TIP]
> ポーリング トリガー パターンのサンプルについては、[ここ](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/PollTriggerController.cs)で GitHub のポーリング トリガー コントローラー サンプルを参照してください。

<a name="webhook-triggers"></a>

### <a name="wait-and-listen-for-new-data-or-events-with-the-webhook-trigger-pattern"></a>webhook トリガー パターンで新しいデータまたはイベントを待ち受ける

webhook トリガーは、サービス エンドポイントで新しいデータまたはイベントを待ち受ける *プッシュ トリガー* です。 新しいデータまたはあるイベントが指定の条件を満たす場合、トリガーが発動し、ロジック アプリ インスタンスが作成されます。このインスタンスはデータを入力として処理します。
webhook トリガーはこのトピックの前半で説明した [webhook アクション](#webhook-actions)に似た動作をします。`subscribe` エンドポイントと `unsubscribe` エンドポイントで設定されます。 

* `subscribe` エンドポイント:ロジック アプリに webhook トリガーを追加し、保存するとき、ロジック アプリ エンジンは `subscribe` エンドポイントを呼び出します。 この手順により、API が保存するコールバック URL をロジック アプリが作成します。 新しいデータまたはあるイベントが指定の条件を満たす場合、API は URL に HTTP POST でコールバックします。 コンテンツ ペイロードとヘッダーは入力としてロジック アプリに渡されます。

* `unsubscribe` エンドポイント:webhook が発動するか、ロジック アプリ全体が削除されると、ロジック アプリ エンジンは `unsubscribe` エンドポイントを呼び出します。 API はコールバック URL を登録解除し、プロセスがあれば必要に応じて停止できます。

![webhook トリガー パターン](./media/logic-apps-create-api-app/custom-api-webhook-trigger-pattern.png)

現在のところ、ロジック アプリ デザイナーでは、Swagger で webhook エンドポイントを検出できません。 そこでこのパターンの場合、[**webhook** トリガー](../connectors/connectors-native-webhook.md)を追加し、要求の URL、ヘッダー、本文を指定する必要があります。 「[HTTPWebhook トリガー](logic-apps-workflow-actions-triggers.md#httpwebhook-trigger)」もご覧ください。 webhook パターンのサンプルについては、[ここ](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs)で GitHub の webhook トリガー コントローラー サンプルを参照してください。

その他のヒントと注意事項のいくつかを次に示します。

* コールバック URL を渡すために、必要に応じて、前のいずれかのフィールドで `@listCallbackUrl()` ワークフロー機能を利用できます。

* 同じデータが複数回処理されることを防止するには、すでに読まれ、ロジック アプリに渡されたデータをトリガーは消去する必要があります。

* ロジック アプリとサブスクライブしているサービスの両方を所有している場合、コールバック URL の呼び出し後に `unsubscribe` エンドポイントを呼び出す必要はありません。 それ以外の場合、Logic Apps ランタイムは `unsubscribe` エンドポイントを呼び出して、予定されている呼び出しがすべて終了したことを伝え、サーバー側でリソースをクリーンアップできるようにする必要があります。

## <a name="improve-security-for-calls-to-your-apis-from-logic-apps"></a>ロジック アプリからの API の呼び出しのセキュリティを強化する

カスタム API を作成した後、ロジック アプリから安全に呼び出すことができるように API の認証を設定します。 [ロジック アプリからのカスタム API の呼び出しのセキュリティを強化する方法](../logic-apps/logic-apps-custom-api-authentication.md)について説明します。

## <a name="deploy-and-call-your-apis"></a>API をデプロイして呼び出す

認証を設定した後は、API のデプロイを設定します。 [ロジック アプリからカスタム API をデプロイして呼び出す方法](../logic-apps/logic-apps-custom-api-host-deploy-call.md)について確認してください。

## <a name="publish-custom-apis-to-azure"></a>カスタム API を Azure に公開する

カスタム API を Azure の他の Logic Apps ユーザーが使用できるようにするには、セキュリティを追加したうえで Logic Apps コネクタとして登録する必要があります。 詳細については、「[Custom connectors overview (カスタム コネクタの概要)](../logic-apps/custom-connector-overview.md)」を参照してください。 

Logic Apps、Power Automate、および Microsoft Power Apps のすべてのユーザーがカスタム API を使用できるようにするには、セキュリティを追加したうえで API を Logic Apps コネクタとして登録し、[Microsoft Azure 認定プログラム](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/)にコネクタを申請する必要があります。 

## <a name="get-support"></a>サポートを受ける

* カスタム API に関するサポートが必要な場合、[customapishelp@microsoft.com](mailto:customapishelp@microsoft.com) にお問い合わせください。

* ご質問がある場合は、[Azure Logic Apps に関する Microsoft Q&A 質問ページ](/answers/topics/azure-logic-apps.html)を参照してください。

* [Logic Apps ユーザー フィードバック サイト](https://aka.ms/logicapps-wish)でアイデアへの投票やアイデアの投稿を行って、Logic Apps の改善にご協力ください。 

## <a name="next-steps"></a>次のステップ

* [エラーと例外を処理する](../logic-apps/logic-apps-exception-handling.md)
* [HTTP エンドポイントでロジック アプリを呼び出し、トリガーし、入れ子にする](../logic-apps/logic-apps-http-endpoint.md)
* [アクションとトリガーの使用状況測定](../logic-apps/logic-apps-pricing.md)
