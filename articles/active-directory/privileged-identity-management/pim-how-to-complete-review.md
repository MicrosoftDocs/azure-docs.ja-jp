---
title: PIM で Azure AD ロールのアクセス レビューを完了する - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure AD ロールのアクセス レビューを完了して結果を表示する方法について説明します
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/06/2017
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e50ccc208219896e89bcc80f40c846f69c759f9b
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804405"
---
# <a name="complete-an-access-review-of-azure-ad-roles-in-pim"></a>PIM で Azure AD ロールのアクセス レビューを完了する
[アクセス レビューが開始](pim-how-to-start-security-review.md)されると、特権ロール管理者は特権アクセスの状況を確認できるようになります。 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) によって、ユーザーに自分のアクセスをレビューするよう求めるメールが自動的に送信されます。 電子メールが届かなかったユーザーがいる場合は、[アクセス レビューを実行する方法](pim-how-to-perform-security-review.md)に関する手順を送信できます。

アクセス レビューの期間が終わった後、またはすべてのユーザーが自己レビューを完了した後に、この記事に記載されている手順に従って、レビューを管理し、結果を表示することができます。

## <a name="manage-access-reviews"></a>アクセス レビューを管理する
1. [Azure Portal](https://portal.azure.com/) に移動し、ダッシュボードで **Azure AD Privileged Identity Management** アプリケーションを選択します。
2. ダッシュボードの **[アクセス レビュー]** セクションをクリックします。
3. 管理するアクセス レビューを選択します。

アクセス レビューの [詳細] ブレードには、このレビューを管理するためのオプションが多数あります。

![PIM アクセス レビューのボタン - スクリーンショット](./media/pim-how-to-complete-review/review-buttons.png)

### <a name="remind"></a>リマインダーを送信
ユーザーが各自をレビューできるようにアクセス レビューが設定された場合、 **[リマインダーを送信]** をクリックすると通知が送信されます。 

### <a name="stop"></a>Stop
すべてのアクセス レビューには終了日が設定されていますが、 **[停止]** ボタンを使用するとレビューを早期に終了することができます。 この時点までにレビューを行っていないユーザーは、レビューが停止されるとレビューを行うことができなくなります。 レビューを停止後に再開することはできません。

### <a name="apply"></a>適用
終了日に達するか手動で停止されたためにアクセス レビューが完了した後で、 **[適用]** ボタンをクリックすると、レビューの結果が適用されます。 レビューでユーザーのアクセスが拒否された場合は、この手順によりそのユーザーのロール割り当てが削除されます。  

### <a name="export"></a>エクスポート
アクセス レビューの結果を手動で適用する場合は、レビューをエクスポートできます。 **[エクスポート]** ボタンをクリックすると、CSV ファイルのダウンロードが開始されます。 Excel など、CSV ファイルを開くことができるプログラムで結果を管理できます。

### <a name="delete"></a>削除
そのレビューが今後も必要なければ、削除します。 **[削除]** ボタンをクリックすると、レビューが PIM アプリケーションから削除されます。

> [!IMPORTANT]
> 削除する前に警告は表示されないため、そのレビューを削除してよいのか確認してください。 

## <a name="next-steps"></a>次の手順

- [PIM で Azure AD ロールのアクセス レビューを開始する](pim-how-to-start-security-review.md)
- [PIM で自分の Azure AD ロールのアクセス レビューを実行する](pim-how-to-perform-security-review.md)
