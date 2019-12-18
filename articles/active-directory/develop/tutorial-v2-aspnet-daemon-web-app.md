---
title: Microsoft ID プラットフォーム エンドポイントを使用してマルチテナント デーモンを作成する
description: このチュートリアルでは、Azure Active Directory によって保護されている ASP.NET Web API を Windows デスクトップ (WPF) アプリケーションから呼び出す方法について説明します。 WPF クライアントは、ユーザーを認証し、アクセ ストークンを要求して、Web API を呼び出します。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.collection: M365-identity-device-management
ms.openlocfilehash: d130a962c14415c417eedecd6ae26af1131b2e86
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997022"
---
# <a name="build-a-multitenant-daemon-that-uses-the-microsoft-identity-platform-endpoint"></a>Microsoft ID プラットフォーム エンドポイントを使用してマルチテナント デーモンを作成する

このチュートリアルでは、Microsoft ID プラットフォームを使用して、Microsoft の企業顧客のデータに非対話型の長期プロセスでアクセスする方法について説明します。 サンプル デーモンでは、[OAuth2 のクライアント資格情報の付与](v2-oauth2-client-creds-grant-flow.md)を使用してアクセス トークンを取得します。 デーモンはその後、そのトークンを使用して [Microsoft Graph](https://graph.microsoft.io) を呼び出して、組織のデータにアクセスします。

アプリは、ASP.NET MVC アプリケーションとしてビルドされています。 ユーザーのサインインには OWIN OpenID Connect ミドルウェアを使用します。  

このサンプルの "デーモン" としてのコンポーネントは、API コントローラー `SyncController.cs` です。 このコントローラーを呼び出すと、顧客の Azure Active Directory (Azure AD) テナントに存在するユーザーのリストが Microsoft Graph からプルされます。 `SyncController.cs` は、Web アプリケーション内の AJAX 呼び出しによってトリガーされます。 Microsoft Graph のアクセス トークンの取得には、[Microsoft Authentication Library (MSAL) for .NET](msal-overview.md) が使用されます。

もっとシンプルなコンソール デーモン アプリケーションについては、[.NET Core デーモンのクイックスタート](quickstart-v2-netcore-daemon.md)を参照してください。

## <a name="scenario"></a>シナリオ

このアプリは Microsoft の企業顧客を対象としたマルチテナント アプリであるため、顧客が "サインアップ" (つまり、その会社のデータにアプリケーションを "接続") する手段を備えている必要があります。 接続フローの過程で、社内管理者はまず、サインイン済みのユーザーがいなくてもアプリが非対話型形式で企業データにアクセスできるよう、*アプリケーションのアクセス許可*を直接アプリに付与します。 このサンプルのロジックの大部分は、ID プラットフォームの[管理者の同意](v2-permissions-and-consent.md#using-the-admin-consent-endpoint)エンドポイントを使用してこの接続フローを実現する方法を示します。

![トポロジ](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

このサンプルで使用されている概念の詳細については、[ID プラットフォーム エンドポイントのクライアント資格情報プロトコルに関するドキュメント](v2-oauth2-client-creds-grant-flow.md)を参照してください。

## <a name="prerequisites"></a>前提条件

このクイックスタートのサンプルを実行するには、次のものが必要です。

- [Visual Studio 2017 または 2019](https://visualstudio.microsoft.com/downloads/)。
- Azure AD テナント。 詳細については、[Azure AD テナントの取得方法](quickstart-create-new-tenant.md)に関するページを参照してください。
- Azure AD テナント内の 1 つ以上のユーザー アカウント。 このサンプルは、Microsoft アカウント (旧称 Windows Live アカウント) では動作しません。 Microsoft アカウントを使用して [Azure portal](https://portal.azure.com) にサインインしたものの、ディレクトリ内にユーザー アカウントを作成したことがなければ、この時点でその作業を行う必要があります。

## <a name="clone-or-download-this-repository"></a>このリポジトリをクローンまたはダウンロードする

シェルまたはコマンド ラインで、次のコマンドを実行します。

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

または、[サンプルを ZIP ファイルとしてダウンロード](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip)します。

## <a name="register-the-sample-application-with-your-azure-ad-tenant"></a>サンプル アプリケーションを Azure AD テナントに登録する

このサンプルには 1 つのプロジェクトがあります。 その登録方法は、次のどちらかを選べます。

- [Azure Active Directory テナントにサンプルを登録する](#register-the-sample-application-with-your-azure-ad-tenant)手順と [Azure AD テナントを使用するようサンプルを構成する](#choose-the-azure-ad-tenant)手順に従う。
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

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. ご利用のアカウントが複数の Azure AD テナントに存在する場合は、ページ上部のメニューにあるプロファイルを選択し、 **[ディレクトリの切り替え]** を選択します。
1. ポータル セッションを目的の Azure AD テナントに変更します。

### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>クライアント アプリを登録する (dotnet-web-daemon-v2)

1. 開発者用の Microsoft ID プラットフォームの [[アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) ページに移動します。
1. **[新規登録]** を選択します。
1. **[アプリケーションの登録]** ページが表示されたら、以下のアプリケーションの登録情報を入力します。
   - **[名前]** セクションに、アプリのユーザーに表示されるわかりやすいアプリケーション名を入力します。 たとえば、「**dotnet-web-daemon-v2**」と入力します。
   - **[サポートされているアカウントの種類]** セクションで、 **[任意の組織のディレクトリ内のアカウント]** を選択します。
   - **[リダイレクト URI (省略可能)]** セクションで、コンボ ボックスの **[Web]** を選択し、次のリダイレクト URI を入力します。
       - **https://localhost:44316/**
       - **https://localhost:44316/Account/GrantPermissions**
          
     リダイレクト URI が 3 つ以上ある場合は、アプリが正常に作成された後、 **[認証]** タブから URI を追加する必要があります。
1. **[登録]** を選択して、アプリケーションを作成します。
1. アプリの **[概要]** ページで、 **[アプリケーション (クライアント) ID]** の値を見つけ、後で使用するために記録します。 これは、このプロジェクトの Visual Studio 構成ファイルを構成するために必要になります。
1. アプリのページの一覧から **[認証]** を選択します。 その後、以下を実行します。
   - **[詳細設定]** セクションの **[ログアウト URL]** を「 **https://localhost:44316/Account/EndSession** 」に設定します
   - **[詳細設定]**  >  **[暗黙的な許可]** セクションで **[アクセス トークン]** と **[ID トークン]** を選択します。 このサンプルでは、ユーザーのサインインと API の呼び出しのために、[暗黙的な許可フロー](v2-oauth2-implicit-grant-flow.md)を有効にする必要があります。
1. **[保存]** を選択します。
1. **[証明書とシークレット]** ページの **[クライアント シークレット]** セクションで、 **[新しいクライアント シークレット]** を選択します。 その後、以下を実行します。

   1. キーの説明 (たとえば**アプリのシークレット**) を入力します。
   1. キーの有効期間として **[1 年]** 、 **[2 年]** 、または **[有効期限なし]** を選択します。
   1. **[追加]** ボタンを選びます。 
   1. キーの値が表示されたら、コピーして安全な場所に保存します。 このキーは、後から Visual Studio でプロジェクトを構成するために必要となります。 これは二度と表示されず、他の手段で取得することもできません。
1. アプリのページの一覧から **[API のアクセス許可]** を選択します。 その後、以下を実行します。
   1. **[アクセス許可の追加]** ボタンを選択します。
   1. **[Microsoft API]** タブが選択されていることを確認します。
   1. **[よく使用される Microsoft API]** セクションで、 **[Microsoft Graph]** を選択します。
   1. **[アプリケーションのアクセス許可]** セクションで、適切なアクセス許可 **[User.Read.All]** を選択していることを確認します。
   1. **[アクセス許可の追加]** ボタンを選択します

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
1. 次の画面で、**MVC** プロジェクト テンプレートを選択します。 後で Web API コントローラーを追加するので、**Web API** 用のフォルダーと主要な参照も追加します。 プロジェクトの認証モードの選択は既定値 (**No Authentication**) のままにします。
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
1. 検索ボックスに「**OWIN**」と入力します。 **OWIN Startup クラス**が選択肢として表示されます。 それを選択して、クラスに **Startup.cs** という名前を付けます。
1. **Startup.cs** で、**Startup** クラスのコードを、サンプル アプリの同じファイルにあるコードに置き換えます。 また、定義が **public class Startup** から **public partial class Startup** に変わります。
1. **[Models]** フォルダーに、**MsGraphUser.cs** という新しいクラスを追加します。 その実装コードを、サンプルに含まれている同じ名前のファイルの内容に置き換えます。
1. **MVC 5 コントローラー - 空**の新しいインスタンスを **AccountController** という名前で追加します。 その実装コードを、サンプルに含まれている同じ名前のファイルの内容に置き換えます。
1. **MVC 5 コントローラー - 空**の新しいインスタンスを **UserController** という名前で追加します。 その実装コードを、サンプルに含まれている同じ名前のファイルの内容に置き換えます。
1. **Web API 2 コントローラー - 空**の新しいインスタンスを **SyncController** という名前で追加します。 その実装コードを、サンプルに含まれている同じ名前のファイルの内容に置き換えます。
1. ユーザー インターフェイスについては、**Views\Account** フォルダー内に、**空の (モデルのない) ビュー**のインスタンスを **GrantPermissions**、**Index**、**UserMismatch**. という名前で 3 つ追加します。 また、**Views\User** フォルダーにも **Index** という名前で 1 つ追加します。 その実装コードを、サンプルに含まれている同じ名前のファイルの内容に置き換えます。
1. **Shared\_Layout.cshtml** と **Home\Index.cshtml** を更新して、各種ビューを正しく関連付けます。

## <a name="deploy-the-sample-to-azure"></a>サンプルを Azure にデプロイする

このプロジェクトには、Web アプリ プロジェクトと Web API プロジェクトがあります。 それらを Azure Web サイトにデプロイするには、それぞれについて次の作業が必要です。

1. Azure Web サイトを作成する。
1. Web アプリと Web API を Web サイトに発行する。
1. IIS Express ではなく Web サイトを呼び出すようにクライアントを更新する。

### <a name="create-and-publish-dotnet-web-daemon-v2-to-an-azure-website"></a>dotnet-web-daemon-v2 を作成し、Azure Web サイトに発行する

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 左上隅にある **[リソースの作成]** を選びます。
1. **[Web]**  >  **[Web アプリ]** の順に選択し、Web サイトに名前を付けます。 たとえば、**dotnet-web-daemon-v2-contoso.azurewebsites.net** という名前を付けます。
1. **サブスクリプション**、**リソース グループ**、**アプリ サービスのプランと場所**に関する情報を選択します。 **[OS]** は **[Windows]** 、 **[発行]** は **[コード]** とします。
1. **[作成]** を選択し、アプリ サービスが作成されるのを待ちます。
1. **[デプロイが成功しました]** という通知が表示されたら **[リソースに移動]** を選択し、新しく作成されたアプリ サービスに移動します。
1. Web サイトが作成されたら、 **[ダッシュボード]** でそれを探して選択し、アプリ サービスの **[概要]** 画面を開きます。
1. アプリ サービスの **[概要]** タブで、 **[発行プロファイルの取得]** リンクを選択して発行プロファイルをダウンロードし、保存します。 ソース管理のデプロイなど、他のデプロイ メカニズムを使用することもできます。
1. Visual Studio に切り替えて、次の操作を行います。
   1. **dotnet-web-daemon-v2** プロジェクトに移動します。 
   1. ソリューション エクスプローラーでプロジェクトを右クリックして、 **[発行]** をクリックします。
   1. 下部のバーにある **[プロファイルのインポート]** を選択し、前にダウンロードした発行プロファイルをインポートします。
1. **[構成]** をクリックします。
1. **[接続]** タブで、接続先 URL を "https" を使ったものに更新します。 たとえば、[https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net) を使用します。 **[次へ]** を選択します。
1. **[設定]** タブで、 **[組織認証の有効化]** が選択されていないことを確認します。  
1. **[保存]** を選択します。 メイン画面で **[発行]** を選択します。

Visual Studio によってプロジェクトが発行され、ブラウザーでプロジェクトの URL が自動的に開きます。 プロジェクトの既定の Web ページが表示された場合には、発行が成功しています。

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>dotnet-web-daemon-v2 用に Azure AD テナント アプリケーションの登録を更新する

1. [Azure Portal](https://portal.azure.com) に戻ります。
1. 左側のペインで、 **[Azure Active Directory]** サービス、 **[アプリの登録]** の順に選択します。
1. **dotnet-web-daemon-v2** アプリケーションを選択します。
1. アプリケーションの **[認証]** ページで、 **[ログアウト URL]** フィールドをサービスのアドレスに更新します。 たとえば、[https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net) を使用します。
1. **[ブランド]** メニューで、 **[ホーム ページ URL]** をサービスのアドレスに更新します。 たとえば、[https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net) を使用します。
1. 構成を保存します。
1. 同じ URL を、 **[認証]**  >  **[リダイレクト URI]** メニューの値のリストに追加します。 複数のリダイレクト URL がある場合には、リダイレクト URL ごとにそのアプリ サービスの URI を使用している新しいエントリを用意する必要があります。

## <a name="community-help-and-support"></a>コミュニティのヘルプとサポート

[Stack Overflow](http://stackoverflow.com/questions/tagged/msal) を利用すれば、コミュニティからサポートを受けることができます。
質問がある場合にはまず Stack Overflow に投稿してください。また、既存の問題を参照し、以前に同じ質問が挙がっていないかどうかを確認してください。
ご質問またはコメントには "adal"、"msal"、"dotnet" タグを付けてください。

サンプルにバグを見つけた場合は、[GitHub の [Issues]](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues) で問題を提起してください。

MSAL.NET にバグを見つけた場合は、[MSAL.NET GitHub の Issues](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues) に問題を提起してください。

ご提案をお寄せいただく場合は、[ユーザーの声の投稿ページ](https://feedback.azure.com/forums/169401-azure-active-directory)にアクセスしてください。

## <a name="next-steps"></a>次の手順
Microsoft ID プラットフォームでサポートされるさまざまな[認証フローとアプリケーション シナリオ](authentication-flows-app-scenarios.md)について学習します。

詳細については、概念を説明した次のドキュメントを参照してください。

- [Azure Active Directory のテナント](single-and-multi-tenant-apps.md)
- [Azure AD アプリケーションの同意エクスペリエンスについて](application-consent-experience.md)
- [すべての Azure Active Directory ユーザーがマルチテナント アプリケーション パターンを使用してサインインする](howto-convert-app-to-be-multi-tenant.md)
- [ユーザーおよび管理者の同意について](howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)
- [Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](app-objects-and-service-principals.md)
- [クイック スタート:Microsoft ID プラットフォームにアプリケーションを登録する](quickstart-register-app.md)
- [クイック スタート:Web API にアクセスするようにクライアント アプリケーションを構成する](quickstart-configure-app-access-web-apis.md)
- [クライアント資格情報フローを使用してアプリケーションのトークンを取得する](msal-client-applications.md)

もっとシンプルなマルチテナント コンソール デーモン アプリケーションについては、[.NET Core デーモンのクイックスタート](quickstart-v2-netcore-daemon.md)を参照してください。
