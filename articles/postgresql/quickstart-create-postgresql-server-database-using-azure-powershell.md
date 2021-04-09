---
title: クイック スタート:サーバーを作成する - Azure PowerShell - Azure Database for PostgreSQL - Single Server
description: Azure PowerShell を使用して Azure Database for PostgreSQL - Single Server を作成するためのクイックスタート ガイド。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: quickstart
ms.date: 06/08/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 91351c0b2982c6ee0e96cc1433c0fadf67e3bcc0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96010658"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---single-server-using-powershell"></a>クイック スタート:PowerShell を使用して Azure Database for PostgreSQL - 単一サーバーを作成する

このクイックスタートでは、PowerShell を使用して、Azure Database for PostgreSQL サーバーを Azure リソース グループに作成する方法を説明します。 PowerShell を使用して、対話形式またはスクリプトで Azure リソースを作成および管理できます。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

ローカルで PowerShell を使用する場合は、Az PowerShell モジュールをインストールしたうえで、[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドレットを使用して自分の Azure アカウントに接続する必要があります。 Az PowerShell モジュールのインストールの詳細については、「[Azure PowerShell のインストール](/powershell/azure/install-az-ps)」を参照してください。

> [!IMPORTANT]
> Az.PostgreSql PowerShell モジュールがプレビュー段階にある間は、コマンド `Install-Module -Name Az.PostgreSql -AllowPrerelease` を使用して、Az PowerShell モジュールとは別にこれをインストールする必要があります。
> Az.PostgreSql PowerShell モジュールは、一般提供された段階で将来の Az PowerShell モジュール リリースの一部となり、Azure Cloud Shell 内からネイティブに使用できるようになります。

Azure Database for PostgreSQL サービスを初めて使用する場合は、**Microsoft.DBforPostgreSQL** リソース プロバイダーを登録する必要があります。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforPostgreSQL
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

複数の Azure サブスクリプションをお持ちの場合は、リソースが課金の対象となる適切なサブスクリプションを選択してください。 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) コマンドレットを使用して、特定のサブスクリプション ID を選択します。

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>リソース グループを作成する

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドレットを使用して、[Azure リソース グループ](../azure-resource-manager/management/overview.md)を作成します。 リソース グループとは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。

次の例では、**myresourcegroup** という名前のリソース グループを **米国西部** リージョンに作成します。

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバーの作成

`New-AzPostgreSqlServer` コマンドレットを使用して、Azure Database for PostgreSQL サーバーを作成します。 1 つのサーバーで複数のデータベースを管理できます。 通常は、プロジェクトまたはユーザーごとに個別のデータベースを使用します。

次の表に、`New-AzPostgreSqlServer` コマンドレットで一般的に使用されるパラメーターとサンプル値の一覧を示します。

|        **設定**         | **値の例** |                                                                                                                                                             **説明**                                                                                                                                                              |
| -------------------------- | ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 名前                       | mydemoserver     | お使いの Azure Database for PostgreSQL サーバーを識別する、Azure でグローバルに一意の名前を選択します。 サーバー名に含めることができるのは、文字、数字、およびハイフン (-) 文字のみです。 大文字が指定された場合、それらの文字は作成プロセス中に自動的に小文字に変換されます。 3 ～ 63 文字にする必要があります。 |
| ResourceGroupName          | myresourcegroup  | Azure リソース グループの名前を指定します。                                                                                                                                                                                                                                                                                            |
| Sku                        | GP_Gen5_2        | SKU の名前。 省略表現の **<価格レベル>\_<コンピューティング世代>\_<仮想コア数>** という規則に従います。 Sku パラメーターの詳細については、この表の後続の情報を参照してください。                                                                                                                                           |
| BackupRetentionDay         | 7                | バックアップを保持する必要のある時間。 単位は日数です。 範囲は 7 ～ 35 です。                                                                                                                                                                                                                                                                       |
| GeoRedundantBackup         | Enabled          | このサーバーに対して geo 冗長バックアップを有効にする必要があるかどうかどうか。 この値は、Basic 価格レベルのサーバーに対して有効にすることはできません。また、サーバーの作成後に変更することもできません。 使用できる値は以下の通りです。Enabled、Disabled                                                                                                      |
| 場所                   | westus           | サーバーの Azure リージョン。                                                                                                                                                                                                                                                                                                         |
| SslEnforcement             | Enabled          | このサーバーに対して SSL を有効にする必要があるかどうかどうか。 使用できる値は以下の通りです。Enabled、Disabled                                                                                                                                                                                                                                                 |
| StorageInMb                | 51200            | サーバーのストレージ容量 (単位はメガバイト)。 有効な StorageInMb は 5,120 MB 以上で、増分単位は 1,024 MB です。 ストレージ サイズの制限の詳細については、[Azure Database for PostgreSQL の価格レベル](./concepts-pricing-tiers.md)に関するページを参照してください。                                                                               |
| Version                    | 9.6              | PostgreSQL のメジャー バージョン。                                                                                                                                                                                                                                                                                                                 |
| AdministratorUserName      | myadmin          | 管理者ログインのユーザー名。 これを **azure_superuser**、**admin**、**administrator**、**root**、**guest**、**public** にすることはできません。                                                                                                                                                                                            |
| AdministratorLoginPassword | `<securestring>` | セキュリティで保護された文字列形式の管理者ユーザーのパスワード。 8 ～ 128 文字にする必要があります。 パスワードには、英大文字、英小文字、数字、英数字以外の文字のうち、3 つのカテゴリの文字が含まれている必要があります。                                       |

**Sku** パラメーターの値は、次の例のように、 **<価格レベル>\_<コンピューティング世代>\_<仮想コア数>** という規則に従います。

- `-Sku B_Gen5_1` は、"Basic、Gen 5、および 1 個の仮想コア" にマップされます。 このオプションは、利用できる最小の SKU です。
- `-Sku GP_Gen5_32` は、"汎用、Gen 5、および 32 個の仮想コア" にマップされます。
- `-Sku MO_Gen5_2` は、"メモリ最適化、Gen 5、および 2 個の仮想コア" にマップされます。

リージョンおよびレベルごとの有効な **Sku** 値については、[Azure Database for PostgreSQL の価格レベル](./concepts-pricing-tiers.md)に関する記事を参照してください。

次の例では、**米国西部** リージョンの **myresourcegroup** リソース グループに **mydemoserver** という名前の PostgreSQL サーバーを作成しています。サーバー管理者ログインは **myadmin** です。 これは、2 つの仮想コアを備え geo 冗長バックアップが有効になっている、汎用価格レベルの Gen 5 サーバーです。 例の最初の行に使用されているパスワードは PostgreSQL サーバー管理者アカウントのパスワードであるため、これを記録しておきます。

> [!TIP]
> サーバー名は DNS 名に対応しており、Azure 内でグローバルに一意であることが必要です。

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

低負荷なコンピューティングと I/O がワークロードに適している場合は、Basic 価格レベルの使用を検討してください。

> [!IMPORTANT]
> Basic 価格レベルで作成されたサーバーは、後で汎用またはメモリ最適化にスケーリングできず、geo レプリケーションすることもできません。

## <a name="configure-a-firewall-rule"></a>ファイアウォール規則を構成する

`New-AzPostgreSqlFirewallRule` コマンドレットを使用して、Azure Database for PostgreSQL サーバーレベルのファイアウォール規則を作成します。 サーバーレベルのファイアウォール規則により、`psql` コマンドライン ツールや PostgreSQL Workbench などの外部アプリケーションが、Azure Database for PostgreSQL サービス ファイアウォールを経由してサーバーに接続できるようになります。

次の例では、特定の IP アドレス 192.168.0.1 からの接続を許可する、**AllowMyIP** と呼ばれるファイアウォール規則を作成しています。 実際の接続元の場所に対応する IP アドレスまたは IP アドレスの範囲に置き換えてください。

```azurepowershell-interactive
New-AzPostgreSqlFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> Azure Database for PostgreSQL との接続では、ポート 5432 が通信に使用されます。 企業ネットワーク内から接続を試みる場合、ポート 5432 でのアウトバウンド トラフィックが許可されていない場合があります。 このシナリオでは、IT 部門によってポート 5432 が開放されている場合にのみ、サーバーに接続できます。

## <a name="get-the-connection-information"></a>接続情報の取得

サーバーに接続するには、ホスト情報とアクセス資格情報を提供する必要があります。 接続情報を確認するには、次の例を使用します。 **FullyQualifiedDomainName** と **AdministratorLogin** の値を書き留めておきます。

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.postgres.database.azure.com       myadmin
```

## <a name="connect-to-postgresql-database-using-psql"></a>psql を使用した PostgreSQL データベースへの接続

クライアント コンピューターに PostgreSQL がインストールされている場合は、[psql](https://www.postgresql.org/docs/current/static/app-psql.html) のローカル インスタンスを使用して Azure PostgreSQL サーバーに接続できます。 また、この記事のコード サンプルにある **[使ってみる]** ボタンを選択することにより、Azure Cloud Shell の `psql` コマンドライン ツールのプレインストール バージョンにアクセスすることもできます。 Azure Cloud Shell にアクセスするその他の方法として、Azure portal の右上のツール バーにある **[>_]** を選択するか、[shell.azure.com](https://shell.azure.com/) にアクセスします。

1. `psql` コマンドライン ユーティリティを使用して、Azure PostgreSQL サーバーに接続します。

   ```azurepowershell-interactive
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   たとえば次のコマンドは、アクセス資格情報を使用して、PostgreSQL サーバー `mydemoserver.postgres.database.azure.com` にある既定のデータベース **postgres** に接続します。 パスワードの入力を求められたら、選択した `<server_admin_password>` を入力します。

   ```azurepowershell-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Postgres への接続に URL パスを使用する場合は、`%40` を使用してユーザー名の @ 記号を URL エンコードします。 たとえば、psql の接続文字列は、`psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres` のようになります。

1. サーバーに接続したら、プロンプトで空のデータベースを作成します。

   ```sql
   CREATE DATABASE mypgsqldb;
   ```

1. プロンプトで次のコマンドを実行し、新しく作成したデータベース **mypgsqldb** に接続を切り替えます。

   ```sql
   \c mypgsqldb
   ```

## <a name="connect-to-the-postgresql-server-using-pgadmin"></a>pgAdmin を使用して PostgreSQL サーバーに接続する

pgAdmin は PostgreSQL で使用されるオープンソース ツールです。 [pgAdmin の Web サイト](https://www.pgadmin.org/)からインストールできます。 使用する pgAdmin のバージョンは、このクイック スタートで使用されているものと異なる可能性があります。 追加のガイダンスが必要な場合は、pgAdmin のドキュメントをご覧ください。

1. クライアント コンピューターで pgAdmin アプリケーションを開きます。

1. ツール バーから **[オブジェクト]** に移動し、 **[作成]** をポイントして、 **[サーバー]** を選択します。

1. **[作成 - サーバー]** ダイアログ ボックスの **[全般]** タブに、サーバーの一意のフレンドリ名 (**mydemoserver** など) を入力します。

   :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-azure-powershell/9-pgadmin-create-server.png" alt-text="[全般] タブ":::

1. **[作成 - サーバー]** ダイアログ ボックスの **[接続]** タブで、設定テーブルに入力します。

   :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-azure-powershell/10-pgadmin-create-server.png" alt-text="[接続] タブ":::

    pgAdmin パラメーター |値|説明
    ---|---|---
    ホスト名/アドレス | サーバー名 | 前の手順で Azure Database for PostgreSQL サーバーを作成したときに使用したサーバー名の値。 例に示したサーバーは、**mydemoserver.postgres.database.azure.com** です。 Use the fully qualified domain name ( **\*.postgres.database.azure.com**) as shown in the example. サーバー名を覚えていない場合は、前のセクションの手順に従って接続情報を取得してください。
    Port | 5432 | Azure Database for PostgreSQL サーバーに接続するときに使用するポート。
    メンテナンス データベース | *postgres* | システムによって生成される既定のデータベース名。
    ユーザー名 | サーバー管理者ログイン名 | 前の手順で Azure Database for PostgreSQL サーバーを作成したときに指定したサーバー管理者ログイン ユーザー名。 ユーザー名を覚えていない場合は、前のセクションの手順に従って接続情報を取得してください。 形式は *username\@servername* です。
    Password | 管理者パスワード | このクイック スタートでサーバーを作成したときに選択したパスワードです。
    Role | 空白 | この時点でロール名を指定する必要はありません。 このフィールドは空白にしてください。
    SSL モード | "*必須*" | pgAdmin の [SSL] タブで、TLS/SSL モードを設定できます。既定では、すべての Azure Database for PostgreSQL サーバーは TLS の適用がオンの状態で作成されます。 TLS の適用をオフにする方法については、「[TLS 適用の構成](./concepts-ssl-connection-security.md#configure-enforcement-of-tls)」を参照してください。

1. **[保存]** を選択します。

1. 左側の **[ブラウザー]** ウィンドウで **[サーバー]** ノードを展開します。 **mydemoserver** など、ご利用のサーバーを選択します。 クリックして接続します。

1. サーバー ノードを展開し、その下の **[Databases]\(データベース\)** を展開します。 一覧には既存の *postgres* データベースと、作成した他のデータベースすべてを含める必要があります。 Azure Database for PostgreSQL では、サーバーごとに複数のデータベースを作成できます。

1. **[データベース]** を右クリックし、 **[作成]** メニューを選択して **[データベース]** を選択します。

1. **[データベース]** フィールドに、任意のデータベース名 (**mypgsqldb2** など) を入力します。

1. データベースの **所有者** をリスト ボックスから選択します。 サーバー管理者ログイン名 (例に示した **my admin** など) を選択します。

   :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-azure-powershell/11-pgadmin-database.png" alt-text="pgAdmin でデータベースを作成する":::

1. **[保存]** を選択して、新しい空のデータベースを作成します。

1. **[ブラウザー]** ウィンドウで、該当するサーバー名のデータベースの一覧で、作成したデータベースを確認できます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートで作成したリソースが別のクイックスタートまたはチュートリアルで必要でない場合は、次の例を実行して削除できます。

> [!CAUTION]
> 次の例では、指定されたリソース グループとそれに含まれるすべてのリソースを削除します。
> 指定したリソース グループにこのクイックスタートの範囲外のリソースが含まれている場合、それらも削除されます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

リソース グループを削除せずに、このクイックスタートで作成したサーバーのみを削除するには、`Remove-AzPostgreSqlServer` コマンドレットを使用します。

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [PowerShell を使用して Azure Database for PostgreSQL を設計する](tutorial-design-database-using-powershell.md)