---
title: "アプリケーションにアクセスするユーザーまたはグループのメンバーに対するアクセス レビューを Azure AD で完了する | Microsoft Docs"
description: "アプリケーションにアクセスするユーザーまたはグループのメンバーに対するアクセス レビューを Azure Active Directory で完了する方法について説明します。"
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: e83bc42d658c4d6304f98b99d0e0942595bd65d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="complete-an-access-review-of-members-of-a-group-or-users-access-to-an-application-in-azure-ad"></a>アプリケーションにアクセスするユーザーまたはグループのメンバーに対するアクセス レビューを Azure AD で完了する

管理者は[アクセス レビューを作成](active-directory-azure-ad-controls-create-access-review.md)することによって、アプリケーションに割り当てられているユーザーまたはグループのメンバーに対するレビューを依頼することができます。 レビュー担当者には、アクセス権をレビューするよう促すメールが Azure AD から自動的に送信されます。 メールが届かなかったユーザーには、[アクセス権をレビューする方法](active-directory-azure-ad-controls-perform-access-review.md)に関するページの手順を送信できます。  アクセス レビュー期間が過ぎるか、管理者がアクセス レビューを停止したら、この記事の手順に従って、その結果を確認したうえで適用します。

## <a name="viewing-an-access-review-in-the-azure-portal"></a>Azure Portal でのアクセス レビューの確認

1. [アクセス レビュー ページ](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)に移動して **[プログラム]** タブに切り替え、アクセス レビュー コントロールが含まれたプログラムを選択します。
2. [管理] をクリックしてアクセス レビュー コントロールを選択します。  プログラムにコントロールが多数存在する場合は、特定の種類のコントロールだけをフィルターで抽出したり、コントロールの状態で並べ替えたり、アクセス レビュー コントロールの名前 (またはそれを作成した所有者の表示名) で検索したりすることができます。 

## <a name="stopping-a-review-that-has-not-yet-completed"></a>未完了のレビューの停止

終了予定日に達していなくても、管理者は **[停止]** ボタンをクリックしてレビューを停止することで、予定より早くレビューを打ち切ることができます。  この時点までにレビューを行っていないユーザーは、レビューが停止されるとレビューを行うことができなくなります。 レビューを停止後に再開することはできません。

## <a name="applying-the-changes"></a>変更の適用 

アクセス レビューが終了日に達するか管理者により手動で停止されて完了した後、**[適用]** ボタンをクリックすると、レビューの結果が適用されて、グループまたはアプリケーションが更新されます。 レビューでアクセス権が拒否されたユーザーについては、管理者がこのボタンをクリックすると、そのメンバーシップまたはアプリケーションへの割り当てが Azure AD によって削除されます。 

オンプレミス ディレクトリに由来するグループ (動的グループ) は、[適用] ボタンをクリックしても影響を受けません。  オンプレミス ディレクトリに由来したグループに変更を加える必要がある場合は、結果をダウンロードし、そのディレクトリ内の対応するグループに対して必要な変更を適用してください。

## <a name="downloading-the-results-of-the-review"></a>レビューの結果のダウンロード

レビューの結果を取得するには、[承認] タブに切り替えて **[ダウンロード]** ボタンをクリックします。  結果の CSV ファイルは、Excel など、CSV ファイルを開くことができるプログラムで閲覧できます。

## <a name="optional-deleting-a-review"></a>(省略可) レビューの削除
そのレビューが今後も必要なければ、削除します。 **[削除]** ボタンをクリックすると、Azure AD からレビューが削除されます。

> [!IMPORTANT]
> 削除する前に警告は表示されないため、そのレビューを削除してよいことを確認してください。
> 
> 

## <a name="next-steps"></a>次のステップ

- [Azure AD のアクセス レビューでユーザー アクセスを管理する](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
- [Azure AD のアクセス レビューでゲスト アクセスを管理する](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
- [Azure AD アクセス レビューに使用するプログラムとコントロールを管理する](active-directory-azure-ad-controls-manage-programs-controls.md)
- [グループのメンバーまたはアプリケーションへのアクセスのアクセス レビューを作成する](active-directory-azure-ad-controls-create-access-review.md)
- [Azure AD 管理者ロールに含まれるユーザーのアクセス レビューを作成する](active-directory-privileged-identity-management-how-to-start-security-review.md)
