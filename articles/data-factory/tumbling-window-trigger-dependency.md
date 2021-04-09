---
title: タンブリング ウィンドウ トリガーの依存関係の作成
description: Azure Data Factory でタンブリング ウィンドウ トリガーの依存関係を作成する方法について説明します。
ms.author: chez
author: chez-charlie
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/03/2020
ms.openlocfilehash: f969c06a3419a8017cfc5ebc0de19caa67c8dc68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361470"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>タンブリング ウィンドウ トリガーの依存関係の作成
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、タンブリング ウィンドウ トリガーの依存関係を作成する手順について説明します。 タンブリング ウィンドウ トリガーに関する全般な情報については、[タンブリング ウィンドウ トリガーを作成する方法](how-to-create-tumbling-window-trigger.md)に関するページを参照してください。

依存関係チェーンを構築し、データ ファクトリ内の別のトリガーの実行が成功した後でのみトリガーが実行されるようにするには、この高度な機能を使用して、タンブリング ウィンドウの依存関係を作成します。

タンブリング ウィンドウ トリガーを使用して Azure Data Factory 内に依存パイプラインを作成する方法のデモについては、次のビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Create-dependent-pipelines-in-your-Azure-Data-Factory/player]

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Data Factory UI で依存関係を作成する

トリガーの依存関係を作成するには、 **[トリガー]、[詳細設定]、[新規]** の順に選択した後、適切なオフセットとサイズに依存するトリガーを選択します。 **[完了]** を選択して、依存関係に対するデータ ファクトリの変更を発行して有効にします。

![依存関係の作成](media/tumbling-window-trigger-dependency/tumbling-window-dependency-01.png "依存関係の作成")

## <a name="tumbling-window-dependency-properties"></a>タンブリング ウィンドウの依存関係プロパティ

依存関係を持つタンブリング ウィンドウ トリガーには、次のプロパティがあります。

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": <<Started/Stopped/Disabled - readonly>>,
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": <<datetime>>,
            "endTime": <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        }
    }
}
```

次の表に、タンブリング ウィンドウの依存関係を定義するために必要な属性の一覧を示します。

| **プロパティ名** | **説明**  | **Type** | **必須** |
|---|---|---|---|
| type  | 既存のすべてのタンブリング ウィンドウ トリガーがこのドロップダウンに表示されます。 依存関係を設定するトリガーを選択します。  | TumblingWindowTriggerDependencyReference または SelfDependencyTumblingWindowTriggerReference | はい |
| offset | 依存関係トリガーのオフセット。 時間範囲形式で値を指定します。正と負の両方のオフセットを指定できます。 このプロパティは、トリガーが自己に依存する場合は必須であり、それ以外の場合は省略可能です。 自己依存関係は、常に負のオフセットにする必要があります。 値が指定されていない場合、ウィンドウはトリガーそのものと同じになります。 | Timespan<br/>(hh:mm:ss) | 自己依存関係:はい<br/>その他:いいえ |
| size | 依存関係のタンブリング ウィンドウのサイズ。 正の timespan 値を指定します。 このプロパティは省略可能です。 | Timespan<br/>(hh:mm:ss) | いいえ  |

> [!NOTE]
> タンブリング ウィンドウ トリガーは、最大 5 つの他のトリガーに依存することができます。

## <a name="tumbling-window-self-dependency-properties"></a>タンブリング ウィンドウの自己依存関係プロパティ

前のウィンドウが正常に完了するまではトリガーが次のウィンドウに進んではならないシナリオでは、自己依存関係を構築します。 前の時間内にそれ自体の以前の実行が成功したことに依存する自己依存トリガーには、次のコードで示されているプロパティがあります。

> [!NOTE]
> トリガーされるパイプラインが、前にトリガーされたウィンドウでのパイプラインの出力に依存している場合は、タンブリング ウィンドウ トリガーの自己依存関係のみを使用することをお勧めします。 並列トリガーの実行を制限するには、最大トリガー コンカレンシーを設定します。

```json
{
    "name": "DemoSelfDependency",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00Z",
            "delay": "00:01:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": "01:00:00",
                    "offset": "-01:00:00"
                }
            ]
        }
    }
}
```
## <a name="usage-scenarios-and-examples"></a>使用シナリオと例

タンブリング ウィンドウの依存関係プロパティのシナリオと使用法をイラストで次に示します。

### <a name="dependency-offset"></a>依存関係のオフセット

![オフセットの例](media/tumbling-window-trigger-dependency/tumbling-window-dependency-02.png "オフセットの例")

### <a name="dependency-size"></a>依存関係のサイズ

![サイズの例](media/tumbling-window-trigger-dependency/tumbling-window-dependency-03.png "サイズの例")

### <a name="self-dependency"></a>自己依存関係

![自己依存関係](media/tumbling-window-trigger-dependency/tumbling-window-dependency-04.png "自己依存関係")

### <a name="dependency-on-another-tumbling-window-trigger"></a>別のタンブリング ウィンドウ トリガーに対する依存関係

過去 7 日間の出力を集計する別の日次ジョブに依存する日次テレメトリ処理ジョブで、7 日分のローリング ウィンドウ ストリームを生成するとします。

![依存関係の例](media/tumbling-window-trigger-dependency/tumbling-window-dependency-05.png "依存関係の例")

### <a name="dependency-on-itself"></a>自己に対する依存関係

ジョブの出力ストリームにずれがない日次ジョブ:

![自己依存関係の例](media/tumbling-window-trigger-dependency/tumbling-window-dependency-06.png "自己依存関係の例")

## <a name="monitor-dependencies"></a>依存関係を監視する

[トリガーの実行] 監視ページで、依存関係チェーンと該当するウィンドウを監視できます。 **[監視]、[トリガーの実行]** の順に移動します。 タンブリング ウィンドウ トリガーに依存関係がある場合、トリガー名には依存関係の監視ビューへのハイパーリンクが表示されます。  

![トリガーの実行を監視する](media/tumbling-window-trigger-dependency/tumbling-window-dependency-07.png "トリガーの実行を監視する - タンブリング ウィンドウの依存関係ビューのエントリ")

トリガーの依存関係を表示するには、トリガー名をクリックします。 右側のパネルには、RunID、ウィンドウの時間、状態など、トリガーの詳細な実行情報が表示されます。

![依存関係の監視のリスト ビュー](media/tumbling-window-trigger-dependency/tumbling-window-dependency-08.png "モニターの依存関係のリスト ビュー")

依存関係の状態、および各依存トリガーのウィンドウが表示されます。 依存関係トリガーのいずれかが失敗した場合は、正常に再実行して、依存するトリガーが稼働できるようにする必要があります。

タンブリング ウィンドウ トリガーは、依存関係を _7 日間_ 待機してからタイムアウトします。7 日が経過すると、トリガーの実行は失敗します。

トリガーの依存関係のスケジュールを視覚的に表示するには、[ガント] ビューを選択します。

![依存関係の監視のガント チャート](media/tumbling-window-trigger-dependency/tumbling-window-dependency-09.png "依存関係の監視のガント チャート ビュー")

透明なボックスには、ダウン ストリームに依存する各トリガーの依存関係ウィンドウが表示されます。上にある塗られたボックスには、個々のウィンドウの実行が表示されます。 ここでは、ガント チャート ビューを解釈するためのヒントをいくつか紹介します。

* 透明なボックスは、依存ウィンドウが保留中または実行中の状態のときに青で表示されます
* すべてのウィンドウが依存トリガーに対して成功すると、透明なボックスが緑色に変わります
* 一部の依存ウィンドウが失敗した場合、透明なボックスは赤で表示されます。 エラー ウィンドウの実行を特定するには、赤く塗られたボックスを探します

ガント チャート ビューでウィンドウを再実行するには、ウィンドウの塗られているボックスを選択します。そうすると、詳細と再実行オプションが記載された操作パネルが表示されます

## <a name="next-steps"></a>次のステップ

* [タンブリング ウィンドウ トリガーの作成方法](how-to-create-tumbling-window-trigger.md)を確認します
