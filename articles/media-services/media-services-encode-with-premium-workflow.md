---
title: "Media Encoder Premium ワークフローでの高度なエンコード | Microsoft Docs"
description: "メディア エンコーダー プレミアム ワークフローでエンコードする方法について説明します。 コード サンプルは C# で記述され、Media Services SDK for .NET を利用しています。"
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 0f4c87ac-810a-4d42-8df8-923dff2016c6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 6dcc79a2adf81c82d245c99116f28eb4db983396
ms.lasthandoff: 12/08/2016


---
# <a name="advanced-encoding-with-media-encoder-premium-workflow"></a>メディア エンコーダー プレミアム ワークフローでの高度なエンコード
> [!NOTE]
> このトピックで説明するメディア エンコーダー プレミアム ワークフロー メディア プロセッサは中国では提供されません。
>
>

プレミアム エンコーダーに関する質問がある場合は、mepd@microsoft.com まで電子メールでお送りください。

## <a name="overview"></a>概要
Microsoft Azure Media Services には、 **メディア エンコーダー プレミアム ワークフロー** メディア プロセッサが導入されています。 このプロセッサでは、プレミアム オンデマンド ワークフローの高度なエンコード機能が提供されます。

次のトピックでは、 **メディア エンコーダー プレミアム ワークフロー**に関連する詳細の概要を説明します。

* [メディア エンコーダー プレミアム ワークフローでサポートされる形式](media-services-premium-workflow-encoder-formats.md) – **メディア エンコーダー プレミアム ワークフロー**でサポートされるファイルの形式とコーデックについて説明します。
* 「[Azure オンデマンド メディア エンコーダーの概要と比較](media-services-encode-asset.md)」では、**Media Encoder Premium Workflow** と **Media Encoder Standard** のエンコード機能を比較しています。

このトピックでは、.NET を使用して **メディア エンコーダー プレミアム ワークフロー** でエンコードする方法を示します。

**メディア エンコーダー プレミアム ワークフロー** のエンコード タスクには、ワークフロー ファイルと呼ばれる別の構成ファイルが必要です。 これらのファイルは .workflow 拡張子を持ち、 [ワークフロー デザイナー](media-services-workflow-designer.md) ツールで作成されます。

## <a name="encode"></a>エンコード
**メディア エンコーダー プレミアム ワークフロー** のエンコード タスクには、ワークフロー ファイルと呼ばれる別の構成ファイルが必要です。 これらのファイルは .workflow 拡張子を持ち、 [ワークフロー デザイナー](media-services-workflow-designer.md) ツールで作成されます。

既定のワークフロー ファイルは [こちら](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)から入手することもできます。 フォルダーにはこれらのファイルの説明も含まれています。

ワークフロー ファイルは、アセットとして Media Services アカウントにアップロードする必要があり、このアセットをエンコード タスクに渡す必要があります。

次の例では、 **メディア エンコーダー プレミアム ワークフロー**でエンコードする方法を示しています。

次の手順を実行します。

1. アセットを作成し、ワークフロー ファイルをアップロードします。
2. アセットを作成し、ソース メディア ファイルをアップロードします。
3. "メディア エンコーダー プレミアム ワークフロー" メディア プロセッサを取得します。
4. ジョブとタスクを作成します。

    ほとんどの場合、次の例と同様にタスクの構成文字列は空です。 (ランタイム プロパティを動的に設定する必要がある) 高度なシナリオでは、エンコード タスクに XML 文字列を提供します。 このようなシナリオの例として、オーバーレイの作成、メディアの並列または順次合成、字幕の作成があります。
5. 2 つの入力アセットをタスクに追加します。

    a. 1 – ワークフロー アセット。

    b. 2 - ビデオ アセット。

    **注**: ワークフロー アセットは、メディア アセットの前にタスクに追加する必要あります。
   このタスクの構成文字列は空にする必要があります。
6. エンコード ジョブを送信します。

完全な例を次に示します。 Media Services の .NET 開発を使用して設定する方法の詳細については、「 [Media Services development with .NET (.NET での Media Services 開発)](media-services-dotnet-how-to-use.md)」を参照してください

     using System;
    using System.Linq;
    using System.Configuration;
    using System.IO;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.MediaServices.Client;

    namespace MediaEncoderPremiumWorkflowSample
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

            private static readonly string _supportFiles =
                Path.GetFullPath(@"../..\Media");

            private static readonly string _workflowFilePath =
                Path.GetFullPath(_supportFiles + @"\H264 Progressive Download MP4.workflow");

            private static readonly string _singleMP4InputFilePath =
                Path.GetFullPath(_supportFiles + @"\BigBuckBunny.mp4");


            static void Main(string[] args)
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);

                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                var workflowAsset = CreateAssetAndUploadSingleFile(_workflowFilePath);
                var videoAsset = CreateAssetAndUploadSingleFile(_singleMP4InputFilePath);
                IAsset outputAsset = CreateEncodingJob(workflowAsset, videoAsset);

            }

            static public IAsset CreateAssetAndUploadSingleFile(string singleFilePath)
            {
                var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
                var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);

                var fileName = Path.GetFileName(singleFilePath);

                var assetFile = asset.AssetFiles.Create(fileName);

                Console.WriteLine("Created assetFile {0}", assetFile.Name);

                Console.WriteLine("Upload {0}", assetFile.Name);

                assetFile.Upload(singleFilePath);
                Console.WriteLine("Done uploading {0}", assetFile.Name);

                return asset;
            }

            static public IAsset CreateEncodingJob(IAsset workflow, IAsset video)
            {
                // Declare a new job.
                IJob job = _context.Jobs.Create("Premium Workflow encoding job");
                // Get a media processor reference, and pass to it the name of the
                // processor to use for the specific task.
                IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");

                // Create a task with the encoding details, using a string preset.
                ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                    processor,
                    "",
                    TaskOptions.None);

                // Specify the input asset to be encoded.
                task.InputAssets.Add(workflow);
                task.InputAssets.Add(video); // we add one asset
                // Add an output asset to contain the results of the job.
                // This output is specified as AssetCreationOptions.None, which
                // means the output asset is not encrypted.
                task.OutputAssets.AddNew("Output asset",
                    AssetCreationOptions.None);

                // Use the following event handler to check job progress.  
                job.StateChanged += new
                        EventHandler<JobStateChangedEventArgs>(StateChanged);

                // Launch the job.
                job.Submit();

                // Check job execution and wait for job to finish.
                Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
                progressJobTask.Wait();

                // If job state is Error the event handling
                // method for job progress should log errors.  Here we check
                // for error state and exit if needed.
                if (job.State == JobState.Error)
                {
                    throw new Exception("\nExiting method due to job error.");
                }

                return job.OutputMediaAssets[0];
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
                        LogJobStop(job.Id);
                        break;
                    default:
                        break;
                }
            }

            static private void LogJobStop(string jobId)
            {
                StringBuilder builder = new StringBuilder();
                IJob job = _context.Jobs.Where(j => j.Id == jobId).FirstOrDefault();

                builder.AppendLine("\nThe job stopped due to cancellation or an error.");
                builder.AppendLine("***************************");
                builder.AppendLine("Job ID: " + job.Id);
                builder.AppendLine("Job Name: " + job.Name);
                builder.AppendLine("Job State: " + job.State.ToString());
                builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
                builder.AppendLine("Media Services account name: " + _mediaServicesAccountName);
                // Log job errors if they exist.  
                if (job.State == JobState.Error)
                {
                    builder.Append("Error Details: \n");
                    foreach (ITask task in job.Tasks)
                    {
                        foreach (ErrorDetail detail in task.ErrorDetails)
                        {
                            builder.AppendLine("  Task Id: " + task.Id);
                            builder.AppendLine("    Error Code: " + detail.Code);
                            builder.AppendLine("    Error Message: " + detail.Message + "\n");
                        }
                    }
                }
                builder.AppendLine("***************************\n");

                Console.Write(builder.ToString());
            }


            static private IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
            {
                var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

                if (processor == null)
                    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

                return processor;
            }
        }
    }


プレミアム エンコーダーに関する質問がある場合は、mepd@microsoft.com まで電子メールでお送りください。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

