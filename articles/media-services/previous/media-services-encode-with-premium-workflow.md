---
title: Media Encoder Premium ワークフローでの高度なエンコード | Microsoft Docs
description: メディア エンコーダー プレミアム ワークフローでエンコードする方法について説明します。 コード サンプルは C# で記述され、Media Services SDK for .NET を利用しています。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: b58a1ae2a20afb357f6363077e70b692256d2ce7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013501"
---
# <a name="advanced-encoding-with-media-encoder-premium-workflow"></a>メディア エンコーダー プレミアム ワークフローでの高度なエンコード

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> この記事で説明する Media Encoder Premium Workflow メディア プロセッサは中国では提供されません。
>
>

## <a name="overview"></a>概要
Microsoft Azure Media Services には、 **メディア エンコーダー プレミアム ワークフロー** メディア プロセッサが導入されています。 このプロセッサでは、プレミアム オンデマンド ワークフローの高度なエンコード機能が提供されます。

次のトピックでは、 **メディア エンコーダー プレミアム ワークフロー** に関連する詳細の概要を説明します。

* [メディア エンコーダー プレミアム ワークフローでサポートされる形式](./media-services-encode-asset.md) – **メディア エンコーダー プレミアム ワークフロー** でサポートされるファイルの形式とコーデックについて説明します。
* 「[Azure オンデマンド メディア エンコーダーの概要と比較](media-services-encode-asset.md)」では、**Media Encoder Premium Workflow** と **Media Encoder Standard** のエンコード機能を比較しています。

この記事では、.NET を使って **Media Encoder Premium Workflow** でエンコードする方法を示します。

**メディア エンコーダー プレミアム ワークフロー** のエンコード タスクには、ワークフロー ファイルと呼ばれる別の構成ファイルが必要です。 これらのファイルは .workflow 拡張子を持ち、 [ワークフロー デザイナー](media-services-workflow-designer.md) ツールで作成されます。

既定のワークフロー ファイルは [こちら](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)から入手することもできます。 フォルダーにはこれらのファイルの説明も含まれています。

ワークフロー ファイルは、アセットとして Media Services アカウントにアップロードする必要があり、このアセットをエンコード タスクに渡す必要があります。

## <a name="create-and-configure-a-visual-studio-project"></a>Visual Studio プロジェクトの作成と構成

「[.NET を使用した Media Services 開発](media-services-dotnet-how-to-use.md)」の説明に従って、開発環境をセットアップし、app.config ファイルに接続情報を指定します。 

## <a name="encoding-example"></a>エンコードの例

次の例では、 **メディア エンコーダー プレミアム ワークフロー** でエンコードする方法を示しています。

次の手順を実行します。

1. アセットを作成し、ワークフロー ファイルをアップロードします。
2. アセットを作成し、ソース メディア ファイルをアップロードします。
3. "メディア エンコーダー プレミアム ワークフロー" メディア プロセッサを取得します。
4. ジョブとタスクを作成します。

    ほとんどの場合、次の例と同様にタスクの構成文字列は空です。 (ランタイム プロパティを動的に設定する必要がある) 高度なシナリオでは、エンコード タスクに XML 文字列を提供します。 このようなシナリオの例として、オーバーレイの作成、メディアの並列または順次合成、字幕の作成があります。
5. 2 つの入力アセットをタスクに追加します。

   1. 1 – ワークフロー アセット。
   2. 2 - ビデオ アセット。

      >[!NOTE]
      >ワークフロー アセットは、メディア アセットの前にタスクに追加する必要があります。
      このタスクの構成文字列は空にする必要があります。
   
6. エンコード ジョブを送信します。

```csharp
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.MediaServices.Client;

namespace MediaEncoderPremiumWorkflowSample
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

        private static readonly string _supportFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _workflowFilePath =
            Path.GetFullPath(_supportFiles + @"\H264 Progressive Download MP4.workflow");

        private static readonly string _singleMP4InputFilePath =
            Path.GetFullPath(_supportFiles + @"\BigBuckBunny.mp4");

        static void Main(string[] args)
        {

            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

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
                    // LogJobStop(job.Id);
                    break;
                default:
                    break;
            }
        }
        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }
    }
}
```

## <a name="need-help"></a>お困りの際は、

[[新しいサポート リクエスト]](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) に移動してサポート チケットを開くことができます

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]