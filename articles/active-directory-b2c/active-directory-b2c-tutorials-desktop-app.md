---
title: チュートリアル:ネイティブのクライアント アプリケーションでユーザーを認証する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C を使用して .NET デスクトップ アプリケーションのユーザー ログインを提供する方法に関するチュートリアルです。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 54a2fe4f0eee5c926878682ea1fccdfdd005e68b
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950194"
---
# <a name="tutorial-authenticate-users-in-a-native-desktop-client-using-azure-active-directory-b2c"></a>チュートリアル:Azure Active Directory B2C を使用してネイティブ デスクトップ クライアントでユーザーを認証する

このチュートリアルでは、Azure Active Directory B2C (Azure AD B2C) を使用して Windows Presentation Foundation (WPF) デスクトップ アプリケーションでユーザーをサインインおよびサインアップする方法を紹介します。 Azure AD B2C を使用すると、アプリケーションはオープンな標準プロトコルを使用してソーシャル アカウント、エンタープライズ アカウント、Azure Active Directory アカウントに対する認証を行うことができます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ネイティブ クライアント アプリケーションを追加する
> * アプリケーションを使用するためのサンプルを構成する
> * ユーザー フローを使用してサインアップする

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

- [ユーザー フローを作成](tutorial-create-user-flows.md)してアプリケーションでのユーザー エクスペリエンスを有効にする。
- **[.NET デスクトップ開発]** および **[ASP.NET および Web の開発]** ワークロードと共に [Visual Studio 2019](https://www.visualstudio.com/downloads/) をインストールする。

## <a name="add-the-native-client-application"></a>ネイティブ クライアント アプリケーションを追加する

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

後の手順で使用するために、**アプリケーション (クライアント) ID** を記録しておきます。

## <a name="configure-the-sample"></a>サンプルの構成

このチュートリアルでは、GitHub からダウンロードできるサンプルを構成します。 サンプルの WPF デスクトップ アプリケーションでは、Azure AD B2C でのサインアップとサインインのデモが行われ、保護された Web API を呼び出すことができます。 [ZIP ファイルをダウンロード](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/msalv3.zip)するか、[リポジトリを参照](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop)するか、GitHub からサンプルを複製します。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

お使いの Azure AD B2C テナントと連携し、(既定のデモ テナントではなく) そのテナントのユーザー フローを呼び出すようにアプリケーションを更新するには:

1. Visual Studio で **active-directory-b2c-wpf** ソリューション (`active-directory-b2c-wpf.sln`) を開きます。
2. **active-directory-b2c-wpf** プロジェクトの *App.xaml.cs* ファイルを開き、次の変数定義を探します。 `{your-tenant-name}` を、ご自身の Azure AD B2C テナント名に置き換えます。また、`{application-ID}` を、前に記録したアプリケーション ID に置き換えます。

    ```csharp
    private static readonly string Tenant = "{your-tenant-name}.onmicrosoft.com";
    private static readonly string AzureAdB2CHostname = "{your-tenant-name}.b2clogin.com";
    private static readonly string ClientId = "{application-ID}";
    ```

3. 前提条件の 1 つとして作成したユーザー フローの名前を使用して、ポリシー名変数を更新します。 例:

    ```csharp
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    public static string PolicyEditProfile = "B2C_1_profileediting1";
    public static string PolicyResetPassword = "B2C_1_passwordreset1";
    ```

## <a name="run-the-sample"></a>サンプルを実行する

**F5** キーを押し、サンプルをビルドして実行します。

### <a name="sign-up-using-an-email-address"></a>メール アドレスを使用してサインアップする

1. **[サインイン]** を選択して、ユーザーとしてサインアップします。 これにより、**B2C_1_signupsignin1** ユーザー フローが使用されます。
2. Azure AD B2C によってサインイン ページが表示されます。このページには **[今すぐサインアップ]** リンクが示されています。 まだアカウントを持っていないため、 **[Sign up now]\(今すぐサインアップ\)** リンクを選択します。
3. サインアップ ワークフローによって、メール アドレスを使用してユーザーの ID を収集および確認するためのページが表示されます。 また、サインアップ ワークフローでは、ユーザー フローで定義されているユーザーのパスワードと要求された属性も収集されます。

    有効なメール アドレスを使用し、確認コードを使用して検証します。 パスワードを設定します。 要求された属性の値を入力します。

    ![サインインまたはサインアップ ワークフローの一部として表示されるサインアップ ページ](media/active-directory-b2c-tutorials-desktop-app/azure-ad-b2c-sign-up-workflow.png)

4. **[作成]** を選択して、Azure AD B2C テナントにローカル アカウントを作成します。

これでユーザーがメール アドレスを使用してサインインし、デスクトップ アプリケーションを使用できるようになりました。 サインアップまたはサインインに成功すると、WPF アプリの下部ウィンドウにトークンの詳細が表示されます。

![WPF デスクトップ アプリケーションの下部ウィンドウに表示されているトークンの詳細](media/active-directory-b2c-tutorials-desktop-app/desktop-app-01-post-signin.png)

**[API 呼び出し]** ボタンを選択すると、**エラー メッセージ** が表示されます。 アプリケーションがアクセスしようとしている API は、現在デモ テナント `fabrikamb2c.onmicrosoft.com` によって保護されているため、エラーが発生します。 お使いのアクセス トークンは、ご利用の Azure AD B2C テナントに対してのみ有効であるため、この API 呼び出しは承認されません。

次のチュートリアルに進み、保護された Web API を独自のテナントに登録し、**API 呼び出し**機能を有効にします。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * ネイティブ クライアント アプリケーションを追加する
> * アプリケーションを使用するためのサンプルを構成する
> * ユーザー フローを使用してサインアップする

次は、 **[API 呼び出し]** ボタンの機能を有効にするために、独自の Azure AD B2C テナントの登録済み Web API に WPF デスクトップ アプリケーションがアクセスできるようにします。

> [!div class="nextstepaction"]
> [チュートリアル:デスクトップ アプリから Node.js Web API へのアクセスを許可する >](active-directory-b2c-tutorials-desktop-app-webapi.md)
