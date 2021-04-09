---
title: チュートリアル:ユーザーを E2E で認証する
description: App Service の認証と承認を使用して、App Service アプリ (リモート API へのアクセスを含む) をエンド ツー エンドでセキュリティ保護する方法について説明します。
keywords: App Service, Azure App Service, authN, authZ, 保護, セキュリティ, 多層, Azure Active Directory, Azure Ad
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/29/2020
ms.custom: devx-track-csharp, seodec18, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 8c3fca6cf7782c3aaac91388a8f8395e288f5ea5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96558744"
---
# <a name="tutorial-authenticate-and-authorize-users-end-to-end-in-azure-app-service"></a>チュートリアル:Azure App Service でユーザーをエンド ツー エンドで認証および承認する

::: zone pivot="platform-windows"  

[Azure App Service](overview.md) は、非常にスケーラブルな、自己適用型の Web ホスティング サービスを提供します。 さらに、App Service には、[ユーザーの認証と承認](overview-authentication-authorization.md)のためのサポートが組み込まれています。 このチュートリアルでは、App Service の認証と承認を使用してアプリケーションをセキュリティで保護する方法を示します。 ここでは ASP.NET Core アプリと Angular.js フロントエンドが例として使用されています。 App Service の認証と承認では、すべての言語のランタイムがサポートされています。このチュートリアルに沿って、お好みの言語に適用する方法を学習することができます。

::: zone-end

::: zone pivot="platform-linux"

[Azure App Service](overview.md) は、Linux オペレーティング システムを使用する、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供します。 さらに、App Service には、[ユーザーの認証と承認](overview-authentication-authorization.md)のためのサポートが組み込まれています。 このチュートリアルでは、App Service の認証と承認を使用してアプリケーションをセキュリティで保護する方法を示します。 ここでは ASP.NET Core アプリと Angular.js フロントエンドが例として使用されています。 App Service の認証と承認では、すべての言語のランタイムがサポートされています。このチュートリアルに沿って、お好みの言語に適用する方法を学習することができます。

::: zone-end

![単純な認証と承認](./media/tutorial-auth-aad/simple-auth.png)

また、認証されたユーザーの代わりに、[サーバー コード](#call-api-securely-from-server-code)と[ブラウザー コード](#call-api-securely-from-browser-code)の両方からセキュリティで保護されたバックエンド API にアクセスすることによって、多層アプリをセキュリティで保護する方法も示します。

![高度な認証と承認](./media/tutorial-auth-aad/advanced-auth.png)

これらは、App Service で実現可能な認証および承認のシナリオのごく一部です。 

以下に、チュートリアルで学習する事項のより包括的な一覧を示します。

> [!div class="checklist"]
> * 組み込みの認証および承認を有効にする
> * 未認証の要求に対してアプリをセキュリティで保護する
> * Azure Active Directory を ID プロバイダーとして使用する
> * サインインしたユーザーの代わりにリモート アプリにアクセスする
> * トークン認証を使用して、サービス間呼び出しをセキュリティで保護する
> * サーバー コードのアクセス トークンを使用する
> * クライアント (ブラウザー) コードのアクセス トークンを使用する

このチュートリアルの手順は、macOS、Linux、Windows で実行できます。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

- <a href="https://git-scm.com/" target="_blank">Git をインストールする</a>
- <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">最新の .NET Core 3.1 SDK をインストールする</a>
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-local-net-core-app"></a>ローカル .NET Core アプリを作成する

この手順では、ローカル .NET Core プロジェクトを設定します。 バックエンド API アプリとフロントエンド Web アプリのデプロイには、同じプロジェクトを使用します。

### <a name="clone-and-run-the-sample-application"></a>サンプル アプリケーションを複製して実行する

次のコマンドを実行して、サンプル リポジトリを複製し、実行します。

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
cd dotnet-core-api
dotnet run
```

`http://localhost:5000` に移動し、ToDo 項目の追加、編集、および削除を試みます。 

![ローカルで実行される ASP.NET Core API](./media/tutorial-auth-aad/local-run.png)

任意のタイミングで ASP.NET Core を停止するには、ターミナルで `Ctrl+C` キーを押します。

## <a name="deploy-apps-to-azure"></a>アプリを Azure にデプロイする

この手順では、2 つの App Service アプリにプロジェクトをデプロイします。 1 つはフロントエンド アプリで、もう 1 つはバックエンド アプリです。

### <a name="configure-a-deployment-user"></a>デプロイ ユーザーを構成する

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-azure-resources"></a>Azure リソースを作成する

::: zone pivot="platform-windows"  

Cloud Shell で次のコマンドを実行して、2 つの Windows Web アプリを作成します。 _\<front-end-app-name>_ および _\<back-end-app-name>_ を、グローバルに一意な 2 つのアプリ名で置き換えてください (有効な文字は、`a-z`、`0-9`、および `-` です)。 各コマンドの詳細については、[Azure App Service での RESTful API と CORS](app-service-web-tutorial-rest-api.md) に関する記事を参照してください。

```azurecli-interactive
az group create --name myAuthResourceGroup --location "West Europe"
az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku FREE
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <front-end-app-name> --deployment-local-git --query deploymentLocalGitUrl
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <back-end-app-name> --deployment-local-git --query deploymentLocalGitUrl
```

::: zone-end

::: zone pivot="platform-linux"

Cloud Shell で次のコマンドを実行して、2 つの Web アプリを作成します。 _\<front-end-app-name>_ および _\<back-end-app-name>_ を、グローバルに一意な 2 つのアプリ名で置き換えてください (有効な文字は、`a-z`、`0-9`、および `-` です)。 各コマンドの詳細については、[Azure App Service での .NET Core アプリの作成](quickstart-dotnetcore.md)に関するページを参照してください。

```azurecli-interactive
az group create --name myAuthResourceGroup --location "West Europe"
az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku FREE --is-linux
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <front-end-app-name> --runtime "DOTNETCORE|3.1" --deployment-local-git --query deploymentLocalGitUrl
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <back-end-app-name> --runtime "DOTNETCORE|3.1" --deployment-local-git --query deploymentLocalGitUrl
```

::: zone-end

> [!NOTE]
> `az webapp create` の出力に表示されている、フロントエンド アプリとバックエンド アプリの Git リモートの URL を保存します。
>

### <a name="push-to-azure-from-git"></a>Git から Azure へのプッシュ

"_ローカル ターミナル ウィンドウ_" に戻り、以下の Git コマンドを実行して、バックエンド アプリにデプロイします。 _\<deploymentLocalGitUrl-of-back-end-app>_ を、「[Azure リソースを作成する](#create-azure-resources)」で保存した Git リモートの URL で置き換えます。 Git Credential Manager によって資格情報の入力を求めるメッセージが表示されたら、Azure portal へのサインインに使用する資格情報ではなく、[デプロイ資格情報](deploy-configure-credentials.md)を入力してください。

```bash
git remote add backend <deploymentLocalGitUrl-of-back-end-app>
git push backend master
```

ローカル ターミナル ウィンドウで、以下の Git コマンドを実行して、同じコードをフロントエンド アプリにデプロイします。 _\<deploymentLocalGitUrl-of-front-end-app>_ を、「[Azure リソースを作成する](#create-azure-resources)」で保存した Git リモートの URL で置き換えます。

```bash
git remote add frontend <deploymentLocalGitUrl-of-front-end-app>
git push frontend master
```

### <a name="browse-to-the-apps"></a>アプリの参照

ブラウザーで次の URL に移動し、2 つのアプリが動作していることを確認します。

```
http://<back-end-app-name>.azurewebsites.net
http://<front-end-app-name>.azurewebsites.net
```

:::image type="content" source="./media/tutorial-auth-aad/azure-run.png" alt-text="ブラウザー ウィンドウでの Azure App Service Rest API Sample のスクリーンショット。To do list アプリが表示されている。":::

> [!NOTE]
> アプリが再起動されると、新しいデータが消去されていることに気付く場合があります。 サンプル ASP.NET Core アプリではメモリ内データベースが使用されているため、この動作は仕様です。
>
>

## <a name="call-back-end-api-from-front-end"></a>フロントエンドからバックエンド API を呼び出す

この手順では、フロントエンド アプリのサーバー コードを、バックエンド API へのアクセスに合わせて設定します。 後で、フロントエンドからバックエンドへの認証済みアクセスを有効にします。

### <a name="modify-front-end-code"></a>フロントエンド コードを変更する

ローカル リポジトリで、_Controllers/TodoController.cs_ を開きます。 `TodoController` クラスの先頭に次の行を追加し、 _\<back-end-app-name>_ を実際のバックエンド アプリの名前で置き換えます。

```cs
private static readonly HttpClient _client = new HttpClient();
private static readonly string _remoteUrl = "https://<back-end-app-name>.azurewebsites.net";
```

`[HttpGet]` で修飾されたメソッドを見つけ、中かっこ内のコードを次のコードで置き換えます。

```cs
var data = await _client.GetStringAsync($"{_remoteUrl}/api/Todo");
return JsonConvert.DeserializeObject<List<TodoItem>>(data);
```

最初の行は、バックエンド API アプリへの `GET /api/Todo` 呼び出しを行います。

次に、`[HttpGet("{id}")]` で修飾されたメソッドを見つけ、中かっこ内のコードを次のコードで置き換えます。

```cs
var data = await _client.GetStringAsync($"{_remoteUrl}/api/Todo/{id}");
return Content(data, "application/json");
```

最初の行は、バックエンド API アプリへの `GET /api/Todo/{id}` 呼び出しを行います。

次に、`[HttpPost]` で修飾されたメソッドを見つけ、中かっこ内のコードを次のコードで置き換えます。

```cs
var response = await _client.PostAsJsonAsync($"{_remoteUrl}/api/Todo", todoItem);
var data = await response.Content.ReadAsStringAsync();
return Content(data, "application/json");
```

最初の行は、バックエンド API アプリへの `POST /api/Todo` 呼び出しを行います。

次に、`[HttpPut("{id}")]` で修飾されたメソッドを見つけ、中かっこ内のコードを次のコードで置き換えます。

```cs
var res = await _client.PutAsJsonAsync($"{_remoteUrl}/api/Todo/{id}", todoItem);
return new NoContentResult();
```

最初の行は、バックエンド API アプリへの `PUT /api/Todo/{id}` 呼び出しを行います。

次に、`[HttpDelete("{id}")]` で修飾されたメソッドを見つけ、中かっこ内のコードを次のコードで置き換えます。

```cs
var res = await _client.DeleteAsync($"{_remoteUrl}/api/Todo/{id}");
return new NoContentResult();
```

最初の行は、バックエンド API アプリへの `DELETE /api/Todo/{id}` 呼び出しを行います。

すべての変更を保存します。 ローカル ターミナル ウィンドウで、以下の Git コマンドを使用して、変更をフロントエンド アプリにデプロイします。

```bash
git add .
git commit -m "call back-end API"
git push frontend master
```

### <a name="check-your-changes"></a>変更を確認する

`http://<front-end-app-name>.azurewebsites.net` に移動し、`from front end 1` や `from front end 2` などのいくつかの項目を追加します。

`http://<back-end-app-name>.azurewebsites.net` に移動して、フロントエンド アプリから追加された項目を確認します。 また、`from back end 1` や `from back end 2` などのいくつかの項目を追加し、フロントエンド アプリを更新して、変更が反映されているかどうかを確認します。

:::image type="content" source="./media/tutorial-auth-aad/remote-api-call-run.png" alt-text="ブラウザー ウィンドウでの Azure App Service Rest API Sample のスクリーンショット。フロントエンド アプリから項目が追加されている To do list アプリが表示されている。":::

## <a name="configure-auth"></a>auth を構成する

この手順では、2 つのアプリの認証と承認を有効にします。 また、フロントエンド アプリも構成して、バックエンド アプリへの認証済みの呼び出しを行うために使用できるアクセス トークンが生成されるようにします。

ID プロバイダーとして Azure Active Directory を使用します。 詳細については、[App Services アプリケーション用の Azure Active Directory 認証の構成](configure-authentication-provider-aad.md)に関するページを参照してください。

### <a name="enable-authentication-and-authorization-for-back-end-app"></a>バックエンド アプリの認証と承認を有効にする

[[Azure portal]](https://portal.azure.com) メニューで **[リソース グループ]** を選択するか、または任意のページから *[リソース グループ]* を検索して選択します。

**[リソース グループ]** でリソース グループを検索して選択します。 **[概要]** でバックエンド アプリの管理ページを選択します。

:::image type="content" source="./media/tutorial-auth-aad/portal-navigate-back-end.png" alt-text="サンプル リソース グループの概要を示し、バックエンド アプリの管理ページが選択されている、[リソース グループ] ウィンドウのスクリーンショット。":::

バックエンド アプリの左側のメニューで **[認証/承認]** を選択し、 **[オン]** を選択して App Service 認証を有効にします。

**[要求が認証されない場合に実行するアクション]** で、 **[Azure Active Directory でのログイン]** を選択します。

**[認証プロバイダー]** の下の **[Azure Active Directory]** をクリックします。

:::image type="content" source="./media/tutorial-auth-aad/configure-auth-back-end.png" alt-text="[認証/承認] が選択され、右側のメニューで設定が選択されている、バックエンド アプリの左側のメニューのスクリーンショット。":::

**[簡易]** を選択し、既定の設定のままで新しい AD アプリを作成して、 **[OK]** を選択します。

**[認証/承認]** ページで、 **[保存]** を選択します。

`Successfully saved the Auth Settings for <back-end-app-name> App` というメッセージを含む通知が表示されたら、ポータル ページを更新します。

**[Azure Active Directory]** を再度選択し、 **[Azure AD アプリ]** を選択します。

Azure AD アプリケーションの **クライアント ID** をメモ帳にコピーします。 この値は、後で必要になります。

:::image type="content" source="./media/tutorial-auth-aad/get-application-id-back-end.png" alt-text="Azure AD アプリを示している [Azure Active Directory の設定] ウィンドウと、コピーするクライアント ID を示している [Azure AD アプリケーション] ウィンドウのスクリーンショット。":::

ここで終えた場合でも、既に App Service の認証と承認によってセキュリティが確保された自己完結型のアプリが完成します。 残りのセクションでは、認証済みのユーザーをフロント エンドからバックエンドに "送る" ことによって、マルチアプリ ソリューションのセキュリティを確保する方法について説明します。 

### <a name="enable-authentication-and-authorization-for-front-end-app"></a>フロントエンド アプリの認証と承認を有効にする

フロントエンド アプリに対して同じ手順を行いますが、最後の手順はスキップします。 フロントエンド アプリでは、クライアント ID は必要ありません。

必要に応じて、`http://<front-end-app-name>.azurewebsites.net` に移動します。 セキュリティで保護されたサインイン ページにリダイレクトされるようになったはずです。 サインインした後も、"*バックエンド アプリからはデータにアクセスできません*"。これは、バックエンド アプリでは、フロントエンド アプリからの Azure Active Directory サインインが必要になっているためです。 次の 3 つの手順を実行する必要があります。

- バックエンドへのフロントエンド アクセスを許可する
- 使用可能なトークンを返すように App Service を構成する
- コードでトークンを使用する

> [!TIP]
> エラーが発生してアプリの認証/承認設定を再構成すると、トークン ストア内のトークンが新しい設定で再生成されないことがあります。 トークンが再生成されるようにするには、サインアウトしてからアプリにサインインし直す必要があります。 そのための簡単な方法は、ブラウザーをプライベート モードで使用し、アプリの設定を変更した後、ブラウザーを閉じてからプライベート モードでもう一度開くことです。

### <a name="grant-front-end-app-access-to-back-end"></a>バックエンドへのフロントエンド アプリのアクセスを許可する

両方のアプリに対する認証と承認を有効にしたので、それぞれのアプリは AD アプリケーションによってサポートされています。 この手順では、ユーザーの代わりにバックエンドにアクセスするアクセス許可をフロントエンド アプリに付与します (技術的には、ユーザーの代わりにバックエンドの "_AD アプリケーション_" にアクセスするためのアクセス許可をフロントエンドの "_AD アプリケーション_" に付与します)。

[[Azure portal]](https://portal.azure.com) メニューで **[Azure Active Directory]** を選択するか、任意のページから *[Azure Active Directory]* を検索して選択します。

**[アプリの登録]**  >  **[所有しているアプリケーション]**  >  **[View all applications in this directory]\(このディレクトリのすべてのアプリケーションを表示\)** の順に選択します。 フロントエンド アプリ名を選択し、 **[API のアクセス許可]** を選択します。

:::image type="content" source="./media/tutorial-auth-aad/add-api-access-front-end.png" alt-text="[所有しているアプリケーション]、フロントエンド アプリ名、および [API のアクセス許可] が選択されている、[Microsoft - アプリの登録] ウィンドウのスクリーンショット。":::

**[アクセス許可の追加]** を選択し、 **[所属する組織で使用している API]**  >  **\<back-end-app-name>** の順に選択します。

バックエンド アプリの **[API アクセス許可の要求]** ページで、 **[委任されたアクセス許可]** と **[user_impersonation]** を選択し、次に **[アクセス許可の追加]** を選択します。

:::image type="content" source="./media/tutorial-auth-aad/select-permission-front-end.png" alt-text="[委任されたアクセス許可]、user_impersonation、および [アクセス許可の追加] ボタンが選択されているところを示す、[API アクセス許可の要求] ページのスクリーンショット。":::

### <a name="configure-app-service-to-return-a-usable-access-token"></a>使用可能なアクセス トークンを返すように App Service を構成する

これで、フロントエンド アプリに、サインインしたユーザーとしてバックエンド アプリにアクセスするために必要なアクセス許可が与えられました。 この手順では、バックエンドにアクセスするための使用可能なアクセス トークンを提供するように、App Service の認証および承認を構成します。 この手順では、バックエンドのクライアント ID が必要です。この ID は、「[バックエンド アプリの認証と承認を有効にする](#enable-authentication-and-authorization-for-back-end-app)」でコピーしたものです。

[Azure Resource Explorer](https://resources.azure.com) に移動し、リソース ツリーを使用してフロントエンド Web アプリを見つけます。

リソース ツリーでフロントエンド アプリが選択された状態で、[Azure Resource Explorer](https://resources.azure.com) が開きます。 ページの上部にある **[読み取り/書き込み]** をクリックして、Azure リソースの編集を有効にします。

:::image type="content" source="./media/tutorial-auth-aad/resources-enable-write.png" alt-text="[読み取り/書き込み] ボタンが選択されている、[Azure Resource Explorer] ページの上部の [読み取り専用] および [読み取り/書き込み] ボタンのスクリーンショット。":::

左側のブラウザーで、**config** > **authsettings** にドリルダウンします。

**[authsettings]** ビューで、 **[編集]** をクリックします。 コピーしたクライアント ID を使用して、`additionalLoginParams` を次の JSON 文字列に設定します。 

```json
"additionalLoginParams": ["response_type=code id_token","resource=<back-end-client-id>"],
```

:::image type="content" source="./media/tutorial-auth-aad/additional-login-params-front-end.png" alt-text="additionalLoginParams 文字列とクライアント ID の例を示す、[authsettings] ビューのコード例のスクリーンショット。":::

**[PUT]** をクリックして、設定を保存します。

これでアプリの構成は完了です。 フロントエンドが適切なアクセス トークンを使用してバックエンドにアクセスする準備ができました。

他のプロバイダー用にアクセス トークンを構成する方法については、「[ID プロバイダー トークンの更新](app-service-authentication-how-to.md#refresh-identity-provider-tokens)」を参照してください。

## <a name="call-api-securely-from-server-code"></a>サーバー コードから API を安全に呼び出す

この手順では、前に変更したサーバー コードを有効にして、バックエンド API への認証済み呼び出しを行います。

フロントエンド アプリに必要なアクセス許可が付与されていて、バックエンドのクライアント ID もログイン パラメーターに追加されます。 そのため、バックエンド アプリでの認証用のアクセス トークンを取得することができます。 App Service は、認証された各要求に `X-MS-TOKEN-AAD-ACCESS-TOKEN` ヘッダーを挿入することで、このトークンをサーバー コードに提供します (「[Retrieve tokens in app code (アプリ コードでトークンを取得する)](app-service-authentication-how-to.md#retrieve-tokens-in-app-code)」を参照)。

> [!NOTE]
> これらのヘッダーは、サポートされているすべての言語用に挿入されます。 言語ごとの標準パターンを使用して、これらにアクセスします。

ローカル リポジトリで、_Controllers/TodoController.cs_ を再度開きます。 `TodoController(TodoContext context)` コンストラクターの下に、次のコードを追加します。

```cs
public override void OnActionExecuting(ActionExecutingContext context)
{
    base.OnActionExecuting(context);

    _client.DefaultRequestHeaders.Accept.Clear();
    _client.DefaultRequestHeaders.Authorization =
        new AuthenticationHeaderValue("Bearer", Request.Headers["X-MS-TOKEN-AAD-ACCESS-TOKEN"]);
}
```

このコードは、すべてのリモート API 呼び出しに標準の HTTP ヘッダー `Authorization: Bearer <access-token>` を追加します。 ASP.NET Core MVC 要求実行パイプラインでは、`OnActionExecuting` が各アクションの直前に実行されるため、送信 API 呼び出しごとにアクセス トークンが提供されるようになりました。

すべての変更を保存します。 ローカル ターミナル ウィンドウで、以下の Git コマンドを使用して、変更をフロントエンド アプリにデプロイします。

```bash
git add .
git commit -m "add authorization header for server code"
git push frontend master
```

`https://<front-end-app-name>.azurewebsites.net` に再度サインインします。 ユーザー データの使用の同意のページで、 **[同意する]** をクリックします。

以前と同様に、バックエンド アプリのデータを作成、読み取り、更新、および削除できるようになったはずです。 唯一の違いは、App Service の認証と承認によって、サービス間呼び出しも含めて、両方のアプリがセキュリティで保護されていることです。

お疲れさまでした。 サーバー コードは、認証されたユーザーの代わりにバックエンドのデータにアクセスするようになりました。

## <a name="call-api-securely-from-browser-code"></a>ブラウザー コードから API を安全に呼び出す

この手順では、フロントエンドの Angular.js アプリをバックエンド API に合わせて設定します。 そのようにして、アクセス トークンを取得し、それを使用してバックエンド アプリへの API 呼び出しを行う方法を学習します。

サーバー コードは要求ヘッダーにアクセスできますが、クライアント コードは `GET /.auth/me` にアクセスして同じアクセス トークンを取得することができます (「[Retrieve tokens in app code (アプリ コードでトークンを取得する)](app-service-authentication-how-to.md#retrieve-tokens-in-app-code)」を参照)。

> [!TIP]
> このセクションでは、標準の HTTP メソッドを使用して、セキュリティで保護された HTTP 呼び出しを行います。 ただし、[JavaScript 用 Microsoft Authentication Library](https://github.com/AzureAD/microsoft-authentication-library-for-js) を使用すると、Angular.js アプリケーション パターンを簡略化できます。
>

### <a name="configure-cors"></a>CORS を構成する

Cloud Shell で [`az webapp cors add`](/cli/azure/webapp/cors#az-webapp-cors-add) コマンドを使用して、クライアントの URL に対して CORS を有効にします。 _\<back-end-app-name>_ および _\<front-end-app-name>_ のプレースホルダーを置き換えます。

```azurecli-interactive
az webapp cors add --resource-group myAuthResourceGroup --name <back-end-app-name> --allowed-origins 'https://<front-end-app-name>.azurewebsites.net'
```

この手順は、認証と承認には無関係です。 ただし、ブラウザーで Angular.js アプリからのクロスドメイン API 呼び出しが許可されるようにするには、この手順が必要です。 詳細については、「[CORS 機能の追加](app-service-web-tutorial-rest-api.md#add-cors-functionality)」を参照してください。

### <a name="point-angularjs-app-to-back-end-api"></a>Angular.js アプリをバックエンド API に合わせて設定する

ローカル リポジトリで、_wwwroot/index.html_ を開きます。

行 51 の `apiEndpoint` 変数を、バックエンド アプリの HTTPS URL (`https://<back-end-app-name>.azurewebsites.net`) に設定します。 _\<back-end-app-name>_ は、App Service のアプリ名で置き換えます。

ローカル リポジトリで、_wwwroot/app/scripts/todoListSvc.js_ を開き、すべての API 呼び出しの前に `apiEndpoint` が付加されていることを確認します。 Angular.js アプリが、バックエンド API を呼び出すようになりました。 

### <a name="add-access-token-to-api-calls"></a>API 呼び出しにアクセス トークンを追加する

_wwwroot/app/scripts/todoListSvc.js_ の API 呼び出しの一覧の上 (`getItems : function(){` の行の上) で、次の関数を一覧に追加します。

```javascript
setAuth: function (token) {
    $http.defaults.headers.common['Authorization'] = 'Bearer ' + token;
},
```

この関数は、既定の `Authorization` ヘッダーにアクセス トークンを設定するために呼び出されます。 すぐ後の手順で、それを呼び出します。

ローカル リポジトリで _wwwroot/app/scripts/app.js_ を開き、次のコードを見つけます。

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
}).otherwise({ redirectTo: "/Home" });
```

コード ブロック全体を次のコードに置き換えます。

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
    resolve: {
        token: ['$http', 'todoListSvc', function ($http, todoListSvc) {
            return $http.get('/.auth/me').then(function (response) {
                todoListSvc.setAuth(response.data[0].access_token);
                return response.data[0].access_token;
            });
        }]
    },
}).otherwise({ redirectTo: "/Home" });
```

新しい変更では、`/.auth/me` を呼び出し、アクセス トークンを設定する、`resolve` マッピングを追加します。 こうすることで、`todoListCtrl` コントローラーをインスタンス化する前に、アクセス トークンを入手することができます。 この方法では、コントローラーによるすべての API 呼び出しにトークンが含まれます。

### <a name="deploy-updates-and-test"></a>更新をデプロイし、テストする

すべての変更を保存します。 ローカル ターミナル ウィンドウで、以下の Git コマンドを使用して、変更をフロントエンド アプリにデプロイします。

```bash
git add .
git commit -m "add authorization header for Angular"
git push frontend master
```

再度 `https://<front-end-app-name>.azurewebsites.net` に移動します。 バックエンド アプリのデータを Angular.js で直接作成、読み取り、更新、および削除できるようになったはずです。

お疲れさまでした。 クライアント コードは、認証されたユーザーの代わりにバックエンドのデータにアクセスするようになりました。

## <a name="when-access-tokens-expire"></a>アクセス トークンの有効期限が切れたら

アクセス トークンは、しばらくすると有効期限が切れます。 アプリに対する再認証をユーザーに強制することなくアクセス トークンを更新する方法については、「[Refresh identity provider tokens (ID プロバイダー トークンの更新)](app-service-authentication-how-to.md#refresh-identity-provider-tokens)」を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

前の手順では、リソース グループ内に Azure リソースを作成しました。 これらのリソースが将来必要になると想定していない場合、Cloud Shell で次のコマンドを実行して、リソース グループを削除します。

```azurecli-interactive
az group delete --name myAuthResourceGroup
```

このコマンドの実行には、少し時間がかかる場合があります。

<a name="next"></a>
## <a name="next-steps"></a>次のステップ

ここで学習した内容は次のとおりです。

> [!div class="checklist"]
> * 組み込みの認証および承認を有効にする
> * 未認証の要求に対してアプリをセキュリティで保護する
> * Azure Active Directory を ID プロバイダーとして使用する
> * サインインしたユーザーの代わりにリモート アプリにアクセスする
> * トークン認証を使用して、サービス間呼び出しをセキュリティで保護する
> * サーバー コードのアクセス トークンを使用する
> * クライアント (ブラウザー) コードのアクセス トークンを使用する

次のチュートリアルに進んで、カスタム DNS 名をアプリにマップする方法を確認してください。

> [!div class="nextstepaction"]
> [既存のカスタム DNS 名を Azure App Service にマップする](app-service-web-tutorial-custom-domain.md)
