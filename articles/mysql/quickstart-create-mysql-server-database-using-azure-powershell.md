---
title: クイック スタート:サーバーを作成する - Azure PowerShell - Azure Database for MySQL
description: このクイックスタートでは、PowerShell を使用して、Azure Database for MySQL サーバーを Azure リソース グループに作成する方法を説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurepowershell
ms.topic: quickstart
ms.date: 04/28/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: d12d447acb3b6bf2b6f84e9768e9f063a9a36b03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94542305"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-using-powershell"></a>クイック スタート:PowerShell を使用して Azure Database for MySQL サーバーを作成する

このクイックスタートでは、PowerShell を使用して、Azure Database for MySQL サーバーを Azure リソース グループに作成する方法を説明します。 PowerShell を使用して、対話形式またはスクリプトで Azure リソースを作成および管理できます。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

ローカルで PowerShell を使用する場合は、Az PowerShell モジュールをインストールしたうえで、[Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) コマンドレットを使用して自分の Azure アカウントに接続する必要があります。 Az PowerShell モジュールのインストールの詳細については、「[Azure PowerShell のインストール](/powershell/azure/install-az-ps)」を参照してください。

> [!IMPORTANT]
> Az.MySql PowerShell モジュールがプレビュー段階にある間は、次のコマンドを使用して、Az PowerShell モジュールとは別にこれをインストールする必要があります: `Install-Module -Name Az.MySql -AllowPrerelease`。
> Az.MySql PowerShell モジュールは、一般提供された後、将来の Az PowerShell モジュール リリースの一部となり、Azure Cloud Shell 内からネイティブに使用できるようになります。

Azure Database for MySQL サービスを初めて使用する場合は、**Microsoft.DBforMySQL** リソースプロバイダーを登録する必要があります。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforMySQL
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

## <a name="create-an-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーの作成

`New-AzMySqlServer` コマンドレットを使用して、Azure Database for MySQL サーバーを作成します。 1 つのサーバーで複数のデータベースを管理できます。 通常は、プロジェクトまたはユーザーごとに個別のデータベースを使用します。

次の表に、`New-AzMySqlServer` コマンドレットで一般的に使用されるパラメーターとサンプル値の一覧を示します。

|        **設定**         | **値の例** |                                                                                                                                                             **説明**                                                                                                                                                              |
| -------------------------- | ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 名前                       | mydemoserver     | お使いの Azure Database for MySQL サーバーを識別する、Azure でグローバルに一意の名前を選択します。 サーバー名に含めることができるのは、文字、数字、およびハイフン (-) 文字のみです。 大文字が指定された場合、それらの文字は作成プロセス中に自動的に小文字に変換されます。 3 ～ 63 文字にする必要があります。 |
| ResourceGroupName          | myresourcegroup  | Azure リソース グループの名前を指定します。                                                                                                                                                                                                                                                                                            |
| Sku                        | GP_Gen5_2        | SKU の名前。 省略表現の **<価格レベル>\_<コンピューティング世代>\_<仮想コア数>** という規則に従います。 Sku パラメーターの詳細については、この表の後続の情報を参照してください。                                                                                                                                           |
| BackupRetentionDay         | 7                | バックアップを保持する必要のある時間。 単位は日数です。 範囲は 7 ～ 35 です。                                                                                                                                                                                                                                                                       |
| GeoRedundantBackup         | Enabled          | このサーバーに対して geo 冗長バックアップを有効にする必要があるかどうかどうか。 この値は、Basic 価格レベルのサーバーに対して有効にすることはできません。また、サーバーの作成後に変更することもできません。 使用できる値は以下の通りです。Enabled、Disabled                                                                                                      |
| 場所                   | westus           | サーバーの Azure リージョン。                                                                                                                                                                                                                                                                                                         |
| SslEnforcement             | Enabled          | このサーバーに対して SSL を有効にする必要があるかどうかどうか。 使用できる値は以下の通りです。Enabled、Disabled                                                                                                                                                                                                                                                 |
| StorageInMb                | 51200            | サーバーのストレージ容量 (単位はメガバイト)。 有効な StorageInMb は 5,120 MB 以上で、増分単位は 1,024 MB です。 ストレージ サイズの制限の詳細については、「[Azure Database for MySQL の価格レベル](./concepts-pricing-tiers.md)」を参照してください。                                                                               |
| Version                    | 5.7              | MySQL のメジャー バージョン。                                                                                                                                                                                                                                                                                                                 |
| AdministratorUserName      | myadmin          | 管理者ログインのユーザー名。 これを **azure_superuser**、**admin**、**administrator**、**root**、**guest**、**public** にすることはできません。                                                                                                                                                                                            |
| AdministratorLoginPassword | `<securestring>` | セキュリティで保護された文字列形式の管理者ユーザーのパスワード。 8 ～ 128 文字にする必要があります。 パスワードには、英大文字、英小文字、数字、英数字以外の文字のうち、3 つのカテゴリの文字が含まれている必要があります。                                       |

**Sku** パラメーターの値は、次の例のように、 **<価格レベル>\_<コンピューティング世代>\_<仮想コア数>** という規則に従います。

- `-Sku B_Gen5_1` は、"Basic、Gen 5、および 1 個の仮想コア" にマップされます。 このオプションは、利用できる最小の SKU です。
- `-Sku GP_Gen5_32` は、"汎用、Gen 5、および 32 個の仮想コア" にマップされます。
- `-Sku MO_Gen5_2` は、"メモリ最適化、Gen 5、および 2 個の仮想コア" にマップされます。

リージョンおよびレベルごとの有効な **Sku** 値については、「[Azure Database for MySQL の価格レベル](./concepts-pricing-tiers.md)」を参照してください。

次の例では、**米国西部** リージョンの **myresourcegroup** リソース グループに **mydemoserver** という名前の MySQL サーバーを作成しています。サーバー管理者ログインは **myadmin** です。 これは、2 つの仮想コアを備え geo 冗長バックアップが有効になっている、汎用価格レベルの Gen 5 サーバーです。 例の最初の行に使用されているパスワードは MySQL server 管理者アカウントのパスワードであるため、これを記録しておきます。

> [!TIP]
> サーバー名は DNS 名に対応しており、Azure 内でグローバルに一意であることが必要です。

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

低負荷なコンピューティングと I/O がワークロードに適している場合は、Basic 価格レベルの使用を検討してください。

> [!IMPORTANT]
> Basic 価格レベルで作成されたサーバーは、後で汎用またはメモリ最適化にスケーリングできず、geo レプリケーションすることもできません。

## <a name="configure-a-firewall-rule"></a>ファイアウォール規則を構成する

`New-AzMySqlFirewallRule` コマンドレットを使用して、Azure Database for MySQL サーバーレベルのファイアウォール規則を作成します。 サーバーレベルのファイアウォール規則により、`mysql` コマンドライン ツールや MySQL Workbench などの外部アプリケーションが、Azure Database for MySQL サービス ファイアウォールを経由してサーバーに接続できるようになります。

次の例では、特定の IP アドレス 192.168.0.1 からの接続を許可する、**AllowMyIP** と呼ばれるファイアウォール規則を作成しています。 実際の接続元の場所に対応する IP アドレスまたは IP アドレスの範囲に置き換えてください。

```azurepowershell-interactive
New-AzMySqlFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> Azure Database for MySQL との接続では、ポート 3306 が通信に使用されます。 企業ネットワーク内から接続を試みる場合、ポート 3306 での送信トラフィックが許可されていない場合があります。 このシナリオでは、IT 部門によってポート 3306 が開かれている場合にのみ、サーバーに接続できます。

## <a name="configure-ssl-settings"></a>SSL 設定の構成

既定では、サーバーとクライアント アプリケーション間で SSL 接続が適用されます。 この既定値では、インターネット上のデータ ストリームを暗号化することによって、"_インモーション_" データのセキュリティが確保されます。 このクイック スタートでは、サーバーの SSL 接続を無効にします。 詳細については、「[Azure Database for MySQL に安全に接続するためにアプリケーションで SSL 接続を構成する](./howto-configure-ssl.md)」を参照してください。

> [!WARNING]
> 実稼働サーバーで SSL を無効にすることはお勧めしません。

次の例では、Azure Database for MySQL サーバー上で SSL を無効にしています。

```azurepowershell-interactive
Update-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -SslEnforcement Disabled
```

## <a name="get-the-connection-information"></a>接続情報の取得

サーバーに接続するには、ホスト情報とアクセス資格情報を提供する必要があります。 接続情報を確認するには、次の例を使用します。 **FullyQualifiedDomainName** と **AdministratorLogin** の値を書き留めておきます。

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.mysql.database.azure.com       myadmin
```

## <a name="connect-to-the-server-using-the-mysql-command-line-tool"></a>mysql コマンドライン ツールを使用してサーバーに接続する

`mysql` コマンドライン ツールを使用してサーバーに接続します。 コマンドライン ツールをダウンロードしてインストールするには、「[MySQL コミュニティ ダウンロード](https://dev.mysql.com/downloads/shell/)」を参照してください。 また、この記事のコード サンプルにある **[使ってみる]** ボタンを選択することにより、Azure Cloud Shell の `mysql` コマンドライン ツールのプレインストール バージョンにアクセスすることもできます。 Azure Cloud Shell にアクセスするその他の方法として、Azure portal の右上のツール バーにある **[>_]** を選択するか、[shell.azure.com](https://shell.azure.com/) にアクセスします。

1. `mysql` コマンドライン ツールを使用してサーバーに接続します。

   ```azurepowershell-interactive
   mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
   ```

1. サーバーの状態を表示します。

   ```sql
   mysql> status
   ```

    ```Output
    C:\Users\>mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
    Enter password: *************
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65512
    Server version: 5.6.42.0 MySQL Community Server (GPL)

    Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

    mysql> status
    --------------
    mysql  Ver 14.14 Distrib 5.7.29, for Win64 (x86_64)

    Connection id:          65512
    Current database:
    Current user:           myadmin@myipaddress
    SSL:                    Not in use
    Using delimiter:        ;
    Server version:         5.6.42.0 MySQL Community Server (GPL)
    Protocol version:       10
    Connection:             mydemoserver.mysql.database.azure.com via TCP/IP
    Server characterset:    latin1
    Db     characterset:    latin1
    Client characterset:    utf8
    Conn.  characterset:    utf8
    TCP port:               3306
    Uptime:                 1 hour 2 min 12 sec

    Threads: 7  Questions: 952  Slow queries: 0  Opens: 66  Flush tables: 3  Open tables: 16  Queries per second avg: 0.255
    --------------

    mysql>
    ```

その他のコマンドについては、「[MySQL 5.7 リファレンス マニュアル - 4.5.1 章](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)」を参照してください。

## <a name="connect-to-the-server-using-mysql-workbench"></a>MySQL Workbench を使用したサーバーへの接続

1. クライアント コンピューターで MySQL Workbench アプリケーションを起動します。 MySQL Workbench をダウンロードしてインストールするには、[MySQL Workbench のダウンロード](https://dev.mysql.com/downloads/workbench/) ページを参照してください。

1. **[Setup New Connection]\(新しい接続のセットアップ\)** ダイアログ ボックスで、次の情報を **[Parameters]\(パラメーター\)** タブに入力します。

   :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-powershell/setup-new-connection.png" alt-text="新しい接続のセットアップ":::

    |    **設定**    |           **推奨値**           |                      **説明**                       |
    | ----------------- | --------------------------------------- | ---------------------------------------------------------- |
    | 接続名   | My Connection                           | この接続のラベルを指定します                        |
    | 接続方法 | Standard (TCP/IP)                       | TCP/IP プロトコルを使用して Azure Database for MySQL に接続します |
    | hostname          | `mydemoserver.mysql.database.azure.com` | 前にメモしておいたサーバー名です                           |
    | Port              | 3306                                    | MySQL の既定のポートです                                 |
    | ユーザー名          | myadmin@mydemoserver                    | 前にメモしておいたサーバーの管理者ログインです                |
    | Password          | *************                           | 前に構成した管理者アカウントのパスワードを使用します      |

1. パラメーターが正しく構成されているかどうかをテストするには、 **[Test Connection]\(接続のテスト\)** をクリックします。

1. サーバーに接続するための接続を選択します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートで作成したリソースが別のクイックスタートまたはチュートリアルで必要でない場合は、次の例を実行して削除できます。

> [!CAUTION]
> 次の例では、指定されたリソース グループとそれに含まれるすべてのリソースを削除します。
> 指定したリソース グループにこのクイックスタートの範囲外のリソースが含まれている場合、それらも削除されます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

リソース グループを削除せずに、このクイックスタートで作成したサーバーのみを削除するには、`Remove-AzMySqlServer` コマンドレットを使用します。

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [PowerShell を使用して Azure Database for MySQL を設計する](tutorial-design-database-using-powershell.md)