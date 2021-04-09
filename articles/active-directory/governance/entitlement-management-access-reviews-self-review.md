---
title: Azure AD エンタイトルメント管理でのアクセス パッケージのセルフレビュー
description: Azure Active Directory アクセス レビュー (プレビュー) で、エンタイトルメント管理アクセス パッケージのユーザー アクセス権を確認する方法について説明します。
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
ms.openlocfilehash: 31c44f2423cdc5c43638fe2515757bcb11a9814c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87798445"
---
# <a name="self-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD エンタイトルメント管理でのアクセス パッケージのセルフレビュー

企業が Azure AD エンタイトルメント管理を使うと、グループ、アプリケーション、および SharePoint サイトへのアクセスを管理する方法が簡単になります。 この記事では、割り当てられたアクセス パッケージのセルフレビューを行う方法について説明します。

## <a name="open-the-access-review"></a>アクセス レビューを開く

アクセス レビューを行うには、まずアクセス レビューを開く必要があります。 アクセス レビューを見つけて開くには、次の手順を行います。

1. アクセスのレビューを求めるメールが Microsoft から送信される場合があります。 メールを探してアクセス レビューを開きます。 アクセスのレビューを求めるメールの例を次に示します。 
    
    ![アクセス レビューのセルフレビュー担当者のメール](./media/entitlement-management-access-reviews-review-access/self-review-reviewer-email.png)

1. **[アクセスのレビュー]** リンクをクリックします。

1. メールが届かない場合は、https://myaccess.microsoft.com に直接アクセスして保留中のアクセス レビューを見つけることもできます。  (米国政府の場合は、代わりに `https://myaccess.microsoft.us` を使用します)。

1. 左側のナビゲーション バーの **[アクセス レビュー]** をクリックして、割り当てられている保留中のアクセス レビューの一覧を表示します。


1.  開始するレビューをクリックします。

## <a name="perform-the-access-review"></a>アクセス レビューを実行する

アクセス レビューを開くと、自分のアクセス権が表示されます。 アクセス レビューを行うには、次の手順を行います。

1.  アクセス パッケージに引き続きアクセスする必要があるかどうかを判断します。 たとえば、作業中のプロジェクトが完了していない場合、プロジェクトの作業を続行するにはアクセスが必要です。

1.  **[はい]** をクリックしてアクセスを維持するか、 **[いいえ]** をクリックしてアクセスを削除します。
    >[!NOTE]
    >アクセスが不要になったと指定しても、アクセス パッケージからすぐには削除されません。 レビューが終了するか、管理者がレビューを停止すると、アクセス パッケージから削除されます。

1.  **[はい]** をクリックした場合は、必要に応じて **[理由]** ボックスに理由の説明を入力します。

1.  **[送信]** をクリックします。

レビューを終了する前に気が変わって回答を変更する場合は、レビューに戻ることができます。

## <a name="next-steps"></a>次のステップ

- [パッケージにアクセスするためのアクセスを確認する](entitlement-management-access-reviews-review-access.md) 
