---
title: Microsoft ID プラットフォーム Python デーモン | Azure
description: Python プロセスでアクセス トークンを取得し、Microsoft ID プラットフォーム エンドポイントによって保護されている API をアプリ自体の ID を使用して呼び出す方法について説明します
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/22/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, devx-track-python, scenarios:getting-started, languages:Python
ms.openlocfilehash: 0969afa95009255981381d41268f416a615dd9f3
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88683744"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-python-console-app-using-apps-identity"></a>クイック スタート:トークンを取得し、Python コンソール アプリからアプリの ID を使用して Microsoft Graph API を呼び出す

このチュートリアルでは、アプリの ID を使用してアクセス トークンを取得した後、Microsoft Graph API を呼び出して、ディレクトリ内の[ユーザーの一覧](/graph/api/user-list)を表示する Python アプリケーションを記述します。 このシナリオは、オペレーターがいない無人のジョブや、ユーザーの ID ではなくアプリケーション ID を使用して実行する必要がある Windows サービスがある状況で役立ちます。

> [!div renderon="docs"]
> ![このクイック スタートで生成されたサンプル アプリの動作の紹介](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

## <a name="prerequisites"></a>前提条件

このサンプルを実行するには、以下が必要です。

- [Python 2.7 以降](https://www.python.org/downloads/release/python-2713)または [Python 3 以降](https://www.python.org/downloads/release/python-364/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>クイック スタート アプリを登録してダウンロードする

> [!div renderon="docs" class="sxs-lookup"]
>
> クイック スタート アプリケーションを開始する方法としては、次の 2 つの選択肢があります。[簡易] (以下のオプション 1) と [手動] (オプション 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>オプション 1: アプリを登録して自動構成を行った後、コード サンプルをダウンロードする
>
> 1. 新しい [Azure portal の [アプリの登録]](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/PythonDaemonQuickstartPage/sourceType/docs) ウィンドウに移動します。
> 1. アプリケーションの名前を入力し、 **[登録]** を選択します。
> 1. 画面の指示に従ってダウンロードし、1 回クリックするだけで、新しいアプリケーションが自動的に構成されます。
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>オプション 2:アプリケーションを登録し、アプリケーションとコード サンプルを手動で構成する

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>手順 1:アプリケーションの登録
> アプリケーションを登録し、その登録情報をソリューションに手動で追加するには、次の手順を実行します。
>
> 1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
> 1. ご利用のアカウントで複数のテナントにアクセスできる場合は、右上隅でアカウントを選択し、ポータルのセッションを目的の Azure AD テナントに設定します。
> 1. 開発者用の Microsoft ID プラットフォームの [[アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) ページに移動します。
> 1. **[新規登録]** を選択します。
> 1. **[アプリケーションの登録]** ページが表示されたら、以下のアプリケーションの登録情報を入力します。
> 1. **[名前]** セクションに、アプリのユーザーに表示されるわかりやすいアプリケーション名 (`Daemon-console` など) を入力した後、 **[登録]** を選択してアプリケーションを作成します。
> 1. 登録されたら、 **[証明書とシークレット]** メニューを選択します。
> 1. **[クライアント シークレット]** で、 **[+ 新しいクライアント シークレット]** を選択します。 名前を付け、 **[追加]** を選択します。 シークレットを安全な場所にコピーします。 コード内でそれを使用する必要があります。
> 1. 次に、 **[API のアクセス許可]** メニューを選択し、 **[+ アクセス許可の追加]** ボタンをクリックし、 **[Microsoft Graph]** を選択します。
> 1. **[アプリケーションのアクセス許可]** を選択します。
> 1. **[ユーザー]** ノードで、 **[User.Read.All]** を選択し、 **[アクセス許可の追加]** を選択します。

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>クイックスタート アプリをダウンロードして構成する
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>手順 1:Azure portal でのアプリケーションの構成
> このクイック スタート用サンプル コードを動作させるには、クライアント シークレットを作成し、Graph API の **User.Read.All** アプリケーションのアクセス許可を追加します。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [これらの変更を行います]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![構成済み](media/quickstart-v2-netcore-daemon/green-check.png) アプリケーションはこれらの属性で構成されています。

#### <a name="step-2-download-your-python-project"></a>手順 2:Python プロジェクトのダウンロード

> [!div renderon="docs"]
> [Python デーモン プロジェクトをダウンロードする](https://github.com/Azure-Samples/ms-identity-python-daemon/archive/master.zip)

> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [コード サンプルをダウンロードします](https://github.com/Azure-Samples/ms-identity-python-daemon/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`


> [!div renderon="docs"]
> #### <a name="step-3-configure-your-python-project"></a>手順 3:Python プロジェクトの構成
>
> 1. ディスクのルートに近いローカル フォルダー (例: **C:\Azure-Samples**) に zip ファイルを展開します。
> 1. サブフォルダー **1-Call-MsGraph-WithSecret"** に移動します。
> 1. **parameters.json** を編集し、`authority`、`client_id`、および `secret` フィールドの値を次のスニペットに置き換えます。
>
>    ```json
>    "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
>    "client_id": "Enter_the_Application_Id_Here",
>    "secret": "Enter_the_Client_Secret_Here"
>    ```
>    各値の説明:
>    - `Enter_the_Application_Id_Here` - 登録したアプリケーションの**アプリケーション (クライアント) ID**。
>    - `Enter_the_Tenant_Id_Here` - この値を**テナント ID** または**テナント名** (例: contoso.microsoft.com) に置き換えます。
>    - `Enter_the_Client_Secret_Here` - この値を手順 1 で作成されたクライアント シークレットに置き換えます。
>
> > [!TIP]
> > **アプリケーション (クライアント) ID** と**ディレクトリ (テナント) ID** の値を見つけるには、Azure portal 上でアプリの **[概要]** ページに移動します。 新しいキーを生成するには、 **[証明書とシークレット]** ページに移動します。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>手順 3:管理者の同意

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>手順 4:管理者の同意

この時点でアプリケーションを実行すると、*HTTP 403 - Forbidden* エラー "`Insufficient privileges to complete the operation`" が表示されます。 これは、すべての "*アプリ専用のアクセス許可*" には管理者の同意が必要であるために発生します。ディレクトリのグローバル管理者にお使いのアプリケーションに同意してもらう必要があります。 ご自身のロールに応じて、次のオプションのいずれかを選択します。

##### <a name="global-tenant-administrator"></a>グローバル テナント管理者

> [!div renderon="docs"]
> グローバル テナント管理者の場合は、Azure Portal 上で [アプリケーションの登録 (プレビュー)] の **[API のアクセス許可]** ページに移動し、 **[{Tenant Name} に管理者の同意を与えます]** ({Tenant Name} はお使いのディレクトリの名前) を選択します。

> [!div renderon="portal" class="sxs-lookup"]
> グローバル管理者の場合は、 **[API のアクセス許可]** ページに移動し、 **[Enter_the_Tenant_Name_Here に管理者の同意を与えます]** を選択します。
> > [!div id="apipermissionspage"]
> > [[API のアクセス許可] ページに移動する]()

##### <a name="standard-user"></a>標準ユーザー

テナントの標準ユーザーの場合は、お使いのアプリケーションに管理者の同意を与えるようグローバル管理者に依頼する必要があります。 これを行うには、次の URL を管理者に知らせます。

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> 各値の説明:
>> * `Enter_the_Tenant_Id_Here` - この値を**テナント ID** または**テナント名** (例: contoso.microsoft.com) に置き換えます。
>> * `Enter_the_Application_Id_Here` - 登録したアプリケーションの**アプリケーション (クライアント) ID**。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>手順 4:アプリケーションの実行

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>手順 5:アプリケーションの実行

このサンプルの依存関係を 1 回インストールする必要があります

```console
pip install -r requirements.txt
```

次に、コマンド プロンプトまたはコンソールを使用して、アプリケーションを実行します。

```console
python confidential_client_secret_sample.py parameters.json
```

コンソール出力には、Azure AD ディレクトリ内のユーザーの一覧を表すいくつかの Json フラグメントが表示されます。

> [!IMPORTANT]
> このクイック スタート アプリケーションは、クライアント シークレットを使用して、それ自体を機密クライアントとして識別します。 クライアント シークレットはプロジェクト ファイルにプレーン テキストとして追加されるため、セキュリティ上の理由から、アプリケーションを運用アプリケーションと見なす前に、クライアント シークレットの代わりに証明書を使用することをお勧めします。 証明書の使用方法の詳細については、このサンプルと同じ GitHub リポジトリの 2 つ目のフォルダー **2-Call-MsGraph-WithCertificate** にある[これらの手順](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/README.md)を参照してください

## <a name="more-information"></a>詳細情報

### <a name="msal-python"></a>MSAL Python

[MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) は、ユーザーをサインインし、Microsoft ID プラットフォームによって保護されている API にアクセスするトークンを要求するために使用するライブラリです。 説明したとおり、このクイック スタートでは、委任されたアクセス許可ではなく、アプリケーション自体の ID を使用してトークンを要求しています。 ここで使用される認証フローは、" *[クライアント資格情報 OAuth フロー](v2-oauth2-client-creds-grant-flow.md)* " と呼ばれます。 デーモン アプリでの MSAL Python の使用方法の詳細については、[この記事](scenario-daemon-overview.md)を参照してください。

 MSAL Python は、次の pip コマンドを実行してインストールできます。

```powershell
pip install msal
```

### <a name="msal-initialization"></a>MSAL の初期化

MSAL への参照を追加するには、次のコードを追加します。

```Python
import msal
```

続いて、次のコードを使用して MSAL を初期化します。

```Python
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"])
```

> | 各値の説明: |説明 |
> |---------|---------|
> | `config["secret"]` | Azure Portal 上でアプリケーションに対して作成されるクライアント シークレット。 |
> | `config["client_id"]` | Azure portal に登録されているアプリケーションの "**アプリケーション (クライアント) ID**"。 この値は、Azure portal のアプリの **[概要]** ページで確認できます。 |
> | `config["authority"]`    | ユーザーが認証するための STS エンドポイント。 通常、パブリック クラウド上では `https://login.microsoftonline.com/{tenant}` です。{tenant} はご自分のテナントの名前またはテナント ID です。|

詳細については、[`ConfidentialClientApplication` 用の参照ドキュメント](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication)をご覧ください。

### <a name="requesting-tokens"></a>トークンの要求

アプリの ID を使用してトークンを要求するには、`AcquireTokenForClient` メソッドを使用します。

```Python
result = None
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])
```

> |各値の説明:| 説明 |
> |---------|---------|
> | `config["scope"]` | 要求されるスコープが含まれています。 Confidential クライアントの場合は、`{Application ID URI}/.default` のような形式を使用して、要求されるスコープが Azure Portal 上で設定されるアプリ オブジェクト内に静的に定義されたものであることを示す必要があります (Microsoft Graph では、`{Application ID URI}` は `https://graph.microsoft.com` を指します)。 カスタム Web API の場合、`{Application ID URI}` は、Azure portal 上で [アプリケーションの登録 (プレビュー)] の **[API の公開]** セクションに定義されます。 |

詳細については、[`AcquireTokenForClient` 用の参照ドキュメント](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client)をご覧ください。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>次のステップ

デーモン アプリケーションの詳細については、シナリオのランディング ページを参照してください。

> [!div class="nextstepaction"]
> [Web API を呼び出すデーモン アプリケーション](scenario-daemon-overview.md)

デーモン アプリケーションのチュートリアルについては、次のページを参照してください。

> [!div class="nextstepaction"]
> [デーモン Python コンソールのチュートリアル](https://github.com/Azure-Samples/ms-identity-python-daemon)

アクセス許可と同意について学習します。

> [!div class="nextstepaction"]
> [アクセス許可と同意](v2-permissions-and-consent.md)

このシナリオ用の認証フローの詳細については、OAuth 2.0 クライアント資格情報フローを参照してください。

> [!div class="nextstepaction"]
> [クライアント資格情報 OAuth フロー](v2-oauth2-client-creds-grant-flow.md)