---
title: Azure CLI スクリプト - Azure Database for MySQL の作成
description: このサンプル CLI スクリプトは、Azure Database for MySQL サーバーを作成して、サーバー レベルのファイアウォール規則を構成するものです。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.custom: mvc
ms.topic: sample
ms.date: 02/28/2018
ms.openlocfilehash: 6a73041218ab6182d8aa3df6261697cc8dd4efe7
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53535508"
---
# <a name="create-a-mysql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Azure CLI での MySQL サーバーの作成とファイアウォール規則の構成
このサンプル CLI スクリプトは、Azure Database for MySQL サーバーを作成して、サーバー レベルのファイアウォール規則を構成するものです。 スクリプトが正常に実行されると、すべての Azure サービスと構成済みの IP アドレスから MySQL サーバーにアクセスできるようになります。

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

CLI をローカルで実行する場合、この記事では、Azure CLI バージョン 2.0 以降が必要です。 `az --version` を実行してバージョンを確認します。 「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照して Azure CLI をインストールするか、お使いのバージョンをアップグレードしてください。 

## <a name="sample-script"></a>サンプル スクリプト
このサンプル スクリプトでは、強調表示された行を編集して、管理者のユーザー名とパスワードを自分のユーザー名とパスワードに更新します。
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/create-mysql-server-and-firewall-rule/create-mysql-server-and-firewall-rule.sh?highlight=15-16 "Create an Azure Database for MySQL, and server-level firewall rule.")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
スクリプトの実行後は、次のコマンドを使用してリソース グループとそこに関連付けられているすべてのリソースを削除してください。 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/create-mysql-server-and-firewall-rule/delete-mysql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>スクリプトの説明
このスクリプトで使用されているコマンドを次の表にまとめました。

| **コマンド** | **メモ** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create) | データベースのホストとなる MySQL サーバーを作成します。 |
| [az mysql server firewall create](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create) | 対象のサーバーとそこにあるデータベースに対し、入力した IP アドレス範囲からアクセスできるようにするファイアウォール規則を作成します。 |
| [az group delete](/cli/azure/group#az-group-delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次の手順
- Azure CLI に関する詳細を読む: [Azure CLI ドキュメント](/cli/azure)
- さらに他のスクリプトも試す: [Azure Database for MySQL 用 Azure CLI サンプル](../sample-scripts-azure-cli.md)
