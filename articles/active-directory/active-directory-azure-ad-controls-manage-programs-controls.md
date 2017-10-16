---
title: "Azure AD アクセス レビューに使用するプログラムとコントロールの管理 | Microsoft Docs"
description: "社内のコンプライアンスへの取り組みやリスク管理、ガバナンスごとに追加のプログラムを作成することで、Azure Active Directory のアクセス レビューをコントロールとしてまとめ、体系化することができます。"
services: active-directory
documentationcenter: 
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
ms.openlocfilehash: f6d7c2a1a3e3bf78b568f2f6b079476e403b9911
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="manage-programs-and-their-controls"></a>プログラムとそのコントロールの管理 

グループ メンバーとアプリケーション アクセスを対象として Azure AD に備わっているアクセス レビューは、アクセス権の所在管理を徹底するコントロールの例です。 こうしたコントロールによって、企業はそのガバナンス、リスク管理、コンプライアンス要件に効率よく対応することができます。  

## <a name="how-to-manage-programs-and-their-controls"></a>プログラムとそのコントロールを管理する方法
目的の異なるさまざまなアクセス レビューは、プログラムにまとめることにより、その追跡と収集を省力化することができます。  それぞれのアクセス レビューを特定のプログラムに関連付けることができるので、監査担当者への報告書には、特定の取り組みの範囲内のアクセス レビューだけを表示することができます。

プログラムの一覧を表示するには、[アクセス レビュー ページ](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)に移動し、**[プログラム]** タブに切り替えます。

"既定のプログラム" というプログラムは最初から 1 つ存在します。  グローバル管理者ロールのユーザーであれば、新たにプログラムを作成することができます。  たとえば、コンプライアンスの取り組みやビジネス目標ごとにプログラムを 1 つ用意することができます。

不要になったためコントロールが関連付けられていないプログラムは削除することができます。

## <a name="next-steps"></a>次のステップ

- [グループのメンバーまたはアプリケーションへのアクセスのアクセス レビューを作成する](active-directory-azure-ad-controls-create-access-review.md)

