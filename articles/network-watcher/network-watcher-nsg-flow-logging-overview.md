---
title: NSG のフローのログ記録の概要
titleSuffix: Azure Network Watcher
description: この記事では Azure Network Watcher の機能である NSG のフロー ログの使用方法について説明します。
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.openlocfilehash: bc085163b4f738d022ab9771794ec85293de5ed8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100521681"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>ネットワーク セキュリティ グループのフローのログ記録の概要

## <a name="introduction"></a>はじめに

[ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md#security-rules) (NSG) のフロー ログは、NSG を使用した IP トラフィックに関する情報をログに記録できる Azure Network Watcher の機能です。 フロー データは、アクセスできる場所から Azure Storage アカウントに送信され、選択した視覚化ツール (SIEM またはIDS) にエクスポートされます。

![フロー ログの概要](./media/network-watcher-nsg-flow-logging-overview/homepage.jpg)

## <a name="why-use-flow-logs"></a>フロー ログを使用する理由

妥協のないセキュリティ、コンプライアンス、パフォーマンスを実現するには、独自のネットワークを監視、管理し、把握することが不可欠です。 独自の環境を保護および最適化するうえで最も重要なのは、環境を理解することです。 多くの場合、ネットワークの現在の状態、接続しているユーザーとその接続先、接続元、インターネットに対して開いているポート、想定されたネットワークの動作、不規則なネットワークの動作、トラフィックの急激な増加を把握する必要があります。

フロー ログは、クラウド環境内のすべてのネットワーク アクティビティの信頼できるソースです。 ご自身がリソースの最適化を試みるスタートアップ企業であろうと、侵入の検出を試みる大企業であろうと、フロー ログは最善の方法です。 フロー ログは、ネットワーク フローの最適化、スループットの監視、コンプライアンスの検証、侵入の検出などに使用できます。

## <a name="common-use-cases"></a>一般的なユース ケース

**ネットワーク監視**:不明または不要なトラフィックを特定します。 トラフィック レベルと帯域幅の消費を監視します。 アプリケーションの動作を把握するために、フロー ログを IP およびポートでフィルター処理します。 監視用ダッシュボードを設定するために、選択した分析および視覚化ツールにフロー ログをエクスポートします。

**使用状況の監視と最適化**:ネットワークのトップ トーカーを特定します。 GeoIP データと組み合わせて、リージョン間のトラフィックを識別します。 容量の予測用にトラフィックの増加について把握します。 データを使用して、明らかに制限の厳しいトラフィック規則を削除します。

**コンプライアンス**:フロー データを使用して、ネットワークの分離とエンタープライズ アクセス規則への準拠を検証します

**ネットワーク フォレンジクスとセキュリティ分析**:侵害された IP とネットワーク インターフェイスからのネットワーク フローを分析します。 選択した SIEM または IDS ツールにフロー ログをエクスポートします。

## <a name="how-logging-works"></a>ログ記録のしくみ

**キーのプロパティ**

- フロー ログは[第 4 層](https://en.wikipedia.org/wiki/OSI_model#Layer_4:_Transport_Layer)で動作し、NSG との間で送受信されるすべての IP フローを記録します
- ログは Azure プラットフォームを通じて **1 分間隔** で収集され、顧客のリソースやネットワークのパフォーマンスには一切影響しません。
- ログは JSON 形式で書き込まれ、NSG ルールごとに送信フローと受信フローを表示します。
- 各ログ レコードには、フローが適用されたネットワーク インターフェイス (NIC)、5 タプル情報、トラフィックに関する決定、スループット情報 (バージョン 2 のみ) が含まれています。 詳しくは、以下の _ログ形式_ をご覧ください。
- フロー ログには、作成後最長 1 年間ログを自動的に削除できる保持機能があります。 

> [!NOTE]
> 保持機能は、[汎用 v2 ストレージ アカウント (GPv2)](../storage/common/storage-account-overview.md#types-of-storage-accounts) を使用している場合にのみ利用できます。 

**主要な概念**

- ソフトウェア定義ネットワークは、仮想ネットワーク (VNet) とサブネットを中心に編成されています。 これらの VNet とサブネットのセキュリティは、NSG を使用して管理できます。
- ネットワーク セキュリティ グループ (NSG) には、それが接続されているリソース内でネットワーク トラフィックを許可または拒否する一連の _セキュリティ規則_ が含まれています。 NSG はサブネットや個々の VM に関連付けることができるほか、Resource Manager モデルについては VM にアタッチされた個々のネットワーク インターフェイス (NIC) に関連付けることができます。 詳細については、[ネットワーク セキュリティ グループの概要](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)に関する記事をご覧ください。
- ネットワーク内のすべてのトラフィック フローは、該当する NSG のルールを使用して評価されます。
- これらの評価の結果が NSG フロー ログです。 フロー ログは Azure プラットフォームを通じて収集され、顧客のリソースに変更を加える必要はありません。
- 注:ルールには、終了するものと、終了しないものの 2 つの種類があり、それぞれログ記録の動作が異なります。
- - NSG の拒否ルールは終了するルールです。 トラフィックを拒否する NSG は、これをフロー ログに記録し、この場合の処理は、NSG がトラフィックを拒否した後に停止されます。 
- - NSG の許可ルールは終了しないルールです。つまり、1 つの NSG で許可された場合でも、処理は次の NSG に進みます。 トラフィックを許可する最後の NSG によって、トラフィックがフロー ログに記録されます。
- NSG フロー ログは、アクセスできる場所からストレージ アカウントに書き込まれます。
- TA、Splunk、Grafana、Stealthwatch などのツールを使用して、フロー ログをエクスポート、処理、分析、視覚化することができます。

## <a name="log-format"></a>ログの形式

フローのログには、次のプロパティが含まれています。

* **time** - イベントがログに記録された時間
* **systemId** - ネットワーク セキュリティ グループのシステム ID。
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


**NSG フロー ログ バージョン 2 (バージョン 1 との比較)** 

ログのバージョン 2 ではフロー状態の概念が導入されています。 受信するフロー ログのバージョンを構成することができます。

フローが開始されると、フロー状態 _B_ が記録されます。 フロー状態 _C_ およびフロー状態 _E_ は、それぞれフローの継続とフローの終了を示す状態です。 _C_ 状態と _E_ 状態のどちらにも、トラフィックの帯域幅情報が含まれます。

### <a name="sample-log-records"></a>サンプル ログ レコード

以下のテキストはフロー ログの例です。 ご覧の通り、前のセクションで説明されているプロパティの一覧に従って、複数のレコードが存在します。

> [!NOTE]
> *flowTuples* プロパティの値は、コンマで区切られたリストです。
 
**バージョン 1 NSG フロー ログ形式のサンプル**
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
        
        
```
**バージョン 2 NSG フロー ログ形式のサンプル**
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
        }
        
```
**ログ タプルの説明**

![フロー ログ タプル](./media/network-watcher-nsg-flow-logging-overview/tuple.png)

**帯域幅の計算のサンプル**

185.170.185.105:35370 と 10.2.0.4:23 間の TCP 通信のフロー タプル:

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,,," "1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072"

継続の _C_ と終了の _E_ の各フロー状態では、バイト数とパケット数は、前のフロー タプル レコードの時点から集計した数です。 前の例の通信では、転送されたパケットの総数は 1021+52+8005+47 = 9125 です。 転送された合計バイト数は 588096+29952+4610880+27072 = 5256000 です。


## <a name="enabling-nsg-flow-logs"></a>NSG フロー ログの有効化

フロー ログを有効にするためのガイドについては、以下の関連リンクをご使用ください。

- [Azure Portal](./network-watcher-nsg-flow-logging-portal.md)
- [PowerShell](./network-watcher-nsg-flow-logging-powershell.md)
- [CLI](./network-watcher-nsg-flow-logging-cli.md)
- [REST](./network-watcher-nsg-flow-logging-rest.md)
- [Azure Resource Manager](./network-watcher-nsg-flow-logging-azure-resource-manager.md)

## <a name="updating-parameters"></a>パラメーターの更新

**Azure Portal**

Azure portal で、Network Watcher の [NSG フロー ログ] セクションに移動します。 次に、NSG の名前をクリックします。 フロー ログの設定ウィンドウが表示されます。 必要なパラメーターを変更し、 **[保存]** をクリックして変更内容をデプロイ ます。

**PS/CLI/REST/ARM**

コマンドライン ツールでパラメーターを更新するには、フロー ログの有効化に使用されたものと同じコマンド (上記) を使用しますが、変更する更新済みのパラメーターも指定します。

## <a name="working-with-flow-logs"></a>フロー ログの操作

*フロー ログの読み取りとエクスポート*

- [ポータルからフロー ログをダウンロードして表示する](./network-watcher-nsg-flow-logging-portal.md#download-flow-log)
- [PowerShell 関数を使用してフロー ログを読み取る](./network-watcher-read-nsg-flow-logs.md)
- [NSG フロー ログを Splunk にエクスポートする](https://www.splunk.com/en_us/blog/tips-and-tricks/splunking-microsoft-azure-network-watcher-data.html)

フロー ログは NSG を対象としていますが、その他のログと同じようには表示されません。 フロー ログは、次の例で示すようにストレージ アカウント内と下記のログ記録パスにのみ格納されます。

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

*フロー ログの視覚化*

- [Azure Traffic analytics](./traffic-analytics.md) は、フロー ログを処理し、分析情報を抽出し、フロー ログを視覚化する Azure ネイティブ サービスです。 
- [[チュートリアル] Power BI を使用して NSG フロー ログを 視覚化する](./network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [[チュートリアル] Elastic Stack を使用して NSG フロー ログを視覚化する](./network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [[チュートリアル] Grafana を使用して NSG フロー ログを管理および分析する](./network-watcher-nsg-grafana.md)
- [[チュートリアル] Graylog を使用して NSG フロー ログを管理および分析する](./network-watcher-analyze-nsg-flow-logs-graylog.md)


## <a name="nsg-flow-logging-considerations"></a>NSG フロー ログ記録の考慮事項

**ストレージ アカウントに関する考慮事項**: 

- 場所:使用するストレージ アカウントは、NSG と同じリージョンに存在する必要があります。
- パフォーマンス レベル:現在、Standard レベルのストレージ アカウントしかサポートされていません。
- キー ローテーションの自己管理: アクセス キーをストレージ アカウントに変更/ローテーションすると、NSG フローログの動作が停止します。 この問題を修正するには、NSG フロー ログを無効にしてから再度有効にする必要があります。

**フロー ログ記録のコスト**:NSG フロー ログ記録は、生成されたログの量に対して課金されます。 トラフィック量が多いと、フロー ログの量が大きくなり、それに関連してコストがかかる可能性があります。 NSG フロー ログの価格には、基礎となるストレージのコストは含まれていません。 保持ポリシー機能と NSG フロー ログ記録を併用すると、長期間にわたって個別のストレージ コストが発生します。 アイテム保持ポリシーを適用せず、データを永続的に保持する場合は、リテンション期間 (日) を 0 に設定してください。 詳細については、「[Network Watcher の価格](https://azure.microsoft.com/pricing/details/network-watcher/)」 と 「[Azure Storage の価格](https://azure.microsoft.com/pricing/details/storage/)」 を参照してください。

**ユーザー定義の受信 TCP ルールの問題**:[ネットワークセキュリティグループ (NSGs)](../virtual-network/network-security-groups-overview.md) は [ ステートフルファイアウォール](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true) として実装されます。 ただし、現行のプラットフォームの制限により、受信 TCP フローを制御するユーザー定義ルールはステートレスな方法で実装されます。 これに起因し、ユーザー定義受信ルールに制御されるフローは終了しなくなります。 また、これらのフローのバイト数やパケット数は記録されません。 結果として、NSG フローログ (および Traffic Analytics) で報告されるバイト数とパケット数は、実際の数とは異なる可能性があります。 これらの問題を修正するオプトイン フラグは遅くとも 2021 年 3 月までに利用できるようになります。 その間、お客様がこの動作に起因する深刻な問題に直面した場合は、サポート経由でオプトインをリクエストできます。[Network Watcher] の [NSG フロー ログ] でサポート リクエストを提出してください。  

**インターネット IP からパブリック IP のない VM へのインバウンド フローのログ記録**:インスタンスレベル パブリック IP として NIC に関連付けられているパブリック IP アドレス経由で割り当てられたパブリック IP アドレスがない VM、または基本的なロード バランサー バックエンド プールの一部である VM では、[既定の SNAT](../load-balancer/load-balancer-outbound-connections.md) が使用され、アウトバウンド接続を容易にするために Azure によって割り当てられた IP アドレスがあります。 その結果、フローが SNAT に割り当てられたポート範囲内のポートに向かう場合、インターネット IP アドレスからのフローのフロー ログ エントリが表示されることがあります。 Azure では VM へのこれらのフローは許可されませんが、試行はログに記録され、設計上、Network Watcher の NSG フロー ログに表示されます。 不要なインバウンド インターネット トラフィックは、NSG で明示的にブロックすることをお勧めします。

**Application Gateway V2 サブネット NSG の問題**: Application Gateway V2 サブネット NSG のフロー ログは、現時点では [サポートされていません](../application-gateway/application-gateway-faq.yml#are-nsg-flow-logs-supported-on-nsgs-associated-to-application-gateway-v2-subnet)。 この問題は Application Gateway V1 には影響しません。

**互換性のないサービス**:プラットフォームの現行の制約に起因し、一部の Azure サービスは NSG フロー ログでサポートされていません。 互換性のないサービスには現在、次が含まれます。
- [Azure Kubernetes Services (AKS)](https://azure.microsoft.com/services/kubernetes-service/)
- [Logic Apps](https://azure.microsoft.com/services/logic-apps/) 

## <a name="best-practices"></a>ベスト プラクティス

**重要な VNet/サブネットで有効にする**:フロー ログは、監査機能とセキュリティのベスト プラクティスとして、サブスクリプション内のすべての重要な VNet/サブネットで有効にする必要があります。 

**リソースに接続されているすべての NSG 上で NSG フロー ログ記録を有効にする**:Azure のフロー ログ記録は NSG リソースに対して構成されています。 1 つのフローは 1 つの NSG ルールにのみ関連付けられます。 複数の NSG が利用されるシナリオでは、リソースのサブネットまたはネットワーク インターフェイスで適用されるすべての NSG で NSG フロー ログを有効にして、すべてのトラフィックを確実に記録することをお勧めします。 詳細については、「ネットワーク セキュリティ グループ」の「[トラフィックの評価方法](../virtual-network/network-security-group-how-it-works.md)」 をご覧ください。 

一般的なシナリオは次のとおりです。
1. **VM での複数の NIC**:複数の NIC が仮想マシンに接続されている場合は、そのすべてでフロー ログを有効にする必要があります
1. **NIC とサブネット レベルの両方で NSG**: NIC およびサブネット レベルで NSG が構成されている場合は、両方の NSG でフロー ログを有効にする必要があります。 

**ストレージのプロビジョニング**:ストレージは、想定されるフロー ログ ボリュームでチューニングする必要があります。

**名前付け**: NSG 名は最大 80 文字で、NSG 規則名は最大 65 文字である必要があります。 名前が文字数の上限を超えると、ログ記録中に切り捨てられることがあります。

## <a name="troubleshooting-common-issues"></a>一般的な問題のトラブルシューティング

**NSG フロー ログを有効にできない**

- **Microsoft.Insights** リソース プロバイダーが登録されていません

_AuthorizationFailed_ または _GatewayAuthenticationFailed_ エラーを受け取った場合は、サブスクリプションで Microsoft Insights リソース プロバイダーを有効にしていない可能性があります。 [こちらの指示に従って](./network-watcher-nsg-flow-logging-portal.md#register-insights-provider)、Microsoft Insights プロバイダーを有効にしてください。

**NSG フロー ログを有効にしたが、ストレージ アカウントにデータが表示されない**

- **セットアップ時間**

NSG フロー ログがストレージ アカウントに表示されるまでに、最大 5 分かかる場合があります (正しく構成されている場合)。 PT1H.json が表示されます。このファイルには、[こちらの説明に従って](./network-watcher-nsg-flow-logging-portal.md#download-flow-log)アクセスできます。

- **NSG にトラフィックがない**

お使いの VM がアクティブでないか、アプリ ゲートウェイまたは他のデバイスに NSG へのトラフィックをブロックしているアップストリーム フィルターがあるために、ログが表示されない場合があります。

**NSG フロー ログを自動化したい**

現在、ARM テンプレートを使用した 自動化のサポートは、NSG フロー ログでは使用できません。 詳細については、[機能のお知らせ](https://azure.microsoft.com/updates/arm-template-support-for-nsg-flow-logs/)を参照してください。

## <a name="faq"></a>よく寄せられる質問

**NSG フロー ログでは何ができますか?**

Azure ネットワーク リソースは、[ネットワーク セキュリティ グループ (NSG)](../virtual-network/network-security-groups-overview.md) を使用して結合および管理できます。 NSG フロー ログを使用すると、NSG を介してすべてのトラフィックに関する 5 組のフロー情報をログに記録できます。 未処理のフロー ログは Azure ストレージ アカウントに書き込まれ、必要に応じてさらに処理、分析、クエリ実行、またはエクスポートできます。

**フロー ログを使用すると、ネットワークの待機時間やパフォーマンスに影響しますか?**

フロー ログのデータは、ネットワーク トラフィックのパスの外部で収集されるため、ネットワークのスループットや待機時間には影響しません。 ネットワーク パフォーマンスに影響を及ぼす危険性がまったく生じずに、フロー ログを作成または削除できます。

**ファイアウォールの背後にあるストレージ アカウントで NSG フロー ログを使用するにはどうしたらよいですか?**

ファイアウォールの背後にあるストレージ アカウントを使用するには、信頼できる Microsoft サービスからストレージ アカウントにアクセスするための例外を指定する必要があります。

- ポータルのグローバル検索でストレージ アカウントの名前を入力するか、[[ストレージ アカウント] のページ](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)から、ストレージ アカウントに移動します
- **[設定]** セクションで、 **[ファイアウォールと仮想ネットワーク]** を選択します
- **[許可するアクセス元]** で、 **[選択されたネットワーク]** を選択します。 次に、 **[例外]** 下で、[****信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します****] の横にあるボックスにチェックを付けます
- 既に選択されている場合、変更は必要ありません。
- [NSG フロー ログの概要ページ](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs)でターゲットの NSG を見つけて、上記のストレージ アカウントを選択した状態で NSG フロー ログを有効にします。

数分後にストレージ ログを確認できます。TimeStamp が更新されていることや新しい JSON ファイルが作成されていることがわかります。

**サービス エンドポイントの背後にあるストレージ アカウントで NSG フロー ログを使用するにはどうしたらよいですか?**

NSG フロー ログはサービス エンドポイントと互換性があります。追加の構成は不要です。 仮想ネットワークの[サービス エンドポイントを有効にする方法に関するチュートリアル](../virtual-network/tutorial-restrict-network-access-to-resources.md#enable-a-service-endpoint)をご覧ください。

**フロー ログのバージョン 1 と 2 の違いは何ですか?**

フロー ログ バージョン 2 では、"_フロー状態_" という概念が導入されており、転送するバイトとパケットに関する情報が保存されます。 [詳細については、こちらを参照してください。](#log-format)

## <a name="pricing"></a>価格

NSG フロー ログは、収集されるログ (GB 単位) ごとに課金され、1 つのサブスクリプションにつき 1 か月あたり 5 GB までは無料になります。 お客様のリージョンでの現在の価格については、[Network Watcher の価格に関するページ](https://azure.microsoft.com/pricing/details/network-watcher/)をご覧ください。

ログの保存には別途料金が発生します。関連する価格については、[Azure Storage のブロック BLOB の価格](https://azure.microsoft.com/pricing/details/storage/blobs/)に関するページをご覧ください。