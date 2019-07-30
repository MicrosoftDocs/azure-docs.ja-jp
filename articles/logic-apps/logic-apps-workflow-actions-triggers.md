---
title: ワークフロー定義言語のトリガーとアクションの種類のリファレンス - Azure Logic Apps
description: Azure Logic Apps におけるワークフロー定義言語のトリガーとアクションの種類のリファレンス ガイド
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: reference
ms.date: 06/19/2019
ms.openlocfilehash: 490131d1743b366b5ac51a5a0fdac4b89ffe08f2
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274175"
---
# <a name="reference-for-trigger-and-action-types-in-workflow-definition-language-for-azure-logic-apps"></a>Azure Logic Apps におけるワークフロー定義言語のトリガーとアクションの種類のリファレンス

このリファレンスでは、ロジック アプリの基となるワークフロー定義でトリガーとアクションを識別するために使用される一般的な種類について説明します。ワークフロー定義については、[ワークフロー定義言語](../logic-apps/logic-apps-workflow-definition-language.md)で説明および検証されています。
ロジック アプリで使用できる特定のコネクターのトリガーおよびアクションを見つけるには、[コネクタの概要](https://docs.microsoft.com/connectors/)にある一覧を参照してください。

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>トリガーの概要

すべてのワークフローにはトリガーが含まれています。トリガーには、ワークフローをインスタンス化して開始する呼び出しが定義されています。 一般的なトリガーのカテゴリを次に示します。

* *ポーリング* トリガーでは、定期的にサービスのエンドポイントをチェックすることができます。

* *プッシュ* トリガーでは、エンドポイントへのサブスクリプションを作成し、*コールバック URL* を指定して、特定のイベントが発生したかデータが使用可能になったときにエンドポイントからトリガーに通知できるようにすることができます。 このトリガーは、起動するまでの間エンドポイントの応答を待機します。 

トリガーには次に示す最上位要素がありますが、一部は省略可能です。  
  
```json
"<trigger-name>": {
   "type": "<trigger-type>",
   "inputs": { "<trigger-inputs>" },
   "recurrence": { 
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "conditions": [ "<array-with-conditions>" ],
   "runtimeConfiguration": { "<runtime-config-options>" },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
},
```

*必須*

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*trigger-name*> | string | トリガーの名前 | 
| <*trigger-type*> | string | トリガーの種類 ("Http" や "ApiConnection" など) | 
| <*trigger-inputs*> | JSON オブジェクト | トリガーのビヘイビアーを定義する入力 | 
| <*time-unit*> | string | トリガーの起動間隔を表す時間の単位: "Second"、"Minute"、"Hour"、"Day"、"Week"、"Month" | 
| <*number-of-time-units*> | 整数 | トリガーの起動間隔を頻度に基づいて指定する値。頻度は、トリガーが再び起動するまで待機する時間の単位数です。 <p>間隔の最小値と最大値は次のとおりです。 <p>- Month: 1 から 16 か月 </br>- Day: 1 から 500 日 </br>- Hour: 1 から 12,000 時間 </br>- Minute: 1 から 72,000 分 </br>- Second: 1 から 9,999,999 秒<p>たとえば、間隔が 6 で頻度が "Month" の場合、6 か月ごとの繰り返しになります。 | 
|||| 

*省略可能*

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*array-with-conditions*> | Array | ワークフローを実行するかどうかを決定する 1 つまたは複数の[条件](#trigger-conditions)を含む配列。 トリガーでのみ使用できます。 | 
| <*runtime-config-options*> | JSON オブジェクト | 実行時のトリガーのビヘイビアーは、`runtimeConfiguration`プロパティを設定することによって変更できます。 詳細については、「[ランタイム構成の設定](#runtime-config-options)」を参照してください。 | 
| <*splitOn-expression*> | string | 配列を返すトリガーの場合、処理のために配列の項目を複数のワークフロー インスタンスに[分割、つまり*バッチ解除*](#split-on-debatch)する式を指定できます。 | 
| <*operation-option*> | string | `operationOptions` プロパティを設定して既定のビヘイビアーを変更できます。 詳細については、「[操作のオプション](#operation-options)」を参照してください。 | 
|||| 

## <a name="trigger-types-list"></a>トリガーの種類の一覧

各トリガーの種類には、トリガーの動作を定義するさまざまなインターフェイスと入力があります。 

### <a name="built-in-triggers"></a>組み込みのトリガー

| トリガーの種類 | 説明 | 
|--------------|-------------| 
| [**HTTP**](#http-trigger) | エンドポイントをチェックまたは*ポーリング*します。 このエンドポイントは特定のトリガー コントラクトに準拠する必要があり、そのためには、"202" 非同期パターンを使用するか、配列を返す必要があります。 | 
| [**HTTPWebhook**](#http-webhook-trigger) | ロジック アプリ用の呼び出し可能なエンドポイントを作成しますが、登録または登録解除を行うために指定された URL を呼び出します。 |
| [**Recurrence**](#recurrence-trigger) | 定義されているスケジュールに基づいて呼び出されます。 今後このトリガーを呼び出す日時を設定できます。 頻度に基づいて、ワークフローを実行する時間数および日数を指定することもできます。 | 
| [**Request**](#request-trigger)  | ロジック アプリ用の呼び出し可能なエンドポイントを作成します。"手動" トリガーとも呼ばれます。 例として、「[HTTP エンドポイントを通じてワークフローを呼び出し、トリガーし、入れ子にする](../logic-apps/logic-apps-http-endpoint.md)」をご覧ください。 | 
||| 

### <a name="managed-api-triggers"></a>マネージド API トリガー

| トリガーの種類 | 説明 | 
|--------------|-------------| 
| [**ApiConnection**](#apiconnection-trigger) | [Microsoft マネージド API](../connectors/apis-list.md) を使用して、エンドポイントのチェックまたは*ポーリング*を行います。 | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-trigger) | 登録または登録解除を行う [Microsoft マネージド API](../connectors/apis-list.md) を呼び出して、ロジック アプリ用の呼び出し可能なエンドポイントを作成します。 | 
||| 

## <a name="triggers---detailed-reference"></a>トリガー - 詳細なリファレンス

<a name="apiconnection-trigger"></a>

### <a name="apiconnection-trigger"></a>APIConnection トリガー  

このトリガーでは、[Microsoft マネージド API](../connectors/apis-list.md) を使用してエンドポイントをチェックまたは*ポーリング*し、このトリガーのパラメーターを、エンドポイントに基づいて異なるパラメーターにすることができます。 このトリガー定義の多くのセクションは省略可能です。 このトリガーのビヘイビアーは、セクションが含まれるかどうかによって異なります。

```json
"<APIConnection_trigger_name>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },
      "method": "<method-type>",
      "path": "/<api-operation>",
      "retryPolicy": { "<retry-behavior>" },
      "queries": { "<query-parameters>" }
   },
   "recurrence": { 
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
}
```

*必須*

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*APIConnection_trigger_name*> | string | トリガーの名前 | 
| <*connection-name*> | string | ワークフローに使用するマネージド API への接続の名前 | 
| <*method-type*> | string | マネージド API と通信するための HTTP メソッド: "GET"、"PUT"、"POST"、"PATCH"、"DELETE" | 
| <*api-operation*> | string | 呼び出す対象の API 操作 | 
| <*time-unit*> | string | トリガーの起動間隔を表す時間の単位: "Second"、"Minute"、"Hour"、"Day"、"Week"、"Month" | 
| <*number-of-time-units*> | 整数 | トリガーの起動間隔を頻度に基づいて指定する値。頻度は、トリガーが再び起動するまで待機する時間の単位数です。 <p>間隔の最小値と最大値は次のとおりです。 <p>- Month: 1 から 16 か月 </br>- Day: 1 から 500 日 </br>- Hour: 1 から 12,000 時間 </br>- Minute: 1 から 72,000 分 </br>- Second: 1 から 9,999,999 秒<p>たとえば、間隔が 6 で頻度が "Month" の場合、6 か月ごとの繰り返しになります。 | 
|||| 

*省略可能*

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*retry-behavior*> | JSON オブジェクト | 状態コード 408、429、5XX の断続的なエラーと接続の例外に対する再試行ビヘイビアーをカスタマイズします。 詳細については、「[Retry policies (再試行ポリシー)](../logic-apps/logic-apps-exception-handling.md#retry-policies)」をご覧ください。 | 
| <*query-parameters*> | JSON オブジェクト | API 呼び出しに含める任意のクエリ パラメーター。 たとえば、`"queries": { "api-version": "2018-01-01" }` オブジェクトでは `?api-version=2018-01-01` を呼び出しに追加します。 | 
| <*max-runs*> | 整数 | 既定では、ワークフロー インスタンスは、[既定の制限](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)に達するまで同時に、または並行して実行されます。 この制限を変更するには、新しい &lt;*count*&gt; 値を設定します。「[トリガーのコンカレンシーを変更する](#change-trigger-concurrency)」を参照してください。 | 
| <*max-runs-queue*> | 整数 | ワークフローで既に最大数のインスタンスが実行されている場合 (最大数は `runtimeConfiguration.concurrency.runs` プロパティに基づいて変更可能)、新たな実行は、[既定の制限](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)に達するまでこのキューに入れられます。 既定の制限を変更するには、「[実行待機の制限を変更する](#change-waiting-runs)」を参照してください。 | 
| <*splitOn-expression*> | string | 配列を返すトリガーの場合、使用する配列をこの式で参照すると、"for each" ループを使わずに、配列の項目ごとにワークフローを作成して実行することができます。 <p>たとえば、`@triggerbody()?['value']` という式は、トリガー本文の内容の中に返される配列の項目を表します。 |
| <*operation-option*> | string | `operationOptions` プロパティを設定して既定のビヘイビアーを変更できます。 詳細については、「[操作のオプション](#operation-options)」を参照してください。 |
||||

*Outputs*
 
| 要素 | Type | 説明 |
|---------|------|-------------|
| headers | JSON オブジェクト | 応答のヘッダー |
| body | JSON オブジェクト | 応答の本文 |
| status code | 整数 | 応答の状態コード |
|||| 

*例*

このトリガー定義では、Office 365 Outlook アカウントの受信トレイ内の電子メールを毎日チェックします。 

```json
"When_a_new_email_arrives": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "get",
      "path": "/Mail/OnNewEmail",
      "queries": {
          "fetchOnlyWithAttachment": false,
          "folderPath": "Inbox",
          "importance": "Any",
          "includeAttachments": false
      }
   },
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

<a name="apiconnectionwebhook-trigger"></a>

### <a name="apiconnectionwebhook-trigger"></a>ApiConnectionWebhook トリガー

このトリガーは、[Microsoft マネージド API](../connectors/apis-list.md) を使用してエンドポイントにサブスクリプション要求を送信し、エンドポイントからの応答の送信先となる*コールバック URL* を指定して、エンドポイントの応答を待機します。 詳細については、[エンドポイントのサブスクリプション](#subscribe-unsubscribe)に関する説明を参照してください。

```json
"<ApiConnectionWebhook_trigger_name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "body": {
          "NotificationUrl": "@{listCallbackUrl()}"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },
      "retryPolicy": { "<retry-behavior>" },
      "queries": "<query-parameters>"
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-run-queue>
      }
   },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
}
```

*必須*

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*connection-name*> | string | ワークフローに使用するマネージド API への接続の名前 | 
| <*body-content*> | JSON オブジェクト | マネージド API にペイロードとして送信するメッセージの内容 | 
|||| 

*省略可能*

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*retry-behavior*> | JSON オブジェクト | 状態コード 408、429、5XX の断続的なエラーと接続の例外に対する再試行ビヘイビアーをカスタマイズします。 詳細については、「[Retry policies (再試行ポリシー)](../logic-apps/logic-apps-exception-handling.md#retry-policies)」をご覧ください。 | 
| <*query-parameters*> | JSON オブジェクト | API 呼び出しに含める任意のクエリ パラメーター <p>たとえば、`"queries": { "api-version": "2018-01-01" }` オブジェクトでは `?api-version=2018-01-01` を呼び出しに追加します。 | 
| <*max-runs*> | 整数 | 既定では、ワークフロー インスタンスは、[既定の制限](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)に達するまで同時に、または並行して実行されます。 この制限を変更するには、新しい &lt;*count*&gt; 値を設定します。「[トリガーのコンカレンシーを変更する](#change-trigger-concurrency)」を参照してください。 | 
| <*max-runs-queue*> | 整数 | ワークフローで既に最大数のインスタンスが実行されている場合 (最大数は `runtimeConfiguration.concurrency.runs` プロパティに基づいて変更可能)、新たな実行は、[既定の制限](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)に達するまでこのキューに入れられます。 既定の制限を変更するには、「[実行待機の制限を変更する](#change-waiting-runs)」を参照してください。 | 
| <*splitOn-expression*> | string | 配列を返すトリガーの場合、使用する配列をこの式で参照すると、"for each" ループを使わずに、配列の項目ごとにワークフローを作成して実行することができます。 <p>たとえば、`@triggerbody()?['value']` という式は、トリガー本文の内容の中に返される配列の項目を表します。 |
| <*operation-option*> | string | `operationOptions` プロパティを設定して既定のビヘイビアーを変更できます。 詳細については、「[操作のオプション](#operation-options)」を参照してください。 | 
|||| 

*例*

このトリガー定義では、Office 365 Outlook API への登録を行い、API エンドポイントに対してコールバック URL を指定した後、新しい電子メールが到着してエンドポイントが応答するのを待機します。

```json
"When_a_new_email_arrives_(webhook)": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "body": {
         "NotificationUrl": "@{listCallbackUrl()}" 
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "path": "/MailSubscription/$subscriptions",
      "queries": {
          "folderPath": "Inbox",
          "hasAttachment": "Any",
          "importance": "Any"
      }
   },
   "splitOn": "@triggerBody()?['value']"
}
```

<a name="http-trigger"></a>

### <a name="http-trigger"></a>HTTP トリガー

このトリガーは、指定された繰り返しスケジュールに基づいて、指定されたエンドポイントをチェックまたはポーリングします。 エンドポイントの応答によって、ワークフローが実行されるかどうかが決定します。

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<endpoint-URL>",
      "headers": { "<header-content>" },
      "body": "<body-content>",
      "authentication": { "<authentication-method>" },
      "retryPolicy": { "<retry-behavior>" },
      "queries": "<query-parameters>"
   },
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

*必須*

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*method-type*> | string | 指定されたエンドポイントのポーリングに使用する HTTP メソッド: "GET"、"PUT"、"POST"、"PATCH"、"DELETE" | 
| <*endpoint-URL*> | string | ポーリング対象エンドポイントの HTTP または HTTPS の URL <p>文字列の最大サイズ: 2 KB | 
| <*time-unit*> | string | トリガーの起動間隔を表す時間の単位: "Second"、"Minute"、"Hour"、"Day"、"Week"、"Month" | 
| <*number-of-time-units*> | 整数 | トリガーの起動間隔を頻度に基づいて指定する値。頻度は、トリガーが再び起動するまで待機する時間の単位数です。 <p>間隔の最小値と最大値は次のとおりです。 <p>- Month: 1 から 16 か月 </br>- Day: 1 から 500 日 </br>- Hour: 1 から 12,000 時間 </br>- Minute: 1 から 72,000 分 </br>- Second: 1 から 9,999,999 秒<p>たとえば、間隔が 6 で頻度が "Month" の場合、6 か月ごとの繰り返しになります。 | 
|||| 

*省略可能*

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*header-content*> | JSON オブジェクト | 要求で送信するヘッダー <p>要求の言語と種類を設定する場合の例を次に示します。 <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | string | ペイロードとして要求で送信するメッセージの内容 | 
| <*authentication-method*> | JSON オブジェクト | 要求で使用する認証の方法。 詳細については、「[Scheduler 送信認証](../scheduler/scheduler-outbound-authentication.md)」を参照してください。 Scheduler 以外に、`authority` プロパティがサポートされています。 指定しない場合の既定値は `https://login.windows.net` ですが、`https://login.windows\-ppe.net` など、別の値を使用できます。 |
| <*retry-behavior*> | JSON オブジェクト | 状態コード 408、429、5XX の断続的なエラーと接続の例外に対する再試行ビヘイビアーをカスタマイズします。 詳細については、「[Retry policies (再試行ポリシー)](../logic-apps/logic-apps-exception-handling.md#retry-policies)」をご覧ください。 |  
 <*query-parameters*> | JSON オブジェクト | 要求に含める任意のクエリ パラメーター <p>たとえば、`"queries": { "api-version": "2018-01-01" }` オブジェクトでは `?api-version=2018-01-01` を要求に追加します。 | 
| <*max-runs*> | 整数 | 既定では、ワークフロー インスタンスは、[既定の制限](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)に達するまで同時に、または並行して実行されます。 この制限を変更するには、新しい &lt;*count*&gt; 値を設定します。「[トリガーのコンカレンシーを変更する](#change-trigger-concurrency)」を参照してください。 | 
| <*max-runs-queue*> | 整数 | ワークフローで既に最大数のインスタンスが実行されている場合 (最大数は `runtimeConfiguration.concurrency.runs` プロパティに基づいて変更可能)、新たな実行は、[既定の制限](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)に達するまでこのキューに入れられます。 既定の制限を変更するには、「[実行待機の制限を変更する](#change-waiting-runs)」を参照してください。 | 
| <*operation-option*> | string | `operationOptions` プロパティを設定して既定のビヘイビアーを変更できます。 詳細については、「[操作のオプション](#operation-options)」を参照してください。 | 
|||| 

*Outputs*

| 要素 | Type | 説明 |
|---------|------|-------------| 
| headers | JSON オブジェクト | 応答のヘッダー | 
| body | JSON オブジェクト | 応答の本文 | 
| status code | 整数 | 応答の状態コード | 
|||| 

*受信要求の要件*

エンドポイントがロジック アプリと適切に連携するためには、特定のトリガー パターンまたはコントラクトに準拠し、以下のプロパティを認識する必要があります。  
  
| Response | 必須 | 説明 | 
|----------|----------|-------------| 
| status code | はい | 状態コード "200 OK" によって実行が開始されます。 その他のすべての状態コードでは実行は開始されません。 | 
| Retry-after ヘッダー | いいえ | ロジック アプリがエンドポイントを再度ポーリングするまでの秒数 | 
| Location ヘッダー | いいえ | 次のポーリング間隔で呼び出す URL です。 指定されていない場合は、元の URL が使われます。 | 
|||| 

*さまざまな要求の動作の例*

| status code | 再試行までの時間 | 動作 | 
|-------------|-------------|----------|
| 200 | {なし} | ワークフローを実行し、定義済みの繰り返しの後に、まだデータがあるかどうかを再確認します。 | 
| 200 | 10 秒 | ワークフローを実行し、10 秒後に、まだデータがあるかどうかを再確認します。 |  
| 202 | 60 秒 | ワークフローをトリガーしません。 次の試行は、定義済みの繰り返しに従って 1 分後に行われます。 定義済みの繰り返しが 1 分未満の場合は、retry-after ヘッダーが優先されます。 それ以外の場合、定義済みの繰り返しが使用されます。 | 
| 400 | {なし} | 正しくない要求です。ワークフローを実行しないでください。 `retryPolicy` が定義されていない場合は、既定のポリシーが使用されます。 再試行回数に達すると、定義済みの繰り返しの後に、トリガーはデータがあるかどうかを再確認します。 | 
| 500 | {なし}| サーバー エラーです。ワークフローを実行しないでください。 `retryPolicy` が定義されていない場合は、既定のポリシーが使用されます。 再試行回数に達すると、定義済みの繰り返しの後に、トリガーはデータがあるかどうかを再確認します。 | 
|||| 

<a name="http-webhook-trigger"></a>

### <a name="httpwebhook-trigger"></a>HTTPWebhook トリガー  

このトリガーを使用すると、指定したエンドポイントの URL を呼び出してサブスクリプションを登録できるエンドポイントを作成することで、ロジック アプリを呼び出し可能にすることができます。 ワークフロー内にこのトリガーを作成すると、送信要求からサブスクリプションを登録する呼び出しを行うことができます。 そのようにして、トリガーはイベントのリッスンを開始できます。 操作によってこのトリガーが無効になったときは、送信要求においてサブスクリプションを取り消す呼び出しが自動的に行われます。 詳細については、[エンドポイントのサブスクリプション](#subscribe-unsubscribe)に関する説明を参照してください。

**HTTPWebhook** トリガーに[非同期制限](#asynchronous-limits)を指定することもできます。
トリガーのビヘイビアーは、どのセクションを使用し、どのセクションを省略するかによって異なります。 

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<endpoint-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" },
         "retryPolicy": { "<retry-behavior>" }
         },
      },
      "unsubscribe": {
         "method": "<method-type>",
         "url": "<endpoint-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" }
      }
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

<*method-type*> などの一部の値は、`"subscribe"` オブジェクトと `"unsubscribe"` オブジェクトの両方に使用できます。

*必須*

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*method-type*> | string | サブスクリプション要求に使用する HTTP メソッド: "GET"、"PUT"、"POST"、"PATCH"、または "DELETE" | 
| <*endpoint-subscribe-URL*> | string | サブスクリプション要求の送信先であるエンドポイント URL | 
|||| 

*省略可能*

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*method-type*> | string | 取り消し要求に使用する HTTP メソッド: "GET"、"PUT"、"POST"、"PATCH"、または "DELETE" | 
| <*endpoint-unsubscribe-URL*> | string | 取り消し要求の送信先であるエンドポイント URL | 
| <*body-content*> | string | サブスクリプションまたは取り消しの要求で送信するメッセージの内容 | 
| <*authentication-method*> | JSON オブジェクト | 要求で使用する認証の方法。 詳細については、「[Scheduler 送信認証](../scheduler/scheduler-outbound-authentication.md)」を参照してください。 |
| <*retry-behavior*> | JSON オブジェクト | 状態コード 408、429、5XX の断続的なエラーと接続の例外に対する再試行ビヘイビアーをカスタマイズします。 詳細については、「[Retry policies (再試行ポリシー)](../logic-apps/logic-apps-exception-handling.md#retry-policies)」をご覧ください。 | 
| <*max-runs*> | 整数 | 既定では、ワークフロー インスタンスは[既定の制限](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)に達するまですべて同時に、または並行して実行されます。 この制限を変更するには、新しい &lt;*count*&gt; 値を設定します。「[トリガーのコンカレンシーを変更する](#change-trigger-concurrency)」を参照してください。 | 
| <*max-runs-queue*> | 整数 | ワークフローで既に最大数のインスタンスが実行されている場合 (最大数は `runtimeConfiguration.concurrency.runs` プロパティに基づいて変更可能)、新たな実行は、[既定の制限](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)に達するまでこのキューに入れられます。 既定の制限を変更するには、「[実行待機の制限を変更する](#change-waiting-runs)」を参照してください。 | 
| <*operation-option*> | string | `operationOptions` プロパティを設定して既定のビヘイビアーを変更できます。 詳細については、「[操作のオプション](#operation-options)」を参照してください。 | 
|||| 

*Outputs* 

| 要素 | Type | 説明 |
|---------|------|-------------| 
| headers | JSON オブジェクト | 応答のヘッダー | 
| body | JSON オブジェクト | 応答の本文 | 
| status code | 整数 | 応答の状態コード | 
|||| 

*例*

このトリガーでは、指定したエンドポイントへのサブスクリプションを作成し、一意のコールバック URL を指定して、新たに公開される技術情報記事を待機します。

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "POST",
         "uri": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{listCallbackUrl()}",
            "hub.mode": "subscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         },
      },
      "unsubscribe": {
         "method": "POST",
         "url": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
            "hub.mode": "unsubscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         }
      }
   }
}
```

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Recurrence トリガー  

このトリガーは、指定した繰り返しスケジュールに基づいて実行され、定期実行ワークフローを作成するための簡単な方法として利用できます。 

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
      "startTime": "<start-date-time-with-format-YYYY-MM-DDThh:mm:ss>",
      "timeZone": "<time-zone>",
      "schedule": {
         // Applies only when frequency is Day or Week. Separate values with commas.
         "hours": [ <one-or-more-hour-marks> ], 
         // Applies only when frequency is Day or Week. Separate values with commas.
         "minutes": [ <one-or-more-minute-marks> ], 
         // Applies only when frequency is Week. Separate values with commas.
         "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
      }
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

*必須*

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*time-unit*> | string | トリガーの起動間隔を表す時間の単位: "Second"、"Minute"、"Hour"、"Day"、"Week"、"Month" | 
| <*number-of-time-units*> | 整数 | トリガーの起動間隔を頻度に基づいて指定する値。頻度は、トリガーが再び起動するまで待機する時間の単位数です。 <p>間隔の最小値と最大値は次のとおりです。 <p>- Month: 1 から 16 か月 </br>- Day: 1 から 500 日 </br>- Hour: 1 から 12,000 時間 </br>- Minute: 1 から 72,000 分 </br>- Second: 1 から 9,999,999 秒<p>たとえば、間隔が 6 で頻度が "Month" の場合、6 か月ごとの繰り返しになります。 | 
|||| 

*省略可能*

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*start-date-time-with-format-YYYY-MM-DDThh:mm:ss*> | string | 次の形式の開始日時: <p>タイム ゾーンを指定する場合は YYYY-MM-DDThh:mm:ss <p>または <p>タイム ゾーンを指定しない場合は YYYY-MM-DDThh:mm:ssZ <p>たとえば、2017 年 9 月 18 日午後 2 時の場合は、「2017-09-18T14:00:00」と指定し、"太平洋標準時" などのタイム ゾーンを指定します。タイム ゾーンを指定しない場合は、「2017-09-18T14:00:00Z」と指定します。 <p>**注:** この開始時刻は、[UTC の日付と時刻の形式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) (ただし、[UTC オフセット](https://en.wikipedia.org/wiki/UTC_offset)を除く) で[日付と時刻に関する ISO 8601 規格](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)に従っている必要があります。 タイム ゾーンを指定しなかった場合は、末尾にスペースを入れず、アルファベットの "Z" を追加してください。 この "Z" は、同等の[航海時間](https://en.wikipedia.org/wiki/Nautical_time)を表します。 <p>単純なスケジュールでは、開始時刻と最初の実行時刻が一致するのに対して、複雑なスケジュールでは、トリガーが作動するのは開始時刻以降となります。 開始日時の詳細については、[定期的に実行されるタスクの作成とスケジュール](../connectors/connectors-native-recurrence.md)に関するページを参照してください。 | 
| <*time-zone*> | string | 開始時刻を指定したときに限り適用されます。このトリガーに [UTC オフセット](https://en.wikipedia.org/wiki/UTC_offset)を指定することはできないためです。 適用するタイム ゾーンを指定してください。 | 
| <*one-or-more-hour-marks*> | 整数または整数配列 | `frequency` に "Day" または "Week" を指定した場合、ワークフローを実行する時刻として 0 ～ 23 の 1 つまたは複数の整数をコンマ区切りで指定できます。 <p>たとえば "10"、"12"、"14" を指定した場合、時刻のマークとして 10 AM、12 PM、2 PM が取得されます。 | 
| <*one-or-more-minute-marks*> | 整数または整数配列 | `frequency` に "Day" または "Week" を指定した場合、ワークフローを実行する時刻の分として 0 ～ 59 の 1 つまたは複数の整数をコンマ区切りで指定できます。 <p>たとえば上の例で指定した時を使用し、分の要素に「30」を指定した場合、実行時刻は 10:30 AM、12:30 PM、2:30 PM となります。 | 
| weekDays | 文字列または文字列配列 | `frequency` に "Week" を指定した場合、ワークフローを実行する 1 日または複数の日 ("Monday"、"Tuesday"、"Wednesday"、"Thursday"、"Friday"、"Saturday"、および "Sunday") をコンマ区切りで指定できます | 
| <*max-runs*> | 整数 | 既定では、ワークフロー インスタンスは[既定の制限](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)に達するまですべて同時に、または並行して実行されます。 この制限を変更するには、新しい &lt;*count*&gt; 値を設定します。「[トリガーのコンカレンシーを変更する](#change-trigger-concurrency)」を参照してください。 | 
| <*max-runs-queue*> | 整数 | ワークフローで既に最大数のインスタンスが実行されている場合 (最大数は `runtimeConfiguration.concurrency.runs` プロパティに基づいて変更可能)、新たな実行は、[既定の制限](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)に達するまでこのキューに入れられます。 既定の制限を変更するには、「[実行待機の制限を変更する](#change-waiting-runs)」を参照してください。 | 
| <*operation-option*> | string | `operationOptions` プロパティを設定して既定のビヘイビアーを変更できます。 詳細については、「[操作のオプション](#operation-options)」を参照してください。 | 
|||| 

*例 1*

次の基本的な Recurrence トリガーは、毎日実行されます。

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

*例 2*

トリガーを起動する開始日時を指定できます。 次の Recurrence トリガーは、指定した日に開始され、毎日起動します。

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1,
      "startTime": "2017-09-18T00:00:00Z"
   }
}
```

*例 3*

次の Recurrence トリガーは、2017 年 9 月 9 日午後 2 時に開始され、毎週月曜日の太平洋標準時の午前 10 時 30 分、午後 12 時 30 分、および午後 2 時 30 分に起動します。

``` json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "hours": [ 10, 12, 14 ],
         "minutes": [ 30 ],
         "weekDays": [ "Monday" ]
      },
      "startTime": "2017-09-07T14:00:00",
      "timeZone": "Pacific Standard Time"
   }
}
```

このトリガーの詳細と例については、[定期的に実行されるタスクの作成とスケジュール](../connectors/connectors-native-recurrence.md)に関するページをご覧ください。

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Request トリガー

このトリガーを使用すると、受信要求を受け入れることができるエンドポイントを作成して、ロジック アプリを呼び出し可能にすることができます。 このトリガーには、トリガーが受信要求から受信するペイロード、つまり入力を記述および検証する JSON スキーマを指定します。 そのスキーマを使用すると、ワークフロー内の後続のアクションから、より簡単にトリガーのプロパティを参照することもできます。 

このトリガーを呼び出すには、`listCallbackUrl` API を使用する必要があります。このトリガーについては、[Workflow Service REST API](https://docs.microsoft.com/rest/api/logic/workflows) の説明を参照してください。 このトリガーを HTTP エンドポイントとして使用する方法については、「[HTTP エンドポイントを通じてワークフローを呼び出し、トリガーし、入れ子にする](../logic-apps/logic-apps-http-endpoint.md)」をご覧ください。

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "<method-type>",
      "relativePath": "<relative-path-for-accepted-parameter>",
      "schema": {
         "type": "object",
         "properties": { 
            "<property-name>": {
               "type": "<property-type>"
            }
         },
         "required": [ "<required-properties>" ]
      }
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-run-queue>
      },
   },
   "operationOptions": "<operation-option>"
}
```

*必須*

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*property-name*> | string | ペイロードを記述する JSON スキーマのプロパティの名前 | 
| <*property-type*> | string | プロパティの型 | 
|||| 

*省略可能*

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*method-type*> | string | 受信要求でのロジック アプリの呼び出しに使用する必要があるメソッド: "GET"、"PUT"、"POST"、"PATCH"、"DELETE" |
| <*relative-path-for-accepted-parameter*> | string | 使用しているエンドポイントの URL で受け入れ可能なパラメーターの相対パス | 
| <*required-properties*> | Array | 値が必要な 1 つ以上のプロパティ | 
| <*max-runs*> | 整数 | 既定では、ワークフロー インスタンスは[既定の制限](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)に達するまですべて同時に、または並行して実行されます。 この制限を変更するには、新しい &lt;*count*&gt; 値を設定します。「[トリガーのコンカレンシーを変更する](#change-trigger-concurrency)」を参照してください。 | 
| <*max-runs-queue*> | 整数 | ワークフローで既に最大数のインスタンスが実行されている場合 (最大数は `runtimeConfiguration.concurrency.runs` プロパティに基づいて変更可能)、新たな実行は、[既定の制限](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)に達するまでこのキューに入れられます。 既定の制限を変更するには、「[実行待機の制限を変更する](#change-waiting-runs)」を参照してください。 | 
| <*operation-option*> | string | `operationOptions` プロパティを設定して既定のビヘイビアーを変更できます。 詳細については、「[操作のオプション](#operation-options)」を参照してください。 | 
|||| 

*例*

このトリガーでは、受信要求から HTTP POST メソッドを使用してこのトリガーを呼び出す必要があることを指定し、受信要求からの入力を検証するスキーマを含めています。 

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "POST",
      "schema": {
         "type": "object",
         "properties": {
            "customerName": {
               "type": "String"
            },
            "customerAddress": { 
               "type": "Object",
               "properties": {
                  "streetAddress": {
                     "type": "string"
                  },
                  "city": {
                     "type": "string"
                  }
               }
            }
         }
      }
   }
}
```

<a name="trigger-conditions"></a>

## <a name="trigger-conditions"></a>トリガーの条件

トリガーのみですが、どのトリガーにも、ワークフローを実行するかどうかを決める条件を表した 1 つまたは複数の式を含む配列を追加できます。 ワークフロー内のトリガーに `conditions` プロパティを追加するには、ロジック アプリをコード ビュー エディターで開きます。

たとえば、Web サイトから内部サーバー エラーが返されたときに限ってトリガーが起動するように指定する場合は、次のように `conditions` プロパティ内のトリガーの状態コードを参照します。

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Hour",
      "interval": 1
   },
   "conditions": [ {
      "expression": "@equals(triggers().code, 'InternalServerError')"
   } ]
}
```

既定では、"200 OK" の応答を受信した後に限り、トリガーが起動します。 式でトリガーの状態コードを参照すると、トリガーの既定のビヘイビアーが置き換えられます。 そのため、状態コード "200" と "201" など、複数の状態コードに対してトリガーを起動させるには、この式を条件として含める必要があります。 

`@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="trigger-multiple-runs"></a>複数の実行をトリガーする

トリガーによって返される配列をロジック アプリが処理する場合、"for each" ループに時間がかかりすぎて、配列の各項目を処理できないことがあります。 この場合は、トリガーで **SplitOn** プロパティを使用すると、配列を "*バッチ解除*" できます。 バッチ解除すると、配列の項目が分割されて、配列の項目ごとに実行される新しいワークフロー インスタンスが開始されます。 このアプローチが役立つのは、たとえば、エンドポイントをポーリングするときに、ポーリング間隔のたびに複数の新しい項目が返される場合です。
**SplitOn** が 1 つのロジック アプリ実行で処理できる配列項目の最大数については、[制限と構成](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)に関するページをご覧ください。 

> [!NOTE]
> 同期応答パターンでは **SplitOn** を使用できません。 **SplitOn** を使用して、応答アクションを含むワークフローは、非同期で実行して、すぐに `202 ACCEPTED` 応答を送信します。

トリガーの Swagger ファイルに、配列であるペイロードが記述されている場合、**SplitOn** プロパティが自動的にトリガーに追加されます。 それ以外の場合は、バッチ解除する配列を含む応答ペイロード内にこのプロパティを追加します。 

*例*

たとえば、次の応答を返す API があるとします。 
  
```json
{
   "Status": "Succeeded",
   "Rows": [ 
      { 
         "id": 938109380,
         "name": "customer-name-one"
      },
      {
         "id": 938109381,
         "name": "customer-name-two"
      }
   ]
}
```
 
ロジック アプリで必要なのは `Rows` 内の配列の内容だけであるため、次の例のようなトリガーを作成できます。

``` json
"HTTP_Debatch": {
   "type": "Http",
    "inputs": {
        "uri": "https://mydomain.com/myAPI",
        "method": "GET"
    },
   "recurrence": {
      "frequency": "Second",
      "interval": 1
    },
    "splitOn": "@triggerBody()?.Rows"
}
```

> [!NOTE]
> `SplitOn` コマンドを使う場合、配列の外側にあるプロパティを取得することはできません。 つまり、この例では、API から返される応答で `status` プロパティを取得できません。
> 
> `Rows`プロパティが存在しない場合のエラーを回避するために、この例では `?` 演算子を使用しています。

これで、このワークフロー定義では、`@triggerBody().name` を使用して `name` の値 (最初の実行から `"customer-name-one"`、2 番目の実行から `"customer-name-two"`) を取得できます。 つまり、トリガーの出力は次の例のようになります。

```json
{
   "body": {
      "id": 938109380,
      "name": "customer-name-one"
   }
}
```

```json
{
   "body": {
      "id": 938109381,
      "name": "customer-name-two"
   }
}
```

<a name="actions-overview"></a>

## <a name="actions-overview"></a>アクションの概要

Azure Logic Apps には、さまざまなアクションの種類があります。アクションの種類ごとに異なる入力を使用して、アクションの固有のビヘイビアーを定義します。 

アクションには以下の最上位要素がありますが、一部は省略可能です。

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { 
      "<input-name>": { "<input-value>" },
      "retryPolicy": "<retry-behavior>" 
   },
   "runAfter": { "<previous-trigger-or-action-status>" },
   "runtimeConfiguration": { "<runtime-config-options>" },
   "operationOptions": "<operation-option>"
},
```

*必須*

| 値 | Type | 説明 | 
|-------|------|-------------|
| <*action-name*> | string | アクションの名前 | 
| <*action-type*> | string | アクションの種類 ("Http" や "ApiConnection" など)| 
| <*input-name*> | string | アクションのビヘイビアーを定義する入力の名前 | 
| <*input-value*> | 各種 | 文字列、整数、JSON オブジェクトなどを入力値にすることができます | 
| <*previous-trigger-or-action-status*> | JSON オブジェクト | 現在のこのアクションを実行する前に直ちに実行しなければならない、トリガーまたはアクションの名前と結果の状態 | 
|||| 

*省略可能*

| 値 | Type | 説明 | 
|-------|------|-------------|
| <*retry-behavior*> | JSON オブジェクト | 状態コード 408、429、5XX の断続的なエラーと接続の例外に対する再試行ビヘイビアーをカスタマイズします。 詳細については、「再試行ポリシー」を参照してください。 | 
| <*runtime-config-options*> | JSON オブジェクト | 一部のアクションについては、`runtimeConfiguration` プロパティを設定してアクションのビヘイビアーを実行時に変更できます。 詳細については、「[ランタイム構成の設定](#runtime-config-options)」を参照してください。 | 
| <*operation-option*> | string | 一部のアクションについては、`operationOptions` プロパティを設定して既定のビヘイビアーを変更できます。 詳細については、「[操作のオプション](#operation-options)」を参照してください。 | 
|||| 

## <a name="action-types-list"></a>アクションの種類の一覧

よく使用するアクションの種類をいくつか示します。 

* 次の例のような[組み込みアクションの種類](#built-in-actions)。 

  * HTTP または HTTPS 経由でエンドポイントを呼び出すための [**HTTP**](#http-action)

  * 要求に応答するための [**Response**](#response-action)

  * JavaScript コード スニペットを実行するための [**JavaScript コードの実行**](#run-javascript-code)

  * Azure 関数を呼び出すための [**Function**](#function-action)

  * [**Join**](#join-action)、[**Compose**](#compose-action)、[**Table**](#table-action)、[**Select**](#select-action) などのデータ操作アクションと、さまざまな入力からデータを作成または変換するその他のアクション

  * 別のロジック アプリ ワークフローを呼び出すための [**Workflow**](#workflow-action)

* [マネージド API アクションの種類](#managed-api-actions) ([**ApiConnection**](#apiconnection-action) や [**ApiConnectionWebHook**](#apiconnectionwebhook-action) など) は、Microsoft が管理するさまざまなコネクタと API を呼び出します (たとえば、Azure Service Bus、Office 365 Outlook、Power BI、Azure Blob Storage、OneDrive、GitHub など)

* [ワークフローを制御するアクションの種類](#control-workflow-actions) ([**If**](#if-action)、[**Foreach**](#foreach-action)、[**Switch**](#switch-action)、[**Scope**](#scope-action)、[**Until**](#until-action) など) は、他のアクションを含み、ワークフローの実行を調整するために使用できます

<a name="built-in-actions"></a>

### <a name="built-in-actions"></a>組み込みアクション

| アクションの種類 | 説明 | 
|-------------|-------------| 
| [**作成**](#compose-action) | さまざまな種類を持つ可能性がある複数の入力から、単一の出力を作成します。 | 
| [**JavaScript コードの実行**](#run-javascript-code) | 特定の条件に適合する JavaScript コード スニペットを実行します。 コードの要件と詳細については、「[Add and run code snippets with inline code](../logic-apps/logic-apps-add-run-inline-code.md)」(インライン コードを使用してコード スニペットを追加および実行する) を参照してください。 |
| [**Function**](#function-action) | Azure 関数を呼び出します。 | 
| [**HTTP**](#http-action) | HTTP エンドポイントを呼び出します。 | 
| [**Join**](#join-action) | 配列内のすべての項目から 1 個の文字列を作成します。それらの項目は指定した区切り文字を使って区切ります。 | 
| [**Parse JSON**](#parse-json-action) | JSON のコンテンツ内にあるプロパティから、ユーザー フレンドリなトークンを作成します。 その後はロジック アプリ内にトークンを含めることで、それらのプロパティを参照できます。 | 
| [**Query**](#query-action) | 条件またはフィルターに基づいて別の配列内の項目から配列を作成します。 | 
| [**Response**](#response-action) | 受信する呼び出しまたは要求に対する応答を作成します。 | 
| [**Select**](#select-action) | 指定したマップに基づいて別の配列の項目を変換することによって、JSON オブジェクトの配列を作成します。 | 
| [**Table**](#table-action) | 配列から CSV または HTML のテーブルを作成します。 | 
| [**Terminate**](#terminate-action) | アクティブに実行中のワークフローを停止します。 | 
| [**Wait**](#wait-action) | 指定した期間、または指定した日付と時刻まで、ワークフローを一時停止します。 | 
| [**Workflow**](#workflow-action) | ワークフローを別のワークフローの中に入れ子にします。 | 
||| 

<a name="managed-api-actions"></a>

### <a name="managed-api-actions"></a>マネージド API アクション

| アクションの種類 | 説明 | 
|-------------|-------------|  
| [**ApiConnection**](#apiconnection-action) | [Microsoft マネージド API](../connectors/apis-list.md)を使用して HTTP エンドポイントを呼び出します。 | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-action) | HTTP Webhook と同様に動作しますが、[Microsoft マネージド API](../connectors/apis-list.md) を使用します。 | 
||| 

<a name="control-workflow-actions"></a>

### <a name="control-workflow-actions"></a>ワークフローを制御するアクション

これらのアクションでは、ワークフローの実行を制御し、他のアクションを含めることができます。 ワークフローを制御するアクションの外側から、そのワークフロー制御アクションの内部にあるアクションを直接参照できます。 たとえば、スコープの内部で `Http` アクションを使用している場合、ワークフロー内のどこからでも `@body('Http')` 式を参照できます。 ただし、ワークフロー制御アクションの内部に存在するアクションは、同じワークフロー制御構造体の中にある他のアクションの "後に実行" することしかできません。

| アクションの種類 | 説明 | 
|-------------|-------------| 
| [**ForEach**](#foreach-action) | 配列内のすべての項目に対して同じアクションをループ実行します。 | 
| [**If**](#if-action) | 指定した条件が true であるか false であるかに基づいて、アクションを実行します。 | 
| [**Scope**](#scope-action) | 一連のアクションから得られるグループの状態に基づいて、アクションを実行します。 | 
| [**Switch**](#switch-action) | 式、オブジェクト、またはトークンの値が、各ケースによって指定された値と一致する場合、ケース別に構成されたアクションを実行します。 | 
| [**Until**](#until-action) | 指定した条件が true になるまで、アクションをループ実行します。 | 
|||  

## <a name="actions---detailed-reference"></a>アクション - 詳細なリファレンス

<a name="apiconnection-action"></a>

### <a name="apiconnection-action"></a>APIConnection アクション

HTTP 要求を [Microsoft マネージド API](../connectors/apis-list.md) に送信するアクションです。このアクションには、API およびパラメーターに関する情報と、有効な接続への参照が必要です。 

``` json
"<action-name>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<api-name>']['connectionId']"
         },
         "<other-action-specific-input-properties>"        
      },
      "method": "<method-type>",
      "path": "/<api-operation>",
      "retryPolicy": "<retry-behavior>",
      "queries": { "<query-parameters>" },
      "<other-action-specific-properties>"
    },
    "runAfter": {}
}
```

*必須*

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*action-name*> | string | コネクタによって指定されるアクションの名前 | 
| <*api-name*> | string | 接続に使用される Microsoft マネージド API の名前 | 
| <*method-type*> | string | API を呼び出すための HTTP メソッド: "GET"、"PUT"、"POST"、"PATCH"、または "DELETE" | 
| <*api-operation*> | string | 呼び出す対象の API 操作 | 
|||| 

*省略可能*

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*other-action-specific-input-properties*> | JSON オブジェクト | この特定のアクションに適用するその他の入力プロパティ | 
| <*retry-behavior*> | JSON オブジェクト | 状態コード 408、429、5XX の断続的なエラーと接続の例外に対する再試行ビヘイビアーをカスタマイズします。 詳細については、「[Retry policies (再試行ポリシー)](../logic-apps/logic-apps-exception-handling.md#retry-policies)」をご覧ください。 | 
| <*query-parameters*> | JSON オブジェクト | API 呼び出しに含める任意のクエリ パラメーター。 <p>たとえば、`"queries": { "api-version": "2018-01-01" }` オブジェクトでは `?api-version=2018-01-01` を呼び出しに追加します。 | 
| <*other-action-specific-properties*> | JSON オブジェクト | この特定のアクションに適用するその他のプロパティ | 
|||| 

*例*

この定義は、Office 365 Outlook コネクタの**メールの送信**アクションを記述しています。このアクションは、Microsoft マネージド API です。 

```json
"Send_an_email": {
   "type": "ApiConnection",
   "inputs": {
      "body": {
         "Body": "Thank you for your membership!",
         "Subject": "Hello and welcome!",
         "To": "Sophie.Owen@contoso.com"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "POST",
      "path": "/Mail"
    },
    "runAfter": {}
}
```

<a name="apiconnection-webhook-action"></a>

### <a name="apiconnectionwebhook-action"></a>APIConnectionWebhook アクション

このアクションは、[Microsoft マネージド API](../connectors/apis-list.md) を使用して HTTP 経由でエンドポイントにサブスクリプション要求を送信し、エンドポイントからの応答の送信先となる*コールバック URL* を指定して、エンドポイントの応答を待機します。 詳細については、[エンドポイントのサブスクリプション](#subscribe-unsubscribe)に関する説明を参照してください。

```json
"<action-name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<api-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" },
         "retryPolicy": "<retry-behavior>",
         "queries": { "<query-parameters>" },
         "<other-action-specific-input-properties>"
      },
      "unsubscribe": {
         "method": "<method-type>",
         "uri": "<api-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" },
         "<other-action-specific-properties>"
      },
   },
   "runAfter": {}
}
```

<*method-type*> などの一部の値は、`"subscribe"` オブジェクトと `"unsubscribe"` オブジェクトの両方に使用できます。

*必須*

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*action-name*> | string | コネクタによって指定されるアクションの名前 | 
| <*method-type*> | string | エンドポイントの登録または登録解除に使用する HTTP メソッド: "GET"、"PUT"、"POST"、"PATCH"、または "DELETE" | 
| <*api-subscribe-URL*> | string | API への登録に使用する URI | 
|||| 

*省略可能*

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*api-unsubscribe-URL*> | string | API からの登録解除に使用する URI | 
| <*header-content*> | JSON オブジェクト | 要求で送信するヘッダー <p>言語と種類を要求に設定する場合の例を次に示します。 <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | JSON オブジェクト | 要求で送信するメッセージの内容 | 
| <*authentication-method*> | JSON オブジェクト | 要求で使用する認証の方法。 詳細については、「[Scheduler 送信認証](../scheduler/scheduler-outbound-authentication.md)」を参照してください。 |
| <*retry-behavior*> | JSON オブジェクト | 状態コード 408、429、5XX の断続的なエラーと接続の例外に対する再試行ビヘイビアーをカスタマイズします。 詳細については、「[Retry policies (再試行ポリシー)](../logic-apps/logic-apps-exception-handling.md#retry-policies)」をご覧ください。 | 
| <*query-parameters*> | JSON オブジェクト | API 呼び出しに含める任意のクエリ パラメーター <p>たとえば、`"queries": { "api-version": "2018-01-01" }` オブジェクトでは `?api-version=2018-01-01` を呼び出しに追加します。 | 
| <*other-action-specific-input-properties*> | JSON オブジェクト | この特定のアクションに適用するその他の入力プロパティ | 
| <*other-action-specific-properties*> | JSON オブジェクト | この特定のアクションに適用するその他のプロパティ | 
|||| 

**ApiConnectionWebhook** アクションに対する制限も、[HTTP 非同期制限](#asynchronous-limits)と同じ方法で指定できます。

<a name="compose-action"></a>

### <a name="compose-action"></a>作成アクション

このアクションでは、複数の入力から単一の出力を作成します。入力には式も含みます。 出力と入力の両方に、Azure Logic Apps でネイティブにサポートされている任意の型 (配列、JSON オブジェクト、XML、バイナリなど) を使用できます。
このアクションの出力を他のアクションで使用できます。 

```json
"Compose": {
   "type": "Compose",
   "inputs": "<inputs-to-compose>",
   "runAfter": {}
},
```

*必須* 

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*inputs-to-compose*> | 任意 | 単一の出力を作成するための入力 | 
|||| 

*例 1*

<!-- markdownlint-disable MD038 -->
このアクション定義では、`abcdefg ` に後続のスペースと値 `1234` を結合します。
<!-- markdownlint-enable MD038 -->

```json
"Compose": {
   "type": "Compose",
   "inputs": "abcdefg 1234",
   "runAfter": {}
},
```

このアクションで作成される出力は次のようになります。

`abcdefg 1234`

*例 2*

このアクション定義では、`abcdefg` が格納された文字列型の変数と、`1234` が格納された整数型の変数を結合します。

```json
"Compose": {
   "type": "Compose",
   "inputs": "@{variables('myString')}@{variables('myInteger')}",
   "runAfter": {}
},
```

このアクションで作成される出力は次のようになります。

`"abcdefg1234"`

<a name="run-javascript-code"></a>

### <a name="execute-javascript-code-action"></a>JavaScript コードの実行アクション

このアクションは、JavaScript コード スニペットを実行し、後続のアクションが参照できる `Result` トークンを使用して結果を返します。

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "<JavaScript-code-snippet>",
      "explicitDependencies": {
         "actions": [ <previous-actions> ],
         "includeTrigger": true
      }
   },
   "runAfter": {}
}
```

*必須*

| 値 | Type | 説明 |
|-------|------|-------------|
| <*JavaScript-code-snippet*> | 多様 | 実行する JavaScript コード。 コードの要件と詳細については、「[Add and run code snippets with inline code](../logic-apps/logic-apps-add-run-inline-code.md)」(インライン コードを使用してコード スニペットを追加および実行する) を参照してください。 <p>コード スニペットは、`code` 属性で、読み取り専用の `workflowContext` オブジェクトを入力として使用できます。 このオブジェクトには、トリガーおよびワークフロー内の前のアクションからの結果へのアクセスをコードに提供するサブプロパティがあります。 `workflowContext` オブジェクトの詳細については、「[Reference trigger and action results in your code](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext)」(コード内でトリガーとアクションの結果を参照する) を参照してください。 |
||||

*場合により必須*

`explicitDependencies` 属性は、トリガー、前のアクション、またはその両方からの結果を依存関係としてコード スニペットに明示的に含めることを指定します。 依存関係の追加の詳細については、[インライン コードへのパラメーターの追加](../logic-apps/logic-apps-add-run-inline-code.md#add-parameters)に関する記事を参照してください。 

`includeTrigger` 属性には、`true` または `false` を指定できます。

| 値 | Type | 説明 |
|-------|------|-------------|
| <*previous-actions*> | 文字列配列 | 指定したアクション名の配列。 ワークフロー定義に示されるアクション名を使用し、アクション名ではスペース (" ") ではなくアンダースコア (_) を使用します。 |
||||

*例 1*

このアクションは、ロジック アプリの名前を取得して、結果として "Hello world from <logic-app-name>" というテキストを返すコードを実行します。 この例では、コードは読み取り専用の `workflowContext` オブジェクトを介して `workflowContext.workflow.name` プロパティにアクセスすることで、ワークフローの名前を参照します。 `workflowContext` オブジェクトの使用方法の詳細については、「[Reference trigger and action results in your code](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext)」(コード内でトリガーとアクションの結果を参照する) を参照してください。

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "var text = \"Hello world from \" + workflowContext.workflow.name;\r\n\r\nreturn text;"
   },
   "runAfter": {}
}
```

*例 2*

このアクションは、Office 365 Outlook アカウントに新しい電子メールが届くとトリガーされるロジック アプリでコードを実行します。 このロジック アプリでは、受信した電子メールの内容と承認要求を転送する、承認メールの送信アクションも使用します。 

このコードは、トリガーの `Body` プロパティから電子メール アドレスを抽出し、それらの電子メール アドレスと承認アクションの `SelectedOption` プロパティ値を返します。 このアクションは、承認メールの送信アクションを依存関係として `explicitDependencies` > `actions` 属性に明示的に含めます。

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "var re = /(([^<>()\\[\\]\\\\.,;:\\s@\"]+(\\.[^<>()\\[\\]\\\\.,;:\\s@\"]+)*)|(\".+\"))@((\\[[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}])|(([a-zA-Z\\-0-9]+\\.)+[a-zA-Z]{2,}))/g;\r\n\r\nvar email = workflowContext.trigger.outputs.body.Body;\r\n\r\nvar reply = workflowContext.actions.Send_approval_email_.outputs.body.SelectedOption;\r\n\r\nreturn email.match(re) + \" - \" + reply;\r\n;",
      "explicitDependencies": {
         "actions": [
            "Send_approval_email_"
         ]
      }
   },
   "runAfter": {}
}
```



<a name="function-action"></a>

### <a name="function-action"></a>Function アクション

あらかじめ作成した [Azure 関数](../azure-functions/functions-create-first-azure-function.md)を呼び出すアクションです。

```json
"<Azure-function-name>": {
   "type": "Function",
   "inputs": {
     "function": {
        "id": "<Azure-function-ID>"
      },
      "method": "<method-type>",
      "headers": { "<header-content>" },
      "body": { "<body-content>" },
      "queries": { "<query-parameters>" } 
   },
   "runAfter": {}
}
```

*必須*

| 値 | Type | 説明 | 
|-------|------|-------------|  
| <*Azure-function-ID*> | string | 呼び出す対象である Azure 関数を表すリソース ID。 この値の形式は次のとおりです。<p>"/subscriptions/<*Azure-subscription-ID*>/resourceGroups/<*Azure-resource-group*>/providers/Microsoft.Web/sites/<*Azure-function-app-name*>/functions/<*Azure-function-name*>" | 
| <*method-type*> | string | 関数を呼び出すために使用する HTTP メソッド: "GET"、"PUT"、"POST"、"PATCH"、または "DELETE" <p>指定しない場合、既定のメソッドは "POST" です。 | 
||||

*省略可能*

| 値 | Type | 説明 | 
|-------|------|-------------|  
| <*header-content*> | JSON オブジェクト | 呼び出しで送信するヘッダー <p>言語と種類を要求に設定する場合の例を次に示します。 <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | JSON オブジェクト | 要求で送信するメッセージの内容 | 
| <*query-parameters*> | JSON オブジェクト | API 呼び出しに含める任意のクエリ パラメーター <p>たとえば、`"queries": { "api-version": "2018-01-01" }` オブジェクトでは `?api-version=2018-01-01` を呼び出しに追加します。 | 
| <*other-action-specific-input-properties*> | JSON オブジェクト | この特定のアクションに適用するその他の入力プロパティ | 
| <*other-action-specific-properties*> | JSON オブジェクト | この特定のアクションに適用するその他のプロパティ | 
||||

ロジック アプリを保存すると、参照される関数に対する以下のチェックが Logic Apps エンジンによって実行されます。

* 使用するワークフローからその関数にアクセスできる必要があります。

* ワークフローでは、標準 HTTP トリガーまたは汎用 JSON webhook トリガーだけを使用できます。 

  トリガーの URL が Logic Apps エンジンによって取得され、キャッシュされます。その URL が実行時に使用されます。 ただし、キャッシュされた URL がいずれかの操作によって無効化された場合、**Function** アクションは実行時に失敗します。 この問題を解決するには、ロジック アプリをもう一度保存して、ロジック アプリによってトリガーの URL の取得とキャッシュが再度行われるようにします。

* 関数にルートを定義することはできません。

* 使用できる承認レベルは、"function" と "anonymous" だけです。 

*例*

このアクション定義では、あらかじめ作成した "GetProductID" 関数を呼び出します。

```json
"GetProductID": {
   "type": "Function",
   "inputs": {
     "function": {
        "id": "/subscriptions/<XXXXXXXXXXXXXXXXXXXX>/resourceGroups/myLogicAppResourceGroup/providers/Microsoft.Web/sites/InventoryChecker/functions/GetProductID"
      },
      "method": "POST",
      "headers": { 
          "x-ms-date": "@utcnow()"
       },
      "body": { 
          "Product_ID": "@variables('ProductID')"
      }
   },
   "runAfter": {}
}
```

<a name="http-action"></a>

### <a name="http-action"></a>HTTP アクション

指定されたエンドポイントに要求を送信し、応答を調べて、ワークフローを実行する必要があるかどうかを判断するアクションです。 

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>"
   },
   "runAfter": {}
}
```

*必須*

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*method-type*> | string | 要求を送信するために使用するメソッド: "GET"、"PUT"、"POST"、"PATCH"、または "DELETE" | 
| <*HTTP-or-HTTPS-endpoint-URL*> | string | 呼び出す対象である HTTP または HTTPS エンドポイント。 文字列の最大サイズ: 2 KB | 
|||| 

*省略可能*

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*header-content*> | JSON オブジェクト | 要求で送信するヘッダー <p>言語と種類を設定する場合の例を次に示します。 <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | JSON オブジェクト | 要求で送信するメッセージの内容 | 
| <*retry-behavior*> | JSON オブジェクト | 状態コード 408、429、5XX の断続的なエラーと接続の例外に対する再試行ビヘイビアーをカスタマイズします。 詳細については、「[Retry policies (再試行ポリシー)](../logic-apps/logic-apps-exception-handling.md#retry-policies)」をご覧ください。 | 
| <*query-parameters*> | JSON オブジェクト | 要求に含める任意のクエリ パラメーター <p>たとえば、`"queries": { "api-version": "2018-01-01" }` オブジェクトでは `?api-version=2018-01-01` を呼び出しに追加します。 | 
| <*other-action-specific-input-properties*> | JSON オブジェクト | この特定のアクションに適用するその他の入力プロパティ | 
| <*other-action-specific-properties*> | JSON オブジェクト | この特定のアクションに適用するその他のプロパティ | 
|||| 

*例*

このアクション定義では、指定したエンドポイントに要求を送信して、最新のニュースを取得します。

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest"
   }
}
```

<a name="join-action"></a>

### <a name="join-action"></a>Join アクション

配列内のすべての項目から 1 個の文字列を作成するアクションです。それらの項目は指定した区切り文字を使って区切ります。 

```json
"Join": {
   "type": "Join",
   "inputs": {
      "from": <array>,
      "joinWith": "<delimiter>"
   },
   "runAfter": {}
}
```

*必須*

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*array*> | Array | ソース項目を渡す配列または式。 式を指定する場合は、その式を二重引用符で囲みます。 | 
| <*delimiter*> | 1 文字の文字列 | 文字列内の各項目を区切る文字 | 
|||| 

*例*

あらかじめ作成した "myIntegerArray" という変数があり、次の整数型配列が格納されているとします。 

`[1,2,3,4]` 

このアクション定義では、式の中で `variables()` 関数を使用してこの変数から値を取得し、コンマで区切ったそれらの値を含む `"1,2,3,4"` という文字列を作成します。

```json
"Join": {
   "type": "Join",
   "inputs": {
      "from": "@variables('myIntegerArray')",
      "joinWith": ","
   },
   "runAfter": {}
}
```

<a name="parse-json-action"></a>

### <a name="parse-json-action"></a>Parse JSON アクション

JSON コンテンツのプロパティからユーザー フレンドリなフィールドまたは*トークン*を作成するアクションです。 作成したトークンをロジック アプリ内でプロパティの代わりに使用して、それらのプロパティにアクセスできます。 たとえば、Azure Service Bus や Azure Cosmos DB といったサービスからの JSON 出力を使用する場合、このアクションをロジック アプリに追加することで、その出力に含まれるデータをより簡単に参照できます。 

```json
"Parse_JSON": {
   "type": "ParseJson",
   "inputs": {
      "content": "<JSON-source>",
         "schema": { "<JSON-schema>" }
      },
      "runAfter": {}
},
```

*必須*

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*JSON-source*> | JSON オブジェクト | 解析する対象の JSON コンテンツ | 
| <*JSON-schema*> | JSON オブジェクト | 基になる JSON コンテンツを記述する JSON スキーマ。ソースの JSON コンテンツを解析するために、アクションによって使用されます。 <p>**ヒント**: Logic Apps デザイナーで、スキーマを指定するかサンプル ペイロードを入力すると、アクションによるスキーマの作成が可能になります。 | 
|||| 

*例*

このアクション定義により、ワークフローで使用できる以下のトークンが作成されますが、これらを使用できるのは、**Parse JSON** アクションに続いて実行されるアクション内に限ります。 

`FirstName`、`LastName`、`Email`

```json
"Parse_JSON": {
   "type": "ParseJson",
   "inputs": {
      "content": {
         "Member": {
            "Email": "Sophie.Owen@contoso.com",
            "FirstName": "Sophie",
            "LastName": "Owen"
         }
      },
      "schema": {
         "type": "object",
         "properties": {
            "Member": {
               "type": "object",
               "properties": {
                  "Email": {
                     "type": "string"
                  },
                  "FirstName": {
                     "type": "string"
                  },
                  "LastName": {
                     "type": "string"
                  }
               }
            }
         }
      }
   },
   "runAfter": { }
},
```

この例では、アクションによる解析の対象である JSON コンテンツを "content" プロパティで指定しています。 スキーマを生成するためのサンプル ペイロードとして、次の JSON コンテンツを指定することもできます。

```json
"content": {
   "Member": { 
      "FirstName": "Sophie",
      "LastName": "Owen",
      "Email": "Sophie.Owen@contoso.com"
   }
},
```

"schema" プロパティでは、JSON コンテンツを記述するために使用される JSON スキーマを指定します。

```json
"schema": {
   "type": "object",
   "properties": {
      "Member": {
         "type": "object",
         "properties": {
            "FirstName": {
               "type": "string"
            },
            "LastName": {
               "type": "string"
            },
            "Email": {
               "type": "string"
            }
         }
      }
   }
}
```

<a name="query-action"></a>

### <a name="query-action"></a>クエリ アクション

指定された条件またはフィルターに基づいて、別の配列内の項目から配列を作成するアクションです。

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": <array>,
      "where": "<condition-or-filter>"
   },
   "runAfter": {}
}
```

*必須*

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*array*> | Array | ソース項目を渡す配列または式。 式を指定する場合は、その式を二重引用符で囲みます。 |
| <*condition-or-filter*> | string | ソース配列内の項目をフィルター処理するために使用される条件 <p>**メモ**:条件を満たす値がない場合は、アクションによって空の配列が作成されます。 |
|||| 

*例*

このアクション定義では、指定した値 2 より大きい値を持つ配列を作成します。

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": [ 1, 3, 0, 5, 4, 2 ],
      "where": "@greater(item(), 2)"
   }
}
```

<a name="response-action"></a>

### <a name="response-action"></a>応答アクション  

HTTP 要求に対する応答のペイロードを作成するアクションです。 

```json
"Response" {
    "type": "Response",
    "kind": "http",
    "inputs": {
        "statusCode": 200,
        "headers": { <response-headers> },
        "body": { <response-body> }
    },
    "runAfter": {}
},
```

*必須*

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*response-status-code*> | 整数 | 受信要求に送信される HTTP 状態コード。 既定のコードは "200 OK" ですが、2xx、4xx、または 5xx で始まる任意の有効な状態コードを使用できます。3xxx で始まるコードは使用できません。 | 
|||| 

*省略可能*

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*response-headers*> | JSON オブジェクト | 応答に含める 1 つまたは複数のヘッダー | 
| <*response-body*> | 各種 | 応答本文。文字列、JSON オブジェクト、または先行アクションからのバイナリ コンテンツとすることができます。 | 
|||| 

*例*

このアクション定義では、指定した状態コード、メッセージ本文、メッセージ ヘッダーを使用して、HTTP 要求への応答を作成します。

```json
"Response": {
   "type": "Response",
   "inputs": {
      "statusCode": 200,
      "body": {
         "ProductID": 0,
         "Description": "Organic Apples"
      },
      "headers": {
         "x-ms-date": "@utcnow()",
         "content-type": "application/json"
      }
   },
   "runAfter": {}
}
```

*制限事項*

他のアクションとは異なり、**Response** アクションには特別な制限があります。 

* ワークフローで **Response** アクションを使用できるのは、ワークフローが HTTP 要求トリガーで始まる場合だけです。つまり、ワークフローは HTTP 要求によってトリガーされる必要があります。

* **Response** アクションはワークフロー内のどの位置でも使用できます。*例外*は、**Foreach** ループ、**Until** ループ、順次ループ、並列分岐です。 

* 元の HTTP 要求でワークフローの応答が取得されるのは、**Response** アクションに必要なすべてのアクションが、[HTTP 要求のタイムアウト期限](../logic-apps/logic-apps-limits-and-config.md#request-limits)内に終了した場合だけです。

  ただし、ワークフローが、入れ子になったワークフローとして別のロジック アプリを呼び出す場合、親ワークフローは、入れ子になったワークフローが終了するまで時間がどれだけ経過しても関係なく、入れ子になったワークフローが終了するまで待機します。

* ワークフローで **Response** アクションと同期応答パターンを使用している場合は、同時にトリガー定義で **splitOn** コマンドを使用することはできません。理由は、そのコマンドによって複数の実行が作成されるからです。 PUT メソッドを使用する場合は、これに該当しているかどうかを確認し、該当している場合は、"bad request (無効な要求)" 応答を返します。

  そうでない場合、ワークフローで **splitOn** コマンドと **Response** アクションを使用すると、ワークフローは非同期で実行され、"202 ACCEPTED" 応答が直ちに返されます。

* ワークフローの実行が **Response** アクションに達したとき、受信要求が既に応答を受信している場合は、**Response** アクションは競合を理由に "Failed" とマークされます。 その結果、ロジック アプリの実行も "Failed" 状態にマークされます。

<a name="select-action"></a>

### <a name="select-action"></a>選択アクション

指定したマップに基づいて別の配列の項目を変換することによって、JSON オブジェクトの配列を作成するアクションです。 主力配列とソース配列は常に同じ数の項目を持ちます。 出力配列内のオブジェクトの数は変更できませんが、それらのオブジェクト全体にプロパティとプロパティの値を追加または削除できます。 `select` プロパティには、ソース配列内の項目を変換するためのマップを定義する、キーと値のペアを少なくとも 1 つ指定します。 キーと値のペアは、出力配列内のすべてのオブジェクトのプロパティとその値を表します。 

```json
"Select": {
   "type": "Select",
   "inputs": {
      "from": <array>,
      "select": { 
          "<key-name>": "<expression>",
          "<key-name>": "<expression>"        
      }
   },
   "runAfter": {}
},
```

*必須* 

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*array*> | Array | ソース項目を渡す配列または式。 式は必ず二重引用符で囲みます。 <p>**メモ**:ソース配列が空の場合、アクションによって空の配列が作成されます。 | 
| <*key-name*> | string | <*expression*>  の結果に割り当てられたプロパティ名<p>出力配列内のすべてのオブジェクトに新しいプロパティを追加するには、そのプロパティの <*key-name*> と、プロパティ値を表す <*expression*> を指定します。 <p>あるプロパティを配列内のすべてのオブジェクトから削除するには、そのプロパティの <*key-name*> を省略します。 | 
| <*expression*> | string | ソース配列の項目を変換し、結果を <*key-name*> に代入する式 | 
|||| 

**Select** アクションでは、出力として配列が作成されます。この出力を使用するアクションは、配列を受け入れるか、配列をコンシューマー アクションで受け入れられる型に変換する必要があります。 たとえば、出力配列を文字列に変換する場合は、その配列を **Compose** アクションに渡し、**Compose** アクションからの出力を他のアクション内で参照することができます。

*例*

このアクション定義では、整数の配列から JSON オブジェクトの配列を作成します。 アクションはソース配列を反復処理し、式 `@item()` を使用して各整数値を取得すると、各値を各 JSON オブジェクトの "`number`" プロパティに割り当てます。 

```json
"Select": {
   "type": "Select",
   "inputs": {
      "from": [ 1, 2, 3 ],
      "select": { 
         "number": "@item()" 
      }
   },
   "runAfter": {}
},
```

このアクションで作成される配列は次のようになります。

`[ { "number": 1 }, { "number": 2 }, { "number": 3 } ]`

この配列出力を他のアクション内で使用するには、この出力を **Compose** アクションに渡します。

```json
"Compose": {
   "type": "Compose",
   "inputs": "@body('Select')",
   "runAfter": {
      "Select": [ "Succeeded" ]
   }
},
```

**Compose** アクションからの出力を他のアクション内で使用できます。たとえば、**Office 365 Outlook - メールの送信**アクションで次のように使用します。

```json
"Send_an_email": {
   "type": "ApiConnection",
   "inputs": {
      "body": {
         "Body": "@{outputs('Compose')}",
         "Subject": "Output array from Select and Compose actions",
         "To": "<your-email@domain>"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "post",
      "path": "/Mail"
   },
   "runAfter": {
      "Compose": [ "Succeeded" ]
   }
},
```

<a name="table-action"></a>

### <a name="table-action"></a>テーブル アクション

配列から CSV または HTML のテーブルを作成します。 JSON オブジェクトを含む配列の場合、このアクションによって、オブジェクトのプロパティ名から列ヘッダーが自動的に作成されます。 他のデータ型を含む配列の場合、列ヘッダーと値をユーザーが指定する必要があります。 たとえば、この配列には "ID" と "Product_Name" というプロパティが含まれます。これらのプロパティを、このアクションで列ヘッダーと名前として使用できます。

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]` 

```json
"Create_<CSV | HTML>_table": {
   "type": "Table",
   "inputs": {
      "format": "<CSV | HTML>",
      "from": <array>,
      "columns": [ 
         {
            "header": "<column-name>",
            "value": "<column-value>"
         },
         {
            "header": "<column-name>",
            "value": "<column-value>"
         } 
      ]
   },
   "runAfter": {}
}
```

*必須* 

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <CSV *or* HTML>| string | 作成するテーブルの形式 | 
| <*array*> | Array | テーブルのソース項目を渡す配列または式 <p>**メモ**:ソース配列が空の場合、アクションによって空のテーブルが作成されます。 | 
|||| 

*省略可能*

列ヘッダーと値を指定またはカスタマイズするには、`columns` 配列を使用します。 ヘッダー名が同じ `header-value` のペアが複数ある場合、それらのペアの値は、そのヘッダー名の下の同じ列に表示されます。 そうでない場合、一意のヘッダーごとに一意の列が定義されます。

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*column-name*> | string | 列のヘッダー名 | 
| <*column-value*> | 任意 | その列に含まれる値 | 
|||| 

*例 1*

あらかじめ作成した "myItemArray" という変数があり、現在は次の配列が格納されているとします。 

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]`

このアクション定義では、"myItemArray" 変数から CSV テーブルを作成します。 `from` プロパティで使用している式によって、`variables()` 関数を使用して "myItemArray" から配列が取得されます。 

```json
"Create_CSV_table": {
   "type": "Table",
   "inputs": {
      "format": "CSV",
      "from": "@variables('myItemArray')"
   },
   "runAfter": {}
}
```

このアクションで作成される CSV テーブルは次のようになります。 

```
ID,Product_Name 
0,Apples 
1,Oranges 
```

*例 2*

このアクション定義では、"myItemArray" 変数から HTML テーブルを作成します。 `from` プロパティで使用している式によって、`variables()` 関数を使用して "myItemArray" から配列が取得されます。 

```json
"Create_HTML_table": {
   "type": "Table",
   "inputs": {
      "format": "HTML",
      "from": "@variables('myItemArray')"
   },
   "runAfter": {}
}
```

このアクションで作成される HTML テーブルは次のようになります。 

<table><thead><tr><th>id</th><th>Product_Name</th></tr></thead><tbody><tr><td>0</td><td>Apples</td></tr><tr><td>1</td><td>Oranges</td></tr></tbody></table>

*例 3*

このアクション定義では、"myItemArray" 変数から HTML テーブルを作成します。 ただし、この例では、既定の列ヘッダー名を "Stock_ID" と "Description" でオーバーライドし、"Description" 列の値に "Organic" という単語を追加します。

```json
"Create_HTML_table": {
   "type": "Table",
   "inputs": {
      "format": "HTML",
      "from": "@variables('myItemArray')",
      "columns": [ 
         {
            "header": "Stock_ID",
            "value": "@item().ID"
         },
         {
            "header": "Description",
            "value": "@concat('Organic ', item().Product_Name)"
         }
      ]
    },
   "runAfter": {}
},
```

このアクションで作成される HTML テーブルは次のようになります。 

<table><thead><tr><th>Stock_ID</th><th>説明</th></tr></thead><tbody><tr><td>0</td><td>Organic Apples</td></tr><tr><td>1</td><td>Organic Oranges</td></tr></tbody></table>

<a name="terminate-action"></a>

### <a name="terminate-action"></a>終了アクション

このアクションは、ワークフロー インスタンスの実行を停止し、進行中のすべてのアクションをキャンセルし、残りのアクションをすべてスキップして、指定された状態を返します。 たとえば、エラー状態のロジック アプリを完全に終了する必要があるときに、**Terminate** アクションを使用できます。 このアクションは、既に完了しているアクションには影響しません。また、順次ループを含めて、**Foreach** ループと **Until** ループの内部には指定できません。 

```json
"Terminate": {
   "type": "Terminate",
   "inputs": {
       "runStatus": "<status>",
       "runError": {
            "code": "<error-code-or-name>",
            "message": "<error-message>"
       }
   },
   "runAfter": {}
}
```

*必須*

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*status*> | string | 実行に関して返す状態: "Failed"、"Cancelled"、または "Succeeded" |
|||| 

*省略可能*

"runStatus" オブジェクトのプロパティは、"runStatus" プロパティが "Failed" 状態に設定されている場合にのみ適用されます。

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*error-code-or-name*> | string | コード、またはエラーの名前 |
| <*error-message*> | string | エラーとアプリ ユーザーが実行できる対処について説明したメッセージまたはテキスト | 
|||| 

*例*

このアクション定義では、ワークフローの実行を停止し、実行の状態を "Failed" に設定して、状態、エラー コード、およびエラー メッセージを返します。

```json
"Terminate": {
    "type": "Terminate",
    "inputs": {
        "runStatus": "Failed",
        "runError": {
            "code": "Unexpected response",
            "message": "The service received an unexpected response. Please try again."
        }
   },
   "runAfter": {}
}
```

<a name="wait-action"></a>

### <a name="wait-action"></a>待機アクション  

指定の期間または指定の時刻まで (両方は不可)、ワークフローの実行を一時停止するアクションです。 

*指定の期間*

```json
"Delay": {
   "type": "Wait",
   "inputs": {
      "interval": {
         "count": <number-of-units>,
         "unit": "<interval>"
      }
   },
   "runAfter": {}
},
```

*指定の時刻*

```json
"Delay_until": {
   "type": "Wait",
   "inputs": {
      "until": {
         "timestamp": "<date-time-stamp>"
      }
   },
   "runAfter": {}
},
```

*必須*

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*number-of-units*> | 整数 | **Delay** アクションで待機する単位数 | 
| <*interval*> | string | **Delay** アクションで待機する期間: "Second"、"Minute"、"Hour"、"Day"、"Week"、"Month" | 
| <*date-time-stamp*> | string | **Delay Until** アクションで実行を再開する日時。 この値には [UTC の日付と時刻の形式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)を指定する必要があります。 | 
|||| 

*例 1*

このアクション定義では、ワークフローを 15 分間一時停止します。

```json
"Delay": {
   "type": "Wait",
   "inputs": {
      "interval": {
         "count": 15,
         "unit": "Minute"
      }
   },
   "runAfter": {}
},
```

*例 2*

このアクション定義では、ワークフローを指定時刻まで一時停止します。

```json
"Delay_until": {
   "type": "Wait",
   "inputs": {
      "until": {
         "timestamp": "2017-10-01T00:00:00Z"
      }
   },
   "runAfter": {}
},
```

<a name="workflow-action"></a>

### <a name="workflow-action"></a>ワークフロー アクション

あらかじめ作成した別のロジック アプリを呼び出すアクションです。つまり、他のロジック アプリのワークフローを挿入して再利用することができます。 子または*入れ子になった*ロジック アプリからの出力を、入れ子になったロジック アプリに続くアクション内で使用することもできます (ただし、子ロジック アプリから応答が返される場合に限る)。

呼び出そうとしているトリガーへのアクセスが Logic Apps エンジンによってチェックされます。そのトリガーにアクセスできることを確認してください。 また、入れ子になったロジック アプリは、以下の条件を満たす必要があります。

* 入れ子になったロジック アプリをトリガー ([Request](#request-trigger) や [HTTP](#http-trigger) トリガーなど) によって呼び出し可能にします

* 親ロジック アプリと同じ Azure サブスクリプション

* 入れ子になったロジック アプリからの出力を親ロジック アプリ内で使用するには、入れ子になったロジック アプリに [Response](#response-action) アクションが必要です 

```json
"<nested-logic-app-name>": {
   "type": "Workflow",
   "inputs": {
      "body": { "<body-content" },
      "headers": { "<header-content>" },
      "host": {
         "triggerName": "<trigger-name>",
         "workflow": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group>/providers/Microsoft.Logic/<nested-logic-app-name>"
         }
      }
   },
   "runAfter": {}
}
```

*必須*

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*nested-logic-app-name*> | string | 呼び出す対象のロジック アプリの前 | 
| <*trigger-name*> | string | 入れ子になったロジック アプリ内の、呼び出す対象のトリガーの名前 | 
| <*Azure-subscription-ID*> | string | 入れ子になったロジック アプリの Azure サブスクリプション ID |
| <*Azure-resource-group*> | string | 入れ子になったロジック アプリの Azure リソース グループ名 |
| <*nested-logic-app-name*> | string | 呼び出す対象のロジック アプリの前 |
||||

*省略可能*

| 値 | Type | 説明 | 
|-------|------|-------------|  
| <*header-content*> | JSON オブジェクト | 呼び出しで送信するヘッダー | 
| <*body-content*> | JSON オブジェクト | 呼び出しで送信するメッセージの内容 | 
||||

*Outputs*

このアクションの出力は、入れ子になったロジック アプリの Response アクションに応じて異なります。 入れ子になったロジック アプリに Response アクションが含まれていない場合、出力は空です。

*例*

このワークフロー アクション定義では、"Start_search" アクションが正常に完了した後、"Get_product_information" という名前の別のロジック アプリを呼び出して、指定した入力を渡します。 

```json
"actions": {
   "Start_search": { <action-definition> },
   "Get_product_information": {
      "type": "Workflow",
      "inputs": {
         "body": {
            "ProductID": "24601",
         },
         "host": {
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/InventoryManager-RG/providers/Microsoft.Logic/Get_product_information",
            "triggerName": "Find_product"
         },
         "headers": {
            "content-type": "application/json"
         }
      },
      "runAfter": { 
         "Start_search": [ "Succeeded" ]
      }
   }
},
```

## <a name="control-workflow-action-details"></a>ワークフローを制御するアクションの詳細

<a name="foreach-action"></a>

### <a name="foreach-action"></a>Foreach アクション

このループ アクションでは、配列を反復処理し、配列の各項目に対してアクションを実行します。 既定では、"for each" ループはループの最大数まで並行して実行されます。 この最大値については、[制限と構成](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)に関するセクションを参照してください。["for each" ループを作成する方法については、こちらを参照してください](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)。

```json
"For_each": {
   "type": "Foreach",
   "actions": { 
      "<action-1>": { "<action-definition-1>" },
      "<action-2>": { "<action-definition-2>" }
   },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": <count>
      }
    },
    "operationOptions": "<operation-option>"
}
```

*必須* 

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*action-1...n*> | string | 配列の各項目に対して実行するアクションの名前 | 
| <*action-definition-1...n*> | JSON オブジェクト | 実行するアクションの定義 | 
| <*for-each-expression*> | string | 指定した配列内の各項目を参照する式 | 
|||| 

*省略可能*

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*count*> | 整数 | 既定では、"for each" ループの反復処理は、[既定の制限](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)に達するまでは同時に (並行して) 実行されます。 この制限を変更するには、新しい &lt;*count*&gt; 値を設定します。「["for each" のコンカレンシーを変更する](#change-for-each-concurrency)」を参照してください。 | 
| <*operation-option*> | string | "for each" ループを並行してではなく順次実行するには、<*operation-option*> を `Sequential` に設定するか、<*count*> を `1` に設定します。両方を設定することはできません。 詳細については、「["for each" ループを順次実行する](#sequential-for-each)」を参照してください。 | 
|||| 

*例*

この "for each" ループでは、配列の項目ごとに、受信メールに付いていた添付ファイルを含む電子メールを送信します。 このループでは、添付ファイルのレビュー担当者に、添付ファイルを含む電子メールを送信します。

```json
"For_each": {
   "type": "Foreach",
   "actions": {
      "Send_an_email": {
         "type": "ApiConnection",
         "inputs": {
            "body": {
               "Body": "@base64ToString(items('For_each')?['Content'])",
               "Subject": "Review attachment",
               "To": "Sophie.Owen@contoso.com"
                },
            "host": {
               "connection": {
                  "id": "@parameters('$connections')['office365']['connectionId']"
               }
            },
            "method": "post",
            "path": "/Mail"
         },
         "runAfter": {}
      }
   },
   "foreach": "@triggerBody()?['Attachments']",
   "runAfter": {}
}
```

トリガーからの出力として渡される配列のみを指定するには、この式でトリガー本文から <*array-name*> 配列を取得します。 配列が存在しない場合のエラーを回避するために、式では `?` 演算子を使用しています。

`@triggerBody()?['<array-name>']` 

<a name="if-action"></a>

### <a name="if-action"></a>If アクション

このアクションは*条件付きステートメント*です。条件を表す式を評価し、条件が true であるか false であるかに基づいて、異なる分岐を実行します。 条件が true の場合、条件は "Succeeded" 状態とマークされます。 [条件付きステートメントを作成する方法については、こちらを参照してください](../logic-apps/logic-apps-control-flow-conditional-statement.md)。

``` json
"Condition": {
   "type": "If",
   "expression": { "<condition>" },
   "actions": {
      "<action-1>": { "<action-definition>" }
   },
   "else": {
      "actions": {
        "<action-2>": { "<action-definition" }
      }
   },
   "runAfter": {}
}
```

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*condition*> | JSON オブジェクト | 評価する条件 (式にすることもできます) | 
| <*action-1*> | JSON オブジェクト | <*condition*> が true と評価された場合に実行するアクション | 
| <*action-definition*> | JSON オブジェクト | アクションの定義 | 
| <*action-2*> | JSON オブジェクト | <*condition*> が false と評価された場合に実行するアクション | 
|||| 

`actions` オブジェクトまたは `else` オブジェクト内のアクションで取得される状態は、次のとおりです。

* "Succeeded": 実行して成功した場合
* "Failed": 実行して失敗した場合
* "Skipped": それぞれの分岐が実行されない場合

*例*

この条件では、整数型の変数が 0 より大きい値を持つときに、ワークフローが Web サイトをチェックすることを指定しています。 変数が 0 以下の場合、ワークフローは別の Web サイトをチェックします。

```json
"Condition": {
   "type": "If",
   "expression": {
      "and": [ {
         "greater": [ "@variables('myIntegerVariable')", 0 ] 
      } ]
   },
   "actions": { 
      "HTTP - Check this website": {
         "type": "Http",
         "inputs": {
         "method": "GET",
            "uri": "http://this-url"
         },
         "runAfter": {}
      }
   },
   "else": {
      "actions": {
         "HTTP - Check this other website": {
            "type": "Http",
            "inputs": {
               "method": "GET",
               "uri": "http://this-other-url"
            },
            "runAfter": {}
         }
      }
   },
   "runAfter": {}
}
```  

#### <a name="how-conditions-use-expressions"></a>条件で式を使用する方法

条件で式を使用する方法の例を次に示します。
  
| JSON | 結果 | 
|------|--------| 
| "expression": "@parameters('<*hasSpecialAction*>')" | ブール式の場合に限り、値が true と評価されれば条件が満たされます。 <p>他の型をブール値に変換するには、`empty()` または `equals()` 関数を使用します。 | 
| "expression": "@greater(actions('<*action*>').output.value, parameters('<*threshold*>'))" | 比較関数の場合、<*action*> からの出力が <*threshold*> の値よりも大きい場合にのみ、アクションが実行されます。 | 
| "expression": "@or(greater(actions('<*action*>').output.value, parameters('<*threshold*>')), less(actions('<*same-action*>').output.value, 100))" | ロジック関数の場合、入れ子になったブール式を作成すると、<*action*> からの出力が <*threshold*> の値より大きいか 100 を下回る場合にのみ、アクションが実行されます。 | 
| "expression": "@equals(length(actions('<*action*>').outputs.errors), 0))" | 配列に項目が存在するかどうかを確認するには、配列関数を使用できます。 アクションは `errors` 配列が空のときに実行されます。 | 
||| 

<a name="scope-action"></a>

### <a name="scope-action"></a>スコープ アクション

アクションを*スコープ*として論理的にグループ化するアクションです。スコープ内のアクションの実行が終了すると、スコープ独自の状態が取得されます。 その後はスコープの状態を使用して、他のアクションを実行するかどうかを判断できます。 [スコープを作成する方法については、こちらを参照してください](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)。

```json
"Scope": {
   "type": "Scope",
   "actions": {
      "<inner-action-1>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      },
      "<inner-action-2>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      }
   }
}
```

*必須*

| 値 | Type | 説明 | 
|-------|------|-------------|  
| <*inner-action-1...n*> | JSON オブジェクト | スコープ内部で実行される 1 つ以上のアクション |
| <*action-inputs*> | JSON オブジェクト | 各アクションの入力 |
|||| 

<a name="switch-action"></a>

### <a name="switch-action"></a>switch アクション

このアクションは *switch ステートメント*とも呼ばれます。他のアクションを*ケース*として整理し、既定のケース (存在する場合) を除く各ケースに値を割り当てるアクションです。 ワークフローを実行すると、**Switch** アクションによって、式、オブジェクト、またはトークンの値が、各ケースに指定した値と比較されます。 一致するケースが **Switch** アクションによって見つかった場合、ワークフローはそのケースに対してのみ実行されます。 **Switch** アクションが実行されるたびに、一致するケースが 1 つだけ存在するか、一致が存在しないかのどちらかになります。 一致が存在しない場合、**Switch** アクションの既定のアクションが実行されます。 [switch ステートメントを作成する方法については、こちらを参照してください](../logic-apps/logic-apps-control-flow-switch-statement.md)。

``` json
"Switch": {
   "type": "Switch",
   "expression": "<expression-object-or-token>",
   "cases": {
      "Case": {
         "actions": {
           "<action-name>": { "<action-definition>" }
         },
         "case": "<matching-value>"
      },
      "Case_2": {
         "actions": {
           "<action-name>": { "<action-definition>" }
         },
         "case": "<matching-value>"
      }
   },
   "default": {
      "actions": {
         "<default-action-name>": { "<default-action-definition>" }
      }
   },
   "runAfter": {}
}
```

*必須*

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*expression-object-or-token*> | 多様 | 評価する対象の式、JSON オブジェクト、またはトークン | 
| <*action-name*> | string | 一致するケースがある場合に実行するアクションの名前 | 
| <*action-definition*> | JSON オブジェクト | 一致するケースがある場合に実行するアクションの定義 | 
| <*matching-value*> | 多様 | 評価された結果と比較する値 | 
|||| 

*省略可能*

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*default-action-name*> | string | 一致するケースが存在しないときに実行する既定のアクションの名前 | 
| <*default-action-definition*> | JSON オブジェクト | 一致するケースが存在しないときに実行するアクションの定義 | 
|||| 

*例*

このアクション定義では、承認要求の電子メールに応答した担当者が、"承認" オプションを選択したか、それとも "拒否" オプションを選択したかを評価します。 この選択に基づいて、**Switch** アクションとして、一致するケースのアクションが実行されます。つまり、異なる文章を使用した別の電子メールを応答者に送信します。 

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
            "Send_an_email": { 
               "type": "ApiConnection",
               "inputs": {
                  "Body": "Thank you for your approval.",
                  "Subject": "Response received", 
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}
         },
         "case": "Approve"
      },
      "Case_2": {
         "actions": {
            "Send_an_email_2": { 
               "type": "ApiConnection",
               "inputs": {
                  "Body": "Thank you for your response.",
                  "Subject": "Response received", 
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}     
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": { 
         "Send_an_email_3": { 
            "type": "ApiConnection",
            "inputs": {
               "Body": "Please respond with either 'Approve' or 'Reject'.",
               "Subject": "Please respond", 
               "To": "Sophie.Owen@contoso.com"
            },
            "host": {
               "connection": {
                  "name": "@parameters('$connections')['office365']['connectionId']"
               }
            },
            "method": "post",
            "path": "/Mail"
         },
         "runAfter": {} 
      }
   },
   "runAfter": {
      "Send_approval_email": [ 
         "Succeeded"
      ]
   }
}
```

<a name="until-action"></a>

### <a name="until-action"></a>Until アクション

このループ アクションには、指定した条件が true になるまで実行されるアクションが含まれています。 ループでは、他のすべてのアクションが実行された後に、最後のステップとして条件をチェックします。 `"actions"` オブジェクトには複数のアクションを含めることができます。アクションには、少なくとも 1 つの制限を定義する必要があります。 ["until" ループを作成する方法については、こちらを参照してください](../logic-apps/logic-apps-control-flow-loops.md#until-loop)。 

```json
 "Until": {
   "type": "Until",
   "actions": {
      "<action-name>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      },
      "<action-name>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      }
   },
   "expression": "<condition>",
   "limit": {
      "count": <loop-count>,
      "timeout": "<loop-timeout>"
   },
   "runAfter": {}
}
```

| 値 | Type | 説明 | 
|-------|------|-------------| 
| <*action-name*> | string | ループ内で実行するアクションの名前 | 
| <*action-type*> | string | 実行するアクションの種類 | 
| <*action-inputs*> | 各種 | アクションを実行するための入力 | 
| <*condition*> | string | ループ内のすべてのアクションの実行が終了した後に評価する条件または式 | 
| <*loop-count*> | 整数 | アクションで実行できる最大ループ回数に対する制限。 `count` の既定値は 60 です。 | 
| <*loop-timeout*> | string | ループを実行できる最長時間に対する制限。 `timeout` の既定値は `PT1H` です。これは、必須の [ISO 8601 フォーマット](https://en.wikipedia.org/wiki/ISO_8601)です。 |
|||| 

*例*

このループ アクション定義では、以下のいずれかの条件が満たされるまで、指定した URL に HTTP 要求を送信します。 

* 要求に対して状態コード "200 OK" の応答が返される。
* ループの実行回数が 60 回に達する。
* ループの実行時間が 1 時間に達する。

```json
 "Run_until_loop_succeeds_or_expires": {
    "type": "Until",
    "actions": {
        "Http": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "expression": "@equals(outputs('Http')['statusCode', 200])",
    "limit": {
        "count": 60,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

<a name="subscribe-unsubscribe"></a>

## <a name="webhooks-and-subscriptions"></a>Webhook とサブスクリプション

Webhook ベースのトリガーとアクションでは、エンドポイントの定期的なチェックは行わず、代わりに特定のイベントやエンドポイントのデータを待機します。 それらのトリガーとアクションでは、エンドポイントからの応答の送信先となる*コールバック URL* を指定することで、エンドポイントを*登録*します。

`subscribe` 呼び出しは、ワークフローに何らかの変更があったとき (たとえば、資格情報が更新されたときや、トリガーまたはアクションの入力パラメーターが変更されたとき) に行われます。 この呼び出しでは、標準の HTTP アクションと同じパラメーターを使用します。 

`unsubscribe` 呼び出しは、以下のような操作によってトリガーまたはアクションが無効になると、自動的に行われます。

* トリガーを削除または無効にする。 
* ワークフローを削除または無効にする。 
* サブスクリプションを削除または無効にする。 

これらの呼び出しをサポートするために、`@listCallbackUrl()` 式により、トリガーまたはアクション用の一意の "コールバック URL" が返されます。 この URL は、サービスの REST API を使用するエンドポイントの一意識別子を表します。 この関数のパラメーターは、webhook トリガーまたはアクションと同じです。

<a name="asynchronous-limits"></a>

## <a name="change-asynchronous-duration"></a>非同期の継続時間を変更する

トリガーとアクションの両方について、`limit.timeout` プロパティを追加することにより、非同期パターンの継続時間を特定の期間に制限できます。 そうすれば、特定の期間が経過した時点でアクションが終了していない場合、アクションの状態は `ActionTimedOut` コードを使って `Cancelled` としてマークされます。 `timeout` プロパティには [ISO 8601 形式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)を使用します。 

``` json
"<trigger-or-action-name>": {
   "type": "Workflow | Webhook | Http | ApiConnectionWebhook | ApiConnection",
   "inputs": {},
   "limit": {
      "timeout": "PT10S"
   },
   "runAfter": {}
}
```

<a name="runtime-config-options"></a>

## <a name="runtime-configuration-settings"></a>実行時の構成設定

トリガーまたはアクションの定義で `runtimeConfiguration` プロパティを使用して、トリガーとアクションの既定の実行時ビヘイビアーを変更できます。

| プロパティ | Type | 説明 | トリガーまたはアクション | 
|----------|------|-------------|-------------------| 
| `runtimeConfiguration.concurrency.runs` | 整数 | 同時に、または並行して実行できるワークフロー インスタンスの数に対する[*既定の制限*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)を変更します。 この値を使用して、バックエンド システムが受信する要求の数を制限できます。 <p>`runs` プロパティを `1` に設定すると、`operationOptions` プロパティを `SingleInstance` に設定したのと同じように機能します。 いずれか一方のプロパティを設定できます。両方を設定することはできません。 <p>既定の制限を変更するには、「[トリガーのコンカレンシーを変更する](#change-trigger-concurrency)」または「[インスタンスを順次トリガーする](#sequential-trigger)」を参照してください。 | すべてのトリガー | 
| `runtimeConfiguration.concurrency.maximumWaitingRuns` | 整数 | ワークフローが最大数の同時実行インスタンスを既に実行しているときに実行を待機できるワークフロー インスタンスの数に対する[*既定の制限*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)を変更します。 `concurrency.runs` プロパティでコンカレンシーの制限を変更できます。 <p>既定の制限を変更するには、「[実行待機の制限を変更する](#change-waiting-runs)」を参照してください。 | すべてのトリガー | 
| `runtimeConfiguration.concurrency.repetitions` | 整数 | 同時に (並行して) 実行できる "for each" ループ繰り返し回数に対する[*既定の制限*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)を変更します。 <p>`repetitions` プロパティを `1` に設定すると、`operationOptions` プロパティを `SingleInstance` に設定したのと同じように機能します。 いずれか一方のプロパティを設定できます。両方を設定することはできません。 <p>既定の制限を変更するには、「["for each" のコンカレンシーを変更する](#change-for-each-concurrency)」、または「["for each" ループを順次実行する](#sequential-for-each)」を参照してください。 | アクション: <p>[Foreach](#foreach-action) | 
| `runtimeConfiguration.paginationPolicy.minimumItemCount` | 整数 | 改ページ位置の自動修正をサポートし、これが有効になっている特定のアクションの場合、この値により、取得する結果の*最小*数を指定します。 <p>改ページ位置の自動修正を有効にするには、[改ページ位置の自動修正によるデータ、アイテム、または結果の一括取得](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md)に関する記事を参照してください | アクション:多様 |
| `runtimeConfiguration.staticResult` | JSON オブジェクト | [静的結果](../logic-apps/test-logic-apps-mock-data-static-results.md)をサポートし、その設定が有効になっているアクションの場合、`staticResult` オブジェクトには以下の属性があります。 <p>- `name`。現在のアクションの静的結果の定義名を参照します。この名前は、ロジック アプリ ワークフローの `definition` 属性の `staticResults` 属性内に表示されます。 詳細については、[静的結果 - ワークフロー定義言語のスキーマ参照](../logic-apps/logic-apps-workflow-definition-language.md#static-results)に関するページを参照してください。 <p> - `staticResultOptions`。現在のアクションに対して静的結果が `Enabled` であるかどうかを指定します。 <p>静的結果を有効にするには、「[静的な結果を設定してモック データでロジック アプリをテストする](../logic-apps/test-logic-apps-mock-data-static-results.md)」を参照してください。 | アクション:多様 |
||||| 

<a name="operation-options"></a>

## <a name="operation-options"></a>操作オプション

トリガーまたはアクションの定義で `operationOptions` プロパティを使用して、トリガーとアクションの既定のビヘイビアーを変更できます。

| 操作オプション | Type | 説明 | トリガーまたはアクション | 
|------------------|------|-------------|-------------------| 
| `DisableAsyncPattern` | string | HTTP ベースのアクションを非同期に実行するのではなく、同期的に実行します。 <p><p>このオプションを設定するには、「[アクションを同期的に実行する](#asynchronous-patterns)」を参照してください。 | アクション: <p>[ApiConnection](#apiconnection-action), <br>[HTTP](#http-action)、 <br>[応答](#response-action) | 
| `OptimizedForHighThroughput` | string | 5 分あたりのアクション実行回数に対する[既定の制限](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)を[上限](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)に変更します。 <p><p>このオプションを設定するには、「[高スループット モードで実行する](#run-high-throughput-mode)」を参照してください。 | すべてのアクション | 
| `Sequential` | string | "for each" ループの反復処理を、すべて同時に並行して実行するのではなく、一度に 1 つずつ実行します。 <p>このオプションは、`runtimeConfiguration.concurrency.repetitions` プロパティを `1` に設定したのと同じように機能します。 いずれか一方のプロパティを設定できます。両方を設定することはできません。 <p><p>このオプションを設定するには、「["for each" ループを順次実行する](#sequential-for-each)」を参照してください。| アクション: <p>[Foreach](#foreach-action) | 
| `SingleInstance` | string | 各ロジック アプリ インスタンスのトリガーを順次実行し、直前のアクティブな実行が終了するまで待機してから、次のロジック アプリ インスタンスをトリガーします。 <p><p>このオプションは、`runtimeConfiguration.concurrency.runs` プロパティを `1` に設定したのと同じように機能します。 いずれか一方のプロパティを設定できます。両方を設定することはできません。 <p>このオプションを設定するには、「[インスタンスを順次トリガーする](#sequential-trigger)」を参照してください。 | すべてのトリガー | 
||||

<a name="change-trigger-concurrency"></a>

### <a name="change-trigger-concurrency"></a>トリガーのコンカレンシーを変更する

既定では、ロジック アプリ インスタンスは、[既定の制限](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)に達するまでは同時に (並行して) 実行されます。 そのため、先行するワークフロー インスタンスの実行が終了する前に、各トリガー インスタンスが起動します。 この制限を使用して、バックエンド システムが受信する要求の数を制限できます。 

既定の制限を変更するには、コード ビュー エディターまたは Logic Apps デザイナーのどちらを使用してもかまいません。コンカレンシーの設定をデザイナーから変更すると、基になるトリガー定義において `runtimeConfiguration.concurrency.runs` プロパティの追加または更新が行われるからです (または、その逆も行われます)。 このプロパティは、並行して実行できるワークフロー インスタンスの最大数を制御します。 

> [!NOTE] 
> デザイナーまたはコード ビュー エディターを使用してトリガーの順次実行を設定する場合、コード ビュー エディターでトリガーの `operationOptions` プロパティを `SingleInstance` に設定しないでください。 これに従わないと、検証エラーになります。 詳細については、「[インスタンスを順次トリガーする](#sequential-trigger)」を参照してください。

#### <a name="edit-in-code-view"></a>コード ビューで編集する 

基になるトリガー定義において、`runtimeConfiguration.concurrency.runs` プロパティを、`1` から `50` までの値に設定して追加または更新します。

同時実行を 10 個のインスタンスに制限する例を次に示します。

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": 10
      }
   }
}
```

#### <a name="edit-in-logic-apps-designer"></a>Logic Apps デザイナーで編集する

1. トリガーの右上隅の省略記号 (...) ボタンを選択し、 **[設定]** を選択します。

2. **[コンカレンシー制御]** で、 **[Limit]\(限度\)** を **[オン]** に設定します。 

3. **[並列処理の次数]** スライダーをドラッグして必要な値に設定します。 ロジック アプリを順番に実行するには、スライダーの値を **1** にドラッグします。

<a name="change-for-each-concurrency"></a>

### <a name="change-for-each-concurrency"></a>"for each" のコンカレンシーを変更する

既定では、"for each" ループの反復処理は、[既定の制限](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)に達するまでは同時に (並行して) 実行されます。 既定の制限を変更するには、コード ビュー エディターまたは Logic Apps デザイナーのどちらを使用してもかまいません。コンカレンシーの設定をデザイナーから変更すると、基になる "for each" アクション定義において `runtimeConfiguration.concurrency.repetitions` プロパティの追加または更新が行われるからです (または、その逆も行われます)。 このプロパティでは、並行して実行できる反復処理の最大数を制御します。

> [!NOTE] 
> デザイナーまたはコード ビュー エディターを使用して "for each" アクションの順次実行を設定する場合、コード ビュー エディターでアクションの `operationOptions` プロパティを `Sequential` に設定しないでください。 これに従わないと、検証エラーになります。 詳細については、「["for each" ループを順次実行する](#sequential-for-each)」を参照してください。

#### <a name="edit-in-code-view"></a>コード ビューで編集する 

基になる "for each" 定義において、`runtimeConfiguration.concurrency.repetitions` プロパティを、`1` から `50` までの値に設定して追加または更新します。 

同時実行を 10 個の反復処理に制限する例を次に示します。

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": 10
      }
   }
}
```

#### <a name="edit-in-logic-apps-designer"></a>Logic Apps デザイナーで編集する

1. **For each** アクションの右上隅で省略記号 (...) ボタンを選択し、 **[設定]** を選択します。

2. **[コンカレンシー制御]** で、 **[コンカレンシー制御]** を **[オン]** に設定します。 

3. **[並列処理の次数]** スライダーをドラッグして必要な値に設定します。 ロジック アプリを順番に実行するには、スライダーの値を **1** にドラッグします。

<a name="change-waiting-runs"></a>

### <a name="change-waiting-runs-limit"></a>実行待機の制限を変更する

既定では、ワークフロー インスタンスは、[既定の制限](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)に達するまではすべて同時に、または並行して実行されます。 直前のアクティブなロジック アプリ インスタンスが実行を終了する前に、各トリガー インスタンスが起動します。 [この既定の制限は変更](#change-trigger-concurrency)できますが、ワークフロー インスタンスの数が新しいコンカレンシーの制限に達すると、他の新しいインスタンスは実行を待機する必要があります。 

待機できる実行の数にも[既定の制限](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)があり、既定の制限は変更できます。 ただし、ロジック アプリが実行待機の制限に達すると、Logic Apps エンジンで新しい実行が受け付けられなくなります。 要求と webhook のトリガーは 429 エラーを返し、繰り返しトリガーによるポーリングの試行がスキップされ始めます。

実行待機に関する既定の制限を変更するには、基になるトリガー定義において、`0` から `100` までの値を含む `runtimeConfiguration.concurency.maximumWaitingRuns` プロパティを追加します。 

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "maximumWaitingRuns": 50
      }
   }
}
```

<a name="sequential-trigger"></a>

### <a name="trigger-instances-sequentially"></a>インスタンスを順次トリガーする

各ロジック アプリ ワークフロー インスタンスを、必ず直前のインスタンスの実行が終了してから実行するには、トリガーの順次実行を設定します。 コード ビュー エディターまたは Logic Apps デザイナーのどちらを使用してもかまいません。コンカレンシーの設定をデザイナーから変更すると、基になるトリガー定義において `runtimeConfiguration.concurrency.runs` プロパティの追加または更新が行われるからです (または、その逆も行われます)。 

> [!NOTE] 
> デザイナーまたはコード ビュー エディターを使用してトリガーの順次実行を設定する場合、コード ビュー エディターでトリガーの `operationOptions` プロパティを `Sequential` に設定しないでください。 これに従わないと、検証エラーになります。 

#### <a name="edit-in-code-view"></a>コード ビューで編集する

トリガー定義において、以下のプロパティのどちらか一方を設定します。両方を設定することはできません。 

`runtimeConfiguration.concurrency.runs` プロパティを `1` に設定します。

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": 1
      }
   }
}
```

*- または -*

`operationOptions` プロパティを `SingleInstance` に設定します。

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "operationOptions": "SingleInstance"
}
```

#### <a name="edit-in-logic-apps-designer"></a>Logic Apps デザイナーで編集する

1. トリガーの右上隅の省略記号 (...) ボタンを選択し、 **[設定]** を選択します。

2. **[コンカレンシー制御]** で、 **[Limit]\(限度\)** を **[オン]** に設定します。 

3. **[並列処理の次数]** スライダーをドラッグして数値 `1` に設定します。 

<a name="sequential-for-each"></a>

### <a name="run-for-each-loops-sequentially"></a>"for each" ループを順次実行する

"for each" ループを、必ず直前の反復処理の実行が終了してから実行するには、"for each" アクションの順次実行を設定します。 コード ビュー エディターまたは Logic Apps デザイナーのどちらを使用してもかまいません。アクションのコンカレンシーの設定をデザイナーから変更すると、基になるアクション定義において `runtimeConfiguration.concurrency.repetitions` プロパティの追加または更新が行われるからです (または、その逆も行われます)。 

> [!NOTE] 
> デザイナーまたはコード ビュー エディターを使用して "for each" アクションの順次実行を設定する場合、コード ビュー エディターでアクションの `operationOptions` プロパティを `Sequential` に設定しないでください。 これに従わないと、検証エラーになります。 

#### <a name="edit-in-code-view"></a>コード ビューで編集する

アクション定義において、以下のプロパティのどちらか一方を設定します。両方を設定することはできません。 

`runtimeConfiguration.concurrency.repetitions` プロパティを `1` に設定します。

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": 1
      }
   }
}
```

*- または -*

`operationOptions` プロパティを `Sequential` に設定します。

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "operationOptions": "Sequential"
}
```

#### <a name="edit-in-logic-apps-designer"></a>Logic Apps デザイナーで編集する

1. **For each** アクションの右上隅の省略記号 (...) ボタンを選択し、 **[設定]** を選択します。

2. **[コンカレンシー制御]** で、 **[コンカレンシー制御]** を **[オン]** に設定します。 

3. **[並列処理の次数]** スライダーをドラッグして数値 `1` に設定します。 

<a name="asynchronous-patterns"></a>

### <a name="run-actions-synchronously"></a>アクションを同期的に実行する

既定では、HTTP ベースのすべてのアクションは、標準的な非同期操作パターンに従います。 このパターンは、HTTP ベースのアクションから指定したエンドポイントに要求を送信するときに、リモート サーバーから "202 ACCEPTED" 応答が返されることを指定します。 この応答は、処理の要求がサーバーで受け付けられたことを意味します。 Logic Apps エンジンは、処理が停止する (202 以外の応答) まで、応答の場所ヘッダーに指定された URL を継続的にチェックします。

ただし、要求にはタイムアウト制限があるため、長時間実行しているアクションについては、非同期ビヘイビアーを無効にすることができます。そのためには、アクションの入力において、`operationOptions` プロパティを追加して `DisableAsyncPattern` に設定します。
  
```json
"<some-long-running-action>": {
   "type": "Http",
   "inputs": { "<action-inputs>" },
   "operationOptions": "DisableAsyncPattern",
   "runAfter": {}
}
```

<a name="run-high-throughput-mode"></a>

### <a name="run-in-high-throughput-mode"></a>高スループット モードで実行する

1 回のロジック アプリの定義において、5 分ごとに実行されるアクションの数には、[既定の制限](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)が設定されています。 この上限を可能な[最大数](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)まで引き上げるには、`operationOptions` プロパティを `OptimizedForHighThroughput` に設定します。 この設定により、ロジック アプリが "高スループット" モードになります。 

> [!NOTE]
> 高スループット モードはプレビュー段階です。 必要に応じて複数のロジック アプリにワークロードを分散することもできます。

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { "<action-inputs>" },
   "operationOptions": "OptimizedForHighThroughput",
   "runAfter": {}
}
```

<a name="connector-authentication"></a>

## <a name="authenticate-http-triggers-and-actions"></a>HTTP トリガーとアクションを認証する

HTTP エンドポイントはさまざまな認証をサポートしています。 次の HTTP トリガーとアクションに対して認証を設定できます。

* [HTTP](../connectors/connectors-native-http.md)
* [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)
* [HTTP Webhook](../connectors/connectors-native-webhook.md)

設定できる認証の種類は次のとおりです。

* [基本認証](#basic-authentication)
* [クライアント証明書認証](#client-certificate-authentication)
* [Azure Active Directory (Azure AD) OAuth 認証](#azure-active-directory-oauth-authentication)

> [!IMPORTANT]
> ロジック アプリ ワークフロー定義で処理される機密情報を保護していることを確認します。 セキュリティで保護されたパラメーターを使用し、必要に応じてデータをエンコードします。 パラメーターの使用とセキュリティ保護の詳細については、[ロジック アプリのセキュリティ保護](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)に関するページを参照してください。

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>基本認証

Azure Active Directory を使用する[基本認証](../active-directory-b2c/active-directory-b2c-custom-rest-api-netfw-secure-basic.md)では、トリガーまたはアクションの定義に、`authentication` JSON オブジェクトを含めることができます。このオブジェクトには、以下の表で指定されたプロパティがあります。 実行時にパラメーター値にアクセスする場合は、[ワークフロー定義言語](https://aka.ms/logicappsdocs)で提供される `@parameters('parameterName')` 式を使用できます。 

| プロパティ | 必須 | 値 | 説明 | 
|----------|----------|-------|-------------| 
| **type** | はい | "Basic" | 使用する認証の種類。ここでは "Basic" です | 
| **username** | はい | "@parameters('userNameParam')" | ターゲット サービス エンドポイントへのアクセスを認証するためのユーザー名 |
| **password** | はい | "@parameters('passwordParam')" | ターゲット サービス エンドポイントへのアクセスを認証するためのパスワード |
||||| 

この HTTP アクション定義の例では、`authentication` セクションで `Basic` 認証が指定されます。 パラメーターの使用とセキュリティ保護の詳細については、[ロジック アプリのセキュリティ保護](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)に関するページを参照してください。

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://www.microsoft.com",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

> [!IMPORTANT]
> ロジック アプリ ワークフロー定義で処理される機密情報を保護していることを確認します。 セキュリティで保護されたパラメーターを使用し、必要に応じてデータをエンコードします。 パラメーターのセキュリティ保護の詳細については、[ロジック アプリのセキュリティ保護](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)に関するページを参照してください。

<a name="client-certificate-authentication"></a>

### <a name="client-certificate-authentication"></a>クライアント証明書認証

Azure Active Directory を使用する[証明書ベース認証](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)では、トリガーまたはアクションの定義に `authentication` JSON オブジェクトを含めることができます。このオブジェクトには、以下の表で指定されたプロパティがあります。 実行時にパラメーター値にアクセスする場合は、[ワークフロー定義言語](https://aka.ms/logicappsdocs)で提供される `@parameters('parameterName')` 式を使用できます。 使用できるクライアント証明書の数の制限については、[Azure Logic Apps の制限と構成](../logic-apps/logic-apps-limits-and-config.md)に関するページを参照してください。

| プロパティ | 必須 | 値 | 説明 |
|----------|----------|-------|-------------|
| **type** | はい | "ClientCertificate" | Secure Sockets Layer (SSL) クライアント証明書に使用する認証の種類。 自己署名証明書はサポートされていますが、SSL 用の自己署名証明書はサポートされていません。 |
| **pfx** | はい | "@parameters('pfxParam') | Base64 でエンコードされた Personal Information Exchange (PFX) ファイルのコンテンツ |
| **password** | はい | "@parameters('passwordParam')" | PFX ファイルにアクセスするためのパスワード |
||||| 

この HTTP アクション定義の例では、`authentication` セクションで `ClientCertificate` 認証が指定されます。 パラメーターの使用とセキュリティ保護の詳細については、[ロジック アプリのセキュリティ保護](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)に関するページを参照してください。

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://www.microsoft.com",
      "authentication": {
         "type": "ClientCertificate",
         "pfx": "@parameters('pfxParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

> [!IMPORTANT]
> ロジック アプリ ワークフロー定義で処理される機密情報を保護していることを確認します。 セキュリティで保護されたパラメーターを使用し、必要に応じてデータをエンコードします。 パラメーターのセキュリティ保護の詳細については、[ロジック アプリのセキュリティ保護](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)に関するページを参照してください。

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-ad-oauth-authentication"></a>Azure Active Directory (AD) OAuth 認証

[Azure AD OAuth 認証](../active-directory/develop/authentication-scenarios.md)では、トリガーまたはアクションの定義に `authentication` JSON オブジェクトを含めることができます。このオブジェクトには、以下の表で指定されたプロパティがあります。 実行時にパラメーター値にアクセスする場合は、[ワークフロー定義言語](https://aka.ms/logicappsdocs)で提供される `@parameters('parameterName')` 式を使用できます。

| プロパティ | 必須 | 値 | 説明 |
|----------|----------|-------|-------------|
| **type** | はい | `ActiveDirectoryOAuth` | 使用する認証の種類 (Azure AD OAuth の場合は "ActiveDirectoryOAuth") |
| **authority** | いいえ | <*URL-for-authority-token-issuer*> | 認証トークンを提供する機関の URL |
| **tenant** | はい | <*tenant-ID*> | Azure AD テナントのテナント ID |
| **audience** | はい | <*resource-to-authorize*> | 承認で使用するリソース (`https://management.core.windows.net/` など) |
| **clientId** | はい | <*client-ID*> | 承認を要求しているアプリのクライアント ID |
| **credentialType** | はい | "Certificate" または "Secret" | クライアントが承認を要求するために使用する資格情報の種類。 このプロパティと値は基の定義には出現しませんが、その資格情報の種類に必要なパラメーターが決まります。 |
| **pfx** | はい (ただし資格情報の種類が "Certificate" の場合のみ) | "@parameters('pfxParam') | Base64 でエンコードされた Personal Information Exchange (PFX) ファイルのコンテンツ |
| **password** | はい (ただし資格情報の種類が "Certificate" の場合のみ) | "@parameters('passwordParam')" | PFX ファイルにアクセスするためのパスワード |
| **secret** | はい (ただし資格情報の種類が "Secret" の場合のみ) | "@parameters('secretParam')" | 承認を要求しているクライアント シークレット |
|||||

この HTTP アクション定義の例では、`authentication` セクションで `ActiveDirectoryOAuth` 認証と "Secret" 資格情報の種類が指定されます。 パラメーターの使用とセキュリティ保護の詳細については、[ロジック アプリのセキュリティ保護](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)に関するページを参照してください。

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://www.microsoft.com",
      "authentication": {
         "type": "ActiveDirectoryOAuth",
         "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
         "audience": "https://management.core.windows.net/",
         "clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
         "secret": "@parameters('secretParam')"
     }
   },
   "runAfter": {}
}
```

> [!IMPORTANT]
> ロジック アプリ ワークフロー定義で処理される機密情報を保護していることを確認します。 セキュリティで保護されたパラメーターを使用し、必要に応じてデータをエンコードします。 パラメーターのセキュリティ保護の詳細については、[ロジック アプリのセキュリティ保護](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

* [ワークフロー定義言語](../logic-apps/logic-apps-workflow-definition-language.md)
