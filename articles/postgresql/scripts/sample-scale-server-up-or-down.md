---
title: "Azure CLI スクリプト - Azure Database for PostgreSQL のスケーリング | Microsoft Docs"
description: "Azure CLI スクリプト サンプル - Azure Database for PostgreSQL サーバーのメトリックを照会したうえで、異なるパフォーマンス レベルにスケーリングします。"
services: postgresql
author: salonisonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.custom: mvc
ms.topic: sample
ms.date: 05/31/2017
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: b847abb336cce5dd5516469dca58002d3ba265f0
ms.contentlocale: ja-jp
ms.lasthandoff: 08/23/2017

---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Azure CLI での単一の PostgreSQL サーバーの監視とスケーリング
このサンプル CLI スクリプトは、単一の Azure Database for PostgreSQL サーバーのメトリックを照会したうえで、異なるパフォーマンス レベルにスケーリングするものです。 

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このトピックでは、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

## <a name="sample-script"></a>サンプル スクリプト
このサンプル スクリプトでは、強調表示された行を変更して、管理者のユーザー名とパスワードをカスタマイズします。 az monitor コマンドで使用するサブスクリプション ID を、自分のサブスクリプション ID で置き換えます。[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh?highlight=15-16 "Azure Database for PostgreSQL を作成およびスケーリングする。")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "リソース グループを削除します。")]

## <a name="script-explanation"></a>スクリプトの説明
このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| **コマンド** | **メモ** |
|---|---|
| [az group create](/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az postgres server create](/cli/azure/postgres/server#create) | データベースをホストする PostgreSQL サーバーを作成します。 |
| [az monitor metrics list](/cli/azure/monitor/metrics#list) | リソースのメトリック値を一覧表示します。 |
| [az group delete](/cli/azure/group#delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ
- Azure CLI に関する詳細を読む: [Azure CLI ドキュメント](/cli/azure/overview)
- 他のスクリプトを試す: [Azure Database for PostgreSQL の Azure CLI サンプル](../sample-scripts-azure-cli.md)
- スケーリングに関する詳細を読む: [サービス階層](../concepts-service-tiers.md)および[コンピューティング ユニットとストレージ ユニット](../concepts-compute-unit-and-storage.md)

