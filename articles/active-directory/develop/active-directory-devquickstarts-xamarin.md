---
title: "Azure AD Xamarin の概要 | Microsoft Docs"
description: "Azure AD と連携して、OAuth を使用してサインインし、Azure AD で保護された API を呼び出すことができる、Xamarin アプリケーションを構築します。"
services: active-directory
documentationcenter: xamarin
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 198cd2c3-f7c8-4ec2-b59d-dfdea9fe7d95
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 9c40d273f102339fac8ea633c870b869d10106a7
ms.openlocfilehash: ccbc051f49220e824782ed4831a31ab1a716570a
ms.lasthandoff: 02/17/2017


---
# <a name="integrate-azure-ad-with-xamarin-apps"></a>Azure AD と Xamarin アプリの統合
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Xamarin を使用すると、iOS、Android、Windows (モバイル デバイスと PC) で実行できるモバイル アプリを C# で作成できます。 Xamarin を使用してアプリを開発する場合、Azure Active Directory (Azure AD) を利用すると、Azure AD アカウントを使ってユーザーを容易に認証できます。 また、Xamarin アプリでは Azure AD によって保護された任意の Web API (Office 365 API や Azure API など) を安全に使用できます。

Xamarin アプリから、保護されたリソースにアクセスする必要がある場合、Azure AD の Active Directory 認証ライブラリ (ADAL) を利用できます。 ADAL の唯一の目的は、アプリがアクセス トークンを容易に取得できるようにすることです。 この記事では ADAL を簡単に利用できることを示すために、次のような DirectorySearcher アプリを作成する方法を説明します。

* iOS、Android、Windows デスクトップ、Windows Phone、Windows ストアで動作する。
* 1 つのポータブル クラス ライブラリ (PCL) を使用してユーザーを認証し、Azure AD Graph API 用のトークンを取得する。
* 指定された UPN を持つユーザーをディレクトリで検索する。

## <a name="before-you-get-started"></a>開始する前に
* [スケルトン プロジェクトをダウンロード](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip)するか、または[完全なサンプル](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip)をダウンロードします。 どちらのダウンロードも Visual Studio 2013 ソリューションです。
* ユーザーを作成し、アプリを登録するための Azure AD テナントも必要です。 テナントを所有していない場合は、「 [How to get an Azure Active Directory tenant (Azure Active Directory テナントの取得方法)](active-directory-howto-tenant.md)」を参照して取得してください。

準備ができたら、次の&4; つのセクションで説明されている手順を実行します。

## <a name="step-1-set-up-your-xamarin-development-environment"></a>手順 1: Xamarin の開発環境を設定する
このチュートリアルには iOS、Android、Windows 用のプロジェクトが含まれるため、Visual Studio と Xamarin の両方が必要です。 必要な環境を構築するには、MSDN の [Visual Studio と Xamarin のセットアップとインストール](https://msdn.microsoft.com/library/mt613162.aspx)に関するページの手順を完了します。 この手順では、Xamarin の詳細情報が記載された資料を紹介しています。インストール中の待ち時間でご覧ください。

セットアップを完了した後、Visual Studio でソリューションを開きます。 そこには&6; つのプロジェクトが表示されます。5 つはプラットフォームに固有のプロジェクトであり、1 つはすべてのプラットフォームで共有される PCL (DirectorySearcher.cs) です。

## <a name="step-2-register-the-directorysearcher-app"></a>手順 2: DirectorySearcher アプリを登録する
アプリでトークンを取得できるようにするには、まず、アプリを Azure AD テナントに登録し、Azure AD Graph API にアクセスするためのアクセス許可を付与する必要があります。 その方法は次のとおりです。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 上部のバーで、自分のアカウントをクリックします。 次に、**[ディレクトリ]** の一覧から、アプリを登録する Active Directory テナントを選択します。
3. 左側のウィンドウで **[その他のサービス]** をクリックし、**[Azure Active Directory]** を選択します。
4. **[アプリの登録]** をクリックし、**[追加]** を選択します。
5. 画面の指示に従って、新しい**ネイティブ クライアント アプリケーション**を作成します。
  * **[名前]** は、ユーザーに対して表示されるアプリ名です。
  * **[リダイレクト URI]** には、Azure AD がトークン応答を返すために使用されるスキームと文字列の組み合わせを設定します。 値を入力します ( http://DirectorySearcher  など)。
6. 登録が完了すると、Azure AD によって一意のアプリケーション ID がアプリに割り当てられます。 **[アプリケーション]** タブから値をコピーします。この値は後で必要になります。
7. **[設定]** ページで、**[必要なアクセス許可]** を選択し、**[追加]** を選択します。
8. API として **[Microsoft Graph]** を選択します。 **[委任されたアクセス許可]** で、**[ディレクトリ データの読み取り]** アクセス許可を追加します。  
この操作によって、ユーザーがアプリで Graph API に照会できるようになります。

## <a name="step-3-install-and-configure-adal"></a>手順 3: ADAL をインストールして構成する
アプリを Azure AD に登録したので、ADAL をインストールし、ID 関連のコードを記述できます。 ADAL と Azure AD が通信できるようにするには、アプリの登録に関する情報を入力する必要があります。

1. パッケージ マネージャー コンソールを使用して ADAL を DirectorySearcher プロジェクトに追加します。

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal
    `

    ADAL の PCL 部分とプラットフォーム固有部分という&2; つのライブラリ参照が、各プロジェクトに追加されていることに注意してください。
2. DirectorySearcherLib プロジェクトで、DirectorySearcher.cs を開きます。
3. クラス メンバーの値を Azure Portal で入力した値に置き換えます。 これらの値は、コードで ADAL を使用する際に常に参照されます。

  * *tenant* は、Azure AD テナントのドメイン (contoso.onmicrosoft.com など) です。
  * *clientId* はアプリのクライアント ID であり、ポータルからコピーしたものです。
  * *returnUri* は、ポータルで入力したリダイレクト URI です (例:  http://DirectorySearcher )。

## <a name="step-4-use-adal-to-get-tokens-from-azure-ad"></a>手順 4: ADAL を使用して Azure AD からトークンを取得する
ほとんどすべてのアプリの認証ロジックは、`DirectorySearcher.SearchByAlias(...)` 内にあります。 プラットフォーム固有プロジェクトで必要なのは、`DirectorySearcher` PCL にコンテキスト パラメーターを渡すことだけです。

1. DirectorySearcher.cs を開いて新しいパラメーターを `SearchByAlias(...)` メソッドに追加します。 `IPlatformParameters` はコンテキスト パラメーターであり、認証を実行するために ADAL が必要とするプラットフォーム固有のオブジェクトをカプセル化しています。

    ```C#
    public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
    {
    ```

2. `AuthenticationContext` を初期化します。これは ADAL のプライマリ クラスです。  
この操作では、ADAL が Azure AD と通信するために必要な調整項目が ADAL に渡されます。
3. `AcquireTokenAsync(...)` を呼び出します。このメソッドは `IPlatformParameters` オブジェクトを受け取り、アプリにトークンを返すために必要な認証フローを呼び出します。

    ```C#
    ...
        AuthenticationResult authResult = null;
        try
        {
            AuthenticationContext authContext = new AuthenticationContext(authority);
            authResult = await authContext.AcquireTokenAsync(graphResourceUri, clientId, returnUri, parent);
        }
        catch (Exception ee)
        {
            results.Add(new User { error = ee.Message });
            return results;
        }
    ...
    ```

    `AcquireTokenAsync(...)` は最初に、ユーザーに資格情報の入力を求めずに、古いトークンのキャッシュまたは更新によって、要求されたリソース (この場合は Graph API) 用のトークンを返そうとします。 要求されたトークンの取得にサインインが必要な場合は、Azure AD のサインイン ページがユーザーに表示されます。
4. Graph API 要求の **Authorization** ヘッダーにアクセス トークンを設定します。

    ```C#
    ...
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
    ...
    ```

`DirectorySearcher` PCL とアプリの ID に関連するコードに関する手順は、以上で完了です。 あとは、各プラットフォームのビューで `SearchByAlias(...)` メソッドを呼び出して、必要に応じて UI のライフサイクルを適切に処理するためのコードを追加します。

### <a name="android"></a>Android
1. MainActivity.cs で、ボタン クリック ハンドラーに `SearchByAlias(...)` の呼び出しを追加します。

    ```C#
    List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
    ```
2. `OnActivityResult` ライフサイクル メソッドをオーバーライドして、認証リダイレクトを適切なメソッドに転送します。 ADAL には、Android でこれを行うためのヘルパー メソッドが用意されています。

    ```C#
    ...
    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
    }
    ...
    ```

### <a name="windows-desktop"></a>Windows デスクトップ
MainWindow.xaml.cs で、デスクトップの `PlatformParameters` オブジェクトに `WindowInteropHelper` を渡し、`SearchByAlias(...)` を呼び出します。

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

#### <a name="ios"></a>iOS
DirSearchClient_iOSViewController.cs で、iOS の `PlatformParameters` オブジェクトはビュー コントローラーへの参照を受け取ります。

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

### <a name="windows-universal"></a>Windows ユニバーサル
Windows ユニバーサルで MainPage.xaml.cs を開いてから、`Search` メソッドを実装します。 このメソッドでは、共有プロジェクトのヘルパー メソッドが使用され、必要に応じて UI が更新されます。

```C#
...
List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

## <a name="whats-next"></a>参照トピック
以上で、動作する Xamarin アプリの完成です。5 種類のプラットフォームで OAuth 2.0 を使用してユーザーを認証し、Web API を安全に呼び出すことができます。

まだテナントにユーザーを設定していない場合は、ここで設定しましょう。

1. DirectorySearcher アプリを実行し、ユーザーの&1; 人としてサインインします。
2. UPN に基づいて、他のユーザーを検索します。

ADAL を使用することにより、共通 ID 機能を容易にアプリに組み込むことができます。 キャッシュ管理、OAuth プロトコル サポート、ユーザーへのログイン UI の表示、有効期限切れとなったトークンの更新など、面倒な操作を容易に実装できます。 知っておく必要があるのは、`authContext.AcquireToken*(…)` の API 呼び出しだけです。

参考として、[完全なサンプル](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip) (環境に応じた構成値を除く) をダウンロードできます。

ここからは、さらに ID シナリオに進むことができます。 たとえば、[Azure AD を使用して .NET Web API を保護](active-directory-devquickstarts-webapi-dotnet.md)してみましょう。

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

