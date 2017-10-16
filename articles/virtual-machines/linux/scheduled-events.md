---
title: "Azure の Linux VM 向けのスケジュールされたイベント | Microsoft Docs"
description: "Linux 仮想マシンでは Azure メタデータ サービスを使用してイベントがスケジュールされています。"
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2017
ms.author: zivr
ms.openlocfilehash: 75e509a7e39f5b268ce550d0c4dea2261d4fe56f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-metadata-service-scheduled-events-preview-for-linux-vms"></a>Azure Metadata Service: Linux VM のスケジュールされたイベント (プレビュー)

> [!NOTE] 
> プレビューは、使用条件に同意することを条件に使用することができます。 詳細については、[Microsoft Azure プレビューのMicrosoft Azure 追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。
>

スケジュールされたイベント機能は、Azure Metadata Service のサブサービスの 1 つです。 今後のイベント (再起動など) に関する情報が表示されるため、アプリケーションはそのイベントに備え、中断を最小限に抑えることができます。 このサービスは、PaaS と IaaS を含むすべての Azure Virtual Machine の種類で利用できます。 スケジュールされたイベント機能により、Virtual Machine が予防的タスクを実行する時間が確保され、イベントの影響を最小限に抑えることができます。 

スケジュールされたイベントは、Windows VM と Linux VM で利用できます。 Windows のスケジュールされたイベントの詳細については、[Windows VM のスケジュールされたイベント](../windows/scheduled-events.md)に関する記事をご覧ください。

## <a name="why-scheduled-events"></a>スケジュールされたイベントを使用する理由

スケジュールされたイベント機能を使用すると、プラットフォームによって開始されるメンテナンスやユーザーによって開始される操作によるサービスへ影響を最小限に抑える処置を講ずることができます。 

状態を維持するためにレプリケーションの手法を使用するマルチ インスタンスのワークロードでは、複数のインスタンスで頻繁に機能停止が発生する可能性があります。 このような機能停止により、負荷の高いタスク (インデックスの再構築など) や、レプリカの損失が発生することがあります。 

その他の多くのケースでは、実行中のトランザクションの完了 (またはキャンセル) などのグレースフル シャットダウン シーケンスの実行、クラスター内のほかの VM へのタスクの再割り当て (手動フェールオーバー)、ネットワーク ロード バランサー プールからの Virtual Machine の削除を行うことにより、サービスの可用性全体が向上する場合があります。 

今後のイベントについて管理者に通知したり、そのようなイベントのログを記録したりすることで、クラウドでホストされているアプリケーションのサービスの向上につながる場合があります。

Azure Metadata Service では、次のユース ケースでスケジュールされたイベントを表示します。
-   プラットフォームによって開始されたメンテナンス (ホスト OS ロールアウトなど)
-   ユーザーが開始した呼び出し (ユーザーによる再起動や VM の再デプロイなど)


## <a name="the-basics"></a>基本  

Azure Metadata Service では、VM 内部からアクセスできる REST エンドポイントを使用した Virtual Machine の実行に関する情報が公開されます。 情報は、VM の外部に公開されないように、ルーティング不可能な IP 経由で提供されます。

### <a name="scope"></a>Scope
スケジュールされたイベントは、クラウド サービス内のすべての Virtual Machine または可用性セット内のすべての Virtual Machine に表示されます。 そのため、イベント内の `Resources` フィールドをチェックして、影響を受ける VM を特定する必要があります。 

### <a name="discovering-the-endpoint"></a>エンドポイントの検出
Virtual Machine が Virtual Network (VNet) 内で作成されている場合、メタデータ サービスはルーティング不可能な静的 IP `169.254.169.254` 経由で提供されます。
Virtual Machine が Virtual Network 内で作成されていない場合 (クラウド サービスと従来の VM の既定のケース)、使用するエンドポイントを検出する追加のロジックが必要となります。 [ホスト エンドポイントの検出](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm)方法については、こちらのサンプルをご覧ください。

### <a name="versioning"></a>バージョン管理 
インスタンス メタデータ サービスはバージョン管理されています。 バージョンは必須で、現在のバージョンは `2017-03-01` です。

> [!NOTE] 
> スケジュールされたイベントの前のプレビュー リリースでは、api-version として {latest} がサポートされていました。 この形式はサポートされなくなり、今後廃止される予定です。

### <a name="using-headers"></a>ヘッダーの使用
メタデータ サービスのクエリを実行するときには、要求が意図せずリダイレクトされないように、ヘッダー `Metadata: true` を指定する必要があります。

### <a name="enabling-scheduled-events"></a>スケジュールされたイベントの有効化
スケジュールされたイベントを初めて呼び出すとき、Azure はこの機能を Virtual Machine で暗黙的に有効化します。 そのため、最初の呼び出しでは最大 2 分の応答遅延が発生すると予想されます。

### <a name="user-initiated-maintenance"></a>ユーザーが開始したメンテナンス
ユーザーが Azure Portal、API、CLI または PowerShell を使用して開始した仮想マシンのメンテナンスによって、スケジュールされたイベントが発生します。 これによって、アプリケーションでメンテナンス準備ロジックをテストすることができ、アプリケーションでは、ユーザーが開始したメンテナンスのための準備することができます。

仮想マシンを再起動すると、`Reboot` 型のイベントがスケジュールされます。 仮想マシンを再デプロイすると、`Redeploy` 型のイベントがスケジュールされます。

> [!NOTE] 
> 現在、最大で 10 ユーザーが開始したメンテナンス操作を同時にスケジュールできます。 この制限は、スケジュールされたイベントが一般公開される前に緩和されます。

> [!NOTE] 
> 現在、スケジュールされたイベントが発生する結果となる、ユーザーが開始したメンテナンスは構成することができません。 構成機能は、将来のリリースで予定されています。

## <a name="using-the-api"></a>API を使用する

### <a name="query-for-events"></a>イベントのクエリ
次の呼び出しを行うだけで、スケジュールされたイベントのクエリを実行できます。

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01
```

応答には、スケジュールされたイベントの配列が含まれています。 空の配列は、現在スケジュールされているイベントがないことを意味します。
スケジュールされたイベントがある場合は、応答にイベントの配列が含まれます。 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},              
        }
    ]
}
```

### <a name="event-properties"></a>イベントのプロパティ
|プロパティ  |  説明 |
| - | - |
| EventId | このイベントのグローバル一意識別子。 <br><br> 例: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | このイベントによって発生する影響。 <br><br> 値: <br><ul><li> `Freeze`: Virtual Machine は数秒間の一時停止がスケジュールされています。 CPU は中断しますが、メモリ、開いているファイル、ネットワーク接続への影響はありません。 <li>`Reboot`: Virtual Machine は再起動がスケジュールされています (非永続メモリは失われます)。 <li>`Redeploy`: Virtual Machine は別のノードへの移動がスケジュールされています (一時ディスクは失われます)。 |
| ResourceType | このイベントが影響を与えるリソースの種類。 <br><br> 値: <ul><li>`VirtualMachine`|
| リソース| このイベントが影響を与えるリソースの一覧。 これには最大 1 つの[更新ドメイン](manage-availability.md)のマシンが含まれることが保証されますが、更新ドメインの一部のマシンは含まれない場合があります。 <br><br> 例: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | このイベントの状態。 <br><br> 値: <ul><li>`Scheduled`: このイベントは、`NotBefore` プロパティに指定された時間が経過した後で開始するようにスケジュールされています。<li>`Started`: このイベントは開始されています。</ul> `Completed` や同様の状態にならないイベントは、イベントの完了時に返されません。
| NotBefore| このイベントが開始される時間。 <br><br> 例: <br><ul><li> 2016-09-19T18:29:47Z  |

### <a name="event-scheduling"></a>イベントのスケジューリング
各イベントは、スケジュールされているイベントの種類に基づいて、将来の最小値の時間でスケジュールされます。 この時間は、イベントの `NotBefore` プロパティに反映されます。 

|EventType  | 最小値の通知 |
| - | - |
| Freeze| 約 15 分 |
| Reboot | 約 15 分 |
| Redeploy | 10 分 |

### <a name="starting-an-event"></a>イベントの開始 

今後予定されているイベントを確認し、グレースフル シャットダウンのロジックを完了すると、`EventId` のメタデータ サービスに `POST` 呼び出しを行うことにより、未処理のイベントを承認できます。 これにより、通知の最小時間を短縮できる (可能な場合) ことが Azure に示されます。 

```
curl -H Metadata:true -X POST -d '{"DocumentIncarnation":"5", "StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01
```

> [!NOTE] 
> イベントの受信確認により、イベントを受信確認した仮想マシンだけでなく、イベント内のすべての `Resources` でイベントが続行されます。 そのため、受信確認を調整するリーダーを選択できます。これは、`Resources` フィールド内の最初のマシンと同様に簡単です。




## <a name="python-sample"></a>Python のサンプル 

次のサンプルでは、スケジュールされたイベントのメタデータ サービスにクエリを実行し、未処理の各イベントを承認しています。

```python
#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01"
headers = "{Metadata:true}"
this_host = socket.gethostname()

def get_scheduled_events():
   req = urllib2.Request(metadata_url)
   req.add_header('Metadata', 'true')
   resp = urllib2.urlopen(req)
   data = json.loads(resp.read())
   return data

def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid = evt['EventId']
        status = evt['EventStatus']
        resources = evt['Resources']
        eventtype = evt['EventType']
        resourcetype = evt['ResourceType']
        notbefore = evt['NotBefore'].replace(" ","_")
        if this_host in resources:
            print "+ Scheduled Event. This host is scheduled for " + eventype + " not before " + notbefore
            # Add logic for handling events here

def main():
   data = get_scheduled_events()
   handle_scheduled_events(data)
   
if __name__ == '__main__':
  main()
  sys.exit(0)
```

## <a name="next-steps"></a>次のステップ 

- [インスタンス メタデータ サービス](instance-metadata-service.md)で使用可能な API の詳細についてご覧ください。
- [Azure での Linux 仮想マシンの計画的メンテナンス](planned-maintenance.md)に関するページをご覧ください。
