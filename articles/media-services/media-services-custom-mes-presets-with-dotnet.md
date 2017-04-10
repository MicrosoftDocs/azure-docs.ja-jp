---
title: "Media Encoder Standard のプリセットのカスタマイズ | Microsoft Docs"
description: "このトピックでは、Media Encoder Standard のタスク プリセットをカスタマイズして、高度なエンコードを実行する方法を紹介します。 また、Media Services .NET SDK を使用してエンコード タスクとジョブを作成する方法も紹介します。 エンコード ジョブにカスタム プリセットを与える方法も紹介します。"
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: ec95392f-d34a-4c22-a6df-5274eaac445b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 244ce537062ef970549ef453adf0ad8d3fea79c1
ms.openlocfilehash: 2a344af8b5bb5002f400d7a0f87b52a0f73ae619


---
# <a name="customizing-media-encoder-standard-presets"></a>Media Encoder Standard のプリセットのカスタマイズ

## <a name="overview"></a>概要

このトピックでは、Media Encoder Standard (MES) をカスタム プリセットと共に使用して高度なエンコード タスクを実行する方法を紹介します。 また、.NET を使用して、エンコード タスクとそのタスクを実行するジョブを作成します。  

このトピックでは、[H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) プリセットを取得してレイヤーの数を減らすことによってプリセットをカスタマイズする方法を説明します。 [Media Encoder Standard のプリセットのカスタマイズ](media-services-advanced-encoding-with-mes.md)のトピックでは、高度なエンコード タスクを実行するために使用できるカスタム プリセットを示しています。

## <a name="a-idcustomizingpresetsa-customizing-a-mes-preset"></a><a id="customizing_presets"></a>MES プリセットのカスタマイズ

### <a name="original-preset"></a>元のプリセット

「[H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md)」のトピックで定義されている JSON を、拡張子 .json を持つファイルに保存します。 たとえば、**CustomPreset_JSON.json** のようにします。

### <a name="customized-preset"></a>カスタマイズしたプリセット

**CustomPreset_JSON.json** ファイルを開いて最初の 3 つのレイヤーを **H264Layers** から削除すると、ファイルは次のようになります。

    
    {  
      "Version": 1.0,  
      "Codecs": [  
        {  
          "KeyFrameInterval": "00:00:02",  
          "H264Layers": [  
            {  
              "Profile": "Auto",  
              "Level": "auto",  
              "Bitrate": 1000,  
              "MaxBitrate": 1000,  
              "BufferWindow": "00:00:05",  
              "Width": 640,  
              "Height": 360,  
              "BFrames": 3,  
              "ReferenceFrames": 3,  
              "AdaptiveBFrame": true,  
              "Type": "H264Layer",  
              "FrameRate": "0/1"  
            },  
            {  
              "Profile": "Auto",  
              "Level": "auto",  
              "Bitrate": 650,  
              "MaxBitrate": 650,  
              "BufferWindow": "00:00:05",  
              "Width": 640,  
              "Height": 360,  
              "BFrames": 3,  
              "ReferenceFrames": 3,  
              "AdaptiveBFrame": true,  
              "Type": "H264Layer",  
              "FrameRate": "0/1"  
            },  
            {  
              "Profile": "Auto",  
              "Level": "auto",  
              "Bitrate": 400,  
              "MaxBitrate": 400,  
              "BufferWindow": "00:00:05",  
              "Width": 320,  
              "Height": 180,  
              "BFrames": 3,  
              "ReferenceFrames": 3,  
              "AdaptiveBFrame": true,  
              "Type": "H264Layer",  
              "FrameRate": "0/1"  
            }  
          ],  
          "Type": "H264Video"  
        },  
        {  
          "Profile": "AACLC",  
          "Channels": 2,  
          "SamplingRate": 48000,  
          "Bitrate": 128,  
          "Type": "AACAudio"  
        }  
      ],  
      "Outputs": [  
        {  
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",  
          "Format": {  
            "Type": "MP4Format"  
          }  
        }  
      ]  
    }  
    

## <a name="a-idencodingwithdotnetaencoding-with-media-services-net-sdk"></a><a id="encoding_with_dotnet"></a>Media Services .NET SDK を使用したエンコード

次のコード サンプルでは、Media Services SDK を使用して次のタスクを実行します。

- エンコード ジョブを作成します。
- Media Encoder Standard エンコーダーの参照を取得します。
- 前のセクションで作成したカスタム JSON プリセットを読み込みます。 
  
        // Load the JSON from the local file.
        string configuration = File.ReadAllText(fileName);  

- エンコード タスクをジョブに追加します。 
- エンコードする入力資産を指定します。
- エンコードされた資産が含まれる出力資産を作成します。
- ジョブの進行状況を確認するイベント ハンドラーを追加します。
- ジョブを送信します。
   
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
  
        namespace CustomizeMESPresests
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
  
                    // Encode and generate the output using custom presets.
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
                    string configuration = File.ReadAllText("CustomPreset_JSON.json");

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



## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>関連項目
[Media Services Encoding の概要](media-services-encode-asset.md)




<!--HONumber=Nov16_HO5-->


