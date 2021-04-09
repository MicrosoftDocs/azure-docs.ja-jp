---
title: チュートリアル:Microsoft Graph のビジネス データにアクセスするマルチテナント デーモンを作成する | Azure
titleSuffix: Microsoft identity platform
description: このチュートリアルでは、Azure Active Directory によって保護されている ASP.NET Web API を Windows デスクトップ (WPF) アプリケーションから呼び出す方法について説明します。 WPF クライアントは、ユーザーを認証し、アクセ ストークンを要求して、Web API を呼び出します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/10/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 5816bc542ff550accedd33ba9a7fd9d98583b3d1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101653764"
---
# <a name="tutorial-build-a-multi-tenant-daemon-that-uses-the-microsoft-identity-platform"></a>チュートリアル:Microsoft ID プラットフォームを使用したマルチテナント デーモンを作成する

このチュートリアルでは、OAuth 2.0 クライアント資格情報の付与を使用して Microsoft Graph API を呼び出すためのアクセス トークンを取得する ASP.NET デーモン Web アプリをダウンロードして実行します。

このチュートリアルの内容:

> [!div class="checklist"]
> * Microsoft ID プラットフォームにデーモン アプリを統合する
> * 管理者がアプリケーションへのアクセス許可をアプリに直接付与する
> * アクセス トークンを取得して Microsoft Graph API を呼び出す
> * Microsoft Graph API を呼び出す。

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

- [Visual Studio 2017 または 2019](https://visualstudio.microsoft.com/downloads/)。
- Azure AD テナント。 詳細については、[Azure AD テナントの取得方法](quickstart-create-new-tenant.md)に関するページを参照してください。
- Azure AD テナント内の 1 つ以上のユーザー アカウント。 このサンプルは、Microsoft アカウントでは正しく動作しません。 Microsoft アカウントを使用して [Azure portal](https://portal.azure.com) にサインインしたものの、ディレクトリ内にユーザー アカウントを作成したことがなければ、この時点でその作業を行ってください。

## <a name="scenario"></a>シナリオ

アプリは、ASP.NET MVC アプリケーションとしてビルドされています。 ユーザーのサインインには OWIN OpenID Connect ミドルウェアを使用します。

このサンプルの "デーモン" としてのコンポーネントは、API コントローラー `SyncController.cs` です。 このコントローラーを呼び出すと、顧客の Azure Active Directory (Azure AD) テナントに存在するユーザーのリストが Microsoft Graph からプルされます。 `SyncController.cs` は、Web アプリケーション内の AJAX 呼び出しによってトリガーされます。 Microsoft Graph のアクセス トークンの取得には、[Microsoft Authentication Library (MSAL) for .NET](msal-overview.md) が使用されます。

このアプリは Microsoft の企業顧客を対象としたマルチテナント アプリであるため、顧客が "サインアップ" (つまり、その会社のデータにアプリケーションを "接続") する手段を備えている必要があります。 接続フローの過程で、全体管理者はまず、サインイン済みのユーザーがいなくてもアプリが非対話型形式で企業データにアクセスできるよう、"*アプリケーションのアクセス許可*" を直接アプリに付与します。 このサンプルのロジックの大部分は、ID プラットフォームの[管理者の同意](v2-permissions-and-consent.md#using-the-admin-consent-endpoint)エンドポイントを使用してこの接続フローを実現する方法を示します。

![Azure に接続する 3 つのローカル項目がある UserSync App を示す図。対話的にトークンを取得して Azure A D に接続する Start dot Auth、管理者の同意を得て Azure A D に接続する AccountController、およびユーザーを読み取って Microsoft Graph に接続する SyncController。](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

このサンプルで使用されている概念の詳細については、[ID プラットフォームのクライアント資格情報プロトコルに関するドキュメント](v2-oauth2-client-creds-grant-flow.md)をご覧ください。

## <a name="clone-or-download-this-repository"></a>このリポジトリをクローンまたはダウンロードする

シェルまたはコマンド ラインで、次のコマンドを実行します。

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

または、[サンプルを ZIP ファイルとしてダウンロード](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip)します。

## <a name="register-your-application"></a>アプリケーションの登録

このサンプルには 1 つのプロジェクトがあります。 アプリケーションを Azure AD テナントに登録するには:

- [Azure Active Directory テナントにサンプルを登録する](#register-the-client-app-dotnet-web-daemon-v2)手順と [Azure AD テナントを使用するようサンプルを構成する](#choose-the-azure-ad-tenant)手順に従う。
- 以下を実行する PowerShell スクリプトを使用する。
  - Azure AD アプリケーションとその関連オブジェクト (パスワード、アクセス許可、依存関係) を "*自動的*" に作成します。
  - Visual Studio プロジェクトの構成ファイルに変更を加えます。

この自動化を使用する場合には、次のようにします。

1. Windows で PowerShell を実行し、クローンされたディレクトリのルートに移動します。
1. 次のコマンドを実行します。

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. 次のスクリプトを実行して Azure AD アプリケーションを作成し、サンプル アプリケーションのコードを適宜構成します。

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   スクリプトを実行するその他の方法については、[アプリの作成スクリプト](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md)に関するページを参照してください。

1. Visual Studio ソリューションを開き、 **[開始]** を選択してコードを実行します。

この自動化を使用しない場合は、以降のセクションに示した手順を使用してください。

### <a name="choose-the-azure-ad-tenant"></a>Azure AD テナントを選択する

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
1. 複数のテナントにアクセスできる場合は、トップ メニューの **[ディレクトリとサブスクリプション]** フィルター:::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::を使用して、アプリケーションを登録するテナントを選択します。


### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>クライアント アプリを登録する (dotnet-web-daemon-v2)

1. **Azure Active Directory** を検索して選択します。
1. **[管理]** で **[アプリの登録]**  >  **[新規登録]** の順に選択します。
1. アプリケーションの **名前** を入力します (例: `dotnet-web-daemon-v2`)。 この名前は、アプリのユーザーに表示される場合があります。また、後で変更することができます。
1. **[サポートされているアカウントの種類]** セクションで、 **[任意の組織のディレクトリ内のアカウント]** を選択します。
1. **[リダイレクト URI (省略可能)]** セクションで、コンボ ボックスの **[Web]** を選択し、リダイレクト URI として `https://localhost:44316/` と `https://localhost:44316/Account/GrantPermissions` を入力します。

    リダイレクト URI が 3 つ以上ある場合は、アプリが正常に作成された後、 **[認証]** タブから URI を追加する必要があります。
1. **[登録]** を選択して、アプリケーションを作成します。
1. アプリの **[概要]** ページで、 **[アプリケーション (クライアント) ID]** の値を見つけ、後で使用するために記録しておきます。 これは、このプロジェクトの Visual Studio 構成ファイルを構成するために必要になります。
1. **[管理]** で、 **[認証]** を選択します。
1. **[Front-channel logout URL]\(フロントチャネル ログアウト URL\)** を `https://localhost:44316/Account/EndSession` に設定します。
1. **[Implicit grant and hybrid flows]\(暗黙的な許可およびハイブリッド フロー\)** セクションで、 **[アクセス トークン]** と **[ID トークン]** を選択します。 このサンプルでは、ユーザーのサインインと API の呼び出しのために、[暗黙的な許可フロー](v2-oauth2-implicit-grant-flow.md)を有効にする必要があります。
1. **[保存]** を選択します。
1. **[管理]** で、 **[証明書とシークレット]** を選択します。
1. **[クライアント シークレット]** セクションで、 **[新しいクライアント シークレット]** を選択します。 
1. キーの説明 (例: **アプリのシークレット**) を入力します。
1. キーの有効期間として **[1 年]** 、 **[2 年]** 、または **[有効期限なし]** を選択します。
1. **[追加]** を選択します。 キー値を安全な場所に記録します。 このキーは、後から Visual Studio でプロジェクトを構成するために必要となります。
1. **[管理]** で、 **[API のアクセス許可]**  >  **[アクセス許可の追加]** の順に選択します。
1. **[よく使用される Microsoft API]** セクションで、 **[Microsoft Graph]** を選択します。
1. **[アプリケーションのアクセス許可]** セクションで、適切なアクセス許可 (**User.Read.All**) が選択されていることを確認します。
1. **[アクセス許可の追加]** を選択します.

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>Azure AD テナントを使用するようサンプルを構成する

以降の手順では、**ClientID** と "アプリケーションID" (**AppId**) は同じものを指します。

Visual Studio でソリューションを開いて、プロジェクトを構成します。

### <a name="configure-the-client-project"></a>クライアント プロジェクトを構成する

セットアップ スクリプトを使用した場合、次の変更が自動的に適用されます。

1. **UserSync\Web.Config** ファイルを開きます。
1. アプリのキー **ida:ClientId** を探します。 既存の値を、Azure portal からコピーした **dotnet-web-daemon-v2** アプリケーションのアプリケーション ID に置き換えます。
1. アプリのキー **ida:ClientSecret** を探します。 既存の値を、Azure portal で **dotnet-web-daemon-v2** アプリの作成時に保存したキーに置き換えます。

## <a name="run-the-sample"></a>サンプルを実行する

ソリューションのクリーンとリビルドを実行し、UserSync アプリケーションを実行したら、Azure AD テナントの管理者としてサインインします。 テスト用の Azure AD テナントをお持ちでない場合は、[こちらの手順](quickstart-create-new-tenant.md)に従って取得してください。

サインイン時には、まずアプリからサインインとユーザー プロファイルの読み取りのアクセス許可が求められます。 これに同意すると、あなたがビジネス ユーザーであることをアプリが認識できます。

![ユーザーの同意](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

アプリはその後、Microsoft Graph を介して Azure AD テナントのユーザーのリストを同期しようとします。 それができない場合には、テナント管理者は、そのテナントをアプリに接続するよう求められます。

その後、テナント内のユーザーのリストを読み取るためのアクセス許可がアプリから求められます。

![管理者の同意](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

アクセス許可を付与した後は、アプリからサインアウトされます。 このサインアウトは、トークン キャッシュから Microsoft Graph の既存のアクセス トークンを確実に削除するためものものです。 再度サインインすると、Microsoft Graph を呼び出すうえで必要なアクセス許可が、取得済みの最新のトークンに割り当てられます。


アクセス許可を付与すると、アプリがいつでもユーザーを照会できるようになります。 このことは、 **[ユーザーの同期]** ボタンを選択し、ユーザーのリストを最新の情報に更新すると確認できます。 ユーザーを追加または削除し、リストを再同期してみてください。 (ただし、同期されるのは最初のページのユーザーだけであることに注意してください)。

## <a name="about-the-code"></a>コードについて

このサンプルの主立ったコードは、次のファイルにあります。

- **App_Start\Startup.Auth.cs**、**Controllers\AccountController.cs**:初回サインイン。 特に、コントローラーに対するアクションには **Authorize** 属性が存在し、これによってユーザーはサインインすることを強制されます。 このアプリケーションでは、ユーザーのサインインに[承認コード フロー](v2-oauth2-auth-code-flow.md)が使用されます。
- **Controllers\SyncController.cs**:ローカルのメモリ内ストアに対してユーザーのリストを同期させます。
- **Controllers\UserController.cs**:ローカルのメモリ内ストアにあるユーザーのリストを表示します。
- **Controllers\AccountController.cs**:管理者の同意エンドポイントを使用してテナント管理者からアクセス許可を取得します。

## <a name="re-create-the-sample-app"></a>サンプル アプリを再作成する

1. Visual Studio で、新しい **Visual C#** **ASP.NET Web アプリケーション (.NET Framework)** プロジェクトを作成します。
1. 次の画面で、**MVC** プロジェクト テンプレートを選択します。 後で Web API コントローラーを追加するので、**Web API** 用のフォルダーと主要な参照も追加します。 プロジェクトで選択される認証モードは既定値 ( **[認証なし]** ) のままにします。
1. **ソリューション エクスプローラー** ウィンドウでプロジェクトを選択し、**F4** キーを押します。
1. プロジェクトのプロパティで、 **[SSL 有効]** を  **True** に設定します。 **[SSL URL]** に表示される情報を書き留めておきます。 これは、このアプリケーションの登録を Azure portal で構成する際に必要になります。
1. 次の ASP.NET OWIN ミドルウェア NuGet パッケージを追加します。
   - Microsoft.Owin.Security.ActiveDirectory
   - Microsoft.Owin.Security.Cookies
   - Microsoft.Owin.Host.SystemWeb
   - Microsoft.IdentityModel.Protocol.Extensions
   - Microsoft.Owin.Security.OpenIdConnect
   - Microsoft.Identity.Client
1. **[App_Start]** フォルダー内で、次のことを行います。
   1. **Startup.Auth.cs** という名前のクラスを作成します。
   1. 名前空間の名前から **.App_Start** を削除します。
   1. **Startup** クラスのコードを、サンプル アプリの同じファイルにあるコードに置き換えます。
   必ずクラス定義全体を置き換えてください。 定義が **public class Startup** から **public partial class Startup** に変わります。
1. **Startup.Auth.cs** で、Visual Studio の IntelliSense によって提示される **using** ステートメントを追加して、不足している参照を解決します。
1. プロジェクトを右クリックし、 **[追加]** 、 **[クラス]** の順に選択します。
1. 検索ボックスに「**OWIN**」と入力します。 **OWIN Startup クラス** が選択肢として表示されます。 それを選択して、クラスに **Startup.cs** という名前を付けます。
1. **Startup.cs** で、**Startup** クラスのコードを、サンプル アプリの同じファイルにあるコードに置き換えます。 また、定義が **public class Startup** から **public partial class Startup** に変わります。
1. **[Models]** フォルダーに、**MsGraphUser.cs** という新しいクラスを追加します。 その実装コードを、サンプルに含まれている同じ名前のファイルの内容に置き換えます。
1. **MVC 5 コントローラー - 空** の新しいインスタンスを **AccountController** という名前で追加します。 その実装コードを、サンプルに含まれている同じ名前のファイルの内容に置き換えます。
1. **MVC 5 コントローラー - 空** の新しいインスタンスを **UserController** という名前で追加します。 その実装コードを、サンプルに含まれている同じ名前のファイルの内容に置き換えます。
1. **Web API 2 コントローラー - 空** の新しいインスタンスを **SyncController** という名前で追加します。 その実装コードを、サンプルに含まれている同じ名前のファイルの内容に置き換えます。
1. ユーザー インターフェイスについては、**Views\Account** フォルダー内に、**空の (モデルのない) ビュー** のインスタンスを **GrantPermissions**、**Index**、**UserMismatch**. という名前で 3 つ追加します。 また、**Views\User** フォルダーにも **Index** という名前で 1 つ追加します。 その実装コードを、サンプルに含まれている同じ名前のファイルの内容に置き換えます。
1. **Shared\_Layout.cshtml** と **Home\Index.cshtml** を更新して、各種ビューを正しく関連付けます。

## <a name="deploy-the-sample-to-azure"></a>サンプルを Azure にデプロイする

このプロジェクトには、Web アプリ プロジェクトと Web API プロジェクトがあります。 それらを Azure Web サイトにデプロイするには、それぞれについて次の作業が必要です。

1. Azure Web サイトを作成する。
1. Web アプリと Web API を Web サイトに発行する。
1. IIS Express ではなく Web サイトを呼び出すようにクライアントを更新する。

### <a name="create-and-publish-dotnet-web-daemon-v2-to-an-azure-website"></a>dotnet-web-daemon-v2 を作成し、Azure Web サイトに発行する

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
1. 左上隅にある **[リソースの作成]** を選びます。
1. **[Web]**  >  **[Web アプリ]** の順に選択し、Web サイトに名前を付けます。 たとえば、**dotnet-web-daemon-v2-contoso.azurewebsites.net** という名前を付けます。
1. **サブスクリプション**、**リソース グループ**、**アプリ サービスのプランと場所** に関する情報を選択します。 **[OS]** は **[Windows]** 、 **[発行]** は **[コード]** とします。
1. **[作成]** を選択し、アプリ サービスが作成されるのを待ちます。
1. **[デプロイが成功しました]** という通知が表示されたら **[リソースに移動]** を選択し、新しく作成されたアプリ サービスに移動します。
1. Web サイトが作成されたら、 **[ダッシュボード]** でそれを探して選択し、アプリ サービスの **[概要]** 画面を開きます。
1. アプリ サービスの **[概要]** タブで、 **[発行プロファイルの取得]** リンクを選択して発行プロファイルをダウンロードし、保存します。 ソース管理のデプロイなど、他のデプロイ メカニズムを使用することもできます。
1. Visual Studio に切り替えて、次の操作を行います。
   1. **dotnet-web-daemon-v2** プロジェクトに移動します。
   1. ソリューション エクスプローラーでプロジェクトを右クリックして、 **[発行]** をクリックします。
   1. 下部のバーにある **[プロファイルのインポート]** を選択し、前にダウンロードした発行プロファイルをインポートします。
1. **[構成]** をクリックします。
1. **[接続]** タブで、接続先 URL を "https" を使ったものに更新します。 たとえば、 `https://dotnet-web-daemon-v2-contoso.azurewebsites.net`を使用します。 **[次へ]** を選択します。
1. **[設定]** タブで、 **[組織認証の有効化]** が選択されていないことを確認します。
1. **[保存]** を選択します。 メイン画面で **[発行]** を選択します。

Visual Studio によってプロジェクトが発行され、ブラウザーでプロジェクトの URL が自動的に開きます。 プロジェクトの既定の Web ページが表示された場合には、発行が成功しています。

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>dotnet-web-daemon-v2 用に Azure AD テナント アプリケーションの登録を更新する

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a> に戻ります。
1. 左側のペインで、 **[Azure Active Directory]** サービス、 **[アプリの登録]** の順に選択します。
1. **dotnet-web-daemon-v2** アプリケーションを選択します。
1. アプリケーションの **[認証]** ページで、 **[Front-channel logout URL]\(フロントチャネル ログアウト URL\)** フィールドをサービスのアドレスで更新します。 たとえば、 `https://dotnet-web-daemon-v2-contoso.azurewebsites.net/Account/EndSession`を使用します。
1. **[ブランド]** メニューで、 **[ホーム ページ URL]** をサービスのアドレスに更新します。 たとえば、 `https://dotnet-web-daemon-v2-contoso.azurewebsites.net`を使用します。
1. 構成を保存します。
1. 同じ URL を、 **[認証]**  >  **[リダイレクト URI]** メニューの値のリストに追加します。 複数のリダイレクト URL がある場合には、リダイレクト URL ごとにそのアプリ サービスの URI を使用している新しいエントリを用意する必要があります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする
必要がなくなったら、「[アプリケーションの登録](#register-your-application)」の手順で作成したアプリ オブジェクトを削除します。  アプリケーションを削除するには、「[自分または自分の組織が作成したアプリケーションを削除する](quickstart-remove-app.md#remove-an-application-authored-by-you-or-your-organization)」の手順に従います。

## <a name="get-help"></a>ヘルプの参照

[Microsoft Q&A](/answers/products/) を利用すれば、コミュニティからサポートを受けることができます。
質問がある場合にはまず [Microsoft Q&A](/answers/products/) に投稿してください。また、既存の問題を参照し、以前に同じ質問が挙がっていないかどうかを確認してください。
ご質問またはコメントには、"azure-ad-adal-deprecation"、"azure-ad-msal"、および "dotnet-standard" のタグを必ず付けてください。

サンプルにバグを見つけた場合は、[GitHub の [Issues]](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues) で問題を提起してください。

MSAL.NET にバグを見つけた場合は、[MSAL.NET GitHub の Issues](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues) に問題を提起してください。

ご提案をお寄せいただく場合は、[ユーザーの声の投稿ページ](https://feedback.azure.com/forums/169401-azure-active-directory)にアクセスしてください。

## <a name="next-steps"></a>次のステップ

Microsoft ID プラットフォームを使用して、保護された Web API にアクセスするデーモン アプリの作成について詳しい情報をご覧ください。

> [!div class="nextstepaction"]
> [シナリオ:Web API を呼び出すデーモン アプリケーション](scenario-daemon-overview.md)