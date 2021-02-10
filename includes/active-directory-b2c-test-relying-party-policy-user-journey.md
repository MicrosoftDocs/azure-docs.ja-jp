---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 4ca47bf5b99588fbe4efd91a9211ee0309c1892c
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98951534"
---
## <a name="test-your-custom-policy"></a>カスタム ポリシーのテスト

1. 証明書利用者ポリシー (`B2C_1A_signup_signin` など) を選択します。
1. **[アプリケーション]** には、[前に登録した](../articles/active-directory-b2c/troubleshoot-custom-policies.md#troubleshoot-the-runtime) Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[今すぐ実行]** ボタンを選択します。

サインイン プロセスが成功すると、ブラウザーは `https://jwt.ms` にリダイレクトされ、Azure AD B2C によって返されたトークンの内容が表示されます。

