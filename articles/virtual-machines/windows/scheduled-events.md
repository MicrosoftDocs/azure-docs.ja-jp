---
title: Azure の Windows VM のスケジュール化されたイベント
description: Windows 仮想マシンでは Azure メタデータ サービスを使用してイベントがスケジュールされています。
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: ericrad
manager: gwallace
editor: ''
tags: ''
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: e6aa53ab5e71cbcc830e31ee1f3650feca7db63b
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74885519"
---
# <a name="azure-metadata-service-scheduled-events-for-windows-vms"></a>Azure Metadata Service: Windows VM のスケジュールされたイベント

スケジュールされたイベントとは、仮想マシンのメンテナンスに備えるための時間をアプリケーションに与える Azure Metadata Service です。 今後のメンテナンス イベント (再起動など) に関する情報を提供することで、アプリケーションがイベントの準備を行い、中断を制限できるようにします。 このサービスは、Windows および Linux で、PaaS と IaaS を含むすべての Azure Virtual Machine の種類で利用できます。 

Linux のスケジュールされたイベントの詳細については、[Linux VM のスケジュールされたイベント](../linux/scheduled-events.md)に関する記事をご覧ください。

> [!Note] 
> スケジュールされたイベントは、すべての Azure リージョンで一般公開されています。 最新リリースについては、「[利用可能なバージョンとリージョン](#version-and-region-availability)」をご覧ください。

## <a name="why-scheduled-events"></a>スケジュールされたイベントを使用する理由

多くのアプリケーションでは、仮想マシンのメンテナンスに備える時間を得ることでメリットがあります。 この時間を使用して、可用性、信頼性、およびサービスを向上させる、次のようなアプリケーション固有のタスクを実行できます。 

- チェックポイントと復元
- 接続のドレイン
- プライマリ レプリカのフェールオーバー 
- ロード バランサー プールからの削除
- イベント ログ
- グレースフル シャットダウン 

スケジュールされたイベントを使用すると、アプリケーションはメンテナンスが行われる時期を検出し、その影響を制限するタスクをトリガーできます。 スケジュール化されたイベントを有効にすると、仮想マシンでメンテナンス アクティビティが実行されるまでの時間が最小限になります。 詳細については、後の「イベントのスケジューリング」セクションをご覧ください。

スケジュールされたイベントは、次のユース ケースでイベントを提供します。
- [プラットフォームで開始されるメンテナンス](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates) (例: VM の再起動、ライブ マイグレーション、ホストの更新を保持するメモリ)
- ハードウェアの機能低下
- ユーザーが開始するメンテナンス (例: ユーザーによる再起動や VM の再デプロイ)
- [スポット VM](spot-vms.md) および[スポット スケール セット](../../virtual-machine-scale-sets/use-spot.md) インスタンスの削除

## <a name="the-basics"></a>基本操作  

Azure Metadata Service では、VM 内部からアクセスできる REST エンドポイントを使用した Virtual Machine の実行に関する情報が公開されます。 情報は、VM の外部に公開されないように、ルーティング不可能な IP 経由で提供されます。

### <a name="endpoint-discovery"></a>エンドポイントの検出
VNET が有効な VM の場合は、静的でルーティング不可能な IP アドレス `169.254.169.254` からメタデータ サービスを利用できます。 スケジュールされたイベントの最新バージョンのフル エンドポイントは次のとおりです。 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01`

Virtual Machine が Virtual Network 内で作成されていない場合 (クラウド サービスと従来の VM の既定のケース)、使用する IP アドレスを検出する追加のロジックが必要となります。 [ホスト エンドポイントの検出](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm)方法については、こちらのサンプルをご覧ください。

### <a name="version-and-region-availability"></a>利用可能なバージョンとリージョン
スケジュールされたイベントのサービスは、バージョンによって管理されています。 バージョンは必須で、現在のバージョンは `2017-11-01` です。

| Version | リリースの種類 | リージョン | リリース ノート | 
| - | - | - | - |
| 2017-11-01 | 一般公開 | All | <li> スポット VM 削除の EventType 「Preempt」のサポートを追加する<br> | 
| 2017-08-01 | 一般公開 | All | <li> IaaS VM のリソース名から先頭のアンダースコアを削除<br><li>すべての要求にメタデータ ヘッダー要件を適用 | 
| 2017-03-01 | プレビュー | All |<li>最初のリリース

> [!NOTE] 
> スケジュールされたイベントの前のプレビュー リリースでは、api-version として {latest} がサポートされていました。 この形式はサポートされなくなり、今後非推奨となる予定です。

### <a name="enabling-and-disabling-scheduled-events"></a>スケジュールされたイベントの有効化と無効化
スケジュールされたイベントは、ユーザーが初めてイベントを要求したときに、サービスに対して有効になります。 最初の呼び出しでは、最大 2 分の応答遅延が発生すると予想されます。 エンドポイントのクエリを定期的に行って、今後のメンテナンス イベントと、実行されているメンテナンス アクティビティの状態を検出する必要があります。

スケジュールされたイベントは、サービスが 24 時間要求を行わないと、サービスに対して無効になります。

### <a name="user-initiated-maintenance"></a>ユーザーが開始したメンテナンス
ユーザーが Azure Portal、API、CLI または PowerShell を使用して開始した仮想マシンのメンテナンスによって、スケジュールされたイベントが発生します。 これによって、アプリケーションでメンテナンス準備ロジックをテストすることができ、アプリケーションでは、ユーザーが開始したメンテナンスのための準備することができます。

仮想マシンを再起動すると、`Reboot` 型のイベントがスケジュールされます。 仮想マシンを再デプロイすると、`Redeploy` 型のイベントがスケジュールされます。

## <a name="using-the-api"></a>API を使用する

### <a name="headers"></a>headers
メタデータ サービスのクエリを実行するときには、要求が意図せずリダイレクトされないように、ヘッダー `Metadata:true` を指定する必要があります。 `Metadata:true` ヘッダーは、スケジュールされたイベントのすべての要求で必要です。 要求にヘッダーを含めないと、メタデータ サービスから Bad Request (無効な要求) という応答が生成されます。

### <a name="query-for-events"></a>イベントのクエリ
次の呼び出しを行うだけで、スケジュールされたイベントのクエリを実行できます。

#### <a name="powershell"></a>Powershell
```
curl http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01 -H @{"Metadata"="true"}
```

応答には、スケジュールされたイベントの配列が含まれています。 空の配列は、現在スケジュールされているイベントがないことを意味します。
スケジュールされたイベントがある場合は、応答にイベントの配列が含まれます。 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze" | "Preempt",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
DocumentIncarnation は ETag であり、前回のクエリ以降にイベントのペイロードが変化したかどうかを簡単に検査する方法を提供します。

### <a name="event-properties"></a>イベントのプロパティ
|プロパティ  |  説明 |
| - | - |
| EventId | このイベントのグローバル一意識別子。 <br><br> 例: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | このイベントによって発生する影響。 <br><br> 値: <br><ul><li> `Freeze`:仮想マシンは数秒間の一時停止がスケジュールされています。 CPU とネットワーク接続が中断する場合がありますが、メモリや開いているファイルへの影響はありません。 <li>`Reboot`:Virtual Machine は再起動がスケジュールされています (非永続メモリは失われます)。 <li>`Redeploy`:Virtual Machine は別のノードへの移動がスケジュールされています (一時ディスクは失われます)。 <li>`Preempt`:スポット仮想マシンが削除されています（一時ディスクは失われています）。|
| ResourceType | このイベントが影響を与えるリソースの種類。 <br><br> 値: <ul><li>`VirtualMachine`|
| リソース| このイベントが影響を与えるリソースの一覧。 これには最大 1 つの[更新ドメイン](manage-availability.md)のマシンが含まれることが保証されますが、更新ドメインの一部のマシンは含まれない場合があります。 <br><br> 例: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | このイベントの状態。 <br><br> 値: <ul><li>`Scheduled`:このイベントは、`NotBefore` プロパティに指定された時間が経過した後で開始するようにスケジュールされています。<li>`Started`:このイベントは開始されています。</ul> `Completed` や同様の状態にならないイベントは、イベントの完了時に返されません。
| NotBefore| このイベントが開始される時間。 <br><br> 例: <br><ul><li> Mon, 19 Sep 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>イベントのスケジューリング
各イベントは、スケジュールされているイベントの種類に基づいて、将来の最小値の時間でスケジュールされます。 この時間は、イベントの `NotBefore` プロパティに反映されます。 

|EventType  | 最小値の通知 |
| - | - |
| Freeze| 約 15 分 |
| 再起動 | 約 15 分 |
| Redeploy | 10 分 |
| Preempt | 30 秒 |

### <a name="event-scope"></a>イベントの範囲     
スケジュールされたイベントの配信先は次のとおりです。
 - スタンドアロンの Virtual Machines
 - クラウド サービス内のすべての Virtual Machines      
 - 可用性セット内のすべての Virtual Machines      
 - スケール セットの配置グループ内のすべての Virtual Machines         

そのため、イベント内の `Resources` フィールドをチェックして、影響を受ける VM を特定する必要があります。 

### <a name="starting-an-event"></a>イベントの開始 

今後予定されているイベントを確認し、グレースフル シャットダウンのロジックを完了すると、`EventId` のメタデータ サービスに `POST` 呼び出しを行うことにより、未処理のイベントを承認できます。 これにより、通知の最小時間を短縮できる (可能な場合) ことが Azure に示されます。 

次に示すのは、`POST` 要求本文に指定する json です。 要求には、`StartRequests` の一覧を含める必要があります。 各 `StartRequest` には、迅速に進める必要があるイベントの `EventId` が含まれます。
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="powershell"></a>Powershell
```
curl -H @{"Metadata"="true"} -Method POST -Body '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' -Uri http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01
```

> [!NOTE] 
> イベントの受信確認により、イベントを受信確認した仮想マシンだけでなく、イベント内のすべての `Resources` でイベントが続行されます。 そのため、受信確認を調整するリーダーを選択できます。これは、`Resources` フィールド内の最初のマシンと同様に簡単です。


## <a name="powershell-sample"></a>PowerShell のサンプル 

次のサンプルでは、スケジュールされたイベントのメタデータ サービスにクエリを実行し、未処理の各イベントを承認しています。

```powershell
# How to get scheduled events 
function Get-ScheduledEvents($uri)
{
    $scheduledEvents = Invoke-RestMethod -Headers @{"Metadata"="true"} -URI $uri -Method get
    $json = ConvertTo-Json $scheduledEvents
    Write-Host "Received following events: `n" $json
    return $scheduledEvents
}

# How to approve a scheduled event
function Approve-ScheduledEvent($eventId, $uri)
{
    # Create the Scheduled Events Approval Document
    $startRequests = [array]@{"EventId" = $eventId}
    $scheduledEventsApproval = @{"StartRequests" = $startRequests} 
    
    # Convert to JSON string
    $approvalString = ConvertTo-Json $scheduledEventsApproval

    Write-Host "Approving with the following: `n" $approvalString

    # Post approval string to scheduled events endpoint
    Invoke-RestMethod -Uri $uri -Headers @{"Metadata"="true"} -Method POST -Body $approvalString
}

function Handle-ScheduledEvents($scheduledEvents)
{
    # Add logic for handling events here
}

######### Sample Scheduled Events Interaction #########

# Set up the scheduled events URI for a VNET-enabled VM
$localHostIP = "169.254.169.254"
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2017-11-01' -f $localHostIP 

# Get events
$scheduledEvents = Get-ScheduledEvents $scheduledEventURI

# Handle events however is best for your service
Handle-ScheduledEvents $scheduledEvents

# Approve events when ready (optional)
foreach($event in $scheduledEvents.Events)
{
    Write-Host "Current Event: `n" $event
    $entry = Read-Host "`nApprove event? Y/N"
    if($entry -eq "Y" -or $entry -eq "y")
    {
        Approve-ScheduledEvent $event.EventId $scheduledEventURI 
    }
}
``` 

## <a name="next-steps"></a>次の手順 

- Azure Friday で、[スケジュールされたイベントのデモ](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance)をご覧ください。 
- [Azure Instance Metadata スケジュールされたイベントの GitHub リポジトリ](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)で、スケジュールされたイベントのコード サンプルをご覧ください。
- [インスタンス メタデータ サービス](instance-metadata-service.md)で使用可能な API の詳細についてご覧ください。
- [Azure での Windows 仮想マシンの計画メンテナンス](planned-maintenance.md)に関するページをご覧ください。
