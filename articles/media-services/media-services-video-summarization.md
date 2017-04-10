---
title: "Azure Media Video Thumbnails を使用してビデオ要約を作成する | Microsoft Docs"
description: "ビデオ要約では、ソース ビデオから興味深いスニペットが自動的に選択されるので、長いビデオの要約を簡単に作成することができます。 これは、長いビデオにおいて予定されている内容の概要をすばやく提供する場合に便利です。"
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: a245529f-3150-4afc-93ec-e40d8a6b761d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/16/2017
ms.author: milanga;juliako;
translationtype: Human Translation
ms.sourcegitcommit: 343658944394e7b620bc70aa0d92affada07e91d
ms.openlocfilehash: 7510c8ab4adadbd7d738ba0b8e2bbdddba8d1048
ms.lasthandoff: 02/18/2017


---
# <a name="use-azure-media-video-thumbnails-to-create-a-video-summarization"></a>Azure Media Video Thumbnails を使用してビデオ要約を作成する
## <a name="overview"></a>概要
**Azure Media Video Thumbnails** メディア プロセッサ (MP) では、長いビデオの要約をプレビューするだけのお客様に役に立つビデオの要約を作成することができます。 たとえば、サムネイルにマウス ポインターを合わせたときに、お客者は短い "要約ビデオ" を参照できます。 構成プリセットを通じて **Azure Media Video Thumbnails** のパラメーターを調整することにより、MP の強力なショット検出と連結テクノロジを使用して、わかりやすいサブクリップをアルゴリズムに生成できます。  

**Azure Media Video Thumbnail** MP は現在プレビュー段階です。

このトピックでは、**Azure Media Video Thumbnail** の詳細と、Media Services SDK for .NET での使用方法について説明します。

## <a name="limitations"></a>制限事項

場合によっては、ビデオが異なるシーンで構成されていない場合、出力はシングル ショットのみになります。

## <a name="video-summary-example"></a>ビデオ要約の例
次に Azure Media Video Thumbnails メディア プロセッサで実行できる例をいくつか示します。

### <a name="original-video"></a>元のビデオ
[元のビデオ](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Faed33834-ec2d-4788-88b5-a4505b3d032c%2FMicrosoft%27s%20HoloLens%20Live%20Demonstration.ism%2Fmanifest)

### <a name="video-thumbnail-result"></a>ビデオのサムネイル結果
[ビデオのサムネイル結果](http://ampdemo.azureedge.net/azuremediaplayer.html?url=http%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Ff5c91052-4232-41d4-b531-062e07b6a9ae%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="task-configuration-preset"></a>タスクの構成 (プリセット)
**Azure Media Video Thumbnails**でビデオのサムネイル タスクを作成するときは、構成プリセットを指定する必要があります。 上記のサムネイル サンプルは、次の基本的な JSON 構成で作成されました。

    {"version":"1.0"}

現在、次のパラメーターを変更できます。

| Param | Description |
| --- | --- |
| outputAudio |結果ビデオにオーディオが含まれているかどうかを指定します。 <br/>使用可能な値: True または False。 既定値は True です。 |
| fadeInFadeOut |フェードの遷移が独立したモーションのサムネイル間で使用されているかどうかを指定します。  <br/>使用可能な値: True または False。  既定値は True です。 |
| maxMotionThumbnailDurationInSecs |結果ビデオ全体の長さを指定する整数。  既定値は、元のビデオの再生時間によって異なります。 |

次の表に、 **maxMotionThumbnailInSecs** が使用されていない場合の既定の再生時間を示します。

|  |  |  |
| --- | --- | --- | --- | --- |
| ビデオの再生時間 |d < 3 分 |3 分 < d < 15 分 |
| サムネイルの再生時間 |15 秒 (2 ～&3; シーン) |30 秒 (3 ～&5; シーン) |

次の JSON では、使用可能なパラメーターを設定します。

    {
        "version": "1.0",
        "options": {
            "outputAudio": "true",
            "maxMotionThumbnailDurationInSecs": "10",
            "fadeInFadeOut": "true"
        }
    }

## <a name="sample-code"></a>サンプル コード
このプログラムでは次の方法を示します。

1. 資産を作成し、その資産にメディア ファイルをアップロードします。
2. 次の JSON プリセットを含む構成ファイルに基づくビデオ サムネイル タスクのジョブを作成します。 
   
        {                
            "version": "1.0",
            "options": {
                "outputAudio": "true",
                "maxMotionThumbnailDurationInSecs": "30",
                "fadeInFadeOut": "false"
            }
        }
3. 出力ファイルをダウンロードします。 

### <a name="net-code"></a>.NET コード
    using System;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Threading;
    using System.Threading.Tasks;

    namespace VideoSummarization
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


                // Run the thumbnail job.
                var asset = RunVideoThumbnailJob(@"C:\supportFiles\VideoThumbnail\BigBuckBunny.mp4",
                                            @"C:\supportFiles\VideoThumbnail\config.json");

                // Download the job output asset.
                DownloadAsset(asset, @"C:\supportFiles\VideoThumbnail\Output");
            }

            static IAsset RunVideoThumbnailJob(string inputMediaFilePath, string configurationFile)
            {
                // Create an asset and upload the input media file to storage.
                IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                    "My Video Thumbnail Input Asset",
                    AssetCreationOptions.None);

                // Declare a new job.
                IJob job = _context.Jobs.Create("My Video Thumbnail Job");

                // Get a reference to Azure Media Video Thumbnails.
                string MediaProcessorName = "Azure Media Video Thumbnails";

                var processor = GetLatestMediaProcessorByName(MediaProcessorName);

                // Read configuration from the specified file.
                string configuration = File.ReadAllText(configurationFile);

                // Create a task with the encoding details, using a string preset.
                ITask task = job.Tasks.AddNew("My Video Thumbnail Task",
                    processor,
                    configuration,
                    TaskOptions.None);

                // Specify the input asset.
                task.InputAssets.Add(asset);

                // Add an output asset to contain the results of the job.
                task.OutputAssets.AddNew("My Video Thumbnail Output Asset", AssetCreationOptions.None);

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

### <a name="video-thumbnail-output"></a>ビデオのサムネイル出力
[ビデオのサムネイル出力](http://ampdemo.azureedge.net/azuremediaplayer.html?url=http%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Fd06f24dc-bc81-488e-a8d0-348b7dc41b56%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>関連リンク
[Azure Media Services Analytics の概要](media-services-analytics-overview.md)

[Azure Media Analytics デモ](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)


