---
title: ".NET を使用してオンプレミス エンコーダーでライブ ストリーミングを実行する方法 | Microsoft Docs"
description: "このトピックでは、.NET を使用して、オンプレミス エンコーダーでライブ エンコードを実行する方法について紹介します。"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 15908152-d23c-4d55-906a-3bfd74927db5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/05/2017
ms.author: cenkdin;juliako
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 67d446263c7a884cd8d22e88e6fb607b1399d9aa
ms.lasthandoff: 03/14/2017


---
# <a name="how-to-perform-live-streaming-with-on-premise-encoders-using-net"></a>.NET を使用してオンプレミス エンコーダーでライブ ストリーミングを実行する方法
> [!div class="op_single_selector"]
> * [ポータル](media-services-portal-live-passthrough-get-started.md)
> * [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)
> * [REST ()](https://docs.microsoft.com/rest/api/media/operations/channel)
> 
> 

このチュートリアルでは、Azure Media Services .NET SDK を使用して、パススルー配信用に構成された **チャネル** を作成する手順を紹介します。 

## <a name="prerequisites"></a>前提条件
チュートリアルを完了するには次のものが必要です。

* Azure アカウント。
* Media Services アカウント。    Media Services アカウントを作成するには、[Media Services アカウントを作成する方法](media-services-portal-create-account.md)に関するページを参照してください。
* 開発環境の設定。 詳細については、「 [環境を設定する](media-services-set-up-computer.md)」を参照してください。
* Web カメラ。 たとえば、 [Telestream Wirecast エンコーダー](http://www.telestream.net/wirecast/overview.htm)。

次の記事の確認をお勧めします。

* [Azure Media Services RTMP サポートおよびライブ エンコーダー](https://azure.microsoft.com/blog/2014/09/18/azure-media-services-rtmp-support-and-live-encoders/)
* [オンプレミスのエンコーダーからマルチ ビットレートのライブ ストリームを受信するチャネルを操作する](media-services-live-streaming-with-onprem-encoders.md)

## <a name="example"></a>例
次のコード サンプルでは、次のタスクを実現する方法を示しています。

* Media Services への接続
* チャネルの作成
* チャネルの更新
* チャネルの入力エンドポイントを取得します。 入力エンドポイントはオンプレミスのライブ エンコーダーに指定する必要があります。 ライブ エンコーダーはカメラからの信号をストリームに変換します。ストリームはチャネルの入力 (インジェスト) エンドポイントに送信されます。
* チャネルのプレビュー エンドポイントの取得
* プログラムの作成と起動
* プログラムにアクセスするために必要なロケーターの作成
* StreamingEndpoint の作成と起動
* ストリーミング エンドポイントの更新
* ストリーミング エンドポイントのロケーターの取得
* リソースのシャット ダウン

>[!NOTE]
>コンテンツのストリーミング元のストリーミング エンドポイントが**実行中**状態であることを確認してください。 
    
    
>[!NOTE]
>さまざまな AMS ポリシー (ロケーター ポリシーや ContentKeyAuthorizationPolicy など) に 1,000,000 ポリシーの制限があります。 常に同じ日数、アクセス許可などを使う場合は、同じポリシー ID を使う必要があります (たとえば、長期間存在するように意図されたロケーターのポリシー (非アップロード ポリシー))。 詳細については、 [こちらの](media-services-dotnet-manage-entities.md#limit-access-policies) トピックを参照してください。

ライブ エンコーダーの設定方法については、「 [Azure Media Services の RTMP サポートとライブ エンコーダー](https://azure.microsoft.com/blog/2014/09/18/azure-media-services-rtmp-support-and-live-encoders/)」をご覧ください。

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

    namespace AMSLiveTest
    {
        class Program
        {
            private const string StreamingEndpointName = "streamingendpoint001";
            private const string ChannelName = "channel001";
            private const string AssetlName = "asset001";
            private const string ProgramlName = "program001";

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

                IChannel channel = CreateAndStartChannel();

                // Set the Live Encoder to point to the channel's input endpoint:
                string ingestUrl = channel.Input.Endpoints.FirstOrDefault().Url.ToString();

                // Use the previewEndpoint to preview and verify
                // that the input from the encoder is actually reaching the Channel.
                string previewEndpoint = channel.Preview.Endpoints.FirstOrDefault().Url.ToString();

                IProgram program = CreateAndStartProgram(channel);
                ILocator locator = CreateLocatorForAsset(program.Asset, program.ArchiveWindowLength);
                IStreamingEndpoint streamingEndpoint = CreateAndStartStreamingEndpoint();
                GetLocatorsInAllStreamingEndpoints(program.Asset);

                // Once you are done streaming, clean up your resources.
                Cleanup(streamingEndpoint, channel);
            }

            public static IChannel CreateAndStartChannel()
            {
                //If you want to change the Smooth fragments to HLS segment ratio, you would set the ChannelCreationOptions’s Output property.

                IChannel channel = _context.Channels.Create(
                new ChannelCreationOptions
                {
                Name = ChannelName,
                Input = CreateChannelInput(),
                Preview = CreateChannelPreview()
                });

                //Starting and stopping Channels can take some time to execute. To determine the state of operations after calling Start or Stop, query the IChannel.State .

                channel.Start();

                return channel;
            }

            private static ChannelInput CreateChannelInput()
            {
                return new ChannelInput
                {
                    StreamingProtocol = StreamingProtocol.RTMP,
                    AccessControl = new ChannelAccessControl
                    {
                        IPAllowList = new List<IPRange>
                                {
                                new IPRange
                            {
                                Name = "TestChannelInput001",
                                // Setting 0.0.0.0 for Address and 0 for SubnetPrefixLength
                                // will allow access to IP addresses.
                                Address = IPAddress.Parse("0.0.0.0"),
                                SubnetPrefixLength = 0
                            }
                        }
                    }
                };
            }

            private static ChannelPreview CreateChannelPreview()
            {
                return new ChannelPreview
                {
                    AccessControl = new ChannelAccessControl
                    {
                        IPAllowList = new List<IPRange>
                        {
                            new IPRange
                            {
                                Name = "TestChannelPreview001",
                                // Setting 0.0.0.0 for Address and 0 for SubnetPrefixLength
                                // will allow access to IP addresses.
                                Address = IPAddress.Parse("0.0.0.0"),
                                SubnetPrefixLength = 0
                            }
                        }
                    }
                };
            }

            public static void UpdateCrossSiteAccessPoliciesForChannel(IChannel channel)
            {
                var clientPolicy =
                    @"<?xml version=""1.0"" encoding=""utf-8""?>
                <access-policy>
                    <cross-domain-access>
                        <policy>
                            <allow-from http-request-headers=""*"" http-methods=""*"">
                                <domain uri=""*""/>
                            </allow-from>
                            <grant-to>
                               <resource path=""/"" include-subpaths=""true""/>
                            </grant-to>
                        </policy>
                    </cross-domain-access>
                </access-policy>";

                var xdomainPolicy =
                    @"<?xml version=""1.0"" ?>
                <cross-domain-policy>
                    <allow-access-from domain=""*"" />
                </cross-domain-policy>";

                channel.CrossSiteAccessPolicies.ClientAccessPolicy = clientPolicy;
                channel.CrossSiteAccessPolicies.CrossDomainPolicy = xdomainPolicy;

                channel.Update();
            }

            public static IProgram CreateAndStartProgram(IChannel channel)
            {
                IAsset asset = _context.Assets.Create(AssetlName, AssetCreationOptions.None);

                // Create a Program on the Channel. You can have multiple Programs that overlap or are sequential;
                // however each Program must have a unique name within your Media Services account.
                IProgram program = channel.Programs.Create(ProgramlName, TimeSpan.FromHours(3), asset.Id);
                program.Start();

                return program;
            }

            public static ILocator CreateLocatorForAsset(IAsset asset, TimeSpan ArchiveWindowLength)
            {
                // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            

                var locator = _context.Locators.CreateLocator
                    (
                        LocatorType.OnDemandOrigin,
                        asset,
                        _context.AccessPolicies.Create
                        (
                            "Live Stream Policy",
                            ArchiveWindowLength,
                            AccessPermissions.Read
                        )
                    );

                return locator;
            }

            public static IStreamingEndpoint CreateAndStartStreamingEndpoint()
            {
                var options = new StreamingEndpointCreationOptions
                {
                    Name = StreamingEndpointName,
                    ScaleUnits = 1,
                    AccessControl = GetAccessControl(),
                    CacheControl = GetCacheControl()
                };

                IStreamingEndpoint streamingEndpoint = _context.StreamingEndpoints.Create(options);
                streamingEndpoint.Start();

                return streamingEndpoint;
            }

            private static StreamingEndpointAccessControl GetAccessControl()
            {
                return new StreamingEndpointAccessControl
                {
                    IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "Allow all",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    },

                    AkamaiSignatureHeaderAuthenticationKeyList = new List<AkamaiSignatureHeaderAuthenticationKey>
                    {
                        new AkamaiSignatureHeaderAuthenticationKey
                        {
                            Identifier = "My key",
                            Expiration = DateTime.UtcNow + TimeSpan.FromDays(365),
                            Base64Key = Convert.ToBase64String(GenerateRandomBytes(16))
                        }
                    }
                };
            }

            private static byte[] GenerateRandomBytes(int length)
            {
                var bytes = new byte[length];
                using (var rng = new RNGCryptoServiceProvider())
                {
                    rng.GetBytes(bytes);
                }

                return bytes;
            }

            private static StreamingEndpointCacheControl GetCacheControl()
            {
                return new StreamingEndpointCacheControl
                {
                    MaxAge = TimeSpan.FromSeconds(1000)
                };
            }

            public static void UpdateCrossSiteAccessPoliciesForStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
            {
                var clientPolicy =
                    @"<?xml version=""1.0"" encoding=""utf-8""?>
                <access-policy>
                    <cross-domain-access>
                        <policy>
                            <allow-from http-request-headers=""*"" http-methods=""*"">
                                <domain uri=""*""/>
                            </allow-from>
                            <grant-to>
                               <resource path=""/"" include-subpaths=""true""/>
                            </grant-to>
                        </policy>
                    </cross-domain-access>
                </access-policy>";

                var xdomainPolicy =
                    @"<?xml version=""1.0"" ?>
                <cross-domain-policy>
                    <allow-access-from domain=""*"" />
                </cross-domain-policy>";

                streamingEndpoint.CrossSiteAccessPolicies.ClientAccessPolicy = clientPolicy;
                streamingEndpoint.CrossSiteAccessPolicies.CrossDomainPolicy = xdomainPolicy;

                streamingEndpoint.Update();
            }

            public static void GetLocatorsInAllStreamingEndpoints(IAsset asset)
            {
                var locators = asset.Locators.Where(l => l.Type == LocatorType.OnDemandOrigin);
                var ismFile = asset.AssetFiles.AsEnumerable().FirstOrDefault(a => a.Name.EndsWith(".ism"));
                var template = new UriTemplate("{contentAccessComponent}/{ismFileName}/manifest");
                var urls = locators.SelectMany(l =>
                            _context
                                .StreamingEndpoints
                                .AsEnumerable()
                                .Where(se => se.State == StreamingEndpointState.Running)
                                .Select(
                                    se =>
                                        template.BindByPosition(new Uri("http://" + se.HostName),
                                        l.ContentAccessComponent,
                                            ismFile.Name)))
                            .ToArray();

            }

            public static void Cleanup(IStreamingEndpoint streamingEndpoint,
                                        IChannel channel)
            {
                if (streamingEndpoint != null)
                {
                    streamingEndpoint.Stop();
                    streamingEndpoint.Delete();
                }

                IAsset asset;
                if (channel != null)
                {

                    foreach (var program in channel.Programs)
                    {
                        asset = _context.Assets.Where(se => se.Id == program.AssetId)
                                                .FirstOrDefault();

                        program.Stop();
                        program.Delete();

                        if (asset != null)
                        {
                            foreach (var l in asset.Locators)
                                l.Delete();

                            asset.Delete();
                        }
                    }

                    channel.Stop();
                    channel.Delete();
                }
            }
        }
    }

## <a name="next-step"></a>次のステップ
Media Services のラーニング パスを確認します。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


