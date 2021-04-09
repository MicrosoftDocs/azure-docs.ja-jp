---
title: 'クイックスタート: Microsoft ID プラットフォームによって保護されている ASP.NET Web API を呼び出す | Azure'
titleSuffix: Microsoft identity platform
description: このクイックスタートでは、Microsoft ID プラットフォームによって保護された ASP.NET Web API を Windows デスクトップ (WPF) アプリケーションから呼び出す方法について説明します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/05/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 7d802aa83e2d48e7b66980b5c06aece10a93f37f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104658528"
---
# <a name="quickstart-call-an-aspnet-web-api-thats-protected-by-microsoft-identity-platform"></a>クイック スタート:Microsoft ID プラットフォームによって保護されている ASP.NET Web API を呼び出す

このクイックスタートでは、リソースへのアクセスを承認されたアカウントだけに制限して、ASP.NET Web API を保護する方法を示すコード サンプルをダウンロードして実行します。 このサンプルでは、個人用 Microsoft アカウントと Azure Active Directory (Azure AD) 組織のアカウントの承認がサポートされています。

また、この記事では、Windows Presentation Foundation (WPF) アプリを使用して、Web API にアクセスするためのアクセス トークンを要求するデモンストレーションを行います。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* Visual Studio 2017 または 2019。 [Visual Studio を無料で](https://www.visualstudio.com/downloads/)ダウンロードします。

## <a name="clone-or-download-the-sample"></a>サンプルをクローンまたはダウンロードする

サンプルは、次の 2 つの方法のいずれかで取得できます。

* シェルまたはコマンド ラインからクローンする
   ```console
   git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
   ```
* [ZIP ファイルとしてダウンロードする](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

## <a name="register-your-web-api"></a>Web API を登録する

このセクションでは、Azure portal の **[アプリの登録]** で Web API を登録します。

### <a name="choose-your-azure-ad-tenant"></a>Azure AD テナントを選択する

アプリを手動で登録するには、アプリを作成する Azure Active Directory (Azure AD) テナントを選択します。

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
1. 複数のテナントにアクセスできる場合は、上部のメニューの **[ディレクトリとサブスクリプション]** フィルター :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: を使用して、使用するテナントを選択します。

### <a name="register-the-todolistservice-app"></a>TodoListService アプリを登録する

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
1. 複数のテナントにアクセスできる場合は、トップ メニューの **[ディレクトリとサブスクリプション]** フィルター:::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::を使用して、アプリケーションを登録するテナントを選択します。
1. **Azure Active Directory** を検索して選択します。
1. **[管理]** で **[アプリの登録]**  >  **[新規登録]** の順に選択します。
1. アプリケーションの **名前** を入力します (例: `AppModelv2-NativeClient-DotNet-TodoListService`)。 この名前は、アプリのユーザーに表示される場合があります。また、後で変更することができます。
1. **[サポートされているアカウントの種類]** で、 **[任意の組織のディレクトリ内のアカウント]** を選択します。
1. **[登録]** を選択して、アプリケーションを作成します。
1. アプリの **[概要]** ページで、 **[アプリケーション (クライアント) ID]** の値を探し、後で使用するために記録します。 これは、このプロジェクトの Visual Studio 構成ファイルを構成するために必要になります (つまり、*TodoListService\Web.config* ファイルの `ClientId`)。
1. **[管理]** で、 **[API の公開]**  >  **[スコープの追加]** の順に選択します。 **[保存して続行]** を選択して、提案されたアプリケーション ID URI (`api://{clientId}`) を受け入れ、次の情報を入力します。

    1. **[スコープ名]** に「`access_as_user`」と入力します。
    1. **[同意できるユーザー]** で **[管理者とユーザー]** オプションが選択されていることを確認します。
    1. **[管理者の同意の表示名]** ボックスには、「`Access TodoListService as a user`」と入力します。
    1. **[管理者の同意の説明]** ボックスには、「`Accesses the TodoListService web API as a user`」と入力します。
    1. **[ユーザーの同意の表示名]** ボックスには、「`Access TodoListService as a user`」と入力します。
    1. **[ユーザーの同意の説明]** ボックスには、「`Accesses the TodoListService web API as a user`」と入力します。
    1. **[状態]** は **[有効]** のままにします。
1. **[スコープの追加]** を選択します。

### <a name="configure-the-service-project"></a>サービス プロジェクトを構成する

次の手順を実行して、登録されている Web API に一致するようにサービス プロジェクトを構成します。

1. Visual Studio でソリューションを開き、TodoListService プロジェクトのルートの下にある *Web.config* ファイルを開きます。

1. `ida:ClientId` パラメーターの値を、**アプリの登録** ポータルで登録したアプリケーションのクライアント ID (アプリケーション ID) に置き換えます。

### <a name="add-the-new-scope-to-the-appconfig-file"></a>新しいスコープを app.config ファイルに追加する

次の手順を実行して、新しいスコープを TodoListClient *app.config* ファイルに追加します。

1. TodoListClient プロジェクトのルート フォルダーで *app.config* ファイルを開きます。

1. `TodoListServiceScope` パラメーターで TodoListService プロジェクトに登録したアプリケーションのアプリケーション ID を貼り付け、`{Enter the Application ID of your TodoListService from the app registration portal}` 文字列を置き換えます。

  > [!NOTE]
  > アプリケーション ID の形式が `api://{TodoListService-Application-ID}/access_as_user` になっていることを確認してください (`{TodoListService-Application-ID}` は TodoListService アプリのアプリケーション ID を表す GUID です)。

## <a name="register-the-todolistclient-client-app"></a>TodoListClient クライアント アプリを登録する

このセクションでは、Azure portal の **アプリの登録** で TodoListClient アプリを登録し、TodoListClient プロジェクトでコードを設定します。 クライアントとサーバーが *同じアプリケーション* と見なされる場合は、手順 2 で登録したアプリケーションを再利用できます。 ユーザーが Microsoft の個人用アカウントでサインインできるようにするには、同じアプリケーションを使用します。

### <a name="register-the-app"></a>アプリを登録する

次の手順を実行して、TodoListClient アプリを登録します。

1. 開発者用の Microsoft ID プラットフォームの[アプリの登録](https://go.microsoft.com/fwlink/?linkid=2083908)ポータルに移動します。
1. **[新規登録]** を選択します。
1. **[アプリケーションの登録]** ページが表示されたら、以下のアプリケーションの登録情報を入力します。

    1. **[名前]** セクションに、アプリのユーザーに表示されるわかりやすいアプリケーション名を入力します (例: **NativeClient-DotNet-TodoListClient**)。
    1. **[サポートされているアカウントの種類]** で、 **[任意の組織のディレクトリ内のアカウント]** を選択します。
    1. **[登録]** を選択して、アプリケーションを作成します。

   > [!NOTE]
   > TodoListClient プロジェクトの *app.config* ファイルで、`ida:Tenant` の既定値は `common` に設定されています。 次の値を指定できます。
   > - `common`:職場または学校アカウントを使用するか、Microsoft の個人用アカウントを使用してサインインできます (手順 3b で **[任意の組織のディレクトリ内のアカウント]** を選択したため)。
   > - `organizations`:職場または学校アカウントを使用してサインインできます。
   > - `consumers`:Microsoft の個人用アカウントを使用してのみサインインできます。

1. アプリの **[概要]** ページで **[認証]** を選択し、次の手順を実行します。

    1. **[プラットフォーム構成]** で **[プラットフォームを追加]** ボタンを選択します。
    1. **[モバイル アプリケーションとデスクトップ アプリケーション]** で、 **[モバイル アプリケーションとデスクトップ アプリケーション]** を選択します。
    1. **[リダイレクト URI]** で、 **https://login.microsoftonline.com/common/oauth2/nativeclient** のチェック ボックスをオンにします。
    1. **[構成]** をクリックします。

1. **[API のアクセス許可]** を選択し、次の手順を実行します。

    1. **[アクセス許可の追加]** ボタンを選択します。
    1. **[自分の API]** タブを選択します。
    1. API のリストで **[AppModelv2-NativeClient-DotNet-TodoListService API]** または Web API に入力した名前を選択します。
    1. まだ選択していない場合は、**access_as_user** アクセス許可のチェック ボックスをオンにします。 必要に応じて検索ボックスを使用します。
    1. **[アクセス許可の追加]** ボタンを選択します

### <a name="configure-your-project"></a>プロジェクトを構成する

次の手順を実行して、TodoListClient プロジェクトを構成します。

1. **アプリの登録** ポータルの **[概要]** ページで、 **[アプリケーション (クライアント) ID]** の値をコピーします。

1. TodoListClient プロジェクトのルート フォルダーで *app.config* ファイルを開き、アプリケーション ID の値を `ida:ClientId` パラメーターに貼り付けます。

## <a name="run-your-todolistclient-project"></a>TodoListClient プロジェクトを実行する

次の手順を実行して、TodoListClient プロジェクトを実行します。

1. F5 キーを押して TodoListClient プロジェクトを開きます。 プロジェクト ページが開きます。

1. 右上にある **[サインイン]** を選択し、アプリケーションの登録に使用したのと同じ資格情報でサインインするか、同じディレクトリ内のユーザーとしてサインインします。

   初めてサインインする場合は、TodoListService Web API に同意するように求められることがあります。

   TodoListService Web API にアクセスし、*To-Do* リストを操作できるようにするために、このサインインでは *access_as_user* スコープへのアクセス トークンも要求されます。

## <a name="pre-authorize-your-client-application"></a>クライアント アプリケーションを事前承認する

他のディレクトリのユーザーが Web API にアクセスできるようにする方法の 1 つとして、Web API にアクセスするクライアント アプリケーションを事前承認する方法があります。 これを行うには、クライアント アプリからのアプリケーション ID を Web API の事前承認済みアプリケーションのリストに追加します。 事前承認されたクライアントを追加すると、ユーザーは同意しなくても Web API にアクセスできるようになります。 次の手順を実行して、クライアント アプリを事前承認します。

1. **アプリの登録** ポータルで、TodoListService アプリのプロパティを開きます。
1. **[API の公開]** セクションの **[承認済みのクライアント アプリケーション]** で **[クライアント アプリケーションの追加]** を選択します。
1. **[クライアント ID]** ボックスに、TodoListClient アプリのアプリケーション ID を貼り付けます。
1. **[承認済みのスコープ]** セクションで、`api://<Application ID>/access_as_user` Web API のスコープを選択します。
1. **[アプリケーションの追加]** をクリックします。

### <a name="run-your-project"></a>プロジェクトを実行する

1. F5 キーを押してプロジェクトを実行します。 TodoListClient アプリが開きます。
1. 右上にある **[サインイン]** を選択して、live.com、hotmail.com などの Microsoft の個人用アカウント、または職場または学校アカウントを使用してサインインします。

## <a name="optional-limit-sign-in-access-to-certain-users"></a>省略可能:サインイン アクセスを特定のユーザーに制限する

既定では、上記の手順に従うと、outlook.com、live.com などの個人用アカウント、または Azure AD に統合されている組織の職場または学校アカウントはすべて、トークンを要求したり、Web API にアクセスしたりできます。

アプリケーションにサインインできるユーザーを指定するには、次のいずれかのオプションを使用します。

### <a name="option-1-limit-access-to-a-single-organization-single-tenant"></a>オプション 1: 単一の組織へのアクセスを制限する (シングル テナント)

単一の Azure AD テナントに属するユーザー アカウント (そのテナントの *ゲスト アカウント* を含む) に、アプリケーションへのサインイン アクセスを制限できます。 このシナリオは、*基幹業務アプリケーション* に共通です。

1. *App_Start\Startup.Auth* ファイルを開き、`OpenIdConnectSecurityTokenProvider` に渡されたメタデータの値を `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"` に変更します。 `contoso.onmicrosoft.com` などのテナント名を使用することもできます。
2. 同じファイルで、`TokenValidationParameters` の `ValidIssuer` プロパティを `"https://sts.windows.net/{Tenant ID}/"` に設定し、`ValidateIssuer` 引数を `true` に設定します。

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>オプション 2:カスタム メソッドを使用して発行者を検証する

`IssuerValidator` パラメーターを使用して、カスタム メソッドを実装して発行者を検証できます。 このパラメーターの詳細については、[TokenValidationParameters クラス](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters)に関するページを参照してください。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>次のステップ
Microsoft ID プラットフォームでサポートされている保護された Web API のシナリオの詳細については、次を参照してください。
> [!div class="nextstepaction"]
> [保護された Web API のシナリオ](scenario-protected-web-api-overview.md)
