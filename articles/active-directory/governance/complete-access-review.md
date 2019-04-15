---
title: グループまたはアプリケーションのアクセス レビューを入力する - Azure Active Directory | Microsoft Docs
description: グループ メンバーまたはアプリケーション アクセスのアクセス レビューを Azure Active Directory アクセス レビューに入力する方法について説明します。
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
ms.date: 05/02/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4265a7e08eab079e55ce91b27142ec3e55b3f3e9
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2019
ms.locfileid: "58579599"
---
# <a name="complete-an-access-review-of-groups-or-applications-in-azure-ad-access-reviews"></a>グループまたはアプリケーションのアクセス レビューを Azure AD アクセス レビューに入力する

管理者は、アプリケーションに割り当てられているユーザーまたはグループのメンバーに対する[アクセス レビューを Azure Active Directory (Azure AD) を使用して作成](create-access-review.md)することができます。 レビュー担当者には、アクセス権をレビューするよう促すメールが Azure AD から自動的に送信されます。 メールが届かなかったユーザーには、[グループまたはアプリケーションに対するアクセス権のレビュー](perform-access-review.md)に関するページの手順を送信できます。 (レビュー担当者として割り当てられているが招待状を受け取っていないゲストは、レビューの前にまず招待状を受け取る必要があるため、アクセス レビューから電子メールが送信されません) アクセス レビュー期間が過ぎるか、管理者がアクセス レビューを停止したら、この記事の手順に従って、その結果を確認したうえで適用します。

## <a name="view-an-access-review-in-the-azure-portal"></a>Azure Portal でのアクセス レビューの確認

1. [アクセス レビュー ページ](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)に移動して **[プログラム]** を選択し、アクセス レビュー コントロールが含まれたプログラムを選択します。

2. **[管理]** を選択してアクセス レビュー コントロールを選択します。 選択したプログラムにコントロールが多数存在する場合は、特定の種類のコントロールだけをフィルターで抽出したり、コントロールの状態で並べ替えたりすることができます。 アクセス レビュー コントロールの名前またはそれを作成した所有者の表示名で検索することも可能です。 

## <a name="stop-a-review-that-hasnt-finished"></a>未完了のレビューの停止

レビューが終了予定日に達していない場合に、管理者は **[停止]** を選択することで、予定よりも早くレビューを終了することができます。 停止後は、ユーザーをレビューできなくなります。 レビューを停止後に再開することはできません。

## <a name="apply-the-changes"></a>変更の適用 

レビューの自動適用が構成されていない場合は、期限に達したことによって、または管理者が手動で停止したことによってアクセス レビューが完了した後に、**[適用]** を選択して手動で変更を適用できます。 レビューの結果が適用されて、グループまたはアプリケーションが更新されます。 レビューでアクセス権が拒否されたユーザーについては、管理者がこのオプションを選択すると、そのメンバーシップまたはアプリケーションへの割り当てが Azure AD によって削除されます。 

レビューの自動適用が構成されている場合は、アクセス レビューが完了した後に、レビューの状態が、[完了] から、中間の状態を経て、最終的に [適用済み] に変更されます。 拒否されたユーザーが存在する場合は、数分以内にリソース グループ メンバーシップまたはアプリ割り当てから削除されます。

オンプレミス ディレクトリに由来するグループ (動的グループ) は、レビューの自動適用を構成したり、**[適用]** を選択したりしても影響を受けません。 オンプレミス ディレクトリに由来したグループに変更を加える必要がある場合は、結果をダウンロードし、そのディレクトリ内の対応するグループに対して必要な変更を適用してください。

## <a name="download-the-results-of-the-review"></a>レビューの結果のダウンロード

レビューの結果を取得するには、**[承認]** を選択した後で **[ダウンロード]** を選択します。 結果の CSV ファイルは、Excel など、UTF-8 でエンコードされた CSV ファイルを開くことができるプログラムで閲覧できます。

## <a name="optional-delete-a-review"></a>省略可能:レビューの削除
そのレビューが今後必要なければ、削除してかまいません。 **[削除]** を選択すると、Azure AD からレビューが削除されます。

> [!IMPORTANT]
> 削除する前に警告は表示されないため、そのレビューを削除してよいことを確認してください。
> 
> 

## <a name="next-steps"></a>次の手順

- [Azure AD のアクセス レビューでユーザー アクセスを管理する](manage-user-access-with-access-reviews.md)
- [Azure AD のアクセス レビューでゲスト アクセスを管理する](manage-guest-access-with-access-reviews.md)
- [Azure AD アクセス レビューに使用するプログラムとコントロールを管理する](manage-programs-controls.md)
- [グループまたはアプリケーションのアクセス レビューを作成する](create-access-review.md)
- [Azure AD 管理者ロールに含まれるユーザーのアクセス レビューを作成する](../privileged-identity-management/pim-how-to-start-security-review.md)
