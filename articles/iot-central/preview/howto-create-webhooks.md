---
title: Azure IoT Central でルールに対する Webhook を作成する | Microsoft Docs
description: Azure IoT Central でルールが作動したときに他のアプリケーションに自動的に通知する Webhook を作成します。
author: viv-liu
ms.author: viviali
ms.date: 12/02/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 206c93c291654d22e945c3064e3c581dceb50cea
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895469"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central-preview-features"></a>Azure IoT Central でルールに対する Webhook アクションを作成する (プレビュー機能)

*このトピックはビルダーおよび管理者向けです。*

Webhook を使用すると、IoT Central アプリを他のアプリケーションやサービスに接続して、リモート監視や通知を行うことができます。 IoT Central アプリ内でルールがトリガーされるたびに、Webhook は、接続されている他のアプリケーションやサービスに対して自動的に通知を行います。 ルールがトリガーされるたびに、IoT Central アプリから他のアプリケーションの HTTP エンドポイントに POST 要求が送信されます。 ペイロードには、デバイスの詳細とルールのトリガーの詳細が含まれます。

## <a name="set-up-the-webhook"></a>Webhook を設定する

この例では RequestBin に接続し、ルールが作動したときに、Webhook を使用して通知が届くようにします。

1. [RequestBin](https://requestbin.net/) を開きます。

1. 新しい RequestBin を作成し、**Bin URL** をコピーします。

1. [テレメトリ ルール](tutorial-create-telemetry-rules.md)を作成します。 ルールを保存し、新しいアクションを追加します。

    ![Webhook の作成画面](media/howto-create-webhooks/webhookcreate.png)

1. Webhook アクションを選択し、表示名を指定して、Bin URL をコールバック URL として貼り付けます。

1. ルールを保存します。

ルールがトリガーされると、新しい要求が RequestBin に表示されます。

## <a name="payload"></a>ペイロード

ルールがトリガーされると、テレメトリ、デバイス、ルール、アプリケーションの詳細に関する JSON ペイロードを含む HTTP POST 要求が、コールバック URL に対して行われます。 ペイロードは次のようになります。

```json
{
    "id": "<id>",
    "displayName": "Webhook 1",
    "timestamp": "2019-10-24T18:27:13.538Z",
    "rule": {
        "id": "<id>",
        "displayName": "High temp alert",
        "enabled": true
    },
    "device": {
        "id": "mx1",
        "displayName": "MXChip IoT DevKit - mx1",
        "instanceOf": "<device-template-id>",
        "simulated": true,
        "provisioned": true,
        "approved": true
    },
    "data": [{
        "@id": "<id>",
        "@type": ["Telemetry"],
        "name": "temperature",
        "displayName": "Temperature",
        "value": 66.27310467496761,
        "interfaceInstanceName": "sensors"
    }],
    "application": {
        "id": "<id>",
        "displayName": "x - Store Analytics Checkout---PnP",
        "subdomain": "<subdomain>",
        "host": "<host>"
    }
}
```

## <a name="known-limitations"></a>既知の制限事項

現在、Webhook の登録/登録解除をプログラムから API を使って行う方法はありません。

この機能を改良する方法についてアイデアがありましたら、[UserVoice フォーラム](https://feedback.azure.com/forums/911455-azure-iot-central)でご提案ください。

## <a name="next-steps"></a>次の手順

Webhook を設定して使用する方法がわかったら、[Azure Monitor のアクション グループの構成](howto-use-action-groups.md)について調べることをお勧めします。
