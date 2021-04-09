---
title: MSAL.js を Azure AD B2C とともに使用する
titleSuffix: Microsoft identity platform
description: JavaScript 用 Microsoft Authentication Library (MSAL.js) を使用すると、アプリケーションは Azure AD B2C と連携し、セキュリティで保護された Web API を呼び出すためのトークンを取得できます。 これらの Web API には、Microsoft Graph、その他の Microsoft API、他の開発者の Web API、または自分の Web API が可能です。
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/05/2020
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev devx-track-js
ms.openlocfilehash: 53294a30b38bed7ab7516443277cac24e4fef4c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98063724"
---
# <a name="use-the-microsoft-authentication-library-for-javascript-to-work-with-azure-ad-b2c"></a>JavaScript 用 Microsoft Authentication Library を使用して Azure AD B2C と連携する

[JavaScript 用 Microsoft Authentication Library (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) を使用すると、JavaScript 開発者は [Azure Active Directory B2C](../../active-directory-b2c/overview.md) (Azure AD B2C) を使用して、ソーシャルおよびローカル ID でユーザーを認証できます。

ID 管理サービスとして Azure AD B2C を使用すると、顧客がアプリケーションを使用するときにサインアップ、サインイン、自分のプロファイルの管理を行う方法をカスタマイズして制御できます。 Azure AD B2C では、認証プロセス中にアプリケーションで表示される UI をブランド化し、カスタマイズすることもできます。

以降のセクションでは、次の方法について説明します。

- Node.js Web API を保護する
- シングルページ アプリケーション (SPA) でのサインインをサポートし、*その* 保護された Web API を呼び出す
- パスワード リセットのサポートを有効にする

## <a name="prerequisites"></a>前提条件

[Azure AD B2C テナント](../../active-directory-b2c/tutorial-create-tenant.md)をまだ作成していない場合、これを作成します。

## <a name="nodejs-web-api"></a>Node.js Web API

次の手順は、**Web API** が Azure AD B2C を使用して自身を保護し、選択されたスコープをクライアント アプリケーションに公開する方法を示しています。

MSAL.js for Node は現在開発中です。 詳細については、GitHub 上の[ ロードマップ](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap) を参照してください。 現時点では、Microsoft によって開発およびサポートされている Node.js 用の認証ライブラリである [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad) を使用することをお勧めします。

### <a name="step-1-register-your-application"></a>手順 1:アプリケーションの登録

Azure AD B2C を使用して Web API を保護するには、まずそれを登録する必要があります。 詳細な手順については、[Register your application (アプリケーションの登録)](../../active-directory-b2c/add-web-api-application.md) に関する記事をご覧ください。

### <a name="step-2-download-the-sample-application"></a>手順 2:サンプル アプリケーションのダウンロード

サンプルを ZIP ファイルとしてダウンロードするか、GitHub から複製します。

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>手順 3:認証を構成する。

1. サンプルの *config.json* ファイルを開きます。

2. 以前にアプリケーションの登録中に取得したアプリケーション資格情報を使用してサンプルを構成します。 次のコード行の値を実際のテナント名、クライアント ID、ポリシー名に置き換えて変更します。

    ```json
         "credentials": {
             "tenantName": "<your-tenant-name>",
             "clientID": "<your-webapi-application-ID>"
         },
         "policies": {
             "policyName": "B2C_1_signupsignin1"
         },
         "resource": {
             "scope": ["demo.read"] 
         },
    ```

詳細については、この [Node.js B2C Web API のサンプル](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)を確認してください。

## <a name="javascript-spa"></a>JavaScript SPA

次の手順は、**シングルページ アプリケーション** が Azure AD B2C を使用して、サインアップ、サインイン、保護された Web API の呼び出しを行う方法を示しています。

### <a name="step-1-register-your-application"></a>手順 1:アプリケーションの登録

認証を実装するには、まず、アプリケーションを登録する必要があります。 詳細な手順については、[Register your application (アプリケーションの登録)](../../active-directory-b2c/tutorial-register-applications.md) に関する記事をご覧ください。

### <a name="step-2-download-the-sample-application"></a>手順 2:サンプル アプリケーションのダウンロード

コード サンプルの [.ZIP アーカイブ](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip)をダウンロードするか、GitHub リポジトリを複製します。

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>手順 3:認証を構成する。

アプリケーションを構成する場合の関心ポイントとして、次の 2 つがあります。

- API エンドポイントと公開されるスコープを構成する
- 認証パラメーターとトークン スコープを構成する

1. サンプルの *apiConfig.js* ファイルを開きます。

2. 以前に Web API の登録中に取得したパラメーターを使用してサンプルを構成します。 次のコード行を、その各値を Web API と公開されるスコープのアドレスに置き換えることによって変更します。

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };
   ```

1. サンプルの *authConfig.js* ファイルを開きます。

1. 以前にシングルページ アプリケーションの登録中に取得したパラメーターを使用してサンプルを構成します。 次のコード行を、その各値をクライアント ID、権限メタデータ、トークン要求スコープに置き換えることによって変更します。

   ```javascript
    // Config object to be passed to Msal on creation.
    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/B2C_1_signupsignin1",
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage", // This configures where your cache will be stored
            storeAuthStateInCookie: false // Set this to "true" to save cache in cookies
        }
    };

    // Add here scopes for id token to be used at the MS Identity Platform endpoint
    const loginRequest = {
        scopes: ["openid", "profile"],
    };
   ```

詳細については、この [JavaScript B2C シングルページ アプリケーションのサンプル](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)を確認してください。

## <a name="support-password-reset"></a>パスワードのリセットのサポート

このセクションでは、Azure AD B2C パスワード リセット ユーザー フローを使用するようにシングルページ アプリケーションを拡張します。 現在、MSAL.js では複数のユーザー フローやカスタム ポリシーがネイティブでサポートされていませんが、ライブラリを使用して、パスワードのリセットなどの一般的なユース ケースを処理できます。

次の手順では、前の [JavaScript SPA](#javascript-spa) セクションの手順に従っていることを前提としています。

### <a name="step-1-define-the-authority-string-for-password-reset-user-flow"></a>手順 1:パスワード リセット ユーザー フローの機関文字列を定義する

1. まず、機関 URI を格納するオブジェクトを作成します。

    ```javascript
        const b2cPolicies = {
            names: {
                signUpSignIn: "b2c_1_susi",
                forgotPassword: "b2c_1_reset"
            },
            authorities: {
                signUpSignIn: {
                    authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
                },
                forgotPassword: {
                    authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
                },
            },
        }
    ```

1. 次に、`signInSignUp` ポリシーを使用して、MSAL オブジェクトを既定として初期化します (前のコード スニペットを参照してください)。 ユーザーがログインしようとすると、次の画面が表示されます。

    :::image type="content" source="media/msal-b2c-overview/user-journey-01-signin.png" alt-text="Azure AD B2C によって表示されるサインイン画面":::

### <a name="step-2-catch-and-handle-authentication-errors-in-your-login-method"></a>手順 2:ログイン方法の認証エラーをキャッチして処理する

ユーザーが **[パスワードを忘れた場合]** を選択すると、アプリケーションによってエラーがスローされます。このエラーは、コード内でキャッチしてから適切なユーザー フローを提示することで処理します。 ここでは、`b2c_1_reset` パスワード リセット フローです。

1. 次のように、サインイン方法を拡張します。

    ```javascript
    function signIn() {
      myMSALObj.loginPopup(loginRequest)
        .then(loginResponse => {
            console.log("id_token acquired at: " + new Date().toString());

            if (myMSALObj.getAccount()) {
              updateUI();
            }

        }).catch(function (error) {
          console.log(error);

          // error handling
          if (error.errorMessage) {
            // check for forgot password error
            if (error.errorMessage.indexOf("AADB2C90118") > -1) {

              //call login method again with the password reset user flow
              myMSALObj.loginPopup(b2cPolicies.authorities.forgotPassword)
                .then(loginResponse => {
                  console.log(loginResponse);
                  window.alert("Password has been reset successfully. \nPlease sign-in with your new password.");
                })
            }
          }
        });
    }
    ```

1. 前のコードスニペットは、コード `AADB2C90118` でエラーをキャッチした後に、パスワード リセット画面を表示する方法を示しています。

    パスワードをリセットした後、ユーザーはアプリケーションに戻って再度サインインします。

    :::image type="content" source="media/msal-b2c-overview/user-journey-02-password-reset.png" alt-text="Azure AD B2C によって示されるパスワード リセット フロー画面" border="false":::

    エラーコードと例外の処理の詳細については、[MSAL のエラーと例外コード](msal-error-handling-js.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

以下の Azure AD B2C の概念について詳しく学習します。

- [ユーザー フロー](../../active-directory-b2c/tutorial-create-user-flows.md)
- [カスタム ポリシー](../../active-directory-b2c/custom-policy-get-started.md)
- [UX のカスタマイズ](../../active-directory-b2c/configure-user-input.md)
