---
title: 'クイックスタート: GitHub Actions を使用して Azure PostgreSQL に接続する'
description: GitHub Actions ワークフローから Azure PostgreSQL を使用します
author: mksuni
ms.service: postgresql
ms.topic: quickstart
ms.author: sumuth
ms.date: 10/12/2020
ms.custom: github-actions-azure
ms.openlocfilehash: 2e546801f95d9d884bdfb3f09a18b3fa6e2d78a1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97364988"
---
# <a name="quickstart-use-github-actions-to-connect-to-azure-postgresql"></a>クイックスタート: GitHub Actions を使用して Azure PostgreSQL に接続する

<Token>**適用対象:** :::image type="icon" source="./media/applies-to/yes.png" border="false":::Azure Database for PostgreSQL - 単一サーバー :::image type="icon" source="./media/applies-to/yes.png" border="false":::Azure Database for PostgreSQL - フレキシブル サーバー</Token>

[Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql/) にデータベースの更新をデプロイするワークフローを使用することによって、[GitHub Actions](https://docs.github.com/en/actions) の使用を開始します。

## <a name="prerequisites"></a>前提条件

以下のものが必要になります。
- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- サンプル データ (`data.sql`) が格納された GitHub リポジトリ。 GitHub アカウントをお持ちでない場合は、[無料でサインアップ](https://github.com/join)できます。
- Azure Database for PostgreSQL サーバー。
    - [クイック スタート:Azure Portal で Azure Database for PostgreSQL サーバーを作成する](quickstart-create-server-database-portal.md)

## <a name="workflow-file-overview"></a>ワークフロー ファイルの概要

GitHub Actions ワークフローは、お使いのリポジトリの `/.github/workflows/` パスの YAML (.yml) ファイルに定義されます。 この定義には、ワークフローを構成するさまざまな手順とパラメーターが含まれます。

このファイルには 2 つのセクションがあります。

|Section  |タスク  |
|---------|---------|
|**認証** | 1.サービス プリンシパルを定義します。 <br /> 2.GitHub シークレットを作成します。 |
|**デプロイする** | 1. データベースをデプロイします。 |

## <a name="generate-deployment-credentials"></a>デプロイ資格情報を生成する

[サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md)は、[Azure CLI](/cli/azure/) で [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac&preserve-view=true) コマンドを使用して作成できます。 このコマンドは、Azure portal で [Azure Cloud Shell](https://shell.azure.com/) を使用するか、 **[試してみる]** ボタンを選択して実行します。

プレースホルダー `server-name` を、Azure でホストされている PostgreSQL サーバーの名前で置き換えます。 `subscription-id` と `resource-group` を、サブスクリプション ID と、PostgreSQL サーバーに接続されているリソース グループで置き換えます。

```azurecli-interactive
   az ad sp create-for-rbac --name {server-name} --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
```

これにより、以下のような、ご自分のデータベースにアクセスするためのロールの割り当て資格情報を含む JSON オブジェクトが出力されます。 後で使用できるように、この出力 JSON オブジェクトをコピーします。

```output
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> 常に最小限のアクセス権を付与することをお勧めします。 前の例の範囲は、リソース グループ全体ではなく、特定のサーバーに限定されます。

## <a name="copy-the-postgresql-connection-string"></a>PostgreSQL 接続文字列をコピーする

Azure portal でご自分の Azure Database for PostgreSQL サーバーにアクセスし、 **[設定]**  >  **[接続文字列]** を開きます。 **ADO.NET** の接続文字列をコピーします。 プレースホルダー `your_database` および `your_password` の値を置き換えます。 接続文字列は、次のようになります。

> [!IMPORTANT]
> - シングル サーバーの場合、```user=adminusername@servername``` を使用します。 ```@servername``` は必須です。
> - フレキシブル サーバーの場合、```user= adminusername``` を使用します。```@servername``` は不要です。

```output
psql host={servername.postgres.database.azure.com} port=5432 dbname={your_database} user={adminusername} password={your_database_password} sslmode=require
```

この接続文字列を GitHub シークレットとして使用します。

## <a name="configure-the-github-secrets"></a>GitHub シークレットを構成する

1. [GitHub](https://github.com/) でリポジトリを参照します。

1. **[Settings]\(設定\) > [Secrets]\(シークレット\) > [New secret]\(新しいシークレット\)** の順に選択します。

1. Azure CLI コマンドからの JSON 出力全体をシークレットの値フィールドに貼り付けます。 シークレットに `AZURE_CREDENTIALS` と名前を付けます。

    後でワークフロー ファイルを構成する場合は、Azure ログイン アクションの入力 `creds` にシークレットを使用します。 次に例を示します。

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
   ```

1. もう一度、 **[New secret]\(新しいシークレット\)** を選択します。

1. 接続文字列の値をシークレットの値フィールドに貼り付けます。 シークレットに `AZURE_POSTGRESQL_CONNECTION_STRING` と名前を付けます。


## <a name="add-your-workflow"></a>ワークフローを追加する

1. GitHub リポジトリの **[Actions]\(アクション\)** にアクセスします。

2. **[Set up a workflow yourself]\(ワークフローを自分でセットアップする\)** を選択します。

2. ワークフロー ファイルの `on:` セクションの後にあるすべてのものを削除します。 たとえば、残りのワークフローは次のようになります。

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. ワークフロー `PostgreSQL for GitHub Actions` の名前を変更し、チェックアウトとログインのアクションを追加します。 これらのアクションは、サイト コードをチェックアウトし、先ほど作成した `AZURE_CREDENTIALS` GitHub シークレットを使用して Azure で認証を行います。

    ```yaml
    name: PostgreSQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

2. Azure PostgreSQL のデプロイ アクションを使用して、PostgreSQL インスタンスに接続します。 `POSTGRESQL_SERVER_NAME` をご自分のサーバーの名前に置き換えます。 リポジトリのルート レベルに `data.sql` という名前の PostgreSQL データ ファイルを配置する必要があります。

    ```yaml
     - uses: azure/postgresql@v1
       with:
        connection-string: ${{ secrets.AZURE_POSTGRESQL_CONNECTION_STRING }}
        server-name: POSTGRESQL_SERVER_NAME
        sql-file: './data.sql'
    ```

3. Azure のログアウトにアクションを追加して、ワークフローを完成させます。 完成したワークフローを次に示します。 このファイルは、リポジトリの `.github/workflows` フォルダー内に表示されます。

    ```yaml
   name: PostgreSQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]


    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

    - uses: azure/postgresql@v1
      with:
        server-name: POSTGRESQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_POSTGRESQL_CONNECTION_STRING }}
        sql-file: './data.sql'

        # Azure logout
    - name: logout
      run: |
         az logout
    ```

## <a name="review-your-deployment"></a>デプロイを確認する

1. GitHub リポジトリの **[Actions]\(アクション\)** にアクセスします。

1. 最初の結果を開くと、ワークフローの実行の詳細なログが表示されます。

    :::image type="content" source="media/how-to-deploy-github-action/gitbub-action-postgres-success.png" alt-text="GitHub アクション実行のログ":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure PostgreSQL データベースとリポジトリが不要になったら、リソース グループと GitHub リポジトリを削除して、デプロイしたリソースをクリーンアップします。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure と GitHub の統合に関する詳細](/azure/developer/github/)
<br/>
> [!div class="nextstepaction"]
> [サーバーに接続する方法の詳細](how-to-connect-query-guide.md)
