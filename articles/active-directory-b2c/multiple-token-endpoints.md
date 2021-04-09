---
title: OWIN ベースの Web API を b2clogin.com またはカスタム ドメインに移行する
titleSuffix: Azure AD B2C
description: アプリケーションを b2clogin.com に移行するときに、複数のトークン発行者によって発行されたトークンを .NET Web API でサポートできるようにする方法について学習します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 860f167913211ee7c511e515937f29ba5bf954cf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491571"
---
# <a name="migrate-an-owin-based-web-api-to-b2clogincom-or-a-custom-domain"></a>OWIN ベースの Web API を b2clogin.com またはカスタム ドメインに移行する

この記事では、[Open Web Interface for .NET (OWIN)](http://owin.org/) が実装されている Web API で複数のトークン発行者のサポートを有効にする方法について説明します。 複数のトークン エンドポイントのサポートは、Azure Active Directory B2C (Azure AD B2C) API とそのアプリケーションをあるドメインから別のドメインに移行する場合に便利です。 たとえば、*login.microsoftonline.com* から *b2clogin.com*、または[カスタム ドメイン](custom-domain.md)に移行するような場合です。

b2clogin.com、login.microsoftonline.com、またはカスタム ドメインによって発行されたトークンを受け入れるように API にサポートを追加することにより、Web アプリケーションを段階的に移行してから、login.microsoftonline.com で発行されたトークンのサポートを API から削除することができます。

以下のセクションでは、[Microsoft OWIN][katana] ミドルウェア コンポーネント (Katana) を使用する Web API で複数の発行者を有効にする方法の例について示します。 コード例は Microsoft OWIN ミドルウェアに固有のものですが、一般的な手法は他の OWIN ライブラリにも適用できます。

## <a name="prerequisites"></a>前提条件

この記事の手順を続ける前に、次の Azure AD B2C リソースを用意しておく必要があります。

* お使いのテナントに作成された[ユーザー フロー](tutorial-create-user-flows.md)または[カスタム ポリシー](custom-policy-get-started.md)

## <a name="get-token-issuer-endpoints"></a>トークン発行者のエンドポイントを取得する

まず、API でサポートする各発行者に対するトークン発行者エンドポイントの URI を取得する必要があります。 Azure AD B2C テナントによってサポートされている *b2clogin.com* エンドポイントと *login.microsoftonline.com* エンドポイントを取得するには、Azure portal で次の手順を使用します。

まず、既存のユーザー フローのいずれかを選択します。

1. [Azure portal](https://portal.azure.com) で、自分の Azure AD B2C テナントに移動します。
1. **[ポリシー]** で **[ユーザー フロー (ポリシー)]** を選択します
1. 既存のポリシー (*B2C_1_signupsignin1* など) を選択し、 **[ユーザー フローを実行します]** を選択します
1. ページの上部近くにある **[ユーザー フローを実行します]** の見出しの下で、ハイパーリンクを選択して、そのユーザー フローの OpenID Connect 検出エンドポイントに移動します。

    ![Azure portal の今すぐ実行ページの既知の URI ハイパーリンク](media/multi-token-endpoints/portal-01-policy-link.png)

1. ブラウザーに表示されるページで、`issuer` の値を記録します。次に例を示します。

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

1. **[ドメインの選択]** ドロップダウンを使って他のドメインを選択し、前の 2 つのステップを再度実行して、その `issuer` の値を記録します。

次のような 2 つの URI が記録されるはずです。

```
https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
```

### <a name="custom-policies"></a>カスタム ポリシー

ユーザー フローではなくカスタム ポリシーを使っている場合は、同様のプロセスを使って発行者の URI を取得できます。

1. Azure AD B2C テナントに移動します
1. **[Identity Experience Framework]** を選択します
1. 証明書利用者ポリシーの 1 つを選択します (たとえば *B2C_1A_signup_signin*)
1. **[ドメインの選択]** ドロップダウンを使って、ドメインを選択します (*yourtenant.b2clogin.com* など)
1. **[OpenID Connect 検出エンドポイント]** の下に表示されるハイパーリンクを選択します
1. `issuer` の値を記録します
1. 他のドメインに対してステップ 4 から 6 を実行します (*login.microsoftonline.com* など)

## <a name="get-the-sample-code"></a>サンプル コードの取得

両方のトークン エンドポイント URI が手に入ったので、コードを更新して、両方のエンドポイントが有効な発行者であることを指定する必要があります。 例の手順を確認するには、サンプル アプリケーションをダウンロードまたは複製し、次にサンプルを更新して、両方のエンドポイントを有効な発行者としてサポートします。

アーカイブ [active-directory-b2c-dotnet-webapp-and-webapi-master.zip][sample-archive] をダウンロードします

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="enable-multiple-issuers-in-web-api"></a>Web API で複数の発行者を有効にする

このセクションでは、コードを更新し、両方のトークン発行者エンドポイントが有効であることを指定します。

1. Visual Studio で **B2C-WebAPI-DotNet.sln** ソリューションを開きます
1. **TaskService** プロジェクトの *TaskService\\App_Start\\ **Startup.Auth.cs**.* ファイルをエディターで開きます。
1. 次の `using` ディレクティブをファイルの先頭に追加します。

    `using System.Collections.Generic;`
1. [`ValidIssuers`][validissuers] プロパティを [`TokenValidationParameters`][tokenvalidationparameters] の定義に追加し、前のセクションで記録した両方の URI を指定します。

    ```csharp
    TokenValidationParameters tvps = new TokenValidationParameters
    {
        // Accept only those tokens where the audience of the token is equal to the client ID of this app
        ValidAudience = ClientId,
        AuthenticationType = Startup.DefaultPolicy,
        ValidIssuers = new List<string> {
            "https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/",
            "https://{your-b2c-tenant}.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/"//,
            //"https://your-custom-domain/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/"
        }
    };
    ```

`TokenValidationParameters` は MSAL.NET によって提供され、*Startup.Auth.cs* のコードの次のセクションで OWIN ミドルウェアによって使用されます。 複数の有効な発行者を指定すると、OWIN アプリケーションのパイプラインでは、両方のトークン エンドポイントが有効な発行者であることが認識されます。

```csharp
app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
{
    // This SecurityTokenProvider fetches the Azure AD B2C metadata &  from the OpenID Connect metadata endpoint
    AccessTokenFormat = new JwtFormat(tvps, new tCachingSecurityTokenProvider(String.Format(AadInstance, ultPolicy)))
});
```

前に説明したように、他の OWIN ライブラリでは、通常、複数の発行者をサポートするために同様の機能が提供されています。 すべてのライブラリの例を提供することはこの記事の範囲外ですが、ほとんどのライブラリで同様の手法を使用できます。

## <a name="switch-endpoints-in-web-app"></a>Web アプリでエンドポイントを切り替える

Web API で両方の URI がサポートされるようになったので、今度は b2clogin.com エンドポイントからトークンを取得するように、Web アプリケーションを更新する必要があります。

たとえば、_ *TaskWebApp** プロジェクトの* TaskWebApp\\**Web.config** _ ファイルで `ida:AadInstance` の値を変更することにより、新しいエンドポイントを使うようにサンプル Web アプリケーションを構成できます。

TaskWebApp の *Web.config* の `ida:AadInstance` の値を、`login.microsoftonline.com` ではなく `{your-b2c-tenant-name}.b2clogin.com` を参照するように変更します。

前:

```xml
<!-- Old value -->
<add key="ida:AadInstance" value="https://login.microsoftonline.com/tfp/{0}/{1}" />
```

変更後 (`{your-b2c-tenant}` は実際の B2C テナントの名前に置き換えます):

```xml
<!-- New value -->
<add key="ida:AadInstance" value="https://{your-b2c-tenant}.b2clogin.com/tfp/{0}/{1}" />
```

Web アプリの実行中にエンドポイントの文字列が作成されるとき、b2clogin.com ベースのエンドポイントを使ってトークンが要求されます。

カスタム ドメインを使用しているとき:

```xml
<!-- Custom domain -->
<add key="ida:AadInstance" value="https://custom-domain/{0}/{1}" />
```

## <a name="next-steps"></a>次のステップ

この記事では、複数の発行者エンドポイントからトークンを受け入れるために、Microsoft OWIN ミドルウェア (Katana) を実装する Web API を構成する方法について説明しました。 お気付きかもしれませんが、TaskService プロジェクトと TaskWebApp プロジェクトの *Web.Config* ファイルには、独自のテナントに対してプロジェクトをビルドして実行するときに変更する必要のある文字列が、他にもいくつかあります。 それらの動作を確認する場合はプロジェクトを適切に変更してかまいませんが、その完全なチュートリアルはこの記事の範囲外です。

Azure AD B2C によって生成されるさまざまな種類のセキュリティ トークンについて詳しくは、「[Azure Active Directory B2C のトークンの概要](tokens-overview.md)」をご覧ください。

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip
[sample-repo]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi

<!-- LINKS - Internal -->
[katana]: /aspnet/aspnet/overview/owin-and-katana/
[validissuers]: /dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters.validissuers
[tokenvalidationparameters]: /dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters
