---
title: ".NET での Azure Media Services テレメトリの構成 | Microsoft Docs"
description: "この記事では、.NET SDK を使用して Azure Media Services テレメトリを使用する方法について説明します。"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: f8f55e37-0714-49ea-bf4a-e6c1319bec44
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/17/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: d693bc0de2f8a03d67b346f3b2d4693284ae4d71
ms.openlocfilehash: da7d4f87fdcaca6517fa95152d42c138533772b9


---

# <a name="configuring-azure-media-services-telemetry-with-net"></a>.NET での Azure Media Services テレメトリの構成

このトピックでは、.NET SDK を使用して Azure Media Services (AMS) テレメトリを構成するときの一般的な手順について説明します。 

>[!NOTE]
>AMS テレメトリの概念と使用方法の詳細については、[概要](media-services-telemetry-overview.md)に関するトピックを参照してください。

テレメトリ データは、次のいずれかの方法で使用できます。

- Azure Table Storage から直接データを読み取ります (Storage SDK の使用など)。 テレメトリのストレージ テーブルの説明については、 **こちら** のトピックの「 [Consuming telemetry information (テレメトリ情報の使用)](https://msdn.microsoft.com/library/mt742089.aspx) 」を参照してください。

または

- Media Services .NET SDK のサポートを利用してストレージ データを読み取ります。 このトピックでは、指定した AMS アカウントのテレメトリを有効にする方法と、Azure Media Services .NET SDK を使用してメトリックにクエリを実行する方法を説明します。  

## <a name="configuring-telemetry-for-a-media-services-account"></a>Media Services アカウントのテレメトリの構成

テレメトリを有効にするには、次の手順を実行する必要があります。

- Media Services アカウントに接続されたストレージ アカウントの資格情報を取得します。 
- 通知エンドポイントを作成します。**EndPointType** は **AzureTable** に設定し、endPointAddress はストレージ テーブルを指すように指定します。

        INotificationEndPoint notificationEndPoint = 
                      _context.NotificationEndPoints.Create("monitoring", 
                      NotificationEndPointType.AzureTable,
                      "https://" + _mediaServicesStorageAccountName + ".table.core.windows.net/");

- 監視するサービスの監視構成設定を作成します。 許可される監視構成設定は 1 つだけです。 
  
        IMonitoringConfiguration monitoringConfiguration = _context.MonitoringConfigurations.Create(notificationEndPoint.Id,
            new List<ComponentMonitoringSetting>()
            {
                new ComponentMonitoringSetting(MonitoringComponent.Channel, MonitoringLevel.Normal),
                new ComponentMonitoringSetting(MonitoringComponent.StreamingEndpoint, MonitoringLevel.Normal)
            });

## <a name="consuming-telemetry-information"></a>こちら

テレメトリ情報の使用については、[こちら](media-services-telemetry-overview.md)のトピックを参照してください。
 
## <a name="example"></a>例  
    
このセクションで示す例では、App.config ファイルに次のセクションがあることを前提としています。
    
    <appSettings>
      <add key="MediaServicesAccountName" value="ams_acct_name" />
      <add key="MediaServicesAccountKey" value="ams_acct_key" />
      <add key="MediaServicesAccountID" value="ams_acct_id" />
      <add key="StorageAccountName" value="storage_name" />
      <add key="StorageAccountKey" value="storage_key" />
    </appSettings>

次の例では、指定した AMS アカウントのテレメトリを有効にする方法と、Azure Media Services .NET SDK を使用してメトリックにクエリを実行する方法を説明します。  

    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client;
    
    namespace AMSMetrics
    {
        class Program
        {
            private static readonly string _mediaServicesAccountName =
                ConfigurationManager.AppSettings["MediaServicesAccountName"];
            private static readonly string _mediaServicesAccountKey =
                ConfigurationManager.AppSettings["MediaServicesAccountKey"];
            private static readonly string _mediaServicesAccountID =
                ConfigurationManager.AppSettings["MediaServicesAccountID"];
            private static readonly string _mediaServicesStorageAccountName =
                ConfigurationManager.AppSettings["StorageAccountName"];
            private static readonly string _mediaServicesStorageAccountKey =
                ConfigurationManager.AppSettings["StorageAccountKey"];
    
            // Field for service context.
            private static CloudMediaContext _context = null;
            private static MediaServicesCredentials _cachedCredentials = null;
    
            private static IStreamingEndpoint _streamingEndpoint = null;
            private static IChannel _channel = null;
    
            static void Main(string[] args)
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);
    
                _streamingEndpoint = _context.StreamingEndpoints.FirstOrDefault();
                _channel = _context.Channels.FirstOrDefault();
    
                var monitoringConfigurations = _context.MonitoringConfigurations;
                IMonitoringConfiguration monitoringConfiguration = null;
    
                // No more than one monitoring configuration settings is allowed.
                if (monitoringConfigurations.ToArray().Length != 0)
                {
                    monitoringConfiguration = _context.MonitoringConfigurations.FirstOrDefault();
                }
                else
                {
                    INotificationEndPoint notificationEndPoint =
                                  _context.NotificationEndPoints.Create("monitoring",
                                  NotificationEndPointType.AzureTable, GetTableEndPoint());
    
                    monitoringConfiguration = _context.MonitoringConfigurations.Create(notificationEndPoint.Id,
                        new List<ComponentMonitoringSetting>()
                        {
                            new ComponentMonitoringSetting(MonitoringComponent.Channel, MonitoringLevel.Normal),
                            new ComponentMonitoringSetting(MonitoringComponent.StreamingEndpoint, MonitoringLevel.Normal)
    
                        });
                }
    
                //Print metrics for a Streaming Endpoint.
                PrintStreamingEndpointMetrics();
    
                Console.ReadLine();
            }
    
            private static string GetTableEndPoint()
            {
                return "https://" + _mediaServicesStorageAccountName + ".table.core.windows.net/";
            }
    
            private static void PrintStreamingEndpointMetrics()
            {
                Console.WriteLine(string.Format("Telemetry for streaming endpoint '{0}'", _streamingEndpoint.Name));
    
                var end = DateTime.UtcNow;
                var start = DateTime.UtcNow.AddHours(-5);
    
                // Get some streaming endpoint metrics.
                var res = _context.StreamingEndPointRequestLogs.GetStreamingEndPointMetrics(
                        GetTableEndPoint(),
                        _mediaServicesStorageAccountKey,
                        _mediaServicesAccountID,
                        _streamingEndpoint.Id,
                        start,
                        end);
    
    
                Console.Title = "Streaming endpoint metrics:";
    
                foreach (var log in res)
                {
                    Console.WriteLine("AccountId: {0}", log.AccountId);
                    Console.WriteLine("BytesSent: {0}", log.BytesSent);
                    Console.WriteLine("EndToEndLatency: {0}", log.EndToEndLatency);
                    Console.WriteLine("HostName: {0}", log.HostName);
                    Console.WriteLine("ObservedTime: {0}", log.ObservedTime);
                    Console.WriteLine("PartitionKey: {0}", log.PartitionKey);
                    Console.WriteLine("RequestCount: {0}", log.RequestCount);
                    Console.WriteLine("ResultCode: {0}", log.ResultCode);
                    Console.WriteLine("RowKey: {0}", log.RowKey);
                    Console.WriteLine("ServerLatency: {0}", log.ServerLatency);
                    Console.WriteLine("StatusCode: {0}", log.StatusCode);
                    Console.WriteLine("StreamingEndpointId: {0}", log.StreamingEndpointId);
                    Console.WriteLine();
                }
    
                Console.WriteLine();
            }
    
            private static void PrintChannelMetrics()
            {
                if (_channel == null)
                {
                    Console.WriteLine("There are no channels in this AMS account");
                    return;
                }
    
                Console.WriteLine(string.Format("Telemetry for channel '{0}'", _channel.Name));
    
                var end = DateTime.UtcNow;
                var start = DateTime.UtcNow.AddHours(-5);
    
                // Get some channel metrics.
                var channelMetrics = _context.ChannelMetrics.GetChannelMetrics(
                    GetTableEndPoint(),
                    _mediaServicesStorageAccountKey,
                    _mediaServicesAccountID,
                   _channel.Id,
                    start,
                    end);
    
                // Print the channel metrics.
                Console.WriteLine("Channel metrics:");
    
                foreach (var channelHeartbeat in channelMetrics.OrderBy(x => x.ObservedTime))
                {
                    Console.WriteLine(
                        "    Observed time: {0}, Last timestamp: {1}, Incoming bitrate: {2}",
                        channelHeartbeat.ObservedTime,
                        channelHeartbeat.LastTimestamp,
                        channelHeartbeat.IncomingBitrate);
                }
    
                Console.WriteLine();
            }
        }
    }


## <a name="next-steps"></a>次のステップ

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供

[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


<!--HONumber=Nov16_HO5-->


