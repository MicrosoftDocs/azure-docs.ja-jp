---
title: Azure Security Center でセキュリティ連絡先の詳細情報を指定する | Microsoft Docs
description: このドキュメントでは、Azure Security Center でセキュリティ連絡先の詳細情報を指定する方法について説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/11/2020
ms.author: memildin
ms.openlocfilehash: dda61b81ee2c357ddac29701832fe4780ea06859
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88516301"
---
# <a name="set-up-email-notifications-for-security-alerts"></a>セキュリティ アラートのメール通知を設定する 

組織内の適切なユーザーが環境内のセキュリティ アラートについて通知を受け取るようにするには、 **[メール通知]** 設定ページでメール アドレスを入力します。

通知を設定するときに、特定の個人またはサブスクリプションの特定の Azure ロールを持つユーザーにメールを送信するように構成できます。 

アラート疲れを避けるため、Security Center では送信メールの量が制限されています。 各サブスクリプションについて、Security Center では以下のように送信が行われます。

- **重要度が高い**アラートに関しては、1 日あたり最大 **4 通**のメール
- **重要度が中程度**のアラートに関しては、1 日あたり最大 **2 通**のメール
- **重要度が低い**アラートに関しては、1 日あたり最大 **1 通**のメール


:::image type="content" source="./media/security-center-provide-security-contacts/email-notification-settings.png" alt-text="セキュリティ アラートに関する電子メールを受信する連絡先の詳細を構成しています。" :::

## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般公開|
|価格:|Free レベル|
|必要なロールとアクセス許可:|**Security Admin**<br>**サブスクリプションの所有者** |
|クラウド:|![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![Yes](./media/icons/yes-icon.png) US Gov (一部)<br>![No](./media/icons/no-icon.png) China Gov、その他の Gov|
|||


## <a name="set-up-email-notifications-for-alerts"></a>アラートの電子メール通知を設定する<a name="email"></a>

個人または特定の Azure ロールを持つすべてのユーザーにメール通知を送信できます。

1. Security Center の **[Pricing & settings]\(価格と設定\)** 領域の関連するサブスクリプションで、 **[メール通知]** を選択します。

1. 以下のように通知の受信者を定義します。

    - ドロップダウン リストから、使用可能なロールを選択します。
    - または、特定のメール アドレスをコンマ区切りで入力します。 入力できる電子メール アドレスの数に制限はありません。

1. セキュリティ連絡先の情報をサブスクリプションに適用するには、 **[保存]** を選択します。


## <a name="see-also"></a>関連項目
セキュリティ アラートの詳細については、次を参照してください。

* [セキュリティ アラート - リファレンス ガイド](alerts-reference.md) - Azure Security Center の Threat Protection モジュールで表示される可能性のあるセキュリティ アラートについて説明しています
* [Azure Security Center でのセキュリティ アラートの管理と対応](security-center-managing-and-responding-alerts.md): セキュリティ アラートの管理と対応の方法について説明しています
* [ワークフローの自動化](workflow-automation.md) -- カスタム通知ロジックを使用してアラートに対する応答を自動化します