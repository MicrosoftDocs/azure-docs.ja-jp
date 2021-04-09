---
title: バッチ文字起こしの使用方法 - 音声サービス
titleSuffix: Azure Cognitive Services
description: バッチ文字起こしは、Azure BLOB などのストレージにある大量の音声を文字起こしする場合に理想的です。 専用の REST API を使用すると、Shared Access Signatures (SAS) URI でオーディオ ファイルを示して、非同期に文字起こしを受け取ることができます。
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/23/2020
ms.author: wolfma
ms.custom: devx-track-csharp
ms.openlocfilehash: e48fead4d4364fd84f178388dbfb9158296e687b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98659973"
---
# <a name="how-to-use-batch-transcription"></a>バッチ文字起こしの使用方法

バッチ文字起こしは、ストレージ内の大量のオーディオを文字起こしできる一連の REST API 操作です。 一般的な URI または [Shared Access Signatures (SAS)](../../storage/common/storage-sas-overview.md) URI を使用してオーディオ ファイルを示し、非同期に文字起こしの結果を受け取ることができます。 v3.0 API では、1 つ以上のオーディオ ファイルを文字起こしするか、またはストレージ コンテナー全体を処理することがきます。

バッチ文字起こし REST API を使用すると、次のメソッドを呼び出すことができます。

|    バッチ文字起こし操作                                             |    Method    |    REST API の呼び出し                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    新しい文字起こしを作成する。                                              |    POST      |    speechtotext/v3.0/transcriptions            |
|    認証されたサブスクリプションに対する文字起こしのリストを取得する。    |    GET       |    speechtotext/v3.0/transcriptions            |
|    オフライン文字起こしでサポートされているロケールの一覧を取得する。              |    GET       |    speechtotext/v3.0/transcriptions/locales    |
|    ID によって示された文字起こしの変更可能な詳細を更新する。    |    PATCH     |    speechtotext/v3.0/transcriptions/{id}       |
|    指定した文字起こしタスクを削除する。                                 |    DELETE    |    speechtotext/v3.0/transcriptions/{id}       |
|    指定した ID によって示される文字起こしを取得する。                        |    GET       |    speechtotext/v3.0/transcriptions/{id}       |
|    指定された ID で識別された文字起こしの結果ファイルを取得します。    |    GET       |    speechtotext/v3.0/transcriptions/{id}/files |

詳細な API を確認してテストできます。これは、[Swagger ドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)で入手できます。

バッチ文字起こしジョブは、ベスト エフォート ベースでスケジュールされます。
ジョブが実行状態になるタイミングを見積もることはできませんが、通常のシステム負荷では数分以内に発生します。 いったん実行状態になると、文字起こしはオーディオのランタイム再生速度よりも速く発生します。

## <a name="prerequisites"></a>前提条件

Speech Service の他の機能と同様に、[使用開始ガイド](overview.md#try-the-speech-service-for-free)に従って [Azure portal](https://portal.azure.com) でサブスクリプション キーを作成します。

>[!NOTE]
> バッチ文字起こしを使用するには、音声サービスの Standard サブスクリプション (S0) が必要です。 Free サブスクリプション キー (F0) は機能しません。 詳細については、[価格と制限](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)に関するページを参照してください。

モデルをカスタマイズする予定がある場合は、[音響のカスタマイズ](./how-to-custom-speech-train-model.md)と[言語のカスタマイズ](./how-to-custom-speech-train-model.md)の手順に従ってください。 作成されたモデルをバッチ文字起こしで使用するには、モデルの場所が必要です。 モデルの詳細 (`self` プロパティ) を調べると、モデルの場所を取得できます。 デプロイされたカスタム エンドポイントは、バッチ文字起こしサービスには "*必要ありません*"。

>[!NOTE]
> REST API の一部として、バッチ文字起こしには[クォータと制限](speech-services-quotas-and-limits.md#batch-transcription)のセットがあり、これらはレビューすることをお勧めします。 大量のオーディオ ファイルを効率的に文字起こしするためのバッチ文字起こし機能を最大限に活用するには、要求ごとに常に複数のファイルを送信するか、または文字起こしするオーディオ ファイルを含む Blob Storage コンテナーを指定することをお勧めします。 このサービスにより、ターンアラウンド時間を短縮しながらファイルの文字起こしが同時に行われます。 1 つの要求での複数ファイルの使用は非常に単純で簡単です。「[構成](#configuration)」セクションを参照してください。 

## <a name="batch-transcription-api"></a>バッチ文字起こし API

バッチ文字起こし API では、次の形式がサポートされています。

| Format | コーデック | サンプルごとのビット数 | サンプル レート             |
|--------|-------|---------|---------------------------------|
| WAV    | PCM 0   | 16 ビット  | 8 kHz または 16 kHz、モノラルまたはステレオ |
| MP3    | PCM 0   | 16 ビット  | 8 kHz または 16 kHz、モノラルまたはステレオ |
| OGG    | OPUS  | 16 ビット  | 8 kHz または 16 kHz、モノラルまたはステレオ |

ステレオ オーディオ ストリームの場合、文字起こし中に左チャンネルと右チャンネルが分離されます。 JSON 結果ファイルが各チャネルに対して作成されます。
時間順の最終的なトランスクリプトを作成するには、発話ごとに生成されたタイムスタンプを使用します。

### <a name="configuration"></a>構成

構成パラメーターは JSON として提供されます。 

**1 つ以上の個別のファイルを文字起こししています。** 複数のファイルを文字起こしする場合は、1 つの要求で複数のファイルを送信することをお勧めします。 次の例では、3 つのファイルを使用しています。

```json
{
  "contentUrls": [
    "<URL to an audio file 1 to transcribe>",
    "<URL to an audio file 2 to transcribe>",
    "<URL to an audio file 3 to transcribe>"
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

**ストレージ コンテナー全体の処理。** コンテナー [SAS](../../storage/common/storage-sas-overview.md) には、`r` (読み取り) と `l` (一覧) のアクセス許可が含まれている必要があります。

```json
{
  "contentContainerUrl": "<SAS URL to the Azure blob container to transcribe>",
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of container using default model for en-US"
}
```

**バッチ文字起こしでカスタム トレーニング済みのモデルを使用します。** この例では、3 つのファイルを使用しています。

```json
{
  "contentUrls": [
    "<URL to an audio file 1 to transcribe>",
    "<URL to an audio file 2 to transcribe>",
    "<URL to an audio file 3 to transcribe>"
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/{id}"
  },
  "displayName": "Transcription of file using default model for en-US"
}
```


### <a name="configuration-properties"></a>構成プロパティ

次の省略可能なプロパティを使用して、文字起こしを構成します。

:::row:::
   :::column span="1":::
      **パラメーター**
   :::column-end:::
   :::column span="2":::
      **説明**
:::row-end:::
:::row:::
   :::column span="1":::
      `profanityFilterMode`
   :::column-end:::
   :::column span="2":::
      省略可能、既定値は `Masked` です。 認識結果内の不適切な表現をどう扱うかを指定します。 指定できる値は、`None` (不適切な表現のフィルターを無効にする)、`Masked` (不適切な表現をアスタリスクに置き換える)、`Removed` (すべての不適切な表現を結果から除去する)、または `Tags` ("profanity" (不適切な表現) のタグを追加する) です。
:::row-end:::
:::row:::
   :::column span="1":::
      `punctuationMode`
   :::column-end:::
   :::column span="2":::
      省略可能、既定値は `DictatedAndAutomatic` です。 認識結果内の句読点をどう扱うかを指定します。 指定できる値は、`None` (句読点を無効にする)、`Dictated` (明示的な (音声指示の) 句読点を暗黙指定する)、`Automatic` (デコーダーで句読点を処理する)、または `DictatedAndAutomatic` (口述指示および自動の句読点を使用する) です。
:::row-end:::
:::row:::
   :::column span="1":::
      `wordLevelTimestampsEnabled`
   :::column-end:::
   :::column span="2":::
      省略可能、既定値は `false` です。 単語レベルのタイムスタンプを出力に追加するかどうかを指定します。
:::row-end:::
:::row:::
   :::column span="1":::
      `diarizationEnabled`
   :::column-end:::
   :::column span="2":::
      省略可能、既定値は `false` です。 2 つの音声を含むモノラル チャネルであることが予測される入力に対してダイアライゼーション分析を実行する必要があることを指定します。 注:`wordLevelTimestampsEnabled` は `true` に設定する必要があります。
:::row-end:::
:::row:::
   :::column span="1":::
      `channels`
   :::column-end:::
   :::column span="2":::
      省略可能、既定では `0` と `1` が文字起こしされます。 処理するチャネル番号の配列。 ここでは、オーディオ ファイルで使用できるチャネルのサブセットを処理するように指定できます (例: `0` のみ)。
:::row-end:::
:::row:::
   :::column span="1":::
      `timeToLive`
   :::column-end:::
   :::column span="2":::
      省略可能、既定では削除は行われません。 文字起こしの完了後に、文字起こしを自動的に削除する期間。 `timeToLive` は、大量の文字起こしを最終的に確実に削除されるように処理するのに役立ちます (例: 12 時間の場合は `PT12H`)。
:::row-end:::
:::row:::
   :::column span="1":::
      `destinationContainerUrl`
   :::column-end:::
   :::column span="2":::
      Azure の書き込み可能なコンテナーに対する[アドホック SAS](../../storage/common/storage-sas-overview.md) のオプションの URL。 結果はこのコンテナーに格納されます。 保存されているアクセス ポリシーによる SAS は **サポートされていません**。 指定しない場合、Microsoft では、Microsoft が管理するストレージ コンテナーに結果を格納します。 [文字起こしの削除](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription)を呼び出して文字起こしを削除すると、結果データも削除されます。
:::row-end:::

### <a name="storage"></a>ストレージ

バッチ文字起こしでは、公開されているインターネット URI からオーディオを読み取ることができます、また、[Azure Blob Storage](../../storage/blobs/storage-blobs-overview.md) で SAS URI を使用してオーディオを読み取ったり、文字起こしを書き込んだりできます。

## <a name="batch-transcription-result"></a>バッチ文字起こしの結果

オーディオ入力ごとに、1 つの文字起こし結果ファイルが作成されます。
[文字起こしファイルの取得](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptionFiles)操作から、この文字起こしの結果ファイルの一覧が返されます。 特定の入力ファイルの文字起こしファイルを見つけるには、`kind` == `Transcription` および `name` == `{originalInputName.suffix}.json` を使用して、返されたすべてのファイルをフィルター処理します。

各文字起こし結果ファイルの形式は次のとおりです。

```json
{
  "source": "...",                      // sas url of a given contentUrl or the path relative to the root of a given container
  "timestamp": "2020-06-16T09:30:21Z",  // creation time of the transcription, ISO 8601 encoded timestamp, combined date and time
  "durationInTicks": 41200000,          // total audio duration in ticks (1 tick is 100 nanoseconds)
  "duration": "PT4.12S",                // total audio duration, ISO 8601 encoded duration
  "combinedRecognizedPhrases": [        // concatenated results for simple access in single string for each channel
    {
      "channel": 0,                     // channel number of the concatenated results
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                // results for each phrase and each channel individually
    {
      "recognitionStatus": "Success",   // recognition state, e.g. "Success", "Failure"          
      "speaker": 1,                     // if `diarizationEnabled` is `true`, this is the identified speaker (1 or 2), otherwise this property is not present
      "channel": 0,                     // channel number of the result
      "offset": "PT0.07S",              // offset in audio of this phrase, ISO 8601 encoded duration 
      "duration": "PT1.59S",            // audio duration of this phrase, ISO 8601 encoded duration
      "offsetInTicks": 700000.0,        // offset in audio of this phrase in ticks (1 tick is 100 nanoseconds)
      "durationInTicks": 15900000.0,    // audio duration of this phrase in ticks (1 tick is 100 nanoseconds)

      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,    // confidence value for the recognition of the whole phrase
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",

          // if wordLevelTimestampsEnabled is `true`, there will be a result for each word of the phrase, otherwise this property is not present
          "words": [
            {
              "word": "hello",
              "offset": "PT0.09S",
              "duration": "PT0.48S",
              "offsetInTicks": 900000.0,
              "durationInTicks": 4800000.0,
              "confidence": 0.987572
            },
            {
              "word": "world",
              "offset": "PT0.59S",
              "duration": "PT0.16S",
              "offsetInTicks": 5900000.0,
              "durationInTicks": 1600000.0,
              "confidence": 0.906032
            }
          ]
        }
      ]
    }
  ]
}
```

結果には次のフィールドが含まれます。

:::row:::
   :::column span="1":::
      **フィールド**
   :::column-end:::
   :::column span="2":::
      **コンテンツ**
:::row-end:::
:::row:::
   :::column span="1":::
      `lexical`
   :::column-end:::
   :::column span="2":::
      実際に認識された単語。
:::row-end:::
:::row:::
   :::column span="1":::
      `itn`
   :::column-end:::
   :::column span="2":::
      認識されたテキストの逆テキスト正規化形式。 略語 ("doctor smith" から "dr smith")、電話番号、およびその他の変換が適用されます。
:::row-end:::
:::row:::
   :::column span="1":::
      `maskedITN`
   :::column-end:::
   :::column span="2":::
      不適切表現のマスキングを適用した ITN 形式。
:::row-end:::
:::row:::
   :::column span="1":::
      `display`
   :::column-end:::
   :::column span="2":::
      認識されたテキストの表示形式。 追加された句読点と大文字化が含まれます。
:::row-end:::

## <a name="speaker-separation-diarization"></a>話者の分離 (ダイアライゼーション)

ダイアライゼーションは、音声に含まれる話者を分離するプロセスです。 ダイアライゼーションはバッチ パイプラインによってサポートされ、モノラル チャンネル レコーディングの 2 人の話者を認識できます。 この機能は、ステレオ録音では使用できません。

ダイアライゼーションを有効にした文字起こしの出力には、文字起こしされたフレーズごとに `Speaker` エントリが含まれます。 ダイアライゼーションが使用されない場合、`Speaker` プロパティは JSON 出力に存在しません。 ダイアライゼーションでは 2 つの音声がサポートされるため、話者は `1` または `2` として識別されます。

ダイアライゼーションを要求するには、次に示す HTTP 要求のように、`diarizationEnabled` プロパティを追加して `true` に設定します。

 ```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "diarizationEnabled": true,
    "wordLevelTimestampsEnabled": true,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

上記の要求のパラメーターが示すように、ワードレベルのタイムスタンプを有効にする必要があります。

## <a name="best-practices"></a>ベスト プラクティス

バッチ文字起こしサービスは、送信された多数の文字起こしを処理できます。 [文字起こしの取得](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptions)を実行して、文字起こしの状態を照会できます。
結果を取得した後は、サービスから定期的に[文字起こしの削除](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription)を呼び出します。 または、`timeToLive` プロパティを設定して、確実に結果が最終的に削除されるようにします。

## <a name="sample-code"></a>サンプル コード

完全なサンプルは、[GitHub サンプル リポジトリ](https://aka.ms/csspeech/samples)の `samples/batch` サブディレクトリにあります。

カスタム モデルを使用している場合は、サブスクリプション情報、サービス リージョン、文字起こしするオーディオ ファイルを指す URI、モデルの場所を使用してサンプル コードを更新します。

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptiondefinition)]

サンプル コードでは、クライアントが設定されて、文字起こし要求が送信されます。 その後、状態情報がポーリングされて、文字起こしの進行状況に関する詳細が表示されます。

```csharp
// get the status of our transcriptions periodically and log results
int completed = 0, running = 0, notStarted = 0;
while (completed < 1)
{
    completed = 0; running = 0; notStarted = 0;

    // get all transcriptions for the user
    paginatedTranscriptions = null;
    do
    {
        // <transcriptionstatus>
        if (paginatedTranscriptions == null)
        {
            paginatedTranscriptions = await client.GetTranscriptionsAsync().ConfigureAwait(false);
        }
        else
        {
            paginatedTranscriptions = await client.GetTranscriptionsAsync(paginatedTranscriptions.NextLink).ConfigureAwait(false);
        }

        // delete all pre-existing completed transcriptions. If transcriptions are still running or not started, they will not be deleted
        foreach (var transcription in paginatedTranscriptions.Values)
        {
            switch (transcription.Status)
            {
                case "Failed":
                case "Succeeded":
                    // we check to see if it was one of the transcriptions we created from this client.
                    if (!createdTranscriptions.Contains(transcription.Self))
                    {
                        // not created form here, continue
                        continue;
                    }

                    completed++;

                    // if the transcription was successful, check the results
                    if (transcription.Status == "Succeeded")
                    {
                        var paginatedfiles = await client.GetTranscriptionFilesAsync(transcription.Links.Files).ConfigureAwait(false);

                        var resultFile = paginatedfiles.Values.FirstOrDefault(f => f.Kind == ArtifactKind.Transcription);
                        var result = await client.GetTranscriptionResultAsync(new Uri(resultFile.Links.ContentUrl)).ConfigureAwait(false);
                        Console.WriteLine("Transcription succeeded. Results: ");
                        Console.WriteLine(JsonConvert.SerializeObject(result, SpeechJsonContractResolver.WriterSettings));
                    }
                    else
                    {
                        Console.WriteLine("Transcription failed. Status: {0}", transcription.Properties.Error.Message);
                    }

                    break;

                case "Running":
                    running++;
                    break;

                case "NotStarted":
                    notStarted++;
                    break;
            }
        }

        // for each transcription in the list we check the status
        Console.WriteLine(string.Format("Transcriptions status: {0} completed, {1} running, {2} not started yet", completed, running, notStarted));
    }
    while (paginatedTranscriptions.NextLink != null);

    // </transcriptionstatus>
    // check again after 1 minute
    await Task.Delay(TimeSpan.FromMinutes(1)).ConfigureAwait(false);
}
```

前の呼び出しに関する詳細については、[Swagger ドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)を参照してください。 ここに示すすべてのサンプルについては、[GitHub](https://aka.ms/csspeech/samples) の `samples/batch` サブディレクトリにアクセスしてください。

このサンプルでは、非同期セットアップの使用により、オーディオをポストして文字起こしの状態を受け取っています。
`PostTranscriptions` メソッドによってオーディオ ファイルの詳細を送信し、`GetTranscriptions` メソッドによって状態を受け取ります。
`PostTranscriptions` はハンドルを返し、`GetTranscriptions` はそのハンドルを使用して文字起こしの状態を取得するためのハンドルを作成します。

このサンプル コードでは、カスタム モデルは指定されていません。 このサービスでは、ファイルの文字起こしにベースライン モデルを使用します。 モデルを指定するには、同じメソッドにカスタム モデルのモデル参照を渡すことができます。

> [!NOTE]
> ベースライン文字起こしの場合、ベースライン モデルの ID を宣言する必要はありません。

## <a name="next-steps"></a>次のステップ

- [Speech to Text v3 API リファレンス](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription)
