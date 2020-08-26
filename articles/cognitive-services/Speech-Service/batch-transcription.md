---
title: バッチ文字起こしとは - Speech サービス
titleSuffix: Azure Cognitive Services
description: バッチ文字起こしは、Azure BLOB などのストレージにある大量の音声を文字起こしする場合に理想的です。 専用の REST API を使用すると、Shared Access Signatures (SAS) URI でオーディオ ファイルを示して、非同期に文字起こしを受け取ることができます。
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: wolfma
ms.openlocfilehash: 70977c30edce124aa0d39bcc57d4ccd015d65961
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214059"
---
# <a name="what-is-batch-transcription"></a>バッチ文字起こしとは

バッチ文字起こしは、ストレージ内の大量のオーディオを文字起こしできる一連の REST API 操作です。 Shared Access Signatures (SAS) URI でオーディオ ファイルを示して、非同期に文字起こしの結果を受け取ることができます。 新しい v3.0 API では、1 つ以上のオーディオ ファイルを文字起こしするか、ストレージ コンテナー全体を処理するかを選択できます。

音声からテキストへの非同期の文字起こしは、その機能の 1 つにすぎません。 バッチ文字起こし REST API を使用すると、次のメソッドを呼び出すことができます。



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

バッチ文字起こしジョブは、ベスト エフォート ベースでスケジュールされます。 現時点では、ジョブがいつ実行状態になるかについて予測できません。 通常のシステム負荷では、この処理は数分以内に行われます。 いったん実行状態になると、実際の文字起こしはリアルタイムのオーディオより速く処理されます。

使用しやすい API のほかに、カスタム エンドポイントをデプロイする必要はなく、監視する必要のある同時実行要件もありません。

## <a name="prerequisites"></a>前提条件

### <a name="subscription-key"></a>サブスクリプション キー

Speech Service の他の機能と同様に、[使用開始ガイド](get-started.md)に従って [Azure portal](https://portal.azure.com) でサブスクリプション キーを作成します。

>[!NOTE]
> バッチ文字起こしを使用するには、音声サービスの Standard サブスクリプション (S0) が必要です。 Free サブスクリプション キー (F0) は機能しません。 詳細については、[価格と制限](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)に関するページを参照してください。

### <a name="custom-models"></a>カスタム モデル

モデルをカスタマイズする予定がある場合は、[音響のカスタマイズ](how-to-customize-acoustic-models.md)と[言語のカスタマイズ](how-to-customize-language-model.md)の手順に従ってください。 作成されたモデルをバッチ文字起こしで使用するには、モデルの場所が必要です。 モデルの詳細 (`self` プロパティ) を調べると、モデルの場所を取得できます。 デプロイされたカスタム エンドポイントは、バッチ文字起こしサービスには "*必要ありません*"。

## <a name="the-batch-transcription-api"></a>Batch 文字起こし API

### <a name="supported-formats"></a>サポートされるフォーマット

Batch 文字起こし API では、次の形式がサポートされています。

| Format | コーデック | サンプルごとのビット数 | サンプル レート             |
|--------|-------|---------|---------------------------------|
| WAV    | PCM 0   | 16 ビット  | 8 kHz または 16 kHz、モノラルまたはステレオ |
| MP3    | PCM 0   | 16 ビット  | 8 kHz または 16 kHz、モノラルまたはステレオ |
| OGG    | OPUS  | 16 ビット  | 8 kHz または 16 kHz、モノラルまたはステレオ |

ステレオ オーディオ ストリームの場合、文字起こし中に左チャンネルと右チャンネルが分離されます。 各チャネルに対して、JSON 結果ファイルが作成されます。 発話ごとに生成されるタイムスタンプにより、開発者は時間順の最終文字起こしを作成できます。

### <a name="configuration"></a>構成

構成パラメーターは JSON (1 つ以上の個別ファイル) として指定されています。

```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

構成パラメーターは JSON (ストレージ コンテナー全体の処理) として指定されています。

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

カスタム トレーニング済みのモデルをバッチ文字起こしで使用するには、次のように参照できます。

```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
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
      認識結果内の不適切な表現をどう扱うかを指定します。 指定できる値は、`None` (不適切な表現のフィルターを無効にする)、`Masked` (不適切な表現をアスタリスクに置き換える)、`Removed` (すべての不適切な表現を結果から除去する)、または `Tags` ("profanity" (不適切な表現) のタグを追加する) です。 既定の設定は `Masked` です。
:::row-end:::
:::row:::
   :::column span="1":::
      `punctuationMode`
   :::column-end:::
   :::column span="2":::
      認識結果内の句読点をどう扱うかを指定します。 指定できる値は、`None` (句読点を無効にする)、`Dictated` (明示的な (音声指示の) 句読点を暗黙指定する)、`Automatic` (デコーダーで句読点を処理する)、または `DictatedAndAutomatic` (口述指示および自動の句読点を使用する) です。 既定の設定は `DictatedAndAutomatic` です。
:::row-end:::
:::row:::
   :::column span="1":::
      `wordLevelTimestampsEnabled`
   :::column-end:::
   :::column span="2":::
      単語レベルのタイムスタンプを出力に追加するかどうかを指定します。 指定できる値は、単語レベルのタイムスタンプを有効にする `true` と、それを無効にする `false` (既定値) です。
:::row-end:::
:::row:::
   :::column span="1":::
      `diarizationEnabled`
   :::column-end:::
   :::column span="2":::
      2 つの音声を含むモノラル チャネルであることが予測される入力に対してダイアライゼーション分析を実行する必要があることを指定します。 指定できる値は、ダイアライゼーションを有効にする `true` と、それを無効にする `false` (既定値) です。 さらに、`wordLevelTimestampsEnabled` を true に設定する必要があります。
:::row-end:::
:::row:::
   :::column span="1":::
      `channels`
   :::column-end:::
   :::column span="2":::
      処理するチャネル番号の配列 (省略可能)。 ここでは、オーディオ ファイルで使用できるチャネルのサブセットを処理するように指定できます (例: `0` のみ)。 指定しない場合、既定としてチャネル `0` と `1` が文字起こしされます。
:::row-end:::
:::row:::
   :::column span="1":::
      `timeToLive`
   :::column-end:::
   :::column span="2":::
      文字起こしの完了後に、文字起こしを自動的に削除する期間 (省略可能)。 `timeToLive` は、最終的に確実に削除される文字起こしの大量処理に役立ちます (例: `PT12H`)。 指定しない場合、または `PT0H` に設定した場合、文字起こしは自動的に削除されません。
:::row-end:::
:::row:::
   :::column span="1":::
      `destinationContainerUrl`
   :::column-end:::
   :::column span="2":::
      Azure の書き込み可能なコンテナーに対する[サービス SAS](../../storage/common/storage-sas-overview.md) のオプションの URL。 結果はこのコンテナーに格納されます。 指定しない場合、Microsoft では、Microsoft が管理するストレージ コンテナーに結果を格納します。 [文字起こしの削除](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription)を呼び出して文字起こしを削除すると、結果データも削除されます。
:::row-end:::

### <a name="storage"></a>ストレージ

Batch 文字起こしでは、オーディオの読み取りや、文字起こしのストレージへの書き込みに [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) をサポートしています。

## <a name="the-batch-transcription-result"></a>バッチ文字起こしの結果

入力オーディオごとに、1 つの文字起こし結果ファイルが作成されます。 結果ファイルの一覧を取得するには、[文字起こしファイルの取得](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptionFiles)を呼び出します。 このメソッドから、この文字起こしの結果ファイルの一覧が返されます。 特定の入力ファイルの文字起こしファイルを見つけるには、`kind` == `Transcription` および `name` == `{originalInputName.suffix}.json` を使用して、返されたすべてのファイルをフィルター処理します。

各文字起こし結果ファイルの形式は次のとおりです。

```json
{
  "source": "...",                                                 // the sas url of a given contentUrl or the path relative to the root of a given container
  "timestamp": "2020-06-16T09:30:21Z",                             // creation time of the transcription, ISO 8601 encoded timestamp, combined date and time
  "durationInTicks": 41200000,                                     // total audio duration in ticks (1 tick is 100 nanoseconds)
  "duration": "PT4.12S",                                           // total audio duration, ISO 8601 encoded duration
  "combinedRecognizedPhrases": [                                   // concatenated results for simple access in single string for each channel
    {
      "channel": 0,                                                // channel number of the concatenated results
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                                           // results for each phrase and each channel individually
    {
      "recognitionStatus": "Success",                              // recognition state, e.g. "Success", "Failure"
      "channel": 0,                                                // channel number of the result
      "offset": "PT0.07S",                                         // offset in audio of this phrase, ISO 8601 encoded duration 
      "duration": "PT1.59S",                                       // audio duration of this phrase, ISO 8601 encoded duration
      "offsetInTicks": 700000.0,                                   // offset in audio of this phrase in ticks (1 tick is 100 nanoseconds)
      "durationInTicks": 15900000.0,                               // audio duration of this phrase in ticks (1 tick is 100 nanoseconds)
      
      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,                               // confidence value for the recognition of the whole phrase
          "speaker": 1,                                            // if `diarizationEnabled` is `true`, this is the identified speaker (1 or 2), otherwise this property is not present
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

結果には、次の形式が含まれます。

:::row:::
   :::column span="1":::
      **形式**
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

ダイアライゼーションを有効にした文字起こしの出力には、文字起こしされたフレーズごとに `Speaker` エントリが含まれます。 ダイアライゼーションが使用されない場合、プロパティ `Speaker` は JSON 出力に存在しません。 ダイアライゼーションでは 2 つの音声がサポートされるため、話者は `1` または `2` として識別されます。

ダイアライゼーションを要求するには、次に示すように、HTTP 要求に関連するパラメーターを追加する必要があります。

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

文字起こしサービスは、送信された多数の文字起こしを処理できます。 [文字起こしの取得](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptions)に対して `GET` を使用して、文字起こしの状態のクエリを実行できます。 結果を取得した後は、サービスから定期的に[文字起こしの削除](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription)を呼び出します。 または、`timeToLive` プロパティを適切な値に設定して、確実に結果が最終的に削除されるようにします。

## <a name="sample-code"></a>サンプル コード

完全なサンプルは、[GitHub サンプル リポジトリ](https://aka.ms/csspeech/samples)の `samples/batch` サブディレクトリにあります。

カスタム モデルを使用する場合は、サブスクリプション情報、サービス リージョン、文字起こしするオーディオ ファイルを指す SAS URI、モデルの場所を使用してサンプル コードを更新してください。

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptiondefinition)]

サンプル コードでは、クライアントが設定されて、文字起こし要求が送信されます。 その後、状態情報がポーリングされて、文字起こしの進行状況に関する詳細が表示されます。

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptionstatus)]

前の呼び出しに関する詳細については、[Swagger ドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)を参照してください。 ここに示すすべてのサンプルについては、[GitHub](https://aka.ms/csspeech/samples) の `samples/batch` サブディレクトリにアクセスしてください。

オーディオを渡して文字起こしの状態を受け取る非同期セットアップに注意してください。 作成されるクライアントは .NET HTTP クライアントです。 オーディオ ファイルの詳細を送信する `PostTranscriptions` メソッドと、状態を受け取る `GetTranscriptions` メソッドがあります。 `PostTranscriptions` はハンドルを返し、`GetTranscriptions` はそのハンドルを使用して文字起こしの状態を取得するためのハンドルを作成します。

現在のサンプル コードでは、カスタム モデルは指定されていません。 このサービスでは、ファイルの文字起こしにベースライン モデルを使用します。 モデルを指定するには、同じメソッドにカスタム モデルのモデル参照を渡すことができます。

> [!NOTE]
> ベースライン文字起こしの場合、ベースライン モデルの ID を宣言する必要はありません。

## <a name="download-the-sample"></a>サンプルのダウンロード

サンプルについては、[GitHub のサンプル リポジトリ](https://aka.ms/csspeech/samples)の `samples/batch` ディレクトリをご覧ください。

## <a name="next-steps"></a>次のステップ

- [Speech to Text v3 API リファレンス](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription)
