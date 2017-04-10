---
title: "アクセス レビューを開始する方法 | Microsoft Docs"
description: "Azure Privileged Identity Management アプリケーションで特権 ID のアクセス レビューを作成する方法について説明します。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 3e52b731-55f4-4c8a-ba87-9fd34033f52f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8c9bac62a92947ea888f1ccdbd300f5b40cb3e07
ms.lasthandoff: 12/29/2016


---
# <a name="how-to-start-an-access-review-in-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management でアクセス レビューを開始する方法
ユーザーが持っているアクセス特権がユーザーには不要になった場合、そのロールの割り当ては "古く" なっています。 このような古くなったロールの割り当てに関連するリスクを軽減するために、特権ロール管理者は、ユーザーに与えられているロールを定期的に確認する必要があります。 このドキュメントでは、Azure AD Privileged Identity Management (PIM) でアクセス レビューを開始する手順を説明します。

## <a name="start-an-access-review"></a>アクセス レビューを開始する
> [!NOTE]
> Azure Portal のダッシュボードに PIM アプリケーションをまだ追加していない場合は、「[Azure Privileged Identity Management の使用](active-directory-privileged-identity-management-getting-started.md)」の手順を参照してください。
> 
> 

PIM アプリケーションのメイン ページには、アクセス レビューを開始する方法が 3 つあります。

* **[アクセス レビュー]** > **[追加]**
* **[ロール]** > **[レビュー]**
* 確認する特定のロールをロールの一覧から選択し、**[レビュー]** をクリックする

**[レビュー]** をクリックすると、**[アクセス レビューを開始する]** ブレードが表示されます。 このブレードでは、名前と時間制限を使用してレビューを構成し、確認するロールを選択し、レビューを実行するユーザーを決定します。

![アクセス レビューを開始する - スクリーン ショット][1]

### <a name="configure-the-review"></a>レビューを構成する
アクセス レビューを作成するには、名前を付け、開始日と終了日を設定する必要があります。

![レビューを構成する - スクリーン ショット][2]

ユーザーが完了するための十分なレビューの期間の長さを作成します。 終了日の前に完了したら、常にレビューを早く停止できます。

### <a name="choose-a-role-to-review"></a>確認するロールを選択する
各レビューでは、1 つのロールに重点を置いています。 特定のロールのブレードからアクセス レビューを開始した場合を除き、ここでロールを選択する必要があります。

1.  **[役割メンバーシップをレビューする]**
   
    ![ロール メンバーシップを確認する - スクリーン ショット][3]
2. 一覧から 1 つのロールを選択します。

### <a name="decide-who-will-perform-the-review"></a>レビューを実行するユーザを決定する
レビューを実行するための 3 つのオプションがあります。 他のユーザーにレビューを割り当てて完了してもらうか、自分でレビューするか、または各ユーザーが自身のアクセスを確認できます。

1.  **[レビュー担当者の選択]**
   
    ![レビュー担当者を選択する - スクリーン ショット][4]
2. いずれかのオプションを選択してください。
   
   * **[レビュー担当者の選択]**: アクセスが必要なユーザーがわからない場合は、このオプションを使用します。 このオプションでは、リソース所有者またはグループ マネージャーにレビューを割り当て、完了してもらうことができます。
   * **[Me (自分)]**: アクセス レビューが機能する方法をプレビューするか、できないユーザーの代わりに確認する場合に役に立ちます。
   * **[Members review themselves (メンバーによる自身のレビュー)]**: ユーザーに自分のロール割り当てを確認してもらう場合は、このオプションを使用します。

### <a name="start-the-review"></a>レビューを開始する
最後に、アクセスを承認する場合にユーザーが理由を提供することを要求するオプションがあります。 必要に応じて、レビューの説明を追加し、 **[開始]**を選択します。

待機しているアクセス レビューがあることをユーザーに通知し、 [アクセス レビューを実行する方法](active-directory-privileged-identity-management-how-to-perform-security-review.md)を示します。

## <a name="manage-the-access-review"></a>アクセスレビューを管理する
レビュー担当者が Azure AD PIM ダッシュボードでレビューを完了すると、[アクセス レビュー] セクションで進行状況を追跡できます。 ディレクトリのアクセス権は、 [レビューが完了する](active-directory-privileged-identity-management-how-to-complete-review.md)まで変更されません。

レビュー期間が終了するまで、レビューの完了をユーザーに通知するか、または [アクセス レビュー] セクションからレビューを早めに停止することができます。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="pim-table-of-contents"></a>PIM の内容一覧
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_start_review.png
[2]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_configure.png
[3]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_role.png
[4]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_reviewers.png

