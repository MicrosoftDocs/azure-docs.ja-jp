---
title: Azure Monitor での自動スケール設定について
description: 自動スケール設定とそのしくみについて詳しく説明します。 Virtual Machines、Cloud Services、Web Apps に当てはまります
ms.topic: conceptual
ms.date: 12/18/2017
ms.subservice: autoscale
ms.openlocfilehash: a914f6d71c013acea8dfde0f6578985bc009bb26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100603402"
---
# <a name="understand-autoscale-settings"></a>自動スケール設定について
自動スケール設定を使用すると、アプリケーションの変動する負荷に対応するために適切な量のリソースを確実に実行できます。 負荷やパフォーマンスを示すメトリック、またはスケジュールされた日時にトリガーするメトリックに基づいてトリガーされるように自動スケール設定を構成できます。 この記事では、自動スケール設定の構造について詳しく説明します。 まず、設定のスキーマとプロパティについて取り上げ、構成可能なさまざまなプロファイルの種類についてわかりやすく説明します。 最後に、そのときどきで実行されるプロファイルが、Azure の自動スケール機能によって、どのように評価されるかを説明します。

## <a name="autoscale-setting-schema"></a>自動スケール設定のスキーマ
自動スケール設定のスキーマを説明するために、次の自動スケール設定を使用します。 この自動スケール設定の内容に注意してください。
- 1 つのプロファイル。 
- このプロファイルには 2 つのメトリック ルールがあります。1 つはスケールアウト用、もう 1 つはスケールイン用です。
  - スケールアウト ルールは、仮想マシン スケール セットの平均 CPU 使用率メトリックが過去 10 分間に 85% を超えるとトリガーされます。
  - スケールイン ルールは、仮想マシン スケール セットの平均が過去 1 分間に 60% 未満になるとトリガーされます。

> [!NOTE]
> 1 つの設定には複数のプロファイルを含めることができます。 詳細については、[プロファイル](#autoscale-profiles)に関するセクションを参照してください。 1 つのプロファイルに複数のスケールアウト ルールとスケールイン ルールが定義されている場合もあります。 これらが評価されるしくみについては、[評価](#autoscale-evaluation)に関するセクションを参照してください。

```JSON
{
  "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/setting1",
  "name": "setting1",
  "type": "Microsoft.Insights/autoscaleSettings",
  "location": "East US",
  "properties": {
    "enabled": true,
    "targetResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
    "profiles": [
      {
        "name": "mainProfile",
        "capacity": {
          "minimum": "1",
          "maximum": "4",
          "default": "1"
        },
        "rules": [
          {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "GreaterThan",
              "threshold": 85
            },
            "scaleAction": {
              "direction": "Increase",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          },
          {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "LessThan",
              "threshold": 60
            },
            "scaleAction": {
              "direction": "Decrease",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          }
        ]
      }
    ]
  }
}
```

| Section | 要素名 | 説明 |
| --- | --- | --- |
| 設定 | id | 自動スケール設定のリソース ID。 自動スケール設定は、Azure Resource Manager リソースです。 |
| 設定 | name | 自動スケール設定の名前。 |
| 設定 | location | 自動スケール設定の場所。 この場所は、スケールするリソースの場所とは異なる場合があります。 |
| properties | targetResourceUri | スケールするリソースのリソース ID。 リソースごとに適用できる自動スケール設定は 1 つに限られます。 |
| properties | profiles | 自動スケール設定は、1 つ以上のプロファイルで構成されます。 自動スケール エンジンが実行されるたびに、1 つのプロファイルが実行されます。 |
| profile | name | プロファイルの名前。 プロファイルを識別できる任意の名前を選択できます。 |
| profile | Capacity.maximum | 許容される最大容量。 このプロファイルの実行時に、自動スケールによってリソースがスケールされるときに、この値を上回ることはありません。 |
| profile | Capacity.minimum | 許容される最小容量。 このプロファイルの実行時に、自動スケールによってリソースがスケールされるときに、この値を下回ることはありません。 |
| profile | Capacity.default | リソース メトリック (この例では "vmss1" の CPU) の読み取りで問題が発生し、現在の容量が既定の容量を下回る場合は、自動スケールによって既定値にスケールアウトされます。 これによってリソースの可用性が確保されます。 現在の容量が既定の容量を既に上回る場合、自動スケールではスケールインされません。 |
| profile | rules | 自動スケールでは、プロファイルのルールを使用して、最大容量から最小容量までの間で自動的にスケールします。 1 つのプロファイルで複数のルールをご利用いただけます。 通常は、スケールアウトするタイミングを指定するルールと、スケールインするタイミングを指定するルールの 2 つのルールを使用します。 |
| ルール | metricTrigger | ルールのメトリックの条件を定義します。 |
| metricTrigger | metricName | メトリックの名前。 |
| metricTrigger |  metricResourceUri | メトリックを生成するリソースのリソース ID。 ほとんどの場合、これはスケールするリソースと同じになります。 場合によっては、別の値を指定できます。 たとえば、ストレージ キューのメッセージの数に基づいて仮想マシン スケール セットをスケールできます。 |
| metricTrigger | timeGrain | メトリックのサンプリング期間。 たとえば、**TimeGrain = "PT1M"** の場合、statistic 要素で指定された集計方法を使用して 1 分ごとにメトリックが集計されます。 |
| metricTrigger | statistic | timeGrain の期間内の集計方法。 たとえば、**statistic = "Average"** で **timeGrain = "PT1M"** の場合、1 分ごとにメトリックが平均して集計されます。 このプロパティによって、メトリックのサンプリング方法が決まります。 |
| metricTrigger | timeWindow | メトリックについてさかのぼる時間。 たとえば、**timeWindow = "PT10M"** の場合、自動スケールが実行されるたびに、過去 10 分間のメトリックが照会されます。 時間枠を使用することで、メトリックの正規化が可能になり、一時的なスパイクへの対応が回避されます。 |
| metricTrigger | timeAggregation | サンプリングされたメトリックの集計に使用する集計方法。 たとえば、**TimeAggregation = "Average"** の場合、サンプリングされたメトリックが平均して集計されます。 上記の例では、1 分間のサンプルを 10 個取り、それらを平均します。 |
| ルール | scaleAction | ルールの metricTrigger がトリガーされたときに実行するアクション。 |
| scaleAction | 方向 | スケールアウトの場合は "Increase"、スケールインの場合は "Decrease" を指定します。|
| scaleAction | value | リソースの容量を増減する量。 |
| scaleAction | cooldown | スケール操作の後、再度スケールするまでの待機時間。 たとえば、**cooldown = "PT10M"** の場合、スケール操作の実行後 10 分間は、再度スケールを試みることはありません。 このクールダウンにより、インスタンスの追加または削除後にメトリックを一定に保つことができます。 |

## <a name="autoscale-profiles"></a>自動スケール プロファイル

自動スケール プロファイルには、次の 3 種類があります。

- **標準プロファイル:** 最も一般的なプロファイル。 曜日や日に応じてリソースをスケールする必要がない場合は、標準プロファイルを使用することができます。 このプロファイルは、スケールアウトするタイミングとスケールインするタイミングを指定したメトリック ルールで構成できます。 標準プロファイルは 1 つだけ定義する必要があります。

    この記事で以前に使用したサンプル プロファイルは標準プロファイルの一例です。 リソースの静的インスタンス数に合わせてスケールするようにプロファイルを設定することはできないことに注意してください。

- **指定日プロファイル :** 特殊なケースで使用されるプロファイルです。 たとえば、2017 年 12 月 26 日 (太平洋標準時) に重要なイベントが予定されており、 その日のリソースの最小/最大容量を別の値にする必要があるとします。ただし、引き続き同じメトリックでスケールする必要があります。 この場合、設定のプロファイル リストに指定日プロファイルを追加します。 このプロファイルをイベントの日にのみ実行するように構成します。 それ以外の日は標準プロファイルが自動スケールに使用されます。

    ```json
    "profiles": [
        {
            "name": " regularProfile",
            "capacity": {
                ...
            },
            "rules": [
                {
                ...
                },
                {
                ...
                }
            ]
        },
        {
            "name": "eventProfile",
            "capacity": {
            ...
            },
            "rules": [
                {
                ...
                }, 
                {
                ...
                }
            ],
            "fixedDate": {
                "timeZone": "Pacific Standard Time",
                "start": "2017-12-26T00:00:00",
                "end": "2017-12-26T23:59:00"
            }
        }
    ]
    ```
    
- **定期プロファイル:** この種類のプロファイルを使用すると、このプロファイルを常に特定の曜日に使用することができます。 定期プロファイルには開始時間しかありません。 次の定期プロファイルまたは指定日プロファイルの開始が設定されるまで実行されます。 定期プロファイルが 1 つだけ設定された自動スケール設定では、同じ設定で標準プロファイルが定義されている場合でも、定期プロファイルが実行されます。 次の 2 つの例は、このプロファイルの使用方法を示しています。

    **例 1: 平日と週末**
    
    週末の最大容量を 4 に設定し、 負荷の増加が予想される平日は最大容量を 10 に設定するとします。 この場合、設定に 2 つの定期プロファイルを含め、1 つを週末に実行し、もう 1 つを平日に実行します。
    設定は次のようになります。

    ``` JSON
    "profiles": [
    {
    "name": "weekdayProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }}
    },
    {
    "name": "weekendProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Saturday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```

    上記の設定を見ると、各定期プロファイルにスケジュールが設定されていることがわかります。 このスケジュールによって、プロファイルの実行をいつ開始するかを指定しています。 プロファイルは、別のプロファイルを実行する時間になると停止されます。

    たとえば、上記の設定では、"weekdayProfile" は月曜日の午前 12 時に開始するように設定されています。 つまり、このプロファイルは月曜日の午前 12 時に実行が開始されます。 "weekendProfile" が実行を開始するようにスケジュールされている土曜日の午前 12 時まで実行を続けます。

    **例 2: 営業時間**
    
    午前 9 時から午後 5 時までの営業時間中とそれ以外とについて、別々のしきい値をメトリックに使用するとします。 設定は次のようになります。
    
    ``` JSON
    "profiles": [
    {
    "name": "businessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                9
            ],
            "minutes": [
                0
            ]
        }
    }
    },
    {
    "name": "nonBusinessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                17
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```
    
    上記の設定を見ると、"businessHoursProfile" は月曜日の午前 9 時に実行が開始され、午後 5 時まで実行を続けます。 この時間に "nonBusinessHoursProfile" の実行が開始されます。 "nonBusinessHoursProfile" は火曜日の午前 9 時まで実行され、その後、"businessHoursProfile" が引き継ぎます。 これが金曜日の午後 5 時まで繰り返されます。 その時点から月曜日の午前 9 時までずっと "nonBusinessHoursProfile" が実行されます。
    
> [!Note]
> Azure Portal における自動スケールのユーザー インターフェイスでは、定期プロファイルの終了時間が強制され、次の定期プロファイルまでの間に自動スケール設定の既定のプロファイルが実行されます。
    
## <a name="autoscale-evaluation"></a>自動スケールの評価
自動スケール設定には複数のプロファイルを設定でき、プロファイルごとに複数のメトリック ルールを設定できることから、自動スケール設定の評価のしくみを理解することが重要になります。 自動スケール ジョブが実行されるたびに、適用するプロファイルがまず選択されます。 その後、プロファイルの最小値、最大値、メトリック ルールが評価され、スケール操作が必要かどうかが判断されます。

### <a name="which-profile-will-autoscale-pick"></a>自動スケールで選択されるプロファイル

自動スケールでは、次の順序でプロファイルが選択されます。
1. まず、すぐに実行するように構成された指定日プロファイルを探し、 存在する場合はそのプロファイルを実行します。 実行することになっている指定日プロファイルが複数ある場合は、最初のプロファイルが選択されます。
2. 指定日プロファイルがない場合は、定期プロファイルが確認されます。 定期プロファイルが見つかった場合は、それが実行されます。
3. 指定日プロファイルも定期プロファイルもない場合は、標準プロファイルが実行されます。

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>自動スケールで複数のルールが評価されるしくみ

自動スケールでは、実行することになっているプロファイルを特定した後、そのプロファイルのすべてのスケールアウト ルール (**direction が "Increase"** のルール) が評価されます。

1 つ以上のスケールアウト ルールがトリガーされた場合、それぞれのルールの **scaleAction** で指定された新しい容量が計算されます。 サービスの可用性を確保するために、それらの容量のうち最大容量にスケールアウトされます。

たとえば、現在の容量が 10 の仮想マシン スケール セットがあり、 容量を 10% 増やすルールと、容量を 3 増やすルールの 2 つのスケールアウト ルールがあるとします。 最初のルールでは新しい容量は 11 になり、2 番目のルールでは容量が 13 になります。 サービスの可用性を確保するために、自動スケールでは最大容量になるアクションが選択されるので、2 番目のルールが選択されます。

スケールアウト ルールがトリガーされない場合は、すべてのスケールイン ルール (**direction が "Decrease"** のルール) が評価されます。 自動スケールでは、すべてのスケールイン ルールがトリガーされた場合にのみ、スケールイン操作が実行されます。

それぞれのルールの **scaleAction** で指定された新しい容量が計算されます。 次に、サービスの可用性を確保するために、それらの容量のうち、容量が最大になるスケール操作が選択されます。

たとえば、現在の容量が 10 の仮想マシン スケール セットがあり、 容量を 50% 減らすルールと、容量を 3 減らすルールの 2 つのスケールイン ルールがあるとします。 最初のルールでは新しい容量は 5 になり、2 番目のルールでは容量が 7 になります。 サービスの可用性を確保するために、自動スケールでは最大容量になるアクションが選択されるので、2 番目のルールが選択されます。

## <a name="next-steps"></a>次のステップ
自動スケールの詳細については、次のページを参照してください。

* [自動スケーリングの概要](./autoscale-overview.md)
* [Azure Monitor の自動スケールの一般的なメトリック](./autoscale-common-metrics.md)
* [Azure Monitor の自動スケールのベスト プラクティス](./autoscale-best-practices.md)
* [自動スケール操作を使用して電子メールと Webhook アラート通知を送信する](./autoscale-webhook-email.md)
* [自動スケールの REST API](/rest/api/monitor/autoscalesettings)

