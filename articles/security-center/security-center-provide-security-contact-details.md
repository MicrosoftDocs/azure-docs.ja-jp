---
title: Azure Security Center アラートの電子メール通知を構成する
description: Azure Security Center によって送信されるセキュリティ アラートの電子メールの種類を微調整する方法について学習します。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 02/09/2021
ms.author: memildin
ms.openlocfilehash: 342904a3ae996fe8c2eeddf2edfbc4283a3d03eb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102439444"
---
# <a name="configure-email-notifications-for-security-alerts"></a>セキュリティ アラートの電子メール通知を構成する 

セキュリティ アラートは、組織内の適切な人物に届ける必要があります。 既定では、サブスクリプションの重大度が高いアラートがトリガーされるたびに、Security Center からサブスクリプションの所有者に電子メールが送信されます。 このページでは、これらの通知をカスタマイズする方法について説明します。

通知用電子メールに独自の設定を定義するには、Azure Security Center の **[電子メール通知]** 設定ページで以下を選択できます。

- **通知する必要がある "*ユーザー*"** - 電子メールは、選択した個人、またはサブスクリプションに指定された Azure ロールを持つ任意のユーザーに送信できます。 
- **通知する必要がある "*内容*"** - Security Center から通知を送信する必要がある重大度レベルを変更します。

アラート疲れを避けるため、Security Center では送信メールの量が制限されています。 各サブスクリプションについて、Security Center では以下のように送信が行われます。

- **重大度が高い** アラートの場合、**6 時間** ごとに最大 1 通 (1 日あたり 4 通) のメール
- **重大度が中程度** のアラートの場合、**12 時間** ごとに最大 1 通 (1 日あたり 2 通) のメール
- **重大度が低い** アラートの場合、**24 時間** ごとに最大 1 通のメール

:::image type="content" source="./media/security-center-provide-security-contacts/email-notification-settings.png" alt-text="セキュリティ アラートに関する電子メールを受信する連絡先の詳細を構成しています。" :::
 
## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般提供 (GA)|
|価格:|Free|
|必要なロールとアクセス許可:|**Security Admin**<br>**サブスクリプションの所有者** |
|クラウド:|![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![Yes](./media/icons/yes-icon.png) ナショナル/ソブリン (US Gov、China Gov、その他の Gov)|
|||


## <a name="customize-the-security-alerts-email-notifications-via-the-portal"></a>セキュリティ アラートの電子メール通知をポータルからカスタマイズする<a name="email"></a>
個人または特定の Azure ロールを持つすべてのユーザーにメール通知を送信できます。

1. Security Center の **[価格と設定]** 領域から、関連するサブスクリプションを選択し、 **[電子メール通知]** を選択します。

1. 次のいずれかまたは両方のオプションを使用して、通知の受信者を定義します。

    - ドロップダウン リストから、使用可能なロールを選択します。
    - 特定の電子メール アドレスをコンマ区切りで入力します。 入力できる電子メール アドレスの数に制限はありません。

1. セキュリティ連絡先の情報をサブスクリプションに適用するには、 **[保存]** を選択します。

## <a name="customize-the-alerts-email-notifications-through-the-api"></a>API を通じてアラートのメール通知をカスタマイズする
提供されている REST API を使用してメール通知を管理することもできます。 詳細については、[SecurityContacts API に関するドキュメント](/rest/api/securitycenter/securitycontacts)を参照してください。

これは、セキュリティ連絡先の構成を作成する際の PUT 要求本文の例です。

```json
{
    "properties": {
        "emails": admin@contoso.com;admin2@contoso.com,
        "notificationsByRole": {
            "state": "On",
            "roles": ["AccountAdmin", "Owner"]
        },
        "alertNotifications": {
            "state": "On",
            "minimalSeverity": "High"
        },
        "phone": ""
    }
}
```


## <a name="see-also"></a>関連項目
セキュリティ アラートの詳細については、次のページを参照してください。

- [セキュリティ アラート - リファレンス ガイド](alerts-reference.md) -- Azure Security Center の Threat Protection モジュールで表示される可能性のあるセキュリティ アラートについて学習します
- [Azure Security Center でのセキュリティ アラートの管理と対応](security-center-managing-and-responding-alerts.md) -- セキュリティ アラートの管理と対応の方法について学習します
- [ワークフローの自動化](workflow-automation.md) -- カスタム通知ロジックを使用してアラートに対する応答を自動化します