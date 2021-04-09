---
title: v2 から v3 REST API への移行 - 音声サービス
titleSuffix: Azure Cognitive Services
description: このドキュメントでは、開発者が音声サービスの Speech to Text REST API で v2 から v3 にコードを移行する方法について説明します。
services: cognitive-services
author: bexxx
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: rbeckers
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c8016b566db8be1b7f5c5ddb8d92123d6673db5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98569846"
---
# <a name="migrate-code-from-v20-to-v30-of-the-rest-api"></a>REST API の v2.0 から v3.0 にコードを移行する

音声テキスト変換用の音声サービス REST API の v3 バージョンでは、v2 と比較して、より信頼性が高く、使いやすくなり、同様のサービスの API との一貫性が向上しています。 ほとんどのチームでは v2 から v3 に 1 日か 2 日で移行できます。

## <a name="forward-compatibility"></a>上位互換性

v2 のすべてのエンティティは、同じ ID で v3 API にも存在します。 結果のスキーマが変更された場合 (文字起こしなど) は、v3 バージョンの API で GET を実行すると v3 スキーマが使用されます。 v2 バージョンの API で実行した GET の結果では、同じ v2 スキーマが使用されます。 v3 で新しく作成されたエンティティは、v2 API からの応答では使用 **できません**。  

## <a name="migration-steps"></a>移行の手順

これは、移行を準備するときに注意が必要な項目をまとめた一覧です。 詳細については、個々のリンクを参照してください。 現在使用している API によっては、ここに記載されているすべての手順が当てはまるとは限りません。 呼び出しコードに大きな変更が必要な変更点は少ししかありません。 ほとんどの変更点では、項目名の変更が必要とされるにすぎません。 

一般的な変更: 

1. [ホスト名を変更する](#host-name-changes)

1. [クライアント コードのプロパティ ID の名前を self に変更する](#identity-of-an-entity) 

1. [エンティティのコレクションを反復処理するようにコードを変更する](#working-with-collections-of-entities)

1. [クライアント コードのプロパティの名前を displayName に変更する](#name-of-an-entity)

1. [参照されるエンティティのメタデータの取得を調整する](#accessing-referenced-entities)

1. バッチ文字起こしを使用する場合: 

    * [バッチ文字起こしを作成するためにコードを調整する](#creating-transcriptions) 

    * [新しい文字起こし結果スキーマに合わせてコードを調整する](#format-of-v3-transcription-results)

    * [結果の取得方法に合わせてコードを調整する](#getting-the-content-of-entities-and-the-results)

1. カスタム モデルのトレーニングまたはテスト API を使用する場合: 

    * [カスタム モデル トレーニングに変更を適用する](#customizing-models)

    * [基本モデルとカスタム モデルの取得方法を変更する](#retrieving-base-and-custom-models)

    * [クライアント コードに含まれるパス セグメントの accuracytests の名前を evaluations に変更する](#accuracy-tests)

1. エンドポイント API を使用する場合:

    * [エンドポイント ログの取得方法を変更する](#retrieving-endpoint-logs)

1. その他の軽微な変更: 

    * [すべてのカスタム プロパティを POST 要求のプロパティではなく customProperties として渡す](#using-custom-properties)

    * [Operation-Location ではなく応答ヘッダーの Location から場所を読み取る](#response-headers)

## <a name="breaking-changes"></a>互換性に影響する変更

### <a name="host-name-changes"></a>ホスト名の変更

エンドポイントのホスト名が `{region}.cris.ai` から `{region}.api.cognitive.microsoft.com` に変更されました。 新しいエンドポイントへのパスには `api/` が含まれなくなりました (ホスト名に含まれているため)。 [Swagger ドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)に、有効なリージョンとパスが記載されています。
>[!IMPORTANT]
>ホスト名を `{region}.cris.ai` から `{region}.api.cognitive.microsoft.com` に変更します。ここでのリージョンは、音声サブスクリプションのリージョンです。 また、クライアント コード内の任意のパスから `api/` を削除します。

### <a name="identity-of-an-entity"></a>エンティティの ID

プロパティ `id` は、`self` に変わりました。 v2 の API のユーザーは、API のパスが作成される方法を知る必要がありました。 これは拡張性に欠け、ユーザーに不必要な手間をかけるものでした。 プロパティ `id` (uuid) は `self` (string) に置き換えられました。これはエンティティの場所 (URL) です。 値は、引き続きすべてのエンティティ間で一意です。 `id` がコードに文字列として格納されている場合は、名前を変更すれば新しいスキーマをサポートできます。 `self` の内容は、エンティティの `GET`、`PATCH`、`DELETE` REST 呼び出しの URL として使用できるようになりました。

エンティティに他のパスを通じて使用できる追加機能がある場合は、`links` の下に一覧表示されます。 次の文字起こしの例は、文字起こしの内容の `GET` を行う別個のメソッドを示しています。
>[!IMPORTANT]
>クライアント コード内のプロパティの名前を `id` から `self` に変更します。 必要に応じて、`uuid` 型から `string` 型に変更します。 

**v2 の文字起こし:**

```json
{
    "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US",
    "name": "Transcription using locale en-US"
}
```

**v3 の文字起こし:**

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US", 
    "displayName": "Transcription using locale en-US",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    }
}
```

コードの実装によっては、プロパティの名前を変更するだけでは不十分な場合があります。 返される `self` と `links` の値を REST 呼び出しのターゲット URL として使用し、クライアントでパスを生成しないようお勧めします。 返された URL を使用すれば、パスの将来の変更によってクライアント コードに破壊的な影響が及ばないようにすることができます。

### <a name="working-with-collections-of-entities"></a>エンティティのコレクションの操作

以前、v2 API では、使用可能なすべてのエンティティが 1 つの結果として返されていました。 v3 では、想定される応答サイズをより細かく制御できるようにするため、すべてのコレクションの結果がページ分割されます。 返されるエンティティの数とページの開始オフセットを制御できます。 この動作により、応答プロセッサの実行時間を簡単に予測できます。

応答の基本的な形状は、すべてのコレクションで同じです。

```json
{
    "values": [
        {     
        }
    ],
    "@nextLink": "https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/{collection}?skip=100&top=100"
}
```

`values` プロパティには、使用可能なコレクション エンティティのサブセットが含まれています。 個数とオフセットは、`skip` および `top` クエリ パラメーターを使用して制御できます。 `@nextLink` が `null` でない場合は、さらに使用可能なデータがあることを示しており、次のデータ バッチを取得するには `$.@nextLink` に対して GET を実行します。

この変更により、すべての要素が返されるまでループして、コレクションに対する `GET` を呼び出すことが必要になりました。

>[!IMPORTANT]
>`speechtotext/v3.0/{collection}` に対する GET の応答に `$.@nextLink` の値が含まれている場合は、そのコレクションのすべての要素を取得するように `$.@nextLink` が設定されていない限り、`$.@nextLink` に対して `GETs` を発行し続けます。

### <a name="creating-transcriptions"></a>文字起こしの作成

文字起こしのバッチを作成する方法の詳細な説明については、[バッチ文字起こしの使用方法](./batch-transcription.md)に関するページをご覧ください。

v3 の文字起こし API では、特定の文字起こしオプションを明示的に設定できます。 すべての (省略可能な) 構成プロパティは、`properties` プロパティで設定できるようになりました。
バージョン v3 では複数の入力ファイルもサポートされているため、v2 のように単一の URL ではなく、URL のリストを指定する必要があります。 v2 のプロパティ名 `recordingsUrl` は、v3 では `contentUrls` に変わりました。 文字起こしでセンチメントを分析する機能は、v3 では削除されています。 センチメント分析オプションについては、Microsoft Cognitive Service の [Text Analytics](https://azure.microsoft.com/en-us/services/cognitive-services/text-analytics/) をご覧ください。

`properties` の下にある新しいプロパティ `timeToLive` は、既存の完了したエンティティを取り除くのに役立ちます。 `timeToLive` には、完了したエンティティが自動的に削除されるまでの期間を指定します。 これを大きな値 (`PT12H` など) に設定できるのは、エンティティの追跡、使用、削除が継続的に行われているゆえに、通常は 12 時間が経過するよりずっと前に処理が行われる場合です。

**v2 の文字起こし POST 要求本文:**

```json
{
  "locale": "en-US",
  "name": "Transcription using locale en-US",
  "recordingsUrl": "https://contoso.com/mystoragelocation",
  "properties": {
    "AddDiarization": "False",
    "AddWordLevelTimestamps": "False",
    "PunctuationMode": "DictatedAndAutomatic",
    "ProfanityFilterMode": "Masked"
  }
}
```

**v3 の文字起こし POST 要求本文:**

```json
{
  "locale": "en-US",
  "displayName": "Transcription using locale en-US",
  "contentUrls": [
    "https://contoso.com/mystoragelocation",
    "https://contoso.com/myotherstoragelocation"
  ],
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  }
}
```
>[!IMPORTANT]
>プロパティの名前を `recordingsUrl` から `contentUrls` に変更し、1 つの url ではなく、url の配列を渡します。 `diarizationEnabled` または `wordLevelTimestampsEnabled` の設定を、`string` ではなく `bool` として渡します。

### <a name="format-of-v3-transcription-results"></a>v3 の文字起こし結果の形式

リアルタイム エンドポイントで作成される文字起こしと一致するように、文字起こしの結果のスキーマが若干変更されています。 新しい形式の詳細な説明については、[バッチ文字起こしの使用方法](./batch-transcription.md)に関するページをご覧ください。 結果のスキーマは、[GitHub サンプル リポジトリ](https://aka.ms/csspeech/samples)の `samples/batch/transcriptionresult_v3.schema.json` の下に公開されています。

プロパティ名は Camel 形式になり、`channel` と `speaker` の値では整数型が使用されるようになりました。 期間の形式には、ISO 8601 で説明されている構造を使用するようになりました。これは、他の Azure API で使用される期間の形式と一致しています。

v3 の文字起こし結果のサンプルです。 相違点については、コメントに記載されています。

```json
{
  "source": "...",                      // (new in v3) was AudioFileName / AudioFileUrl
  "timestamp": "2020-06-16T09:30:21Z",  // (new in v3)
  "durationInTicks": 41200000,          // (new in v3) was AudioLengthInSeconds
  "duration": "PT4.12S",                // (new in v3)
  "combinedRecognizedPhrases": [        // (new in v3) was CombinedResults
    {
      "channel": 0,                     // (new in v3) was ChannelNumber
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                // (new in v3) was SegmentResults
    {
      "recognitionStatus": "Success",   // 
      "channel": 0,                     // (new in v3) was ChannelNumber
      "offset": "PT0.07S",              // (new in v3) new format, was OffsetInSeconds
      "duration": "PT1.59S",            // (new in v3) new format, was DurationInSeconds
      "offsetInTicks": 700000.0,        // (new in v3) was Offset
      "durationInTicks": 15900000.0,    // (new in v3) was Duration

      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,phrase
          "speaker": 1,
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",

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
>[!IMPORTANT]
>上に示すように、文字起こしの結果を新しい型に逆シリアル化します。 オーディオ チャネルごとに 1 つのファイルではなく、`recognizedPhrases` 内の各要素について `channel` のプロパティ値を確認することで、チャネルを区別します。 これで、入力ファイルごとに 1 つの結果ファイルが生成されます。


### <a name="getting-the-content-of-entities-and-the-results"></a>エンティティの内容と結果の取得

v2 では、入力または結果ファイルへのリンクは、エンティティ メタデータの残りの部分と共にインライン化されています。 v3 の改善点として、エンティティ メタデータ (`$.self` に対する GET によって返される) と、結果ファイルにアクセスするための詳細および資格情報とが明確に分離されています。 この分離により、顧客データを保護し、資格情報の有効期間を細かく制御できます。

v3 では、エンティティによってデータ (データセット、文字起こし、エンドポイント、または評価) が公開される場合のために、`links` に `files` という名前のサブプロパティが組み込まれています。 `$.links.files` に対する GET では、ファイルの一覧と、各ファイルの内容にアクセスするための SAS URL が返されます。 SAS URL の有効期間を制御するには、クエリ パラメーター `sasValidityInSeconds` を使用して有効期間を指定できます。

**v2 の文字起こし:**

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "status": "Succeeded",
  "reportFileUrl": "https://contoso.com/report.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f728327c53b5",
  "resultsUrls": {
    "channel_0": "https://contoso.com/audiofile1.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f72832e6600c",
    "channel_1": "https://contoso.com/audiofile2.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=3e0163f1-0029-4d4a-988d-3fba7d7c53b5"
  }
}
```

**v3 の文字起こし:**

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    } 
}
```

**`$.links.files` に対して GET を実行すると、次のような結果になります。**

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/f23e54f5-ed74-4c31-9730-2f1a3ef83ce8",
      "name": "Name",
      "kind": "Transcription",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/mywavefile1.wav.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/28bc946b-c251-4a86-84f6-ea0f0a2373ef",
      "name": "Name",
      "kind": "TranscriptionReport",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/report.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files?skip=2&top=2"
}
```

`kind` プロパティは、ファイルの内容の形式を示します。 文字起こしの場合、`TranscriptionReport` という種類のファイルはジョブの概要であり、`Transcription` という種類のファイルはジョブそのものの結果です。

>[!IMPORTANT]
>操作の結果を取得するには、`/speechtotext/v3.0/{collection}/{id}/files` で `GET` を使用します。これらの操作は、`/speechtotext/v3.0/{collection}/{id}` または `/speechtotext/v3.0/{collection}` に対する `GET` の応答には含まれなくなります。

### <a name="customizing-models"></a>モデルのカスタマイズ

v3 より前では、モデルをトレーニングするときに "_音響モデル_" と "_言語モデル_" とが区別されていました。 この区別があるため、エンドポイントまたは文字起こしを作成するときに複数のモデルを指定する必要がありました。 このプロセスを呼び出し元に対して簡略化するために、この違いが排除され、モデルのトレーニングに使用されているデータセットの内容に応じてすべてが判断されるようになりました。 この変更により、モデルの作成で混合データセット (言語データと音響データ) がサポートされるようになりました。 エンドポイントと文字起こしに必要なモデルは 1 つのみになりました。

この変更により、`POST` 操作で `kind` の必要性がなくなり、`datasets[]` 配列には同じまたは混合した複数のデータセットを含めることができるようになりました。

トレーニング済みのモデルの結果を向上させるために、言語のトレーニング中に音響データが自動的に内部で使用されます。 一般に、v3 API を使用して作成されたモデルでは、v2 API を使用して作成されたモデルよりも正確な結果が提供されます。

>[!IMPORTANT]
>音響と言語の両方のモデル パーツをカスタマイズするには、必要なすべての言語と音響のデータセットを `/speechtotext/v3.0/models` に対する POST の `datasets[]` に渡します。 これにより、両方のパーツがカスタマイズされた単一モデルが作成されます。

### <a name="retrieving-base-and-custom-models"></a>基本およびカスタム モデルの取得

使用可能なモデルを簡単に取得できるように、v3 では、"基本モデル" のコレクションが顧客の所有している "カスタマイズされたモデル" から分離されています。 この 2 つのルートは `GET /speechtotext/v3.0/models/base` と `GET /speechtotext/v3.0/models/` です。

v2 では、すべてのモデルが 1 つの応答でまとめて返されていました。

>[!IMPORTANT]
>カスタマイズのために提供された基本モデルの一覧を取得するには、`/speechtotext/v3.0/models/base` で `GET` を使用します。 独自にカスタマイズしたモデルは、`/speechtotext/v3.0/models` で `GET` を使用すると見つかります。

### <a name="name-of-an-entity"></a>エンティティの名前

`name` プロパティは `displayName` に変わりました。 これにより、ID プロパティを示さない他の Azure API と一貫性が保たれます。 このプロパティの値は一意である必要がなく、エンティティを作成した後に `PATCH` 操作で変更できます。

**v2 の文字起こし:**

```json
{
    "name": "Transcription using locale en-US"
}
```

**v3 の文字起こし:**

```json
{
    "displayName": "Transcription using locale en-US"
}
```

>[!IMPORTANT]
>クライアント コード内のプロパティの名前を `name` から `displayName` に変更します。

### <a name="accessing-referenced-entities"></a>参照されたエンティティのアクセス

v2 では、参照されたエンティティは常にインライン化されていました (エンドポイントの使用モデルなど)。 エンティティが入れ子になると応答が大きくなり、コンシューマーが入れ子になったコンテンツを消費することはほとんどありません。 応答のサイズを縮小してパフォーマンスを向上させるために、参照されたエンティティが応答でインライン化されることはなくなりました。 代わりに、他のエンティティへの参照が表示されます。これは、`self` リンクと同じパターンに従って、後続の `GET` (これも URL) に直接使用できます。

**v2 の文字起こし:**

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "models": [
    {
      "id": "827712a5-f942-4997-91c3-7c6cde35600b",
      "modelKind": "Language",
      "lastActionDateTime": "2019-01-07T11:36:07Z",
      "status": "Running",
      "createdDateTime": "2019-01-07T11:34:12Z",
      "locale": "en-US",
      "name": "Acoustic model",
      "description": "Example for an acoustic model",
      "datasets": [
        {
          "id": "702d913a-8ba6-4f66-ad5c-897400b081fb",
          "dataImportKind": "Language",
          "lastActionDateTime": "2019-01-07T11:36:07Z",
          "status": "Succeeded",
          "createdDateTime": "2019-01-07T11:34:12Z",
          "locale": "en-US",
          "name": "Language dataset",
        }
      ]
    },
  ]
}
```

**v3 の文字起こし:**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/021a72d0-54c4-43d3-8254-27336ead9037"
  }
}
```

上の例に示されている参照されたモデルの詳細を使用する必要がある場合は、`$.model.self` に対して GET を実行するだけで済みます。

>[!IMPORTANT]
>参照されたエンティティのメタデータを取得するには、`$.{referencedEntity}.self` に対して GET を発行します。たとえば、文字起こしのモデルを取得するには、`$.model.self` に対して `GET` を実行します。


### <a name="retrieving-endpoint-logs"></a>エンドポイント ログの取得

サービスのバージョン v2 では、エンドポイントの結果のログがサポートされていました。 v2 を使用してエンドポイントの結果を取得するには、"データのエクスポート" を作成していました。これは、時間の範囲で定義された結果のスナップショットを表すものでした。 データのバッチをエクスポートするプロセスには柔軟性がありませんでした。 v3 API では、個々のファイルへのアクセスが提供され、それらのファイルを反復処理できます。

**正常に実行されている v3 エンドポイント:**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6",
  "links": {
    "logs": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs" 
  }
}
```

**GET `$.links.logs`の応答:**

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/6d72ad7e-f286-4a6f-b81b-a0532ca6bcaa/files/logs/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "name": "2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "kind": "Audio",
      "properties": {
        "size": 12345
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }    
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs?top=2&SkipToken=2!188!MDAwMDk1ITZhMjhiMDllLTg0MDYtNDViMi1hMGRkLWFlNzRlOGRhZWJkNi8yMDIwLTA0LTAxLzEyNDY0M182MzI5NGRkMi1mZGYzLTRhZmEtOTA0NC1mODU5ZTcxOWJiYzYud2F2ITAwMDAyOCE5OTk5LTEyLTMxVDIzOjU5OjU5Ljk5OTk5OTlaIQ--"
}
```

エンドポイント ログのページ分割の自動調整は、他のすべてのコレクションと同様に機能しますが、オフセットを指定することはできません。 使用可能なデータが大量にあるため、ページ分割はサーバーによって決定されます。

v3 では、ファイルの `self` に対して `DELETE` 操作を発行するか、`$.links.logs` に対して `DELETE` を使用すれば、各エンドポイント ログを個別に削除することができます。 終了日を指定するには、クエリ パラメーター `endDate` を要求に追加します。

>[!IMPORTANT]
>ログのエクスポートを `/api/speechtotext/v2.0/endpoints/{id}/data` で作成するのではなく、`/v3.0/endpoints/{id}/files/logs/` を使用してログ ファイルに個別にアクセスします。 

### <a name="using-custom-properties"></a>カスタム プロパティの使用

カスタム プロパティをオプションの構成プロパティから分離するために、明示的に名前を指定されたすべてのプロパティは `properties` プロパティに配置され、呼び出し元によって定義されたすべてのプロパティは `customProperties` プロパティに配置されるようになりました。

**v2 の文字起こしエンティティ:**

```json
{
  "properties": {
    "customerDefinedKey": "value",
    "diarizationEnabled": "False",
    "wordLevelTimestampsEnabled": "False"
  }
}
```

**v3 の文字起こしエンティティ:**

```json
{
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false 
  },
  "customProperties": {
    "customerDefinedKey": "value"
  }
}
```

この変更により、`properties` の下にある明示的に名前を指定されたすべてのプロパティに対して適切な型 (たとえば、文字列ではなくブール値) を使用することもできるようになりました。

>[!IMPORTANT]
>`POST` 要求で、`properties` ではなく、すべてのカスタム プロパティを `customProperties` として渡します。

### <a name="response-headers"></a>応答ヘッダー

v3 では、`POST` 要求で `Location` ヘッダーに加えて `Operation-Location` ヘッダーが返されることがなくなりました。 v2 では、この両方のヘッダーの値は同じでした。 現在は `Location` のみが返されるようになりました。

新しい API バージョンは Azure API Management (APIM) によって管理されるようになったため、スロットリング関連のヘッダー `X-RateLimit-Limit`、`X-RateLimit-Remaining`、`X-RateLimit-Reset` は応答ヘッダーに含まれなくなりました。

>[!IMPORTANT]
>`Operation-Location` ではなく、応答ヘッダー `Location` から場所を読み取ります。 429 応答コードの場合は、`X-RateLimit-Limit`、`X-RateLimit-Remaining`、`X-RateLimit-Reset` ではなく、`Retry-After` ヘッダー値を読み取ります。


### <a name="accuracy-tests"></a>正確性テスト

正確性テストは、名前が表す内容をより適切に示すため、評価に名称が変更されました。 新しいパスは次のとおりです: `https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/evaluations`。

>[!IMPORTANT]
>クライアント コード内のパスのセグメントの名前を `accuracytests` から `evaluations` に変更します。


## <a name="next-steps"></a>次のステップ

音声サービスによって提供される次の一般的な REST API のすべての機能を確認します。

* [Speech to Text REST API](rest-speech-to-text.md)
* REST API v3 に関する [Swagger ドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* バッチ文字起こしを実行するサンプル コードについては、[GitHub サンプル リポジトリ](https://aka.ms/csspeech/samples)の `samples/batch` サブディレクトリをご覧ください。
