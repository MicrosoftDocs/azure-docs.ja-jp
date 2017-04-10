---
title: "Azure Network Watcher を使用したネットワーク セキュリティ グループに関するフローのログ記録の概要 - Azure CLI | Microsoft Docs"
description: "このページでは Azure Network Watcher の機能である NSG のフロー ログの使用方法について説明します"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 47d91341-16f1-45ac-85a5-e5a640f5d59e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: c24c2c4e297497e106691d62be76ad0591bb33de
ms.lasthandoff: 03/04/2017


---

# <a name="introduction-to-flow-logging-for-network-security-groups"></a>ネットワーク セキュリティ グループのフローのログ記録の概要

ネットワーク セキュリティ グループ フロー ログは、ネットワーク セキュリティ グループを介して IP トラフィックの送信と受信に関する情報を表示できるようにする Network Watcher の機能です。 これらのフローのログは json 形式で記述され、ルールごとの送信、受信フロー、フロー (送信元/送信先 IP、送信元/送信先ポート、プロトコル) についての 5 組の情報が適用される NIC、トラフィックが許可されているか、拒否されているかを示しています。

![フロー ログの概要][1]

フロー ログはネットワーク セキュリティ グループを対象としていますが、その他のログと同じようには表示されません。 フロー ログは、次の例で示すようにストレージ アカウント内と下記のログ記録パスにのみ格納されます。

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId%3D/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.network/networksecuritygroups/{nsgName}/{year}/{month}/{day}/PT1H.json
```

その他のログで見られる同様のリテンション期間ポリシーが、フロー ログに適用されます。 ログにはリテンション期間ポリシーがあり、1 日 から 365 日まで設定できます。 リテンション期間ポリシーが設定されていない場合、ログは無期限に保持されます。

## <a name="log-file"></a>ログ ファイル

フロー ログには複数のプロパティがあります。 次の一覧は、NSG フロー ログ内で返されるプロパティの一覧です。

* **time** - イベントがトリガーされた時間
* **systemId** - ネットワーク セキュリティ グループのリソース ID。
* **category** - イベントのカテゴリ名で、常に NetworkSecurityGroupFlowEvent
* **resourceid** - NSG のリソース ID
* **operationName** -常に NetworkSecurityGroupFlowEvents
* **properties** - フローのプロパティのコレクション
    * **Version** - フロー ログ イベントのスキーマのバージョン番号
    * **flows** - フローのコレクション。 このプロパティには異なるルールに対して複数のエントリがあります
        * **rule** - フローが一覧表示されているルール
            * **flows** - フローのコレクション
                * **mac** - フローが収集された VM に関する NIC の MAC アドレス
                * **flowTuples** - コンマで区切る形式で表現されたフローの組に対して複数のプロパティを含む文字列
                    * **Time Stamp** - UNIX EPOCH 形式でフローが発生した際のタイム スタンプ
                    * **Source IP** - 発信元 IP
                    * **Destination IP** - 宛先 IP
                    * **Source Port** - 発信ポート
                    * **Destination Port** - 宛先ポート
                    * **Protocol**フローのプロトコル。 有効な値は TCP の **T** と UDP の **U** です
                    * **Traffic Flow** - トラフィック フローの方向。 有効な値は受信の **I** と送信の **O** です。
                    * **Traffic** - トラフィックが許可または拒否されたかどうか。 有効な値は許可の **A** と拒否の **D** です。


以下はフロー ログの例です。 ご覧の通り、前のセクションで説明されているプロパティの一覧に従って、複数のレコードが存在します。 

> [!NOTE]
> FlowTuples プロパティの値は、コンマで区切られたリストです。
 
```json
{
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

## <a name="next-steps"></a>次のステップ

フロー ログを有効にする方法については、[フロー ログ記録の有効化](network-watcher-nsg-flow-logging-portal.md)に関するページをご覧ください。

NSG のログ記録については、「[ネットワーク セキュリティ グループ (NSGs) のログ分析](../virtual-network/virtual-network-nsg-manage-log.md)」をご覧ください。

VM におけるトラフィックの許可または拒否を確認するには、[IP フローの確認によるトラフィックの検証](network-watcher-check-ip-flow-verify-portal.md)に関するページをご覧ください

<!-- Image references -->
[1]: ./media/network-watcher-nsg-flow-logging-overview/figure1.png


