---
title: Azure IoT Central ルールから複数のアクションを実行する | Microsoft Docs
description: 1 つの IoT Central ルールから複数のアクションを実行し、複数のルールから実行できる、再利用可能なアクション グループを作成します。
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: conceptual
ms.service: iot-central
manager: philmea
ms.openlocfilehash: d9d7b2d189c6a1533be2d1cae4989669787c3f2a
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849001"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>1 つまたは複数のルールから実行する複数のアクションをグループ化する

*この記事はビルダーと管理者に適用されます。*

Azure IoT Central では、条件に一致したとき、アクションを実行するルールを作成します。 ルールはデバイスのテレメトリやイベントに基づきます。 たとえば、デバイスの温度がしきい値を超えたとき、オペレーターに通知できます。 この記事では、[Azure Monitor](../azure-monitor/overview.md) *アクション グループ*を使用し、複数のアクションを IoT Central ルールに関連付ける方法について説明します。 複数のルールにアクション グループを関連付けることができます。 [アクション グループ](../azure-monitor/platform/action-groups.md)は、Azure サブスクリプションの所有者によって定義された通知設定のコレクションです。

## <a name="prerequisites"></a>前提条件

- 従量課金制アプリケーション
- Azure Monitor アクション グループを作成して管理する Azure アカウントおよびサブスクリプション

## <a name="create-action-groups"></a>アクション グループを作成する

[Azure portal でアクション グループを作成して管理](../azure-monitor/platform/action-groups.md)できます。あるいは、[Azure Resource Manager テンプレートを利用して](../azure-monitor/platform/action-groups-create-resource-manager-template.md)アクション グループを作成し、管理できます。

アクション グループでできること:

- メール、SMS、音声通話で通知を送信します。
- Webhook を呼び出すなど、操作を実行します。

次のスクリーンショットでは、メールや SMS の通知を送信し、Webhook を呼び出すアクション グループを確認できます。

![アクション グループ](media/howto-use-action-groups/actiongroup.png)

IoT Central ルールでアクション グループを使用するには、IoT Central アプリケーションと同じ Azure サブスクリプションにアクション グループを置く必要があります。

## <a name="use-an-action-group"></a>アクション グループを使用する

IoT Central アプリケーションでアクション グループを使用するには、まず、テレメトリやイベントのルールを作成します。 ルールにアクションを追加するとき、 **[Azure Monitor アクション グループ]** を選択します。

![アクションの選択](media/howto-use-action-groups/chooseaction.png)

Azure サブスクリプションからアクション グループを選択します。

![アクション グループを選択する](media/howto-use-action-groups/chooseactiongroup.png)

**[保存]** を選択します。 これでこのアクション グループはアクションの一覧に表示され、ルールがトリガーされたときに実行されます。

![保存されたアクション グループ](media/howto-use-action-groups/savedactiongroup.png)

次の表は、サポートされているアクションの種類に送信される情報をまとめたものです。

| アクションの種類 | 出力形式 |
| ----------- | -------------- |
| Email       | 標準 IoT Central メール テンプレート |
| sms         | Azure IoT Central alert: ${applicationName} - "${ruleName}" triggered on "${deviceName}" at ${triggerDate} ${triggerTime} |
| 音声       | Azure I.O.T Central alert: rule "${ruleName}" triggered on device "${deviceName}" at ${triggerDate} ${triggerTime}, in application ${applicationName} |
| Webhook     | { "schemaId" :"AzureIoTCentralRuleWebhook", "data": {[regular webhook payload](#payload)} } |

次のテキストは、アクション グループからの SMS メッセージの例です。

`iotcentral: Azure IoT Central alert: Sample Contoso 22xu4spxjve - "Low pressure alert" triggered on "Refrigerator 2" at March 20, 2019 10:12 UTC`

<a id="payload"></a> 次の JSON は、Webhook アクション ペイロードの例です。

```json
{
  "schemaId":"AzureIoTCentralRuleWebhook",
  "data":{
    "id":"97ae27c4-17c5-4e13-9248-65c7a2c57a1b",
    "timestamp":"2019-03-20T10:53:17.059Z",
    "rule":{
      "id":"031b660e-528d-47bb-b33d-f1158d7e31bf",
      "name":"Low pressure alert",
      "enabled":true,
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      }
    },
    "device":{
      "id":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "name":"Refrigerator 2",
      "simulated":true,
      "deviceId":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      },
      "measurements":{
        "telemetry":{
           "pressure":343.269190673549
        }
      }
    },
    "application":{
      "id":"8e70742b-0d5c-4a1d-84f1-4dfd42e61c7b",
      "name":"Sample Contoso",
      "subdomain":"sample-contoso"
    }
  }
}
```

## <a name="next-steps"></a>次の手順

これでアクション グループとルールの使用方法に関する説明が終わりました。次の手順としては、[デバイスの管理](howto-manage-devices.md)方法を学習することをお勧めします。
