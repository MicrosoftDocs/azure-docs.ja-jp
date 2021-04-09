---
title: ストレージの自動拡張 - Azure PowerShell - Azure Database for PostgreSQL
description: この記事では、PowerShell を使用して Azure Database for PostgreSQL のストレージの自動拡張を有効にする方法について説明します。
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: bc4655ce6cd572183cd92e1c8b2ac10e613ebd8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92489967"
---
# <a name="auto-grow-storage-in-azure-database-for-postgresql-server-using-powershell"></a>PowerShell を使用して Azure Database for PostgreSQL サーバーのストレージを自動拡張する

この記事では、ワークロードに影響を与えることなく拡張されるように Azure Database for PostgreSQL サーバーのストレージを構成する方法について説明します。

ストレージの自動拡張により、サーバーの[ストレージ制限に達する](./concepts-pricing-tiers.md#reaching-the-storage-limit)ことで読み取り専用になることを防ぎます。 プロビジョニングされたストレージが 100 GB 以上のサーバーでは、空き領域が10% を下回ると、サイズが 5 GB 増加します。 プロビジョニングされたストレージが 100 GB を超えるサーバーの場合、空き領域が 10 GB を下回ると、サイズが 5% 増加します。 [Azure Database for PostgreSQL 価格レベル](./concepts-pricing-tiers.md#storage)の [ストレージ] セクションで指定したストレージの最大制限が適用されます。

> [!IMPORTANT]
> ストレージはスケールアップのみ可能で、スケールダウンはできないことに注意してください。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドを完了するには、次が必要です。

- ローカルにインストールされた [Az PowerShell モジュール](/powershell/azure/install-az-ps)、またはブラウザーの [Azure Cloud Shell](https://shell.azure.com/)
- [Azure Database for PostgreSQL サーバー](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az.PostgreSql PowerShell モジュールがプレビュー段階にある間は、コマンド `Install-Module -Name Az.PostgreSql -AllowPrerelease` を使用して、Az PowerShell モジュールとは別にこれをインストールする必要があります。
> Az.PostgreSql PowerShell モジュールは、一般提供された段階で将来の Az PowerShell モジュール リリースの一部となり、Azure Cloud Shell 内からネイティブに使用できるようになります。

PowerShell をローカルで使用する場合は、[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドレットを使用して Azure アカウントに接続します。

## <a name="enable-postgresql-server-storage-auto-grow"></a>PostgreSQL サーバー ストレージの自動拡張を有効にする

次のコマンドを使用して、既存のサーバー上でサーバーの自動拡張ストレージを有効にします。

```azurepowershell-interactive
Update-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

次のコマンドを使用して、新しいサーバーの作成時にサーバーの自動拡張ストレージを有効にします。

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [PowerShell を使用して Azure Database for PostgreSQL の読み取りレプリカを作成し、管理する方法](howto-read-replicas-powershell.md)