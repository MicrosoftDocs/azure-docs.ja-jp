---
title: .NET を使用した Media Encoder Standard での資産のエンコード | Microsoft Docs
description: この記事では、.NET を使用して、Media Encoder Standard で資産をエンコードする方法について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 03431b64-5518-478a-a1c2-1de345999274
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: anilmur
ms.custom: devx-track-csharp
ms.openlocfilehash: b94ec0b50182381d2a4e1bca09f4c8bc482d13ee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014096"
---
# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>.NET を使用した Media Encoder Standard での資産のエンコード

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

エンコード ジョブは Media Services で最も一般的な処理の 1 つです。 エンコード ジョブを作成することで、メディア ファイルをあるエンコードから別のエンコードに変換できるようになります。 エンコードには、Media Services の組み込み Media Encoder を使用できます。 Media Services のパートナーから提供されているエンコーダーを使うこともできます。サードパーティのエンコーダーは Azure Marketplace から入手できます。 

この記事では、.NET を使用して、Media Encoder Standard (MES) で資産をエンコードする方法について説明します。 Media Encoder Standard は、 [ここ](./media-services-mes-presets-overview.md)で説明されているエンコーダーのプリセット文字列のいずれかを使用して構成されます。

ソース ファイルは常にアダプティブ ビットレート MP4 セットにエンコードして、その後、[ダイナミック パッケージ](media-services-dynamic-packaging-overview.md)を使用して目的の形式に変換することをお勧めします。 

出力資産がストレージで暗号化されている場合は、資産配信のポリシーを構成する必要があります。 詳細については、「[方法: アセットの配信ポリシーを構成する](media-services-dotnet-configure-asset-delivery-policy.md)」を参照してください。

> [!NOTE]
> MES は、入力ファイル名の最初の 32 文字を含む名前で出力ファイルを生成します。 この名前は、プリセット ファイルに指定されているものに基づきます。 たとえば、"FileName": "{Basename}_{Index}{Extension}" の場合、 {Basename} が入力ファイル名の最初の 32 文字で置換されます。
> 
> 

### <a name="mes-formats"></a>MES の形式
[形式とコーデック](media-services-media-encoder-standard-formats.md)

### <a name="mes-presets"></a>MES のプリセット
Media Encoder Standard は、 [ここ](./media-services-mes-presets-overview.md)で説明されているエンコーダーのプリセット文字列のいずれかを使用して構成されます。

### <a name="input-and-output-metadata"></a>入力メタデータと出力メタデータ
MES を使用して 1 つ (または複数) の入力資産をエンコードし、そのエンコード タスクが正常に終了すると、出力資産が得られます。 出力資産には、使用するエンコード プリセットに基づいて、ビデオ、オーディオ、サムネイル、マニフェストなどが含まれます。

この出力資産には、入力資産に関するメタデータが格納されたファイルも含まれます。 メタデータ XML ファイルの名前は、<asset_id>_metadata.xml という形式になっています (たとえば、41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml)。ここで、<asset_id> は入力資産の AssetId の値です。 この入力メタデータ XML のスキーマについては、[こちら](media-services-input-metadata-schema.md)で説明されています。

出力資産には、出力資産に関するメタデータが格納されたファイルも含まれます。 メタデータ XML ファイルの名前は、<source_file_name>_manifest.xml という形式になっています (たとえば、BigBuckBunny_manifest.xml)。 この出力メタデータ XML のスキーマについては、 [こちら](media-services-output-metadata-schema.md)で説明されています。

2 つのメタデータ ファイルのいずれかを確認したい場合は、SAS ロケータ―を作成してファイルをローカル コンピューターにダウンロードできます。 Media Services .NET SDK Extensions を使って SAS ロケータ―を作成し、ファイルをダウンロードする方法の例をご覧ください。

## <a name="download-sample"></a>サンプルのダウンロード
MES を使用したエンコード方法を示すサンプルを[こちら](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/)から取得して実行できます。

## <a name="net-sample-code"></a>.NET サンプル コード

次のコード サンプルでは、Media Services SDK を使用して次のタスクを実行します。

* エンコード ジョブを作成します。
* Media Encoder Standard エンコーダーの参照を取得します。
* [アダプティブ ストリーミング](media-services-autogen-bitrate-ladder-with-mes.md) プリセットを使用するように指定します。 
* 1 つのエンコード タスクをジョブに追加します。 
* エンコードする入力資産を指定します。
* エンコードされた資産が含まれる出力資産を作成します。
* ジョブの進行状況を確認するイベント ハンドラーを追加します。
* ジョブを送信します。

#### <a name="create-and-configure-a-visual-studio-project"></a>Visual Studio プロジェクトの作成と構成

「[.NET を使用した Media Services 開発](media-services-dotnet-how-to-use.md)」の説明に従って、開発環境をセットアップし、app.config ファイルに接続情報を指定します。 

#### <a name="example"></a>例 

```csharp
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using Microsoft.WindowsAzure.MediaServices.Client;

namespace MediaEncoderStandardSample
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

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);


            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the output using the "Adaptive Streaming" preset.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Create a task with the encoding details, using a string preset.
            // In this case "Adaptive Streaming" preset is used.
            ITask task = job.Tasks.AddNew("My encoding task",
                processor,
                "Adaptive Streaming",
                TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }

        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
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

## <a name="advanced-encoding-features-to-explore"></a>高度なエンコーディング機能の詳細
* [サムネイルを生成する方法](media-services-dotnet-generate-thumbnail-with-mes.md)
* [エンコード中のサムネイルの生成](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [エンコード中にビデオをトリミングする](media-services-crop-video.md)
* [エンコード プリセットをカスタマイズする](media-services-custom-mes-presets-with-dotnet.md)
* [ビデオと画像を重ね合わせる、または透かしを入れる](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>次のステップ
[Media Encoder Standard と .NET を使用してサムネイルを生成する方法](media-services-dotnet-generate-thumbnail-with-mes.md)
[Media Services でのエンコーディングの概要](media-services-encode-asset.md)