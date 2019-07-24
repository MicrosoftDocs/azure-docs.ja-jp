---
title: Azure CLI スクリプト - サーバーの構成を変更する
description: このサンプル CLI スクリプトは、すべての利用可能なサーバー構成オプションを一覧表示し、1 つのオプションの値を更新します。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: 6b5a855c8db5cb87f313e14c42396ae70b407e61
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2019
ms.locfileid: "55692207"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Azure CLI を使用して Azure Database for PostgreSQL サーバーの構成を一覧表示および更新する
このサンプル CLI スクリプトは、Azure Database for PostgreSQL サーバーのすべての利用可能な構成パラメーターと、許容値を一覧表示し、*log_retention_days* を既定値以外の値に設定します。

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

CLI をローカルで実行する場合、この記事では、Azure CLI バージョン 2.0 以降が必要です。 `az --version` を実行してバージョンを確認します。 「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照して Azure CLI をインストールするか、お使いのバージョンをアップグレードしてください。 

## <a name="sample-script"></a>サンプル スクリプト
このサンプル スクリプトでは、強調表示された行を編集して、管理者のユーザー名とパスワードを自分のユーザー名とパスワードに更新します。
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
スクリプトの実行後は、次のコマンドを使用してリソース グループとそこに関連付けられているすべてのリソースを削除してください。 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>スクリプトの説明
このスクリプトで使用されているコマンドを次の表にまとめました。

| **コマンド** | **メモ** |
|---|---|
| [az group create](/cli/azure/group) | すべてのリソースを格納するリソース グループを作成します。 |
| [az postgres server create](/cli/azure/postgres/server) | データベースをホストする PostgreSQL サーバーを作成します。 |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration) | Azure Database for PostgreSQL サーバーの構成を一覧表示します。 |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration) | Azure Database for PostgreSQL サーバーの構成を更新します。 |
| [az postgres server configuration show](/cli/azure/postgres/server/configuration) | Azure Database for PostgreSQL サーバーの構成を表示します。 |
| [az group delete](/cli/azure/group) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次の手順
- Azure CLI に関する詳細を読む: [Azure CLI ドキュメント](/cli/azure)
- さらに他のスクリプトも試す: [Azure Database for PostgreSQL 用 Azure CLI サンプル](../sample-scripts-azure-cli.md)
- サーバー パラメーターについて詳しくは、[Azure Portal でサーバー パラメータを構成する方法](../howto-configure-server-parameters-using-portal.md)についての記事をご覧ください。
