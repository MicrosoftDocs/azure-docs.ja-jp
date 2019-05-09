---
title: アクセス レビューでグループまたはアプリケーションのアクセス レビュー結果を取得する - Azure Active Directory | Microsoft Docs
description: Azure Active Directory アクセス レビューでグループ メンバーまたはアプリケーション アクセスのアクセス レビュー結果を取得する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: eae4bafb3eefcee2785c784030d7be8dde66988e
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578713"
---
# <a name="retrieve-access-review-results-for-groups-or-applications-in-azure-ad-access-reviews"></a>Azure AD アクセス レビューでグループまたはアプリケーションのアクセス レビュー結果を取得する

管理者は、アプリケーションに割り当てられているユーザーまたはグループのメンバーに対する[アクセス レビューを Azure Active Directory (Azure AD) を使用して作成](create-access-review.md)することができます。  **グローバル管理者**、**ユーザー管理者**、**セキュリティ管理者**、または**セキュリティ閲覧者** の各役割に属するユーザーは、アクセス レビューの結果を閲覧することができます。  そのいずれかの役割にユーザーを割り当てるには、特権ロール管理者が Azure AD PIM を使用して、その役割をアクティブにする資格がある人物としてユーザーを設定するか、グローバル管理者が永続的に [ユーザーを役割に割り当て](../fundamentals/active-directory-users-assign-role-azure-portal.md) ます。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="locating-an-access-review"></a>アクセス レビューの検索

どのプログラムにアクセス レビューが含まれているかがわかっている場合は、アクセス レビュー ページに移動して **[プログラム]** を選択し、アクセス レビュー コントロールが含まれたプログラムを選択します。  そのうえで、**[コントロール]** を選択してアクセス レビュー コントロールを選択します。 選択したプログラムにコントロールが多数存在する場合は、特定の種類のコントロールだけをフィルターで抽出したり、コントロールの状態で並べ替えたりすることができます。 アクセス レビュー コントロールの名前またはそれを作成した所有者の表示名で検索することも可能です。 

どのプログラムにアクセス レビューが含まれているかがわからない場合は、アクセス レビュー ページに移動し、**[コントロール]** を選択します。  特定の種類のコントロールだけをフィルターで抽出したり、コントロールの状態で並べ替えたりすることができるほか、アクセス レビュー コントロールの名前またはそれを作成した所有者の表示名で検索することもできます。 

## <a name="retrieving-the-results-for-a-one-time-access-review"></a>1 回限りのアクセス レビューの結果を取得する

レビューの繰り返しの種類が 1 回限りである場合、アクティブなアクセス レビューの進行状況と完了したアクセス レビューの結果は、**[結果]** セクションから得ることができます。  アクセス レビューの対象となるユーザーの表示名またはユーザー プリンシパル名を入力すると、そのユーザーのアクセスだけが表示されます。  完了したアクセス レビューの結果をすべて取得するには、**[ダウンロード]** ボタンをクリックしてください。

## <a name="retrieving-the-results-for-multiple-instances-of-a-recurring-access-review"></a>複数インスタンスから成る定期的アクセス レビューについて結果を取得する

定期的に実行されているアクティブなアクセス レビューの進行状況を表示するには、**[結果]** セクションをクリックします。  アクセス レビューの対象となるユーザーの表示名またはユーザー プリンシパル名を入力してください。

定期的に実行されているアクセス レビューのうち、完了しているインスタンスの結果を表示するには、**[レビューの履歴]** を選択したうえで、完了しているアクセス レビューのインスタンス一覧から、特定のインスタンスをその開始日と終了日に基づいて選択します。   このインスタンスの結果は、**[結果]** セクションから取得できます。  アクセス レビューの対象となるユーザーの表示名またはユーザー プリンシパル名を入力すると、そのユーザーのアクセスだけが表示されます。  定期的に実行されるアクセス レビューの中から完了したインスタンスの結果をすべて取得するには、**[ダウンロード]** ボタンをクリックしてください。


## <a name="removing-users-from-an-access-review"></a>アクセス レビューからユーザーを削除する

既定では、削除されたユーザーが Azure AD に 30 日間、削除状態で維持されます。その間は、管理者が必要に応じて復元することができます。  30 日後、そのユーザーは完全に削除されます。  その期間に達していなくても、全体管理者は、Azure Active Directory ポータルを使用して明示的に、[最近削除したユーザーを完全に削除](../fundamentals/active-directory-users-restore.md)することができます。  ユーザーが完全に削除されると、以降そのユーザーに関するデータは、アクティブなアクセス レビューから削除されます。  監査ログには、削除済みユーザーに関する監査情報が維持されます。

## <a name="next-steps"></a>次の手順

- [Azure AD のアクセス レビューでユーザー アクセスを管理する](manage-user-access-with-access-reviews.md)
- [Azure AD のアクセス レビューでゲスト アクセスを管理する](manage-guest-access-with-access-reviews.md)
- [Azure AD アクセス レビューに使用するプログラムとコントロールを管理する](manage-programs-controls.md)
- [グループまたはアプリケーションのアクセス レビューを作成する](create-access-review.md)
- [Azure AD 管理者ロールに含まれるユーザーのアクセス レビューを作成する](../privileged-identity-management/pim-how-to-start-security-review.md)


