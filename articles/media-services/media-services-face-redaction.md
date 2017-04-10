---
title: "Azure Media Analytics で顔を編集する | Microsoft Docs"
description: "このトピックでは、Azure Media Analytics で顔を編集する方法を示します。"
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 5b6d8b8c-5f4d-4fef-b3d6-dc22c6b5a0f5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/01/2017
ms.author: juliako;
translationtype: Human Translation
ms.sourcegitcommit: 5b8d989d950c17d867f30a6fa8a464a8750e2303
ms.openlocfilehash: 98922addf7aa8bfc77033be29bf137b362378661
ms.lasthandoff: 02/02/2017


---
# <a name="redact-faces-with-azure-media-analytics"></a>Azure Media Analytics で顔を編集する
## <a name="overview"></a>概要
**Azure Media Redactor** は、クラウドでスケーラブルな顔編集を提供する [Azure Media Analytics](media-services-analytics-overview.md) メディア プロセッサ (MP) です。 顔編集では、ビデオを編集して選択した個人の顔をぼかすことができます。 顔編集サービスは、公共の安全やニュース媒体などに使用していただけます。 複数人の顔を含んでいる映像の場合、顔編集を手作業で行うと数分の映像でも数時間かかりますが、このサービスを使えば数ステップの簡単な手順で完了します。 詳細については、[こちらの投稿](https://azure.microsoft.com/blog/azure-media-redactor/)を参照してください。

ここでは、 **Azure Media Redactor** の詳細と、Media Services SDK for .NET での使用方法について説明します。

**Azure Media Redactor** MP は現在プレビュー段階です。 すべてのパブリックな Azure リージョン、米国政府、および中国国内のデータセンターで使用できます。 このプレビューは、現在無料で提供されています。 

## <a name="face-redaction-modes"></a>顔編集モード
顔編集は、ビデオのフレームごとに顔を検出し、その顔オブジェクトを時間軸の前後にわたって追跡することで、同一の人間を他の角度からも処理します。 自動修正のプロセスは非常に複雑で、常に 100% 満足のいく結果になるとは限りません。そのため、Media Analytics には最終的なアウトプットを変更する方法がいくつか用意されています。

完全な自動モードに加え、2 パス ワークフローというものがあり、これによって、検出された顔を ID リストを使って選択または選択解除することができます。 また、任意のフレームごとの調整をするには、JSON 形式でメタデータ ファイルを使用します。 このワークフローは、**分析**モードと**編集**モードに分かれています。 単一のパスに&2; つのモードを結合して、両方のタスクを&1; つのジョブで実行します。このモードは**結合**と呼ばれます。

### <a name="combined-mode"></a>結合モード
手作業なしで、自動的に修正された mp4 が生成されます。

| 段階 | ファイル名 | メモ |
| --- | --- | --- |
| 入力資産 |foo.bar |WMV、MPV、MP4 形式のビデオ |
| 入力 config |ジョブ構成プリセット |{'version':'1.0', 'options': {'mode':'combined'}} |
| 出力資産 |foo_redacted.mp4 |ぼかしが適用されたビデオ |

#### <a name="input-example"></a>入力例:
[ビデオ](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fed99001d-72ee-4f91-9fc0-cd530d0adbbc%2FDancing.mp4)

#### <a name="output-example"></a>出力例:
[ビデオ](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc6608001-e5da-429b-9ec8-d69d8f3bfc79%2Fdance_redacted.mp4)

### <a name="analyze-mode"></a>分析モード
2 パス ワークフローの **分析** パスでは、ビデオ入力を受け取り、顔の位置の JSON ファイルと、検出された顔それぞれの jpg イメージを生成します。

| 段階 | ファイル名 | メモ |
| --- | --- | --- |
| 入力資産 |foo.bar |WMV、MPV、MP4 形式のビデオ |
| 入力 config |ジョブ構成プリセット |{'version':'1.0', 'options': {'mode':'analyze'}} |
| 出力資産 |foo_annotations.json |JSON 形式での、顔の位置の注釈データです。 ユーザー編集によりぼかし枠を変更することができます。 以下のサンプルを参照してください。 |
| 出力資産 |foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |検出された顔それぞれをトリミングした jpg (数字は顔の labelId を示す) |

#### <a name="output-example"></a>出力例:
    {
      "version": 1,
      "timescale": 50,
      "offset": 0,
      "framerate": 25.0,
      "width": 1280,
      "height": 720,
      "fragments": [
        {
          "start": 0,
          "duration": 2,
          "interval": 2,
          "events": [
            [  
              {
                "id": 1,
                "x": 0.306415737,
                "y": 0.03199235,
                "width": 0.15357475,
                "height": 0.322126418
              },
              {
                "id": 2,
                "x": 0.5625317,
                "y": 0.0868245438,
                "width": 0.149155334,
                "height": 0.355517566
              }
            ]
          ]
        },

... 省略

### <a name="redact-mode"></a>編集モード
ワークフローの&2; 番目のパスでは、単一の資産に結合する必要のある大量の入力を受け取ります。

これには、ぼかす対象となる Id の一覧、元のビデオ、JSON の注釈が含まれます。 このモードでは、注釈を使用して入力ビデオにぼかし効果を適用します。

Analyze パスからの出力は、元のビデオを含みません。 ビデオは、編集モードのタスクの入力資産にアップロードし、プライマリ ファイルとして選択する必要があります。

| 段階 | ファイル名 | メモ |
| --- | --- | --- |
| 入力資産 |foo.bar |WMV、MPV、MP4 形式のビデオ 手順 1 と同じビデオです。 |
| 入力資産 |foo_annotations.json |フェーズ&1; からの注釈メタデータ ファイルで、変更可能です。 |
| 入力資産 |foo_IDList.txt (Optional) |行で区切られた、編集する顔 ID の新しい一覧です (オプション)。 空白の場合、すべての顔をぼかします。 |
| 入力 config |ジョブ構成プリセット |{'version':'1.0', 'options': {'mode':'redact'}} |
| 出力資産 |foo_redacted.mp4 |注釈に基づいてぼかし効果を適用したビデオ |

#### <a name="example-output"></a>出力例
これは&1; つの ID を選択した場合の IDList からの出力です。

[ビデオ](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fad6e24a2-4f9c-46ee-9fa7-bf05e20d19ac%2Fdance_redacted1.mp4)

## <a name="attribute-descriptions"></a>属性の説明
Redaction MP は、高精度の顔位置検出と追跡を行い、ビデオ フレーム内で最大 64 個の人の顔を検出できます。 顔が正面を向いているときに最善の結果が得られ、横顔や小さい顔 (24 x 24 ピクセル以下) のときは精度が低下することがあります。

検出されて追跡される顔に対しては、画像内での顔の位置を示す座標と、追跡対象の個人を識別する顔 ID 番号が返されます。 顔 ID 番号は、前向きの顔が失われたりフレーム内で重なったりするとリセットされる場合があり、同じ顔に複数の ID が割り当てられる可能性があります。

属性の詳細については、「[Azure Media Analytics での顔と感情の検出](media-services-face-and-emotion-detection.md)」を参照してください。

## <a name="sample-code"></a>サンプル コード
このプログラムでは次の方法を示します。

1. 資産を作成し、その資産にメディア ファイルをアップロードします。
2. 次の JSON プリセットを含む構成ファイルに基づく顔編集タスクのジョブを作成します。 
   
        {'version':'1.0', 'options': {'mode':'combined'}}
3. 出力 JSON ファイルをダウンロードします。 
   
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
   
        namespace FaceRedaction
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
   
                // Field for service context.
                private static CloudMediaContext _context = null;
                private static MediaServicesCredentials _cachedCredentials = null;
   
                static void Main(string[] args)
                {
   
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the cached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
   
                    // Run the FaceRedaction job.
                    var asset = RunFaceRedactionJob(@"C:\supportFiles\FaceRedaction\SomeFootage.mp4",
                                                @"C:\supportFiles\FaceRedaction\config.json");
   
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\FaceRedaction\Output");
                }
   
                static IAsset RunFaceRedactionJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Face Redaction Input Asset",
                        AssetCreationOptions.None);
   
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Face Redaction Job");
   
                    // Get a reference to Azure Media Redactor.
                    string MediaProcessorName = "Azure Media Redactor";
   
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
   
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
   
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Face Redaction Task",
                        processor,
                        configuration,
                        TaskOptions.None);
   
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
   
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Face Redaction Output Asset", AssetCreationOptions.None);
   
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

## <a name="next-step"></a>次のステップ
Media Services のラーニング パスを確認します。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>関連リンク
[Azure Media Services Analytics の概要](media-services-analytics-overview.md)

[Azure Media Analytics デモ](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)


