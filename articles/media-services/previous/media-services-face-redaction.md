---
title: Azure Media Analytics で顔を編集する | Microsoft Docs
description: Azure Media Redactor は、クラウドでスケーラブルな顔編集を提供する Azure Media Analytics メディア プロセッサです。 この記事では、Azure Media Analytics で顔を編集する方法を示します。
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 6a1b7a76ef1efda51f09ac733b3d434235ff40ef
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74900299"
---
# <a name="redact-faces-with-azure-media-analytics"></a>Azure Media Analytics で顔を編集する 
## <a name="overview"></a>概要
**Azure Media Redactor** は、クラウドでスケーラブルな顔編集を提供する [Azure Media Analytics](media-services-analytics-overview.md) メディア プロセッサ (MP) です。 顔編集では、ビデオを編集して選択した個人の顔をぼかすことができます。 顔編集サービスは、公共の安全やニュース媒体などに使用していただけます。 複数人の顔を含んでいる映像の場合、顔編集を手作業で行うと数分の映像でも数時間かかりますが、このサービスを使えば数ステップの簡単な手順で完了します。 詳細については、[こちらの投稿](https://azure.microsoft.com/blog/azure-media-redactor/)を参照してください。

ここでは、**Azure Media Redactor** の詳細と、Media Services SDK for .NET での使用方法について説明します。

## <a name="face-redaction-modes"></a>顔編集モード
顔編集は、ビデオのフレームごとに顔を検出し、その顔オブジェクトを時間軸の前後にわたって追跡することで、同一の人間を他の角度からも処理します。 自動修正のプロセスは複雑で、常に 100% 満足のいく結果になるとは限りません。そのため、Media Analytics には最終的なアウトプットを変更する方法がいくつか用意されています。

完全な自動モードに加え、2 パス ワークフローというものがあり、これによって、検出された顔を ID リストを使って選択または選択解除することができます。 また、任意のフレームごとの調整をするには、JSON 形式でメタデータ ファイルを使用します。 このワークフローは、**分析**モードと**編集**モードに分かれています。 単一のパスに 2 つのモードを結合して、両方のタスクを 1 つのジョブで実行します。このモードは**結合**と呼ばれます。

### <a name="combined-mode"></a>結合モード
手作業なしで、自動的に修正された mp4 が生成されます。

| 段階 | ファイル名 | メモ |
| --- | --- | --- |
| 入力資産 |foo.bar |WMV、MPV、MP4 形式のビデオ |
| 入力 config |ジョブ構成プリセット |{'version':'1.0', 'options': {'mode':'combined'}} |
| 出力資産 |foo_redacted.mp4 |ぼかしが適用されたビデオ |

#### <a name="input-example"></a>入力例:
[ビデオ](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fed99001d-72ee-4f91-9fc0-cd530d0adbbc%2FDancing.mp4)

#### <a name="output-example"></a>出力例:
[ビデオ](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc6608001-e5da-429b-9ec8-d69d8f3bfc79%2Fdance_redacted.mp4)

### <a name="analyze-mode"></a>分析モード
2 パス ワークフローの **分析** パスでは、ビデオ入力を受け取り、顔の位置の JSON ファイルと、検出された顔それぞれの jpg イメージを生成します。

| 段階 | ファイル名 | メモ |
| --- | --- | --- |
| 入力資産 |foo.bar |WMV、MPV、MP4 形式のビデオ |
| 入力 config |ジョブ構成プリセット |{'version':'1.0', 'options': {'mode':'analyze'}} |
| 出力資産 |foo_annotations.json |JSON 形式での、顔の位置の注釈データです。 ユーザー編集によりぼかし枠を変更することができます。 以下のサンプルを参照してください。 |
| 出力資産 |foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |検出された顔それぞれをトリミングした jpg (数字は顔の labelId を示す) |

#### <a name="output-example"></a>出力例:

```json
    {
      "version": 1,
      "timescale": 24000,
      "offset": 0,
      "framerate": 23.976,
      "width": 1280,
      "height": 720,
      "fragments": [
        {
          "start": 0,
          "duration": 48048,
          "interval": 1001,
          "events": [
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [
              {
                "index": 13,
                "id": 1138,
                "x": 0.29537,
                "y": -0.18987,
                "width": 0.36239,
                "height": 0.80335
              },
              {
                "index": 13,
                "id": 2028,
                "x": 0.60427,
                "y": 0.16098,
                "width": 0.26958,
                "height": 0.57943
              }
            ],

    … truncated
```

### <a name="redact-mode"></a>編集モード
ワークフローの 2 番目のパスでは、単一の資産に結合する必要のある大量の入力を受け取ります。

これには、ぼかす対象となる Id の一覧、元のビデオ、JSON の注釈が含まれます。 このモードでは、注釈を使用して入力ビデオにぼかし効果を適用します。

Analyze パスからの出力は、元のビデオを含みません。 ビデオは、編集モードのタスクの入力資産にアップロードし、プライマリ ファイルとして選択する必要があります。

| 段階 | ファイル名 | メモ |
| --- | --- | --- |
| 入力資産 |foo.bar |WMV、MPV、MP4 形式のビデオ 手順 1 と同じビデオです。 |
| 入力資産 |foo_annotations.json |フェーズ 1 からの注釈メタデータ ファイルで、変更可能です。 |
| 入力資産 |foo_IDList.txt (Optional) |行で区切られた、編集する顔 ID の新しい一覧です (オプション)。 空白の場合、すべての顔をぼかします。 |
| 入力 config |ジョブ構成プリセット |{'version':'1.0', 'options': {'mode':'redact'}} |
| 出力資産 |foo_redacted.mp4 |注釈に基づいてぼかし効果を適用したビデオ |

#### <a name="example-output"></a>出力例
これは 1 つの ID を選択した場合の IDList からの出力です。

[ビデオ](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fad6e24a2-4f9c-46ee-9fa7-bf05e20d19ac%2Fdance_redacted1.mp4)

foo_IDList.txt の例
 
     1
     2
     3

## <a name="blur-types"></a>ぼかしの種類

**Combined** または **Redact** モードでは、JSON 入力構成で選択できる **Low**、**Med**、**High**、**Box**、**Black** の 5 種類のぼかしモードがあります。 既定では **Med** が使用されます。

ぼかしの種類のサンプルを以下に示します。

### <a name="example-json"></a>サンプル JSON:

```json
    {'version':'1.0', 'options': {'Mode': 'Combined', 'BlurType': 'High'}}
```

#### <a name="low"></a>低

![低](./media/media-services-face-redaction/blur1.png)
 
#### <a name="med"></a>Med

![Med](./media/media-services-face-redaction/blur2.png)

#### <a name="high"></a>高

![高](./media/media-services-face-redaction/blur3.png)

#### <a name="box"></a>Box

![Box](./media/media-services-face-redaction/blur4.png)

#### <a name="black"></a>Black

![Black](./media/media-services-face-redaction/blur5.png)

## <a name="elements-of-the-output-json-file"></a>出力 JSON ファイルの要素

Redaction MP は、高精度の顔位置検出と追跡を行い、ビデオ フレーム内で最大 64 個の人の顔を検出できます。 顔が正面を向いているときに最善の結果が得られ、横顔や小さい顔 (24 x 24 ピクセル以下) のときは精度が低下することがあります。

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>.NET サンプル コード

このプログラムでは次の方法を示します。

1. 資産を作成し、その資産にメディア ファイルをアップロードします。
2. 次の JSON プリセットを含む構成ファイルに基づく顔編集タスクのジョブを作成します。 

    ```json
            {
                'version':'1.0',
                'options': {
                    'mode':'combined'
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

namespace FaceRedaction
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
```

## <a name="next-steps"></a>次の手順

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>関連リンク
[Azure Media Services Analytics の概要](media-services-analytics-overview.md)

[Azure Media Analytics デモ](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

