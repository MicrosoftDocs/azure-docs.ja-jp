---
title: Azure Media Analytics OCR でテキストをデジタル化する | Microsoft Docs
description: Azure Media Analytics OCR (光学式文字認識) では、ビデオ ファイル内のテキスト コンテンツを編集かつ検索可能なデジタル テキストに変換できます。  これにより、メディアのビデオ信号から有意なメタデータを自動的に抽出することができます。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 307c196e-3a50-4f4b-b982-51585448ffc6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 91fad34073d7505c596bedfb6c93946ee7393dd7
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58315132"
---
# <a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>Azure Media Analytics を使用して、ビデオ ファイル内のテキスト コンテンツをデジタル テキストに変換する  
## <a name="overview"></a>概要
ビデオ ファイルからテキスト コンテンツを抽出し、編集かつ検索可能なデジタル テキストを生成する必要がある場合は、Azure Media Analytics OCR (光学式文字認識) を使用する必要があります。 この Azure メディア プロセッサは、ビデオ ファイル内のテキスト コンテンツを検出し、テキスト ファイルを生成して使用できるようにします。 OCR を使用すると、メディアのビデオ信号から有意なメタデータを自動的に抽出できます。

検索エンジンと組み合わせれば、テキストを使って簡単にメディアにインデックスを作成できるため、コンテンツがさらに探しやすくなります。 これは、ビデオ記録やスライドショー プレゼンテーションの画面キャプチなど、テキスト情報の多いビデオでは非常に便利です。 Azure OCR メディア プロセッサは、デジタル テキスト用に最適化されています。

**Azure Media OCR** メディア プロセッサは現在プレビュー段階です。

この記事では、**Azure Media OCR** の詳細と、Media Services SDK for .NET での使用方法について説明します。 詳細や例については、[こちらのブログ](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/)をご覧ください。

## <a name="ocr-input-files"></a>OCR 入力ファイル
ビデオ ファイルです。 現在サポートされている形式は MP4、MOV、WMV です。

## <a name="task-configuration"></a>タスクの構成
タスクの構成 (プリセット) です。 **Azure Media OCR**でタスクを作成するときは、JSON または XML を使用して、構成プリセットを指定する必要があります。 

>[!NOTE]
>OCR エンジンが有効な入力として処理できるのは、高さと幅の両方が最小 40 ピクセルから最大 32000 ピクセルまでのイメージ領域のみです。
>

### <a name="attribute-descriptions"></a>属性の説明
| 属性名 | 説明 |
| --- | --- |
|AdvancedOutput| AdvancedOutput を true に設定すると、JSON 出力には各単語ごとに (語句とリージョンに加えて) 位置指定データが含まれます。 これらの詳細を表示しないようにするには、フラグを false に設定します。 既定値は false です。 詳細については、 [このブログ](https://azure.microsoft.com/blog/azure-media-ocr-simplified-output/)をご覧ください。|
| 言語 |(省略可能) 検索対象テキストの言語です。 次のいずれか:AutoDetect (既定)、Arabic、ChineseSimplified、ChineseTraditional、Czech、Danish、Dutch、English、Finnish、French、German、Greek、Hungarian、Italian、Japanese、Korean、Norwegian、Polish、Portuguese、Romanian、Russian、SerbianCyrillic、SerbianLatin、Slovak、Spanish、Swedish、Turkish。 |
| TextOrientation |(省略可能) 検索対象テキストの向きです。  "Left" は、すべての文字の上部が左を指していることを意味します。  既定のテキスト (書籍など) は "Up"、つまり上を指しています。  次のいずれか:AutoDetect (既定)、Up、Right、Down、Left。 |
| TimeInterval |(省略可能) サンプリング レートです。  既定値は、毎 0.5 秒です。<br/>JSON 形式 – HH:mm:ss.SSS (既定値 00:00:00.500)<br/>XML 形式 – W3C XSD プリミティブ期間 (既定値 PT0.5) |
| DetectRegions |(省略可能)テキストを検出するビデオ フレーム内の領域を指定する DetectRegion オブジェクトの配列。<br/>DetectRegion オブジェクトは、次の 4 つの整数値で構成されます。<br/>Left: 左余白からのピクセル<br/>Top: 上余白からのピクセル<br/>Width: 領域の幅 (ピクセル)<br/>Height: 領域の高さ (ピクセル) |

#### <a name="json-preset-example"></a>JSON プリセットの例

```json
    {
        "Version":1.0, 
        "Options": 
        {
            "AdvancedOutput":"true",
            "Language":"English", 
            "TimeInterval":"00:00:01.5",
            "TextOrientation":"Up",
            "DetectRegions": [
                    {
                       "Left": 10,
                       "Top": 10,
                       "Width": 100,
                       "Height": 50
                    }
             ]
        }
    }
```

#### <a name="xml-preset-example"></a>XML プリセットの例

```xml
    <?xml version=""1.0"" encoding=""utf-16""?>
    <VideoOcrPreset xmlns:xsi=""https://www.w3.org/2001/XMLSchema-instance"" xmlns:xsd=""https://www.w3.org/2001/XMLSchema"" Version=""1.0"" xmlns=""https://www.windowsazure.com/media/encoding/Preset/2014/03"">
      <Options>
         <AdvancedOutput>true</AdvancedOutput>
         <Language>English</Language>
         <TimeInterval>PT1.5S</TimeInterval>
         <DetectRegions>
             <DetectRegion>
                   <Left>10</Left>
                   <Top>10</Top>
                   <Width>100</Width>
                   <Height>50</Height>
            </DetectRegion>
       </DetectRegions>
       <TextOrientation>Up</TextOrientation>
      </Options>
    </VideoOcrPreset>
```

## <a name="ocr-output-files"></a>OCR 出力ファイル
OCR メディア プロセッサの出力は、JSON ファイルです。

### <a name="elements-of-the-output-json-file"></a>出力 JSON ファイルの要素
ビデオ OCR 出力は、ビデオで検出された文字のデータを、時間に基づいてセグメント化して提供します。  分析中、言語、向きなどの属性を使用することで、興味のある単語に正確に焦点を合わせることができます。 

出力には、次の属性が含まれています。

| 要素 | 説明 |
| --- | --- |
| タイムスケール |ビデオの 1 秒あたりの "ティック数" |
| Offset |タイムスタンプの時間オフセット。 Video API のバージョン 1.0 では、これは常に 0 になります。 |
| framerate |ビデオの 1 秒あたりのフレーム数 |
| width |ビデオの幅 (ピクセル単位) |
| height |ビデオの高さ (ピクセル単位) |
| fragments |ビデオの時間ベースのチャンクの配列。メタデータはこのチャンク配列単位で分割されます |
| start |"ティック" 内のフラグメントの開始時刻 |
| duration |"ティック" 内のフラグメントの長さ |
| interval |指定したフラグメント内の各イベントの間隔 |
| events |リージョンを含む配列 |
| region |検出された単語または語句を表すオブジェクト |
| language |リージョン内で検出されたテキストの言語 |
| orientation |リージョン内で検出されたテキストの向き |
| lines |リージョン内で検出されたラインの配列 |
| text |実際のテキスト |

### <a name="json-output-example"></a>JSON 出力の例
次の出力例には、一般的なビデオ情報とビデオ フラグメントがいくつか含まれています。 ビデオ フラグメントごとに、OCR MP によって検出されたすべてのリージョンと、言語およびそのテキストの向きが示されています。 また、リージョンには、そのリージョン内のすべての単語ラインと、ラインのテキスト、ラインの位置、およびこのラインのすべての単語情報 (単語のコンテンツ、位置、信頼度) も含まれます。 例を次に示します。この例には、コメントをいくつかインラインで挿入してあります。

```json
    {
        "version": 1, 
        "timescale": 90000, 
        "offset": 0, 
        "framerate": 30, 
        "width": 640, 
        "height": 480,  // general video information
        "fragments": [
            {
                "start": 0, 
                "duration": 180000, 
                "interval": 90000,  // the time information about this fragment
                "events": [
                    [
                       { 
                            "region": { // the detected region array in this fragment 
                                "language": "English",  // region language
                                "orientation": "Up",  // text orientation
                                "lines": [  // line information array in this region, including the text and the position
                                    {
                                        "text": "One Two", 
                                        "left": 10, 
                                        "top": 10, 
                                        "right": 210, 
                                        "bottom": 110, 
                                        "word": [  // word information array in this line
                                            {
                                                "text": "One", 
                                                "left": 10, 
                                                "top": 10, 
                                                "right": 110, 
                                                "bottom": 110, 
                                                "confidence": 900
                                            }, 
                                            {
                                                "text": "Two", 
                                                "left": 110, 
                                                "top": 10, 
                                                "right": 210, 
                                                "bottom": 110, 
                                                "confidence": 910
                                            }
                                        ]
                                    }
                                ]
                            }
                        }
                    ]
                ]
            }
        ]
    }
```

## <a name="net-sample-code"></a>.NET サンプル コード

このプログラムでは次の方法を示します。

1. 資産を作成し、その資産にメディア ファイルをアップロードします。
2. OCR 構成/プリセット ファイルを使用して、ジョブを作成します。
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

namespace OCR
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

            // Run the OCR job.
            var asset = RunOCRJob(@"C:\supportFiles\OCR\presentation.mp4",
                                        @"C:\supportFiles\OCR\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\OCR\Output");
        }

        static IAsset RunOCRJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My OCR Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My OCR Job");

            // Get a reference to Azure Media OCR.
            string MediaProcessorName = "Azure Media OCR";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My OCR Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My OCR Output Asset", AssetCreationOptions.None);

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

