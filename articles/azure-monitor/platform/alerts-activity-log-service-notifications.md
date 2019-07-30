---
title: Azure サービスの通知でアクティビティ ログ アラートを受け取る
description: Azure サービスが発生したときに、SMS、電子メール、または Webhook で通知を受け取ります。
author: shawntabrizi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/09/2018
ms.author: shtabriz
ms.openlocfilehash: 3157cef6952f01d689d3cb4a30c9d7c843095809
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071694"
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>サービス通知のアクティビティ ログ アラートを作成する
## <a name="overview"></a>概要
この記事では、Azure Portal を使用してサービスの正常性通知を行うアクティビティ ログ アラートを設定する方法について説明します。  

Azure でサービス正常性通知を Azure サブスクリプションに送信するときに、アラートを受け取ることができます。 次の情報に基づくアラートを構成できます。

- サービス正常性通知のクラス (サービスに関する問題、計画済みメンテナンス、正常性の勧告)。
- 影響を受けたサブスクリプション。
- 影響を受けたサービス。
- 影響を受けたリージョン。

> [!NOTE]
> サービス正常性通知では、リソース正常性イベントに関するアラートは送信されません。

次の方法でアラートを送信するユーザーを構成することもできます。

- 既存のアクション グループを選択します。
- 新しいアクション グループを作成します (将来のアラートで使用できます)。

アクション グループの詳細については、[アクション グループの作成および管理](../../azure-monitor/platform/action-groups.md)に関するページを参照してください。

Azure Resource Manager テンプレートを使用したサービス正常性通知アラートの構成方法の詳細については、[Resource Manager テンプレート](alerts-activity-log.md)に関するページを参照してください。

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>初めての Azure Service Health アラートの設定に関するビデオを見る

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="alert-and-new-action-group-using-azure-portal"></a>Azure portal を使用するアラートと新しいアクション グループ
1. [ポータル](https://portal.azure.com)で、 **[サービス正常性]** を選択します。

    ![[サービス正常性] サービス](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. **[アラート]** セクションで、 **[正常性アラート]** を選択します。

    ![[正常性アラート] タブ](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. **[サービス正常性アラートの作成]** を選択し、フィールドに入力します。

    ![[サービス正常性アラートの作成] コマンド](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. アラートを受け取る **[サブスクリプション]** 、 **[サービス]** 、 **[リージョン]** を選択します。

    ![[アクティビティ ログ アラートの追加] ダイアログ ボックス](media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)

> [!NOTE]
> このサブスクリプションは、アクティビティ ログ アラートの保存に使用されます。 アラート リソースはこのサブスクリプションにデプロイされ、アクティビティ ログのイベントを監視します。

1. アラートを受け取りたい**イベントの種類**を選択します。*サービスの問題*、*計画済みメンテナンス*、および*正常性の勧告*のいずれかです 

1. **[アラート ルール名]** と **[説明]** を入力してアラートの詳細を定義します。

1. アラートを保存する **[リソース グループ]** を選択します。

1. **[新しいアクション グループ]** を選択して新しいアクション グループを作成します。 **[アクション グループ名]** ボックスおよび **[短い名前]** ボックスに名前を入力します。 短い名前は、このアラートが発生したときに送信される通知で参照されます。

    ![新しいアクション グループを作成する](media/alerts-activity-log-service-notifications/action-group-creation.png)

1. 受信者についての次の情報を入力して、受信者の一覧を定義します。

    a. **[名前]** :受信者の名前、エイリアス、または識別子を入力します。

    b. **アクションの種類**:SMS、メール、webhook、Azure アプリなどを選択します。

    c. **詳細**:選択したアクションの種類に基づいて、電話番号、メール アドレス、Webhook の URI などを入力します。

1. **[OK]** を選択してアクション グループを選択し、 **[アラート ルールの作成]** を選択してアラートを完成させます。

数分以内にアラートがアクティブになり、作成時に指定した条件に基づいてトリガーが開始されます。

[既存の問題管理システム用の webhook 通知を構成する](../../service-health/service-health-alert-webhook-guide.md)方法について学習します。 アクティビティ ログ アラートの webhook スキーマの詳細については、「[Azure アクティビティ ログ アラートのための webhook](../../azure-monitor/platform/activity-log-alerts-webhook.md)」を参照してください。

>[!NOTE]
>上記の手順で定義されたアクション グループは、今後すべてのアラート定義で既存のアクション グループとして再利用できます。
>
>

## <a name="alert-with-existing-action-group-using-azure-portal"></a>Azure portal を使用する既存のアクション グループでのアラート

1. 前のセクションの手順 1 から 6 に従って、サービス正常性通知を作成します。 

1. **[Define action group]\(アクション グループの定義\)** で、 **[アクション グループの選択]** ボタンをクリックします。 適切なアクション グループを選択します。

1. **[追加]** を選択してアクション グループを追加し、 **[アラート ルールの作成]** を選択してアラートを完成させます。

数分以内にアラートがアクティブになり、作成時に指定した条件に基づいてトリガーが開始されます。

## <a name="alert-and-new-action-group-using-the-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用するアラートと新しいアクション グループ

電子メール ターゲットを使ってアクション グループを作成し、ターゲット サブスクリプションのすべてのサービス正常性通知を有効にする例を次に示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "actionGroups_name": {
            "defaultValue": "SubHealth",
            "type": "String"
        },
        "activityLogAlerts_name": {
            "defaultValue": "ServiceHealthActivityLogAlert",
            "type": "String"
        },
        "emailAddress":{
            "type":"string"
        }
    },
    "variables": {
        "alertScope":"[concat('/','subscriptions','/',subscription().subscriptionId)]"
    },
    "resources": [
        {
            "comments": "Action Group",
            "type": "microsoft.insights/actionGroups",
            "name": "[parameters('actionGroups_name')]",
            "apiVersion": "2017-04-01",
            "location": "Global",
            "tags": {},
            "scale": null,
            "properties": {
                "groupShortName": "[parameters('actionGroups_name')]",
                "enabled": true,
                "emailReceivers": [
                    {
                        "name": "[parameters('actionGroups_name')]",
                        "emailAddress": "[parameters('emailAddress')]"
                    }
                ],
                "smsReceivers": [],
                "webhookReceivers": []
            },
            "dependsOn": []
        },
        {
            "comments": "Service Health Activity Log Alert",
            "type": "microsoft.insights/activityLogAlerts",
            "name": "[parameters('activityLogAlerts_name')]",
            "apiVersion": "2017-04-01",
            "location": "Global",
            "tags": {},
            "scale": null,
            "properties": {
                "scopes": [
                    "[variables('alertScope')]"
                ],
                "condition": {
                    "allOf": [
                        {
                            "field": "category",
                            "equals": "ServiceHealth"
                        },
                        {
                            "field": "properties.incidentType",
                            "equals": "Incident"
                        }
                    ]
                },
                "actions": {
                    "actionGroups": [
                        {
                            "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
                            "webhookProperties": {}
                        }
                    ]
                },
                "enabled": true,
                "description": ""
            },
            "dependsOn": [
                "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
            ]
        }
    ]
}
```

## <a name="manage-your-alerts"></a>アラートの管理

アラートを作成すると、 **[モニター]** の **[アラート]** セクションにアラートが表示されます。 次の操作を行うために管理するアラートを選択します。

* 編集する。
* 削除する。
* 無効または有効にしてそのアラートの通知受信を一時的に停止または再開する。

## <a name="next-steps"></a>次の手順
- [Azure Service Health アラートの設定に関するベスト プラクティス](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa)を学習します。
- [Azure Service Health のモバイル プッシュ通知を設定](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw)する方法について学習します。
- [既存の問題管理システム用の webhook 通知を構成する](../../service-health/service-health-alert-webhook-guide.md)方法について学習します。
- [サービス正常性の通知](../../azure-monitor/platform/service-notifications.md)について学習します。
- [通知のレート制限](../../azure-monitor/platform/alerts-rate-limiting.md)について学習します。
- [アクティビティ ログ アラート webhook スキーマ](../../azure-monitor/platform/activity-log-alerts-webhook.md)を確認します。
- [アクティビティ ログ アラートの概要](../../azure-monitor/platform/alerts-overview.md)を把握し、アラートを受信する方法について学習します。 
- [アクション グループ](../../azure-monitor/platform/action-groups.md)について学習します。
