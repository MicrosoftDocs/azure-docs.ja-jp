---
title: Webhook - Speech Services
titlesuffix: Azure Cognitive Services
description: Webhook は、長期実行ファイルのインポート、適応、正確性テスト、文字起こしなど、長期実行プロセスを扱うときのソリューションの最適化に理想的な HTTP コール バックです。
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 7b47d4fc3aa4a1a50e441e668a856703c67045ae
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2019
ms.locfileid: "59580999"
---
# <a name="webhooks-for-speech-services"></a>Speech Services の Webhook

Webhook は、アプリケーションが使用可能になったときに Speech Services からデータを受け入れられるようにする HTTP コールバックのようなものです。 Webhook を使用すると、継続的な応答のポーリングを不要することで、REST API の使用を最適化できます。 以下のセクションで、Speech Services で Webhook を使用する方法について説明します。

## <a name="supported-operations"></a>サポート対象の操作

Speech Services は、すべての長期実行操作で Webhook をサポートします。 下に示した操作のそれぞれで、完了時に HTTP コールバックをトリガーできます。 

* DataImportCompletion
* ModelAdaptationCompletion
* AccuracyTestCompletion
* TranscriptionCompletion
* EndpointDeploymentCompletion
* EndpointDataCollectionCompletion

次に Webhook を作成しましょう。

## <a name="create-a-webhook"></a>webhook を作成する

オフラインの文字起こし用の Webhook を作成してみましょう。 シナリオ: ユーザーが、バッチ文字起こし API で非同期に文字起こしする可能性が高い長期実行のオーディオ ファイルを持っているとします。 

要求の構成パラメーターは JSON として提供されます。

```json
{
  "configuration": {
    "url": "https://your.callback.url/goes/here",
    "secret": "<my_secret>"
  },
  "events": [
    "TranscriptionCompletion"
  ],
  "active": true,
  "name": "TranscriptionCompletionWebHook",
  "description": "This is a Webhook created to trigger an HTTP POST request when my audio file transcription is completed.",
  "properties": {
      "Active" : "True"
  }

}
```
バッチ文字起こし API に対するすべての POST 要求では、`name` が必要になります。 `description` および `properties` パラメーターは省略可能です。

`Active` プロパティは、Webhook 登録を削除して再作成しなくても、URL へのコールバックのオンとオフを切り替えるために使用されます。 プロセスの完了後に 1 度コールバックすればよい場合は、Webhook を削除して、`Active` プロパティを false に切り替えます。

イベントの種類 `TranscriptionCompletion` がイベント アレイで指定されます。 文字起こしが終了状態 (`Succeeded` または `Failed`) になったときに、エンドポイントにコールバックします。 登録済み URL にコールバックすると、登録されたいずれかのイベントの種類を含んだ `X-MicrosoftSpeechServices-Event` ヘッダーが、要求に含まれます。 登録済みのイベントの種類ごとに 1 つの要求があります。 

サブスクライブできないイベントの種類が 1 つあります。 それは `Ping` のイベントの種類です。 この種類を含む要求は、ping URL (下記参照) の使用時に、Webhook の作成が完了すると URL に送信されます。  

構成では、`url` プロパティが必要です。 POST 要求はこの URL に送信されます。 `secret` は、HMAC キーとしてシークレットを使用して、ペイロードの SHA256 ハッシュを作成するために使用されます。 登録済みの URL にコールバックするときに、ハッシュは `X-MicrosoftSpeechServices-Signature` ヘッダーとして設定されます。 このヘッダーは、Base64 でエンコードされます。

このサンプルでは、C# を使用してペイロードを検証する方法を示しています。

```csharp

private const string EventTypeHeaderName = "X-MicrosoftSpeechServices-Event";
private const string SignatureHeaderName = "X-MicrosoftSpeechServices-Signature";

[HttpPost]
public async Task<IActionResult> PostAsync([FromHeader(Name = EventTypeHeaderName)]WebHookEventType eventTypeHeader, [FromHeader(Name = SignatureHeaderName)]string signature)
{
    string body = string.Empty;
    using (var streamReader = new StreamReader(this.Request.Body))
    {
        body = await streamReader.ReadToEndAsync().ConfigureAwait(false);
        var secretBytes = Encoding.UTF8.GetBytes("my_secret");
        using (var hmacsha256 = new HMACSHA256(secretBytes))
        {
            var contentBytes = Encoding.UTF8.GetBytes(body);
            var contentHash = hmacsha256.ComputeHash(contentBytes);
            var storedHash = Convert.FromBase64String(signature);
            var validated = contentHash.SequenceEqual(storedHash);
        }
    }
 
    switch (eventTypeHeader)
    {
        case WebHookEventType.Ping:
            // Do your ping event related stuff here (or ignore this event)
            break;
        case WebHookEventType.TranscriptionCompletion:
            // Do your subscription related stuff here.
            break;
        default:
            break;
    }
 
    return this.Ok();
}

```
このコード スニペットでは、`secret` はデコードされて検証されます。 Webhook イベントの種類が切り替えられたこともわかります。 現在、完了した文字起こしごとに 1 つのイベントがあります。 コードは、停止する前にイベントごとに 5 回再試行します (1 秒の遅延)。

### <a name="other-webhook-operations"></a>その他の Webhook 操作

登録されているすべての Webhook を取得するには:GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks

1 つの特定の Webhook を取得するには:GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

1 つの特定の Webhook を削除するには:DELETE https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

> [!Note] 
> 上記の例では、リージョンは 'westus' です。 これは、Azure portal で Speech Services リソースを作成したリージョンに置き換える必要があります。

POST https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/ping Body: empty

登録済みの URL に POST 要求を送信します。 要求には、`X-MicrosoftSpeechServices-Event` ヘッダーと値の ping が含まれます。 Webhook は、シークレットに登録された場合、シークレットを HMAC キーとして使用したペイロードの SHA256 ハッシュと共に `X-MicrosoftSpeechServices-Signature` ヘッダーを含みます。 ハッシュは、Base64 でエンコードされます。 

POST https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/test Body: empty

サブスクライブされたイベントの種類 (文字起こし) のエンティティがシステムに存在し、適切な状態にある場合に、登録済み URL に POST 要求を送信します。 ペイロードは、webhook を呼び出した最後のエンティティから生成されます。 エンティティが存在しない場合、POST は 204 で応答します。 テスト要求を行える場合、200 で応答します。 要求本文は、webhook がサブスクライブした特定のエンティティ (文字起こしなど) の GET 要求の場合と同じ形状です。 前述のように、要求には `X-MicrosoftSpeechServices-Event` および `X-MicrosoftSpeechServices-Signature` のヘッダーがあります。

### <a name="run-a-test"></a>テストを実行する

https://bin.webhookrelay.com の Web サイトを使用して、簡単なテストを行うことができます。 そこから、このドキュメントで前述した Webhook を作成するための HTTP POST にパラメーターとして渡すコールバック URL を取得できます。

## <a name="next-steps"></a>次の手順

* [Speech 試用版サブスクリプションを取得する](https://azure.microsoft.com/try/cognitive-services/)
