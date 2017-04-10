---
title: "Azure API Management でユーザー アカウントを管理する方法 | Microsoft Docs"
description: "Azure API Management でユーザーを作成または招待する方法について説明します。"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 078abfa5-1e4f-4c9d-b9c7-a172bd19c1a2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: a7ff82a47b4e972db96929acb47fcce760b244b3
ms.openlocfilehash: eb2e260e71d9ebc3000b440d0413c7d557e15258


---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Azure API Management でユーザー アカウントを管理する方法
API Management では、開発者は、API Management を使用して公開された API のユーザーになります。 このガイドでは、開発者を作成する方法と、API Management インスタンスで公開した API および成果物を案内する招待メッセージを開発者に送信する方法について説明します。 プログラムによるユーザー アカウントの管理については、「[API Management REST (API Management REST)](https://msdn.microsoft.com/library/azure/dn776326.aspx)」リファレンスの[ユーザー エンティティ](https://msdn.microsoft.com/library/azure/dn776330.aspx)に関するドキュメントを参照してください。

## <a name="create-developer"> </a>新しい開発者の作成
新しい開発者を作成するには、API Management サービスの Azure Portal で **[パブリッシャー ポータル]** をクリックします。 API Management パブリッシャー ポータルが表示されます。 まだ API Management サービス インスタンスを作成していない場合は、[Azure API Management の概要][Get started with Azure API Management]に関するチュートリアルの「[API Management インスタンスの作成][Create an API Management service instance]」を参照してください。

![パブリッシャー ポータル][api-management-management-console]

左側の **[API Management]** メニューの **[ユーザー]** をクリックし、**[ユーザーの追加]** をクリックします。

![開発者を作成する][api-management-create-developer]

新しい開発者の**電子メール**、**パスワード**、および**名前**を入力し、**[保存]** をクリックします。

![開発者を作成する][api-management-add-new-user]

既定では、新しく作成された開発者アカウントは**アクティブ**になり、**開発者**グループに関連付けられます。

![新しい開発者][api-management-new-developer]

**[アクティブ]** 状態の開発者アカウントを使用すると、サブスクリプションがあるすべての API にアクセスできます。 新しく作成した開発者を追加のグループに関連付ける方法については、「[グループと開発者の関連付け][How to associate groups with developers]」を参照してください。

## <a name="invite-developer"> </a>開発者の招待
開発者を招待するには、左側の **[API Management]** メニューの **[ユーザー]** をクリックし、**[ユーザーの招待]** をクリックします。

![開発者を招待する][api-management-invite-developer]

開発者の名前と電子メール アドレスを入力し、 **[招待]**をクリックします。

![開発者を招待する][api-management-invite-developer-window]

確認メッセージが表示されます。ただし、新しく招待した開発者は、招待を受け入れるまで一覧に表示されません。 

![招待の確認][api-management-invite-developer-confirmation]

開発者を招待すると、招待された開発者に電子メールが送信されます。 この電子メールはテンプレートを使用して生成され、カスタマイズできます。 詳細については、「[電子メール テンプレートの構成][Configure email templates]」を参照してください。

招待が受け入れられると、そのアカウントがアクティブになります。

## <a name="block-developer"> </a> 開発者アカウントの非アクティブ化および再アクティブ化
既定では、新しく作成または招待された開発者アカウントは **アクティブ**になります。 開発者アカウントを非アクティブ化するには、 **[ブロック]**をクリックします。 ブロックされている開発者アカウントを再アクティブ化するには、 **[アクティブ化]**をクリックします。 ブロックされている開発者アカウントは、開発者ポータルにアクセスすることも、API を呼び出すこともできません。 ユーザー アカウントを削除するには、 **[削除]**をクリックします。

![開発者をブロックする][api-management-new-developer]

## <a name="reset-a-user-password"></a>ユーザーのパスワードのリセット
ユーザー アカウントのパスワードをリセットするには、アカウント名をクリックします。

![[パスワードのリセット]][api-management-view-developer]

**[パスワードのリセット]** をクリックして、パスワードをリセットするためのリンクをユーザーに送信します。

![パスワードのリセット][api-management-reset-password]

プログラムによってユーザー アカウントを操作するには、「[API Management REST (API Management REST)](https://msdn.microsoft.com/library/azure/dn776326.aspx)」リファレンスの[ユーザー エンティティ](https://msdn.microsoft.com/library/azure/dn776330.aspx)に関するドキュメントを参照してください。 ユーザー アカウントのパスワードを特定の値にリセットするには、 [ユーザーの更新](https://msdn.microsoft.com/library/azure/dn776330.aspx#UpdateUser) 操作を使用して目的のパスワードを指定します。

## <a name="pending-verification"></a>保留中の検証
![保留中の検証][api-management-pending-verification]

## <a name="next-steps"> </a>次のステップ
開発者アカウントを作成した後は、このアカウントをロールに関連付け、成果物と API をサブスクライブできます。 詳細については、[グループを作成して使用する方法][How to create and use groups]に関するページを参照してください。

[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-pending-verification]: ./media/api-management-howto-create-or-invite-developers/api-management-pending-verification.png
[api-management-view-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-view-developer.png
[api-management-reset-password]: ./media/api-management-howto-create-or-invite-developers/api-management-reset-password.png


[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Configure email templates]: api-management-howto-configure-notifications.md#email-templates



<!--HONumber=Dec16_HO3-->


