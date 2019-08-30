---
title: Durable Functions での人による操作とタイムアウト - Azure
description: Azure Functions の Durable Functions 拡張機能で人による操作とタイムアウトを処理する方法について説明します。
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 3918c37d985c6766fe6ad4601b70ddbd4597b0ba
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087144"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Durable Functions での人による操作 - 電話確認サンプル

このサンプルでは、人による操作を含む [Durable Functions](durable-functions-overview.md) オーケストレーションを構築する方法を示します。 自動プロセスに実際の人が関与する場合、プロセスは人への通知の送信と応答の受信を非同期に行うことができる必要があります。 また、人がいない可能性にも対応する必要があります (この最後の部分では、タイムアウトが重要になります)。

このサンプルでは、SMS ベースの電話確認システムを実装します。 この種のフローは、顧客の電話番号を確認するとき、または多要素認証 (MFA) に、よく使われます。 これは、実装全体が 2 つの小さい関数を使って行われる強力な例です。 データベースなどの外部データ ストアは必要ありません。

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>シナリオの概要

電話確認は、アプリケーションのエンド ユーザーがスパマーではなく本人であることを確認するために使われます。 多要素認証は、ハッカーからユーザー アカウントを保護するための一般的なユース ケースです。 独自の電話確認を実装する場合の課題は、人間との**ステートフルな相互作用**が必要になることです。 通常、エンド ユーザーは何らかのコード (4 桁の数字など) を提供されて、**一定の時間内に**応答する必要があります。

通常の Azure Functions は (他のプラットフォームの他の多くのクラウド エンドポイントと同様) ステートレスなので、この種の相互作用には、外部のデータベースまたは他の永続的なストアで状態を明示的に管理する必要があります。 さらの、連携できる複数の関数に相互作用を分割する必要があります。 たとえば、コードを決定し、それをどこかに永続化して、ユーザーの電話に送信するために、少なくとも 1 つの関数が必要です。 さらに、ユーザーからの応答を受信し、コードの検証を行うために何らかの方法で元の関数呼び出しにマップするために、他に少なくとも 1 つの関数が必要です。 また、セキュリティを確保するためにはタイムアウトも重要な側面です。 これは非常に複雑になる可能性があります。

このシナリオの複雑さは、Durable Functions を使うと大幅に軽減できます。 このサンプルを見るとわかるように、オーケストレーター関数は、外部データ ストアを必要とすることなく、簡単にステートフルな相互作用を管理できます。 オーケストレーター関数は "*永続的*" であるため、これらの対話型フローは高信頼性でもあります。

## <a name="configuring-twilio-integration"></a>Twilio 統合の構成

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>関数

この記事では、サンプル アプリで使われている次の関数について説明します。

* **E4_SmsPhoneVerification**
* **E4_SendSmsChallenge**

以下のセクションでは、C# スクリプトと JavaScript で使用される構成とコードについて説明します。 Visual Studio 開発用のコードは、この記事の最後に記載されています。

## <a name="the-sms-verification-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>SMS 確認オーケストレーション (Visual Studio Code と Azure Portal のサンプル コード)

**E4_SmsPhoneVerification** 関数は、オーケストレーター関数用の標準的な *function.json* を使います。

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/function.json)]

関数を実装するコードを次に示します。

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x のみ)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/index.js)]

開始されると、このオーケストレーター関数は次のことを行います。

1. SMS 通知を "*送信*" する電話番号を取得します。
2. **E4_SendSmsChallenge** を呼び出して SMS メッセージをユーザーに送信し、予期される 4 桁のチャレンジ コードを返します。
3. 現在の時刻から 90 秒後にトリガーする永続的なタイマーを作成します。
4. タイマーと並行して、ユーザーからの **SmsChallengeResponse** イベントを待ちます。

ユーザーは、4 桁のコードで SMS メッセージを受け取ります。 ユーザーが同じ 4 桁のコードをオーケストレーター関数のインスタンスに返送して確認プロセスを完了するまでに、90 秒の猶予があります。 正しくないコードを送信した場合、(同じ 90 秒の間に) さらに 3 回 正しいコードの送信を試みることができます。

> [!NOTE]
> 最初はわかりにくいかもしれませんが、このオーケストレーター関数は完全に決定論的です。 これは、タイマーの期限切れ日時を計算するために `CurrentUtcDateTime` (.NET) および `currentUtcDateTime` (JavaScript) プロパティが使用され、これらのプロパティが、オーケストレーター コード内のこのポイントで再生されるたびに同じ値を返すためです。 これは、`Task.WhenAny` (.NET) または `context.df.Task.any` (JavaScript) への呼び出しが繰り返されるたびに、確実に同じ `winner` が得られるようにするために重要です。

> [!WARNING]
> タイマーが期限切れになる必要がなくなった場合は (上の例でチャレンジ応答を受け取った場合)、[タイマーをキャンセルする](durable-functions-timers.md)ことが重要です。

## <a name="send-the-sms-message"></a>SMS メッセージの送信

**E4_SendSmsChallenge** 関数は、Twilio バインディングを使って、4 桁のコードを含む SMS メッセージをエンド ユーザーに送信します。 *function.json* の定義は次のようになります。

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/function.json)]

次に示すのは、4 桁のチャレンジ コードを生成して SMS メッセージを送信するコードです。

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x のみ)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/index.js)]

この **E4_SendSmsChallenge** 関数は、プロセスがクラッシュしたりリプレイされたりする場合であっても、1 回だけしか呼び出されません。 エンド ユーザーが複数の SMS メッセージを受け取るのは望ましくないので、これはよいことです。 戻り値の `challengeCode` は自動的に永続化されるので、オーケストレーター関数はいつでも正しいコードがわかります。

## <a name="run-the-sample"></a>サンプルを実行する

サンプルに含まれる HTTP によってトリガーされる関数を使って、次の HTTP POST 要求を送信することによりオーケストレーションを開始できます。

```
POST http://{host}/orchestrators/E4_SmsPhoneVerification
Content-Length: 14
Content-Type: application/json

"+1425XXXXXXX"
```

```
HTTP/1.1 202 Accepted
Content-Length: 695
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"id":"741c65651d4c40cea29acdd5bb47baf1","statusQueryGetUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","sendEventPostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","terminatePostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

オーケストレーター関数は、指定された電話番号を受け取ると、すぐにランダムに生成された 4 桁の確認コード (*2168* など) を含む SMS メッセージをその番号に送信します。 その後、関数は 90 秒間応答を待機します。

このコードに応答するには、別の関数内で [`RaiseEventAsync` (.NET) または `raiseEvent` (JavaScript)](durable-functions-instance-management.md) を使用するか、または上の 202 応答で参照されている **sendEventUrl** HTTP POST webhook を呼び出して `{eventName}` をイベントの名前 `SmsChallengeResponse` に置き換えることができます。

```
POST http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

タイマーの期限が切れる前にこれを送信すると、オーケストレーションが完了し、`output` フィールドに確認成功を示す `true` が設定されます。

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

タイマーの期限が切れるようにするか、または正しくないコードを 4 回入力した場合は、状態を照会して、オーケストレーション関数の出力が電話番号確認失敗を示す `false` であることを確認できます。

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="visual-studio-sample-code"></a>Visual Studio のサンプル コード

Visual Studio プロジェクトの単一の C# ファイルとしてのオーケストレーションを次に示します。

> [!NOTE]
> 下のサンプル コードを実行するには、`Microsoft.Azure.WebJobs.Extensions.Twilio` Nuget パッケージをインストールする必要があります。

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs)]

## <a name="next-steps"></a>次の手順

このサンプルでは、Durable Functions の高度な機能のいくつか、特に `WaitForExternalEvent` と `CreateTimer` について説明しました。 これらを `Task.WaitAny` と組み合わせて、信頼性の高いタイムアウト システムを実装する方法を示しました。これは、実際の人と対話する場合に役に立つことがよくあります。 Durable Functions の使用方法の詳細については、特定のトピックについて詳しく解説している一連の記事をご覧ください。

> [!div class="nextstepaction"]
> [シリーズの最初の記事に移動する](durable-functions-bindings.md)
