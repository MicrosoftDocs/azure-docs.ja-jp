---
title: 自動スケールを使用して電子メールと webhook アラート通知を送信する
description: Azure Monitor で自動スケール操作を使用して Web URL を呼び出したり、電子メール通知を送信したりする方法について説明します。
ms.topic: conceptual
ms.date: 04/03/2017
ms.subservice: autoscale
ms.openlocfilehash: 3b1f13fd1ce8bedcbe58385d4cee321f1d1405df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100603390"
---
# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Azure Monitor で自動スケール操作を使用して電子メールと webhook アラート通知を送信する
この記事では、Azure で自動スケール操作に基づいて特定の Web URL を呼び出すことや電子メールを送信することができるようにトリガーを設定する方法について説明します。  

## <a name="webhooks"></a>Webhooks
Webhook を使用すると、後処理やカスタム通知のために、Azure アラート通知を他のシステムにルーティングすることができます。 たとえば、受信 Web 要求を処理して SMS を送信する、バグをログに記録する、チャットやメッセージング サービスを使用してチームに通知するなどのサービスにアラートをルーティングすることができます。Webhook URI は有効な HTTP または HTTPS エンドポイントである必要があります。

## <a name="email"></a>Email
電子メールは、任意の有効な電子メール アドレスに送信できます。 このルールが実行されているサブスクリプションの管理者と共同管理者にも通知されます。

## <a name="cloud-services-and-app-services"></a>Cloud Services と App Services
Azure portal から Cloud Services とサーバー ファーム (App Services) をオプトインできます。

* **[スケールの基準]** メトリックを選択します。

![[スケールの基準]](./media/autoscale-webhook-email/insights-autoscale-notify.png)

## <a name="virtual-machine-scale-sets"></a>仮想マシン スケール セット
Resource Manager で作成された比較的新しい仮想マシン (仮想マシン スケール セット) の場合、REST API、Resource Manager テンプレート、PowerShell、CLI を使用してこれを構成できます。 ポータルのインターフェイスはまだ使用できません。
REST API または Resource Manager テンプレートを使用する場合は、次のオプションを使用して [autoscalesettings](/azure/templates/microsoft.insights/2015-04-01/autoscalesettings) に通知要素を追加してください。

```
"notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": false,
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

| フィールド | 必須 | 説明 |
| --- | --- | --- |
| operation |はい |値は "Scale" にする必要があります。 |
| sendToSubscriptionAdministrator |はい |値は "true" または "false" にする必要があります。 |
| sendToSubscriptionCoAdministrators |はい |値は "true" または "false" にする必要があります。 |
| customEmails |はい |値として null [] または電子メールの文字列配列を指定できます。 |
| webhooks |はい |値として null または有効な URI を指定できます。 |
| serviceUri |はい |有効な https URI を指定します。 |
| properties |はい |値は空 {} にするか、キーと値のペアを指定できます。 |

## <a name="authentication-in-webhooks"></a>Webhook での認証
webhook はトークンベースの認証を利用して認証できます。クエリ パラメーターとしてトークン ID を使用し、webhook URI を保存します。 https:\//mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue などです

## <a name="autoscale-notification-webhook-payload-schema"></a>自動スケール通知の Webhook ペイロード スキーマ
自動スケール通知が生成されると、次のメタデータが Webhook ペイロードに含まれます。

```
{
        "version": "1.0",
        "status": "Activated",
        "operation": "Scale In",
        "context": {
                "timestamp": "2016-03-11T07:31:04.5834118Z",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/myautoscaleSetting",
                "name": "myautoscaleSetting",
                "details": "Autoscale successfully started scale operation for resource 'MyCSRole' from capacity '3' to capacity '2'",
                "subscriptionId": "s1",
                "resourceGroupName": "rg1",
                "resourceName": "MyCSRole",
                "resourceType": "microsoft.classiccompute/domainnames/slots/roles",
                "resourceId": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService/slots/Production/roles/MyCSRole",
                "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService",
                "oldCapacity": "3",
                "newCapacity": "2"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```


| フィールド | 必須 | 説明 |
| --- | --- | --- |
| status |はい |自動スケール操作が生成されたことを示す状態。 |
| operation |はい |インスタンスの増加の場合は "Scale Out"、インスタンスの減少の場合は "Scale In" になります。 |
| context |はい |自動スケール操作のコンテキスト。 |
| timestamp |はい |自動スケール操作がトリガーされたときのタイム スタンプ。 |
| id |はい |自動スケール設定の Resource Manager ID |
| name |はい |自動スケール設定の名前。 |
| details |はい |自動スケール サービスが実行した操作とインスタンス数の変更の説明。 |
| subscriptionId |はい |スケールされるターゲット リソースのサブスクリプション ID。 |
| resourceGroupName |はい |スケールされるターゲット リソースのリソース グループ名。 |
| resourceName |はい |スケールされるターゲット リソースの名前。 |
| resourceType |はい |"microsoft.classiccompute/domainnames/slots/roles" (クラウド サービス ロール)、"microsoft.compute/virtualmachinescalesets" (仮想マシン スケール セット)、"Microsoft.Web/serverfarms" (Web アプリ) の 3 つの値がサポートされています。 |
| resourceId |はい |スケールされるターゲット リソースの Resource Manager ID。 |
| portalLink |はい |ターゲット リソースの概要ページへの Azure ポータルのリンク。 |
| oldCapacity |はい |自動スケールによってスケール操作が実行された時点の (以前の) インスタンス数。 |
| newCapacity |はい |自動スケールによってリソースがスケールされた後の新しいインスタンス数。 |
| properties |いいえ |省略可能。 <Key, Value> ペアのセット (例: Dictionary <String, String>)。 properties フィールドは省略可能です。 カスタム ユーザー インターフェイスまたはロジック アプリ ベースのワークフローでは、ペイロードを使用して渡すことのできるキーと値を入力できます。 Webhook URI 自体を (クエリ パラメーターとして) 使用して、カスタム プロパティを送信 Webhook 呼び出しに戻すこともできます。 |
