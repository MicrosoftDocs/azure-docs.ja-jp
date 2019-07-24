---
title: Azure CLI スクリプト - サーバーの構成を変更する
description: このサンプル CLI スクリプトは、すべての利用可能なサーバー構成を一覧表示し、innodb_lock_wait_timeout の値を更新します。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 11/28/2018
ms.openlocfilehash: 514f2fe7e8c2b94e175bc6f6fb0cec8ea3d0c9c6
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53547424"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>Azure CLI を使用して Azure Database for MariaDB サーバーの構成を一覧表示および更新する
このサンプル CLI スクリプトは、Azure Database for MariaDB サーバーのすべての利用可能な構成パラメーターとその許容値を一覧表示し、*innodb_lock_wait_timeout* を既定値以外の値に設定します。

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

CLI をローカルで実行する場合、この記事では、Azure CLI バージョン 2.0 以降が必要です。 `az --version` を実行してバージョンを確認します。 「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照して Azure CLI をインストールするか、お使いのバージョンをアップグレードしてください。 

## <a name="sample-script"></a>サンプル スクリプト
このサンプル スクリプトでは、強調表示された行を編集して、管理者のユーザー名とパスワードを自分のユーザー名とパスワードに更新します。
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
スクリプトの実行後は、次のコマンドを使用してリソース グループとそこに関連付けられているすべてのリソースを削除してください。
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>スクリプトの説明
このスクリプトで使用されているコマンドを次の表にまとめました。

| **コマンド** | **メモ** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) | データベースのホストとなる MariaDB サーバーを作成します。 |
| [az mariadb server configuration list](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) | Azure Database for MariaDB サーバーの構成を一覧表示します。 |
| [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) | Azure Database for MariaDB サーバーの構成を更新します。 |
| [az mariadb server configuration show](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show) | Azure Database for MariaDB サーバーの構成を表示します。 |
| [az group delete](/cli/azure/group#az-group-delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次の手順
- Azure CLI に関する詳細を読む: [Azure CLI ドキュメント](/cli/azure)
- さらに他のスクリプトも試す: [Azure Database for MariaDB 用の Azure CLI サンプル](../sample-scripts-azure-cli.md)
- サーバー パラメーターの詳細については、[Azure Database for MariaDB のサーバー パラメーターを構成する方法](../howto-server-parameters.md)に関するページを参照してください。
