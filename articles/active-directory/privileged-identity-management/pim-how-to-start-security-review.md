---
title: PIM で Azure AD ロールのアクセス レビューを開始する - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure ロールに対するアクセス レビューを開始する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/21/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5cbf96c165d79c26985663ef5a9d64bbf8f9892
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2019
ms.locfileid: "58574996"
---
# <a name="start-an-access-review-for-azure-ad-roles-in-pim"></a>PIM で Azure AD ロールのアクセス レビューを開始する
ユーザーが持っているアクセス特権がユーザーには不要になった場合、そのロールの割り当ては "古く" なっています。 このような古くなったロールの割り当てに関連するリスクを軽減するには、特権ロール管理者またはグローバル管理者が定期的にアクセス レビューを作成して、ユーザーに付与されたロールの確認を管理者に依頼する必要があります。 このドキュメントでは、Azure Active Directory (Azure AD) Privileged Identity Management (PIM) でアクセス レビューを開始する手順を説明します。

## <a name="start-an-access-review"></a>アクセス レビューを開始する
> [!NOTE]
> Azure Portal のダッシュボードに PIM アプリケーションをまだ追加していない場合は、「[Azure Privileged Identity Management の使用](pim-getting-started.md)」の手順を参照してください。
> 
> 

PIM アプリケーションのメイン ページには、アクセス レビューを開始する方法が 3 つあります。

* **[アクセス レビュー]** > **[追加]**
* **[ロール]** > **[レビュー]**
* 確認する特定のロールをロールの一覧から選択し、**[レビュー]** をクリックする

**[レビュー]** をクリックすると、**[アクセス レビューを開始する]** ブレードが表示されます。 このブレードでは、名前と時間制限を使用してレビューを構成し、確認するロールを選択し、レビューを実行するユーザーを決定します。

![アクセス レビューを開始する - スクリーン ショット](./media/pim-how-to-start-security-review/PIM_start_review.png)

### <a name="configure-the-review"></a>レビューを構成する
アクセス レビューを作成するには、名前を付け、開始日と終了日を設定する必要があります。

![レビューを構成する - スクリーン ショット](./media/pim-how-to-start-security-review/PIM_review_configure.png)

ユーザーが完了するための十分なレビューの期間の長さを作成します。 終了日の前に完了したら、常にレビューを早く停止できます。

### <a name="choose-a-role-to-review"></a>確認するロールを選択する
各レビューでは、1 つのロールに重点を置いています。 特定のロールのブレードからアクセス レビューを開始した場合を除き、ここでロールを選択する必要があります。

1.  **[役割メンバーシップをレビューする]**
   
    ![ロール メンバーシップを確認する - スクリーン ショット](./media/pim-how-to-start-security-review/PIM_review_role.png)
2. 一覧から 1 つのロールを選択します。

### <a name="decide-who-will-perform-the-review"></a>レビューを実行するユーザを決定する
レビューを実行するための 3 つのオプションがあります。 他のユーザーにレビューを割り当てて完了してもらうか、自分でレビューするか、または各ユーザーが自身のアクセスを確認できます。

1.  **[レビュー担当者の選択]**
   
    ![レビュー担当者を選択する - スクリーン ショット](./media/pim-how-to-start-security-review/PIM_review_reviewers.png)
2. いずれかのオプションを選択してください。
   
   * **レビュー担当者の選択**:アクセスする必要があるユーザーがわからない場合は、このオプションを使用します。 このオプションでは、リソース所有者またはグループ マネージャーにレビューを割り当て、完了してもらうことができます。
   * **Me (自分)**:アクセス レビューが機能する方法をプレビューするか、できないユーザーの代わりに確認する場合に役に立ちます。
   * **メンバーによる自己レビュー**:ユーザーに自分のロール割り当てを確認してもらう場合は、このオプションを使用します。

### <a name="start-the-review"></a>レビューを開始する
最後に、アクセスを承認する場合にユーザーが理由を提供することを要求するオプションがあります。 必要に応じて、レビューの説明を追加し、 **[開始]** を選択します。

待機しているアクセス レビューがあることをユーザーに通知し、 [アクセス レビューを実行する方法](pim-how-to-perform-security-review.md)を示します。

## <a name="manage-the-access-review"></a>アクセスレビューを管理する
レビュー担当者が Azure AD PIM ダッシュボードでレビューを完了すると、[アクセス レビュー] セクションで進行状況を追跡できます。 ディレクトリのアクセス権は、 [レビューが完了する](pim-how-to-complete-review.md)まで変更されません。

レビュー期間が終了するまで、レビューの完了をユーザーに通知するか、または [アクセス レビュー] セクションからレビューを早めに停止することができます。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>次の手順

- [PIM で Azure AD ロールのアクセス レビューを完了する](pim-how-to-complete-review.md)
- [PIM で自分の Azure AD ロールのアクセス レビューを実行する](pim-how-to-perform-security-review.md)
- [PIM で Azure リソース ロールのアクセス レビューを開始する](pim-resource-roles-start-access-review.md)
