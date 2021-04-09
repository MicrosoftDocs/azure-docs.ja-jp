---
title: チュートリアル:Python Web アプリケーションで認証を行う
titleSuffix: Azure AD B2C
description: このチュートリアルでは、Azure Active Directory B2C を使用して、Python Flask Web アプリケーションにユーザー ログインを提供する方法を学習します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 06/12/2020
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.custom: devx-track-python
ms.openlocfilehash: 56e92a698446bd417c74820d6da662ad9ee55c77
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100555688"
---
# <a name="tutorial-enable-authentication-in-a-python-web-application-with-azure-ad-b2c"></a>チュートリアル:Python Web アプリケーションで Azure AD B2C を使用して認証を行う

このチュートリアルでは、Python Flask Web アプリケーションで Azure Active Directory B2C (Azure AD B2C) を使用してユーザーのサインアップとサインインを行う方法について説明します。

このチュートリアルの内容:

> [!div class="checklist"]
> * Azure AD B2C テナントに登録されているアプリケーションに応答 URL を追加する
> * GitHub からコード サンプルをダウンロードする
> * サンプル アプリケーションのコードをテナントで動作するように変更する
> * サインアップ/サインイン ユーザー フローを使用してサインアップする

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルの手順を続ける前に、次の Azure AD B2C リソースを用意しておく必要があります。

* [Azure AD B2C テナント](tutorial-create-tenant.md)
* テナントに [登録されたアプリケーション](tutorial-register-applications.md)とその *アプリケーション (クライアント) ID* および *クライアント シークレット*
* ご利用のテナントで[作成したユーザー フロー](tutorial-create-user-flows.md)

さらに、ご利用のローカル開発環境には次のものが必要です。

* [Visual Studio Code](https://code.visualstudio.com/) または別のコード エディター
* [Python](https://nodejs.org/en/download/) 2.7 以降または 3 以降

## <a name="add-a-redirect-uri"></a>リダイレクト URI を追加する

前提条件の一環として完了した 2 番目のチュートリアルで、Azure AD B2C に Web アプリケーションを登録しました。 このチュートリアルのコード サンプルとの通信を可能にするには、アプリケーションの登録に応答 URL (リダイレクト URI とも呼ばれます) を追加します。

Azure AD B2C テナントでアプリケーションを更新するには、Microsoft の新しい統合 **アプリの登録** エクスペリエンスか以前の **アプリケーション (レガシ)** エクスペリエンスを使用できます。 [この新しいエクスペリエンスの詳細を参照してください](./app-registrations-training-guide.md)。

#### <a name="app-registrations"></a>[アプリの登録](#tab/app-reg-ga/)

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリの登録]** 、 **[所有しているアプリケーション]** タブ、*webapp1* アプリケーションの順に選択します。
1. **[管理]** で、 **[認証]** を選択します。
1. **[Web]** で **[URI の追加]** リンクを選択し、テキスト ボックスに「`http://localhost:5000/getAToken`」と入力します。
1. **[保存]** を選択します。

#### <a name="applications-legacy"></a>[アプリケーション (レガシ)](#tab/applications-legacy/)

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリケーション (レガシ)]** を選択し、*webapp1* アプリケーションを選択します。
1. **[応答 URL]** に「`http://localhost:5000/getAToken`」を追加します。
1. **[保存]** を選択します。
* * *

## <a name="get-the-sample-code"></a>サンプル コードの取得

このチュートリアルでは、GitHub からダウンロードしたコード サンプルを B2C テナントで動作するように構成します。 サンプルには、Python Flask Web アプリケーションで Azure AD B2C を使用してユーザーのサインアップおよびサインインを行う方法が示されています。

[.ZIP アーカイブをダウンロード](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)するか、または GitHub から[コード サンプル リポジトリ](https://github.com/Azure-Samples/ms-identity-python-webapp)を複製します。

```console
git clone https://github.com/Azure-Samples/ms-identity-python-webapp.git
```

## <a name="update-the-sample"></a>サンプルを更新する

サンプルを入手したら、Azure AD B2C テナントとアプリケーションの登録、ユーザー フローを使用するようにアプリケーションを構成します。

プロジェクトのルート ディレクトリで次の作業を行います。

1. *app_config.py* ファイルの名前を *app_config.py.OLD* に変更します。
1. *app_config_b2c.py* の名前を *app_config.py* に変更します。

先ほど名前を変更した *app_config.py* の値を、前提条件のセクションで作成した Azure AD B2C テナントとアプリケーションの登録に合わせて更新します。

1. お使いのエディターで *app_config.py* ファイルを開きます。
1. `b2c_tenant` の値を自分の Azure AD B2C テナントの名前に更新します (例: *contosob2c*)。
1. 前提条件のセクションで作成したユーザー フローの名前に合わせて `*_user_flow` の各値を更新します。
1. `CLIENT_ID` 値を、前提条件のセクションで登録した Web アプリケーションの **アプリケーション (クライアント) ID** に更新します。
1. `CLIENT_SECRET` の値を、前提条件で作成した **クライアント シークレット** の値に更新します。 実際には、コメントでも推奨されているように、セキュリティ強化の観点から、この値は **環境変数** に格納することを検討してください。

これで *app_config.py* の最上部は、次のコード スニペットのようになります。

```python
import os

b2c_tenant = "contosob2c"
signupsignin_user_flow = "B2C_1_signupsignin1"
editprofile_user_flow = "B2C_1_profileediting1"
resetpassword_user_flow = "B2C_1_passwordreset1"
authority_template = "https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{user_flow}"

CLIENT_ID = "11111111-1111-1111-1111-111111111111" # Application (client) ID of app registration

CLIENT_SECRET = "22222222-2222-2222-2222-222222222222" # Placeholder - for use ONLY during testing.
# In a production app, we recommend you use a more secure method of storing your secret,
# like Azure Key Vault. Or, use an environment variable as described in Flask's documentation:
# https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables
# CLIENT_SECRET = os.getenv("CLIENT_SECRET")
# if not CLIENT_SECRET:
#     raise ValueError("Need to define CLIENT_SECRET environment variable")
```

> [!WARNING]
> コード スニペットのコメントにも注意書きがあるように、アプリケーション コードでは、**シークレットをプレーンテキストに格納することは避けて** ください。 コード サンプルでは変数をハードコーディングしていますが、これは "*あくまで便宜上*" の措置です。 環境変数を使用するか、Azure Key Vault のようなシークレット格納を使用することを検討してください。

## <a name="run-the-sample"></a>サンプルを実行する

1. コンソールまたはターミナルから、サンプルが格納されているディレクトリに切り替えます。 次に例を示します。

    ```console
    cd ms-identity-python-webapp
    ```
1. 次のコマンドを実行して、PyPi から必要なパッケージをインストールし、ローカル コンピューターで Web アプリを実行します。

    ```console
    pip install -r requirements.txt
    flask run --host localhost --port 5000
    ```

    コンソール ウィンドウには、ローカルで稼働中のアプリケーションのポート番号が表示されます。

    ```console
     * Serving Flask app "app" (lazy loading)
     * Environment: production
       WARNING: This is a development server. Do not use it in a production deployment.
       Use a production WSGI server instead.
     * Debug mode: off
     * Running on http://localhost:5000/ (Press CTRL+C to quit)
    ```

1. `http://localhost:5000` を参照して、ローカル コンピューターで実行されている Web アプリケーションを表示します。

    :::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-01.png" alt-text="ローカルで実行されている Python Flask Web アプリケーションを示す Web ブラウザー":::

### <a name="sign-up-using-an-email-address"></a>メール アドレスを使用してサインアップする

このサンプル アプリケーションでは、サインアップ、サインイン、パスワードのリセットがサポートされています。 このチュートリアルでは、メール アドレスを使用してサインアップします。

1. **[Sign In]\(サインイン\)** を選択して、前の手順で指定した *B2C_1_signupsignin1* ユーザー フローを開始します。
1. Azure AD B2C によって、サインアップ リンクを含むサインイン ページが表示されます。 まだアカウントを持っていないため、 **[Sign up now]\(今すぐサインアップ\)** リンクを選択します。
1. サインアップ ワークフローによって、メール アドレスを使用してユーザーの ID を収集および確認するためのページが表示されます。 また、サインアップ ワークフローでは、ユーザーのパスワードと、ユーザー フローで定義されている要求された属性も収集されます。

    有効なメール アドレスを使用し、確認コードを使用して検証します。 パスワードを設定します。 要求された属性の値を入力します。

    :::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-02.png" alt-text="Azure AD B2C ユーザー フローによって表示されるサインアップ ページ":::

1. **[作成]** を選択して、Azure AD B2C ディレクトリにローカル アカウントを作成します。

**[Create]\(作成\)** を選択すると、サインインしているユーザーの名前がアプリケーションに表示されます。

:::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-03.png" alt-text="ログイン ユーザーを表示している Python Flask Web アプリケーションを示す Web ブラウザー":::

サインインをテストする場合は、 **[Logout]\(ログアウト\)** リンクを選択してから、 **[Sign In]\(サインイン\)** を選択し、サインアップした時に入力したメール アドレスとパスワードでサインインします。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure AD B2C テナントのユーザー フローと連携するように Python Flask Web アプリケーションを構成し、サインアップとサインインの機能を提供しました。 次の手順を完了しました。

> [!div class="checklist"]
> * Azure AD B2C テナントに登録されているアプリケーションに応答 URL を追加しました
> * GitHub からコード サンプルをダウンロードしました
> * サンプル アプリケーションのコードをテナントで動作するように変更しました
> * サインアップ/サインイン ユーザー フローを使用してサインアップしました

この後は、Azure AD B2C によってユーザーに表示されるユーザー フロー ページの UI をカスタマイズする方法について見ていきましょう。

> [!div class="nextstepaction"]
> [Azure AD B2C でユーザー エクスペリエンスのインターフェイスをカスタマイズする >](customize-ui.md)