---
title: OpsGenie で webhook を使用して Azure サービス正常性アラートを構成する
description: OpsGenie インスタンスに送られたサービス正常性イベントについて、個人用に設定された通知を取得します。
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: d8867d442d0c7fe563f6429fc1ff4edb212737c5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594603"
---
# <a name="send-azure-service-health-alerts-with-opsgenie-using-webhooks"></a>OpsGenie で webhook を使用して Azure サービス正常性アラートを構成する

この記事では、OpsGenie で webhook を使用して Azure サービス正常性アラートを設定する方法について説明します。 [OpsGenie](https://www.opsgenie.com/) の Azure Service Health Integration を使用することにより、Azure Service Health アラートを OpsGenie に転送できます。 OpsGenie は、オンコール スケジュールに基づいて適切な通知対象ユーザーを判断し、メール、テキスト メッセージ (SMS)、電話、iOS と Android のプッシュ通知によって通知した後、確認されるか閉じられるまでアラートをエスカレーションします。

## <a name="creating-a-service-health-integration-url-in-opsgenie"></a>OpsGenie でサービス正常性統合 URL を作成する
1.  [OpsGenie](https://www.opsgenie.com/) アカウントをサインアップ済みであることを確認した後、サインインします。

1.  OpsGenie で **[Integrations (統合)]** セクションに移動します。

    ![OpsGenie の [Integrations (統合)] セクション](./media/webhook-alerts/opsgenie-integrations-section.png)

1.  **[Azure Service Health]** 統合ボタンをクリックします。

    ![OpsGenie の[Azure Service Health] ボタン](./media/webhook-alerts/opsgenie-azureservicehealth-button.png)

1.  **[Name (名前)]** にアラートの名前を入力し、**[Assigned to Team (チームに割り当て)]** フィールドを指定します。

1.  **[Recipients (受信者)]**、**[Enabled (有効)]**、**[Suppress Notifications (通知を抑制)]** などの他のフィールドに入力します。

1.  **[Integration URL (統合 URL)]** をコピーして保存します。これには既に `apiKey` が末尾に追加されています。

    ![OpsGenie に表示された統合 URL](./media/webhook-alerts/opsgenie-integration-url.png)

1.  **[Save Integration (統合の保存)]** を選択します。

## <a name="create-an-alert-using-opsgenie-in-the-azure-portal"></a>Azure Portal で OpsGenie を使用してアラートを作成する
### <a name="for-a-new-action-group"></a>新しいアクション グループの場合:
1. 「[Azure Portal を使用して新しいアクション グループのサービス正常性通知に関するアラートを作成する](./alerts-activity-log-service-notifications-portal.md)」の手順 1 から 8 を実行します。

1. **[アクション]** の一覧で以下を定義します。

    a. **[アクションの種類]:** *webhook*

    b. **詳細:** 先ほど保存した OpsGenie の **統合 URL**。

    c. **[名前]:** Webhook の名前、別名、または識別子。

1. 完了したら **[保存]** を選択して、アラートを作成します。

### <a name="for-an-existing-action-group"></a>既存のアクション グループの場合:
1. [Azure Portal](https://portal.azure.com/) で、 **[モニター]** を選択します。

1. **[設定]** セクションで **[アクション グループ]** を選択します。

1. 編集するアクション グループを見つけて選択します。

1. **[アクション]** の一覧に以下を追加します。

    a. **[アクションの種類]:** *webhook*

    b. **詳細:** 先ほど保存した OpsGenie の **統合 URL**。

    c. **[名前]:** Webhook の名前、別名、または識別子。

1. 完了したら **[保存]** を選択して、アクション グループを更新します。

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>HTTP POST 要求によって webhook 統合をテストする
1. 送信するサービス正常性のペイロードを作成します。 サービス正常性 webhook ペイロードの例については、「[Azure アクティビティ ログ アラートのための webhook](../azure-monitor/alerts/activity-log-alerts-webhook.md)」を参照してください。

1. 次のような HTTP POST 要求を作成します。

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. "successful" の状態を示すメッセージと共に `200 OK` 応答が返されるはずです。

1. [OpsGenie](https://www.opsgenie.com/) に移動して、統合が正常に設定されたことを確認します。

## <a name="next-steps"></a>次のステップ
- [既存の問題管理システム用の webhook 通知を構成する](service-health-alert-webhook-guide.md)方法について学習します。
- [アクティビティ ログ アラート webhook スキーマ](../azure-monitor/alerts/activity-log-alerts-webhook.md)を確認します。 
- [サービス正常性の通知](./service-notifications.md)について学習します。
- [アクション グループ](../azure-monitor/alerts/action-groups.md)について学習します。
