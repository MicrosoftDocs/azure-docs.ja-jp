---
title: チュートリアル - ネイティブ クライアント アプリケーションで認証を有効にする - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C を使用して .NET デスクトップ アプリケーションのユーザー ログインを提供する方法に関するチュートリアルです。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 3740a032db6ca9fd0fb88ce348610684d9f895bc
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326326"
---
# <a name="tutorial-enable-authentication-in-a-native-client-application-using-azure-active-directory-b2c"></a>チュートリアル:Azure Active Directory B2C を使用してネイティブ クライアント アプリケーションで認証を有効にする

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

後の手順で使用するために、**アプリケーション ID** を記録しておきます。

## <a name="configure-the-sample"></a>サンプルの構成

このチュートリアルでは、GitHub からダウンロードできるサンプルを構成します。 サンプルの WPF デスクトップ アプリケーションでは、Azure AD B2C でのサインアップ、サインイン、および保護された Web API の呼び出しのデモが行われます。 [ZIP ファイルをダウンロード](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip)するか、[リポジトリを参照](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop)するか、GitHub からサンプルを複製します。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

アプリの設定を変更するには、`<your-tenant-name>` を自分のテナント名に置き換え、`<application-ID`> を記録したアプリケーション ID に置き換えます。

1. Visual Studio で `active-directory-b2c-wpf` ソリューションを開きます。
2. `active-directory-b2c-wpf` プロジェクトの **App.xaml.cs** ファイルを開き、次の更新を行います。

    ```csharp
    private static string Tenant = "<your-tenant-name>.onmicrosoft.com";
    private static string ClientId = "<application-ID>";
    ```

3. **PolicySignUpSignIn** 変数を、作成したユーザー フローの名前で更新します。

    ```csharp
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>サンプルを実行する

**F5** キーを押し、サンプルをビルドして実行します。

### <a name="sign-up-using-an-email-address"></a>メール アドレスを使用してサインアップする

1. **[サインイン]** をクリックし、ユーザーとしてサインアップします。 これにより、**B2C_1_signupsignin1** ユーザー フローが使用されます。
2. Azure AD B2C によって、サインアップ リンクを含むサインイン ページが表示されます。 まだアカウントを持っていないため、 **[今すぐサインアップ]** リンクをクリックします。
3. サインアップ ワークフローによって、メール アドレスを使用してユーザーの ID を収集および確認するためのページが表示されます。 また、サインアップ ワークフローでは、ユーザー フローで定義されているユーザーのパスワードと要求された属性も収集されます。

    有効なメール アドレスを使用し、確認コードを使用して検証します。 パスワードを設定します。 要求された属性の値を入力します。

    ![サインインまたはサインアップ ワークフローの一部として表示されるサインアップ ページ](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.PNG)

4. **[作成]** をクリックして、Azure AD B2C テナントにローカル アカウントを作成します。

これで、ユーザーはメール アドレスを使用してサインインし、デスクトップ アプリを使用できるようになりました。

> [!NOTE]
> **[Call API]** ボタンをクリックすると、"許可されていません" というエラーが表示されます。 デモ テナントからリソースにアクセスしようとしているため、このエラーが発生します。 アクセス トークンは Azure AD テナントのみに有効であるため、API 呼び出しは許可されていません。 次のチュートリアルに進み、テナント用に保護された Web API を作成してください。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * ネイティブ クライアント アプリケーションを追加する
> * アプリケーションを使用するためのサンプルを構成する
> * ユーザー フローを使用してサインアップする

> [!div class="nextstepaction"]
> [チュートリアル:Azure Active Directory B2C を使用してデスクトップ アプリから Node.js Web API へのアクセスを許可する](active-directory-b2c-tutorials-spa-webapi.md)
