---
title: バッチ文字起こしの使用方法 - Speech Services
titlesuffix: Azure Cognitive Services
description: バッチ文字起こしは、Azure BLOB などのストレージにある大量の音声を文字起こしする場合に理想的です。 専用の REST API を使用すると、Shared Access Signatures (SAS) URI でオーディオ ファイルを示して、非同期に文字起こしを受け取ることができます。
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: b71400c3ae3c1cc6737d9194b4d94bf0b9c7efa9
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606743"
---
# <a name="why-use-batch-transcription"></a>Batch 文字起こしを使用する理由

バッチ文字起こしは、Azure BLOB などのストレージにある大量の音声を文字起こしする場合に理想的です。 専用の REST API を使用すると、Shared Access Signatures (SAS) URI でオーディオ ファイルを示して、非同期に文字起こしを受け取ることができます。

## <a name="prerequisites"></a>前提条件

### <a name="subscription-key"></a>サブスクリプション キー

Speech Service の他の機能と同様に、[使用開始ガイド](get-started.md)に従って [Azure portal](https://portal.azure.com) でサブスクリプション キーを作成します。 ベースライン モデルから文字起こしを取得する場合は、行う必要があるのはキーを作成することだけです。

>[!NOTE]
> バッチ文字起こしを使用するには、Speech Services の Standard サブスクリプション (S0) が必要です。 Free サブスクリプション キー (F0) は機能しません。 詳細については、[価格と制限](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)に関するページを参照してください。

### <a name="custom-models"></a>カスタム モデル

音響モデルまたは言語モデルをカスタマイズする場合は、[音響モデルのカスタマイズ](how-to-customize-acoustic-models.md)および[言語モデルのカスタマイズ](how-to-customize-language-model.md)に関する記事をご覧ください。 作成されたモデルをバッチ文字起こしで使用するには、モデル ID が必要です。 この ID は [エンドポイントの詳細] ビューに表示されるエンドポイント ID ではなく、そのモデルの詳細を選択して取得できるモデル ID です。

## <a name="the-batch-transcription-api"></a>Batch 文字起こし API

Batch 文字起こし API では、音声からテキストへの非同期文字起こしと他の機能が提供されます。 次のためのメソッドを公開する REST API です。

1. バッチ処理要求の作成
1. クエリの状態
1. 文字起こしのダウンロード

> [!NOTE]
> Batch 文字起こし API は、何千時間ものオーディオがたまるのが普通なコール センターに理想的です。 大量のオーディオ録音の文字起こしが簡単になります。

### <a name="supported-formats"></a>サポートされるフォーマット

Batch 文字起こし API では、次の形式がサポートされています。

| 形式 | コーデック | Bitrate | サンプル レート |
|--------|-------|---------|-------------|
| WAV | PCM 0 | 16 ビット | 8 または 16 kHz、モノラル、ステレオ |
| MP3 | PCM 0 | 16 ビット | 8 または 16 kHz、モノラル、ステレオ |
| OGG | OPUS | 16 ビット | 8 または 16 kHz、モノラル、ステレオ |

ステレオ オーディオ ストリームの場合、Batch 文字起こし API は文字起こしの間に左チャンネルと右チャンネルを分離します。 各チャンネルから 2 つの JSON ファイルが作成されます。 発話に従うタイムスタンプにより、開発者は時間順の最終文字起こしを作成できます。 このサンプルの要求には、不適切な表現のフィルター、句読点、および単語レベルのタイムスタンプのプロパティが含まれます。

### <a name="configuration"></a>構成

構成パラメーターは JSON として提供されます。

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to us, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True",
    "AddSentiment" : "True"
  }
}
```

> [!NOTE]
> Batch 文字起こし API は REST サービスを使用して、文字起こし、その状態、および関連する結果を要求します。 任意の言語からこの API を使用できます。 次のセクションでは、API の使用方法について説明します。

### <a name="configuration-properties"></a>構成プロパティ

次の省略可能なプロパティを使用して、文字起こしを構成します。

| パラメーター | 説明 |
|-----------|-------------|
| `ProfanityFilterMode` | 認識結果内の不適切な表現をどう扱うかを指定します。 指定できる値は、`none` (不適切な表現のフィルターを無効にする)、`masked` (不適切な表現をアスタリスクに置き換える)、`removed` (すべての不適切な表現を結果から除去する) または `tags` ("不適切な表現" のタグを追加する) です。 既定の設定は `masked`です。 |
| `PunctuationMode` | 認識結果内の句読点をどう扱うかを指定します。 指定できる値は、`none` (句読点を無効にする)、`dictated` (明示的な句読点)、`automatic` (デコーダーで句読点を処理する)、`dictatedandautomatic` (指定された句読点、または自動) です。 |
 | `AddWordLevelTimestamps` | 単語レベルのタイムスタンプを出力に追加するかどうかを指定します。 `true` を指定すると単語レベルのタイムスタンプが有効になり、`false` (既定値) を指定すると無効になります。 |
 | `AddSentiment` | センチメントを発話に追加するかどうかを指定します。 `true` を指定すると発話ごとのセンチメントが有効になり、`false` (既定値) を指定すると無効になります。 |
 | `AddDiarization` | 2 つの音声が含まれているモノラル チャンネルであることが想定される入力に対して、ダイアライゼーション分析を実行するように指定します。 指定できる値は、ダイアライゼーションを有効にする `true` と、それを無効にする `false` (既定値) です。 さらに、`AddWordLevelTimestamps` を true に設定する必要があります。|

### <a name="storage"></a>Storage

Batch 文字起こしでは、オーディオの読み取りや、文字起こしのストレージへの書き込みに [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) をサポートしています。

## <a name="webhooks"></a>webhooks

文字起こし状態のポーリングでは、最も高いパフォーマンスを実現できない場合や、最適なユーザー エクスペリエンスを提供できない場合があります。 状態をポーリングするには、コールバックを登録して、実行時間の長い文字起こしタスクが完了したときにクライアントに通知することができます。

詳細については、「[Webhook](webhooks.md)」を参照してください。

## <a name="speaker-separation-diarization"></a>話者の分離 (ダイアライゼーション)

ダイアライゼーションは、音声に含まれる話者を分離するプロセスです。 ダイアライゼーションはバッチ パイプラインによってサポートされ、モノラル チャンネル レコーディングの 2 人の話者を認識できます。

音声文字起こし要求でダイアライゼーションを処理するように要求するには、次に示すように、HTTP 要求の中に関連するパラメーターを追加します。

 ```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to us, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "AddWordLevelTimestamps" : "True",
    "AddDiarization" : "True"
  }
}
```

単語レベルのタイムスタンプも、上の要求に示されているように、パラメーターとして "有効にする" 必要があります。

対応する音声には、番号で識別された話者 (現時点では 2 つの音声のみがサポートされているため、話者は 'Speaker 1' と 'Speaker 2' として識別されます) と、文字起こしの出力が含まれます。

ステレオ レコーディングではダイアライゼーションは使用できないことにも注意してください。 さらに、すべての JSON 出力には、話者のタグが含まれます。 ダイアライゼーションが使用されていない場合、JSON 出力には 'Speaker:Null' と表示されます。

> [!NOTE]
> すべてのリージョンとすべてのロケールでダイアライゼーションを利用できます。

## <a name="sentiment"></a>センチメント

センチメントは Batch 文字起こし API の新機能であり、コール センター ドメインの重要な機能です。 お客様は自身の要求に対して `AddSentiment` パラメーターを使用して次のことを行えます。

1.  顧客満足度に関する分析情報を得る
2.  エージェント (通話を受けるチーム) のパフォーマンスに関する分析情報を得る
3.  通話がネガティブな方向に向かったときの正確なポイントインタイムをつかむ
4.  ネガティブ通話がポジティブになったときに何が良かったのか特定する
5.  製品やサービスについて、お客様が何を気に入り、何を好まないかを特定する

センチメントは、オーディオのセグメントが発話 (オフセット) の開始とバイト ストリームの末尾の無音検出の間の時間経過として定義されているオーディオ セグメント単位でスコア付けされます。 そのセグメント内のテキスト全体が、センチメントの計算に使用されます。 各チャネルの通話全体または音声全体の集計センチメント値は計算されません。 これらの集計は、さらに適用するためにドメインの所有者に残されます。

センチメントは、語彙形式で適用されます。

JSON の出力サンプルは、次のようになります。

```json
{
  "AudioFileResults": [
    {
      "AudioFileName": "Channel.0.wav",
      "AudioFileUrl": null,
      "SegmentResults": [
        {
          "RecognitionStatus": "Success",
          "ChannelNumber": null,
          "Offset": 400000,
          "Duration": 13300000,
          "NBest": [
            {
              "Confidence": 0.976174,
              "Lexical": "what's the weather like",
              "ITN": "what's the weather like",
              "MaskedITN": "what's the weather like",
              "Display": "What's the weather like?",
              "Words": null,
              "Sentiment": {
                "Negative": 0.206194,
                "Neutral": 0.793785,
                "Positive": 0.0
              }
            }
          ]
        }
      ]
    }
  ]
}
```
この機能では、現在ベータ版であるセンチメント モデルが使用されます。

## <a name="sample-code"></a>サンプル コード

完全なサンプルは、[GitHub サンプル リポジトリ](https://aka.ms/csspeech/samples)の `samples/batch` サブディレクトリにあります。

自分のサブスクリプション情報、サービス リージョン、文字起こしするオーディオ ファイルをポイントする SAS URI、カスタムの音響モデルまたは言語モデルを使用する場合のモデル ID で、サンプル コードをカスタマイズする必要があります。

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

サンプル コードでは、クライアントが設定されて、文字起こし要求が送信されます。 その後、状態情報がポーリングされて、文字起こしの進行状況に関する詳細が表示されます。

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

前の呼び出しに関する詳細については、[Swagger ドキュメント](https://westus.cris.ai/swagger/ui/index)を参照してください。 ここに示すすべてのサンプルについては、[GitHub](https://aka.ms/csspeech/samples) の `samples/batch` サブディレクトリにアクセスしてください。

オーディオを渡して文字起こしの状態を受け取る非同期セットアップに注意してください。 作成されるクライアントは .NET HTTP クライアントです。 `PostTranscriptions` メソッドはオーディオ ファイルの詳細を送信し、`GetTranscriptions` メソッドは結果を受け取ります。 `PostTranscriptions` はハンドルを返し、`GetTranscriptions` はそのハンドルを使用して文字起こしの状態を取得するためのハンドルを作成します。

現在のサンプル コードでは、カスタム モデルは指定されていません。 サービスは、ベースライン モデルをファイルの文字起こしに使用します。 モデルを指定するために、音響モデルと言語モデルのモデル ID と同じメソッドに渡すことができます。

> [!NOTE]
> ベースライン文字起こしの場合は、ベースライン モデルの ID を宣言する必要はありません。 言語モデル ID だけを指定すると (音響モデル ID を指定しない)、一致する音響モデルが自動的に選択されます。 音響モデル ID だけを指定すると、一致する言語モデルが自動的に選択されます。

## <a name="download-the-sample"></a>サンプルのダウンロード

サンプルについては、[GitHub のサンプル リポジトリ](https://aka.ms/csspeech/samples)の `samples/batch` ディレクトリをご覧ください。

> [!NOTE]
> バッチ文字起こしジョブはベスト エフォートでスケジュール設定され、ジョブが実行中状態になるときの推定時刻はありません。 いったん実行中状態になると、実際の文字起こしはリアルタイムのオーディオより速く処理されます。

## <a name="next-steps"></a>次の手順

* [Speech 試用版サブスクリプションを取得する](https://azure.microsoft.com/try/cognitive-services/)
