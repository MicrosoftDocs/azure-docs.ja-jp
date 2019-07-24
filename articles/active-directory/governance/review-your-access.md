---
title: アクセス レビューでグループまたはアプリケーションに対する自分のアクセスをレビューする - Azure Active Directory | Microsoft Docs
description: グループまたはアプリケーションに対する自分のアクセスを Azure Active Directory アクセス レビューでレビューする方法を学習します。
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 02/20/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3fe2013ff84dd0451fed7d108539606520cb9403
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576311"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Azure AD アクセス レビューでグループまたはアプリケーションに対する自分のアクセスをレビューする

企業で Azure AD アクセス レビューという機能を使用して、Azure AD およびその他の Microsoft Online Services 内のグループまたはアプリケーションへのアクセスを管理する方法が、Azure Active Directory (Azure AD) により簡素化されます。

この記事では、グループまたはアプリケーションに対する自分のアクセスをレビューする方法を説明します。

## <a name="open-the-access-review"></a>アクセス レビューを開く

アクセス レビューを実行する最初の手順は、アクセス レビューを見つけて開くことです。

1. アクセス レビューを実行するように求める Microsoft からのメールを見つけます。 グループに対する自分のアクセスをレビューするように求めるメールの例を次に示します。

    ![アクセスのレビューの電子メール](./media/review-your-access/access-review-email.png)

1. **[アクセスのレビュー]** リンクをクリックして、アクセス レビューを開きます。

メールが届いていない場合は、次の手順に従って、保留中のアクセス レビューを見つけることができます。

1. [https://myapps.microsoft.com](https://myapps.microsoft.com) から、MyApps ポータルにサインインします。

    ![MyApps ポータル](./media/review-your-access/myapps-access-panel.png)

1. ページの右上隅にあるユーザー記号をクリックすると、ユーザーの名前と既定の組織が表示されます。 複数の組織が一覧表示されている場合は、アクセス レビューの依頼元の組織を選択してください。

1. ページの右側にある **[アクセス レビュー]** タイルをクリックすると、保留中のアクセス レビューのリストが表示されます。

    このタイルが表示されない場合は、その組織に対して実施するアクセス レビューがないので、現時点で必要な対応はありません。

    ![アクセス レビューのリスト](./media/review-your-access/access-reviews-list.png)

1. 実行するアクセス レビューの **[レビューの開始]** リンクをクリックします。

## <a name="perform-the-access-review"></a>アクセス レビューを実行する

アクセス レビューを開いたら、自分のアクセスを確認できます。

1. 自分のアクセスをレビューし、アクセスが引き続き必要かどうかを判断します。

    他のユーザーのアクセスをレビューするように要求した場合は、ページの外観が異なります。 詳細については、[グループまたはアプリケーションに対するアクセスのレビュー](perform-access-review.md)に関するページを参照してください。

    ![アクセス レビューを実行する](./media/review-your-access/perform-access-review.png)

1. **[はい]** をクリックしてアクセスを維持するか、**[いいえ]** をクリックしてアクセスを削除します。

1. **[はい]** をクリックする場合は、**[理由]** ボックスに正当性を指定する必要がある場合があります。

    ![アクセス レビューを実行する](./media/review-your-access/perform-access-review-submit.png)

1. **[送信]** をクリックします。

    選択内容が送信され、MyApps ポータルに戻ります。

    応答内容を変更する場合は、アクセス レビュー ページをもう一度開き、応答内容を更新します。 応答内容は、アクセス レビューが終了するまでいつでも変更できます。

    > [!NOTE]
    > アクセスが不要になったことを指定した場合、すぐには削除されません。 レビューが終了されたか、管理者がレビューを停止したときに削除されます。

## <a name="next-steps"></a>次の手順

- [グループまたはアプリケーションに対するアクセス レビューを完了する](complete-access-review.md)
