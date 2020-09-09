---
title: Azure Monitor のデータ収集ルール (プレビュー)
description: Azure Monitor のデータ収集ルール (DCR) の概要 (その内容と構造、それらを作成して使用する方法など)。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/19/2020
ms.openlocfilehash: 177b79e0a33f4d43d07da9d0dea26df40e2ef11e
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723862"
---
# <a name="data-collection-rules-in-azure-monitor-preview"></a>Azure Monitor のデータ収集ルール (プレビュー)
データ収集ルール (DCR) では、Azure Monitor に入ってくるデータを定義し、そのデータを送信または格納する場所を指定します。 この記事では、データ収集ルールの概要 (その内容と構造、それらを作成して使用する方法など) について説明します。

## <a name="input-sources"></a>入力ソース
データ収集ルールでは現在、次の入力ソースがサポートされています。

- Azure Monitor エージェントを使用した Azure 仮想マシン。 [Azure Monitor エージェント用のデータ コレクションの構成 (プレビュー)](data-collection-rule-azure-monitor-agent.md) に関する記事をご覧ください。



## <a name="components-of-a-data-collection-rule"></a>データ収集ルールのコンポーネント
データ収集ルールに含まれるコンポーネントを次に示します。

| コンポーネント | 説明 |
|:---|:---|
| データ ソース | 独自の形式と方法でデータを公開する、監視データの一意のソース。 データ ソースの例としては、Windows イベント ログ、パフォーマンス カウンター、syslog などがあります。 以下で説明するように、各データ ソースは特定のデータ ソースの種類に適合します。 |
| ストリーム | 1 つの種類として変換およびスキーマ化される 1 組のデータ ソースを記述する一意のハンドル。 各データ ソースにはストリームが 1 つ以上必要であり、1 つのストリームが複数のデータ ソースで使用される場合もあります。 1 つのストリーム内のすべてのデータ ソースで、1 つの共通スキーマが共有されます。 同じ Log Analytics ワークスペース内の複数のテーブルに特定のデータ ソースを送信する場合などは、複数のストリームを使用します。 |
| 変換先 | データの送信先となる 1 組の宛先。 例として、Log Analytics ワークスペース、Azure Monitor メトリック、Azure Event Hubs があります。 | 
| データ フロー | どのストリームをどの宛先に送信するかの定義。 | 

次の図は、データ収集ルールのコンポーネントとその関係を示しています

[![DCR の図](media/data-collection-rule/data-collection-rule-components.png)](media/data-collection-rule/data-collection-rule-components.png#lightbox)

### <a name="data-source-types"></a>データ ソースの種類
各データ ソースには、データソースの種類があります。 各種類は、データ ソースごとに指定する必要がある一意のプロパティ セットを定義します。 次の表に、現在使用できるデータ ソースの種類を示します。

| [データ ソースの種類] | Description | 
|:---|:---|
| 拡張機能 | VM 拡張機能に基づいたデータ ソース |
| performanceCounters | Windows と Linux の両方のパフォーマンス カウンター |
| syslog | Linux の Syslog イベント |
| windowsEventLogs | Windows イベント ログ |


## <a name="limits"></a>制限
次の表に、各データ収集ルールに現在適用されている制限を示します。

| 制限 | 値 |
|:---|:---|
| データ ソースの最大数 | 10 |
| パフォーマンス カウンターのカウンター指定子の最大数 | 100 |
| SysLog 内のファシリティ名の最大数 | 20 |
| イベントログ内の XPath クエリの最大数 | 100 |
| データ フローの最大数 | 10 |
| データ ストリームの最大数 | 10 |
| 拡張機能の最大数 | 10 |
| 拡張機能設定の最大サイズ | 32 Kb |
| Log Analytics ワークスペースの最大数 | 10 |


## <a name="create-a-dcr"></a>DCR を作成する
現在、DCR を作成する方法は 2 つあります。

- [Azure portal を使用](data-collection-rule-azure-monitor-agent.md)してデータ収集ルールを作成し、1 つまたは複数の仮想マシンに関連付けます。
- データ収集ルールを JSON で直接編集し、[REST API を使用して送信します](https://docs.microsoft.com/rest/api/monitor/datacollectionrules)。

## <a name="sample-data-collection-rule"></a>データ収集ルールのサンプル
以下のデータ収集ルールのサンプルは Azure 管理エージェントを使用した仮想マシン用で、次の詳細が含まれています。

- パフォーマンス データ
  - 特定のプロセッサ、メモリ、論理ディスク、および物理ディスクのカウンターを 15 秒ごとに収集し、1 分ごとにアップロードします。
  - 特定のプロセス カウンターを 30 秒ごとに収集し、5 分ごとにアップロードします。
- Windows イベント
  - Windows セキュリティ イベントを収集し、1 分ごとにアップロードします。
  - Windows のアプリケーション イベントとシステム イベントを収集し、5 分ごとにアップロードします。
- syslog
  - cron ファシリティからデバッグ、重大、および緊急イベントを収集します。
  - syslog ファシリティからアラート、重大、および緊急イベントを収集します。
- 変換先
  - すべてのデータを centralWorkspace という名前の Log Analytics ワークスペースに送信します。

```json
{
    "location": "eastus",
    "properties": {
      "dataSources": {
        "performanceCounters": [
          {
            "name": "cloudTeamCoreCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT1M",
            "samplingFrequencyInSeconds": 15,
            "counterSpecifiers": [
              "\\Processor(_Total)\\% Processor Time",
              "\\Memory\\Committed Bytes",
              "\\LogicalDisk(_Total)\\Free Megabytes",
              "\\PhysicalDisk(_Total)\\Avg. Disk Queue Length"
            ]
          },
          {
            "name": "appTeamExtraCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT5M",
            "samplingFrequencyInSeconds": 30,
            "counterSpecifiers": [
              "\\Process(_Total)\\Thread Count"
            ]
          }
        ],
        "windowsEventLogs": [
          {
            "name": "cloudSecurityTeamEvents",
            "streams": [
              "Microsoft-WindowsEvent"
            ],
            "scheduledTransferPeriod": "PT1M",
            "xPathQueries": [
              "Security!*"
            ]
          },
          {
            "name": "appTeam1AppEvents",
            "streams": [
              "Microsoft-WindowsEvent"
            ],
            "scheduledTransferPeriod": "PT5M",
            "xPathQueries": [
              "System!*[System[(Level = 1 or Level = 2 or Level = 3)]]",
              "Application!*[System[(Level = 1 or Level = 2 or Level = 3)]]"
            ]
          }
        ],
        "syslog": [
          {
            "name": "cronSyslog",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "cron"
            ],
            "logLevels": [
              "Debug",
              "Critical",
              "Emergency"
            ]
          },
          {
            "name": "syslogBase",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "syslog"
            ],
            "logLevels": [
              "Alert",
              "Critical",
              "Emergency"
            ]
          }
        ]
      },
      "destinations": {
        "logAnalytics": [
          {
            "workspaceResourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.OperationalInsights/workspaces/my-workspace",
            "name": "centralWorkspace"
          }
        ]
      },
      "dataFlows": [
        {
          "streams": [
            "Microsoft-Perf",
            "Microsoft-Syslog",
            "Microsoft-WindowsEvent"
          ],
          "destinations": [
            "centralWorkspace"
          ]
        }
      ]
    }
  }
```


## <a name="next-steps"></a>次の手順

- [データ収集ルールを作成](data-collection-rule-azure-monitor-agent.md)し、Azure Monitor エージェントを使用する仮想マシンからそのデータ収集ルールとの関連付けを作成します。
