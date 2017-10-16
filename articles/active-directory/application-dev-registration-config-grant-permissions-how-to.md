---
title: "独自に開発したアプリケーションにアクセス許可を付与する方法 | Microsoft Docs"
description: "Azure AD ポータルまたは URL パラメーターを使用して、独自に開発したアプリケーションにアクセス許可を付与する方法"
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
ms.openlocfilehash: 336b945929f80e1a566f7cf71b40fd799a98c12d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-grant-permissions-to-a-custom-developed-application"></a>独自に開発したアプリケーションにアクセス許可を付与する方法

事前にアプリに同意を付与したい場合、またはアプリに対する同意がないことを示すエラーが発生した場合は、以下の手順を試してみてください。

## <a name="how-to-perform-admin-consent-for-your-application"></a>アプリケーションに対して管理者の同意を実行する方法

この方法を使用すると、組織内のすべてのユーザーに関してアプリケーションに同意が付与されます。

1. **グローバル管理者**として **[アプリの登録]** ブレードに移動し、アプリを選択します。

2. **[必要なアクセス許可]** を選択し、最後にブレードの上部にある **[アクセス許可の付与]** ボタンをクリックします。

または、アプリ構成を使用して *login.microsoftonline.com* に対する要求を作成し、そこに *&prompt=admin\_consent* を追加する方法があります。 管理者の資格情報でサインインしたら、すべてのユーザーに関してアプリに同意が付与されます。

## <a name="how-to-force-user-consent-for-your-application"></a>アプリケーションでユーザーの同意を強制する方法

* *&prompt=consent* を認証要求に追加します。認証のたびにエンド ユーザーの同意が必要になります。

## <a name="next-steps"></a>次のステップ

[同意と Azure AD へのアプリの統合](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications)

[Azure AD v2.0 集中型アプリの同意とアクセス許可](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure AD StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
