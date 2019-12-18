---
title: Azure AD によって保護された ASP.NET Web API を呼び出す - Microsoft ID プラットフォーム
description: このクイックスタートでは、Azure Active Directory によって保護された ASP.NET Web API を Windows デスクトップ (WPF) アプリケーションから呼び出す方法について説明します。 WPF クライアントは、ユーザーを認証し、アクセ ストークンを要求して、Web API を呼び出します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe3301c3c91343277997be1ee554ced76884274a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74963309"
---
# <a name="quickstart-call-an-aspnet-web-api-protected-by-azure-ad"></a>クイック スタート:Azure AD で保護されている ASP.NET Web API を呼び出す

このクイックスタートでは、認証されたユーザーのみがアクセスできるように Web API を公開し、保護します。 このサンプルでは、個人アカウント (outlook.com、live.com などを含む) だけでなく、Azure Active Directory と統合されたすべての会社や組織の職場/学校アカウントによって発行されたトークンを受け入れることができるように ASP.NET Web API を公開する方法について説明します。

このサンプルには、Web API にアクセスするためのアクセス トークンを要求する方法を示す Windows デスクトップ アプリケーション (WPF) クライアントも含まれています。

## <a name="prerequisites"></a>前提条件

このサンプルを実行するには、次のものが必要になります。

* Visual Studio 2017 または 2019。  [Visual Studio を無料で](https://www.visualstudio.com/downloads/)ダウンロードします。

* [Microsoft アカウント](https://www.outlook.com)または [Office 365 Developer Program](/office/developer-program/office-365-developer-program)

## <a name="download-or-clone-this-sample"></a>このサンプルをダウンロードまたは複製する

このサンプルは、シェルまたはコマンド ラインから複製できます。

  ```console
  git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
  ```

または、[サンプルを ZIP ファイルでダウンロード](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip)できます。

## <a name="register-your-web-api-in-the-application-registration-portal"></a>Web API をアプリケーション登録ポータルに登録する

### <a name="choose-the-azure-ad-tenant-where-you-want-to-create-your-applications"></a>アプリケーションを作成するための Azure AD テナントを選択する

アプリを手動で登録する場合は、最初の手順として次の操作を行う必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. ご利用のアカウントが複数の Azure AD テナントに存在する場合は、ページ上部のメニューの右上隅にあるプロファイルを選択し、 **[ディレクトリの切り替え]** を選択します。
   ポータル セッションを目的の Azure AD テナントに変更します。

### <a name="register-the-service-app-todolistservice"></a>サービス アプリを登録する (TodoListService)

1. 開発者用の Microsoft ID プラットフォームの [[アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) ページに移動します。
1. **[新規登録]** を選択します。
1. **[アプリケーションの登録]** ページが表示されたら、以下のアプリケーションの登録情報を入力します。
   - **[名前]** セクションに、アプリのユーザーに表示されるわかりやすいアプリケーション名を入力します (例: `AppModelv2-NativeClient-DotNet-TodoListService`)。
   - **[サポートされているアカウントの種類]** を **[任意の組織のディレクトリ内のアカウント]** に変更します。
   - **[登録]** を選択して、アプリケーションを作成します。

1. アプリの **[概要]** ページで、 **[アプリケーション (クライアント) ID]** の値を見つけ、後で使用するために記録します。 これは、このプロジェクトの Visual Studio 構成ファイルを構成するために必要になります (`TodoListService\Web.config` の `ClientId`)。
1. **[API の公開]** セクションを選択し、次の操作を行います。
   - **[Scope の追加]** を選択します。
   - **[保存して続行]** を選択して、提案されたアプリケーション ID URI (api://{clientId}) を受け入れます。
   - 次のパラメーターを入力します。
     - **[スコープ名]** には、`access_as_user` を使用します。
     - **[同意できるユーザー]** で **[管理者とユーザー]** オプションが選択されていることを確認します。
     - **[管理者の同意の表示名]** には、「`Access TodoListService as a user`」と入力します。
     - **[管理者の同意の説明]** には、「`Accesses the TodoListService Web API as a user`」と入力します。
     - **[ユーザーの同意の表示名]** には、「`Access TodoListService as a user`」と入力します。
     - **[ユーザーの同意の説明]** には、「`Accesses the TodoListService Web API as a user`」と入力します。
     - **[状態]** を **[有効化]** のままにします。
     - **[スコープの追加]** を選択します

### <a name="configure-the-service-and-client-projects-to-match-the-registered-web-api"></a>登録されている Web API に一致するようにサービスとクライアント プロジェクトを構成する 

1. Visual Studio でソリューションを開き、**TodoListService** プロジェクトのルートの下にある **Web.config** ファイルを開きます。
1. `ida:ClientId` パラメーターの値を、アプリケーション登録ポータルで登録したアプリケーションの**クライアント ID (アプリケーション ID)** に置き換えます。

### <a name="add-the-new-scope-to-the-todolistclients-appconfig"></a>*TodoListClient* の app.config に新しいスコープを追加する

1. **TodoListClient** プロジェクトのルート フォルダーにある **app.config** ファイルを開き、`TodoListServiceScope` パラメーターで *TodoListService* に登録したアプリケーションの**アプリケーション ID** を貼り付けて、文字列 `{Enter the Application ID of your TodoListService from the app registration portal}` を置き換えます。

   > 注:次の形式が使用されていることを確認します。
   >
   > `api://{TodoListService-Application-ID}/access_as_user` 
   >
   >({TodoListService-Application-ID} は、TodoListService のアプリケーション ID を表す GUID です)。

## <a name="register-the-client-app-todolistclient"></a>クライアント アプリを登録する (TodoListClient)

この手順では、アプリケーション登録ポータルで新しいアプリケーションを登録して、*TodoListClient* プロジェクトを構成します。 クライアントとサーバーが*同じアプリケーション*と見なされる場合は、手順 2 で登録したのと同じアプリケーションを再利用することもできます。 ユーザーが Microsoft の個人アカウントでサインインできるようにするには、同じアプリケーションを使用する必要があります。

### <a name="register-the-todolistclient-application-in-the-application-registration-portal"></a>*アプリケーション登録ポータル*に *TodoListClient* アプリケーションを登録する

1. 開発者用の Microsoft ID プラットフォームの [[アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) ページに移動します。
1. **[新規登録]** を選択します。
1. **[アプリケーションの登録]** ページが表示されたら、以下のアプリケーションの登録情報を入力します。
   - **[名前]** セクションに、アプリのユーザーに表示されるわかりやすいアプリケーション名を入力します (例: `NativeClient-DotNet-TodoListClient`)。
   - **[サポートされているアカウントの種類]** を **[任意の組織のディレクトリ内のアカウント]** に変更します。
   - **[登録]** を選択して、アプリケーションを作成します。
1. アプリの [概要] ページで、 **[認証]** セクションを選択します。
   - **[リダイレクト URL]**  |  **[パブリック クライアント (モバイル、デスクトップ) に推奨されるリダイレクト URL]** セクションで、 **[urn:ietf:wg:oauth:2.0:oob]** をオンにします。
   - **[保存]** を選択します。
1. **[API のアクセス許可]** セクションを選択します
   - **[アクセス許可の追加]** をクリックします。さらに、
   - **[自分の API]** タブを選択します。
   - API の一覧で、`AppModelv2-NativeClient-DotNet-TodoListService API` または Web API に入力した名前を選択します。
   - まだチェックしていない場合は、**access_as_user** アクセス許可をチェックします。 必要に応じて検索ボックスを使用します。
   - **[アクセス許可の追加]** ボタンを選択します

### <a name="configure-your-todolistclient-project"></a>*TodoListClient* プロジェクトを構成する

1. *アプリケーション登録ポータル*の **[概要]** ページで、 **[アプリケーション (クライアント) ID]** の値をコピーします。
1. **TodoListClient** プロジェクトのルート フォルダーにある **app.config** ファイルを開き、値を `ida:ClientId` パラメーター値に貼り付けます。

## <a name="run-your-project"></a>プロジェクトを実行する

1. `<F5>` キーを押してプロジェクトを実行します。 *TodoListClient* が開きます。
1. 右上にある **[サインイン]** を選択し、アプリケーションの登録に使用したのと同じユーザー、または同じディレクトリ内のユーザーでサインインします。
1. 初めてサインインする場合は、この時点で *TodoListService* Web API に同意するように求められることがあります。
1. このサインインでは、*TodoListService* Web API にアクセスして *To-Do* リストを操作するためのアクセス トークンも *access_as_user* スコープに対して要求されます。

## <a name="pre-authorize-your-client-application"></a>クライアント アプリケーションを事前承認する

他のディレクトリのユーザーが Web API にアクセスできるようにする方法の 1 つは、Web API に対する*事前承認済み*アプリケーションの一覧にあるクライアント アプリケーションのアプリケーション ID を追加することで、Web API にアクセスするためにクライアント アプリケーションを*事前承認*することです。 事前承認済みのクライアントを追加することで、Web API の使用に同意する必要がなくなります。 Web アプリケーションを事前承認するには、次の手順に従います。

1. *アプリケーション登録ポータル*に戻り、**TodoListService** のプロパティを開きます。
1. **[API の公開]** セクションで、 *[承認済みのクライアント アプリケーション]* セクションの **[クライアント アプリケーションの追加]** をクリックします。
1. *[クライアント ID]* フィールドに、`TodoListClient` アプリケーションのアプリケーション ID を貼り付けます。
1. *[承認済みのスコープ]* セクションで、この Web API のスコープ `api://<Application ID>/access_as_user` を選択します。
1. ページの下部にある **[アプリケーションの追加]** ボタンを押します。

## <a name="run-your-project"></a>プロジェクトを実行する

1. `<F5>` キーを押してプロジェクトを実行します。 *TodoListClient* が開きます。
1. 右上にある **[サインイン]** (または [キャッシュをクリア]/[サインイン]) を選択し、個人用 Microsoft アカウント (live.com または hotmail.com) を使用するか、職場または学校アカウントを使用してサインインします。

## <a name="optional-restrict-sign-in-access-to-your-application"></a>省略可能:アプリケーションへのサインイン アクセスを制限する

既定では、このコード サンプルをダウンロードして、前の手順の後に Azure Active Directory v2 エンドポイントを使用するようにアプリケーションを構成すると、個人アカウント (outlook.com、live.com など) と、Azure AD に統合された組織の職場または学校アカウントの両方で、トークンを要求して Web API にアクセスできます。 

アプリケーションにサインインできるユーザーを制限するには、次のいずれかのオプションを使用します。

### <a name="option-1-restrict-access-to-a-single-organization-single-tenant"></a>オプション 1:単一の組織へのアクセスを制限する (シングルテナント)

単一の Azure AD テナントに属するユーザー アカウント (そのテナントの*ゲスト アカウント*を含む) のみに対して、アプリケーションへのサインイン アクセスを制限できます。 このシナリオは、*基幹業務アプリケーション*に共通です。

1. **App_Start\Startup.Auth** ファイルを開き、`OpenIdConnectSecurityTokenProvider` に渡されたメタデータ エンドポイントの値を `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"` に変更します (`contoso.onmicrosoft.com` などのテナント名を使用することもできます)。
2. 同じファイルで、`TokenValidationParameters` の `ValidIssuer` プロパティを `"https://sts.windows.net/{Tenant ID}/"` に設定し、`ValidateIssuer` 引数を `true` に設定します。

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>オプション 2:カスタム メソッドを使用して発行者を検証する

**IssuerValidator** パラメーターを使用して、カスタム メソッドを実装して発行者を検証できます。 このパラメーターの使用方法の詳細については、[TokenValidationParameters クラス](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters?view=azure-dotnet)に関するページをご覧ください。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>次の手順
Microsoft ID プラットフォームでサポートされている保護された Web API のシナリオの詳細については、次を参照してください。
> [!div class="nextstepaction"]
> [保護された Web API のシナリオ](scenario-protected-web-api-overview.md)
