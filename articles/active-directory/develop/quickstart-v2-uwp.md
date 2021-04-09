---
title: 'クイックスタート: ユニバーサル Windows プラットフォーム アプリでユーザーのサインインと Microsoft Graph の呼び出しを行う | Azure'
titleSuffix: Microsoft identity platform
description: このクイックスタートでは、ユニバーサル Windows プラットフォーム (UWP) アプリケーションでアクセス トークンを取得し、Microsoft ID プラットフォームで保護されている API を呼び出す方法について説明します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/07/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:UWP
ms.openlocfilehash: 88fd6260df0039e960ac87b4b83d43ea497a58b0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104658511"
---
# <a name="quickstart-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>クイック スタート:ユニバーサル Windows プラットフォーム (UWP) アプリケーションから Microsoft Graph API を呼び出す

このクイックスタートでは、ユニバーサル Windows プラットフォーム (UWP) アプリケーションでユーザーをサインインし、アクセス トークンを取得して Microsoft Graph API を呼び出す方法を示すコード サンプルをダウンロードして実行します。 

図については、「[このサンプルのしくみ](#how-the-sample-works)」を参照してください。

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>前提条件
>
> * アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)
>
> ## <a name="register-and-download-your-quickstart-app"></a>クイック スタート アプリを登録してダウンロードする
> [!div renderon="docs" class="sxs-lookup"]
> クイック スタート アプリケーションを開始する方法としては、次の 2 つの選択肢があります。
> * [簡易] [選択肢 1: アプリを登録して自動構成を行った後、コード サンプルをダウンロードする](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [手動] [選択肢 2: アプリケーションを登録し、コード サンプルを手動で構成する](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>オプション 1: アプリを登録して自動構成を行った後、コード サンプルをダウンロードする
>
> 1. <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/UwpQuickstartPage/sourceType/docs" target="_blank">Azure portal のアプリの登録</a>クイックスタート エクスペリエンスに移動します。
> 1. アプリケーションの名前を入力し、 **[登録]** を選択します。
> 1. 画面の指示に従ってダウンロードし、1 回クリックするだけで、新しいアプリケーションが自動的に構成されます。
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>オプション 2:アプリケーションを登録し、アプリケーションとコード サンプルを手動で構成する
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>手順 1:アプリケーションの登録
> アプリケーションを登録し、その登録情報をソリューションに追加するには、次の手順を実行します。
> 1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
> 1. 複数のテナントにアクセスできる場合は、トップ メニューの **[ディレクトリとサブスクリプション]** フィルター:::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::を使用して、アプリケーションを登録するテナントを選択します。
> 1. **Azure Active Directory** を検索して選択します。
> 1. **[管理]** で **[アプリの登録]**  >  **[新規登録]** の順に選択します。
> 1. アプリケーションの **名前** を入力します (例: `UWP-App-calling-MsGraph`)。 この名前は、アプリのユーザーに表示される場合があります。また、後で変更することができます。
> 1. **[サポートされているアカウントの種類]** セクションで、 **[Accounts in any organizational directory and personal Microsoft accounts (for example, Skype, Xbox, Outlook.com)]\(任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント (例: Skype、Xbox、Outlook.com)\)** を選択します。
> 1. **[登録]** を選択してアプリケーションを作成し、後の手順で使用する **アプリケーション (クライアント) ID** を記録します。
> 1. **[管理]** で、 **[認証]** を選択します。
> 1. **[プラットフォームを追加]**  >  **[モバイル アプリケーションとデスクトップ アプリケーション]** を選択します。
> 1. **[リダイレクト URI]** で `https://login.microsoftonline.com/common/oauth2/nativeclient` を選択します。
> 1. **[構成]** をクリックします。

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-the-application"></a>手順 1: アプリケーションの構成
> このクイックスタートのコード サンプルを動作させるには、**リダイレクト URI** (`https://login.microsoftonline.com/common/oauth2/nativeclient`) を追加します。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [この変更を行う]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![構成済み](media/quickstart-v2-uwp/green-check.png) アプリケーションはこれらの属性で構成されています。

#### <a name="step-2-download-the-visual-studio-project"></a>手順 2:Visual Studio プロジェクトのダウンロード

> [!div renderon="docs"]
> [Visual Studio プロジェクトをダウンロードする](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Visual Studio 2019 を使用してプロジェクトを実行します。
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [コード サンプルをダウンロードします](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>手順 3:アプリが構成され、実行準備ができる
> アプリのプロパティの値を使用してプロジェクトを構成したら、実行する準備は完了です。

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-visual-studio-project"></a>手順 3:Visual Studio プロジェクトの構成
>
> 1. .zip アーカイブを、ドライブのルートに近いローカル フォルダーに抽出します。 たとえば、**C:\Azure-Samples** に抽出します。
> 1. Visual Studio でプロジェクトを開きます。 **[ユニバーサル Windows プラットフォーム開発]** ワークロードをインストールします。また、SDK コンポーネントのインストールを求められた場合は、個々のコンポーネントをインストールします。
> 1. *MainPage.Xaml.cs* で、`ClientId` 変数の値を、先ほど登録したアプリケーションの **アプリケーション (クライアント) ID** に変更します。
>
>    ```csharp
>    private const string ClientId = "Enter_the_Application_Id_here";
>    ```
>
>    **アプリケーション (クライアント) ID** は、Azure portal のアプリの **[概要]** ペインで確認できます ( **[Azure Active Directory]**  >  **[アプリの登録]**  >  *[<実際のアプリの登録>]* )。
> 1. パッケージに使用する新しい自己署名テスト証明書を作成して選択します。
>     1. **ソリューション エクスプローラー** で、*Package.appxmanifest* ファイルをダブルクリックします。
>     1. **[パッケージ]**  >  **[証明書の選択]**  >  **[作成]** を選択します。
>     1. パスワードを入力し、 **[OK]** を選択します。
>     1. **[ファイルから選択]** を選択し、先ほど作成した *Native_UWP_V2_TemporaryKey.pfx* ファイルを選択して、 **[OK]** を選択します。
>     1. *Package.appxmanifest* ファイルを閉じます (ファイルの保存を求めるメッセージが表示されたら **[OK]** を選択します)。
>     1. **[ソリューション エクスプローラー]** で、 **[Native_UWP_V2]** プロジェクトを右クリックし、 **[プロパティ]** を選択します。
>     1. **[署名]** を選択し、 **[厳密な名前のキー ファイルを選択してください]** ボックスの一覧から、作成した .pfx を選択します。

#### <a name="step-4-run-the-application"></a>手順 4:アプリケーションの実行

ローカル コンピューターでサンプル アプリケーションを実行するには、次の手順に従います。

1. Visual Studio ツールバーで、適切なプラットフォーム (おそらく ARM ではなく、**x64** または **x86**) を選択します。 ターゲット デバイスが *[デバイス]* から *[ローカル コンピューター]* に変わります。
1. **[デバッグ]**  >  **[デバッグなしで開始]** を選択します。
    
    **開発者モード** を有効にするよう求められた場合は、まず開発者モードを有効にしたうえで、 **[デバッグなしで開始]** を再度選択し、アプリを起動してください。

アプリのウィンドウが表示されたら、 **[Call Microsoft Graph API]\(Microsoft Graph API を呼び出す\)** ボタンを選択し、資格情報を入力して、アプリケーションから要求されたアクセス許可に同意してください。 成功した場合、Microsoft Graph API を呼び出すことによって取得したデータとトークン情報が表示されます。

## <a name="how-the-sample-works"></a>このサンプルのしくみ

![このクイック スタートで生成されたサンプル アプリの動作の紹介](media/quickstart-v2-uwp/uwp-intro.svg)

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) は、ユーザーをサインインし、セキュリティ トークンを要求するために使用されるライブラリです。 セキュリティ トークンは、Microsoft ID プラットフォームによって保護されている API にアクセスするために使用されます。 MSAL は、Visual Studio の "*パッケージ マネージャー コンソール*" で次のコマンドを実行してインストールできます。

```powershell
Install-Package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>MSAL の初期化

MSAL への参照を追加するには、次のコードを追加します。

```csharp
using Microsoft.Identity.Client;
```

その後、MSAL は次のコードを使用して初期化されます。

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                                                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                                                    .Build();
```

`ClientId` の値は、Azure portal で登録したアプリの **アプリケーション (クライアント) ID** です。 この値は、Azure portal のアプリの **[概要]** ページで確認できます。

### <a name="requesting-tokens"></a>トークンの要求

MSAL には、UWP アプリでトークンを取得するための 2 つのメソッド `AcquireTokenInteractive` および `AcquireTokenSilent` があります。

#### <a name="get-a-user-token-interactively"></a>ユーザー トークンを対話形式で取得する

ユーザーは Microsoft ID プラットフォームの操作を強制される場合があります。その場合、各自の資格情報の検証または同意を行うポップアップ ウィンドウが表示されます。 次に例をいくつか示します。

- ユーザーが初めてアプリケーションにサインインした場合
- パスワードの有効期限が切れているため、ユーザーが資格情報を再入力する必要がある場合
- ご使用のアプリケーションが、ユーザーによる同意が必要なリソースへのアクセスを要求している場合
- 2 要素認証が必須である場合

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(scopes)
                      .ExecuteAsync();
```

`scopes` パラメーターには、要求するスコープが格納されます (Microsoft Graph の `{ "user.read" }`、カスタム Web API の `{ "api://<Application ID>/access_as_user" }` など)。

#### <a name="get-a-user-token-silently"></a>ユーザー トークンを自動で取得する

最初の `AcquireTokenInteractive` メソッドを呼び出した後、`AcquireTokenSilent` メソッドを使用して、保護されたリソースにアクセスするためのトークンを取得します。 リソースへのアクセスを必要とするたびに自分の資格情報を確認するようユーザーに要求したくありません。 ほとんどの場合は、ユーザーの操作なしにトークンの取得や更新を求めます。

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

* `scopes` には、要求するスコープが格納されます (Microsoft Graph の `{ "user.read" }`、カスタム Web API の `{ "api://<Application ID>/access_as_user" }` など)。
* `firstAccount` は、キャッシュ内の最初のユーザー アカウントを指定します (MSAL は、1 つのアプリで複数のユーザーをサポート)。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>次のステップ

アプリケーションや新機能の構築についての完全なステップ バイ ステップ ガイドは、Windows デスクトップ チュートリアルをお試しください。このクイック スタートの完全な説明も含まれています。

> [!div class="nextstepaction"]
> [UWP -Graph API 呼び出しチュートリアル](tutorial-v2-windows-uwp.md)
