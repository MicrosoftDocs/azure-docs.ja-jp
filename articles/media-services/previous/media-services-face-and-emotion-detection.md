---
title: Azure Media Analytics での顔と感情の検出 | Microsoft Docs
description: このトピックでは、Azure Media Analytics で顔と感情を検出する方法を示します。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 5ca4692c-23f1-451d-9d82-cbc8bf0fd707
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: milanga;juliako;
ms.openlocfilehash: 46e60583da79006c133c8d9fac63e27f28bd699f
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58188188"
---
# <a name="detect-face-and-emotion-with-azure-media-analytics"></a>Azure Media Analytics での顔と感情の検出
## <a name="overview"></a>概要
**Azure Media Face Detector** メディア プロセッサ (MP) を使用すると、対象ユーザーを数えたり、動きを追跡したり、表情によって対象ユーザーの参加や反応を測定することさえできます。 このサービスには 2 つの機能があります。 

* **顔検出**
  
    顔検出は、ビデオの中で人の顔を検出して追跡します。 複数の顔を検出した後、画面内の移動を追跡でき、時間と位置のメタデータが JSON ファイルで返されます。 追跡中、この機能は画面内を移動する同じ人の顔には同じ ID をできる限り設定し、顔が遮られたり一瞬フレームの外に出たりしても維持されます。
  
  > [!NOTE]
  > このサービスでは、顔認識は実行されません。 顔が遮られたりフレームから外れる時間が長すぎると、戻ったときには新しい ID が設定されます。
  > 
  > 
* **感情検出**
  
    感情検出は Face Detection メディア プロセッサのオプションのコンポーネントであり、喜び、悲しみ、恐怖、怒りなど、検出された顔から複数の感情的属性の分析を返します。 

**Azure Media Face Detector** MP は現在プレビュー段階です。

ここでは、**Azure Media Face Detector** の詳細を説明し、Media Services SDK for .NET でのその使用方法を示します。

## <a name="face-detector-input-files"></a>Face Detector の入力ファイル
ビデオ ファイルです。 現在サポートされている形式は MP4、MOV、WMV です。

## <a name="face-detector-output-files"></a>Face Detector の出力ファイル
顔検出および追跡 API は、高精度の顔位置検出と追跡を行い、ビデオ内で最大 64 個の人の顔を検出できます。 顔が正面を向いているときに最善の結果が得られ、横顔や小さい顔 (24 x 24 ピクセル以下) のときは精度が低下することがあります。

検出されて追跡される顔に対しては、画像内での顔の位置を示す座標 (左端、上端、幅、高さ、ピクセル単位) と、追跡対象の個人を識別する顔 ID 番号が返されます。 顔 ID 番号は、前向きの顔が失われたりフレーム内で重なったりするとリセットされる場合があり、同じ顔に複数の ID が割り当てられる可能性があります。

## <a id="output_elements"></a>出力 JSON ファイルの要素

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

Face Detector は、フラグメント化 (メタデータを時間に基づいて分割し、必要なものだけをダウンロードできます) およびセグメント化 (イベントが大きくなりすぎた場合に分割されます) の技法を使用します。 簡単な計算でデータを変換できます。 たとえば、イベントが 6300 (ティック) に開始し、タイムスケールが 2997 (ティック/秒)、フレームレートが 29.97 (フレーム/秒) である場合、次のようになります。

* 開始/タイムスケール = 2.1 秒
* 秒 x フレームレート = 63 フレーム

## <a name="face-detection-input-and-output-example"></a>顔検出の入力と出力の例
### <a name="input-video"></a>入力ビデオ
[入力ビデオ](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>タスクの構成 (プリセット)
**Azure Media Face Detector**でタスクを作成するときは、構成プリセットを指定する必要があります。 次の構成プリセットは、顔検出用だけです。

```json
    {
      "version":"1.0",
      "options":{
          "TrackingMode": "Fast"
      }
    }
```

#### <a name="attribute-descriptions"></a>属性の説明
| 属性名 | 説明 |
| --- | --- |
| Mode |Fast: 処理速度は速くなりますが、精度が低下します (既定値)。|

### <a name="json-output"></a>JSON 出力
次の JSON 出力例は途中までです。

```json
    {
    "version": 1,
    "timescale": 30000,
    "offset": 0,
    "framerate": 29.97,
    "width": 1280,
    "height": 720,
    "fragments": [
        {
        "start": 0,
        "duration": 60060
        },
        {
        "start": 60060,
        "duration": 60060,
        "interval": 1001,
        "events": [
            [
            {
                "id": 0,
                "x": 0.519531,
                "y": 0.180556,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517969,
                "y": 0.181944,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517187,
                "y": 0.183333,
                "width": 0.0851562,
                "height": 0.151389
            }
            ],
```


## <a name="emotion-detection-input-and-output-example"></a>感情検出の入力と出力の例
### <a name="input-video"></a>入力ビデオ
[入力ビデオ](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>タスクの構成 (プリセット)
**Azure Media Face Detector**でタスクを作成するときは、構成プリセットを指定する必要があります。 次の構成プリセットでは、感情検出に基づく JSON の作成を指定しています。

```json
    {
      "version": "1.0",
      "options": {
        "aggregateEmotionWindowMs": "987",
        "mode": "aggregateEmotion",
        "aggregateEmotionIntervalMs": "342"
      }
    }
```


#### <a name="attribute-descriptions"></a>属性の説明
| 属性名 | 説明 |
| --- | --- |
| Mode |Faces:顔検出のみ。<br/>PerFaceEmotion:検出された顔ごとに、感情を個別に返します。<br/>AggregateEmotion:フレーム内のすべての顔の平均的感情値を返します。 |
| AggregateEmotionWindowMs |AggregateEmotion モードが選択されている場合に使用します。 各集計結果を生成するために使用するビデオの長さを指定します (ミリ秒単位)。 |
| AggregateEmotionIntervalMs |AggregateEmotion モードが選択されている場合に使用します。 集計結果を生成する頻度を指定します。 |

#### <a name="aggregate-defaults"></a>集計の既定値
以下は、集計時間枠と間隔の設定に対して推奨される値です。 AggregateEmotionWindowMs は AggregateEmotionIntervalMs より長くする必要があります。

|| 既定値 (秒) | 最大 (秒) | 最小 (秒) |
|--- | --- | --- | --- |
| AggregateEmotionWindowMs |0.5 |2 |0.25|
| AggregateEmotionIntervalMs |0.5 |1 |0.25|

### <a name="json-output"></a>JSON 出力
感情の集計の JSON 出力 (途中まで):

```json
    {
     "version": 1,
     "timescale": 30000,
     "offset": 0,
     "framerate": 29.97,
     "width": 1280,
     "height": 720,
     "fragments": [
       {
         "start": 0,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ]
         ]
       },
       {
         "start": 60060,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0.688541,
                 "happiness": 0.0586323,
                 "surprise": 0.227184,
                 "sadness": 0.00945675,
                 "anger": 0.00592107,
                 "disgust": 0.00154993,
                 "fear": 0.00450447,
                 "contempt": 0.0042109
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
```

## <a name="limitations"></a>制限事項
* サポートされている入力ビデオ形式は、MP4、MOV、WMV です。
* 検出可能な顔のサイズは、24 x 24 ～ 2048 x 2048 ピクセルの範囲です。 この範囲から外れる顔は検出されません。
* 各ビデオについて、返される顔の最大数は 64 です。
* 技術的な課題のために、顔を検出できない場合があります (例: 顔を角度が非常に大きい (頭部姿勢)、遮蔽が大きい)。 顔が正面または正面に近い方向を向いているときに、最善の結果が得られます。

## <a name="net-sample-code"></a>.NET サンプル コード

このプログラムでは次の方法を示します。

1. 資産を作成し、その資産にメディア ファイルをアップロードします。
2. 次の JSON プリセットを含む構成ファイルに基づく顔検出タスクのジョブを作成します。 

    ```json
            {
                "version": "1.0"
            }
    ```
3. 出力 JSON ファイルをダウンロードします。 

#### <a name="create-and-configure-a-visual-studio-project"></a>Visual Studio プロジェクトの作成と構成

「[.NET を使用した Media Services 開発](media-services-dotnet-how-to-use.md)」の説明に従って、開発環境をセットアップし、app.config ファイルに接続情報を指定します。 

#### <a name="example"></a>例

```csharp
using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;
using System.Threading.Tasks;

namespace FaceDetection
{
    class Program
    {
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        // Field for service context.
        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Run the FaceDetection job.
            var asset = RunFaceDetectionJob(@"C:\supportFiles\FaceDetection\BigBuckBunny.mp4",
                                        @"C:\supportFiles\FaceDetection\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\FaceDetection\Output");
        }

        static IAsset RunFaceDetectionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Face Detection Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Face Detection Job");

            // Get a reference to Azure Media Face Detector.
            string MediaProcessorName = "Azure Media Face Detector";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Face Detection Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Face Detection Output Asset", AssetCreationOptions.None);

            // Use the following event handler to check job progress.  
            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Check job execution and wait for job to finish.
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);

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
                return null;
            }

            return job.OutputMediaAssets[0];
        }

        static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
        {
            IAsset asset = _context.Assets.Create(assetName, options);

            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);

            return asset;
        }

        static void DownloadAsset(IAsset asset, string outputDirectory)
        {
            foreach (IAssetFile file in asset.AssetFiles)
            {
                file.Download(Path.Combine(outputDirectory, file.Name));
            }
        }

        static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors
                .Where(p => p.Name == mediaProcessorName)
                .ToList()
                .OrderBy(p => new Version(p.Version))
                .LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor",
                                                           mediaProcessorName));

            return processor;
        }

        static private void StateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);

            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished.");
                    Console.WriteLine();
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:
                    // Cast sender as a job.
                    IJob job = (IJob)sender;
                    // Display or log error details as needed.
                    // LogJobStop(job.Id);
                    break;
                default:
                    break;
            }
        }
    }
}
```

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>関連リンク
[Azure Media Services Analytics の概要](media-services-analytics-overview.md)

[Azure Media Analytics デモ](https://amslabs.azurewebsites.net/demos/Analytics.html)

