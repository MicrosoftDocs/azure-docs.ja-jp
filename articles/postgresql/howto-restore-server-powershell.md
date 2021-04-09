---
title: バックアップと復元 - Azure PowerShell - Azure Database for PostgreSQL
description: Azure PowerShell を使用して Azure Database for PostgreSQL サーバーをバックアップおよび復元する方法について説明します。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 63fffb5998b0b6a245db3f1c8fcf16f2d576936e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92489763"
---
# <a name="how-to-back-up-and-restore-an-azure-database-for-postgresql-server-using-powershell"></a>PowerShell を使用して Azure Database for PostgreSQL サーバーをバックアップおよび復元する方法

Azure Database for PostgreSQL サーバーは、復元機能が有効になるように、バックアップが定期的に行われます。 この機能を使用して、新しいサーバー上で、サーバーとそのすべてのデータベースを過去の特定の時点に復元できます。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドを完了するには、次が必要です。

- ローカルにインストールされた [Az PowerShell モジュール](/powershell/azure/install-az-ps)、またはブラウザーの [Azure Cloud Shell](https://shell.azure.com/)
- [Azure Database for PostgreSQL サーバー](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az.PostgreSql PowerShell モジュールがプレビュー段階にある間は、コマンド `Install-Module -Name Az.PostgreSql -AllowPrerelease` を使用して、Az PowerShell モジュールとは別にこれをインストールする必要があります。
> Az.PostgreSql PowerShell モジュールは、一般提供された段階で将来の Az PowerShell モジュール リリースの一部となり、Azure Cloud Shell 内からネイティブに使用できるようになります。

PowerShell をローカルで使用する場合は、[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドレットを使用して Azure アカウントに接続します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-backup-configuration"></a>バックアップ構成の設定

サーバーの作成時に、ローカル冗長バックアップまたは geo 冗長バックアップのどちらでサーバーを構成するかを選択します。

> [!NOTE]
> サーバーの作成後は、冗長の種類 (geo 冗長とローカル冗長) を変更することはできません。

`New-AzPostgreSqlServer` コマンドでサーバーを作成するときに、**GeoRedundantBackup** パラメーターでバックアップの冗長オプションを指定します。 **[Enabled]\(有効\)** を指定すると、geo 冗長バックアップが取得されます。 **[Disabled]\(無効\)** を指定すると、ローカル冗長バックアップが取得されます。

バックアップのリテンション期間は、**BackupRetentionDay** パラメーターで設定します。

サーバー作成時にこれらの値を設定する方法の詳細については、「[PowerShell を使用して Azure Database for PostgreSQL サーバーを作成する](quickstart-create-postgresql-server-database-using-azure-powershell.md)」をご覧ください。

サーバーのバックアップのリテンション期間は、次のようにして変更できます。

```azurepowershell-interactive
Update-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -BackupRetentionDay 10
```

上記の例では、mydemoserver のバックアップ リテンション期間が 10 日に変更されます。

バックアップのリテンション期間によって、現在からどのくらい遡ってポイントインタイム リストアを取得できるかが管理されます。ポイントインタイム リストアは使用可能なバックアップに基づいているためです。 ポイントインタイム リストアについては、次のセクションで詳しく説明します。

## <a name="server-point-in-time-restore"></a>サーバーのポイントインタイム リストア

サーバーを以前の状態に復元できます。 復元されたデータは新しいサーバーにコピーされ、既存のサーバーは変更されません。 たとえば、テーブルが誤って削除された場合、削除の発生時点に復元できます。 その後、不足しているテーブルとデータを、サーバーの復元されたコピーから取得できます。

サーバーを復元するには、`Restore-AzPostgreSqlServer` PowerShell コマンドレットを使用します。

### <a name="run-the-restore-command"></a>復元コマンドを実行する

サーバーを復元するには、PowerShell から次の例を実行します。

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzPostgreSqlServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

`Restore-AzPostgreSqlServer` コマンドレットの **PointInTimeRestore** パラメーター セットには、次のパラメーターが必要です。

| 設定 | 推奨値 | 説明  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  ソース サーバーが存在するリソース グループ。  |
| 名前 | mydemoserver-restored | 復元コマンドで作成される新しいサーバーの名前。 |
| RestorePointInTime | 2020-03-13T13:59:00Z | 復元する特定の時点を選択します。 この日付と時刻は、ソース サーバーのバックアップ保有期間内でなければなりません。 ISO8601 の日時形式を使います。 たとえば、**2020-03-13T05:59:00-08:00** など自身のローカル タイム ゾーンを使用できます。 また、**2018-03-13T13:59:00Z** など UTC Zulu 形式も使用できます。 |
| UsePointInTimeRestore | `<SwitchParameter>` | ポイントインタイム モードを使用して復元します。 |

サーバーを過去の特定の時点に復元すると、新しいサーバーが作成されます。 特定の時点における元のサーバーとそのデータベースが新しいサーバーにコピーされます。

復元されたサーバーの場所と価格レベルの値は、元のサーバーと同じです。

復元プロセスが完了したら、新しいサーバーを検索して、想定どおりにデータが復元できたかどうかを確認します。 新しいサーバーには、復元が開始された時点の既存のサーバーで有効であったサーバー管理者のログイン名とパスワードが設定されています。 このパスワードは、新しいサーバーの **[概要]** ページで変更できます。

復元中に作成される新しいサーバーには、元のサーバーに存在した VNet サービス エンドポイントはありません。 新しいサーバー用に、これらの規則を個別に設定する必要があります。 元のサーバーのファイアウォール規則は復元されます。

## <a name="geo-restore"></a>geo リストア

geo 冗長バックアップを使用するようにサーバーを構成した場合は、新しいサーバーをその既存のサーバーのバックアップから作成できます。 この新しいサーバーは、Azure Database for PostgreSQL を使用できる任意のリージョンに作成できます。

geo 冗長バックアップを使ってサーバーを作成するには、**UseGeoRestore** パラメーターを指定して `Restore-AzPostgreSqlServer` コマンドを使用します。

> [!NOTE]
> サーバーが最初に作成された時点では、すぐには geo リストアで使用できない可能性があります。 必要なメタデータが設定されるまで数時間かかる場合があります。

サーバーを geo リストアするには、PowerShell から次の例を実行します。

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzPostgreSqlServer -Name mydemoserver-georestored -ResourceGroupName myresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

この例は、**myresourcegroup** に属する **mydemoserver-georestored** という名前の新しいサーバーを米国東部リージョンに作成します。 これは、8 個の仮想コアを備えた General Purpose Gen 5 サーバーです。 サーバーは **mydemoserver** の geo 冗長バックアップ (これもリソース グループ **myresourcegroup** に含まれます) から作成されます。

既存のサーバーとは異なるリソース グループに新しいサーバーを作成するには、次の例のように **ResourceGroupName** パラメーターを使用して新しいリソース グループ名を指定します。

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzPostgreSqlServer -Name mydemoserver-georestored -ResourceGroupName newresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

`Restore-AzPostgreSqlServer` コマンドレットの **GeoRestore** パラメーター セットには、次のパラメーターが必要です。

| 設定 | 推奨値 | 説明  |
| --- | --- | --- |
|ResourceGroupName | myresourcegroup | 新しいサーバーが属するリソース グループの名前。|
|名前 | mydemoserver-georestored | 新しいサーバーの名前。 |
|場所 | eastus | 新しいサーバーの場所。 |
|UseGeoRestore | `<SwitchParameter>` | geo モードを使用して復元します。 |

geo リストアを使用して新しいサーバーを作成すると、**Sku** パラメーターが指定されていない限り、新しいサーバーは元のサーバーと同じストレージ サイズおよび価格レベルを継承します。

復元プロセスが完了したら、新しいサーバーを検索して、想定どおりにデータが復元できたかどうかを確認します。 新しいサーバーには、復元が開始された時点の既存のサーバーで有効であったサーバー管理者のログイン名とパスワードが設定されています。 このパスワードは、新しいサーバーの **[概要]** ページで変更できます。

復元中に作成される新しいサーバーには、元のサーバーに存在した VNet サービス エンドポイントはありません。 この新しいサーバー用に、これらの規則を個別に設定する必要があります。 元のサーバーのファイアウォール規則は復元されます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [PowerShell を使用して Azure Database for PostgreSQL 接続文字列を生成する方法](howto-connection-string-powershell.md)