---
title: チュートリアル - Web アプリケーションで認証を有効にする - Azure Active Directory B2C | Microsoft Docs
description: ASP.NET Web アプリケーションで Azure Active Directory B2C を使用してユーザー ログインを提供する方法に関するチュートリアル。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 041bcf32035ab6cdc3ee4df06050f75186759f5e
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835648"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>チュートリアル:Azure Active Directory B2C を使用して Web アプリケーションで認証を有効にする

このチュートリアルでは、Azure Active Directory (Azure AD) B2C を使用して ASP.NET Web アプリケーションでユーザーをサインインおよびサインアップする方法を紹介します。 Azure AD B2C を使用すると、アプリケーションはオープンな標準プロトコルを使用してソーシャル アカウント、エンタープライズ アカウント、Azure Active Directory アカウントに対する認証を行うことができます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure AD B2C でアプリケーションを更新する
> * アプリケーションを使用するようにサンプルを構成する
> * ユーザー フローを使用してサインアップする

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

- [ユーザー フローを作成](tutorial-create-user-flows.md)してアプリケーションでのユーザー エクスペリエンスを有効にする。
- **[ASP.NET および Web の開発]** ワークロードと共に [Visual Studio 2019](https://www.visualstudio.com/downloads/) をインストールする。

## <a name="update-the-application"></a>アプリケーションの更新

前提条件の一環として完了したチュートリアルで、Azure AD B2C に Web アプリケーションを追加しました。 このチュートリアルのサンプルとの通信を可能にするには、Azure AD B2C 内のそのアプリケーションにリダイレクト URI を追加する必要があります。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. お使いの Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
4. **[アプリケーション]** を選択し、*webapp1* アプリケーションを選択します。
5. **[応答 URL]** に「`https://localhost:44316`」を追加します。
6. **[保存]** を選択します。
7. プロパティ ページで、アプリケーション ID を記録しておきます。これは、Web アプリケーションを構成するときに使用します。
8. **[キー]** 、 **[キーの生成]** 、 **[保存]** の順に選択します。 Web アプリケーションの構成時に使用するキーを書き留めておきます。

## <a name="configure-the-sample"></a>サンプルの構成

このチュートリアルでは、GitHub からダウンロードできるサンプルを構成します。 このサンプルは ASP.NET を使用して簡単な To Do リストを提供します。 このサンプルでは、[Microsoft OWIN ミドルウェア コンポーネント](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/)が使用されています。 [ZIP ファイルをダウンロード](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip)するか、GitHub からサンプルを複製します。 サンプル ファイルは必ず、パスの長さが合計 260 文字未満のフォルダーに展開してください。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

サンプル ソリューションには次の 2 つのプロジェクトが含まれています。

- **TaskWebApp** - タスク一覧を作成および編集します。 このサンプルでは、**サインアップまたはサインイン**のユーザー フローを使用してユーザーのサインアップまたはサインインを実行します。
- **TaskService** - タスク リストの作成、読み取り、更新、削除の機能をサポートします。 この API は Azure AD B2C によって保護されており、TaskWebApp によって呼び出されます。

このサンプルは、ご利用のテナントに登録されているアプリケーションを使用するように変更します。これには、以前書き留めておいたアプリケーション ID とキーが含まれます。 また、作成したユーザー フローも構成します。 このサンプルでは、Web.config ファイルの設定として構成値を定義します。 設定を変更するには:

1. Visual Studio で **B2C-WebAPI-DotNet** ソリューションを開きます。
2. **TaskWebApp** プロジェクトの **Web.config** ファイルを開きます。 `ida:Tenant` の値は、実際に作成したテナントの名前に置き換えます。 `ida:ClientId` の値は、実際に記録したアプリケーション ID に置き換えます。 `ida:ClientSecret` の値は、実際に記録したキーに置き換えます。
3. **Web.config** ファイルで、`ida:SignUpSignInPolicyId` の値を `b2c_1_signupsignin1` に置き換えます。 `ida:EditProfilePolicyId` の値を `b2c_1_profileediting1` に置き換えます。 `ida:ResetPasswordPolicyId` の値を `b2c_1_passwordreset1` に置き換えます。

## <a name="run-the-sample"></a>サンプルを実行する

1. ソリューション エクスプローラーで、**TaskWebApp** プロジェクトを右クリックし、 **[スタートアップ プロジェクトに設定]** をクリックします。
2. **F5**キーを押します。 既定のブラウザーで、ローカルの Web サイトアドレス `https://localhost:44316/` が開かれます。

### <a name="sign-up-using-an-email-address"></a>メール アドレスを使用してサインアップする

1. **[Sign up / Sign in]\(サインアップ/サインイン\)** をクリックして、アプリケーションのユーザーとしてサインアップします。 **b2c_1_signupsignin1** ユーザー フローが使用されます。
2. Azure AD B2C によって、サインアップ リンクを含むサインイン ページが表示されます。 まだアカウントを持っていないため、 **[今すぐサインアップ]** を選択します。 サインアップ ワークフローによって、メール アドレスを使用してユーザーの ID を収集および確認するためのページが表示されます。 また、サインアップ ワークフローでは、ユーザー フローで定義されているユーザーのパスワードと要求された属性も収集されます。
3. 有効なメール アドレスを使用し、確認コードを使用して検証します。 パスワードを設定します。 要求された属性の値を入力します。

    ![サインインまたはサインアップ ワークフローの一部として表示されるサインアップ ページ](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.PNG)

4. **[作成]** をクリックして、Azure AD B2C テナントにローカル アカウントを作成します。

これで、ユーザーはメールアドレスを使用してサインインし、Web アプリケーションを使用できるようになりました。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Azure AD B2C でアプリケーションを更新する
> * アプリケーションを使用するようにサンプルを構成する
> * ユーザー フローを使用してサインアップする

> [!div class="nextstepaction"]
> [チュートリアル:Azure Active Directory B2C を使用して ASP.NET Web API を保護する](active-directory-b2c-tutorials-web-api.md)
