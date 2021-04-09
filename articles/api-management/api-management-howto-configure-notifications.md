---
title: 通知と電子メール テンプレートの構成
titleSuffix: Azure API Management
description: Azure API Management で通知と電子メール テンプレートを構成する方法について説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: 786a9e26003a7afb98307e0bd7fae94c42a2f00d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84690304"
---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Azure API Management で通知と電子メール テンプレートを構成する方法

API Management には、特定のイベントの通知を構成する機能と、API Management インスタンスの管理者および開発者との通信に使用される電子メール テンプレートを構成する機能があります。 この記事では、使用可能なイベントの通知を構成する方法について説明し、これらのイベントに使用できる電子メール テンプレートを構成する方法の概要を示します。

## <a name="prerequisites"></a>前提条件

API Management サービス インスタンスを保有していない場合は、[Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関するクイックスタートを完了します。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="configure-notifications"></a><a name="publisher-notifications"> </a>通知の構成

1.  **API Management** インスタンスを選択します。
2.  **[通知]** をクリックして、使用可能な通知を表示します。

    ![[発行者通知]][api-management-publisher-notifications]

    通知に対して、次のイベントを構成できます。

    -   **[サブスクリプション要求 (承認が必要)]** - 指定された電子メール受信者およびユーザーは、承認が必要な API 成果物のサブスクリプション要求に関する電子メール通知を受け取ります。
    -   **[新しいサブスクリプション]** - 指定された電子メール受信者およびユーザーは、新しい API 成果物のサブスクリプションに関する電子メール通知を受け取ります。
    -   **[アプリケーション ギャラリー要求]** - 指定された電子メール受信者およびユーザーは、新しいアプリケーションがアプリケーション ギャラリーに送信されたときに電子メール通知を受け取ります。
    -   **[BCC]** - 指定された電子メール受信者およびユーザーは、開発者宛てに送信されたすべての電子メールの電子メール ブラインド カーボン コピーを受け取ります。
    -   **[新しい問題またはコメント]** - 指定された電子メール受信者およびユーザーは、新しい問題やコメントが開発者ポータルに送信されたときに電子メール通知を受け取ります。
    -   **[アカウントの使用停止メッセージ]** - 指定された電子メール受信者およびユーザーは、アカウントの使用が停止されたときに電子メール通知を受け取ります。
    -   **[サブスクリプション クォータ制限間近]** - 指定された電子メール受信者およびユーザーは、サブスクリプションの使用量が使用量クォータに近づいたときに電子メール通知を受け取ります。

        > [!NOTE]
        > 通知は、[サブスクリプション別のクォータ](api-management-access-restriction-policies.md#SetUsageQuota) ポリシーのみによってトリガーされます。 [キー別のクォータ](api-management-access-restriction-policies.md#SetUsageQuotaByKey) ポリシーでは、通知は生成されません。

    それぞれのイベントに対し、電子メール アドレス テキスト ボックスを使用して電子メール受信者を指定するか、または一覧からユーザーを選択できます。

3.  通知先の電子メール アドレスを指定するには、電子メール アドレス テキスト ボックスに電子メール アドレスを入力します。 複数の電子メール アドレスを指定するには、電子メール アドレスをコンマで区切ります。

    ![通知の受信者][api-management-email-addresses]

4.  **[追加]** をクリックします。

## <a name="configure-notification-templates"></a><a name="email-templates"> </a>通知テンプレートの構成

API Management には、サービスの管理および使用に関連して送信する電子メール メッセージ向けの通知テンプレートが用意されています。 次の電子メール テンプレートが用意されています。

-   承認されたアプリケーション ギャラリーへの送信
-   開発者向けのお別れのあいさつ
-   開発者向けのクォータ制限間近通知
-   ユーザーの招待
-   問題に追加された新しいコメント
-   新しい問題の受信
-   アクティブ化された新しいサブスクリプション
-   サブスクリプションの更新の確認
-   サブスクリプション要求の拒否
-   サブスクリプション要求の受信

これらのテンプレートは、必要に応じて変更できます。

API Management インスタンスの電子メール テンプレートを表示および構成するには、**[通知テンプレート]** をクリックします。

![電子メール テンプレート][api-management-email-templates]

それぞれの電子メール テンプレートは、件名がプレーンテキスト形式で記述され、本文定義が HTML 形式で記述されています。 必要に応じてそれぞれの項目をカスタマイズできます。

![電子メール テンプレート エディター][api-management-email-template]

**[パラメーター]** には、パラメーターの一覧が表示されます。件名または本文にパラメーターを挿入しておくと、電子メールの送信時にパラメーターが指定された値と置き換えられます。 パラメーターを挿入するには、パラメーターを挿入する場所にカーソルを置いた状態で、パラメーター名の左側の矢印をクリックします。

> [!NOTE]
> プレビュー時またはテスト用のメールの送信時は、パラメーターは実際の値に置き換えられません。

電子メール テンプレートに加えた変更を保存するには、**[保存]** をクリックします。変更を取り消すには、**[破棄]** をクリックします。

[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png
[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png
[configure publisher notifications]: #publisher-notifications
[configure email templates]: #email-templates
[how to create and use groups]: api-management-howto-create-groups.md
[how to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer
[get started with azure api management]: get-started-create-service-instance.md
[create an api management service instance]: get-started-create-service-instance.md
