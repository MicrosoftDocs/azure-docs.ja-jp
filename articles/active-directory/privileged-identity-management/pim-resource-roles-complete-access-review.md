---
title: PIM で Azure リソース ロールのアクセス レビューを終了する - Azure AD | Microsoft Docs
description: Azure Active Directory の Privileged Identity Management (PIM) で Azure リソース ロールのアクセス レビューを完了する方法を説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1601c9fc750df1f8e18bdaa072ede1b5ee164329
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84743815"
---
# <a name="complete-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Privileged Identity Management で Azure リソース ロールのアクセス レビューを完了する

[アクセス レビューが開始](pim-resource-roles-start-access-review.md)されると、特権ロール管理者は特権アクセスの状況を確認できるようになります。 Azure Active Directory (Azure AD) の Privileged Identity Management (PIM) によって、ユーザーに自分のアクセスをレビューするよう求めるメールが自動的に送信されます。 電子メールが届かなかったユーザーがいる場合は、[アクセス レビューを実行する方法](pim-resource-roles-perform-access-review.md)に関する手順を送信できます。

アクセス レビューの期間が終わった後、またはすべてのユーザーが自己レビューを完了した後に、この記事に記載されている手順に従って、レビューを管理し、結果を表示することができます。

## <a name="manage-access-reviews"></a>アクセス レビューを管理する

1. [Azure ポータル](https://portal.azure.com/)にアクセスします。 ダッシュボードで、**Azure リソース** サービスを選択します。

2. リソースを選択します。

3. ダッシュボードの **[アクセス レビュー]** セクションをクリックします。

    ![Azure リソース - ロール、所有者、開始日、終了日、状態を示すアクセス レビューの一覧](media/pim-resource-roles-complete-access-review/rbac-access-review-home-list.png)

4. 管理するアクセス レビューを選択します。

アクセス レビューの詳細ページには、このレビューを管理するためのオプションが多数あります。 次のようなオプションがあります。

![レビューを管理するオプション - 停止、リセット、適用、削除](media/pim-resource-roles-complete-access-review/rbac-access-review-menu.png)

### <a name="stop"></a>Stop

すべてのアクセス レビューには終了日があります。 **[停止]** を選択して早期に終了します。 この時点でレビューを完了していないユーザーは、レビューを停止した後に完了することはできません。 レビューを停止した後に再開することはできません。

### <a name="reset"></a>Reset

アクセス レビューをリセットして、それに対して行われたすべての決定を削除できます。 アクセス レビューをリセットすると、すべてのユーザーは、再度レビューを行っていないユーザーとしてマークされます。

### <a name="apply"></a>適用

アクセス レビューを完了したら、**[適用]** を選択して、レビューの結果を実装します。 レビューでユーザーのアクセスが拒否された場合は、この手順によりそのユーザーのロール割り当てが削除されます。  

### <a name="delete"></a>削除

そのレビューが今後も必要なければ、削除します。 **[削除]** を選択して、Privileged Identity Management サービスからレビューを削除します。

## <a name="results"></a>結果

**[結果]** ページで、レビュー結果の一覧の表示とダウンロードを行います。

![ユーザー、結果、理由、レビュー担当者、適用元、適用結果を一覧する [結果] ページ](media/pim-resource-roles-complete-access-review/rbac-access-review-results.png)

## <a name="reviewers"></a>レビュー担当者

既存のアクセス レビューの表示とレビュー担当者の追加を行います。 レビュー担当者にレビューを完了するように伝えます。

![名前とユーザー プリンシパル名を一覧する [レビュー担当者] ページ](media/pim-resource-roles-complete-access-review/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で Azure リソース ロールのアクセス レビューを開始する](pim-resource-roles-start-access-review.md)
- [Privileged Identity Management で Azureリソース ロールのアクセス レビューを実行する](pim-resource-roles-perform-access-review.md)
