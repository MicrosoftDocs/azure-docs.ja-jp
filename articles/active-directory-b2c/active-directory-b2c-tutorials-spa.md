---
title: チュートリアル - シングルページ アプリケーションで認証を有効にする - Azure Active Directory B2C
description: シングル ページ アプリケーション (JavaScript) で Azure Active Directory B2C を使用してユーザー ログインを提供する方法を学習します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/08/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 496cf801a44638af61306b43791abce9466e2cb2
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835679"
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

このチュートリアルの手順を続ける前に、次の Azure AD B2C リソースを用意しておく必要があります。

* [Azure AD B2C テナント](tutorial-create-tenant.md)
* ご利用のテナントで[登録されているアプリケーション](tutorial-register-applications.md)
* ご利用のテナントで[作成したユーザー フロー](tutorial-create-user-flows.md)

さらに、ご利用のローカル開発環境には次のものが必要です。

* コード エディター。例: [Visual Studio Code](https://code.visualstudio.com/) または [Visual Studio 2019](https://www.visualstudio.com/downloads/)
* [.NET Core SDK 2.0.0](https://www.microsoft.com/net/core) 以降
* [Node.JS](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>アプリケーションの更新

前提条件の一環として完了した 2 番目のチュートリアルで、Azure AD B2C に Web アプリケーションを登録しました。 チュートリアルのサンプルとの通信を有効にするには、Azure AD B2C のアプリケーションにリダイレクト URI を 追加する必要があります。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. お使いの Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリケーション]** を選択し、*webapp1* アプリケーションを選択します。
1. **[応答 URL]** に「`http://localhost:6420`」を追加します。
1. **[保存]** を選択します。
1. プロパティ ページで、**アプリケーション ID** をメモします。 このアプリ ID は、後の手順でシングル ページ Web アプリケーションのコードを更新する際に使用します。

## <a name="get-the-sample-code"></a>サンプル コードの取得

このチュートリアルでは、GitHub からダウンロードするコード サンプルを構成します。 そのサンプルでは、シングルページ アプリケーションでユーザーのサインアップおよびサインイン、保護された Web API の呼び出しに Azure AD B2C を使用する方法が示されています。

[ZIP ファイルをダウンロード](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip)するか、GitHub からサンプルを複製します。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>サンプルを更新する

サンプルを入手したので、ご利用の Azure AD B2C テナント名と前の手順でメモしたアプリケーション ID を使用してコードを更新します。

1. サンプル ディレクトリのルートにある `index.html` ファイルを開きます。
1. `msalConfig` 定義内で **clientId** 値を、前の手順でメモしたアプリケーション ID を使用して変更します。 次に、**機関**の URI 値を、ご利用の Azure AD B2C テナント名を使用して更新します。 また、前提条件の 1 つで作成したサインアップ/サインイン ユーザー フローの名前 (たとえば、*B2C_1_signupsignin1*) を使用して URI を更新します。

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "00000000-0000-0000-0000-000000000000", //This is your client ID
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi", //This is your tenant info
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    このチュートリアルで使用されているユーザー フローの名前は、**B2C_1_signupsignin1** です。 別のユーザー フロー名を使用している場合は、その名前を `authority` 値に指定します。

## <a name="run-the-sample"></a>サンプルを実行する

1. コンソール ウィンドウを開き、サンプルを含むディレクトリに変更します。 例:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. 次のコマンドを実行します。

    ```
    npm install && npm update
    node server.js
    ```

    コンソール ウィンドウには、ローカルで稼働中の Node.js サーバーのポート番号が表示されます。

    ```
    Listening on port 6420...
    ```

1. アプリケーションを表示するには、ブラウザーで `http://localhost:6420` に移動します。

サンプルでは、サインアップ、サインイン、プロファイルの編集、パスワードのリセットがサポートされています。 このチュートリアルでは、ユーザーがメール アドレスを使用してサインアップを行う方法に焦点を当てます。

### <a name="sign-up-using-an-email-address"></a>メール アドレスを使用してサインアップする

1. **[Login]\(ログイン\)** をクリックし、アプリケーションのユーザーとしてサインアップします。 これにより、前の手順で指定した **B2C_1_signupsignin1** ユーザー フローが使用されます。
1. Azure AD B2C によって、サインアップ リンクを含むサインイン ページが表示されます。 まだアカウントを持っていないため、 **[今すぐサインアップ]** リンクをクリックします。
1. サインアップ ワークフローによって、メール アドレスを使用してユーザーの ID を収集および確認するためのページが表示されます。 また、サインアップ ワークフローでは、ユーザー フローで定義されているユーザーのパスワードと要求された属性も収集されます。

    有効なメール アドレスを使用し、確認コードを使用して検証します。 パスワードを設定します。 要求された属性の値を入力します。

    ![サインインまたはサインアップ ユーザー フローによって表示されるサインアップ ページ](./media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.PNG)

1. **[作成]** をクリックして、Azure AD B2C ディレクトリにローカル アカウントを作成します。

**[作成]** をクリックすると、サインアップ ページが閉じてサインイン ページが再び表示されます。

これで、ご利用のメール アドレスとパスワードを使用してアプリケーションにサインインできるようになりました。

### <a name="error-insufficient-permissions"></a>エラー: アクセス許可が不十分です

サインイン後、このアプリでは、アクセス許可が不十分であることを示すエラーが表示されます - これには次のことが**予想されます**。

`ServerError: AADB2C90205: This application does not have sufficient permissions against this web resource to perform the operation.`

demo ディレクトリからリソースにアクセスを試みていますが、ご利用のアクセス トークンはご自分の Azure AD ディレクトリに対してしか有効ではないため、このエラーが発生します。 したがって、API 呼び出しは承認されません。

このシリーズの次のチュートリアルに進み ([次の手順](#next-steps)を参照)、ご利用のディレクトリ用に保護された Web API を作成してください。

## <a name="next-steps"></a>次の手順

この記事で学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure AD B2C でアプリケーションを更新する
> * アプリケーションを使用するようにサンプルを構成する
> * ユーザー フローを使用してサインアップする

それでは、このシリーズの次のチュートリアルに進んで、SPA から保護された Web API へのアクセスを許可します。

> [!div class="nextstepaction"]
> [チュートリアル:Azure Active Directory B2C を使用してシングルページ アプリから ASP.NET Core Web API へのアクセスを許可する](active-directory-b2c-tutorials-spa-webapi.md)
