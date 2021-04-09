---
title: チュートリアル:仮想ネットワークの App Service と Azure Database for PostgreSQL - フレキシブル サーバー (プレビュー) を使用して Django アプリをデプロイする
description: 仮想ネットワークの App Service と Azure Database for PostgreSQL - フレキシブル サーバー (プレビュー) を使用して Django アプリをデプロイする
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 09/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: bcc9ca0175e0e03c62c2ce2b91d8ec337756a3cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92490103"
---
# <a name="tutorial-deploy-django-app-with-app-service-and-azure-database-for-postgresql---flexible-server-preview"></a>チュートリアル:App Service と Azure Database for PostgreSQL - フレキシブル サーバー (プレビュー) を使用して Django アプリをデプロイする

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーはプレビュー段階です

このチュートリアルでは、仮想ネットワークの App Services と Azure Database for PostgreSQL - フレキシブル サーバーを使用して、Azure に Django アプリケーションをデプロイする方法について説明します。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

この記事では、Azure CLI バージョン 2.0 以降をローカルで実行している必要があります。 インストールされているバージョンを確認するには、`az --version` コマンドを実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

[az login](/cli/azure/authenticate-azure-cli) コマンドを使用して、アカウントにログインする必要があります。 対応するサブスクリプション名のコマンド出力で **id** プロパティを確認します。

```azurecli
az login
```

複数のサブスクリプションをお持ちの場合は、リソースが課金の対象となる適切なサブスクリプションを選択してください。 [az account set](/cli/azure/account) コマンドを使用して、アカウントの特定のサブスクリプション ID を選択します。 サブスクリプション ID プレースホルダーへのサブスクリプションを、**az login** 出力の **サブスクリプション ID** プロパティに置き換えます。

```azurecli
az account set --subscription <subscription id>
```
## <a name="clone-or-download-the-sample-app"></a>サンプル アプリをクローンまたはダウンロードする

# <a name="git-clone"></a>[git clone](#tab/clone)

サンプル リポジトリをクローンします。

```terminal
git clone https://github.com/Azure-Samples/djangoapp
```

次に、そのフォルダーに移動します。

```terminal
cd djangoapp
```

# <a name="download"></a>[ダウンロード](#tab/download)

[https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp) にアクセスして **[Clone]\(クローン\)** を選択し、 **[Download ZIP]\(ZIP のダウンロード\)** を選択します。

その ZIP ファイルを、*djangoapp* という名前のフォルダーに展開します。

次に、その *djangoapp* フォルダー内でターミナル ウィンドウを開きます。

---

djangoapp サンプルには、データ ドリブンの Django 投票アプリが含まれます。これを、Django ドキュメントの「[はじめての Django アプリ作成](https://docs.djangoproject.com/en/2.1/intro/tutorial01/)」に従って取得します。 参考までに、完成したアプリをここに記載しています。

このサンプルは、App Service のような運用環境で実行するために変更もされています。

- 運用環境の設定は、*azuresite/production.py* ファイルにあります。 開発の詳細は *azuresite/settings.py* にあります。
- `DJANGO_ENV` 環境変数を "production" に設定した場合に、アプリで運用環境の設定が使用されます。 この環境変数は、PostgreSQL データベース構成に使用する他のものと共に、チュートリアルの後半で作成します。

これらの変更は、任意の運用環境で実行するために Django を構成する場合に固有であり、App Service に固有ではありません。 詳細については、[Django デプロイ チェックリスト](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/)に関するページを参照してください。

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>新しい仮想ネットワークに PostgreSQL フレキシブル サーバーを作成する

次のコマンドを使用して、仮想ネットワーク (VNET) にプライベート フレキシブル サーバーとデータベースを作成します。
```azurecli
# Create Flexible server in a VNET

az postgres flexible-server create --resource-group myresourcegroup --location westus2

```

このコマンドによって次の操作が実行されます。これには数分かかる場合があります。

- リソース グループがまだ存在していない場合は作成します。
- サーバー名が指定されていない場合は、それが生成されます。
- 新しい postgreSQL サーバー用の新しい仮想ネットワークが作成されます。 Web アプリを同じ仮想ネットワークに追加する必要があるため、サーバー用に作成された **仮想ネットワーク名とサブネット名をメモしておいてください**。
- サーバーの管理者のユーザー名とパスワードが指定されていない場合は、それらが作成されます。 次の手順で使用するため、**ユーザー名とパスワードをメモしておいてください**。
- 開発のために使用できるデータベース ```postgres``` を作成します。 [**psql** を実行してデータベースに接続](quickstart-create-server-portal.md#connect-to-the-postgresql-database-using-psql)し、別のデータベースを作成できます。

> [!NOTE]
> 指定されていない場合に生成されるパスワードをメモしておいてください。 パスワードを忘れた場合は、``` az postgres flexible-server update``` コマンドを使用してパスワードをリセットする必要があります。


## <a name="deploy-the-code-to-azure-app-service"></a>Azure App Service にコードをデプロイする

このセクションでは App Service アプリでアプリ ホストを作成し、このアプリを Postgres データベースに接続して、そのホストにコードをデプロイします。


### <a name="create-the-app-service-web-app-in-a-virtual-network"></a>仮想ネットワークに App Service Web アプリを作成する

ターミナルで、現在の場所がアプリ コードを含むリポジトリのルート (`djangoapp`) であることを確認します。

[`az webapp up`](/cli/azure/webapp#az-webapp-up) コマンドを使用して App Service アプリ (ホスト プロセス) を作成します。

```azurecli

# Create a web app
az webapp up --resource-group myresourcegroup --location westus2 --plan DjangoPostgres-tutorial-plan --sku B1 --name <app-name>

# Enable VNET integration for web app.
# Replace <vnet-name> and <subnet-name> with the virtual network and subnet name that the flexible server is using.

az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet <vnet-name> --subnet <subnet-name>

# Configure database information as environment variables
# Use the postgres server name , database name , username , password for the database created in the previous steps

az webapp config appsettings set --settings DJANGO_ENV="production" DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="postgres" DBUSER="<username>" DBPASS="<password>"
```
- `--location` 引数には、前のセクションでデータベースに使用したのと同じ場所を使用します。
- *\<app-name>* は、すべての Azure で一意の名前に置き換えます (サーバー エンドポイントは `https://\<app-name>.azurewebsites.net`)。 *\<app-name>* に使用できる文字は `A`-`Z`、`0`-`9`、`-` です。 会社名とアプリ識別子を組み合わせて使用すると、適切なパターンになります。
- Basic 価格レベル (B1) で [App Service プラン](../../app-service/overview-hosting-plans.md) *DjangoPostgres-tutorial-plan* を作成します (存在しない場合)。 `--plan` と `--sku` は省略可能ですが、
- App Service アプリが存在しない場合は作成します。
- アプリの既定のログがまだ有効になっていない場合は、有効にします。
- ビルド オートメーションを有効にし、ZIP デプロイを使用してリポジトリをアップロードします。
- **az webapp vnet-integration** コマンドによって、Web アプリが postgres サーバーと同じ仮想ネットワークに追加されます。
- アプリ コードでは、さまざまな環境変数でデータベース情報を検索することを想定しています。 App Service で環境変数を設定するには、[az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) コマンドを使用して "アプリ設定" を作成します。

> [!TIP]
> 多くの Azure CLI コマンドでは、リソース グループの名前や App Service プランなどの一般的なパラメーターが、ファイル *.azure/config* にキャッシュされます。このため、後のコマンドで同じパラメーターをすべて指定する必要はありません。 たとえば、変更を加えた後でアプリを再デプロイするには、パラメーターを指定せずに `az webapp up` を再実行するだけです。

### <a name="run-django-database-migrations"></a>Django データベースの移行を実行する

Django データベースの移行によって、Azure データベース上の PostgreSQL のスキーマが、コードに記述されているスキーマと一致していることが確認されます。

1. ブラウザーで *https://\<app-name>.scm.azurewebsites.net/webssh/host* に移動して SSH セッションを開き、Azure アカウントの資格情報 (データベース サーバーの資格情報ではなく) を使用してサインインします。

1. SSH セッションで次のコマンドを実行します (**Ctrl**+**Shift**+**V** キーを使用してコマンドを貼り付けることができます)。

    ```bash
    cd site/wwwroot

    # Activate default virtual environment in App Service container
    source /antenv/bin/activate
    # Install packages
    pip install -r requirements.txt
    # Run database migrations
    python manage.py migrate
    # Create the super user (follow prompts)
    python manage.py createsuperuser
    ```

1. `createsuperuser` コマンドを使用すると、スーパーユーザーの資格情報の入力を求められます。 このチュートリアルの目的では、既定のユーザー名である `root` を使用し、**Enter** キーを押してメール アドレスを空白のままにして、パスワードを「`postgres1`」と入力します。

### <a name="create-a-poll-question-in-the-app"></a>アプリで投票の質問を作成する

1. ブラウザーで、URL *http:\//\<app-name>.azurewebsites.net* を開きます。 データベース内に特定の投票がまだないため、アプリには "No polls are available" (投票は利用できません) というメッセージが表示されます。

1. *http:\//\<app-name>.azurewebsites.net/admin* に移動します。前のセクションのスーパーユーザー資格情報 (`root` と `postgres1`) を使用してサインインします。 **[Polls]\(投票\)** で、 **[Questions]\(質問\)** の横の **[Add]\(追加\)** を選択し、いくつかの選択肢がある投票の質問を作成します。

1. もう一度 *http:\//\<app-name>.azurewebsites.net/* に移動し、質問がユーザーに表示されるようになったことを確認します。 質問に好きなように回答してデータベースにデータを生成します。

**お疲れさまでした。** アクティブな Postgres データベースを使用して、Azure App Service for Linux で Python Django Web アプリが実行されています。

> [!NOTE]
> App Service では、各サブフォルダー内で *wsgi.py* ファイル (`manage.py startproject` によって既定で作成されます) を探すことにより、Django プロジェクトが検出されます。 App Service でそのファイルが見つかると、Django Web アプリが読み込まれます。 詳細については、[組み込みの Python イメージの構成](../../app-service/configure-language-python.md)に関するページを参照してください。

## <a name="make-code-changes-and-redeploy"></a>コードの変更を加えて再デプロイする

このセクションでは、アプリに対してローカルでの変更を行い、App Service にコードを再デプロイします。 このプロセスで、進行中の作業をサポートする Python 仮想環境を設定します。

### <a name="run-the-app-locally"></a>アプリをローカルで実行する

ターミナル ウィンドウで次のコマンドを実行します。 スーパーユーザーを作成するときは、必ずプロンプトに従ってください。

```bash
# Configure the Python virtual environment
python3 -m venv venv
source venv/bin/activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```
Web アプリが完全に読み込まれると、Django 開発サーバーによって、次のメッセージにローカル アプリの URL が表示されます: "Starting development server at http://127.0.0.1:8000/. Quit the server with CTRL-BREAK" (http://127.0.0.1:8000/ で開発サーバーを起動しています。CTRL-BREAK キーを押してサーバーを終了します)。

:::image type="content" source="./media/tutorial-django-app-service-postgres/django-dev-server-output.png" alt-text="Django 開発サーバーの出力例":::

次の手順に従って、アプリをローカルでテストします。

1. ブラウザーで *http:\//localhost:8000* にアクセスすると、"No polls are available" (投票は利用できません) というメッセージが再び表示されます。

1. *http:\//localhost:8000/admin* に移動し、前の手順で作成した管理者ユーザーを使用してサインインします。 **[Polls]\(投票\)** で、 **[Questions]\(質問\)** の横の **[Add]\(追加\)** をもう一度選択し、いくつかの選択肢がある投票の質問を作成します。

1. *http:\//localhost:8000* に再び移動し、アプリをテストするために質問に回答します。

1. **Ctrl**+**C** キーを押して Django サーバーを停止します。

ローカルで実行している場合、アプリではローカルの Sqlite3 データベースを使用しており、運用データベースに干渉することはありません。 必要に応じて、ローカルの PostgreSQL データベースを使用して、運用環境をより正確にシミュレートすることもできます。



### <a name="update-the-app"></a>アプリの更新

`polls/models.py` で、`choice_text` で始まる行を見つけ、`max_length` パラメーターを 100 に変更します。

```python
# Find this lie of code and set max_length to 100 instead of 200
choice_text = models.CharField(max_length=100)
```

データ モデルを変更したので、新しい Django の移行を作成し、データベースを移行します。

```
python manage.py makemigrations
python manage.py migrate
```

`python manage.py runserver` を使用して開発サーバーを再度実行し、*http:\//localhost:8000/admin* でアプリをテストします。

**Ctrl**+**C** キーを使用して Django Web サーバーを再度停止します。


### <a name="redeploy-the-code-to-azure"></a>Azure にコードを再デプロイする

リポジトリのルートで次のコマンドを実行します。

```azurecli
az webapp up
```

このコマンドでは、 *.azure/config* ファイルにキャッシュされたパラメーターを使用します。 アプリが既に存在することが App Service によって検出されるので、コードの再デプロイだけが行われます。



### <a name="rerun-migrations-in-azure"></a>Azure で移行を再実行する

データ モデルに変更を加えたので、App Service へのデータベースの移行を再実行する必要があります。

ブラウザーで *https://\<app-name>.scm.azurewebsites.net/webssh/host* にアクセスして SSH セッションを再度開きます。 次のコマンドを実行します。

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>運用環境でアプリを確認する

*http:\//\<app-name>.azurewebsites.net* に移動し、運用環境でアプリを再度テストします (データベース フィールドの長さを変更しただけなので、質問の作成時に長い回答を入力しようとした場合にのみ、この変更が明らかになります)。

> [!TIP]
> [django-storages](https://django-storages.readthedocs.io/en/latest/backends/azure.html) を使用して、静的資産とメディア資産を Azure ストレージに格納できます。 静的ファイルを gzip 処理するために Azure CDN を使用できます。


## <a name="manage-your-app-in-the-azure-portal"></a>Azure portal でアプリを管理する

[Azure portal](https://portal.azure.com) でアプリ名を探し、結果内のアプリを選択します。

:::image type="content" source="./media/tutorial-django-app-service-postgres/navigate-to-django-app-in-app-services-in-the-azure-portal.png" alt-text="Azure portal で Python Django アプリに移動する":::

既定では、アプリの **[Overview]\(概要\)** ページがポータルに表示されます。これにより、全般的なパフォーマンス ビューが提供されます。 ここでは、参照、停止、再開、削除といった基本的な管理タスクを実行することもできます。 ページの左側にあるタブは、開くことができるさまざまな構成ページを示しています。

:::image type="content" source="./media/tutorial-django-app-service-postgres/manage-django-app-in-app-services-in-the-azure-portal.png" alt-text="Azure portal の [概要] ページで Python Django アプリを管理する":::


## <a name="clean-up-resources"></a>リソースをクリーンアップする

アプリを残しておく場合、または次のチュートリアルに進む場合は、「[次のステップ](#next-steps)」に進んでください。 それ以外の場合は、継続して料金が発生しないように、このチュートリアルで作成したリソース グループを削除できます。

```azurecli
az group delete -g myresourcegroup
```

このコマンドでは、 *.azure/config* ファイルにキャッシュされたリソース グループ名が使用されます。 リソース グループを削除することによって、その中に含まれるすべてのリソースの割り当て解除と削除も行われます。

## <a name="next-steps"></a>次のステップ

カスタム DNS 名をアプリにマップする方法を確認する:

> [!div class="nextstepaction"]
> [チュートリアル:カスタム DNS 名をアプリにマップする](../../app-service/app-service-web-tutorial-custom-domain.md)

App Service で Python アプリが実行される方法を確認する:

> [!div class="nextstepaction"]
> [Python アプリの構成](../../app-service/configure-language-python.md)
