---
title: アクセス レビューでグループまたはアプリケーションに対するアクセスをレビューする - Azure Active Directory | Microsoft Docs
description: Azure Active Directory アクセス レビューで、グループ メンバーのアクセスまたはアプリケーション アクセスをレビューする方法について説明します。
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
ms.openlocfilehash: e51ad8faecd935ea999b1287e542e4b14d55290a
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2019
ms.locfileid: "71948240"
---
# <a name="review-access-to-groups-or-applications-in-azure-ad-access-reviews"></a>Azure AD アクセス レビューでグループまたはアプリケーションに対するアクセスをレビューする

Azure Active Directory (Azure AD) には、Azure AD をはじめとする Microsoft オンライン サービス内のグループやアプリケーションへのアクセス管理を省力化する Azure AD アクセス レビューという機能が備わっています。

この記事では、アプリケーションにアクセスするグループのメンバーやユーザーに対するアクセス レビューを専用のレビュアーで実行する方法について説明します。

## <a name="open-the-access-review"></a>アクセス レビューを開く

アクセス レビューを実行する最初の手順は、アクセス レビューを見つけて開くことです。

1. アクセス レビューを実行するように求める Microsoft からのメールを見つけます。 グループに対するアクセスをレビューするように求めるメールの例を次に示します。

    ![グループに対するアクセスをレビューするための Microsoft からのメールの例](./media/perform-access-review/access-review-email.png)

1. **[レビューの開始]** リンクをクリックして、アクセス レビューを開きます。

メールが届いていない場合は、次の手順で保留中のアクセス レビューを見つけることができます。

1. [https://myapps.microsoft.com](https://myapps.microsoft.com) から、MyApps ポータルにサインインします。

    ![アクセス許可があるアプリの一覧を示す MyApps ポータル](./media/perform-access-review/myapps-access-panel.png)

1. ページの右上隅にあるユーザー アイコンをクリックすると、ユーザーの名前と既定の組織が表示されます。 複数の組織が一覧表示されている場合は、アクセス レビューの依頼元の組織を選択してください。

1. **[アクセス レビュー]** タイルをクリックすると、保留中のアクセス レビューの一覧が表示されます。

    このタイルが表示されない場合は、その組織に対して実施するアクセス レビューがないので、現時点で必要な対応はありません。

    ![アプリとグループについての保留中のアクセス レビューの一覧](./media/perform-access-review/access-reviews-list.png)

1. 実行するアクセス レビューの **[レビューの開始]** リンクをクリックします。

## <a name="perform-the-access-review"></a>アクセス レビューを実行する

アクセス レビューを開くと、レビューする必要のあるユーザーの名前が表示されます。

自分のアクセスをレビューするように要求した場合は、ページの外観が異なります。 詳細については、「[グループまたはアプリケーションへの自分のアクセス権のレビュー](review-your-access.md)」を参照してください。

![レビューする必要があるユーザーの一覧を示す、開かれたアクセス レビュー](./media/perform-access-review/perform-access-review.png)

2 つの方法でアクセスを承認または拒否できます。

- 1 名以上のユーザーのアクセスを承認するか拒否できます。または、
- システムの推奨事項に同意することができます。これは最も簡単で手早い方法です。

### <a name="approve-or-deny-access-for-one-or-more-users"></a>1 名以上のユーザーのアクセスを承認するか拒否する

1. ユーザーのリストをレビューして、ユーザーに継続してアクセスを承認するか拒否するかを決定します。

1. 単一のユーザーのアクセスを承認または拒否するには、行をクリックしてウィンドウを開き、実行するアクションを指定します。 複数のユーザーのアクセスを承認または拒否するには、ユーザーの横にチェック マークを追加した後、 **[Review X user(s)]\(X 名のユーザーのレビュー\)** ボタンをクリックしてウィンドウを開き、実行するアクションを指定します。

1. **[承認]** または **[拒否]** をクリックします。 不明な場合は、 **[不明]** をクリックできます。 これを行うと、ユーザーは自分のアクセスを維持できますが、その選択が監査ログに反映されます。

    ![[承認]、[拒否]、および [不明] のオプションを含むアクション ウィンドウ](./media/perform-access-review/approve-deny.png)

1. 必要に応じて、 **[理由]** ボックスに理由を入力します。

    アクセスやグループ メンバーシップの継続を承認する理由を説明するように、アクセス レビューの管理者から要求される場合があります。

1. 実行するアクションを指定し終えたら、 **[保存]** をクリックします。

    応答内容を変更する場合は、その行を選択して、応答内容を更新します。 たとえば、一度拒否したユーザーを承認したり、一度承認したユーザーを拒否したりできます。 応答内容は、アクセス レビューが終了するまでいつでも変更できます。

    レビュー担当者が複数いる場合、最後に送信された応答内容が記録されます。 管理者が Alice と Bob という 2 人のレビュー担当者を指名した場合を考えてみましょう。 最初に Alice がアクセス レビューを開いて、アクセスを承認します。 レビューが終了する前に、Bob がアクセス レビューを開き、アクセスを拒否します。 この場合、最後の [拒否] という応答内容が記録されます。

    > [!NOTE]
    > ユーザーは、アクセスを拒否されても、すぐに削除されるわけではありません。 レビューが終了したか、管理者がレビューを停止したときに削除されます。

### <a name="approve-or-deny-access-based-on-recommendations"></a>推奨事項に基づいてアクセスを承認または拒否する

アクセス レビューを簡単に手早くできるように、1 回のクリックで承認できる推奨事項も用意されています。 推奨事項は、ユーザーのサインイン アクティビティに基づいて生成されます。

1. ページの下部にある青色のバーで、 **[推奨事項の承認]** をクリックします。

    ![[推奨事項の承認] ボタン表示する、開かれたアクセス レビューの一覧](./media/perform-access-review/accept-recommendations.png)

    推奨されているアクションの概要が表示されます。

    ![推奨されているアクションの概要を表示するウィンドウ](./media/perform-access-review/accept-recommendations-summary.png)

1. **[OK]** をクリックして、推奨事項を承認します。

## <a name="next-steps"></a>次の手順

- [グループまたはアプリケーションに対するアクセス レビューを完了する](complete-access-review.md)
