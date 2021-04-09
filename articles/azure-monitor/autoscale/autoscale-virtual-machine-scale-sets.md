---
title: Azure Virtual Machines を使用した高度な自動スケール
description: 複数のルールおよびプロファイルと共に Resource Manager および VM スケール セットを使用して、スケール アクション時に電子メール送信と Webhook URL の呼び出しを行います。
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 06/25/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 26cfdf05847b01d28a8574483acc89cfeced0ffa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101717742"
---
# <a name="advanced-autoscale-configuration-using-resource-manager-templates-for-vm-scale-sets"></a>VM Scale Sets 向けの Resource Manager テンプレートを使用した高度な自動スケール構成
Virtual Machine Scale Sets では、パフォーマンス メトリックのしきい値、定期的なスケジュール、または特定の日付に基づいてスケールインおよびスケールアウトを行うことができます。 また、スケール アクションに対して電子メール通知や webhook 通知を構成することもできます。 このチュートリアルでは、これらすべてのオブジェクトを VM スケール セットで Resource Manager テンプレートを使用して構成する例を示します。

> [!NOTE]
> このチュートリアルでは VM Scale Sets の手順について説明しますが、この情報は [Cloud Services](https://azure.microsoft.com/services/cloud-services/)、[App Service - Web Apps](https://azure.microsoft.com/services/app-service/web/)、および [API Management サービス](../../api-management/api-management-key-concepts.md)の自動スケーリングにも適用されます。CPU などシンプルなパフォーマンス メトリックに基づいて VM スケール セットに単純なスケールイン/スケールアウトを設定する方法については、[Linux](../../virtual-machine-scale-sets/tutorial-autoscale-cli.md) や [Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) のドキュメントを参照してください。
>
>

## <a name="walkthrough"></a>チュートリアル
このチュートリアルでは、スケール セットの自動スケール設定の構成と更新に [Azure リソース エクスプローラー](https://resources.azure.com/)を使用します。 Azure リソース エクスプローラーを使用すると、Resource Manager テンプレートを使用した Azure リソースの管理を容易に行うことができます。 Azure リソース エクスプローラー ツールを初めて使用する場合は、[こちらの概要](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/)をご覧ください。

1. 基本的な自動スケール設定を指定して新しいスケール セットをデプロイします。 この記事では、Azure クイック スタート ギャラリーのスケール セットを使用します。ギャラリーには、基本的な自動スケール テンプレートが付随する Windows スケール セットが用意されています。 Linux スケール セットも同じように動作します。
2. スケール セットを作成したら、Azure リソース エクスプローラーからスケール セット リソースに移動します。 Microsoft.Insights ノードの下に次の要素が表示されます。

    ![Azure 用エクスプローラー](media/autoscale-virtual-machine-scale-sets/azure_explorer_navigate.png)

    テンプレートを実行すると、**autoscalewad** という名前の既定の自動スケール設定が作成されます。 右側に、この自動スケール設定の定義がすべて表示されます。 この例では、既定の自動スケール設定に、CPU 使用率に基づくスケールアウトとスケールインのルールが付属しています。  

3. また、スケジュールや特定の要件に基づいてプロファイルとルールを追加することもできます。 ここでは、3 つのプロファイルを使用して自動スケール設定を作成します。 自動スケールのプロファイルとルールの詳細については、[自動スケールのベスト プラクティス](autoscale-best-practices.md)に関するページを参照してください。  

    | プロファイルとルール | 説明 |
    |--- | --- |
    | **プロファイル** |**パフォーマンスまたはメトリック ベース** |
    | ルール |Service Bus キューのメッセージ数が x 以上 |
    | ルール |Service Bus キューのメッセージ数が y 以下 |
    | ルール |CPU 使用率が n を超える |
    | ルール |CPU 使用率が p 以下 |
    | **プロファイル** |**平日の午前中 (ルールなし)** |
    | **プロファイル** |**製品の発売日 (ルールなし)** |

4. 以下は、このチュートリアルで使用する架空のスケーリング シナリオです。

   * **負荷ベース** - スケール セットにホストされているアプリケーションの負荷に基づいてスケールアウトまたはスケールインを行います。*
   * **メッセージ キュー サイズ** - アプリケーションへの受信メッセージ用の Service Bus キューを使用します。 キューのメッセージ数と CPU 使用率を使用して、メッセージ数または CPU 使用率のいずれかがしきい値に達したときにスケール アクションをトリガーするように既定のプロファイルを構成します。\*
   * **週と日の時間帯** - "平日の午前中" という名前の、毎週特定の時間帯に実行されるプロファイルを使用します。 履歴データから、この時間帯に一定数の VM インスタンスでアプリケーションの負荷を処理すると効率が良くなることがわかっています。\*
   * **特別な日** - "製品の発売日" プロファイルを追加しました。 前もって特定の日の計画を立てておくことで、市場への発表やアプリケーションへの新製品の設定による負荷にアプリケーションを備えさせることができます。\*
   * *最後の 2 つのプロファイルには、その他のパフォーマンス メトリックに基づくルールも含まれる可能性があります。そのような場合は、該当するルールではなく、既定のパフォーマンス メトリックに基づくルールを使用することに決めました。定期的なプロファイルと日付ベースのプロファイルでは、ルールの使用は任意です。*

     自動スケール エンジンにおけるプロファイルとルールの優先順位付けは、[自動スケールのベスト プラクティス](autoscale-best-practices.md)に関する記事にも記載されています。
     自動スケールの一般的なメトリックの一覧については、[自動スケールの一般的なメトリック](autoscale-common-metrics.md)に関するページを参照してください。

5. リソース エクスプローラーが **読み取り/書き込み** モードになっていることを確認します。

    ![Autoscalewad, default autoscale setting](media/autoscale-virtual-machine-scale-sets/autoscalewad.png)

6. [編集] をクリックします。 自動スケール設定の "profiles" 要素を次の構成に **置き換え** ます。

    ![スクリーンショットは profiles 要素を示しています。](media/autoscale-virtual-machine-scale-sets/profiles.png)

    ```
    {
            "name": "Perf_Based_Scale",
            "capacity": {
              "minimum": "2",
              "maximum": "12",
              "default": "2"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 10
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
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 3
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "Percentage CPU",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
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
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
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
          },
          {
            "name": "Weekday_Morning_Hours_Scale",
            "capacity": {
              "minimum": "4",
              "maximum": "12",
              "default": "4"
            },
            "rules": [],
            "recurrence": {
              "frequency": "Week",
              "schedule": {
                "timeZone": "Pacific Standard Time",
                "days": [
                  "Monday",
                  "Tuesday",
                  "Wednesday",
                  "Thursday",
                  "Friday"
                ],
                "hours": [
                  6
                ],
                "minutes": [
                  0
                ]
              }
            }
          },
          {
            "name": "Product_Launch_Day",
            "capacity": {
              "minimum": "6",
              "maximum": "20",
              "default": "6"
            },
            "rules": [],
            "fixedDate": {
              "timeZone": "Pacific Standard Time",
              "start": "2016-06-20T00:06:00Z",
              "end": "2016-06-21T23:59:00Z"
            }
          }
    ```
    サポートされているフィールドとその値については、[自動スケールの REST API](/rest/api/monitor/autoscalesettings) に関するドキュメントを参照してください。 これで、自動スケール設定に、前述の 3 つのプロファイルを含めることができました。

7. 最後に、自動スケールの **通知** セクションを見てみます。 自動スケールの通知を使用すると、スケールアウトまたはスケールインのアクションが正常にトリガーされたときに、3 つのことを実行できます。
   - サブスクリプションの管理者と共同管理者に通知する。
   - ユーザーのグループに電子メールを送信する。
   - webhook 呼び出しをトリガーする。 この webhook を呼び出すと、自動スケールの条件とスケール セット リソースに関するメタデータが送信されます。 自動スケール webhook のペイロードの詳細については、[自動スケールに関する webhook と電子メールの通知を構成する方法](autoscale-webhook-email.md)に関する記事を参照してください。

   次のコードを自動スケール設定に追加します。具体的には、値が null である **notification** 要素を置き換えます。

   ```
   "notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": true,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]

   ```

   リソース エクスプローラーの **[配置]** ボタンをクリックすると、自動スケール設定が更新されます。

これで、VM スケール セットの自動スケール設定が更新され、複数のスケール プロファイルとスケール通知が追加されました。

## <a name="next-steps"></a>次の手順
次のリンク先を使用して、自動スケールの詳細をご確認ください。

[仮想マシン スケール セットの自動スケールに関するトラブルシューティング](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

[自動スケールの一般的なメトリック](autoscale-common-metrics.md)

[Azure の自動スケールのベスト プラクティス](autoscale-best-practices.md)

[PowerShell を使用した自動スケールの管理](../powershell-samples.md#create-and-manage-autoscale-settings)

[CLI を使用した自動スケールの管理](../cli-samples.md#autoscale)

[自動スケールに関する webhook と電子メールの通知の構成](autoscale-webhook-email.md)

[Microsoft.Insights/autoscalesettings](/azure/templates/microsoft.insights/autoscalesettings) テンプレート リファレンス