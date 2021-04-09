---
title: Azure の Windows VM のスケジュール化されたイベント
description: Windows 仮想マシンでは Azure Metadata Service を使用してイベントがスケジュールされています。
author: EricRadzikowskiMSFT
ms.service: virtual-machines
ms.subservice: scheduled-events
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/01/2020
ms.author: ericrad
ms.reviwer: mimckitt
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3a388ade2b44260bfa21e22866d85a46e482bc97
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499953"
---
# <a name="azure-metadata-service-scheduled-events-for-windows-vms"></a>Azure Metadata Service: Windows VM のスケジュールされたイベント

スケジュールされたイベントとは、仮想マシン (VM) のメンテナンスに備えるための時間をアプリケーションに与える Azure Metadata Service です。 今後のメンテナンス イベント (再起動など) に関する情報を提供することで、アプリケーションがイベントの準備を行い、中断を制限できるようにします。 このサービスは、Windows および Linux の、PaaS と IaaS を含むすべての Azure Virtual Machine の種類で利用できます。 

Linux のスケジュールされたイベントの詳細については、[Linux VM のスケジュールされたイベント](../linux/scheduled-events.md)に関する記事をご覧ください。

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

- [プラットフォームで開始されるメンテナンス](../maintenance-and-updates.md?bc=/azure/virtual-machines/windows/breadcrumb/toc.json&toc=/azure/virtual-machines/windows/toc.json) (例: VM の再起動、ライブ マイグレーション、ホストの更新を保持するメモリ)
- 仮想マシンは、じきに故障することが予測されている[劣化したホスト ハードウェア](https://azure.microsoft.com/blog/find-out-when-your-virtual-machine-hardware-is-degraded-with-scheduled-events)で実行されています。
- ユーザーが開始するメンテナンス (たとえば、ユーザーによる再起動や VM の再デプロイ)
- [スポット VM](../spot-vms.md) および[スポット スケール セット](../../virtual-machine-scale-sets/use-spot.md) インスタンスの削除。

## <a name="the-basics"></a>基本  

  Metadata Service では、VM 内部からアクセスできる REST エンドポイントを使用した VM の実行に関する情報が公開されます。 情報は、VM の外部に公開されないように、ルーティング不可能な IP 経由で提供されます。

### <a name="scope"></a>Scope
スケジュールされたイベントの配信先は次のとおりです。

- スタンドアロンの仮想マシン。
- クラウド サービス内のすべての VM。
- 可用性セット内のすべての VM。
- 可用性ゾーン内のすべての VM。
- スケール セットの配置グループすべての VM。 

> [!NOTE]
> ファブリック コントローラー (FC) テナント内のすべての仮想マシン (VM) の Scheduled Events は、FC テナント内のすべての VM に配信されます。 FC テナントは、可用性ゾーンの使用に関係なく、スタンドアロン VM、クラウド サービス全体、可用性セット全体、および VM Scale Sets (VMSS) の配置グループに相当します。 

そのため、イベント内の `Resources` フィールドをチェックして、影響を受ける VM を特定する必要があります。

### <a name="endpoint-discovery"></a>エンドポイントの検出
VNET が有効な VM の場合は、静的でルーティング不可能な IP アドレス `169.254.169.254` から Metadata Service を利用できます。 スケジュールされたイベントの最新バージョンのフル エンドポイントは次のとおりです。 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01`

VM が仮想ネットワーク内で作成されていない場合 (クラウド サービスと従来の VM の既定のケース)、使用する IP アドレスを検出する追加のロジックが必要となります。 [ホスト エンドポイントの検出](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm)方法の詳細については、このサンプルを参照してください。

### <a name="version-and-region-availability"></a>利用可能なバージョンとリージョン
スケジュールされたイベントのサービスは、バージョンによって管理されています。 バージョンは必須で、現在のバージョンは `2019-01-01` です。

| Version | リリースの種類 | リージョン | リリース ノート | 
| - | - | - | - | 
| 2019-08-01 | 一般公開 | All | <li> EventSource のサポートを追加しました |
| 2019-04-01 | 一般公開 | All | <li> イベントの説明のサポートを追加しました |
| 2019-01-01 | 一般公開 | All | <li> 仮想マシン スケール セットの EventType "Terminate" のサポートが追加されました |
| 2017-11-01 | 一般公開 | All | <li> スポット VM 削除の EventType 「Preempt」のサポートを追加する<br> | 
| 2017-08-01 | 一般公開 | All | <li> IaaS VM のリソース名から先頭のアンダースコアを削除<br><li>すべての要求にメタデータ ヘッダー要件を適用 | 
| 2017-03-01 | プレビュー | All | <li>最初のリリース |


> [!NOTE] 
> スケジュールされたイベントの前のプレビュー リリースでは、api-version として {latest} がサポートされていました。 この形式はサポートされなくなり、今後非推奨となる予定です。

### <a name="enabling-and-disabling-scheduled-events"></a>Scheduled Events の有効化と無効化
スケジュールされたイベントは、ユーザーが初めてイベントを要求したときに、サービスに対して有効になります。 最初の呼び出しでは、最大 2 分の応答遅延が発生すると予想されます。

スケジュールされたイベントは、サービスが 24 時間要求を行わないと、サービスに対して無効になります。

### <a name="user-initiated-maintenance"></a>ユーザー開始メンテナンス
ユーザーが Azure Portal、API、CLI または PowerShell を使用して開始した VM のメンテナンスによって、スケジュールされたイベントが発生します。 これによって、アプリケーションでメンテナンス準備ロジックをテストすることができ、アプリケーションでは、ユーザーが開始したメンテナンスの準備をすることができます。

VM を再起動すると、型 `Reboot` のイベントがスケジュールされます。 VM を再デプロイすると、型 `Redeploy` のイベントがスケジュールされています。

## <a name="use-the-api"></a>API の使用

### <a name="headers"></a>ヘッダー
メタデータ サービスのクエリを実行するときには、要求が意図せずリダイレクトされないように、ヘッダー `Metadata:true` を指定する必要があります。 `Metadata:true` ヘッダーは、スケジュールされたイベントのすべての要求で必要です。 要求にヘッダーを含めないと、メタデータ サービスから Bad Request (無効な要求) という応答があります。

### <a name="query-for-events"></a>イベントのクエリ
次の呼び出しを行うと、スケジュールされたイベントのクエリを実行できます。

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

応答には、スケジュールされたイベントの配列が含まれています。 空の配列は、現在スケジュールされているイベントがないことを意味します。
スケジュールされたイベントがある場合は、応答にイベントの配列が含まれます。 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze" | "Preempt" | "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},       
            "Description": {eventDescription},
            "EventSource" : "Platform" | "User",
        }
    ]
}
```

### <a name="event-properties"></a>イベントのプロパティ
|プロパティ  |  説明 |
| - | - |
| EventId | このイベントのグローバル一意識別子。 <br><br> 例: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | このイベントによって発生する影響。 <br><br> 値: <br><ul><li> `Freeze`:仮想マシンは数秒間の一時停止がスケジュールされています。 CPU とネットワーク接続が中断する場合がありますが、メモリや開いているファイルへの影響はありません。<li>`Reboot`:Virtual Machine は再起動がスケジュールされています (非永続メモリは失われます)。 <li>`Redeploy`:Virtual Machine は別のノードへの移動がスケジュールされています (一時ディスクは失われます)。 <li>`Preempt`:スポット仮想マシンが削除されています（一時ディスクは失われています）。 <li> `Terminate`:仮想マシンは削除がスケジュールされています。 |
| ResourceType | このイベントが影響を与えるリソースの種類。 <br><br> 値: <ul><li>`VirtualMachine`|
| リソース| このイベントが影響を与えるリソースの一覧。 これには最大 1 つの[更新ドメイン](../availability.md)のマシンが含まれることが保証されますが、更新ドメインの一部のマシンは含まれない場合があります。 <br><br> 例: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | このイベントの状態。 <br><br> 値: <ul><li>`Scheduled`:このイベントは、`NotBefore` プロパティに指定された時間が経過した後で開始するようにスケジュールされています。<li>`Started`:このイベントは開始されています。</ul> `Completed` や類似の状態が提供されることはありません。 イベントが完了すると、イベントは返されなくなります。
| NotBefore| このイベントが開始される時間。 <br><br> 例: <br><ul><li> Mon, 19 Sep 2016 18:29:47 GMT  |
| 説明 | このイベントの説明。 <br><br> 例: <br><ul><li> ホスト サーバーのメンテナンス中です。 |
| EventSource | イベントのイニシエーター。 <br><br> 例: <br><ul><li> `Platform`:このイベントは、プラットフォームによって開始されています。 <li>`User`:このイベントは、ユーザーによって開始されています。 |

### <a name="event-scheduling"></a>イベントのスケジューリング
各イベントは、スケジュールされているイベントの種類に基づいて、将来の最小値の時間でスケジュールされます。 この時間は、イベントの `NotBefore` プロパティに反映されます。 

|EventType  | 最小値の通知 |
| - | - |
| Freeze| 約 15 分 |
| 再起動 | 約 15 分 |
| Redeploy | 10 分 |
| Preempt | 30 秒 |
| Terminate | [ユーザーが構成可能](../../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md#enable-terminate-notifications):5 から 15 分 |

> [!NOTE] 
> Azure では、劣化したハードウェアに起因するホストの故障を予測が可能になり、移行をスケジュールすることでサービスの中断を軽減しようとすることがあります。 影響を受ける仮想マシンには、スケジュールされているイベントと `NotBefore` が届きます。これは通常、2、3 日先になります。 実際の時間は、予測された故障のリスク評価によって異なります。 Azure では、可能であれば、7 日前に通知を行いますが、実際の時間はさまざまであり、ハードウェアが今にも故障する可能性が高い場合、7 日より短くなることがあります。 システムによって開始される移行の前にハードウェアで障害が発生した場合に備えて、サービスのリスクを最小限に抑えるために、できるだけ早く仮想マシンをご自身で再デプロイすることをお勧めします。

### <a name="polling-frequency"></a>ポーリング頻度

更新のエンドポイントは、任意の頻度でポーリングできます。 ただし、要求間の間隔が長くなるほど、発生するイベントに対応するのが遅くなる可能性があります。 ほとんどのイベントは、5 - 15 分前に通知されますが、場合によっては、通知がわずか 30 秒前ということもあります。 対策を講じるための時間をできるだけ多く確保するために、1 秒に 1 回サービスをポーリングすることをお勧めします。

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
curl -H Metadata:true -X POST -d '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01
```

> [!NOTE] 
> イベントの受信確認により、イベントを受信確認した VM だけでなく、イベント内のすべての `Resources` でイベントが続行されます。 そのため、受信確認を調整するリーダーを選択できます。これは、`Resources` フィールド内の最初のマシンと同様に簡単です。

## <a name="python-sample"></a>Python のサンプル 

次のサンプルでは、スケジュールされたイベントのメタデータ サービスにクエリを実行し、未処理の各イベントを承認しています。

```python
#!/usr/bin/python

import json
import socket
import urllib2

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01"
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
        notbefore = evt['NotBefore'].replace(" ", "_")
    description = evt['Description']
    eventSource = evt['EventSource']
        if this_host in resources:
            print("+ Scheduled Event. This host " + this_host +
                " is scheduled for " + eventtype + 
        " by " + eventSource + 
        " with description " + description +
        " not before " + notbefore)
            # Add logic for handling events here


def main():
    data = get_scheduled_events()
    handle_scheduled_events(data)


if __name__ == '__main__':
    main()
```

## <a name="next-steps"></a>次のステップ 
- [Azure Friday でのスケジュールされたイベント](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance)のデモをご覧ください。 
- スケジュールされたイベントのコード サンプルは、[Azure Instance Metadata Scheduled Events の GitHub リポジトリ](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)をご覧ください。
- 「[インスタンス メタデータ サービス](instance-metadata-service.md)」で使用可能な API の詳細についてご覧ください。
- [Azure での Windows 仮想マシンの計画メンテナンス](../maintenance-and-updates.md?bc=/azure/virtual-machines/windows/breadcrumb/toc.json&toc=/azure/virtual-machines/windows/toc.json)に関するページをご覧ください。
