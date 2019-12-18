---
title: .NET SDK を使用してストリーミング エンドポイントを管理する。 | Microsoft Docs
description: この記事では、Azure portal を使ってストリーミング エンドポイントを管理する方法について説明します。
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.assetid: 0da34a97-f36c-48d0-8ea2-ec12584a2215
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: b6c6fccd473ae57139c0b46bf32dc9468a4ba1a8
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74901276"
---
# <a name="manage-streaming-endpoints-with-net-sdk"></a>.NET SDK を使用してストリーミング エンドポイントを管理する  

>[!NOTE]
>必ず、[概要](media-services-streaming-endpoints-overview.md)記事をお読みください。 また、[StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint) もお読みください。

この記事では、Azure Media Services .NET SDK を使って次のタスクを実行するためのコードを示します。

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

ストリーミング エンドポイントのスケールを設定する方法については、[こちら](media-services-portal-scale-streaming-endpoints.md)の記事をご覧ください。

## <a name="create-and-configure-a-visual-studio-project"></a>Visual Studio プロジェクトの作成と構成

「[.NET を使用した Media Services 開発](media-services-dotnet-how-to-use.md)」の説明に従って、開発環境をセットアップし、app.config ファイルに接続情報を指定します。 

## <a name="add-code-that-manages-streaming-endpoints"></a>ストリーミング エンドポイントを管理するコードを追加する
    
Program.cs のコードを次のコードに置き換えます。

```csharp
using System;
using System.Configuration;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.MediaServices.Client.Live;

namespace AMSStreamingEndpoint
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

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            var defaultStreamingEndpoint = _context.StreamingEndpoints.Where(s => s.Name.Contains("default")).FirstOrDefault();
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
            if (streamingEndpoint.StreamingEndpointVersion == "1.0")
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
```

## <a name="next-steps"></a>次の手順
Media Services のラーニング パスを確認します。

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

