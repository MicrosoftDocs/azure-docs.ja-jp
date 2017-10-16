---
title: "アクセス権をレビューする方法 | Microsoft Docs"
description: "Azure Active Directory でアクセス権をレビューする方法"
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 7029eb7e4af3bb22dff3d841e28c5b348944cd17
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-review-your-access"></a>アクセス権をレビューする方法

Azure Active Directory には、Azure AD をはじめとする Microsoft Online Services 内のグループのメンバー管理やアプリケーションへのアクセス管理を省力化する "アクセス レビュー" という機能が備わっています。  アプリケーションへのアクセス権が付与されたユーザーまたはグループのメンバーには、各自のアクセス権をレビューするよう求めるメールが Microsoft から送信される場合があります。 

## <a name="opening-an-access-review"></a>アクセス レビューを開く

メールに記載されているリンクをクリックすると、実行待ちのアクセス レビューが表示されます。  メールが届かない場合は、次の操作でアクセス レビューを探すことができます。

1. [Azure AD アクセス パネル](https://myapps.microsoft.com)にサインインします。
2. このページの右上隅にあるユーザー アイコンをクリックすると、自分の名前と既定の組織が表示されます。 複数の組織が一覧表示されている場合は、アクセス レビューの依頼元の組織を選択してください。
3. ページの右側に**アクセス レビュー**という名称のタイルがある場合は、それをクリックします。 このタイルが表示されない場合は、その組織に対して実施するアクセス レビューがないので、現時点で必要な対応はありません。

## <a name="filling-out-an-access-review"></a>アクセス レビューに回答する

一覧からアクセス レビューを選択すると、自分のアクセス権が表示されます。  目的の行をクリックして、アクセス権の継続を承認するか拒否するかを選んでください。

アクセス権の継続を承認することについて、その正当性を説明するよう求められる場合があります。

## <a name="next-steps"></a>次のステップ

アクセス権を拒否してもすぐには削除されないので、回答と承認の内容に変更がある場合は、回答をいったんリセットしてから改めて回答を選択してください。  この作業は、アクセス レビューが完了するまで行うことができます。






