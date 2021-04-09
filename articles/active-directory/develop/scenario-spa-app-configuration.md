---
title: シングルページ アプリの構成 | Azure
titleSuffix: Microsoft identity platform
description: シングルページ アプリケーション (アプリのコード構成) を構築する方法について説明します
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: fe73832ec5eaee62a2dc2d397c12f82334e2efd8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103010706"
---
# <a name="single-page-application-code-configuration"></a>シングルページ アプリケーション：コード構成

ご利用のシングルページ アプリケーション (SPA) のコードを構成する方法について説明します。

## <a name="microsoft-libraries-supporting-single-page-apps"></a>シングルページ アプリをサポートする Microsoft ライブラリ 

次の Microsoft ライブラリはシングルページ アプリをサポートしています。

[!INCLUDE [active-directory-develop-libraries-spa](../../../includes/active-directory-develop-libraries-spa.md)]

## <a name="application-code-configuration"></a>アプリケーションのコード構成

MSAL ライブラリでは、ライブラリの初期化中にアプリケーションの登録情報が構成として渡されます。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_client_id'
    }
};

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```

構成可能なオプションの詳細については、[MSAL.js を使用したアプリケーションの初期化](msal-js-initializing-client-applications.md)に関する記事を参照してください。

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
// App.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id'
            }
        })
    ]
})

export class AppModule { }
```

---

## <a name="next-steps"></a>次のステップ

このシナリオの次の記事である[サインインとサインアウト](scenario-spa-sign-in.md)に関する記事に進みます。
