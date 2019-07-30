---
title: グループまたはアプリケーションのアクセス レビューを入力する - Azure Active Directory | Microsoft Docs
description: グループ メンバーまたはアプリケーション アクセスのアクセス レビューを Azure Active Directory アクセス レビューに入力する方法について説明します。
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97c405032368ffd06f5808bc4518302d2f6d66b9
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489156"
---
# <a name="complete-an-access-review-of-groups-or-applications-in-azure-ad-access-reviews"></a>グループまたはアプリケーションのアクセス レビューを Azure AD アクセス レビューに入力する

管理者が[グループまたはアプリケーションに対するアクセス レビューを作成](create-access-review.md)し、レビュー担当者が[アクセス レビューを実行](perform-access-review.md)します。 この記事では、アクセス レビューの結果を確認し、その結果を適用する方法について説明します。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>前提条件

- Azure AD Premium P2
- グローバル管理者、ユーザー管理者、セキュリティ管理者、またはセキュリティ閲覧者

詳細については、[ライセンスが必要なユーザー](access-reviews-overview.md#which-users-must-have-licenses)に関する記事をご覧ください。

## <a name="view-an-access-review"></a>アクセス レビューの確認

レビュー担当者がレビューを完了したら、その進行状況を追跡できます。

1. Azure portal にサインインして、[[Identity Governance] ページ](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)を開きます。

1. 左側のメニューで **[アクセス レビュー]** をクリックします。

1. 一覧で、アクセス レビューをクリックします。

    アクセス レビューの系列を表示するには、アクセス レビューに移動します。スケジュール済みのレビューに、今後予定されている実行が表示されます。

    **[概要]** ページで、進行状況を確認できます。 ディレクトリのアクセス権は、レビューが完了するまで変更されません。

    ![アクセス レビューの進捗状況](./media/complete-access-review/overview-progress.png)

1. 終了予定日の前にアクセス レビューを停止する場合は、 **[停止]** をクリックします。

    レビューを停止すると、レビュー担当者が応答を提供できなくなります。 レビューを停止後に再開することはできません。

1. そのアクセス レビューが今後必要ない場合は、 **[削除]** をクリックして削除できます。

## <a name="apply-the-changes"></a>変更の適用

**[リソースへの結果の自動適用]** が有効の場合は、 **[設定完了時]** での選択に基づいて、レビューの終了日の後、またはレビューを手動で停止したときに自動適用が実行されます。

**[リソースへの結果の自動適用]** がレビューに対して有効になっていない場合は、 **[適用]** をクリックして手動で変更を適用します。 レビューでアクセス権が拒否されたユーザーについては、 **[適用]** をクリックすると、そのメンバーシップまたはアプリケーションへの割り当てが Azure AD によって削除されます。

![アクセス レビューの変更の適用](./media/complete-access-review/apply-changes.png)

レビューの状態は、 **[完了]** から **[適用中]** などの中間状態を経由して、最終的には **[結果を適用済み]** 状態になります。 拒否されたユーザーが存在する場合は、それらのユーザーが数分以内にグループ メンバーシップまたはアプリケーション割り当てから削除されることを確認できます。

オンプレミス ディレクトリに由来するグループ (動的グループ) は、レビューの自動適用を構成したり、 **[適用]** を選択したりしても影響を受けません。 オンプレミス ディレクトリに由来したグループに変更を加える必要がある場合は、結果をダウンロードし、そのディレクトリ内の対応するグループに対して必要な変更を適用してください。

## <a name="retrieve-the-results"></a>結果の取得

1 回限りのアクセス レビューの結果を取得するには、 **[結果]** ページをクリックします。 ユーザーのアクセスのみを表示するには、[検索] ボックスに、レビューされたアクセスのユーザーの表示名またはユーザー プリンシパル名を入力します。

![アクセス レビューの結果を取得](./media/complete-access-review/retrieve-results.png)

定期的に実行されているアクティブなアクセス レビューの進行状況を表示するには、 **[結果]** ページをクリックします。

定期的に実行されているアクセス レビューのうち、完了しているインスタンスの結果を表示するには、 **[レビューの履歴]** をクリックし、完了しているアクセス レビューのインスタンス一覧から、特定のインスタンスをその開始日と終了日に基づいて選択します。 このインスタンスの結果は、 **[結果]** ページから取得できます。

アクセス レビューの結果をすべて取得するには、 **[ダウンロード]** をクリックします。 結果の CSV ファイルは、Excel など、UTF-8 でエンコードされた CSV ファイルを開くことができるプログラムで閲覧できます。

## <a name="remove-users-from-an-access-review"></a>アクセス レビューからのユーザーの削除

 既定では、削除されたユーザーが Azure AD に 30 日間、削除状態で維持されます。その間は、管理者が必要に応じて復元することができます。  30 日後、そのユーザーは完全に削除されます。  その期間に達していなくても、全体管理者は、Azure Active Directory ポータルを使用して明示的に、[最近削除したユーザーを完全に削除](../fundamentals/active-directory-users-restore.md)することができます。  ユーザーが完全に削除されると、以降そのユーザーに関するデータは、アクティブなアクセス レビューから削除されます。  監査ログには、削除済みユーザーに関する監査情報が維持されます。

## <a name="next-steps"></a>次の手順

- [Azure AD のアクセス レビューでユーザー アクセスを管理する](manage-user-access-with-access-reviews.md)
- [Azure AD のアクセス レビューでゲスト アクセスを管理する](manage-guest-access-with-access-reviews.md)
- [グループまたはアプリケーションのアクセス レビューを作成する](create-access-review.md)
- [Azure AD 管理者ロールに含まれるユーザーのアクセス レビューを作成する](../privileged-identity-management/pim-how-to-start-security-review.md)
