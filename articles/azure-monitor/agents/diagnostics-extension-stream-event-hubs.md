---
title: Windows Azure Diagnostics 拡張機能から Azure Event Hubs にデータを送信する
description: Azure の外部の場所にデータを転送できるよう、Azure Event Hubs にデータを送信するように、Azure Monitor で Diagnostics 拡張機能を構成します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/18/2020
ms.openlocfilehash: 19c39632a1ed040636372d6bad53bf6859960b94
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101732039"
---
# <a name="send-data-from-windows-azure-diagnostics-extension-to-azure-event-hubs"></a>Windows Azure Diagnostics 拡張機能から Azure Event Hubs にデータを送信する
Azure Diagnostics 拡張機能は Azure Monitor のエージェントで、ゲスト オペレーティング システムと Azure 仮想マシンと他のコンピューティング リソースのワークロードから監視データを収集します。 この記事では、Azure の外部の場所にデータを転送できるように、Windows Azure Diagnostic (WAD) 拡張機能から [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) にデータを送信する方法について説明します。

## <a name="supported-data"></a>サポートされるデータ

以下のデータをゲスト オペレーティング システムから収集して Event Hubs に送信できます。 IIS ログやクラッシュ ダンプなど、WAD によって収集される他のデータ ソースは、Event Hubs に送信できません。

* Windows イベント トレーシング (ETW) イベント
* パフォーマンス カウンター
* Windows イベント ログ (Windows イベント ログのアプリケーション ログを含む)
* Azure Diagnostics インフラストラクチャ ログ

## <a name="prerequisites"></a>前提条件

* Windows Diagnostics 拡張機能 1.6 以降。 サポートされているリソースについては、「[Azure Diagnostics 拡張機能の構成スキーマのバージョンと履歴](diagnostics-extension-versions.md)」と「[Azure Diagnostics 拡張機能の概要](diagnostics-extension-overview.md)」を参照してください。
* Event Hubs 名前空間が、常にプロビジョニングされている必要があります。 「[Event Hubs の使用](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)」を参照してください。


## <a name="configuration-schema"></a>構成スキーマ
Diagnostics 拡張機能を有効にして構成するためのさまざまなオプションについては「[Windows Azure Diagnostics 拡張機能 (WAD) をインストールして構成する](diagnostics-extension-windows-install.md)」を参照し、構成スキーマのリファレンスについては「[Azure Diagnostics の構成スキーマ](diagnostics-extension-schema-windows.md)」を参照してください。 この記事の残りの部分では、この構成を使用してイベント ハブにデータを送信する方法について説明します。 

Azure Diagnostics では常に、ログとメトリックが Azure Storage アカウントに送信されます。 追加の場所にデータを送信する "*データ シンク*" を 1 つ以上構成できます。 各シンクはパブリック構成の [SinksConfig 要素](diagnostics-extension-schema-windows.md#sinksconfig-element)で定義し、機密情報はプライベート構成で定義します。 イベント ハブのこの構成では、次の表の値を使用します。

| プロパティ | 説明 |
|:---|:---|
| 名前 | シンクのわかりやすい名前。 シンクに送信するデータ ソースを指定するために構成で使用します。 |
| url  | \<event-hubs-namespace\>.servicebus.windows.net/\<event-hub-name\> の形式のイベント ハブの URL。          |
| SharedAccessKeyName | イベント ハブに対する少なくとも **送信** 権限を持つ共有アクセス ポリシーの名前。 |
| SharedAccessKey     | イベント ハブに対する共有アクセス ポリシーのプライマリ キーまたはセカンダリ キー。 |

パブリック構成とプライベート構成の例を次に示します。 これは、イベント ハブ データ シンクの構成方法と使用方法を説明するために、1 つのパフォーマンス カウンターとイベント ログを含む最小構成です。 より複雑な例については、「[Azure Diagnostics の構成スキーマ](diagnostics-extension-schema-windows.md)」を参照してください。

### <a name="public-configuration"></a>パブリック構成

```JSON
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 5120,
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "myEventHub",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "myEventHub",
                    "DataSource": [
                    {
                        "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "myEventHub",
                    "EventHub": {
                        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule"
                    }
                }
            ]
        }
    },
    "StorageAccount": "mystorageaccount",
}
```


### <a name="private-configuration"></a>プライベート構成

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "EventHub": {
        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    }
}
```



## <a name="configuration-options"></a>構成オプション
データ シンクにデータを送信するには、データ ソースのノードで **sinks** 属性を指定します。 **sinks** 属性を配置する場所によって、割り当てのスコープが決まります。 次の例では、**sinks** 属性が **PerformanceCounters** ノードに対して定義されているため、すべての子パフォーマンス カウンターがイベント ハブに送信されます。

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "MyEventHub",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        }
    ]
}
```


次の例では、**sinks** 属性が 3 つのカウンターに直接適用されているため、それらのパフォーマンス カウンターだけがイベント ハブに送信されます。 

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Rejected",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Queued",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        }
    ]
}
```

## <a name="validating-configuration"></a>構成を検証する
さまざまな方法を使用して、データがイベント ハブに送信されることを検証できます。 簡単な方法は、「[Azure Event Hubs で Azure Blob Storage または Azure Data Lake Storage にイベントをキャプチャする](../../event-hubs/event-hubs-capture-overview.md)」で説明されている Event Hubs キャプチャを使用することです。 


## <a name="troubleshoot-event-hubs-sinks"></a>Event Hubs シンクのトラブルシューティング

- Azure Diagnostics 自体のログとエラーが含まれる Azure Storage のテーブル **WADDiagnosticInfrastructureLogsTable** を確認します。 1 つの方法は、 [Azure Storage Explorer](https://www.storageexplorer.com) などのツールを利用してこのストレージ アカウントに接続して、このテーブルを表示し、過去 24 時間の TimeStamp のクエリを追加することです。 ツールを使用して .csv ファイルをエクスポートし、Microsoft Excel などのアプリケーションで開くことができます。 Excel を使用すると、 **EventHubs** のようなコーリングカード文字列を簡単に検索して、報告されたエラーを確認できます。  

- イベント ハブが正しくプロビジョニングされていることを確認してください。 構成の **PrivateConfig** セクションのすべての接続情報は、ポータルに表示されるリソースの値に一致している必要があります。 ポータルで SAS ポリシー (この例では *SendRule*) が定義されていること、および "*送信*" アクセス許可が付与されていることを確認します。  

## <a name="next-steps"></a>次のステップ

* [Event Hubs の概要](../../event-hubs/event-hubs-about.md)
* [イベント ハブの作成](../../event-hubs/event-hubs-create.md)
* [Event Hubs の FAQ](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png



