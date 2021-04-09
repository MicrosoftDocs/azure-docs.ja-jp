---
title: Azure AD エンタイトルメント管理でアクセス パッケージのアクセス レビューを作成する
description: Azure Active Directory アクセス レビュー (プレビュー) で、エンタイトルメント管理アクセス パッケージのアクセス レビュー ポリシーを作成する方法について説明します。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 012eefe9140703a62d7bb1074ab763191a0976cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87798513"
---
# <a name="create-an-access-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD エンタイトルメント管理でアクセス パッケージのアクセス レビューを作成する

古くなったアクセスのリスクを軽減するには、Azure AD のエンタイトルメント管理で、アクセス パッケージに対するアクティブな割り当てを持つユーザーの定期的レビューを有効にする必要があります。 レビューを有効にできるのは、新しいアクセス パッケージを作成するとき、または既存のアクセス パッケージを編集するときです。 この記事では、アクセス パッケージのアクセス レビューを有効にする方法について説明します。

## <a name="prerequisites"></a>前提条件

アクセス パッケージのレビューを有効にするには、アクセス パッケージを作成するための前提条件を満たしている必要があります。
- Azure AD Premium P2
- グローバル管理者、ユーザー管理者、カタログ所有者、またはアクセス パッケージ マネージャー

詳細については、「[License requirements ライセンスの要件](entitlement-management-overview.md#license-requirements)」を参照してください。


## <a name="create-an-access-review-of-an-access-package"></a>アクセス パッケージのアクセス レビューを作成する

アクセス レビューを有効にできるのは、[新しいアクセス パッケージの作成時](entitlement-management-access-package-create.md)、または[既存アクセス パッケージの編集時](entitlement-management-access-package-lifecycle-policy.md)です。 次の手順に従って、アクセス パッケージのアクセス レビューを有効にします。

1. アクセス パッケージの **[ライフサイクル]** タブを開き、**[アクセス レビュー]** まで下にスクロールします。

1. **[アクセスレビューが必要]** トグルを **[はい]** に移動します。

    ![アクセス レビューを追加する](./media/entitlement-management-access-reviews/access-reviews-pane.png)

1. **[開始日時]** の横でレビューが開始される日付を指定します。

1. 次に、**[レビュー頻度]** を **[毎年]**、**[半年毎]**、**[四半期に 1 回]**、または **[月 1 回]** に設定します。
この設定は、アクセス レビューを実行する頻度を決定します。

1. **[期間]** を設定して、繰り返しの系列の各レビューでレビュー担当者からの入力を受け付ける日数を定義します。 たとえば、1 月 1 日に始まり、レビューのために 30 日間開いている毎年のレビューをスケジュールして、その月の終わりまでレビュー担当者に対応する時間があるようにします。

1. ユーザーに独自のアクセス レビューを実行させたい場合は、**[レビューアー]** の横にある **[セルフレビュー]** を選択し、レビュー担当者を指定したい場合は、**[特定のレビュー担当者]** を選択します。

    ![レビュー担当者を選択する](./media/entitlement-management-access-reviews/access-reviews-add-reviewer.png)

1. **[特定のレビュー担当者]** を選択した場合は、どのユーザーがアクセス レビューを行うかを指定します。
    1. **[レビュー担当者の追加]** を選択します。
    1. **[レビュー担当者を選択する]** ペインで、レビュー担当者にするユーザーを検索して選択します。
    1. レビュー担当者を選択したら、**[選択]** ボタンをクリックします。

    ![レビュー担当者を指定する](./media/entitlement-management-access-reviews/access-reviews-select-reviewer.png)

1. 新しいアクセス パッケージを作成しようとしている場合は **[確認および作成]** をクリックし、アクセス パッケージを編集しようとしている場合はページ下部の **[更新]** をクリックします。

## <a name="view-the-status-of-the-access-review"></a>アクセス レビューの状態を表示する

開始日の後、アクセス レビューは **[アクセス レビュー]** セクションに一覧表示されます。 アクセス レビューの状態を表示するには、次の手順に従います。

1. **[Identity Governance]** で、**[アクセス パッケージ]** をクリックしてから、アクセス レビューの状態を調べたいアクセス パッケージを選択します。   

1. アクセス パッケージの概要が表示されたら、左側のメニューの **[アクセス レビュー]** をクリックします。
    
    ![アクセス レビューを選択する](./media/entitlement-management-access-reviews/access-review-status-access-package-overview.png)

1. アクセス レビューが関連付けられているすべてのポリシーが含まれる一覧が表示されます。 レビューをクリックし、そのレポートを表示します。

    ![アクセス レビューの一覧](./media/entitlement-management-access-reviews/access-review-status-select-access-reviews.png)
   
1. レポートを表示すると、レビューされたユーザーの数と、レビュー担当者が行ったアクションが表示されます。

    ![レビューの状態を表示する](./media/entitlement-management-access-reviews/access-review-status.png)
 

## <a name="access-reviews-email-notifications"></a>アクセス レビューのメール通知
レビュー担当者を指定することも、ユーザーが自分のアクセスを自分自身で確認することもできます。 既定では、レビューの開始後間もなく、Azure AD からレビュー担当者やセルフ レビュー実施者にメールが送信されます。

このメールには、アクセス パッケージへのアクセスを確認する方法に関する指示が記載されます。 レビューでユーザーが自分のアクセスを確認する場合は、アクセス パッケージのセルフ レビューを実行する方法に関する指示が示されます。
  
ゲスト ユーザーをレビュー担当者として割り当てて、彼らが Azure AD のゲスト招待を承諾していない場合、彼らは Azure AD アクセス レビューからのメールを受信しません。 電子メールを受信する前に、まず招待を受け入れて、Azure AD でアカウントを作成する必要があります。 

## <a name="next-steps"></a>次のステップ

- [アクセス パッケージのアクセスを確認する](entitlement-management-access-reviews-review-access.md)
- [アクセス パッケージのセルフレビュー](entitlement-management-access-reviews-self-review.md)
