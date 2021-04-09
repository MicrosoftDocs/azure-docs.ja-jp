---
title: サーバーを管理する - Azure CLI - Azure Database for PostgreSQL - フレキシブル サーバー
description: Azure CLI から Azure Database for PostgreSQL - フレキシブル サーバーを管理する方法について説明します。
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 278f8f816909a7e365d7e45d04c5169950e79a65
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96493680"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-by-using-the-azure-cli"></a>Azure CLI を使用して Azure Database for PostgreSQL - フレキシブル サーバーを管理する

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーはプレビュー段階です。

この記事では、Azure にデプロイされたフレキシブル サーバーを管理する方法を示します。 管理タスクには、コンピューティングとストレージのスケーリング、管理者パスワードのリセット、サーバーの詳細の表示が含まれます。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。 

Azure CLI バージョン 2.0 以降をローカルで実行する必要があります。 インストールされているバージョンを確認するには、`az --version` コマンドを実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

[az login](/cli/azure/reference-index#az-login) コマンドを使用して、お使いのアカウントにサインインします。 

```azurecli-interactive
az login
```

[az account list](/cli/azure/account) コマンドを使用して、お使いのサブスクリプションを選択します。 **az login** の出力の **id** 値を書き留めておき、次のコマンドで **subscription** 引数の値として使用します。 複数のサブスクリプションがある場合は、リソースが課金対象になるサブスクリプションを選択してください。 すべてのサブスクリプションを識別するには、[az account list](/cli/azure/account#az-account-list) コマンドを使用します。

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> フレキシブル サーバーをまだ作成していない場合は、この攻略ガイドに従うために、それを行う必要があります。

## <a name="scale-compute-and-storage"></a>コンピューティングとストレージのスケーリング

次のコマンドを使用して、コンピューティング レベル、仮想コア、ストレージを簡単にスケールアップできます。 実行可能なすべてのサーバー操作の一覧については、[az postgres flexible-server](/cli/azure/postgres/flexible-server) の概要に関するページを参照してください。

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v3 --storage-size 6144
```

以下に、上記のコードの引数の詳細を示します。

**設定** | **値の例** | **説明**
---|---|---
name | mydemoserver | サーバーの一意の名前を入力します。 サーバー名に含めることができるのは、英小文字、数字、およびハイフン (-) のみであり、 3 から 63 文字にする必要があります。
resource-group | myresourcegroup | Azure リソース グループの名前を指定します。
sku-name|Standard_D4ds_v3|コンピューティング レベルの名前とサイズを入力します。 値は、省略表現の *Standard_{VM サイズ}* という規則に従います。 詳細については、[価格レベル](../concepts-pricing-tiers.md)に関するページを参照してください。
storage-size | 6144 | サーバーのストレージ容量をメガバイト単位で入力します。 最小は 5120 で、1024 単位で増やします。

> [!IMPORTANT]
> ストレージをスケールダウンすることはできません。 

## <a name="manage-postgresql-databases-on-a-server"></a>サーバー上の PostgreSQL データベースを管理する

Azure Database for PostgreSQL サーバーに接続するために使用できる、多くのアプリケーションがあります。 クライアント コンピューターに PostgreSQL がインストールされている場合は、[psql](https://www.postgresql.org/docs/current/static/app-psql.html) のローカル インスタンスを使用できます。 ここでは psql コマンド ライン ツールを使用して、Azure Database for PostgreSQL サーバーに接続しましょう。

1. 次の **psql** コマンドを実行します。

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   たとえば次のコマンドでは、お使いのアクセス資格情報を通して、PostgreSQL サーバー **mydemoserver.postgres.database.azure.com** にある **postgres** という名前の既定のデータベースに接続します。 プロンプトが表示されたら、選択した `<server_admin_password>` を入力します。
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   接続されると、psql ツールによって、SQL コマンドを入力できる **postgres** プロンプトが表示されます。 使用している psql のバージョンが Azure Database for PostgreSQL サーバーでのバージョンと異なっている場合、初回接続時の出力に警告が表示されます。

   psql の出力例: 

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>
   ```

   > [!TIP]
   > クライアントの IP アドレスを許可するようにファイアウォールが構成されていない場合、次のエラーが発生します。
   >
   > "psql: FATAL:  no pg_hba.conf entry for host `<IP address>`, user "myadmin", database "postgres", SSL on FATAL: SSL connection is required. ("psql: 致命的:  ホスト <IP アドレス>、ユーザー "myadmin"、データベース "postgres" のエントリが pg_hba.conf にありません。SSL オン 致命的: SSL 接続が必要です。) SSL のオプションを指定して再試行してください。"
   >
   > ファイアウォール規則で、クライアントの IP アドレスが許可されていることを確認してください。

2. プロンプトで次のコマンドを入力して、**postgresdb** という名前の空のデータベースを作成します。

    ```bash
    CREATE DATABASE postgresdb;
    ```

3. プロンプトで次のコマンドを実行して、新しく作成したデータベース **postgresdb** に接続を切り替えます。

    ```bash
    \c postgresdb
    ```

4. 「`\q`」と入力し、Enter を選択して psql を終了します。

このセクションでは、psql によって、Azure Database for PostgreSQL サーバーに接続し、空のユーザー データベースを作成しました。

## <a name="reset-the-admin-password"></a>管理者パスワードをリセットする

次のコマンドを使用して、管理者の役割のパスワードを変更できます

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!IMPORTANT]
> 8 文字以上 128 文字以下のパスワードを選択してください。 パスワードには、次の 3 種類の文字を含める必要があります。 
> - 英大文字
> - 英小文字
> - 数値
> - 英数字以外の文字

## <a name="delete-a-server"></a>サーバーの削除

Azure Database for PostgreSQL フレキシブル サーバーを削除するには、[az postgres flexible-server delete](/cli/azure/postgres/flexible-server#az-PostgreSQL-flexible-server-delete) コマンドを実行します。

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>次のステップ

- [バックアップと復元の概念を確認する](concepts-backup-restore.md)
- [サーバーのチューニングと監視を行う](concepts-monitoring.md)