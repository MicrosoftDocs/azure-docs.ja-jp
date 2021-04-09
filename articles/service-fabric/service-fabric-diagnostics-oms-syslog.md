---
title: Azure Service Fabric で Linux クラスター イベントを監視する
description: Service Fabric プラットフォーム イベントを Syslog に書き込んで Service Fabric Linux クラスター イベントを監視する方法について説明します。
author: srrengar
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: srrengar
ms.openlocfilehash: 6a98a833baefa9b1c2d1ebd9ff8147206c2106bf
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570192"
---
# <a name="service-fabric-linux-cluster-events-in-syslog"></a>Syslog 内の Service Fabric Linux クラスター イベント

Service Fabric では、クラスター内の重要なアクティビティを通知するための一連のプラットフォーム イベントを公開しています。 公開されているすべてのイベントの一覧については、[こちら](service-fabric-diagnostics-event-generation-operational.md)を参照してください。 これらのイベントは、さまざまな方法で使用できます。 この記事では、このようなイベントを Syslog に書き込むように Service Fabric を構成する方法について説明します。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="introduction"></a>はじめに

6\.4 リリースでは、Service Fabric プラットフォーム イベントを Linux クラスター用の Syslog に送信するための SyslogConsumer が導入されました。 有効にすると、イベントは自動的に Syslog に送信されます。これは、Log Analytics エージェントで収集および送信できます。

各 Syslog イベントには 4 つのコンポーネントがあります
* Facility
* ID
* Message
* 重大度

SyslogConsumer では、Facility `Local0` を使用すると、すべてのプラットフォーム イベントが書き込まれます。 構成を変更することで任意の有効な Facility に更新できます。使用される ID は `ServiceFabric` です。 Message フィールドには JSON でシリアル化されたイベント全体が含まれているため、さまざまなツールからクエリを実行したり使用したりすることができます。 

## <a name="enable-syslogconsumer"></a>SyslogConsumer を有効にする

SyslogConsumer を有効にするには、クラスターのアップグレードを実行する必要があります。 `fabricSettings` セクションは、次のコードで更新する必要があります。 このコードには SyslogConsumer に関連するセクションのみが含まれていることに注意してください

```json
    "fabricSettings": [
        {
            "name": "Diagnostics",
            "parameters": [
            {
                "name": "ConsumerInstances",
                "value": "AzureWinFabCsv, AzureWinFabCrashDump, AzureTableWinFabEtwQueryable, SyslogConsumer"
            }
            ]
        },
        {
            "name": "SyslogConsumer",
            "parameters": [
            {
                "name": "ProducerInstance",
                "value": "WinFabLttProducer"
            },
            {
            "name": "ConsumerType",
            "value": "SyslogConsumer"
            },
            {
                "name": "IsEnabled",
                "value": "true"
            }
            ]
        },
        {
            "name": "Common",
            "parameters": [
            {
                "name": "LinuxStructuredTracesEnabled",
                "value": "true"
            }
            ]
        }
    ],
```

これは、呼び出すための変更です
1. Common セクションには、`LinuxStructuredTracesEnabled` という新しいパラメーターがあります。 **これは、Syslog への送信時に Linux イベントを構造化およびシリアル化するために必要です。**
2. Diagnostics セクションに、新しい ConsumerInstance であるSyslogConsumer が追加されました。 これで、イベントの他のコンシューマーが存在することをプラットフォームに伝えます。 
3. 新しいセクション SyslogConsumer では `IsEnabled` を `true` にする必要があります。 これは Local0 Facility を自動的に使用するように構成されています。 他のパラメーターを追加することで、これを上書きすることができます。

```json
    {
        "name": "New LogFacility",
        "value": "<Valid Syslog Facility>"
    }
```

## <a name="azure-monitor-logs-integration"></a>Azure Monitor ログの統合
Azure Monitor ログなどの監視ツールでこれらの Syslog イベントを読み取ることができます。 Azure Marketplace を使用して Log Analytics ワークスペースを作成するには、こちらの [手順].(../azure-monitor/logs/quick-create-workspace.md) を参照してください。また、このデータを収集してワークスペースに送信するには、Log Analytics エージェントをクラスターに追加する必要があります。 これは、パフォーマンス カウンターの収集に使用されるものと同じエージェントです。 

1. `Advanced Settings` ブレードに移動します

    ![ワークスペースの設定](media/service-fabric-diagnostics-oms-syslog/workspace-settings.png)

2. [`Data`] をクリックします。
3. [`Syslog`] をクリックします。
4. Local0 を追跡する Facility として構成します。fabricSettings で変更した場合は、別の Facility を追加できます。

    ![Configure Syslog](media/service-fabric-diagnostics-oms-syslog/syslog-configure.png)
5. ワークスペース リソースのメニューの `Logs` をクリックしてクエリ エクスプローラーに進み、クエリを開始します。

    ![ワークスペースのログ](media/service-fabric-diagnostics-oms-syslog/workspace-logs.png)
6. ProcessName としての `ServiceFabric` を探して、`Syslog` テーブルに対してクエリを実行できます。 次のクエリは、イベント内の JSON を解析してその内容を表示する方法の例です。

```kusto
    Syslog | where ProcessName == "ServiceFabric" | extend $payload = parse_json(SyslogMessage) | project $payload
```

![Syslog クエリ](media/service-fabric-diagnostics-oms-syslog/syslog-query.png)

上の例は、NodeDown イベントの例です。 すべてのイベント一覧については、[こちら](service-fabric-diagnostics-event-generation-operational.md)を参照してください。

## <a name="next-steps"></a>次のステップ
* お使いのノードに [Log Analytics エージェントをデプロイ](service-fabric-diagnostics-oms-agent.md)してパフォーマンス カウンターを収集し、Docker の統計とコンテナーのログを収集する
* Azure Monitor ログの一部として提供されている[ログ検索とクエリ](../azure-monitor/logs/log-query-overview.md)機能の詳細を確認します
* [Azure Monitor ログのビュー デザイナーを使用してカスタム ビューを作成する](../azure-monitor/visualize/view-designer.md)
* [Azure Monitor ログを Syslog と統合する](../azure-monitor/agents/data-sources-syslog.md)方法を参照する。
