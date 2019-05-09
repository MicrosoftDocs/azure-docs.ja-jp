---
title: チュートリアル - シングルページ アプリケーションで認証を有効にする - Azure Active Directory B2C | Microsoft Docs
description: シングルページ アプリケーション (JavaScript) で Azure Active Directory B2C を使用してユーザー ログインを提供する方法に関するチュートリアル。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 813c7131ff0a56e843e728cd78fff969b1d90fcc
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756327"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-using-azure-active-directory-b2c"></a>チュートリアル:Azure Active Directory B2C を使用してシングルページ アプリケーションで認証を有効にする

このチュートリアルでは、Azure Active Directory (Azure AD) B2C を使用してシングルページ アプリケーション (SPA) でユーザーをサインインおよびサインアップする方法を説明します。 Azure AD B2C を使用すると、アプリケーションでオープンな標準プロトコルを使用して、ソーシャル アカウント、エンタープライズ アカウント、Azure Active Directory アカウントに対する認証を行うことができます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure AD B2C でアプリケーションを更新する
> * アプリケーションを使用するようにサンプルを構成する
> * ユーザー フローを使用してサインアップする

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* [ユーザー フローを作成](tutorial-create-user-flows.md)して、アプリケーションのユーザー エクスペリエンスを有効にします。 
* **ASP.NET および Web 開発**のワークロードと共に、[Visual Studio 2017](https://www.visualstudio.com/downloads/) をインストールする。
* [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) 以降をインストールします
*  [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>アプリケーションの更新

前提条件の一部として完了したチュートリアルで、Azure AD B2C に Web アプリケーションを追加しました。 チュートリアルのサンプルとの通信を有効にするには、Azure AD B2C のアプリケーションにリダイレクト URI を 追加する必要があります。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. お使いの Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、**[Azure AD B2C]** を検索して選択します。
4. **[アプリケーション]** を選択し、*webapp1* アプリケーションを選択します。
5. **[応答 URL]** に「`http://localhost:6420`」を追加します。
6. **[保存]** を選択します。
7. プロパティ ページで、Web アプリケーションを構成するときに使用するアプリケーション ID を記録しておきます。
8. **[キー]**、**[キーの生成]**、**[保存]** の順に選択します。 Web アプリケーションを構成するときに使用するキーを記録しておきます。

## <a name="configure-the-sample"></a>サンプルの構成

このチュートリアルでは、GitHub からダウンロードできるサンプルを構成します。 そのサンプルでは、シングルページ アプリケーションでユーザーのサインアップ、サインイン、保護された Web API の呼び出しに Azure AD B2C を使用する方法が示されています。

[ZIP ファイルをダウンロード](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip)するか、GitHub からサンプルを複製します。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

設定を変更するには:

1. サンプルの `index.html` ファイルを開きます。
2. 前に記録したアプリケーション ID とキーを使用して、サンプルを構成します。 次のコード行を変更し、値を実際のディレクトリと API の名前に置き換えます。

    ```javascript
    // The current application coordinates were pre-registered in a B2C directory.
    var applicationConfig = {
        clientID: '<Application ID>',
        authority: "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://contoso.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://contosohello.azurewebsites.net/hello',
    };
    ```

    このチュートリアルで使用されているユーザー フローの名前は、**B2C_1_signupsignin1** です。 別のユーザー フロー名を使用している場合は、`authority` の値にそのユーザー フロー名を使用します。

## <a name="run-the-sample"></a>サンプルを実行する

1. Node.js コマンド プロンプトを起動します。
2. Node.js サンプルが含まれているディレクトリに変更します。 例: `cd c:\active-directory-b2c-javascript-msal-singlepageapp`。
3. 次のコマンドを実行します。

    ```
    npm install && npm update
    node server.js
    ```

    コンソール ウィンドウには、アプリがホストされている場所のポート番号が表示されます。
    
    ```
    Listening on port 6420...
    ```

4. ブラウザーを使用してアドレス `http://localhost:6420` に移動し、アプリケーションを表示します。

サンプルでは、サインアップ、サインイン、プロファイルの編集、パスワードのリセットがサポートされています。 このチュートリアルでは、ユーザーがメール アドレスを使用してサインアップを行う方法に焦点を当てます。

### <a name="sign-up-using-an-email-address"></a>メール アドレスを使用してサインアップする

1. **[Login]\(ログイン\)** をクリックし、アプリケーションのユーザーとしてサインアップします。 これにより、前のステップで定義した **B2C_1_signupsignin1** ユーザー フローが使用されます。
2. Azure AD B2C によって、サインアップ リンクを含むサインイン ページが表示されます。 まだアカウントを持っていないため、**[今すぐサインアップ]** リンクをクリックします。 
3. サインアップ ワークフローによって、メール アドレスを使用してユーザーの ID を収集および確認するためのページが表示されます。 また、サインアップ ワークフローでは、ユーザー フローで定義されているユーザーのパスワードと要求された属性も収集されます。

    有効なメール アドレスを使用し、確認コードを使用して検証します。 パスワードを設定します。 要求された属性の値を入力します。 

    ![サインアップ ワークフロー](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. **[作成]** をクリックして、Azure AD B2C ディレクトリにローカル アカウントを作成します。

これで、ユーザーはメール アドレスを使用してサインインし、SPA アプリを使用できるようになりました。

> [!NOTE]
> ログイン後、このアプリでは "アクセス許可が不十分です" というエラーが表示されます。 デモ ディレクトリからリソースにアクセスしようとしているため、このエラーが発生します。 アクセス トークンは Azure AD ディレクトリのみに有効であるため、API 呼び出しは許可されていません。 次のチュートリアルに進み、ディレクトリ用に保護された Web API を作成してください。

## <a name="next-steps"></a>次の手順

この記事で学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure AD B2C でアプリケーションを更新する
> * アプリケーションを使用するようにサンプルを構成する
> * ユーザー フローを使用してサインアップする

> [!div class="nextstepaction"]
> [チュートリアル:Azure Active Directory B2C を使用してシングルページ アプリから ASP.NET Core Web API へのアクセスを許可する](active-directory-b2c-tutorials-spa-webapi.md)
