---
title: "Azure Metadata Service のスケジュールされたイベント | Microsoft Docs"
description: "影響力の強いイベントが発生する前に、Virtual Machine で対処します。"
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: 
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/10/2016
ms.author: zivr
translationtype: Human Translation
ms.sourcegitcommit: f67ff1cc18b23c3d4006121e266a5d96933e49bf
ms.openlocfilehash: d6c4d0b9f9cbf05341d30fe75e9828d0237a1962
ms.lasthandoff: 03/02/2017


---
# <a name="azure-metadata-service---scheduled-events-preview"></a>Azure Metadata Service: スケジュールされたイベント (プレビュー)

> [!NOTE] 
> プレビューは、使用条件に同意することを条件に使用することができます。 詳細については、[Microsoft Azure プレビューの追加使用条件] (https://azure.microsoft.com/en-us/support/legal/preview-supplemental-terms/) をご覧ください。
>

Azure Metadata Service のサブサービスの&1; つであるスケジュールされたイベントにより、今後のイベント (再起動など) に関する情報が表示されるため、アプリケーションはそれらのイベントに向けて準備し、中断を抑えることができます。 このサービスは、PaaS と IaaS を含むすべての Azure Virtual Machine の種類で利用できます。 スケジュールされたイベントにより、Virtual Machine が予防的タスクを実行する時間が確保され、イベントの影響を最小限に抑えることができます。 


## <a name="introduction---why-scheduled-events"></a>はじめに - スケジュールされたイベントが使用される理由

スケジュールされたイベントを使用すると、サービスへの影響を抑えるための対策を講じることができます。 状態を維持するためにレプリケーションの手法を使用するマルチ インスタンスのワークロードでは、複数のインスタンスで頻繁に機能停止が発生する可能性があります。 このような機能停止により、負荷の高いタスク (インデックスの再構築など) や、レプリカの損失が発生することがあります。 その他の多くの場合では、グレースフル シャット ダウン シーケンスを使用すると、全体的なサービスの利用可能性が向上します。 たとえば、実行中のトランザクションの完了 (またはキャンセル)、クラスター内の他の VM へのタスクの再割り当て (手動フェールオーバー)、ロード バランサー プールからの Virtual Machine の削除などです。 今後のイベントについて管理者に通知したり、単にそのようなイベントのログ記録をつけたりすることで、クラウドでホストされているアプリケーションのサービスの向上につながる場合があります。
Azure Metadata Service では、次のユース ケースでスケジュールされたイベントを表示します。
-    プラットフォームによって開始されたメンテナンス (ホスト OS ロールアウトなど)
-    ユーザーが開始した呼び出し (ユーザーによる再起動や VM の再デプロイなど)


## <a name="scheduled-events---the-basics"></a>スケジュールされたイベントの基本  

Azure Metadata Service によって、VM 内部からの REST エンドポイントを使用した Virtual Machine の実行に関する情報が表示されます。 情報は、VM の外部に公開されないように、ルーティングできない IP 経由で提供されます。

### <a name="scope"></a>Scope
スケジュールされたイベントは、クラウド サービス内のすべての Virtual Machine または可用性セット内のすべての Virtual Machine に表示されます。 そのため、イベント内の **[リソース]** フィールドをチェックして、影響を受ける VM を特定する必要があります。 

### <a name="discover-the-endpoint"></a>エンドポイントの検出
Virtual Machine が Virtual Network (VNet) 内で作成されている場合、メタデータ サービスはルーティング不可能な IP (169.254.169.254) 経由で提供されます。それ以外の場合は、クラウド サービスとクラシック VM の既定の設定として、使用するエンドポイントを検出するための追加のロジックが必要となります。 ホスト エンドポイントを検出する方法については、[こちらのサンプル] (https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm) を参照してください。

### <a name="versioning"></a>バージョン管理 
メタデータ サービスでは、http://{ip}/metadata/{version}/scheduledevents の形式でバージョン管理された API が使用されます。サービスが、http://{ip}/metadata/latest/scheduledevents で提供されている最新のバージョンを使用するようにすることをお勧めします。

### <a name="using-headers"></a>ヘッダの使用
メタデータ サービスのクエリを実行するときは、ヘッダー *Metadata: true* を指定する必要があります。 

### <a name="enable-scheduled-events"></a>スケジュールされたイベントの有効化
スケジュールされたイベントを初めて呼び出すときに、Azure はこの機能を Virtual Machine で暗黙的に有効化します。 そのため、最初の呼び出しでは最大&2; 分の応答遅延が発生すると予想されます。

### <a name="testing-your-logic-with-user-initiated-operations"></a>ユーザーが開始した操作でのロジックのテスト
ロジックをテストするには、Azure Portal、API、CLI、または PowerShell を使用して、スケジュールされたイベントが発生する操作を開始します。 仮想マシンを再起動すると、イベントの種類が "Reboot" であるスケジュールされたイベントが発生します。 仮想マシンを再デプロイすると、イベントの種類が "Redeploy" であるスケジュールされたイベントが発生します。
どちらの場合も、スケジュールされたイベントにより、アプリケーションは時間をかけて正常にシャットダウンすることが可能になるため、ユーザーが開始した操作の完了に時間がかかります。 

## <a name="using-the-api"></a>API を使用する

### <a name="query-for-events"></a>イベントのクエリ
次の呼び出しを行うだけで、スケジュールされたイベントのクエリを実行できます。

    curl -H Metadata:true http://169.254.169.254/metadata/latest/scheduledevents

応答には、スケジュールされたイベントの配列が含まれています。 空の配列は、現在スケジュールされているイベントがないことを意味します。
スケジュールされたイベントがある場合は、応答にイベントの配列が含まれます。 

    {
     "DocumentIncarnation":{IncarnationID},
     "Events":[
          {
                "EventId":{eventID},
                "EventType":"Reboot" | "Redeploy" | "Freeze",
                "ResourceType":"VirtualMachine",
                "Resources":[{resourceName}],
                "EventStatus":"Scheduled" | "Started",
                "NotBefore":{timeInUTC},              
         }
     ]
    }

EventType は、以下の場合に Virtual Machine が受けると想定される影響をキャプチャします。
- Freeze: Virtual Machine は数秒間の一時停止がスケジュールされています。 メモリ、開いているファイル、ネットワーク接続への影響はありません。
- 再起動: Virtual Machine は再起動がスケジュールされています (メモリをワイプします)。
- 再デプロイ: Virtual Machine は別のノードへの移動がスケジュールされています (一時ディスクは失われます)。 

イベントがスケジュールされている (Status = Scheduled) 場合、Azure は (NotBefore フィールドで指定された) イベントを開始できる時間を共有します。

### <a name="starting-an-event-expedite"></a>イベントの開始 (高速化)

今後予定されているイベントを確認し、グレースフル シャットダウンのロジックを完了すると、**POST** 呼び出しを行うことによって Azure の処理が (可能な場合は) 高速になるように指示することができます。 
 

## <a name="powershell-sample"></a>PowerShell のサンプル 

次の例は、スケジュールされたイベントのメタデータ サーバーを読み込んで、イベントを承認します。

```PowerShell
# How to get scheduled events 
function GetScheduledEvents($uri)
{
    $scheduledEvents = Invoke-RestMethod -Headers @{"Metadata"="true"} -URI $uri -Method get
    $json = ConvertTo-Json $scheduledEvents
    Write-Host "Received following events: `n" $json
    return $scheduledEvents
}

# How to approve a scheduled event
function ApproveScheduledEvent($eventId, $uri)
{    
    # Create the Scheduled Events Approval Json
    $startRequests = [array]@{"EventId" = $eventId}
    $scheduledEventsApproval = @{"StartRequests" = $startRequests} 
    $approvalString = ConvertTo-Json $scheduledEventsApproval

    Write-Host "Approving with the following: `n" $approvalString

    # Post approval string to scheduled events endpoint
    Invoke-RestMethod -Uri $uri -Headers @{"Metadata"="true"} -Method POST -Body $approvalString
}

# Add logic relevant to your service here
function HandleScheduledEvents($scheduledEvents)
{

}

######### Sample Scheduled Events Interaction #########

# Set up the scheduled events uri for VNET enabled VM
$localHostIP = "169.254.169.254"
$scheduledEventURI = 'http://{0}/metadata/latest/scheduledevents' -f $localHostIP 


# Get the document
$scheduledEvents = GetScheduledEvents $scheduledEventURI


# Handle events however is best for your service
HandleScheduledEvents $scheduledEvents


# Approve events when ready (optional)
foreach($event in $scheduledEvents.Events)
{
    Write-Host "Current Event: `n" $event
    $entry = Read-Host "`nApprove event? Y/N"
    if($entry -eq "Y" -or $entry -eq "y")
    {
    ApproveScheduledEvent $event.EventId $scheduledEventURI 
    }
}
``` 


## <a name="c-sample"></a>C\# のサンプル 
Metadata Service と通信する API を表示するクライアントのサンプルを次に示します。
```csharp
   public class ScheduledEventsClient
    {
        private readonly string scheduledEventsEndpoint;
        private readonly string defaultIpAddress = "169.254.169.254"; 

        public ScheduledEventsClient()
        {
            scheduledEventsEndpoint = string.Format("http://{0}/metadata/latest/scheduledevents", defaultIpAddress);
        }
        /// Retrieve Scheduled Events 
        public string GetDocument()
        {
            Uri cloudControlUri = new Uri(scheduledEventsEndpoint);
            using (var webClient = new WebClient())
            {
                webClient.Headers.Add("Metadata", "true");
                return webClient.DownloadString(cloudControlUri);
            }   
        }

        /// Issues a post request to the scheduled events endpoint with the given json string
        public void PostResponse(string jsonPost)
        {
            using (var webClient = new WebClient())
            {
                webClient.Headers.Add("Content-Type", "application/json");
                webClient.UploadString(scheduledEventsEndpoint, jsonPost);
            }
        }
    }

```
スケジュールされたイベントは、次のデータ構造を使用して解析できます 

```csharp
    public class ScheduledEventsDocument
    {
        public List<CloudControlEvent> Events { get; set; }
    }

    public class CloudControlEvent
    {
        public string EventId { get; set; }
        public string EventStatus { get; set; }
        public string EventType { get; set; }
        public string ResourceType { get; set; }
        public List<string> Resources { get; set; }
        public DateTime NoteBefore { get; set; }
    }

    public class ScheduledEventsApproval
    {
        public List<StartRequest> StartRequests = new List<StartRequest>();
    }

    public class StartRequest
    {
        [JsonProperty("EventId")]
        private string eventId;

        public StartRequest(string eventId)
        {
            this.eventId = eventId;
        }
    }

```

クライアントを使用してイベントを取得、処理、および確認するサンプル プログラム   

```csharp
public class Program
    {
    static ScheduledEventsClient client;
    static void Main(string[] args)
    {
        while (true)
        {
            client = new ScheduledEventsClient();
            string json = client.GetDocument();
            ScheduledEventsDocument scheduledEventsDocument = JsonConvert.DeserializeObject<ScheduledEventsDocument>(json);

            HandleEvents(scheduledEventsDocument.Events);

            // Wait for user response
            Console.WriteLine("Press Enter to approve executing events\n");
            Console.ReadLine();

            // Approve events
            ScheduledEventsApproval scheduledEventsApprovalDocument = new ScheduledEventsApproval();
            foreach (CloudControlEvent ccevent in scheduledEventsDocument.Events)
            {
                scheduledEventsApprovalDocument.StartRequests.Add(new StartRequest(ccevent.EventId));
            }
            if (scheduledEventsApprovalDocument.StartRequests.Count > 0)
            {
                // Serialize using Newtonsoft.Json
                string approveEventsJsonDocument =
                    JsonConvert.SerializeObject(scheduledEventsApprovalDocument);

                Console.WriteLine($"Approving events with json: {approveEventsJsonDocument}\n");
                client.PostResponse(approveEventsJsonDocument);
            }

            Console.WriteLine("Complete. Press enter to repeat\n\n");
            Console.ReadLine();
            Console.Clear();
        }
    }

    private static void HandleEvents(List<CloudControlEvent> events)
    {
        // Add logic for handling events here
    }
}

```

## <a name="python-sample"></a>Python サンプル 

```python


#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url="http://169.254.169.254/metadata/latest/scheduledevents"
headers="{Metadata:true}"
this_host=socket.gethostname()

def get_scheduled_events():
   req=urllib2.Request(metadata_url)
   req.add_header('Metadata','true')
   resp=urllib2.urlopen(req)
   data=json.loads(resp.read())
   return data

def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid=evt['EventId']
        status=evt['EventStatus']
        resources=evt['Resources'][0]
        eventype=evt['EventType']
        restype=evt['ResourceType']
        notbefore=evt['NotBefore'].replace(" ","_")
        if this_host in evt['Resources'][0]:
            print "+ Scheduled Event. This host is scheduled for " + eventype + " not before " + notbefore
            print "++ Add you logic here"

def main():
   data=get_scheduled_events()
   handle_scheduled_events(data)
   

if __name__ == '__main__':
  main()
  sys.exit(0)


```
## <a name="next-steps"></a>次のステップ 
[Azure での仮想マシンに対する計画的なメンテナンス](./virtual-machines-linux-planned-maintenance.md)

