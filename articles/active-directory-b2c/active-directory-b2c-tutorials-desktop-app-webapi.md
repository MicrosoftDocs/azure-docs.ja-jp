---
title: チュートリアル - デスクトップ アプリケーションから Node.js Web API へのアクセスを許可する - Azure Active Directory B2C | Microsoft Docs
description: Active Directory B2C を使用して Node.js Web API を保護し、.NET デスクトップ アプリからそれを呼び出す方法に関するチュートリアル。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 8c4b2d818549da07faf9ecd28f61b4ed5315f745
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679323"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>チュートリアル:Azure Active Directory B2C を使用してデスクトップ アプリから Node.js Web API へのアクセスを許可する

このチュートリアルでは、Azure Active Directory B2C (Azure AD B2C) で保護された Node.js Web API リソースを Windows Presentation Foundation (WPF) デスクトップ アプリから呼び出す方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Web API アプリケーションを追加する
> * Web API のスコープを構成する
> * Web API に対するアクセス許可を付与する
> * アプリケーションを使用するようにサンプルを更新する

## <a name="prerequisites"></a>前提条件

「[チュートリアル:Azure Active Directory B2C を使用してアカウントによるデスクトップ アプリの認証を有効にする](active-directory-b2c-tutorials-desktop-app.md)」の手順と前提条件を完了します。

## <a name="add-a-web-api-application"></a>Web API アプリケーションを追加する

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>スコープを構成する

スコープを使用すると、保護されたリソースへのアクセスを統制できます。 スコープは、スコープベースのアクセス制御を実装するために Web API によって使用されます。 たとえば、読み取りアクセスと書き込みアクセス両方を持つユーザーもいれば、読み取り専用アクセス許可を持つユーザーもいます。 このチュートリアルでは、Web API の読み取りアクセス許可を定義します。

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>アクセス許可を付与する

保護された Web API をアプリケーションから呼び出すには、その API へのアクセス許可をアプリケーションに付与する必要があります。 前提条件のチュートリアルでは、*app1* という名前の Azure AD B2C に Web アプリケーションを作成しました。 このアプリケーションを使用して、Web API を呼び出します。

1. **[アプリケーション]** を選択し、*nativeapp1* を選択します。
1. **[API アクセス]** を選択し、 **[追加]** を選択します。
1. **[API の選択]** ドロップダウンで、*webapi1* を選択します。
1. **[スコープの選択]** ボックスの一覧で、先ほど定義したスコープを選択します。 *demo.read* や *demo.write* などです。
1. **[OK]** を選択します。

ユーザーは、WPF デスクトップ アプリケーションを使用するために Azure AD B2C で認証を行います。 デスクトップ アプリケーションで、保護された Web API にアクセスするための認可の付与が、Azure AD B2C から取得されます。

## <a name="configure-the-sample"></a>サンプルの構成

Web API を登録し、スコープを定義したので、Azure AD B2C テナントを使用するように Web API コードを構成します。 このチュートリアルでは、GitHub からダウンロードできるサンプル Node.js Web アプリケーションを構成します。

[ZIP ファイルをダウンロード](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip)するか、GitHub からサンプル Web アプリを複製します。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

Node.js Web API サンプルでは、Passport.js ライブラリを使用して、Azure AD B2C で API 呼び出しを保護できるようにします。

1. `index.js` ファイルを開きます。
2. Azure AD B2C テナントの登録情報でサンプルを構成します。 次のコード行を変更します。

    ```nodejs
    var tenantID = "<your-tenant-name>.onmicrosoft.com";
    var clientID = "<application-ID>";
    var policyName = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>サンプルを実行する

1. Node.js コマンド プロンプトを起動します。
2. Node.js サンプルが含まれているディレクトリに変更します。 例: `cd c:\active-directory-b2c-javascript-nodejs-webapi`。
3. 次のコマンドを実行します。
    ```
    npm install && npm update
    ```
    ```
    node index.js
    ```

### <a name="run-the-desktop-application"></a>デスクトップ アプリケーションを実行する

1. Visual Studio で **active-directory-b2c-wpf** ソリューションを開きます。
2. **F5** キーを押してデスクトップ アプリを実行します。
3. [デスクトップ アプリにおける Azure Active Directory B2C を使用したユーザーの認証に関するチュートリアル](active-directory-b2c-tutorials-desktop-app.md)で使用したメール アドレスとパスワードでサインインします。
4. **[Call API]** ボタンをクリックします。

デスクトップ アプリケーションで、Web API に要求が送信されて、ログインしているユーザーの表示名を含む応答が取得されます。 保護されているデスクトップ アプリケーションでは、Azure AD B2C テナント内の保護された Web API が呼び出されます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Web API アプリケーションを追加する
> * Web API のスコープを構成する
> * Web API に対するアクセス許可を付与する
> * アプリケーションを使用するようにサンプルを更新する

> [!div class="nextstepaction"]
> [チュートリアル:Azure Active Directory B2C でアプリケーションに ID プロバイダーを追加する](tutorial-add-identity-providers.md)
