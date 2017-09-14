---
title: "指定の API のアクセス許可を選択する方法 | Microsoft Docs"
description: "Azure AD で開発中または登録中のカスタム アプリケーションの認証エンドポイントを見つける方法。"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: 3578684ac8c92ff195b19740b28ef9adebdc7309
ms.contentlocale: ja-jp
ms.lasthandoff: 04/14/2017

---

# <a name="how-to-select-permissions-for-a-given-api"></a>指定の API のアクセス許可を選択する方法

アプリケーションの認証エンドポイントは [Azure Portal](https://portal.azure.com) で見つけることができます。

-   [Azure Portal](https://portal.azure.com) に移動します。

-   左側のナビゲーション ウィンドウで、**[Azure Active Directory]** をクリックします。

-   **[アプリの登録]** をクリックして、**[エンドポイント]** を選択します。

-   **[エンドポイント]** ページが開き、テナントのすべての認証エンドポイントが一覧表示されます。

-   使用する認証プロトコルに固有のエンドポイントをアプリケーション ID と共に使用して、アプリケーション固有の認証要求を作成します。

## <a name="next-steps"></a>次のステップ
[Azure Active Directory 開発者ガイド](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-developers-guide#authentication-and-authorization-protocols)

