---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/03/2016
ms.author: patricka
ms.openlocfilehash: fcd71f74e0b00934958828024094773e42496b66
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2018
ms.locfileid: "51017272"
---
[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

Web API を登録するには、表に指定された設定を使用してください。

![新しい Web API の登録設定の例](./media/active-directory-b2c-register-web-api/b2c-new-web-api-settings.png)

| Setting      | 値の例  | 説明                                        |
| ------------ | ------- | -------------------------------------------------- |
| **名前** | Contoso B2C API | アプリケーションの **[名前]** には、コンシューマーが API の機能を把握できるような名前を入力します。 | 
| **Web アプリ/Web API を含める** | はい | Web API の場合は **はい** を選択します。 |
| **暗黙的フローを許可する** | はい | アプリケーションで [OpenID Connect サインイン](../articles/active-directory-b2c/active-directory-b2c-reference-oidc.md)を使用する場合は、**はい** を選択します。 |
| **応答 URL** | `https://localhost:44316/` | [応答 URL] は、アプリケーションが要求したトークンを Azure AD B2C が返すエンドポイントです。 この例では、Web API がローカルに存在し、ポート 44316 でリッスンしています。 |
| **アプリケーション ID/URI** | api | アプリケーション ID/URI は Web API に使用される ID です。 ドメインを含んだ完全な識別子 URI が自動的に生成されます。 |

**[作成]** をクリックして、アプリケーションを登録します。

新しく登録したアプリケーションが、B2C テナントのアプリケーションの一覧に表示されます。 一覧から Web API を選択します。 API のプロパティ ウィンドウが表示されます。

![Web API のプロパティ](./media/active-directory-b2c-register-web-api/b2c-web-api-properties.png)

グローバルに一意となる**アプリケーション クライアント ID** をメモします。 アプリケーションのコードには、この ID を使用します。