---
title: チュートリアル:デスクトップ アプリケーションから Node.js Web API へのアクセスを許可する
description: Active Directory B2C を使用して Node.js Web API を保護し、.NET デスクトップ アプリからそれを呼び出す方法に関するチュートリアル。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: f271dd23501aa0514faff29f638f695c80c844f2
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949774"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>チュートリアル:Azure Active Directory B2C を使用してデスクトップ アプリから Node.js Web API へのアクセスを許可する

このチュートリアルでは、Azure Active Directory B2C (Azure AD B2C) で保護された Node.js Web API を、こちらも Azure AD B2C で保護されている Windows Presentation Foundation (WPF) デスクトップ アプリから呼び出す方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Web API アプリケーションを追加する
> * Web API のスコープを構成する
> * Web API に対するアクセス許可を付与する
> * アプリケーションを使用するようにサンプルを更新する

## <a name="prerequisites"></a>前提条件

「[チュートリアル: ネイティブのデスクトップ クライアントでのユーザー認証](active-directory-b2c-tutorials-desktop-app.md)」掲載されている手順と前提条件を完了させてください。

## <a name="add-a-web-api-application"></a>Web API アプリケーションを追加する

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>スコープを構成する

スコープを使用すると、保護されたリソースへのアクセスを統制できます。 スコープは、スコープベースのアクセス制御を実装するために Web API によって使用されます。 たとえば、読み取りアクセスと書き込みアクセス両方を持つユーザーもいれば、読み取り専用アクセス許可を持つユーザーもいます。 このチュートリアルでは、Web API の読み取りおよび書き込みアクセス許可を定義します。

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

`demo.read` スコープの **[スコープ]** に示されている値を記録しておきます。これは、後の手順でデスクトップ アプリケーションを構成するときに使用します。 完全なスコープ値は `https://contosob2c.onmicrosoft.com/api/demo.read` のようになります。

## <a name="grant-permissions"></a>アクセス許可を付与する

ネイティブのクライアント アプリケーションから保護された Web API を呼び出すには、登録したネイティブ クライアント アプリケーションに Azure AD B2C に登録した Web API へのアクセス許可を付与する必要があります。

この前提条件のチュートリアルでは、*nativeapp1* という名前のネイティブ クライアント アプリケーションを登録しました。 次の手順では、前のセクションで *webapi1* に対して公開した API のスコープをそのネイティブ アプリケーションの登録に構成します。 これにより、デスクトップ アプリケーションが Azure AD B2C から Web API がそのリソースへの範囲指定がある検証されたアクセスを提供するのに使用できるアクセス トークンを取得できるようになります。 このチュートリアルでは、後でデスクトップ アプリケーションと Web API の両方のコード サンプルを構成して実行します。

#### <a name="applicationstabapplications"></a>[アプリケーション](#tab/applications/)

1. **[アプリケーション]** を選択し、*nativeapp1* を選択します。
1. **[API アクセス]** を選択し、 **[追加]** を選択します。
1. **[API の選択]** ドロップダウンで、*webapi1* を選択します。
1. **[スコープの選択]** ボックスの一覧で、先ほど定義したスコープを選択します。 *demo.read* や *demo.write* などです。
1. **[OK]** を選択します。

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[アプリの登録 (プレビュー)](#tab/app-reg-preview/)

1. **[アプリの登録 (プレビュー)]** を選択し、API にアクセスできるようにするネイティブ クライアント アプリケーションを選択します。 たとえば、*nativeapp1* とします。
1. **[管理]** の下にある **[API のアクセス許可]** を選択します。
1. **[構成されたアクセス許可]** の下で **[アクセス許可の追加]** を選択します。
1. **[自分の API]** タブを選択します。
1. ネイティブのクライアント アプリケーションにアクセスを付与する API を選択します。 たとえば、*webapi1* とします。
1. **[アクセス許可]** で、 **[デモ]** を展開し、前に定義したスコープを選択します。 *demo.read* や *demo.write* などです。
1. **[アクセス許可の追加]** を選択します. 指示に従って、数分待ってから次の手順に進みます。
1. **[(ご自身のテナント名) に管理者の同意を与えます]** を選択します。
1. 現在サインインしているお使いの管理者アカウントを選択するか、少なくとも*クラウド アプリケーション管理者* ロールが割り当てられているお使いの Azure AD B2C テナントのアカウントでサインインします。
1. **[Accept]\(承認\)** を選択します。
1. **[更新]** を選択し、両方のスコープの **[状態]** に、"... に付与されました" が表示されていることを確認します。 アクセス許可が反映されるまでに数分かかる場合があります。

* * *

ユーザーは、WPF デスクトップ アプリケーションを使用するために Azure AD B2C で認証を行います。 デスクトップ アプリケーションで、保護された Web API にアクセスするための認可の付与が、Azure AD B2C から取得されます。

## <a name="configure-the-samples"></a>サンプルの構成

これで Web API が登録され、スコープとアクセス許可が構成されたので、ご自分の Azure AD B2C テナントを使用できるようにデスクトップ アプリケーションと Web API のサンプルを構成します。

### <a name="update-the-desktop-application"></a>デスクトップ アプリケーションをアップデートする

この記事の前提条件では、ご自分の Azure AD B2C テナントのユーザー フローでサインインできるよう [WPF デスクトップ アプリケーション](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop)を変更しました。 このセクションでは、先ほど登録した Web API を参照するようにその同じアプリケーションの *webapi1* を更新します。

1. Visual Studio で **active-directory-b2c-wpf** ソリューション (`active-directory-b2c-wpf.sln`) を開きます。
1. **active-directory-b2c-wpf** プロジェクトの *App.xaml.cs* ファイルを開き、次の変数定義を探します。
    1. `ApiScopes` 変数の値を、**demo.read** スコープを定義したときに記録した値と置き換えます。
    1. `ApiEndpoint` 変数の値を、ご自分のテナントで Web API (例: *webapi1*) を登録したときに記録した**リダイレクト URI** に置き換えます。

    次に例を示します。

    ```csharp
    public static string[] ApiScopes = { "https://contosob2c.onmicrosoft.com/api/demo.read" };
    public static string ApiEndpoint = "http://localhost:5000";
    ```

### <a name="get-and-update-the-nodejs-api-sample"></a>Node.js API のサンプルの取得および更新

次に、GitHub から Node.js の Web API のコード サンプルを取得し、お使いの Azure AD B2C テナントに登録した Web API を使用するようにそれを構成します。

[ZIP ファイルをダウンロード](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip)するか、GitHub からサンプル Web アプリを複製します。

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

Node.js Web API サンプルでは、Passport.js ライブラリを使用して、Azure AD B2C で API 呼び出しを保護できるようにします。

1. `index.js` ファイルを開きます。
1. これらの変数の定義を次の値で更新します。 `<web-API-application-ID>` を、先ほど登録した Web API (*webapi1*) の **アプリケーション (クライアント) ID** に変更します。 `<your-b2c-tenant>` をお使いの Azure AD B2C テナントの名前に変更します。

    ```nodejs
    var clientID = "<web-API-application-ID>";
    var b2cDomainHost = "<your-b2c-tenant>.b2clogin.com";
    var tenantIdGuid = "<your-b2c-tenant>.onmicrosoft.com";
    var policyName = "B2C_1_signupsignin1";
    ```
1. API はローカルで実行しているため、GET メソッドのルートのパスを、デモ アプリの `/hello` の場所ではなく `/` に更新します。

    ```nodejs
    app.get("/",
    ```

## <a name="run-the-samples"></a>サンプルを実行する

### <a name="run-the-nodejs-web-api"></a>Node.js Web API を実行する

1. Node.js コマンド プロンプトを起動します。
2. Node.js サンプルが含まれているディレクトリに変更します。 例: `cd c:\active-directory-b2c-javascript-nodejs-webapi`。
3. 次のコマンドを実行します。
    ```console
    npm install && npm update
    ```
    ```console
    node index.js
    ```

### <a name="run-the-desktop-application"></a>デスクトップ アプリケーションを実行する

1. Visual Studio で **active-directory-b2c-wpf** ソリューションを開きます。
2. **F5** キーを押してデスクトップ アプリを実行します。
3. [デスクトップ アプリにおける Azure Active Directory B2C を使用したユーザーの認証に関するチュートリアル](active-directory-b2c-tutorials-desktop-app.md)で使用したメール アドレスとパスワードでサインインします。
4. **[Call API]** ボタンを選択します。

デスクトップ アプリケーションによりローカルで実行されている Web API に対して要求が行われ、アクセス トークンが有効であることが検証されると、サインインしているユーザーの表示名が表示されます。

![WPF デスクトップ アプリケーションの上部ウィンドウに表示される表示名](media/active-directory-b2c-tutorials-desktop-app-webapi/desktop-app-01-post-api-call.png)

Azure AD B2C によって保護されているお使いのデスクトップ アプリケーションが、これもまた Azure AD B2C によっても保護されているローカルで実行されている Web API を呼び出しています。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Web API アプリケーションを追加する
> * Web API のスコープを構成する
> * Web API に対するアクセス許可を付与する
> * アプリケーションを使用するようにサンプルを更新する

> [!div class="nextstepaction"]
> [チュートリアル:Azure Active Directory B2C でアプリケーションに ID プロバイダーを追加する](tutorial-add-identity-providers.md)
