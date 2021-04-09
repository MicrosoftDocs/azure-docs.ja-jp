---
title: 'クイックスタート: シングルページ アプリ (SPA) のサインインの設定'
titleSuffix: Azure AD B2C
description: このクイックスタートでは、Azure Active Directory B2C を使用してアカウント サインインを提供するサンプル シングルページ アプリケーションを実行します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.date: 04/04/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6471d1b5a5ad2b8ba34080ae1220872fa0e2e232
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93421058"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>クイックスタート: Azure Active Directory B2C を使用したシングルページ アプリのサインインの設定

Azure Active Directory B2C (Azure AD B2C) は、アプリケーション、ビジネス、顧客を保護するためのクラウド ID 管理を提供します。 Azure AD B2C に対応したアプリケーションは、オープンな標準プロトコルを使用し、ソーシャル アカウントやエンタープライズ アカウントで認証を行うことができます。 このクイック スタートでは、シングル ページ アプリケーションにソーシャル ID プロバイダーを使ってサインインし、Azure AD B2C で保護された Web API を呼び出します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

- [Visual Studio Code](https://code.visualstudio.com/)
- [Node.js](https://nodejs.org/en/download/)
- Facebook、Google、または Microsoft のソーシャル アカウント
- GitHub からのコード サンプル: [ms-identity-b2c-javascript-spa](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa)

    [zip アーカイブをダウンロードする](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/archive/main.zip)か、リポジトリを複製することができます。

    ```console
    git clone https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa.git
    ```

## <a name="run-the-application"></a>アプリケーションの実行

1. Node.js のコマンド プロンプトから次のコマンドを実行してサーバーを起動します。

    ```console
    npm install && npm update
    npm start
    ```

    *server.js* によって起動されたサーバーが、リッスンしているポートを表示します。

    ```console
    Listening on port 6420...
    ```

1. ブラウザーでアプリケーションの URL にアクセスします。 たとえば、「 `http://localhost:6420` 」のように入力します。

    ![ブラウザーに表示されたシングルページ アプリケーションのサンプル アプリ](./media/quickstart-single-page-app/sample-app-spa.png)

## <a name="sign-in-using-your-account"></a>自分のアカウントを使用してサインインする

1. **[サインイン]** を選択して、ユーザー体験を開始します。
1. Azure AD B2C には、サンプルの Web アプリケーション用に "Fabrikam" という名前の架空の会社のサインイン ページが用意されています。 ソーシャル ID プロバイダーを使用してサインアップするには、使用する ID プロバイダーのボタンを選択します。

    ![ID プロバイダー ボタンが表示されたサインインまたはサインアップ ページ](./media/quickstart-single-page-app/sign-in-or-sign-up-spa.png)

    ユーザーは、ソーシャル アカウントの資格情報を使用して認証 (サインイン) し、アプリケーションがそのソーシャル アカウントから情報を読み取ることを承認します。 アクセスを許可することにより、アプリケーションはソーシャル アカウントからプロファイル情報 (名前やお住まいの都市など) を取得できるようになります。

1. ID プロバイダーのサインイン プロセスを完了します。

## <a name="access-a-protected-api-resource"></a>保護された API リソースにアクセスする

**[Call API]\(API の呼び出し\)** を選択すると、Web API から表示名が JSON オブジェクトとして返されます。

![Web API の応答が表示されているブラウザーのサンプル アプリケーション](./media/quickstart-single-page-app/call-api-spa.png)

このシングルページ アプリケーション サンプルは、保護された Web API リソースへの要求にアクセス トークンを追加します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

他の Azure AD B2C クイックスタートやチュートリアルを試す場合は、Azure AD B2C テナントを使用できます。 不要になったら、[Azure AD B2C テナントを削除する](faq.md#how-do-i-delete-my-azure-ad-b2c-tenant)ことができます。

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、サンプルのシングルページ アプリケーションを使用して次のことを行いました。

- ソーシャル ID プロバイダーを使用してサインインする
- Azure AD B2C ユーザー アカウントを作成する (サインイン時に自動的に作成されます)
- Azure AD B2C によって保護された Web API を呼び出す

独自の Azure AD B2C テナントを作成してみましょう。

> [!div class="nextstepaction"]
> [Azure Portal で Azure Active Directory B2C テナントを作成する](tutorial-create-tenant.md)
