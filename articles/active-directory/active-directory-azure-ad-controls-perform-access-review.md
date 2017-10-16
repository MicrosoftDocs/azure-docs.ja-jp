---
title: "Azure AD のアクセス レビューを使ってアクセス権をレビューする方法 | Microsoft Docs"
description: "Azure AD のアクセス レビューを使ってアクセス権をレビューする方法。"
services: active-directory
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
ms.openlocfilehash: c9115bb5bc3d22c67ea0ac77b8972ae7f919926b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-review-access-with-azure-ad-access-reviews"></a>Azure AD のアクセス レビューでアクセス権をレビューする方法

Azure Active Directory には、Azure AD をはじめとする Microsoft Online Services 内のグループのメンバー管理やアプリケーションへのアクセス管理を省力化する "アクセス レビュー" という機能が備わっています。  アプリケーションへのアクセス権が付与されたユーザーまたはグループのメンバーには、各自のアクセス権をレビューするよう求めるメールが Microsoft から送信される場合があります。 

## <a name="opening-an-access-review"></a>アクセス レビューを開く

メールに記載されているリンクをクリックすると、実行待ちのアクセス レビューが表示されます。  メールが届かない場合は、次の操作でアクセス レビューを探すことができます。

1. [Azure AD アクセス パネル](https://myapps.microsoft.com)にサインインします。
2. このページの右上隅にあるユーザー アイコンをクリックすると、自分の名前と既定の組織が表示されます。 複数の組織が一覧表示されている場合は、アクセス レビューの依頼元の組織を選択してください。
3. ページの右側に**アクセス レビュー**という名称のタイルがある場合は、それをクリックします。 このタイルが表示されない場合は、その組織に対して実施するアクセス レビューがないので、現時点で必要な対応はありません。

## <a name="filling-out-an-access-review"></a>アクセス レビューに回答する

一覧からアクセス レビューを選択すると、確認が必要なユーザーの名前が表示されます。  自分のアクセス権をレビューするように求められている場合は、1 つの名前 (自分の名前) だけを表示することができます。

一覧の行ごとに、ユーザーのアクセス権を承認または拒否するかどうかを指定できます。 目的の行をクリックして、承認するか拒否するかを選んでください  (ユーザーがわからない場合も回答することができます)。

アクセス権の継続またはグループ メンバーシップの継続を承認することについて、その正当性を説明するよう求められる場合があります。

## <a name="next-steps"></a>次のステップ

ユーザーのアクセス権を拒否してもすぐには削除されないことに注意してください。削除できる状態になるのは、レビューが完了するか、管理者がレビューを停止したときです。 回答に変更が生じて、一度拒否したユーザーを承認する場合や一度承認したユーザーを拒否する場合は、その行をクリックして回答をリセットしてから改めて回答を選択してください。  この作業は、アクセス レビューが完了するまで行うことができます。



