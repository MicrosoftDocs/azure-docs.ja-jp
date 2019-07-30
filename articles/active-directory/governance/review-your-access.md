---
title: アクセス レビューでグループまたはアプリケーションに対する自分のアクセスをレビューする - Azure Active Directory | Microsoft Docs
description: グループまたはアプリケーションに対する自分のアクセスを Azure Active Directory アクセス レビューでレビューする方法を学習します。
services: active-directory
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/21/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4b48e035476e5381104b442f87dcac03ac11778
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499670"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Azure AD アクセス レビューでグループまたはアプリケーションに対する自分のアクセスをレビューする

企業で Azure AD アクセス レビューという機能を使用して、Azure AD およびその他の Microsoft Online Services 内のグループまたはアプリケーションへのアクセスを管理する方法が、Azure Active Directory (Azure AD) により簡素化されます。

この記事では、グループまたはアプリケーションに対する自分のアクセスをレビューする方法を説明します。

## <a name="prerequisites"></a>前提条件

- Azure AD Premium P2

詳細については、[ライセンスが必要なユーザー](access-reviews-overview.md#which-users-must-have-licenses)に関する記事をご覧ください。

## <a name="open-the-access-review"></a>アクセス レビューを開く

アクセス レビューを実行する最初の手順は、アクセス レビューを見つけて開くことです。

1. アクセス レビューを実行するように求める Microsoft からのメールを見つけます。 グループに対する自分のアクセスをレビューするように求めるメールの例を次に示します。

    ![グループに対する自分のアクセスをレビューするための Microsoft からのメールの例](./media/review-your-access/access-review-email.png)

1. **[アクセスのレビュー]** リンクをクリックして、アクセス レビューを開きます。

メールが届いていない場合は、次の手順に従って、保留中のアクセス レビューを見つけることができます。

1. [https://myapps.microsoft.com](https://myapps.microsoft.com) から、MyApps ポータルにサインインします。

    ![アクセス許可があるアプリの一覧を示す MyApps ポータル](./media/review-your-access/myapps-access-panel.png)

1. ページの右上隅にあるユーザー アイコンをクリックすると、ユーザーの名前と既定の組織が表示されます。 複数の組織が一覧表示されている場合は、アクセス レビューの依頼元の組織を選択してください。

1. ページの右側にある **[アクセス レビュー]** タイルをクリックすると、保留中のアクセス レビューのリストが表示されます。

    このタイルが表示されない場合は、その組織に対して実施するアクセス レビューがないので、現時点で必要な対応はありません。

    ![アプリとグループについての保留中のアクセス レビューの一覧](./media/review-your-access/access-reviews-list.png)

1. 実行するアクセス レビューの **[レビューの開始]** リンクをクリックします。

## <a name="perform-the-access-review"></a>アクセス レビューを実行する

アクセス レビューを開いたら、自分のアクセスを確認できます。

1. 自分のアクセスをレビューし、アクセスが引き続き必要かどうかを判断します。

    他のユーザーのアクセスをレビューするように要求した場合は、ページの外観が異なります。 詳細については、[グループまたはアプリケーションに対するアクセスのレビュー](perform-access-review.md)に関するページを参照してください。

    ![グループへのアクセスが引き続き必要かどうかを確認する、開かれたアクセス レビュー](./media/review-your-access/perform-access-review.png)

1. **[はい]** をクリックしてアクセスを維持するか、 **[いいえ]** をクリックしてアクセスを削除します。

1. **[はい]** をクリックする場合は、 **[理由]** ボックスに正当性を指定する必要がある場合があります。

    ![グループへのアクセスが引き続き必要かどうかを確認する、完了したアクセス レビュー](./media/review-your-access/perform-access-review-submit.png)

1. **[送信]** をクリックします。

    選択内容が送信され、MyApps ポータルに戻ります。

    応答内容を変更する場合は、アクセス レビュー ページをもう一度開き、応答内容を更新します。 応答内容は、アクセス レビューが終了するまでいつでも変更できます。

    > [!NOTE]
    > アクセスが不要になったことを指定した場合、すぐには削除されません。 レビューが終了されたか、管理者がレビューを停止したときに削除されます。

## <a name="next-steps"></a>次の手順

- [グループまたはアプリケーションに対するアクセス レビューを完了する](complete-access-review.md)
