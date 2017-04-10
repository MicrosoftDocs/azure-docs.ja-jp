---
title: ".NET で Media Encoder Standard を使用してサムネイルを生成する方法"
description: "このトピックでは、.NET で Media Encoder Standard を使用して、資産をエンコードするのと同時にサムネイルを生成する方法を説明します。"
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: b8dab73a-1d91-4b6d-9741-a92ad39fc3f7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 1c28e756548b69d336b7f66ae9fbfa3c9259f787


---
# <a name="how-to-generate-thumbnails-using-media-encoder-standard-with-net"></a>.NET で Media Encoder Standard を使用してサムネイルを生成する方法
このトピックでは、Media Services .NET SDK で Media Encoder Standard を使用して、資産をエンコードしサムネイルを生成する方法を説明します。 このトピックでは、エンコードと同時にサムネイルを生成するタスクの作成に使用できる XML および JSON サムネイル プリセットを定義します。 [この](https://msdn.microsoft.com/library/mt269962.aspx) ドキュメントには、これらのプリセットで使用される要素の説明があります。

必ず「 [考慮事項](media-services-dotnet-generate-thumbnail-with-mes.md#considerations) 」セクションを確認してください。

## <a name="example"></a>例
次のコード サンプルでは、Media Services SDK を使用して次のタスクを実行します。

* エンコード ジョブを作成します。
* Media Encoder Standard エンコーダーの参照を取得します。
* エンコード用のプリセットとサムネイルの生成に必要な情報を含む、プリセット [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) または [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) を読み込みます。 この [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) または [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) はファイルに保存して、次のコードを使用してファイルを読み込みます。
  
            // Load the XML (or JSON) from the local file.
            string configuration = File.ReadAllText(fileName);  
* 1 つのエンコード タスクをジョブに追加します。 
* エンコードする入力資産を指定します。
* エンコードされた資産が含まれる出力資産を作成します。
* ジョブの進行状況を確認するイベント ハンドラーを追加します。
* ジョブを送信します。
  
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Net;
        using System.Security.Cryptography;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Newtonsoft.Json.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using System.Web;
        using System.Globalization;
  
        namespace EncodeAndGenerateThumbnails
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
  
                private static readonly string _mediaFiles =
                    Path.GetFullPath(@"../..\Media");
  
                private static readonly string _singleMP4File =
                    Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
  
                static void Main(string[] args)
                {
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the chached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
  
                    // Get an uploaded asset.
                    var asset = _context.Assets.FirstOrDefault();
  
                    // Encode and generate the thumbnails.
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

                    // Load the XML (or JSON) from the local file.
                    string configuration = File.ReadAllText("ThumbnailPreset_JSON.json");

                    // Create a task
                    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
                        processor,
                        configuration,
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

## <a name="a-idjsonathumbnail-json-preset"></a><a id="json"></a>サムネイル JSON プリセット
スキーマの詳細については、 [この](https://msdn.microsoft.com/library/mt269962.aspx) トピックを参照してください。

    {
      "Version": 1.0,
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": "true",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 4500,
              "MaxBitrate": 4500,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "ReferenceFrames": 3,
              "EntropyMode": "Cabac",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"

            }
          ],
          "Type": "H264Video"
        },
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "{Best}",
          "Type": "JpgImage"
        },
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": 640,
              "Height": 360,
            }
          ],
          "Start": "00:00:01",
          "Step": "00:00:10",
          "Range": "00:00:58",
          "Type": "PngImage"
        },
        {
          "BmpLayers": [
            {
              "Type": "BmpLayer",
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "10%",
          "Step": "10%",
          "Range": "90%",
          "Type": "BmpImage"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        },
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
          }
        },
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "BmpFormat"
          }
        },
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


## <a name="a-idxmlathumbnail-xml-preset"></a><a id="xml"></a>サムネイル XML プリセット
スキーマの詳細については、 [この](https://msdn.microsoft.com/library/mt269962.aspx) トピックを参照してください。

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <SceneChangeDetection>true</SceneChangeDetection>
          <H264Layers>
            <H264Layer>
              <Bitrate>4500</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>4500</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
        <JpgImage Start="{Best}">
          <JpgLayers>
            <JpgLayer>
              <Width>640</Width>
              <Height>360</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
        <BmpImage Start="10%" Step="10%" Range="90%">
          <BmpLayers>
            <BmpLayer>
              <Width>640</Width>
              <Height>360</Height>
            </BmpLayer>
          </BmpLayers>
        </BmpImage>
        <PngImage Start="00:00:01" Step="00:00:10" Range="00:00:58">
          <PngLayers>
            <PngLayer>
              <Width>640</Width>
              <Height>360</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <BmpFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>

## <a name="considerations"></a>考慮事項
次の考慮事項が適用されます。

* 明示的に Start、Step、Range でタイムスタンプを使用する場合、入力ソースは少なくとも 1 分であると仮定しています。
* Jpg/Png/BmpImage 要素には Start、Step、Range の文字列属性があり、次のように解釈できます。
  
  * 負ではない整数の場合は、フレーム番号 (例: "Start": "120")
  * % サフィックス付きで表現される場合は、ソース期間に対する相対値 (例: "Start": "15%")
  * HH:MM:SS… 形式で表現されている場合は、タイムスタンプ  ( 例: "Start" : "00:01:00")
    
    必要に応じて、表記法を混在させたり、一致させたりすることができます。
    
    また、Start は特殊なマクロの {Best} もサポートしています。このマクロは、コンテンツの最初の "関連する" フレームを決定しようと試みます。注: (Start が {Best} に設定されている場合、Step と Range は無視されます)
  * 既定: Start:{Best}
* 各画像形式の出力形式は明示的に指定する必要があります (Jpg/Png/BmpFormat)。 指定されている場合、JpgVideo は JpgFormat に、などと MES によって関連付けられます。 OutputFormat には新しい画像コーデック固有のマクロである {Index} が導入されました。このマクロは、画像出力形式を指定する場合に (1 度だけ) 指定する必要があります。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>関連項目
[Media Services Encoding の概要](media-services-encode-asset.md)




<!--HONumber=Nov16_HO3-->


