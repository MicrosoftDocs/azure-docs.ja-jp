---
title: "Azure Media Encoder Standard を使用したビットレート ラダーの自動生成 | Microsoft Docs"
description: "このトピックでは、Media Encoder Standard (MES) を使用して、入力解像度とビットレートに基づいてビットレート ラダーを自動生成する方法を説明します。 入力解像度とビットレートを超えることはありません。 たとえば、入力が 3Mbps で 720p の場合、出力は最高でも 720p のままになり、3Mbps よりも低い速度で開始されます。"
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 63ed95da-1b82-44b0-b8ff-eebd535bc5c7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 244413be8b094605883445bb3cbf675b538b704e
ms.lasthandoff: 03/10/2017


---
#  <a name="use-azure-media-encoder-standard-to-auto-generate-a-bitrate-ladder"></a>Azure Media Encoder Standard を使用したビットレート ラダーの自動生成

## <a name="overview"></a>概要

このトピックでは、Media Encoder Standard (MES) を使用して、入力解像度とビットレートに基づいてビットレート ラダー (ビットレートと解像度のペア) を自動生成する方法を説明します。 自動生成されたプリセットが、入力解像度とビットレートを超えることはありません。 たとえば、入力が 3Mbps で 720p の場合、出力は最高でも 720p のままになり、3Mbps よりも低い速度で開始されます。

この機能を使用するには、エンコード タスクを作成するときに**アダプティブ ストリーミング** プリセットを指定する必要があります。 **アダプティブ ストリーミング** プリセットを使用するときは、MES エンコーダーの判断によってビットレート ラダーの上限が設定されます。 ただし、使用されるレイヤーの数と解像度はサービスによって決まるため、ユーザーがエンコード コストを制御することはできません。 **アダプティブ ストリーミング** プリセットをエンコードした結果、MES によって生成される出力レイヤーの例は、このトピックの[終わり](#output)で確認できます。

## <a id="encoding_with_dotnet"></a>Media Services .NET SDK を使用したエンコード

次のコード サンプルでは、Media Services SDK を使用して次のタスクを実行します。

- エンコード ジョブを作成します。
- Media Encoder Standard エンコーダーの参照を取得します。
- エンコード タスクをジョブに追加し、**アダプティブ ストリーミング** プリセットを使用するように指定します。 
- エンコードされた資産が含まれる出力資産を作成します。
- ジョブの進行状況を確認するイベント ハンドラーを追加します。
- ジョブを送信します。

        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;

        namespace AdaptiveStreamingMESPresest
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
                // Used the chached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

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

                // Create a task
                ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
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

## <a id="output"></a>出力

このセクションでは、**アダプティブ ストリーミング** プリセットを使用してエンコードした結果として MES によって生成された出力レイヤーの&3; つの例を示します。 

### <a name="example-1"></a>例 1
高さが "1080" でフレーム レートが "29.970" のソースからは、6 層のビデオ レイヤーが生成されます。

|レイヤー|高さ|幅|ビットレート (kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>例 2
高さが "720" でフレーム レートが "23.970" のソースからは、5 層のビデオ レイヤーが生成されます。

|レイヤー|高さ|幅|ビットレート (kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>例 3
高さが "360" でフレーム レートが "29.970" のソースからは、3 層のビデオ レイヤーが生成されます。

|レイヤー|高さ|幅|ビットレート (kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|
## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>関連項目
[Media Services Encoding の概要](media-services-encode-asset.md)


