---
title: CLI の例 - BYOK TDE の有効化 - Azure SQL Managed Instance
description: PowerShell を使用して、保存時の暗号化に BYOK Transparent Data Encryption (TDE) を使用するように Azure SQL Managed Instance を構成する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: azurecli
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto
ms.date: 11/05/2019
ms.openlocfilehash: 2b948161633569d629dfb048a7d7dee6a9946f43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91323690"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Azure Key Vault の独自のキーを使用して Managed Instance で Transparent Data Encryption を管理する

この Azure CLI スクリプトの例では、Azure Key Vault のキーを使用し、Azure SQL Managed Instance のユーザー管理キーで Transparent Data Encryption (TDE) を構成します。 よく "TDE の Bring Your Own Key" と呼ばれるシナリオです。 ユーザー管理キーを使用した TDE の詳細については、[Azure SQL の TDE Bring Your Own Key](../../azure-sql/database/transparent-data-encryption-byok-overview.md) に関するページを参照してください。

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="sample-script"></a>サンプル スクリプト

### <a name="prerequisites"></a>前提条件

マネージド インスタンスがあらかじめ存在すること。[Azure CLI を使用して Azure SQL Managed Instance を作成する方法](sql-database-create-configure-managed-instance-cli.md)に関するページを参照してください。

### <a name="sign-in-to-azure"></a>Azure へのサインイン

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>スクリプトを実行する

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.sh "Set up BYOK TDE for SQL Managed Instance")]

### <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを使用して、リソース グループと、それに関連付けられているすべてのリソースを削除します。

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>サンプル リファレンス

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | 説明 |
|---|---|
| [az sql db](/cli/azure/sql/db) | データベースのコマンド。 |
| [az sql failover-group](/cli/azure/sql/failover-group) | フェールオーバー グループのコマンド。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の SQL Database 用の CLI サンプル スクリプトは、[Azure SQL Database のドキュメント](../../azure-sql/database/az-cli-script-samples-content-guide.md)のページにあります。
