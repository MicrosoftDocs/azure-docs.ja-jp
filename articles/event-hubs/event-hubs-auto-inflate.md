---
title: スループットユニットを自動的にスケールアップする - Azure Event Hubs | Microsoft Docs
description: 名前空間の自動インフレを有効にしてスループット単位を自動的にスケールアップします
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: d705993c7cd3816e89da21625dc5b003435b9128
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408164"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Azure Event Hubs のスループット単位を自動的にスケールアップする
Azure Event Hubs は、拡張性の高いデータ ストリーミング プラットフォームです。 そのため、Event Hubs の使用は、多くの場合、サービスの利用開始後に増えます。 このような使用では、Event Hubs のスケーリングとより大きな転送率の処理を行うために、事前に定義された[スループット単位](event-hubs-features.md#throughput-units)を増やす必要があります。 Event Hubs の**自動インフレ**機能は、使用量のニーズに合わせてスループット単位の数を増やすことで、自動的にスケールアップします。 スループット単位を増やすことで、以下の状況で必要になる調整シナリオを防ぐことができます。

* データの受信レートが、設定されたスループット単位を超えている。
* データの送信要求レートが、設定されたスループット単位を超えている。

負荷が最小しきい値を超えていて、ServerBusy エラーなどで失敗した要求がない場合、Event Hubs サービスでスループットが増えます。

## <a name="how-auto-inflate-works"></a>自動インフレのしくみ

Event Hubs のトラフィックは [スループット単位](event-hubs-features.md#throughput-units)で制御されます。 受信の場合、1 スループット単位は 1 MB/秒となり、送信の場合はその倍となります。 標準的なイベント ハブを構成するときのスループット単位は 1 ～ 20 です。 自動インフレを使用すると、自分で選択した最低限必要なスループット単位から小さく始めることができます。 その後、この機能は、トラフィックの増加に応じて、必要なスループット単位の上限を自動的にスケーリングします。 自動インフレには、次の利点があります。

- 小さく始めて、成長と共にスケールアップする効率的なスケーリング メカニズムです。
- 調整問題なしで、指定された上限に自動的にスケーリングします。
- タイミングと量を制御できるため、スケーリングを細かく制御できます。

## <a name="enable-auto-inflate-on-a-namespace"></a>名前空間で自動インフレを有効にする

Event Hubs 名前空間の自動インフレを有効または無効にするには、次のいずれかの方法を使用します。

- [Azure ポータル](https://portal.azure.com)。
- [Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate)。

### <a name="enable-auto-inflate-through-the-portal"></a>ポータルで自動インフレを有効にする


#### <a name="enable-at-the-time-of-creation"></a>作成時に有効にする 
自動インフレ機能は、**Event Hubs 名前空間の作成時**に有効にできます。
 
![イベント ハブの作成時に自動インフレを有効にする](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

このオプションを有効にすると、小さなスループット単位で開始し、使用量のニーズの増加に合わせてスケールアップできます。 インフレの上限はすぐには料金に影響しません。料金は 1 時間ごとに使用されるスループット単位の数によって決まります。

#### <a name="enable-auto-inflate-for-an-existing-event-hub"></a>既存のイベント ハブの自動インフレを有効にする
自動インフレ機能を有効にし、次の手順を使用してその設定を変更することもできます。 
 
1. **[Event Hubs 名前空間**] ページの **[Auto-inflate throughput units](自動インフレのスループット ユニット)** で **[無効]** を選択します。  

    ![[Event Hubs 名前空間] ページでスループット ユニットを選択する](./media/event-hubs-auto-inflate/select-throughput-units.png)
2. **[スケールの設定**] ページで、**[有効]** のチェック ボックスをオンにします (自動スケール機能が有効になっていなかった場合)。

    ![[有効] を選択する](./media/event-hubs-auto-inflate/scale-settings.png)
3. スループット ユニットの**最大**数を入力するか、スクロール バーを使用して値を設定します。 
4. (省略可能) このページの上部でスループット ユニットの**最小**数を更新します。 


> [!NOTE]
> 自動インフレの構成を適用してスループット ユニットを増やした場合、Event Hubs サービスにより、スループットがいつ何故増加したかに関する情報を提供する診断ログが出力されます。 イベント ハブの診断ログを有効にするには、Azure portal のイベント ハブ ページの左のメニューで **[診断設定]** を選択します。 詳しくは、「[Azure イベント ハブの診断ログを設定する](event-hubs-diagnostic-logs.md)」をご覧ください。 

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して自動インフレを有効にする

Azure Resource Manager テンプレートのデプロイ時に自動インフレを有効にできます。 たとえば、`isAutoInflateEnabled` プロパティを **true** に設定し、`maximumThroughputUnits` を 10 に設定します。 例: 

```json
"resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "properties": {
                "isAutoInflateEnabled": true,
                "maximumThroughputUnits": 10
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {},
                    "resources": [
                        {
                            "apiVersion": "2017-04-01",
                            "name": "[parameters('consumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {}
                        }
                    ]
                }
            ]
        }
    ]
```

完全なテンプレートについては、GitHub の[イベント ハブ名前空間とインフレの有効化](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate)のテンプレートを参照してください。


## <a name="next-steps"></a>次の手順

Event Hubs の詳細については、次のリンク先を参照してください:

* [Event Hubs の概要](event-hubs-what-is-event-hubs.md)

