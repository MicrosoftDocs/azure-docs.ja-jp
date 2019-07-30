---
title: Microsoft Authentication Library を使用してアプリケーションを Azure AD B2C と相互運用できる方法を学習する
description: Microsoft Authentication Library (MSAL) を使用すると、アプリケーションを Azure AD B2C と相互運用し、セキュリティで保護された Web API を呼び出すためにトークンを取得できます。 これらの Web API には、Microsoft Graph、その他の Microsoft API、他の開発者の Web API、または自分の Web API が可能です。
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: db05f59faf945e425761fe7a20bad3e263246a39
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849341"
---
# <a name="use-microsoft-authentication-library-to-interoperate-with-azure-active-directory-b2c"></a>Microsoft Authentication Library を使用して Azure Active Directory B2C と相互運用する

Microsoft Authentication Library (MSAL) を使用すると、アプリケーション開発者は、[Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/) を使用してソーシャル ID およびローカル ID でユーザーを認証できます。 Azure AD B2C は、ID 管理サービスです。 それを使うと、顧客がアプリケーションを使用するときにサインアップ、サインイン、プロファイル管理を行う方法をカスタマイズおよび制御できます。

Azure AD B2C を使うと、アプリケーションの UI をカスタマイズしたり、ブランド名を付けて、一貫性のある操作性を顧客に与えることもできます。

このチュートリアルでは、MSAL を使用して Azure AD B2C と相互運用する方法について説明します。

## <a name="prerequisites"></a>前提条件

独自の [Azure AD B2C テナント](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)をまだ作成していない場合、すぐに作成してください。 また、既存の Azure AD B2C テナントを使用できます。

## <a name="javascript"></a>JavaScript

以下の手順では、シングルページ アプリケーションでユーザーのサインアップ、サインイン、保護された Web API の呼び出しに Azure AD B2C を使用する方法が示されています。

### <a name="step-1-register-your-application"></a>手順 1:アプリケーションの登録

認証を実装するには、まず、アプリケーションを登録する必要があります。 詳細な手順については、[Register your application (アプリケーションの登録)](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c) に関する記事をご覧ください。

### <a name="step-2-download-the-sample-application"></a>手順 2:サンプル アプリケーションのダウンロード

サンプルを ZIP ファイルとしてダウンロードするか、GitHub から複製します。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>手順 3:認証を構成する。

1. サンプルの **index.html** ファイルを開きます。

1. アプリケーションの登録中に前に記録したアプリケーション ID とキーを使用して、サンプルを構成します。 次のコード行を変更し、値を実際のディレクトリと API の名前に置き換えます。

   ```javascript
   // The current application coordinates were pre-registered in a B2C directory.

   const msalConfig = {
       auth:{
           clientId: "Enter_the_Application_Id_here",
           authority: "https://login.microsoftonline.com/tfp/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>",
           b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/hello/demo.read"],
           webApi: 'http://localhost:5000/hello',
     };

   // create UserAgentApplication instance
   const myMSALObj = new UserAgentApplication(msalConfig);
   ```

このチュートリアルの[ユーザー フロー](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies)の名前は、**B2C_1_signupsignin1** です。 別のユーザー フロー名を使用している場合は、**authority** の値にその名前を設定します。

### <a name="step-4-configure-your-application-to-use-b2clogincom"></a>手順 4:`b2clogin.com` を使用するようにアプリケーションを構成する

リダイレクト URL として `login.microsoftonline.com` 代わりに `b2clogin.com` を使用できます。 サインアップとサインインのための ID プロバイダーを設定するときに、Azure AD B2C アプリケーションでそれを行います。

`https://your-tenant-name.b2clogin.com/your-tenant-guid` のコンテキストで `b2clogin.com` を使用すると、次のような効果があります。

- Microsoft のサービスで Cookie ヘッダーに使用される領域が少なくなります。
- お使いの URL に、Microsoft への参照が含まれなくなりました。 たとえば、Azure AD B2C アプリケーションではおそらく `login.microsoftonline.com` が参照されます。

 `b2clogin.com` を使用するには、アプリケーションの構成を更新する必要があります。  

- `b2clogin.com` を使用するリダイレクトが発生できるように、**validateAuthority** プロパティを `false` に設定します。

次の例は、このプロパティを設定する方法を示しています。

```javascript
// The current application coordinates were pre-registered in a B2C directory.

const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://contoso.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://contosohello.azurewebsites.net/hello',
        validateAuthority: false;

};
// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

> [!NOTE]
> お使いの Azure AD B2C アプリケーションでは、おそらく、ユーザー フロー参照やトークン エンドポイントなどのいくつかの場所で `login.microsoftonline.com` が参照されています。 承認エンドポイント、トークン エンドポイント、および発行者が、`your-tenant-name.b2clogin.com` を使用するように更新されていることを確認してください。

JavaScript 用 MSAL プレビュー (MSAL.js) の使用方法については、この [MSAL JavaScript サンプル](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c)に従ってください。 そのサンプルでは、アクセス トークンが取得され、Azure AD B2C によって保護された API が呼び出されます。

## <a name="next-steps"></a>次の手順

各項目の詳細情報

- [カスタム ポリシー](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [ユーザー インターフェイスのカスタマイズ](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)