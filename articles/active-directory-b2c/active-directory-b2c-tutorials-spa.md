---
title: チュートリアル:シングルページ アプリで認証を行う
titleSuffix: Azure AD B2C
description: このチュートリアルでは、Azure Active Directory B2C を使用して、JavaScript ベースのシングルページ アプリケーション (SPA) にユーザー ログインを提供する方法を学習します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 10/14/2019
ms.custom: mvc, seo-javascript-september2019
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 2f18a7ddeb5256c660c6458fc8d2d29f83e04429
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950165"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-using-azure-active-directory-b2c-azure-ad-b2c"></a>チュートリアル:Azure Active Directory B2C (Azure AD B2C) を使用してシングルページ アプリケーションで認証を有効にする

このチュートリアルでは、Azure Active Directory B2C (Azure AD B2C) を使用してシングルページ アプリケーション (SPA) でユーザーをサインインおよびサインアップする方法を説明します。 Azure AD B2C を使用すると、アプリケーションはオープンな標準プロトコルを使用してソーシャル アカウント、エンタープライズ アカウント、Azure Active Directory アカウントに対する認証を行うことができます。

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
* [.NET Core SDK 2.2](https://dotnet.microsoft.com/download) 以降
* [Node.JS](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>アプリケーションの更新

前提条件の一環として完了した 2 番目のチュートリアルで、Azure AD B2C に Web アプリケーションを登録しました。 チュートリアルのサンプルとの通信を有効にするには、Azure AD B2C のアプリケーションにリダイレクト URI を 追加する必要があります。

現在の**アプリケーション** エクスペリエンス、または新しく統合された**アプリの登録 (プレビュー)** エクスペリエンスを使用して、アプリケーションを更新できます。 [この新しいエクスペリエンスの詳細を参照してください](https://aka.ms/b2cappregintro)。

#### <a name="applicationstabapplications"></a>[アプリケーション](#tab/applications/)

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリケーション]** を選択し、*webapp1* アプリケーションを選択します。
1. **[応答 URL]** に「`http://localhost:6420`」を追加します。
1. **[保存]** を選択します。
1. プロパティ ページで、**アプリケーション ID** をメモします。 このアプリ ID は、後の手順でシングル ページ Web アプリケーションのコードを更新する際に使用します。

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[アプリの登録 (プレビュー)](#tab/app-reg-preview/)

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリの登録 (プレビュー)]** 、 **[所有しているアプリケーション]** タブ、 *[webapp1]* アプリケーションの順に選択します。
1. **[認証]** 、 **[新しいエクスペリエンスを試す]** (表示されている場合) の順に選択します。
1. **[Web]** で **[URI の追加]** リンクを選択し、「`http://localhost:6420`」と入力して、 **[保存]** を選択します。
1. **[概要]** を選択します。
1. 単一ページの Web アプリケーションでコードを更新する場合は、後の手順で使用するために**アプリケーション (クライアント) ID** をメモしておきます。

* * *

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

> [!WARNING]
> サインアップまたはサインインすると、["アクセス許可が不十分です" というエラー](#error-insufficient-permissions)が表示されることがあります。 コード サンプルの現在の実装では、このエラーが発生する可能性があります。 この問題は、今後のバージョンのコード サンプルで解決される予定であり、その際にこの警告は削除されます。

1. **[Login]\(ログイン\)** を選択して、前の手順で指定した *B2C_1_signupsignin1* ユーザー フローを開始します。
1. Azure AD B2C によって、サインアップ リンクを含むサインイン ページが表示されます。 まだアカウントを持っていないため、 **[Sign up now]\(今すぐサインアップ\)** リンクを選択します。
1. サインアップ ワークフローによって、メール アドレスを使用してユーザーの ID を収集および確認するためのページが表示されます。 また、サインアップ ワークフローでは、ユーザー フローで定義されているユーザーのパスワードと要求された属性も収集されます。

    有効なメール アドレスを使用し、確認コードを使用して検証します。 パスワードを設定します。 要求された属性の値を入力します。

    ![サインインまたはサインアップ ユーザー フローによって表示されるサインアップ ページ](./media/active-directory-b2c-tutorials-spa/azure-ad-b2c-sign-up-workflow.png)

1. **[作成]** を選択して、Azure AD B2C ディレクトリにローカル アカウントを作成します。

**[作成]** を選択すると、サインアップ ページが閉じてサインイン ページが再び表示されます。

これで、ご利用のメール アドレスとパスワードを使用してアプリケーションにサインインできるようになりました。

### <a name="error-insufficient-permissions"></a>エラー: アクセス許可が不十分です

サインインした後、アプリケーションから "アクセス許可が不十分です" というエラーが返されることがあります。

```Output
ServerError: AADB2C90205: This application does not have sufficient permissions against this web resource to perform the operation.
Correlation ID: ce15bbcc-0000-0000-0000-494a52e95cd7
Timestamp: 2019-07-20 22:17:27Z
```

このエラーが発生するのは、Web アプリケーションがデモ用ディレクトリ *fabrikamb2c* によって保護されている Web API にアクセスしようとしているためです。 アクセス トークンはご利用の Azure AD ディレクトリのみに有効であるため、この API 呼び出しは承認されません。

このエラーを修正するには、このシリーズの次のチュートリアルに進み (「[次の手順](#next-steps)」を参照)、ご利用のディレクトリ用に保護された Web API を作成してください。

## <a name="next-steps"></a>次の手順

この記事で学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure AD B2C でアプリケーションを更新する
> * アプリケーションを使用するようにサンプルを構成する
> * ユーザー フローを使用してサインアップする

それでは、このシリーズの次のチュートリアルに進んで、SPA から保護された Web API へのアクセスを許可します。

> [!div class="nextstepaction"]
> [チュートリアル:Azure AD B2C を使用して SPA から ASP.NET Core Web API へのアクセスを許可する](active-directory-b2c-tutorials-spa-webapi.md)
