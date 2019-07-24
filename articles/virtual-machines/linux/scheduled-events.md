---
title: Azure の Linux VM 向けのスケジュールされたイベント | Microsoft Docs
description: Linux 仮想マシンに Azure Metadata Service を使用してイベントをスケジュールします。
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: ericrad
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: 6337477b55addefb7579d6f328473428ba72ba24
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58446124"
---
# <a name="azure-metadata-service-scheduled-events-for-linux-vms"></a>Azure Metadata Service: Linux VM の Scheduled Events

スケジュールされたイベントとは、仮想マシン (VM) のメンテナンスに備えるための時間をアプリケーションに与える Azure Metadata Service です。 今後のメンテナンス イベント (再起動など) に関する情報を提供することで、アプリケーションがイベントの準備を行い、中断を制限できるようにします。 このサービスは、Windows および Linux の、PaaS と IaaS を含むすべての Azure Virtual Machine の種類で利用できます。 

Windows のスケジュールされたイベントの詳細については、[Windows VM のスケジュールされたイベント](../windows/scheduled-events.md)に関する記事をご覧ください。

> [!Note] 
> スケジュールされたイベントは、すべての Azure リージョンで一般公開されています。 最新リリースについては、「[利用可能なバージョンとリージョン](#version-and-region-availability)」をご覧ください。

## <a name="why-use-scheduled-events"></a>スケジュールされたイベントを使用する理由

多くのアプリケーションに、VM のメンテナンスに備える時間が得られるメリットがあります。 この時間を使用して、可用性、信頼性、およびサービスを向上させる、次のようなアプリケーション固有のタスクを実行できます。 

- チェックポイントと復元
- 接続のドレイン
- プライマリ レプリカのフェールオーバー
- ロード バランサー プールからの削除
- イベント ログ
- グレースフル シャットダウン

スケジュールされたイベントを使用すると、アプリケーションはメンテナンスが行われる時期を検出し、その影響を制限するタスクをトリガーできます。  

スケジュールされたイベントは、次のユース ケースでイベントを提供します。

- プラットフォームによって開始されたメンテナンス (たとえば、ホスト OS の更新など)
- ハードウェアの機能低下
- ユーザーが開始するメンテナンス (たとえば、ユーザーによる再起動や VM の再デプロイ)
- スケール セット内の[優先順位の低い VM の削除](https://azure.microsoft.com/en-us/blog/low-priority-scale-sets)

## <a name="the-basics"></a>基本操作  

  Metadata Service では、VM 内部からアクセスできる REST エンドポイントを使用した VM の実行に関する情報が公開されます。 情報は、VM の外部に公開されないように、ルーティング不可能な IP 経由で提供されます。

### <a name="scope"></a>Scope (スコープ)
スケジュールされたイベントの配信先は次のとおりです。

- クラウド サービス内のすべての VM。
- 可用性セット内のすべての VM。
- スケール セットの配置グループすべての VM。 

そのため、イベント内の `Resources` フィールドをチェックして、影響を受ける VM を特定する必要があります。

### <a name="endpoint-discovery"></a>エンドポイントの検出
VNET が有効な VM の場合は、静的でルーティング不可能な IP アドレス `169.254.169.254` から Metadata Service を利用できます。 スケジュールされたイベントの最新バージョンのフル エンドポイントは次のとおりです。 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01`

VM が仮想ネットワーク内で作成されていない場合 (クラウド サービスと従来の VM の既定のケース)、使用する IP アドレスを検出する追加のロジックが必要となります。 [ホスト エンドポイントの検出](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm)方法の詳細については、このサンプルを参照してください。

### <a name="version-and-region-availability"></a>利用可能なバージョンとリージョン
スケジュールされたイベントのサービスは、バージョンによって管理されています。 バージョンは必須で、現在のバージョンは `2017-11-01` です。

| Version | リリースの種類 | リージョン | リリース ノート | 
| - | - | - | - | 
| 2017-11-01 | 一般公開 | All | <li> 優先順位の低い VM の削除の EventType 'Preempt' のサポートを追加<br> | 
| 2017-08-01 | 一般公開 | All | <li> IaaS VM のリソース名から先頭のアンダースコアを削除<br><li>すべての要求にメタデータ ヘッダー要件を適用 | 
| 2017-03-01 | プレビュー | All | <li>最初のリリース


> [!NOTE] 
> スケジュールされたイベントの前のプレビュー リリースでは、api-version として {latest} がサポートされていました。 この形式はサポートされなくなり、今後非推奨となる予定です。

### <a name="enabling-and-disabling-scheduled-events"></a>スケジュールされたイベントの有効化と無効化
スケジュールされたイベントは、ユーザーが初めてイベントを要求したときに、サービスに対して有効になります。 最初の呼び出しでは、最大 2 分の応答遅延が発生すると予想されます。

スケジュールされたイベントは、サービスが 24 時間要求を行わないと、サービスに対して無効になります。

### <a name="user-initiated-maintenance"></a>ユーザー開始メンテナンス
ユーザーが Azure Portal、API、CLI または PowerShell を使用して開始した VM のメンテナンスによって、スケジュールされたイベントが発生します。 これによって、アプリケーションでメンテナンス準備ロジックをテストすることができ、アプリケーションでは、ユーザーが開始したメンテナンスの準備をすることができます。

VM を再起動すると、型 `Reboot` のイベントがスケジュールされます。 VM を再デプロイすると、型 `Redeploy` のイベントがスケジュールされています。

## <a name="use-the-api"></a>API の使用

### <a name="headers"></a>headers
メタデータ サービスのクエリを実行するときには、要求が意図せずリダイレクトされないように、ヘッダー `Metadata:true` を指定する必要があります。 `Metadata:true` ヘッダーは、スケジュールされたイベントのすべての要求で必要です。 要求にヘッダーを含めないと、メタデータ サービスから Bad Request (無効な要求) という応答があります。

### <a name="query-for-events"></a>イベントのクエリ
次の呼び出しを行うと、スケジュールされたイベントのクエリを実行できます。

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
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

### <a name="event-properties"></a>イベントのプロパティ
|プロパティ  |  説明 |
| - | - |
| EventId | このイベントのグローバル一意識別子。 <br><br> 例: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | このイベントによって発生する影響。 <br><br> 値: <br><ul><li> `Freeze`:Virtual Machine は数秒間の一時停止がスケジュールされています。 CPU は中断しますが、メモリ、開いているファイル、ネットワーク接続への影響はありません。 <li>`Reboot`:Virtual Machine は再起動がスケジュールされています (非永続メモリは失われます)。 <li>`Redeploy`:Virtual Machine は別のノードへの移動がスケジュールされています (一時ディスクは失われます)。 <li>`Preempt`:優先順位の低い仮想マシンを削除中です (一時ディスクは失われます)。|
| ResourceType | このイベントが影響を与えるリソースの種類。 <br><br> 値: <ul><li>`VirtualMachine`|
| リソース| このイベントが影響を与えるリソースの一覧。 これには最大 1 つの[更新ドメイン](manage-availability.md)のマシンが含まれることが保証されますが、更新ドメインの一部のマシンは含まれない場合があります。 <br><br> 例: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | このイベントの状態。 <br><br> 値: <ul><li>`Scheduled`:このイベントは、`NotBefore` プロパティに指定された時間が経過した後で開始するようにスケジュールされています。<li>`Started`:このイベントは開始されています。</ul> `Completed` や類似の状態が提供されることはありません。 イベントが完了すると、イベントは返されなくなります。
| NotBefore| このイベントが開始される時間。 <br><br> 例: <br><ul><li> Mon, 19 Sep 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>イベントのスケジューリング
各イベントは、スケジュールされているイベントの種類に基づいて、将来の最小値の時間でスケジュールされます。 この時間は、イベントの `NotBefore` プロパティに反映されます。 

|EventType  | 最小値の通知 |
| - | - |
| Freeze| 約 15 分 |
| 再起動 | 約 15 分 |
| Redeploy | 10 分 |
| Preempt | 30 秒 |

### <a name="start-an-event"></a>イベントの開始 

今後予定されているイベントを確認し、グレースフル シャットダウンのロジックを完了すると、`EventId` を使用してメタデータ サービスに `POST` 呼び出しを行うことにより、未処理のイベントを承認できます。 この呼び出しは、通知の最小時間を短縮できる (可能な場合) ことが Azure に示されます。 

次に示すのは、`POST` 要求本文で求められている JSON のサンプルです。 要求には、`StartRequests` の一覧を含める必要があります。 各 `StartRequest` には、迅速に進める必要があるイベントの `EventId` が含まれます。
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="bash-sample"></a>Bash のサンプル
```
curl -H Metadata:true -X POST -d '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01
```

> [!NOTE] 
> イベントの受信確認により、イベントを受信確認した VM だけでなく、イベント内のすべての `Resources` でイベントが続行されます。 そのため、受信確認を調整するリーダーを選択できます。これは、`Resources` フィールド内の最初のマシンと同様に簡単です。

## <a name="python-sample"></a>Python のサンプル 

次のサンプルでは、スケジュールされたイベントのメタデータ サービスにクエリを実行し、未処理の各イベントを承認しています。

```python
#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01"
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
            print "+ Scheduled Event. This host " + this_host + " is scheduled for " + eventtype + " not before " + notbefore
            # Add logic for handling events here


def main():
   data = get_scheduled_events()
   handle_scheduled_events(data)

if __name__ == '__main__':
  main()
  sys.exit(0)
```

## <a name="next-steps"></a>次の手順 
- [Azure Friday でのスケジュールされたイベント](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance)のデモをご覧ください。 
- スケジュールされたイベントのコード サンプルは、[Azure Instance Metadata Scheduled Events の GitHub リポジトリ](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)をご覧ください。
- 「[インスタンス メタデータ サービス](instance-metadata-service.md)」で使用可能な API の詳細についてご覧ください。
- [Azure での Linux 仮想マシンの計画的メンテナンス](planned-maintenance.md)に関するページをご覧ください。
