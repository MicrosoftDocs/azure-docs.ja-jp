---
title: "Azure API Management での通知と電子メール テンプレートの構成 | Microsoft Docs"
description: "Azure API Management で通知と電子メール テンプレートを構成する方法について説明します。"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: ee25f26d-4752-433b-af9c-3817db38aed5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 94e13ac6fec09081484a2f7f5d7bc1871822743f
ms.openlocfilehash: 3d8b74e32059cfc1a4c3a8fc7d3bd04676ee80c8
ms.lasthandoff: 01/31/2017

---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Azure API Management で通知と電子メール テンプレートを構成する方法
API Management には、特定のイベントの通知を構成する機能と、API Management インスタンスの管理者および開発者との通信に使用される電子メール テンプレートを構成する機能があります。 このトピックでは、使用可能なイベントの通知を構成する方法について説明し、これらのイベントに使用できる電子メール テンプレートを構成する方法の概要を示します。

## <a name="publisher-notifications"> </a>発行者通知の構成
通知を構成するには、API Management サービスについて Azure Portal で **[発行者ポータル]** をクリックします。 API Management パブリッシャー ポータルが表示されます。

![パブリッシャー ポータル][api-management-management-console]

> [!NOTE] 
> まだ API Management サービス インスタンスを作成していない場合は、[Azure API Management の概要][Get started with Azure API Management]に関するチュートリアルの「[API Management インスタンスの作成][Create an API Management service instance]」を参照してください。

左側の **[API Management]** メニューの **[通知]** をクリックして、使用可能な通知を表示します。

![[発行者通知]][api-management-publisher-notifications]

通知に対して、次のイベントを構成できます。

* **[サブスクリプション要求 (承認が必要)]** - 指定された電子メール受信者およびユーザーは、承認が必要な API 成果物のサブスクリプション要求に関する電子メール通知を受け取ります。
* **[新しいサブスクリプション]** - 指定された電子メール受信者およびユーザーは、新しい API 成果物のサブスクリプションに関する電子メール通知を受け取ります。
* **[アプリケーション ギャラリー要求]** - 指定された電子メール受信者およびユーザーは、新しいアプリケーションがアプリケーション ギャラリーに送信されたときに電子メール通知を受け取ります。
* **[BCC]** - 指定された電子メール受信者およびユーザーは、開発者宛てに送信されたすべての電子メールの電子メール ブラインド カーボン コピーを受け取ります。
* **[新しい問題またはコメント]** - 指定された電子メール受信者およびユーザーは、新しい問題やコメントが開発者ポータルに送信されたときに電子メール通知を受け取ります。
* **[アカウントの使用停止メッセージ]** - 指定された電子メール受信者およびユーザーは、アカウントの使用が停止されたときに電子メール通知を受け取ります。
* **[サブスクリプション クォータ制限間近]** - 指定された電子メール受信者およびユーザーは、サブスクリプションの使用量が使用量クォータに近づいたときに電子メール通知を受け取ります。

それぞれのイベントに対し、電子メール アドレス テキスト ボックスを使用して電子メール受信者を指定するか、または一覧からユーザーを選択できます。

通知先の電子メール アドレスを指定するには、電子メール アドレス テキスト ボックスに電子メール アドレスを入力します。 複数の電子メール アドレスを指定するには、電子メール アドレスをコンマで区切ります。

![通知の受信者][api-management-email-addresses]

通知を送信するユーザーを指定するには、**[受信者の追加]** をクリックします。目的のユーザーの横のチェック ボックスをオンにし、**[OK]** をクリックします。

> [!NOTE] 
> 一覧には管理者のみが表示されます。


通知の受信者を構成したら、 **[保存]** をクリックして、更新された通知の受信者を適用します。

> [!NOTE] 
> 変更を保存しないで **[発行者通知]** タブから移動しようとすると、パブリッシャー ポータルにその旨を示すメッセージが表示されます。


## <a name="email-templates"> </a>電子メール テンプレートの構成
API Management には、サービスの管理および使用に関連して送信する電子メール メッセージ向けの電子メール テンプレートが用意されています。 次の電子メール テンプレートが用意されています。

* 承認されたアプリケーション ギャラリーへの送信
* 開発者向けのお別れのあいさつ
* 開発者向けのクォータ制限間近通知
* ユーザーの招待
* 問題に追加された新しいコメント
* 新しい問題の受信
* アクティブ化された新しいサブスクリプション
* サブスクリプションの更新の確認
* サブスクリプション要求の拒否
* サブスクリプション要求の受信

これらのテンプレートは、必要に応じて変更できます。

API Management インスタンスの電子メール テンプレートを表示および構成するには、左側の **[API Management]** メニューの **[通知]** をクリックし、**[電子メール テンプレート]** タブを選択します。

![Email templates][api-management-email-templates]

特定のテンプレートを表示および構成するには、 **[テンプレート]** ボックスの一覧から目的のテンプレートを選択します。

![電子メール テンプレートの一覧][api-management-email-templates-list]

それぞれの電子メール テンプレートは、件名がプレーンテキスト形式で記述され、本文定義が HTML 形式で記述されています。 必要に応じてそれぞれの項目をカスタマイズできます。

![電子メール テンプレート エディター][api-management-email-template]

**[パラメーター]** には、パラメーターの一覧が表示されます。件名または本文にパラメーターを挿入しておくと、電子メールの送信時にパラメーターが指定された値と置き換えられます。 パラメーターを挿入するには、パラメーターを挿入する場所にカーソルを置いた状態で、パラメーター名の左側の矢印をクリックします。

**[プレビュー]** または **[テスト送信]** をクリックすると、電子メールのプレビューを表示したり、テスト用の電子メールを送信したりできます。

> [!NOTE] 
> プレビュー時またはテスト用のメールの送信時は、パラメーターは実際の値に置き換えられません。

電子メール テンプレートに加えた変更を保存するには、**[保存]** をクリックします。変更を取り消すには、**[キャンセル]** をクリックします。
 

[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png


[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png


[Configure publisher notifications]: #publisher-notifications
[Configure email templates]: #email-templates

[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

