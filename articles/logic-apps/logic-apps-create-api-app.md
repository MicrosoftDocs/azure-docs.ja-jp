---
title: "Logic Apps 向けの API の作成"
description: "Logic Apps で使用するカスタム API の作成"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bd229179-7199-4aab-bae0-1baf072c7659
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: 4c6bf3e46d80871c8f6ef1cd0a9959e4766a69f5


---
# <a name="creating-a-custom-api-to-use-with-logic-apps"></a>Logic Apps で使用するカスタム API の作成
Logic Apps プラットフォームを拡張する際は、多数ある標準搭載コネクタには含まれていない API やシステムをさまざまな方法で呼び出すことができます。  そのような方法の&1; つに、ロジック アプリ ワークフロー内から呼び出せる API アプリの作成があります。

## <a name="helpful-tools"></a>便利なツール
作成した API が Logic Apps とうまく連携するように、API でサポートされる操作とパラメーターを記述した [swagger](http://swagger.io) ドキュメントを作成することをお勧めします。  swagger を自動的に生成するライブラリが ([Swashbuckle](https://github.com/domaindrivendev/Swashbuckle) など) 豊富に用意されています。  また、[TRex](https://github.com/nihaue/TRex) を使用して、Logic Apps でうまく機能するように swagger に注釈 (表示名、プロパティの型など) を付けることもできます。  Logic Apps 向けに作成された API Apps のサンプルが [GitHub リポジトリ](http://github.com/logicappsio)と[ブログ](http://aka.ms/logicappsblog)で公開されていますので、ぜひご覧ください。

## <a name="actions"></a>アクション
ロジック アプリの基本的なアクションは、HTTP 要求を受け取って応答 (通常は 200) を返すコントローラーですが、  さまざまなパターンを使用して、ニーズに合わせてアクションを拡張できます。

ロジック アプリのエンジンでは、要求は既定で 1 分後にタイムアウトになります。  ただし、後で詳しく説明する非同期パターンか Webhook パターンを使用して、もっと時間のかかるアクションを API で実行したり、アクションが完了するまでエンジンを待機させたりすることもできます。

標準的なアクションであれば、swagger で公開する API に HTTP 要求メソッドを記述するだけです。  Logic Apps と連携する API Apps のサンプルは、 [GitHub リポジトリ](https://github.com/logicappsio)でご覧いただけます。  以降、カスタム コネクタを使用した一般的なパターンの実現方法を紹介します。

### <a name="long-running-actions---async-pattern"></a>実行時間の長いアクション - 非同期パターン
時間のかかるステップやタスクを実行する際は、まず、タイムアウトしたことをエンジンが認識できるようにする必要があります。 また、タスクが完了したことを認識する方法をエンジンに伝える必要もあります。さらに、エンジンがワークフローを続行できるように関連データをエンジンに返す必要もあります。 このような操作は、API を使用して、次のフローに沿って実行することができます。 以下の手順は、カスタム API の視点で記述しています。

1. 要求を受信したら、(作業を実行する前に) すぐに応答を返します。 この応答は `202 ACCEPTED` 応答で、データを取得し、ペイロードを受け取り、現在処理中であることをエンジンに知らせます。 202 応答には、次のヘッダーを含める必要があります。 
   
   * `location` ヘッダー (必須): これは、Logic Apps がジョブの状態を確認するために使用する URL への絶対パスです。
   * `retry-after` (省略可能。アクションの既定値は 20)。 これは、エンジンが状態を確認するために location ヘッダーの URL をポーリングする前に待機する秒数です。
2. ジョブの状態が確認されたら、次の確認を実行します。 
   
   * ジョブが完了している場合: `200 OK` 応答を応答ペイロードと共に返します。
   * ジョブがまだ処理中の場合: もう一度 `202 ACCEPTED` 応答を、初回の応答と同じヘッダーで返します。

このパターンでは、カスタム API のスレッド内で極端に長いタスクでも実行でき、Logic Apps エンジンでアクティブな接続が持続されて、作業が完了するまでタイムアウトにならずに続行されます。 このパターンをロジック アプリに追加する際は、ロジック アプリの定義に何もしなくても、ポーリングが継続され、状態が確認されることに注意が必要です。 有効な location ヘッダーを持つ 202 ACCEPTED 応答をエンジンが受け取ると、すぐに非同期パターンが実行され、202 以外の応答が返されるまで location ヘッダーのポーリングが継続されます。

[こちら](https://github.com/jeffhollan/LogicAppsAsyncResponseSample)

### <a name="webhook-actions"></a>Webhook アクション
ワークフロー中に、ロジック アプリを一時停止し、"コールバック" が続くのを待機させることができます。  このコールバックは、HTTP POST の形式をとります。  このパターンを実装するには、サブスクライブとサブスクライブ解除という&2; つのエンドポイントをコントローラーに提供する必要があります。

"サブスクライブ" では、ロジック アプリがコールバック URL を作成して登録します。API は、それを HTTP POST として準備された状態で格納してコールバックできます。  コンテンツやヘッダーがあればロジック アプリに渡され、ワークフローの残りの部分で使用できます。  ロジック アプリ エンジンは、実行時にそのステップに到達すると、すぐに "サブスクライブ" エンドポイントを呼び出します。

実行がキャンセルされた場合、ロジック アプリ エンジンは "サブスクライブ解除" エンドポイントを呼び出します。  これを受けて、API は必要に応じてコールバック URL の登録を解除できます。

現在、ロジック アプリ デザイナーでは、swagger による Webhook エンドポイントの検出をサポートしていないため、この種類のアクションを使用するには、"Webhook" アクションを追加し、要求の URL、ヘッダー、および本文を指定する必要があります。  必要に応じて、これらのフィールドのいずれかでワークフロー関数 `@listCallbackUrl()` を使用して、コールバック URL を渡すことができます。

[こちら](https://github.com/jeffhollan/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs)

## <a name="triggers"></a>トリガー
アクションに加えて、カスタム API をロジック アプリのトリガーとして機能させることができます。  ロジック アプリをトリガーするパターンには、次の&2; つがあります。

### <a name="polling-triggers"></a>ポーリング トリガー
ポーリング トリガーの動作は、上記の実行時間の長い非同期アクションに似ています。  ロジック アプリ エンジンは、一定の時間が経過した後に (Premium は 15 秒、Standard は1 分、Free は 1 時間など、SKU によって異なります)、トリガー エンドポイントを呼び出します。

使用できるデータがない場合、トリガーは `202 ACCEPTED` 応答で `location` と `retry-after` およびヘッダーを設定して返します。  ただし、トリガーの場合、`location` ヘッダーには `triggerState` というクエリ パラメーターを含めることが推奨されます。  これは、ロジック アプリが前回いつ起動したかを API が把握するための識別子です。  使用できるデータがある場合、トリガーは `200 OK` 応答をコンテンツ ペイロードと共に返します。  これにより、ロジック アプリが起動されます。

たとえば、ファイルが使用できるかどうかを私がポーリングした場合、皆さんは次の操作を実行するポーリング トリガーを作成できます。

* triggerState のない要求を受け取った場合、API は `202 ACCEPTED` を `location` ヘッダーと共に返します。このヘッダーには、triggerState に現在の時刻が、`retry-after` には 15 が設定されます。
* triggerState のある要求を受け取った場合は次の操作を実行します。
  * triggerState の日時の後に追加されたファイルがあるかどうかを確認します。 
  * ファイルが 1 つある場合は、`200 OK` 応答をコンテンツ ペイロードと共に返し、私が返したファイルの日時まで triggerState をインクリメントして、`retry-after` を 15 に設定します。
  * 複数のファイルがある場合、私は一度に 1 つのファイルを `200 OK` 応答と共に返し、`location` ヘッダーの triggerState をインクリメントして、`retry-after` を 0 に設定します。  こうすることで、使用可能なデータが他にもあることをエンジンに知らせることができます。エンジンはすぐに、指定された `location` ヘッダーで残りのデータを要求します。
  * ファイルがない場合は、`202 ACCEPTED` 応答を返し、`location` triggerState はそのままにしておきます。  `retry-after` は 15 に設定します。

[こちら](https://github.com/jeffhollan/LogicAppTriggersExample/tree/master/LogicAppTriggers)

### <a name="webhook-triggers"></a>WebHook トリガー
Webhook トリガーの動作は、上記の Webhook アクションに似ています。  Webhook トリガーが追加されて保存されるたびに、ロジック アプリ エンジンは "サブスクライブ" エンドポイントを呼び出します。  API は Webhook の URL を登録し、データがあるときに HTTP POST を介して呼び出すことができます。  ロジック アプリの実行には、コンテンツ ペイロードとヘッダーが渡されます。

Webhook トリガー (ロジック アプリ全体または Webhook トリガーのみ) が削除されると、エンジンは "サブスクライブ解除" URL を呼び出ます。API はこの URL を使用して、コールバック URL の登録を解除し、必要に応じてプロセスを停止できます。

現在、ロジック アプリ デザイナーでは、swagger による Webhook トリガーの検出をサポートしていないため、この種類のアクションを使用するには、"Webhook" トリガーを追加し、要求の URL、ヘッダー、および本文を指定する必要があります。  必要に応じて、これらのフィールドのいずれかでワークフロー関数 `@listCallbackUrl()` を使用して、コールバック URL を渡すことができます。

[こちら](https://github.com/jeffhollan/LogicAppTriggersExample/tree/master/LogicAppTriggers)




<!--HONumber=Jan17_HO3-->


