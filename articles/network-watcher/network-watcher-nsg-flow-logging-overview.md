---
title: Azure Network Watcher を使用したネットワーク セキュリティ グループに関するフローのログ記録の概要 | Microsoft Docs
description: この記事では Azure Network Watcher の機能である NSG のフロー ログの使用方法について説明します。
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 47d91341-16f1-45ac-85a5-e5a640f5d59e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: a77cc22c7a56c29b5b42a032af3d0ea0b2c17d88
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563523"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>ネットワーク セキュリティ グループのフローのログ記録の概要

ネットワーク セキュリティ グループ (NSG) のフローのログは、NSG を使用したイングレスおよびエグレス IP トラフィックに関する情報を表示できる Network Watcher の機能です。 フロー ログは JSON 形式で記述され、規則ごとの送信フローと受信フロー、フローが適用されるネットワーク インターフェイス (NIC)、フローに関する 5 組の情報 (送信元/宛先 IP、送信元/宛先ポート、プロトコル)、トラフィックが許可されているか拒否されているかが示されます。また、バージョン 2 ではスループット情報 (バイトとパケット) も示されます。


![フロー ログの概要](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

フロー ログは NSG を対象としていますが、その他のログと同じようには表示されません。 フロー ログは、次の例で示すようにストレージ アカウント内と下記のログ記録パスにのみ格納されます。

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```
[トラフィック分析](traffic-analytics.md)を使用してフロー ログを分析し、ネットワーク トラフィックに関する分析情報を得ることができます。

その他のログで見られる同様のリテンション期間ポリシーが、フロー ログに適用されます。 ログの保持ポリシーの期間は、1 日から 2,147,483,647 日まで設定できます。 リテンション期間ポリシーが設定されていない場合、ログは無期限に保持されます。

> [!NOTE] 
> NSG フロー ログ記録と共にアイテム保持ポリシー機能を使用すると、大量のストレージ操作とそれに関連するコストが発生する可能性があります。 アイテム保持ポリシー機能が不要な場合は、この値を 0 に設定することをお勧めします。


## <a name="log-file"></a>ログ ファイル

フローのログには、次のプロパティが含まれています。

* **time** - イベントがログに記録された時間
* **systemId** - ネットワーク セキュリティ グループのリソース ID。
* **category** - イベントのカテゴリです。 カテゴリは常に **NetworkSecurityGroupFlowEvent** となります。
* **resourceid** - NSG のリソース ID
* **operationName** -常に NetworkSecurityGroupFlowEvents
* **properties** - フローのプロパティのコレクション
    * **Version** - フロー ログ イベントのスキーマのバージョン番号
    * **flows** - フローのコレクション。 このプロパティには異なるルールに対して複数のエントリがあります
        * **rule** - フローが一覧表示されているルール
            * **flows** - フローのコレクション
                * **mac** - フローが収集された VM の NIC の MAC アドレス
                * **flowTuples** - コンマで区切る形式で表現されたフローの組に対して複数のプロパティを含む文字列
                    * **Time Stamp** - UNIX epoch 形式でフローが発生した際のタイム スタンプ
                    * **Source IP** - 発信元 IP
                    * **Destination IP** - 宛先 IP
                    * **Source Port** - 発信ポート
                    * **Destination Port** - 宛先ポート
                    * **Protocol** - フローのプロトコル。 有効な値は TCP の **T** と UDP の **U** です
                    * **Traffic Flow** - トラフィック フローの方向。 有効な値は受信の **I** と送信の **O** です。
                    * **Traffic Decision** - トラフィックが許可された、または拒否された。 有効な値は許可の **A** と拒否の **D** です。
                    * **Flow State - バージョン 2 のみ** - フローの状態をキャプチャします。 次の状態があります。**B**:開始。フローが作成された時点です。 統計は提供されません。 **C**: 継続中。フローが進行中です。 5 分間隔で統計が提供されます。 **E**:終了。フローが終了した時点です。 統計が提供されます。
                    * **Packets - Source to destination - バージョン 2 のみ** - 最後の更新以降に送信元から宛先に送信された TCP または UDP パケットの総数。
                    * **Bytes sent - Source to destination - バージョン 2 のみ** - 最後の更新以降に送信元から宛先に送信された TCP または UDP パケット バイトの総数。 パケットのバイト数には、パケット ヘッダーとペイロードが含まれます。
                    * **Packets - Destination to source - バージョン 2 のみ** - 最後の更新以降に宛先から送信元に送信された TCP または UDP パケットの総数。
                    * **Bytes sent - Destination to source - バージョン 2 のみ** - 最後の更新以降に宛先から送信元に送信された TCP および UDP パケット バイトの総数。 パケットのバイト数には、パケット ヘッダーとペイロードが含まれます。

## <a name="nsg-flow-logs-version-2"></a>NSG フロー ログ バージョン 2

ログのバージョン 2 ではフロー状態が導入されます。 受信するフロー ログのバージョンを構成することができます。 フロー ログを有効にする方法については、[フロー ログ記録の有効化](network-watcher-nsg-flow-logging-portal.md)に関するページをご覧ください。

フローが開始されると、フロー状態 *B* が記録されます。 フロー状態 *C* およびフロー状態 *E* は、それぞれフローの継続とフローの終了を示す状態です。 *C* 状態と *E* 状態のどちらにも、トラフィックの帯域幅情報が含まれます。

**例**:185.170.185.105:35370 と 10.2.0.4:23 間の TCP 通信のフロー タプル:

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,,," "1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072"

継続の *C* と終了の *E* の各フロー状態では、バイト数とパケット数は、前のフロー タプル レコードの時点から集計した数です。 前の例の通信では、転送されたパケットの総数は 1021+52+8005+47 = 9125 です。 転送された合計バイト数は 588096+29952+4610880+27072 = 5256000 です。

以下のテキストはフロー ログの例です。 ご覧の通り、前のセクションで説明されているプロパティの一覧に従って、複数のレコードが存在します。

## <a name="nsg-flow-logging-considerations"></a>NSG フロー ログ記録の考慮事項

**リソースに接続されているすべての NSG 上で NSG フロー ログ記録を有効にする**:Azure のフロー ログ記録は NSG リソースに対して構成されています。 1 つのフローは 1 つの NSG ルールにのみ関連付けられます。 複数の NSG が利用されるシナリオでは、リソースのサブネットまたはネットワーク インターフェイスが適用されたすべての NSG で NSG フロー ログ記録を有効にして、すべてのトラフィックを確実に記録することをお勧めします。 ネットワーク セキュリティ グループの詳細については、[トラフィックの評価方法](../virtual-network/security-overview.md#how-traffic-is-evaluated)に関するページを参照してください。 

**フロー ログ記録のコスト**:NSG フロー ログ記録は、生成されたログの量に対して課金されます。 トラフィック量が多いと、フロー ログの量が大きくなり、それに関連してコストがかかる可能性があります。 NSG フロー ログの価格には、基礎となるストレージのコストは含まれていません。 NSG フロー ログ記録と共にアイテム保持ポリシー機能を使用すると、大量のストレージ操作とそれに関連するコストが発生する可能性があります。 アイテム保持ポリシー機能が不要な場合は、この値を 0 に設定することをお勧めします。 詳細については、「[Network Watcher の価格](https://azure.microsoft.com/pricing/details/network-watcher/)」と「[Azure Storage の価格](https://azure.microsoft.com/pricing/details/storage/)」を参照してください。

> [!IMPORTANT]
> 現在、Network Watcher の[ネットワーク セキュリティ グループ (NSG) フロー ログ](network-watcher-nsg-flow-logging-overview.md)がアイテム保持ポリシー設定に基づいて BLOB ストレージから自動的に削除されないという問題があります。 0 以外のアイテム保持ポリシーが存在する場合は、保持期間を過ぎているストレージ BLOB を定期的に削除して、課金が発生しないようにすることをお勧めします。 NSG フロー ログ ストレージ BLOB を削除する方法の詳細については、[NSG フロー ログ ストレージ BLOB の削除](network-watcher-delete-nsg-flow-log-blobs.md)に関する記事を参照してください。

**インターネット IP からパブリック IP のない VM へのインバウンド フローのログ記録**:インスタンスレベル パブリック IP として NIC に関連付けられているパブリック IP アドレス経由で割り当てられたパブリック IP アドレスがない VM、または基本的なロード バランサー バックエンド プールの一部である VM では、[既定の SNAT](../load-balancer/load-balancer-outbound-connections.md#defaultsnat) が使用され、アウトバウンド接続を容易にするために Azure によって割り当てられた IP アドレスがあります。 その結果、フローが SNAT に割り当てられたポート範囲内のポートに向かう場合、インターネット IP アドレスからのフローのフロー ログ エントリが表示されることがあります。 Azure では VM へのこれらのフローは許可されませんが、試行はログに記録され、設計上、Network Watcher の NSG フロー ログに表示されます。 不要なインバウンド インターネット トラフィックは、NSG で明示的にブロックすることをお勧めします。

## <a name="sample-log-records"></a>サンプル ログ レコード

以下のテキストはフロー ログの例です。 ご覧の通り、前のセクションで説明されているプロパティの一覧に従って、複数のレコードが存在します。


> [!NOTE]
> **FlowTuples* プロパティの値は、コンマで区切られたリストです。
 
### <a name="version-1-nsg-flow-log-format-sample"></a>バージョン 1 NSG フロー ログ形式のサンプル
```json
{
    "records": [
        {
            "time": "2017-02-16T22:00:32.8950000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A",
                                    "1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A",
                                    "1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A",
                                    "1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2017-02-16T22:01:32.8960000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A",
                                    "1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A",
                                    "1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
    "records":
    [
        
        {
             "time": "2017-02-16T22:00:32.8950000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A","1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A","1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A","1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:01:32.8960000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A","1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A","1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:02:32.9040000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282492,175.182.69.29,10.1.0.4,28918,5358,T,I,D","1487282505,71.6.216.55,10.1.0.4,8080,8080,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282512,91.224.160.154,10.1.0.4,59046,3389,T,I,A"]}]}]}
        }
        ,
        ...
```
### <a name="version-2-nsg-flow-log-format-sample"></a>バージョン 2 NSG フロー ログ形式のサンプル
```json
 {
    "records": [
        {
            "time": "2018-11-13T12:00:35.3899262Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110402,94.102.49.190,10.5.16.4,28746,443,U,I,D,B,,,,",
                                    "1542110424,176.119.4.10,10.5.16.4,56509,59336,T,I,D,B,,,,",
                                    "1542110432,167.99.86.8,10.5.16.4,48495,8088,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "DefaultRule_AllowInternetOutBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110377,10.5.16.4,13.67.143.118,59831,443,T,O,A,B,,,,",
                                    "1542110379,10.5.16.4,13.67.143.117,59932,443,T,O,A,E,1,66,1,66",
                                    "1542110379,10.5.16.4,13.67.143.115,44931,443,T,O,A,C,30,16978,24,14008",
                                    "1542110406,10.5.16.4,40.71.12.225,59929,443,T,O,A,E,15,8489,12,7054"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2018-11-13T12:01:35.3918317Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110437,125.64.94.197,10.5.16.4,59752,18264,T,I,D,B,,,,",
                                    "1542110475,80.211.72.221,10.5.16.4,37433,8088,T,I,D,B,,,,",
                                    "1542110487,46.101.199.124,10.5.16.4,60577,8088,T,I,D,B,,,,",
                                    "1542110490,176.119.4.30,10.5.16.4,57067,52801,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        ...
```

## <a name="next-steps"></a>次の手順

- フロー ログを有効にする方法については、[フロー ログ記録の有効化](network-watcher-nsg-flow-logging-portal.md)に関するページをご覧ください。
- フロー ログを読み取る方法については、「[NSG フロー ログの読み取り](network-watcher-read-nsg-flow-logs.md)」をご覧ください。
- NSG のログ記録の詳細については、[ネットワーク セキュリティ グループ (NSG) のための Azure Monitor ログ](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)に関するページを参照してください。
- VM に対するトラフィックを許可するのか拒否するのかを判断するには、[VM ネットワーク トラフィック フィルターの問題のトラブルシューティング](diagnose-vm-network-traffic-filtering-problem.md)に関するページを参照してください。
