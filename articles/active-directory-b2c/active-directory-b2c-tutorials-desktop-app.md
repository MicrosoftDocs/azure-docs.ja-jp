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
ms.openlocfilehash: 699dd41024bd60b2016771d728253d938fc9ab23
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347153"
---
# <a name="tutorial-enable-authentication-in-a-native-client-application-using-azure-active-directory-b2c"></a>チュートリアル:Azure Active Directory B2C を使用してネイティブ クライアント アプリケーションで認証を有効にする

このチュートリアルでは、Azure Active Directory (Azure AD) B2C を使用して Windows Presentation Foundation (WPF) デスクトップ アプリケーションでユーザーをサインインおよびサインアップする方法を紹介します。 Azure AD B2C を使用すると、アプリケーションでオープンな標準プロトコルを使用して、ソーシャル アカウント、エンタープライズ アカウント、Azure Active Directory アカウントに対する認証を行うことができます。

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

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. お使いの Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
4. **[アプリケーション]** を選択し、 **[追加]** を選択します。
5. アプリケーションの名前を入力します。 たとえば、*nativeapp1* とします。
6. **[Include web app/ web API]\(Web アプリ/Web API を含める\)** の場合、 **[いいえ]** を選択します。
7. **[ネイティブ クライアントを含める]** の場合、 **[はい]** を選択します。
8. **[リダイレクト URI]** に、カスタム スキームを含めた有効なリダイレクト URI を入力します。 リダイレクト URI を選択する際には、2 つの重要な考慮事項があります。

    - **一意** - リダイレクト URI のスキームは、すべてのアプリケーションで一意である必要があります。 `com.onmicrosoft.contoso.appname://redirect/path` の例では、`com.onmicrosoft.contoso.appname` はスキームです。 このパターンに従う必要があります。 2 つのアプリケーションで同じスキームを共有している場合、ユーザーにはアプリケーションを選択する選択肢が与えられます。 ユーザーが不適切な選択を行った場合、サインインは失敗します。
    - **完全** - リダイレクト URI には、スキームとパスが必要です。 パスには、ドメインの後に少なくとも 1 つのスラッシュを含める必要があります。 たとえば、`//contoso/` は機能しますが、`//contoso` は失敗します。 リダイレクト URI にアンダースコアなどの特殊文字は含めないようにしてください。

9. **Create** をクリックしてください。
10. プロパティ ページで、サンプルを構成するときに使用するアプリケーション ID を記録します。

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
