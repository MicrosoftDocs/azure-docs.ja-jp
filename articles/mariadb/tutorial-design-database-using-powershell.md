---
title: チュートリアル:サーバーを設計する - Azure PowerShell - Azure Database for MariaDB
description: このチュートリアルでは、PowerShell を使用して、Azure Database for MariaDB サーバーとデータベースを作成および管理する方法について説明します。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurepowershell
ms.topic: tutorial
ms.date: 05/26/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 9b4500df459e4d4ef67f97dc4fa923988f30401b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664503"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-using-powershell"></a>チュートリアル:PowerShell を使用して Azure Database for MariaDB を設計する

Azure Database for MariaDB は、Microsoft クラウドにおける、MariaDB コミュニティ エディションのデータベース エンジンを基にしたリレーショナル データベース サービスです。 このチュートリアルでは、PowerShell とその他のユーティリティを使用して、次のことを行う方法を説明します。

> [!div class="checklist"]
> - Azure Database for MariaDB を作成する
> - サーバーのファイアウォールの構成
> - [mysql コマンドライン ツール](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)を使用したデータベースの作成
> - サンプル データを読み込む
> - クエリ データ
> - データの更新
> - データの復元

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

ローカルで PowerShell を使用する場合は、Az PowerShell モジュールをインストールしたうえで、[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドレットを使用して自分の Azure アカウントに接続する必要があります。 Az PowerShell モジュールのインストールの詳細については、「[Azure PowerShell のインストール](/powershell/azure/install-az-ps)」を参照してください。

> [!IMPORTANT]
> Az.MariaDb PowerShell モジュールがプレビュー段階にある間は、次のコマンドを使用して、Az PowerShell モジュールとは別にこれをインストールする必要があります: `Install-Module -Name Az.MariaDb -AllowPrerelease`。
> Az.MariaDb PowerShell モジュールは、一般提供された段階で将来の Az PowerShell モジュール リリースの一部となり、Azure Cloud Shell 内からネイティブに使用できるようになります。

Azure Database for MariaDB サービスを初めて使用する場合は、**Microsoft.DBforMariaDB** リソースプロバイダーを登録する必要があります。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforMariaDB
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

## <a name="create-an-azure-database-for-mariadb-server"></a>Azure Database for MariaDB サーバーを作成する

`New-AzMariaDbServer` コマンドレットを使用して Azure Database for MariaDB サーバーを作成します。 1 つのサーバーで複数のデータベースを管理できます。 通常は、プロジェクトまたはユーザーごとに個別のデータベースを使用します。

次の例では、**米国西部** リージョンの **myresourcegroup** リソース グループに **mydemoserver** という名前の MariaDB サーバーを作成しています。サーバー管理者ログインは **myadmin** です。 これは、2 つの仮想コアを備え geo 冗長バックアップが有効になっている、汎用価格レベルの Gen 5 サーバーです。 例の最初の行に使用されているパスワードは MariaDB サーバー管理者アカウントのパスワードであるため、これを記録しておきます。

> [!TIP]
> サーバー名は DNS 名に対応しており、Azure 内でグローバルに一意であることが必要です。

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

**Sku** パラメーターの値は、次の例のように、 **<価格レベル>\_<コンピューティング世代>\_<仮想コア数>** という規則に従います。

- `-Sku B_Gen5_1` は、"Basic、Gen 5、および 1 個の仮想コア" にマップされます。 このオプションは、利用できる最小の SKU です。
- `-Sku GP_Gen5_32` は、"汎用、Gen 5、および 32 個の仮想コア" にマップされます。
- `-Sku MO_Gen5_2` は、"メモリ最適化、Gen 5、および 2 個の仮想コア" にマップされます。

リージョンおよびレベルごとの有効な **Sku** 値については、「[Azure Database for MariaDB の価格レベル](./concepts-pricing-tiers.md)」を参照してください。

低負荷なコンピューティングと I/O がワークロードに適している場合は、Basic 価格レベルの使用を検討してください。

> [!IMPORTANT]
> Basic 価格レベルで作成されたサーバーは、後で汎用またはメモリ最適化にスケーリングできず、geo レプリケーションすることもできません。

## <a name="configure-a-firewall-rule"></a>ファイアウォール規則を構成する

`New-AzMariaDbFirewallRule` コマンドレットを使用して、Azure Database for MariaDB サーバーレベルのファイアウォール規則を作成します。 サーバーレベルのファイアウォール規則により、`mysql` コマンドライン ツールや MariaDB Workbench などの外部アプリケーションが、Azure Database for MariaDB サービス ファイアウォールを経由してサーバーに接続できるようになります。

次の例では、特定の IP アドレス 192.168.0.1 からの接続を許可する、**AllowMyIP** と呼ばれるファイアウォール規則を作成しています。 実際の接続元の場所に対応する IP アドレスまたは IP アドレスの範囲に置き換えてください。

```azurepowershell-interactive
New-AzMariaDbFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> Azure Database for MariaDB との接続では、ポート 3306 が通信に使用されます。 企業ネットワーク内から接続を試みる場合、ポート 3306 での送信トラフィックが許可されていない場合があります。 このシナリオでは、IT 部門によってポート 3306 が開かれている場合にのみ、サーバーに接続できます。

## <a name="get-the-connection-information"></a>接続情報の取得

サーバーに接続するには、ホスト情報とアクセス資格情報を提供する必要があります。 接続情報を確認するには、次の例を使用します。 **FullyQualifiedDomainName** と **AdministratorLogin** の値を書き留めておきます。

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.mariadb.database.azure.com       myadmin
```

## <a name="connect-to-the-server-using-the-mysql-command-line-tool"></a>mysql コマンドライン ツールを使用してサーバーに接続する

`mysql` コマンドライン ツールを使用してサーバーに接続します。 コマンドライン ツールをダウンロードしてインストールするには、「[MariaDB コミュニティ ダウンロード](https://dev.mysql.com/downloads/shell/)」を参照してください。 また、この記事のコード サンプルにある **[使ってみる]** ボタンを選択することにより、Azure Cloud Shell の `mysql` コマンドライン ツールのプレインストール バージョンにアクセスすることもできます。 Azure Cloud Shell にアクセスするその他の方法として、Azure portal の右上のツール バーにある **[>_]** を選択するか、[shell.azure.com](https://shell.azure.com/) にアクセスします。

```azurepowershell-interactive
mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="create-a-database"></a>データベースを作成する

サーバーに接続したら、空のデータベースを作成します。

```sql
mysql> CREATE DATABASE mysampledb;
```

プロンプトで次のコマンドを実行し、新しく作成したデータベースに接続を切り替えます。

```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>データベースのテーブルを作成する

Azure Database for MariaDB データベースに接続する方法を説明したので、次はいくつかの基本的なタスクを実行します。

最初に、テーブルを作成してデータを読み込みます。 インベントリ情報を格納するテーブルを作成しましょう。

```sql
CREATE TABLE inventory (
  id serial PRIMARY KEY,
  name VARCHAR(50),
  quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>テーブルにデータを読み込む

テーブルを作成したので、次はデータを挿入します。 開いているコマンド プロンプト ウィンドウで、次のクエリを実行してデータ行を挿入します。

```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150);
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

これで、先ほど作成したテーブルにサンプル データが 2 行挿入されました。

## <a name="query-and-update-the-data-in-the-tables"></a>クエリを実行し、テーブル内のデータを更新する

次のクエリを実行して、データベース テーブルから情報を取得します。

```sql
SELECT * FROM inventory;
```

さらに、テーブル内のデータを更新することもできます。

```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

データを取得するとき、それに応じてデータ行が更新されます。

```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>データベースを以前の状態に復元する

サーバーを以前の状態に復元できます。 復元されたデータは新しいサーバーにコピーされ、既存のサーバーは変更されません。 たとえば、テーブルが誤って削除された場合、削除の発生時点に復元できます。 その後、不足しているテーブルとデータを、サーバーの復元されたコピーから取得できます。

サーバーを復元するには、`Restore-AzMariaDbServer` PowerShell コマンドレットを使用します。

### <a name="run-the-restore-command"></a>復元コマンドを実行する

サーバーを復元するには、PowerShell から次の例を実行します。

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMariaDbServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

サーバーを過去の特定の時点に復元すると、新しいサーバーが作成されます。 特定の時点における元のサーバーとそのデータベースが新しいサーバーにコピーされます。

復元されたサーバーの場所と価格レベルの値は、元のサーバーと同じです。

復元プロセスが完了したら、新しいサーバーを検索して、想定どおりにデータが復元できたかどうかを確認します。 新しいサーバーには、復元が開始された時点の既存のサーバーで有効であったサーバー管理者のログイン名とパスワードが設定されています。 このパスワードは、新しいサーバーの **[概要]** ページで変更できます。

復元中に作成される新しいサーバーには、元のサーバーに存在した VNet サービス エンドポイントはありません。 新しいサーバー用に、これらの規則を個別に設定する必要があります。 元のサーバーのファイアウォール規則は復元されます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [PowerShell を使用して Azure Database for MariaDB サーバーをバックアップおよび復元する方法](howto-restore-server-powershell.md)