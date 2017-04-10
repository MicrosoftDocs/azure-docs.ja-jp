---

title: ".NET SDK を使用してストリーミング エンドポイントを管理する。 | Microsoft Docs"
description: "このトピックでは、Azure ポータルを使用してストリーミング エンドポイントを管理する方法について説明します。"
services: media-services
documentationcenter: 
author: Juliako
writer: juliako
manager: erikre
editor: 
ms.assetid: 0da34a97-f36c-48d0-8ea2-ec12584a2215
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 4f61f7c0fd50065d341ae1ce182a033395857579
ms.openlocfilehash: 68011ef634b1f3bdeb7c219a46e1307698a12f7e


---


# <a name="manage-streaming-endpoints-with-net-sdk"></a>.NET SDK を使用してストリーミング エンドポイントを管理する

>[!NOTE]
>必ず、[概要](media-services-streaming-endpoints-overview.md)トピックをお読みください。 また、[StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint) もお読みください。

このトピックでは、Azure Media Services .NET SDK を使用して次のタスクを実行するためのコードを示します。

- 既定のストリーミング エンドポイントを調べる。
- 新しいストリーミング エンドポイントを作成/追加する。

    複数の CDN を使用する場合や、CDN と直接アクセスを使用する場合には、複数のストリーミング エンドポイントが必要になる可能性があります。

    > [!NOTE]
    > ストリーミング エンドポイントが実行状態の場合のみ課金されます。
    
- ストリーミング エンドポイントを更新する。
    
    必ず、Update() 関数を呼び出してください。

- ストリーミング エンドポイントを削除する。

    >[!NOTE]
    >既定のストリーミング エンドポイントは削除できません。

ストリーミング エンドポイントのスケールを設定する方法については、 [こちらの](media-services-portal-scale-streaming-endpoints.md) トピックを参照してください。


###<a name="set-up-the-visual-studio-project"></a>Visual Studio プロジェクトをセットアップする

1. Visual Studio 2015 で、新しい C# コンソール アプリケーションを作成します。  
2. ソリューションをビルドします。
3. **NuGet** を使用して、[最新の Azure Media Services .NET SDK パッケージ](https://www.nuget.org/packages/windowsazure.mediaservices/)をインストールします。   
4. .config ファイルに appSettings セクションを追加し、Media Services 名とキーの値を更新します。 
    
        <appSettings>
          <add key="MediaServicesAccountName" value="Media-Services-Account-Name"/>
          <add key="MediaServicesAccountKey" value="Media-Services-Account-Key"/>
        </appSettings>

###<a name="add-code-that-manages-streaming-endpoints"></a>ストリーミング エンドポイントを管理するコードを追加する
    
Program.cs のコードを次のコードに置き換えます。

    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using Microsoft.WindowsAzure.MediaServices.Client.Live;
    
    namespace AMSStreamingEndpoint
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
    
                var defaultStreamingEndpoint = _context.StreamingEndpoints.Where(s=>s.Name.Contains("default")).FirstOrDefault();
                ExamineStreamingEndpoint(defaultStreamingEndpoint);
    
                IStreamingEndpoint newStreamingEndpoint = AddStreamingEndpoint();
                UpdateStreamingEndpoint(newStreamingEndpoint);
                DeleteStreamingEndpoint(newStreamingEndpoint);
            }
    
            static public void ExamineStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
            {
                Console.WriteLine(streamingEndpoint.Name);
                Console.WriteLine(streamingEndpoint.StreamingEndpointVersion);
                Console.WriteLine(streamingEndpoint.FreeTrialEndTime);
                Console.WriteLine(streamingEndpoint.ScaleUnits);
                Console.WriteLine(streamingEndpoint.CdnProvider);
                Console.WriteLine(streamingEndpoint.CdnProfile);
                Console.WriteLine(streamingEndpoint.CdnEnabled);
            }
    
            static public IStreamingEndpoint AddStreamingEndpoint()
            {
                var name = "StreamingEndpoint" + DateTime.UtcNow.ToString("hhmmss");
                var option = new StreamingEndpointCreationOptions(name, 1)
                {
                    StreamingEndpointVersion = new Version("2.0"),
                    CdnEnabled = true,
                    CdnProfile = "CdnProfile",
                    CdnProvider = CdnProviderType.PremiumVerizon
                };
    
                var streamingEndpoint = _context.StreamingEndpoints.Create(option);
    
                return streamingEndpoint;
            }
    
            static public void UpdateStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
            {
                if(streamingEndpoint.StreamingEndpointVersion == "1.0")
                    streamingEndpoint.StreamingEndpointVersion = "2.0";
    
                streamingEndpoint.CdnEnabled = false;
                streamingEndpoint.Update();
            }
    
            static public void DeleteStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
            {
                streamingEndpoint.Delete();
            }
        }
    }
    

## <a name="next-steps"></a>次のステップ
Media Services のラーニング パスを確認します。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Jan17_HO2-->


