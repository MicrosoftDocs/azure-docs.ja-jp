---
title: Azure Media Analytics で動作を検出する | Microsoft Docs
description: Azure Media Motion Detector メディア プロセッサ (MP) を使用すると、長くて動きの少ないビデオから注目すべき部分を効率よく識別できます。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.reviewer: milanga
ms.openlocfilehash: c053e4dfc38fc0f055ec91a6622ef7f767c13a86
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "69015332"
---
# <a name="detect-motions-with-azure-media-analytics"></a>Azure Media Analytics で動作を検出する
## <a name="overview"></a>概要
**Azure Media Motion Detector** メディア プロセッサ (MP) を使用すると、長くて動きの少ないビデオから注目すべき部分を効率よく識別できます。 モーション検出は、ビデオでモーションが発生したセクションを識別するために、静止カメラ映像で使用できます。 この機能は、イベントが発生したタイムスタンプと境界範囲のメタデータを含む JSON ファイルを生成します。

セキュリティ ビデオ フィードを対象とするこのテクノロジは、関連するイベントと、影や照明の変化などの誤検出に、モーションを分類できません。 これにより、いつまでも続く関係のないイベントに煩わされることなくカメラ フィードからセキュリティ アラートを生成することができ、長い監視ビデオから関心のある瞬間を抽出できます。

**Azure Media Motion Detector** MP は現在プレビュー段階です。

ここでは、**Azure Media Motion Detector** の詳細を説明し、Media Services SDK for .NET でのその使用方法を示します

## <a name="motion-detector-input-files"></a>Motion Detector の入力ファイル
ビデオ ファイルです。 現在サポートされている形式は MP4、MOV、WMV です。

## <a name="task-configuration-preset"></a>タスクの構成 (プリセット)
**Azure Media Motion Detector**でタスクを作成するときは、構成プリセットを指定する必要があります。 

### <a name="parameters"></a>parameters
次のパラメーターを使用できます。

| EnableAdfsAuthentication | オプション | 説明 | 既定値 |
| --- | --- | --- | --- |
| sensitivityLevel |文字列: "low"、"medium"、"high" |モーション報告の感度を設定します。 誤検出の数を調整します。 |"medium" |
| frameSamplingValue |正の整数 |アルゴリズムの実行頻度を設定します。 1 = すべてのフレーム、2 = 2 フレームごと、以降同様に続きます。 |1 |
| detectLightChange |ブール値: "true" または "false" |ライトの変更を結果で報告するかどうかを設定します |"False" |
| mergeTimeThreshold |Xs-time:Hh:mm:ss<br/>例:00:00:03 |モーション イベント間の時間枠を指定します。ここでは 2 つのイベントが結合され、1 として報告されます。 |00:00:00 |
| detectionZones |検出ゾーンの配列:<br/>- 検出ゾーンとは 3 つ以上のポイントの配列です<br/>- ポイントは、0 から 1 の xy 座標です。 |使用する多角形検出ゾーンの一覧を記述します<br/>結果にはゾーンが ID として報告され、最初は "ID": 0 です |フレーム全体を対象とする 1 つのゾーンです。 |

### <a name="json-example"></a>JSON の例

```json
    {
      "version": "1.0",
      "options": {
        "sensitivityLevel": "medium",
        "frameSamplingValue": 1,
        "detectLightChange": "False",
        "mergeTimeThreshold":
        "00:00:02",
        "detectionZones": [
          [
            {"x": 0, "y": 0},
            {"x": 0.5, "y": 0},
            {"x": 0, "y": 1}
           ],
          [
            {"x": 0.3, "y": 0.3},
            {"x": 0.55, "y": 0.3},
            {"x": 0.8, "y": 0.3},
            {"x": 0.8, "y": 0.55},
            {"x": 0.8, "y": 0.8},
            {"x": 0.55, "y": 0.8},
            {"x": 0.3, "y": 0.8},
            {"x": 0.3, "y": 0.55}
          ]
        ]
      }
    }
```

## <a name="motion-detector-output-files"></a>Motion Detector の出力ファイル
モーション検出ジョブは、ビデオ内のモーション アラートおよびそのカテゴリが記述されている JSON ファイルを出力資産で返します。 このファイルには、ビデオで検出されたモーションの時間と継続時間に関する情報が含まれます。

Motion Detector API は、固定背景ビデオ (監視ビデオなど) 内に動く対象物があると、インジケーターを提供します。 Motion Detector は、照明と影の変化などの誤アラームが減るようにトレーニングされます。 アルゴリズムの現在の制限事項としては、暗視ビデオ、半透明の対象物、小さな対象物などがあります。

### <a id="output_elements"></a>出力 JSON ファイルの要素
> [!NOTE]
> 最新リリースでは、出力 JSON 形式が変更されています。これは、一部のお客様にとっては重大な変更である場合があります。
> 
> 

出力 JSON ファイルの要素を次の表に示します。

| 要素 | 説明 |
| --- | --- |
| version |Video API のバージョンを示します。 現行バージョンは 2 です。 |
| timescale |ビデオの 1 秒あたりの "ティック数" です。 |
| offset |タイムスタンプの時間オフセットです ("ティック数")。 Video API のバージョン 1.0 では、これは常に 0 になります。 今後サポートされるシナリオでは、変更される可能性があります。 |
| framerate |ビデオの 1 秒あたりのフレーム数です。 |
| width、height |ビデオの幅と高さです (ピクセル)。 |
| start |"ティック数" での開始タイムスタンプです。 |
| duration |イベントの長さです ("ティック数")。 |
| interval |イベント内の各エントリの間隔です ("ティック数")。 |
| events |各イベント フラグメントには、その期間内で検出されたモーションが含まれます。 |
| type |現在のバージョンでは、これは常に一般モーションを示す "2" です。 このラベルにより、Video API は将来のバージョンでモーションを柔軟に分類できます。 |
| regionId |前述のように、このバージョンではこの値は常に 0 です。 このラベルにより、Video API は将来のバージョンでさまざまな領域のモーションを柔軟に検出できるます。 |
| regions |モーションに注意するビデオ内の領域を示します。 <br/><br/>\- "id" は、領域の範囲を表します。現バージョンでは ID 0 だけです。 <br/>\- "type" は、モーションに注意する領域の形状を表します。 現在 "rectangle" と "polygon" がサポートされています。<br/> "rectangle" を指定した場合、領域のディメンションは X、Y、幅、高さです。 X および Y 座標は、0.0 ～ 1.0 に正規化されたスケール内の領域の左上の XY 座標を表します。 幅と高さは、0.0 ～ 1.0 に正規化されたスケール内の領域のサイズを表します。 現在のバージョンでは、X、Y、幅、高さは常に 0、0、1、1 の固定値です。 <br/>"polygon" を指定したした場合、領域のディメンションはポイント単位になります。 <br/> |
| fragments |メタデータは、フラグメントと呼ばれる複数のセグメントに分割されます。 各フラグメントには、開始、継続時間、間隔数、およびイベントが含まれます。 イベントのないフラグメントは、その開始時間と継続時間中にモーションが検出されなかったことを意味します。 |
| 角かっこ [] |各角かっこは、イベント内の 1 つの間隔を表します。 その間隔の角かっこが空の場合、モーションが検出されなかったことを意味します。 |
| locations |イベントの下にあるこの新しいエントリには、モーションが発生した場所が示されます。 これは検出ゾーンよりも具体的です。 |

次の JSON の例は、出力を示しています。

```json
    {
      "version": 2,
      "timescale": 23976,
      "offset": 0,
      "framerate": 24,
      "width": 1280,
      "height": 720,
      "regions": [
        {
          "id": 0,
          "type": "polygon",
          "points": [{'x': 0, 'y': 0},
            {'x': 0.5, 'y': 0},
            {'x': 0, 'y': 1}]
        }
      ],
      "fragments": [
        {
          "start": 0,
          "duration": 226765
        },
        {
          "start": 226765,
          "duration": 47952,
          "interval": 999,
          "events": [
            [
              {
                "type": 2,
                "typeName": "motion",
                "locations": [
                  {
                    "x": 0.004184,
                    "y": 0.007463,
                    "width": 0.991667,
                    "height": 0.985185
                  }
                ],
                "regionId": 0
              }
            ],
```

## <a name="limitations"></a>制限事項
* サポートされている入力ビデオ形式は、MP4、MOV、WMV です。
* モーション検出は、固定背景のビデオに最適化されています。 アルゴリズムは、照明の変化や影などの誤ったアラームを減らすことに焦点が当てられています。
* 暗視ビデオ、半透明の対象物、小さな対象物など、技術的な問題のために検出できない可能性のあるモーションがあります。

## <a name="net-sample-code"></a>.NET サンプル コード

このプログラムでは次の方法を示します。

1. 資産を作成し、その資産にメディア ファイルをアップロードします。
2. 次の JSON プリセットを含む構成ファイルに基づくビデオ モーション検出タスクのジョブを作成します。 
   
    ```json
            {
            "Version": "1.0",
            "Options": {
                "SensitivityLevel": "medium",
                "FrameSamplingValue": 1,
                "DetectLightChange": "False",
                "MergeTimeThreshold":
                "00:00:02",
                "DetectionZones": [
                [
                    {"x": 0, "y": 0},
                    {"x": 0.5, "y": 0},
                    {"x": 0, "y": 1}
                ],
                [
                    {"x": 0.3, "y": 0.3},
                    {"x": 0.55, "y": 0.3},
                    {"x": 0.8, "y": 0.3},
                    {"x": 0.8, "y": 0.55},
                    {"x": 0.8, "y": 0.8},
                    {"x": 0.55, "y": 0.8},
                    {"x": 0.3, "y": 0.8},
                    {"x": 0.3, "y": 0.55}
                ]
                ]
            }
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

namespace VideoMotionDetection
{
    class Program
    {
        // Read values from the App.config file.
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

            // Run the VideoMotionDetection job.
            var asset = RunVideoMotionDetectionJob(@"C:\supportFiles\VideoMotionDetection\BigBuckBunny.mp4",
                                        @"C:\supportFiles\VideoMotionDetection\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\VideoMotionDetection\Output");
        }

        static IAsset RunVideoMotionDetectionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Video Motion Detection Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Video Motion Detection Job");

            // Get a reference to Azure Media Motion Detector.
            string MediaProcessorName = "Azure Media Motion Detector";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Video Motion Detection Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Video Motion Detection Output Asset", AssetCreationOptions.None);

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
[Azure Media Services Motion Detector のブログ](https://azure.microsoft.com/blog/motion-detector-update/)

[Azure Media Services Analytics の概要](media-services-analytics-overview.md)

[Azure Media Analytics デモ](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

