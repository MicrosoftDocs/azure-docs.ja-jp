---
title: "ユーザー割り当ての要求 - Azure AD | Microsoft Docs"
description: "Azure アプリケーションにユーザーの割り当てを要求する方法です。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: 30b78cba-1e0f-472f-8314-f2250a9b91c3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: kgremban
robots: noindex
ms.openlocfilehash: 079b806c041a4a21e9350342867aee581c57bf45
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-and-applications-require-user-assignment"></a>Azure AD とアプリケーション: ユーザー割り当ての要求
## <a name="requiring-user-assignment"></a>ユーザー割り当ての要求
1. 管理者アカウントを使用して、Azure Portal にログインします。
2. メイン メニューの **[すべてのアイテム]** をクリックします。
3. アプリケーションに使用するディレクトリを選択します。
4. **[アプリケーション]** タブをクリックします。
5. このディレクトリに関連付けられているアプリケーションの一覧から、アプリケーションを選択します。
6. **[構成]** タブをクリックします。
7. **[アプリにアクセスするにはユーザー割り当てが必要]** を [はい] に変更します。
8. 画面の下部にある **[保存]** をクリックします。

ここで、アプリケーションにユーザーやグループを割り当てる必要があります。 「[アプリケーションへのユーザーの割り当て](active-directory-applications-guiding-developers-assigning-users.md)」と「[アプリケーションへのグループの割り当て](active-directory-applications-guiding-developers-assigning-groups.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
[!INCLUDE [active-directory-applications-guiding-developers-for-lob-applications-toc.md](../../includes/active-directory-applications-guiding-developers-for-lob-applications-toc.md)]
