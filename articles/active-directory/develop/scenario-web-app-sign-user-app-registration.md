---
title: ユーザーがサインインするための Web アプリの登録 - Microsoft ID プラットフォーム | Azure
description: ユーザーがサインインするための Web アプリを登録する方法について学習します
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: b16ee98b44e52482423229a0940c8927a94d4c83
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88118673"
---
# <a name="web-app-that-signs-in-users-app-registration"></a>ユーザーをサインインさせる Web アプリ:アプリの登録

この記事では、ユーザーをサインインさせる Web アプリにおけるアプリの登録の特性について説明されています。

アプリケーションを登録するには、次を使用することができます。

- [Web アプリ クイックスタート](#register-an-app-by-using-the-quickstarts)。 アプリケーションを初めて作成するすばらしい体験に加えて、Azure portal のクイックスタートには、 **[この変更を行う]** という名前のボタンが含まれます。 既存のアプリであっても、このボタンを使用して必要なプロパティを設定できます。 これらのプロパティの値を自分のケースに適応させる必要があります。 具体的には、自分のアプリ用の Web API URL は、提案された既定値とは異なる可能性があり、URI のサインアウトにも影響があります。
- [手動でアプリケーションを登録する](#register-an-app-by-using-the-azure-portal) Azure portal。
- PowerShell とコマンドライン ツール。

## <a name="register-an-app-by-using-the-quickstarts"></a>クイックスタートを使用してアプリを登録する

これらのリンクを使用し、Web アプリケーションの作成をブートストラップできます。

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-by-using-the-azure-portal"></a>Azure portal を使用してアプリを登録する

> [!NOTE]
> 使用するポータルは、アプリケーションが Microsoft Azure パブリック クラウド、各国のクラウドまたはソブリン クラウドのいずれで実行されるかによって異なります。 詳細については、[各国のクラウド](./authentication-national-cloud.md#app-registration-endpoints)に関する記事をご覧ください。


1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。 または、[選択した各国のクラウドの Azure portal](./authentication-national-cloud.md#app-registration-endpoints) にサインインします。
2. お使いのアカウントで複数のテナントにアクセスできる場合は、右上隅で自分のアカウントをクリックします。 次に、ポータル セッションを目的の Azure Active Directory (Azure AD) テナントに変更します。
3. 左側のウィンドウで、 **[Azure Active Directory]** サービスを選択し、 **[アプリの登録]** 、 **[新規登録]** の順に選択します。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

1. **[アプリケーションの登録]** ページが表示されたら、以下のアプリケーションの登録情報を入力します。
   1. 自分のアプリケーションでサポートされているアカウントの種類を選択します。 (「[サポートされているアカウントの種類](./v2-supported-account-types.md)」を参照してください。)
   1. **[名前]** セクションに、アプリのユーザーに表示されるわかりやすいアプリケーション名を入力します。 たとえば、「**AspNetCore-WebApp**」と入力します。
   1. **[リダイレクト URI]** に、アプリケーションの種類と認証に成功した後に返されたトークンの応答を受け入れる URI の接続先を追加します。 たとえば、「 **https://localhost:44321** 」と入力します。 次に、 **[登録]** を選択します。
   ![登録](media/scenario-webapp/scenario-webapp-app-registration-1.png)
1. **[認証]** メニューを選択し、次の情報を追加します。
   1. **[応答 URL]** で、種類が **Web** の **https://localhost:44321/signin-oidc** を追加します。
   1. **[詳細設定]** セクションの **[ログアウト URL]** を「 **https://localhost:44321/signout-oidc** 」に設定します
   1. **[暗黙的な許可]** の下の **[ID トークン]** を選択します。
   1. **[保存]** を選択します。
  ![登録](media/scenario-webapp/scenario-webapp-app-registration-2.png)
 
# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

1. **[アプリケーションの登録]** ページが表示されたら、以下のアプリケーションの登録情報を入力します。
   1. 自分のアプリケーションでサポートされているアカウントの種類を選択します。 (「[サポートされているアカウントの種類](./v2-supported-account-types.md)」を参照してください。)
   1. **[名前]** セクションに、アプリのユーザーに表示されるわかりやすいアプリケーション名を入力します。 たとえば、「**MailApp-openidconnect-v2**」と入力します。
   1. **[リダイレクト URI (省略可能)]** セクションで、コンボボックスの **[Web]** を選択し、次のリダイレクト URI **https://localhost:44326/** を入力します。
1. **[登録]** を選択して、アプリケーションを作成します。
1. **[認証]** メニューを選択します。
1. **[詳細設定]** の **[暗黙的な許可]** セクションで **[ID トークン]** を選択します。 このサンプルでは、ユーザーをサインインさせるには、[暗黙的な許可フロー](v2-oauth2-implicit-grant-flow.md)を有効にする必要があります。
1. **[保存]** を選択します。

# <a name="java"></a>[Java](#tab/java)

1. **[アプリケーションの登録]** ページが表示されたら、アプリケーションの表示名を入力します。 たとえば、「**java-webapp**」と入力します。
1. **[任意の組織のディレクトリ内のアカウントと、個人用の Microsoft アカウント (Skype、Xbox、Outlook.com など)]** を選択し、 **[アプリケーションの種類]** に **[Web アプリ / API]** を選択します。
1. **[登録]** を選択し、アプリケーションを登録します。
1. 左側のメニューで、 **[認証]** を選択します。 **[リダイレクト URI]** で **[Web]** を選択します。

1. 2 つのリダイレクト URI を入力します。1 つはサインイン ページ用で、もう 1 つはグラフ ページ用です。 いずれの場合も、同じホストとポート番号を使用し、その後にサインイン ページの場合は **/msal4jsample/secure/aad** を、ユーザー情報ページの場合は **msal4jsample/graph/me** を続けます。

   既定では、このサンプルでは次のものが使用されます。

   - **http://localhost:8080/msal4jsample/secure/aad**
   - **http://localhost:8080/msal4jsample/graph/me**

  次に、 **[保存]** を選択します。

1. メニューから **[証明書とシークレット]** を選択します。
1. **[クライアント シークレット]** セクションで、 **[新しいクライアント シークレット]** を選択し、次に、

   1. キーの説明を入力します。
   1. キーの有効期間として **[1 年]** を選択します。
   1. **[追加]** を選択します。
   1. キー値が表示されたら、後で使うためにコピーしておきます。 この値は二度と表示されず、他の何らかの手段で取得することもできません。

# <a name="python"></a>[Python](#tab/python)

1. **[アプリケーションの登録]** ページが表示されたら、以下のアプリケーションの登録情報を入力します。
   1. **[名前]** セクションに、アプリのユーザーに表示されるわかりやすいアプリケーション名を入力します。 たとえば、「**python-webapp**」と入力します。
   1. **[サポートされているアカウントの種類]** を **[任意の組織のディレクトリ内のアカウントと、個人用の Microsoft アカウント (Skype、Xbox、Outlook.com など)]** に変更します。
   1. **[リダイレクト URI (省略可能)]** セクションで、コンボボックスの **[Web]** を選択し、次のリダイレクト URI **http://localhost:5000/getAToken** を入力します。
1. **[登録]** を選択して、アプリケーションを作成します。
1. アプリの **[概要]** ページで、 **[アプリケーション (クライアント) ID]** の値を見つけ、後で使用するために記録します。 これは、このプロジェクトの Visual Studio 構成ファイルを構成するために必要になります。
1. 左側のメニューで **[証明書とシークレット]** を選択します。
1. **[クライアント シークレット]** セクションで、 **[新しいクライアント シークレット]** を選択し、次に、

   1. キーの説明を入力します。
   1. キーの有効期間として **[1 年]** を選択します。
   1. **[追加]** を選択します。
   1. キー値が表示されたらコピーしておきます。 この情報は後で必要になります。
---

## <a name="register-an-app-by-using-powershell"></a>PowerShell を使用してアプリを登録する

> [!NOTE]
> 現在のところ、Azure AD PowerShell では、次のサポートされるアカウントの種類でのみアプリケーションを作成します。
>
> - MyOrg (この組織のディレクトリ内のアカウントのみ)
> - AnyOrg (任意の組織のディレクトリ内のアカウント)
>
> ユーザーが個人の Microsoft アカウント (Skype、Xbox、Outlook.com など) でサインインできるアプリケーションを作成できます。 まず、マルチテナント アプリケーションを作成します。 サポートされているアカウントの種類は、任意の組織のディレクトリ内のアカウントです。 次に、Azure portal から[アプリケーション マニフェスト](./reference-app-manifest.md)の [`accessTokenAcceptedVersion`](./reference-app-manifest.md#accesstokenacceptedversion-attribute) プロパティを **2** に、[`signInAudience`](./reference-app-manifest.md#signinaudience-attribute) プロパティを `AzureADandPersonalMicrosoftAccount` に変更します。 詳細については、ASP.NET Core チュートリアルの「[手順 1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant)」を参照してください。 この手順は一般化し、あらゆる言語の Web アプリに適用できます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [アプリのコード構成](scenario-web-app-sign-user-app-configuration.md)