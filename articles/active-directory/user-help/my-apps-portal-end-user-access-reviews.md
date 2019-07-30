---
title: My Apps ポータルからアクセス レビューを実行する - Azure Active Directory| Microsoft Docs
description: 組織のアプリおよびグループのセキュリティ アクセスを表示および管理する方法について説明します。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6681720fc7c0fe04c99bf7957be14865c663dd67
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383151"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>My Apps ポータルからアクセス レビューを実行する

Web ベースの**マイ アプリ** ポータルでは、職場または学校アカウントを使用して、社内にあるクラウドベースのアプリを表示して起動したり、一部のプロフィール情報とアカウント情報を更新したり、**グループ**情報を確認したり、アプリとグループの**アクセス レビュー**を実行したりすることができます。 **マイ アプリ** ポータルにアクセスできない場合は、ヘルプデスクに連絡してアクセス許可を得る必要があります。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>このコンテンツはユーザーを対象としています。 管理者の方は、「[アプリケーション管理のドキュメント](https://docs.microsoft.com/azure/active-directory/manage-apps)」で、クラウドベースのアプリの設定と管理の方法を詳しくご覧いただけます。

## <a name="manage-access-reviews"></a>アクセス レビューを管理する

自身のアクセス レビューを実行するアクセス許可を管理者から与えられている場合は、**マイ アプリ** ポータル ページの **[アクセス レビュー]** タイルから、グループまたはアプリ アクセスを管理できます。

>[!Note]
>**[アクセス レビュー]** タイルが表示されない場合は、アクセス レビューを実行するアクセス許可がないか、承認を待っている保留中のレビューがないことを意味します。 タイルへのアクセス権が必要な場合は、ヘルプ デスクに問い合わせてください。

### <a name="to-perform-your-access-reviews"></a>自分のアクセス レビューを実行するには

1. 職場または学校のアカウントにサインインします。

2. Web ブラウザーを開いて https://myapps.microsoft.com に移動するか、組織から与えられたリンクを使用します。 たとえば、 https://myapps.microsoft.com/contoso.com など、組織のカスタマイズされたページに転送されることがあります。

    **[アプリ]** ページが表示され、組織が所有し、お客様が利用できるクラウドベースのアプリがすべて示されます。

    ![マイ アプリ ポータルの [アプリ] ページ](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. **[アクセス レビュー]** タイルを選択して、承認を待機しているアクセス レビューの一覧を表示します。

    ![保留中の組織のアクセス レビューを示した [アクセス レビュー] ページ](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. **[レビューの開始]** を選択してアクセス レビューを開始します。

5. アクセスをレビューし、まだ必要かどうかを判断します。

    ![[レビューの開始] ページ、レビューの詳細を表示](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >管理者であり、グループまたはアプリへの組織のアクセスをレビューできる場合は、別のページが表示されます。 組織のグループまたはアプリのレビューの詳細については、[Azure AD アクセス レビューでのグループまたはアプリケーションへのアクセスのレビュー](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review)に関するページを参照してください。

6. **[はい]** を選択してアクセスを維持するか、 **[いいえ]** を選択してアクセスを削除します。

    **[はい]** を選択する場合は、 **[理由]** ボックスに正当性を指定する必要がある場合があります。

    ![[アクセス レビュー] ページ、サンプル テキストを含む [理由] ボックスを表示](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. **[Submit]\(送信\)** をクリックします。

    アクセス レビューが完了し、 **[マイ アプリ]** ポータルに戻ります。

    >[!Note]
    >アクセス レビュー期間が終了するまで、いつでもアクセスを変更できます。 アプリまたはグループへのアクセスを削除しても、すぐには削除されません。 削除は、アクセス レビュー期間が終了したとき、または管理者がレビューを閉じたときに行われます。

## <a name="next-steps"></a>次の手順

- [マイ アプリ ポータルのアプリにアクセスして使用する](my-apps-portal-end-user-access.md)。

- [プロファイル情報を変更する](my-apps-portal-end-user-update-profile.md)。

- [グループ関連の情報を表示し、更新する](my-apps-portal-end-user-groups.md)。
