---
title: シングルページ アプリケーション (アプリのコード構成) - Microsoft ID プラットフォーム
description: シングルページ アプリケーション (アプリのコード構成) を構築する方法について説明します
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7b4fba03f9edf8a3f4e42b23c6a1b5e06518863
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891533"
---
# <a name="single-page-application---code-configuration"></a>シングルページ アプリケーション - コード構成

ご利用のシングルページ アプリケーション (SPA) のコードを構成する方法について説明します。

## <a name="msal-libraries-supporting-implicit-flow"></a>暗黙的なフローをサポートする MSAL ライブラリ

Microsoft ID プラットフォームは、業界推奨のセキュアな手法を使用して暗黙的なフローをサポートする MSAL.js ライブラリを提供します。  

暗黙的なフローをサポートするライブラリは次のとおりです。

| MSAL ライブラリ | 説明 |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | JavaScript または SPA フレームワーク (Angular、Vue.js、React.js など) を使用して構築されたクライアント側 Web アプリで使用するためのプレーンな JavaScript ライブラリ |
| ![MSAL Angular](media/sample-v2-code/logo_angular.png) <br/> [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Angular フレームワークで構築されたシングル ページ アプリでの使用を簡素化するためのコア MSAL.js ライブラリのラッパー。 このライブラリはプレビュー段階であり、特定の Angular バージョンおよびブラウザーにおいて[既知の問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular)があります。 |

## <a name="application-code-configuration"></a>アプリケーションのコード構成

MSAL ライブラリでは、ライブラリの初期化中にアプリケーションの登録情報が構成として渡されます。

### <a name="javascript"></a>JavaScript

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri" //defaults to application start page
    }
}

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```
使用できる構成可能なオプションの詳細については、[MSAL.js を使用したアプリケーションの初期化](msal-js-initializing-client-applications.md)に関する記事を参照してください。

### <a name="angular"></a>Angular

```javascript
//In app.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id'
            })]
         })

  export class AppModule { }
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [サインインとサインアウト](scenario-spa-sign-in.md)
