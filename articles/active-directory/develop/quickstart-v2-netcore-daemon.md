---
title: 'クイックスタート: コンソール アプリを使用したトークンの取得と Microsoft Graph の呼び出し | Azure'
titleSuffix: Microsoft identity platform
description: このクイックスタートでは、.NET Core サンプル アプリでクライアント資格情報フローを使用してトークンを取得し、Microsoft Graph を呼び出す方法について説明します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/05/2020
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 1b539c168deab7c1893f071a2453be28310fc132
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105022927"
---
# <a name="quickstart-get-a-token-and-call-the-microsoft-graph-api-by-using-a-console-apps-identity"></a>クイックスタート: コンソール アプリの ID を使用してトークンを取得し、Microsoft Graph API を呼び出す

このクイックスタートでは、.NET Core コンソール アプリケーションでアクセス トークンを取得して Microsoft Graph API を呼び出し、ディレクトリ内の[ユーザーの一覧](/graph/api/user-list)を表示する方法を示すコード サンプルをダウンロードして実行します。 このコード サンプルでは、ユーザーの ID ではなく、アプリケーション ID を使用してジョブまたは Windows サービスを実行する方法も示します。 このクイックスタートのサンプル コンソール アプリケーションは、デーモン アプリケーションでもあるため、機密クライアント アプリケーションです。

> [!div renderon="docs"]
> 次の図は、サンプル アプリの動作を示しています。
>
> ![このクイックスタートで生成されたサンプル アプリの動作を示す図。](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)
>

## <a name="prerequisites"></a>前提条件

このクイックスタートには [.Net Core 3.1](https://www.microsoft.com/net/download/dotnet-core) が必要ですが、.Net Core 5.0 でも動作します。

> [!div renderon="docs"]
> ## <a name="register-and-download-the-app"></a>アプリを登録してダウンロードする

> [!div renderon="docs" class="sxs-lookup"]
>
> アプリケーションを作成するにあたっては、自動構成と手動構成という 2 つの選択肢があります。
>
> ### <a name="automatic-configuration"></a>自動構成
>
> アプリを登録して自動的に構成してからコード サンプルをダウンロードする場合は、こちらの手順に従ってください。
>
> 1. <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs" target="_blank">アプリの登録用の Azure portal ページ</a>に移動します。
> 1. アプリケーションの名前を入力し、 **[登録]** を選択します。
> 1. 画面の指示に従ってダウンロードし、1 回クリックするだけで、新しいアプリケーションが自動的に構成されます。
>
> ### <a name="manual-configuration"></a>手動構成
>
> アプリケーションとコードサンプルを手動で構成する場合は、次の手順を使用します。
>
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>手順 1:アプリケーションの登録
> アプリケーションを登録し、その登録情報をソリューションに手動で追加するには、次の手順を実行します。
>
> 1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</span></a> にサインインします。
> 1. 複数のテナントにアクセスできる場合は、トップ メニューの **[ディレクトリとサブスクリプション]** フィルター :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: を使用して、アプリケーションを登録するテナントを選択します。
> 1. **Azure Active Directory** を検索して選択します。
> 1. **[管理]** で **[アプリの登録]**  >  **[新規登録]** の順に選択します。
> 1. **[名前]** に、アプリケーションの名前を入力します。 たとえば、「**Daemon-console**」と入力します。 この名前は、アプリのユーザーに表示される場合があります。また、後で変更することができます。
> 1. **[登録]** を選択して、アプリケーションを作成します。
> 1. **[管理]** で、 **[証明書とシークレット]** を選択します。
> 1. **[クライアント シークレット]** で、 **[新しいクライアント シークレット]** を選択し、名前を入力して、 **[追加]** を選択します。 後の手順で使用できるように、シークレットの値を安全な場所に記録します。
> 1. **[管理]** で、 **[API のアクセス許可]**  >  **[アクセス許可の追加]** の順に選択します。 **[Microsoft Graph]** を選択します。
> 1. **[アプリケーションのアクセス許可]** を選択します。
> 1. **[ユーザー]** ノードで、 **[User.Read.All]** を選択し、 **[アクセス許可の追加]** を選択します。

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>クイックスタート アプリをダウンロードして構成する
>
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>手順 1:Azure portal でのアプリケーションの構成
> このクイックスタート内のサンプル コードを動作させるには、クライアント シークレットを作成し、Graph API の **User.Read.All** アプリケーション アクセス許可を追加します。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [これらの変更を行います]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![構成済み](media/quickstart-v2-netcore-daemon/green-check.png) アプリケーションはこれらの属性で構成されています。

#### <a name="step-2-download-your-visual-studio-project"></a>手順 2:Visual Studio プロジェクトをダウンロードする

> [!div renderon="docs"]
> [Visual Studio プロジェクトをダウンロードする](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)
>
> 提供されているプロジェクトは、Visual Studio または Visual Studio for Mac で実行できます。


> [!div class="sxs-lookup" renderon="portal"]
> Visual Studio 2019 を使用してプロジェクトを実行します。
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [コード サンプルをダウンロードします](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>手順 3:Visual Studio プロジェクトの構成
>
> 1. ディスクのルートに近いローカル フォルダーに .zip ファイルを抽出します。 たとえば、*C:\Azure-Samples* に展開します。
>
>    Windows におけるパスの長さの制限に起因したエラーを防ぐため、ドライブのルートに近いディレクトリをアーカイブの展開先とすることをお勧めします。
>
> 1. Visual Studio でソリューション *1-Call-MSGraph\daemon-console.sln* を開きます (省略可能)。
> 1. *appsettings.json* で、`Tenant`、`ClientId`、および `ClientSecret` の値を置き換えます。
>
>    ```json
>    "Tenant": "Enter_the_Tenant_Id_Here",
>    "ClientId": "Enter_the_Application_Id_Here",
>    "ClientSecret": "Enter_the_Client_Secret_Here"
>    ```
>    このコードでは:
>    - `Enter_the_Application_Id_Here` は、登録したアプリケーションのアプリケーション (クライアント) ID の値です。
        アプリケーション (クライアント) ID とディレクトリ (テナント) ID の値を見つけるには、Azure portal でアプリの **[概要]** ページに移動します。
>    - `Enter_the_Tenant_Id_Here` をテナント ID またはテナント名 (`contoso.microsoft.com` など) に置き換えます。
>    - `Enter_the_Client_Secret_Here` を、手順 1 で作成したクライアント シークレットに置き換えます。
    新しいキーを生成するには、 **[証明書とシークレット]** ページに移動します。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>手順 3:管理者の同意

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>手順 4:管理者の同意

この時点でアプリケーションを実行しようとすると、"*HTTP 403 - 禁止*" エラー "操作を完了するのに十分な特権がありません" が表示されます。 このエラーが発生する理由は、すべてのアプリ専用アクセス許可では、ディレクトリの全体管理者がアプリケーションに同意を与える必要があるからです。 ご自身のロールに応じて、次のオプションのいずれかを選択します。

##### <a name="global-tenant-administrator"></a>グローバル テナント管理者

> [!div renderon="docs"]
> グローバル テナント管理者の場合は、Azure portal で **エンタープライズ アプリケーション** にアクセスします。 アプリの登録を選択し、左ペインの **[セキュリティ]** セクションで **[アクセス許可]** を選択します。 次に、 **[{テナント名} に管理者の同意を与えます]** というラベルの付いた大きいボタンを選択します ( **{テナント名}** は実際のディレクトリの名前です)。

> [!div renderon="portal" class="sxs-lookup"]
> 全体管理者の場合は、 **[API のアクセス許可]** ページに移動し、 **[Enter_the_Tenant_Name_Here に管理者の同意を与えます]** を選択します。
> > [!div id="apipermissionspage"]
> > [[API のアクセス許可] ページに移動する]()

##### <a name="standard-user"></a>標準ユーザー

テナントの標準ユーザーの場合は、お使いのアプリケーションに管理者の同意を与えるようグローバル管理者に依頼してください。 これを行うには、次の URL を管理者に知らせます。

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
> その URL で:
> * `Enter_the_Tenant_Id_Here` をテナント ID またはテナント名 (`contoso.microsoft.com` など) に置き換えます。
> * `Enter_the_Application_Id_Here` は、登録したアプリケーションのアプリケーション (クライアント) ID の値です。

前の URL を使用してアプリに同意を与えた後に、"AADSTS50011: No reply address is registered for the application" (AADSTS50011: アプリケーションの応答アドレスが登録されていません) というエラーが表示される場合があります。 このエラーは、このアプリケーションと URL にリダイレクト URI がないために発生します。 これは無視できます。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>手順 4:アプリケーションの実行

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>手順 5:アプリケーションの実行

Visual Studio または Visual Studio for Mac を使用している場合は、**F5** を押してアプリケーションを実行します。 それ以外の場合は、コマンド プロンプト、コンソール、またはターミナルを使用してアプリケーションを実行します。

```dotnetcli
cd {ProjectFolder}\1-Call-MSGraph\daemon-console
dotnet run
```
このコードでは:
* `{ProjectFolder}` は、.zip ファイルを抽出したフォルダーです。 たとえば `C:\Azure-Samples\active-directory-dotnetcore-daemon-v2` です。

結果として、Azure Active Directory 内のユーザーの一覧が表示されます。

このクイックスタート アプリケーションでは、クライアント シークレットを使用して、それ自体を機密クライアントとして識別します。 クライアント シークレットは、プロジェクト ファイルにプレーン テキスト ファイルとして追加されます。 セキュリティ上の理由により、アプリケーションを運用アプリケーションと見なす前に、クライアント シークレットではなく、証明書を使用することをお勧めします。 証明書の使用方法の詳細については、このサンプルの GitHub リポジトリにある[これらの手順](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates)を参照してください。

## <a name="more-information"></a>詳細情報
このセクションでは、ユーザーをサインインさせるために必要なコードの概要を示します。 この概要は、コードの働きや主な引数、また既存の .NET Core コンソール アプリケーションにサインインを追加する方法を理解するのに役立ちます。

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>このサンプルのしくみ
>
> ![このクイックスタートで生成されたサンプル アプリの動作を示す図。](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

### <a name="msalnet"></a>MSAL.NET

Microsoft Authentication Library (MSAL。[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) パッケージにある) は、ユーザーをサインインさせ、Microsoft ID プラットフォームによって保護された API にアクセスするためのトークンを要求するために使用されるライブラリです。 このクイックスタートでは、委任されたアクセス許可ではなく、アプリケーション自体の ID を使用してトークンを要求しています。 この場合の認証フローは、[クライアント資格情報 OAuth フロー](v2-oauth2-client-creds-grant-flow.md)と呼ばれます。 クライアント資格情報フローで MSAL.NET を使用する方法の詳細については、[こちらの記事](https://aka.ms/msal-net-client-credentials)を参照してください。

 MSAL.NET をインストールするには、Visual Studio のパッケージ マネージャー コンソールで次のコマンドを実行します。

```dotnetcli
dotnet add package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>MSAL の初期化

MSAL への参照を追加するには、次のコードを追加します。

```csharp
using Microsoft.Identity.Client;
```

その後、次のコードを使用して MSAL を初期化します。

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientSecret(config.ClientSecret)
                                          .WithAuthority(new Uri(config.Authority))
                                          .Build();
```

 | 要素 | 説明 |
 |---------|---------|
 | `config.ClientSecret` | Azure portal でアプリケーション用に作成されたクライアント シークレット。 |
 | `config.ClientId` | Azure portal に登録されているアプリケーションのアプリケーション (クライアント) ID。 この値は、Azure portal 内のアプリの **[概要]** ページで確認できます。 |
 | `config.Authority`    | (省略可能) ユーザーが認証するためのセキュリティ トークン サービス (STS) エンドポイント。 通常、パブリック クラウドの場合は `https://login.microsoftonline.com/{tenant}` です。`{tenant}` はテナントの名前またはテナント ID です。|

詳しくは、[`ConfidentialClientApplication` のリファレンス ドキュメント](/dotnet/api/microsoft.identity.client.iconfidentialclientapplication)をご覧ください。

### <a name="requesting-tokens"></a>トークンの要求

アプリの ID を使用してトークンを要求するには、`AcquireTokenForClient` メソッドを使用します。

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

|要素| 説明 |
|---------|---------|
| `scopes` | 要求されたスコープが含まれています。 機密クライアントの場合、この値では `{Application ID URI}/.default` のような形式を使用する必要があります。 この形式は、要求されたスコープが、Azure portal に設定されたアプリ オブジェクトに静的に定義されているものであることを示します。 Microsoft Graph の場合、`{Application ID URI}` は `https://graph.microsoft.com` を指しています。 カスタム Web API の場合、`{Application ID URI}` は、Azure portal で **[アプリケーションの登録 (プレビュー)]**  >  **[API の公開]** に定義されます。 |

詳しくは、[`AcquireTokenForClient` のリファレンス ドキュメント](/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient)をご覧ください。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>次のステップ

デーモン アプリケーションの詳細については、シナリオの概要を参照してください。

> [!div class="nextstepaction"]
> [Web API を呼び出すデーモン アプリケーション](scenario-daemon-overview.md)
