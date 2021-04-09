---
title: PIM で Azure AD ロールのアクセス レビューを完了する - Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure AD ロールのアクセス レビューを完了して結果を表示する方法について説明します
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa7e8089fbbf2ee653100a05383fdbdc877ffda4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84742218"
---
# <a name="complete-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management で Azure AD ロールのアクセス レビューを完了する

[アクセス レビューが開始](pim-how-to-start-security-review.md)されると、特権ロール管理者は特権アクセスの状況を確認できるようになります。  Privileged Identity Management (PIM) によって、Azure Active Directory (Azure AD) 組織内のユーザーに自分のアクセスをレビューするよう求めるメールが自動的に送信されます。 電子メールが届かなかったユーザーがいる場合は、[アクセス レビューを実行する方法](pim-how-to-perform-security-review.md)に関する手順を送信できます。

アクセス レビューの期間が終わった後、またはすべてのユーザーが自己レビューを完了した後に、この記事に記載されている手順に従って、レビューを管理し、結果を表示することができます。

## <a name="manage-access-reviews"></a>アクセス レビューを管理する

1. [Azure Portal](https://portal.azure.com/) に移動し、ダッシュボードで **Azure AD Privileged Identity Management** サービスを選択します。
1. ダッシュボードの **[アクセス レビュー]** セクションをクリックします。
1. 管理するアクセス レビューを選択します。

アクセス レビューの [詳細] ブレードには、このレビューを管理するためのオプションが多数あります。

![Privileged Identity Management のアクセス レビュー ボタン - スクリーンショット](./media/pim-how-to-complete-review/review-buttons.png)

### <a name="remind"></a>リマインダーを送信

ユーザーが各自をレビューできるようにアクセス レビューが設定された場合、 **[リマインダーを送信]** をクリックすると通知が送信されます。

### <a name="stop"></a>Stop

すべてのアクセス レビューには終了日が設定されていますが、 **[停止]** ボタンを使用するとレビューを早期に終了することができます。 この時点までにレビューを行っていないユーザーは、レビューが停止されるとレビューを行うことができなくなります。 レビューを停止後に再開することはできません。

### <a name="apply"></a>適用

終了日に達するか手動で停止されたためにアクセス レビューが完了した後で、 **[適用]** ボタンをクリックすると、レビューの結果が適用されます。 レビューでユーザーのアクセスが拒否された場合は、この手順によりそのユーザーのロール割り当てが削除されます。  

### <a name="export"></a>エクスポート

アクセス レビューの結果を手動で適用する場合は、レビューをエクスポートできます。 **[エクスポート]** ボタンをクリックすると、CSV ファイルのダウンロードが開始されます。 Excel など、CSV ファイルを開くことができるプログラムで結果を管理できます。

### <a name="delete"></a>削除

そのレビューが今後も必要なければ、削除します。 **[削除]** ボタンは、Privileged Identity Management サービスからレビューを削除します。

> [!IMPORTANT]
> この破壊的な変更を確認する必要はありません。そのレビューを削除する必要があるかどうかを確認してください。

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で Azure AD ロールのアクセス レビューを開始する](pim-how-to-start-security-review.md)
- [Privileged Identity Management で Azure AD ロールのアクセス レビューを実行する](pim-how-to-perform-security-review.md)
