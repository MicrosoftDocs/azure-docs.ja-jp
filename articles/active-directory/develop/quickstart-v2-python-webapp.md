---
title: Microsoft ID プラットフォーム Python Web アプリのクイックスタート | Azure
description: OAuth2 を使用して、Python Web アプリ上で Microsoft サインインを実装する方法について説明します。
services: active-directory
documentationcenter: dev-center-name
author: abhidnya13
editor: ''
ms.assetid: 9551f0b5-04f2-44d7-87b5-756409180fe9
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2019
ms.author: abpati
ms.custom: aaddev
ms.openlocfilehash: c5817427102bf10dcd1ece932b0f582d973efaf7
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257901"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>クイック スタート: Python Web アプリに Microsoft でサインインを追加する

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

このクイックスタートでは、Python Web アプリを Microsoft ID プラットフォームと統合する方法を説明します。 お使いのアプリによって、ユーザーがサインインされ、Microsoft Graph API を呼び出すためのアクセス トークンが取得されて、Microsoft Graph API への要求が行われます。

このガイドを完了すると、アプリケーションは、個人用の Microsoft アカウント (outlook.com、live.com など) と、Azure Active Directory を使用する会社や組織の職場または学校アカウントのサインインを受け入れるようになります。

![このクイック スタートで生成されたサンプル アプリの動作の紹介](media/quickstart-v2-python-webapp/python-quickstart.svg)

## <a name="prerequisites"></a>前提条件

このサンプルを実行するには、次のものが必要になります。

- [Python 2.7 以降](https://www.python.org/downloads/release/python-2713)または [Python 3 以降](https://www.python.org/downloads/release/python-364/)
- [Flask](http://flask.pocoo.org/)、[Flask-Session](https://pythonhosted.org/Flask-Session/)、[要求](https://2.python-requests.org/en/master/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) 
- Azure Active Directory (Azure AD) テナント。 Azure AD テナントの取得方法の詳細については、[Azure AD テナントの取得方法](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)に関するページを参照してください。

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>クイック スタート アプリを登録してダウンロードする
>
> クイックスタート アプリケーションを開始する方法としては、[簡易] (オプション 1) と [手動] (オプション 2) の 2 つの選択肢があります。
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>オプション 1:アプリを登録して自動構成を行った後、コード サンプルをダウンロードする
>
> 1. [Azure portal の [アプリの登録]](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) に移動します。
> 1. **[新規登録]** を選択します。
> 1. アプリケーションの名前を入力し、 **[登録]** を選択します。
> 1. 指示に従って新しいアプリケーションをダウンロードし、自動構成します。
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>オプション 2:アプリケーションを登録し、アプリケーションとコード サンプルを手動で構成する
>
> #### <a name="step-1-register-your-application"></a>手順 1: アプリケーションの登録
>
> アプリケーションを登録し、その登録情報をソリューションに手動で追加するには、次の手順を実行します。
>
> 1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
> 1. ご利用のアカウントで複数のテナントにアクセスできる場合は、右上隅でアカウントを選択し、ポータルのセッションを目的の Azure AD テナントに設定します。
> 1. 開発者用の Microsoft ID プラットフォームの [[アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) ページに移動します。
> 1. **[新規登録]** を選択します。
> 1. **[アプリケーションの登録]** ページが表示されたら、以下のアプリケーションの登録情報を入力します。
>      - **[名前]** セクションに、アプリのユーザーに表示されるわかりやすいアプリケーション名を入力します (例: `python-webapp`)。
>      - **[サポートされているアカウントの種類]** で、 **[Accounts in any organizational directory and personal Microsoft accounts]\(任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント\)** を選択します。
>      - **[リダイレクト URI]** セクションのドロップダウン リストで、**Web** プラットフォームを選択し、値を `http://localhost:5000/getAToken` に設定します。
>      - **[登録]** を選択します。 後で使用するために、アプリの **[概要]** ページで、 **[アプリケーション (クライアント) ID]** の値を書き留めます。
> 1. 左側のメニューで **[証明書とシークレット]** を選択し、 **[クライアント シークレット]** セクションで **[新しいクライアント シークレット]** をクリックします。
>
>      - キーの説明 (インスタンス アプリ シークレットの) を入力します。
>      - キーの有効期間として **[1 年]** を選択します。
>      - **[追加]** をクリックすると、キーの値が表示されます。
>      - キーの値をコピーします。 この情報は後で必要になります。
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>手順 1:Azure portal でのアプリケーションの構成
>
> このクイックスタートのコード サンプルを動作させるには、以下の操作が必要です。
>
> 1. 応答 URL を `http://localhost:5000/getAToken` として追加する
> 1. クライアント シークレットを作成します。
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [この変更を行う]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![構成済み](media/quickstart-v2-aspnet-webapp/green-check.png) アプリケーションはこの属性で構成されています

#### <a name="step-2-download-your-project"></a>手順 2: プロジェクトのダウンロード

[コード サンプルのダウンロード](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

#### <a name="step-3-configure-the-application"></a>手順 3: アプリケーションの構成

1. ルート フォルダーに近いローカル フォルダー (例: **C:\Azure-Samples**) に zip ファイルを展開します。
1. 統合開発環境を使用する場合は、その IDE でサンプルを開きます (オプション)。
1. **app_config.py** ファイルを開きます。このファイルはルート フォルダーにあり、次のコード スニペットに置き換えることができます。

```python
AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
CLIENT_ID = "Enter_the_Application_Id_here"
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
SCOPE = ["https://graph.microsoft.com/User.Read"]
REDIRECT_URI = "http://localhost:5000/getAToken"
```

> [!div renderon="docs"]
> 各値の説明:
>
> - `Enter_the_Application_Id_here` - 登録したアプリケーションのアプリケーション ID。
> - `Enter_the_Tenant_Info_Here` - 以下のいずれかのオプション。
>   - アプリケーションでサポートされるのが **[所属する組織のみ]** である場合、この値を**テナント ID** または**テナント名** (例: contoso.onmicrosoft.com) に置き換えます
>   - アプリケーションで **[任意の組織のディレクトリ内のアカウント]** がサポートされる場合は、この値を `organizations` に置き換えます。
>   - アプリケーションで **[すべての Microsoft アカウント ユーザー]** がサポートされる場合は、この値を `common` に置き換えます。
> - `Enter_the_Client_Secret_Here` - 登録済みアプリケーション用に **[証明書とシークレット]** で作成した **[クライアント シークレット]** です。

#### <a name="step-4-run-the-code-sample"></a>手順 4:コード サンプルの実行

- MSAL Python ライブラリ、Flask フレームワーク、サーバー側のセッション管理用の Flask-Sessions、および requests を、次のように pip を使用してインストールする必要があります。

```Shell
pip install msal
pip install flask
pip install Flask-Session
pip install requests
```

- Flask 用の環境変数が既に設定されている場合: シェルまたはコマンド ラインから app.py を実行する:

```Shell
python app.py
```

- Flask 用の環境変数が設定されていない場合:

    1. プロジェクト ディレクトリに移動し、シェルまたはコマンド ライン上に次のコマンドを入力します。

```Shell
export FLASK_APP=app.py
export FLASK_DEBUG=1
flask run
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
