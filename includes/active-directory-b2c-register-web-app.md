---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/03/2016
ms.author: patricka
ms.openlocfilehash: 4d5c8373f2a8f02ccfbe51cb970ab08744950e7e
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133395"
---
[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

Web アプリケーションを登録するには、表に指定された設定を使用してください。

![新しい Web アプリの登録設定の例](./media/active-directory-b2c-register-web-app/b2c-new-app-settings.png)

| Setting      | 値の例  | 説明                                        |
| ------------ | ------- | -------------------------------------------------- |
| **名前** | Contoso B2C アプリ | アプリケーションの **名前** には、コンシューマーがアプリケーションの機能を把握できるような名前を入力します。 | 
| **Web アプリ/Web API を含める** | はい | Web アプリケーションの場合は **はい** を選択します。 |
| **暗黙的フローを許可する** | はい | アプリケーションで [OpenID Connect サインイン](../articles/active-directory-b2c/active-directory-b2c-reference-oidc.md)を使用する場合は、**[はい]** を選択します。 |
| **応答 URL** | `https://localhost:44316` | 応答 URL は、アプリケーションが要求したトークンを Azure AD B2C が返すエンドポイントです。 [適切な](../articles/active-directory-b2c/active-directory-b2c-app-registration.md#choosing-a-web-app-or-api-reply-url)**応答 URL** を入力します。 この例では、アプリがローカルに存在し、ポート 44316 でリッスンします。 |

**[作成]** をクリックして、アプリケーションを登録します。

新しく登録したアプリケーションが、B2C テナントのアプリケーションの一覧に表示されます。 一覧から Web アプリを選択します。 Web アプリケーションのプロパティ ウィンドウが表示されます。

![Web アプリのプロパティ](./media/active-directory-b2c-register-web-app/b2c-web-app-properties.png)

グローバルに一意となる**アプリケーション クライアント ID** をメモします。 アプリケーションのコードには、この ID を使用します。