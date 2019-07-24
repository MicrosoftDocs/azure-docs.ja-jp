---
title: Azure Media Content Moderator を使用して、成人向けやわいせつな可能性のあるコンテンツを検出する | Microsoft Docs
description: ビデオのモデレートを使用すると、ビデオに含まれる成人向けやわいせつな可能性のあるコンテンツを検出するのに役立ちます。
services: media-services
documentationcenter: ''
author: sanjeev3
manager: mikemcca
editor: ''
ms.assetid: a245529f-3150-4afc-93ec-e40d8a6b761d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: eb16f5e1e72e5a9379ad530ab9677adba2ccbbcd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57899565"
---
# <a name="use-azure-media-content-moderator-to-detect-possible-adult-and-racy-content"></a>Azure Media Content Moderator を使用して、成人向けやわいせつな可能性のあるコンテンツを検出する 

## <a name="overview"></a>概要
**Azure Media Content Moderator** メディア プロセッサ (MP) を使用すると、マシンによるモデレートをビデオに使用できます。 たとえば、成人向けやわいせつな可能性のあるビデオ コンテンツを検出し、フラグが設定されたコンテンツを人間のモデレート チームがレビューすることができます。

**Azure Media Content Moderator** MP は現在プレビュー段階です。

この記事では、**Azure Media Content Moderator** の詳細と、Media Services SDK for .NET での使用方法を説明します。

## <a name="content-moderator-input-files"></a>Content Moderator の入力ファイル
ビデオ ファイルです。 現在サポートされている形式は MP4、MOV、WMV です。

## <a name="content-moderator-output-files"></a>Content Moderator の出力ファイル
JSON 形式のモデレート済み出力には、自動検出されたショットとキーフレームが含まれます。 キーフレームは、成人向けやわいせつな可能性のあるコンテンツの信頼度スコアと共に返されます。 また、レビューが推奨されるかどうかを示すブール型フラグも含まれます。 レビュー推奨フラグには、成人向けやわいせつなコンテンツのスコア用の内部しきい値に基づく値が割り当てられます。

## <a name="elements-of-the-output-json-file"></a>出力 JSON ファイルの要素

このジョブで生成される JSON 出力ファイルには、検出されたショットやキーフレームと、成人向けやわいせつなコンテンツの有無に関するメタデータが含まれています。

出力 JSON には、次の要素が含まれています。

### <a name="root-json-elements"></a>ルート JSON 要素

| 要素 | 説明 |
| --- | --- |
| version |Content Moderator のバージョンです。 |
| timescale |ビデオの 1 秒あたりの "ティック数" です。 |
| offset |タイムスタンプの時間オフセットです。 Video API のバージョン 1.0 では、この値は常に 0 になります。 この値は今後変更される可能性があります。 |
| framerate |ビデオの 1 秒あたりのフレーム数です。 |
| width |出力ビデオ フレームの幅です (ピクセル単位)。|
| height |出力ビデオ フレームの高さです (ピクセル単位)。|
| totalDuration |入力ビデオの長さです ("ティック数")。 |
| [fragments](#fragments-json-elements) |メタデータは、フラグメントと呼ばれる複数のセグメントに分割されます。 各フラグメントは、開始、継続時間、間隔数、およびイベントが含まれた自動検出ショットです。 |

### <a name="fragments-json-elements"></a>フラグメント JSON 要素

|要素|説明|
|---|---|
| start |最初のイベントの開始時間です ("ティック数")。 |
| duration |フラグメントの長さです ("ティック数")。 |
| interval |フラグメント内の各イベント エントリの間隔です ("ティック数")。 |
| [events](#events-json-elements) |各イベントはクリップに相当し、各クリップにはその期間内に検出および追跡されたキーフレームが含まれています。 これは、イベントの配列です。 外側の配列は、1 つの時間間隔を表します。 内側の配列は、その時点で発生した 0 個以上のイベントで構成されます。|

### <a name="events-json-elements"></a>イベント JSON 要素

|要素|説明|
|---|---|
| reviewRecommended | **adultScore** または **racyScore** が内部しきい値を超えているかどうかに応じて `true` または `false` です。 |
| adultScore | 成人向けの可能性のあるコンテンツの信頼度スコアです。0.00 から 0.99 の間の値です。 |
| racyScore | わいせつな可能性のあるコンテンツの信頼度ストアです。0.00 から 0.99 の間の値です。 |
| Index | フレームのインデックスです。最初のフレーム インデックスから最後のフレーム インデックスの間の値です。 |
| timestamp | フレームの位置です ("ティック数")。 |
| shotIndex | 親ショットのインデックスです。 |


## <a name="content-moderation-quickstart-and-sample-output"></a>Content Moderation のクイックスタートとサンプル出力

### <a name="task-configuration-preset"></a>タスクの構成 (プリセット)
**Azure Media Content Moderator** でタスクを作成するときは、構成プリセットを指定する必要があります。 次の構成プリセットは、ビデオのモデレート用です。

    {
      "version":"2.0"
    }

### <a name="net-code-sample"></a>.NET コード サンプル

次の .NET コード サンプルでは、Media Services .NET SDK を使用して、Content Moderator ジョブを実行します。 メディア サービス資産を、モデレート対象のビデオが含まれた入力として受け取ります。
ソース コード全体と Visual Studio プロジェクトについては、[Content Moderator のビデオのモデレートに関するクイックスタート](../../cognitive-services/Content-Moderator/video-moderation-api.md)に関するページをご覧ください。


```csharp
    /// <summary>
    /// Run the Content Moderator job on the designated Asset from local file or blob storage
    /// </summary>
    /// <param name="asset"></param>
    static void RunContentModeratorJob(IAsset asset)
    {
        // Grab the presets
        string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

        // grab instance of Azure Media Content Moderator MP
        IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

        // create Job with Content Moderator task
        IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
                asset.AssetFiles.First() + "_" + Guid.NewGuid()));

        ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
                mp, configuration,
                TaskOptions.None);
        contentModeratorTask.InputAssets.Add(asset);
        contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
            AssetCreationOptions.None);

        job.Submit();


        // Create progress printing and querying tasks
        Task progressPrintTask = new Task(() =>
        {
            IJob jobQuery = null;
            do
            {
                var progressContext = _context;
                jobQuery = progressContext.Jobs
                .Where(j => j.Id == job.Id)
                    .First();
                    Console.WriteLine(string.Format("{0}\t{1}",
                    DateTime.Now,
                    jobQuery.State));
                    Thread.Sleep(10000);
             }
             while (jobQuery.State != JobState.Finished &&
             jobQuery.State != JobState.Error &&
             jobQuery.State != JobState.Canceled);
        });
        progressPrintTask.Start();

        Task progressJobTask = job.GetExecutionProgressTask(
        CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling 
        // method for job progress should log errors.  Here we check 
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            ErrorDetail error = job.Tasks.First().ErrorDetails.First();
            Console.WriteLine(string.Format("Error: {0}. {1}",
            error.Code,
            error.Message));
        }

        DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
    }

For the full source code and the Visual Studio project, check out the [Content Moderator video quickstart](../../cognitive-services/Content-Moderator/video-moderation-api.md).

### JSON output

The following example of a Content Moderator JSON output was truncated.

> [!NOTE]
> Location of a keyframe in seconds = timestamp/timescale

    {
    "version": 2,
    "timescale": 90000,
    "offset": 0,
    "framerate": 50,
    "width": 1280,
    "height": 720,
    "totalDuration": 18696321,
    "fragments": [
    {
      "start": 0,
      "duration": 18000
    },
    {
      "start": 18000,
      "duration": 3600,
      "interval": 3600,
      "events": [
        [
          {
            "reviewRecommended": false,
            "adultScore": 0.00001,
            "racyScore": 0.03077,
            "index": 5,
            "timestamp": 18000,
            "shotIndex": 0
          }
        ]
      ]
    },
    {
      "start": 18386372,
      "duration": 119149,
      "interval": 119149,
      "events": [
        [
          {
            "reviewRecommended": true,
            "adultScore": 0.00000,
            "racyScore": 0.91902,
            "index": 5085,
            "timestamp": 18386372,
            "shotIndex": 62
          }
        ]
      ]
    }
    ]
    }
```

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>関連リンク
[Azure Media Services Analytics の概要](media-services-analytics-overview.md)

[Azure Media Analytics デモ](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

## <a name="next-steps"></a>次の手順

Content Moderator の[ビデオのモデレートとレビュー ソリューション](../../cognitive-services/Content-Moderator/video-moderation-human-review.md)について学ぶ。

[ビデオのモデレートに関するクイックスタート](../../cognitive-services/Content-Moderator/video-moderation-api.md)からソース コード全体と Visual Studio プロジェクトを取得する。 

モデレート済みの出力から[ビデオのレビュー](../../cognitive-services/Content-Moderator/video-reviews-quickstart-dotnet.md)を生成し、.NET で[トランスクリプトをモデレート](../../cognitive-services/Content-Moderator/video-transcript-reviews-quickstart-dotnet.md)する方法を学ぶ。

詳細な .NET [ビデオのモデレートとレビューに関するチュートリアル](../../cognitive-services/Content-Moderator/video-transcript-moderation-review-tutorial-dotnet.md)を確認する。 
