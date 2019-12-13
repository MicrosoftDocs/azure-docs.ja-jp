---
title: Azure IoT Central ルールから複数のアクションを実行する | Microsoft Docs
description: 1 つの IoT Central ルールから複数のアクションを実行し、複数のルールから実行できる、再利用可能なアクション グループを作成します。
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: conceptual
ms.service: iot-central
manager: philmea
ms.openlocfilehash: a93e96dd14411302bf5368b4825044e060b54a43
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974361"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules-preview-features"></a>1 つまたは複数のルールから実行する複数のアクションをグループ化する (プレビュー機能)

*この記事はビルダーと管理者に適用されます。*

Azure IoT Central では、条件に一致したとき、アクションを実行するルールを作成します。 ルールはデバイスのテレメトリやイベントに基づきます。 たとえば、デバイスの温度がしきい値を超えたとき、オペレーターに通知できます。 この記事では、[Azure Monitor](../../azure-monitor/overview.md) *アクション グループ*を使用し、複数のアクションを IoT Central ルールに関連付ける方法について説明します。 複数のルールにアクション グループを関連付けることができます。 [アクション グループ](../../azure-monitor/platform/action-groups.md)は、Azure サブスクリプションの所有者によって定義された通知設定のコレクションです。

## <a name="prerequisites"></a>前提条件

- 従量課金制アプリケーション
- Azure Monitor アクション グループを作成して管理する Azure アカウントおよびサブスクリプション

## <a name="create-action-groups"></a>アクション グループを作成する

[Azure portal でアクション グループを作成して管理](../../azure-monitor/platform/action-groups.md)できます。あるいは、[Azure Resource Manager テンプレートを利用して](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)アクション グループを作成し、管理できます。

アクション グループでできること:

- メール、SMS、音声通話で通知を送信します。
- Webhook を呼び出すなど、操作を実行します。

次のスクリーンショットでは、メールや SMS の通知を送信し、Webhook を呼び出すアクション グループを確認できます。

![アクション グループ](media/howto-use-action-groups/actiongroup.png)

IoT Central ルールでアクション グループを使用するには、IoT Central アプリケーションと同じ Azure サブスクリプションにアクション グループを置く必要があります。

## <a name="use-an-action-group"></a>アクション グループを使用する

IoT Central アプリケーションでアクション グループを使用するには、まずルールを作成します。 ルールにアクションを追加するとき、 **[Azure Monitor アクション グループ]** を選択します。

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
| Webhook     | { "schemaId" :"AzureIoTCentralRuleWebhook", "data": {[regular webhook payload](howto-create-webhooks.md#payload)}} |

次のテキストは、アクション グループからの SMS メッセージの例です。

`iotcentral: Azure IoT Central alert: Contoso - "Low pressure alert" triggered on "Motion sensor 2" at March 20, 2019 10:12 UTC`

## <a name="next-steps"></a>次の手順

これでアクション グループとルールの使用方法に関する説明が終わりました。次の手順としては、[デバイスの管理](howto-manage-devices.md)方法を学習することをお勧めします。
