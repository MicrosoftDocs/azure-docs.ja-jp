---
title: ファイアウォール規則の管理 - Azure CLI - Azure Database for PostgreSQL - Single Server
description: この記事では、Azure CLI コマンド ラインを使用して Azure Database for PostgreSQL - Single Server のファイアウォール規則を作成し、管理する方法について説明します。
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3a559b8c65ab57b0144b807a3b4cc1faa912d430
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93422741"
---
# <a name="create-and-manage-firewall-rules-in-azure-database-for-postgresql---single-server-using-azure-cli"></a>Azure CLI を使用して Azure Database for PostgreSQL - Single Server のファイアウォール規則を作成および管理する
サーバーレベルのファイアウォール規則を使用して、特定の IP アドレスまたは IP アドレス範囲からの Azure Database for PostgreSQL サーバーへのアクセスを管理できます。 便利な Azure CLI コマンドを使用すると、サーバーを管理するためのファイアウォール規則の作成、更新、削除、一覧化、表示などができます。 Azure Database for PostgreSQL ファイアウォール規則の概要については、「[Azure Database for PostgreSQL サーバーのファイアウォール規則](concepts-firewall-rules.md)」をご覧ください。

仮想ネットワーク (VNet) 規則を使用して、サーバーへのアクセスをセキュリティで保護することもできます。 [Azure CLI を使用した仮想ネットワーク サービス エンドポイントと規則の作成と管理](howto-manage-vnet-using-cli.md)の詳細を確認してください。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドの手順を実行するには、以下が必要です。
- [Azure CLI](/cli/azure/install-azure-cli) コマンド ライン ユーティリティをインストールするか、ブラウザーで Azure Cloud Shell を使用します。
- [Azure Database for PostgreSQL サーバーとデータベース](quickstart-create-server-database-azure-cli.md)。

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Azure Database for PostgreSQL のファイアウォール規則の構成
ファイアウォール規則を構成するには、[az postgres server firewall-rule](/cli/azure/postgres/server/firewall-rule) コマンドを使用します。

## <a name="list-firewall-rules"></a>ファイアウォール規則の一覧表示 
既存のサーバー ファイアウォール規則の一覧を表示するには、[az postgres server firewall-rule list](/cli/azure/postgres/server/firewall-rule) コマンドを実行します。
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
既定では、JSON 形式でファイアウォール規則の一覧が出力されます (規則が存在する場合)。 出力をもっとわかりやすい表形式にする場合は、`--output table` スイッチを使用することもできます。
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-firewall-rule"></a>ファイアウォール規則の作成
サーバーに新しいファイアウォール規則を作成するには、[az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) コマンドを実行します。 


1 つの IP アドレスにのみアクセスを許可するには、次の例のように、`--start-ip-address` と `--end-ip-address` に同じアドレスを指定して、ここに示されている IP を固有の IP に置き換えます。
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowSingleIpAddress --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
Azure IP アドレスからの Azure Database for PostgreSQL サーバーへの接続を許可するには、次の例のように、開始 IP と終了 IP に IP アドレス 0.0.0.0 を指定します。
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowAllAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> このオプションは、ファイアウォールを構成して、他のお客様のサブスクリプションからの接続を含むすべての接続を許可します。 このオプションを選択する場合は、ログインおよびユーザーのアクセス許可が、承認されたユーザーのみにアクセスを制限していることを確認してください。
> 

正常に完了すると、作成したファイアウォール規則の詳細が、既定では JSON 形式で、コマンドの出力として一覧表示されます。 失敗した場合は、代わりにエラー メッセージが出力されます。

## <a name="update-firewall-rule"></a>ファイアウォール規則の更新 
サーバー上の既存のファイアウォール規則を更新するには、[az postgres server firewall-rule update](/cli/azure/postgres/server/firewall-rule) コマンドを使用します。 更新する既存のファイアウォール規則の名前、開始 IP、終了 IP 属性を入力します。
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.0
```
正常に完了すると、更新したファイアウォール規則の詳細が、既定では JSON 形式で、コマンドの出力として一覧表示されます。 失敗した場合は、代わりにエラー メッセージが出力されます。
> [!NOTE]
> ファイアウォール規則が存在しない場合は、更新コマンドによって新しい規則が作成されます。

## <a name="show-firewall-rule-details"></a>ファイアウォール規則の詳細の表示
[az postgres server firewall-rule show](/cli/azure/postgres/server/firewall-rule) コマンドを実行することで、既存のサーバー レベルのファイアウォール規則の詳細を表示することもできます。
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
正常に完了すると、指定したファイアウォール規則の詳細が、既定では JSON 形式で、コマンドの出力として一覧表示されます。 失敗した場合は、代わりにエラー メッセージが出力されます。

## <a name="delete-firewall-rule"></a>ファイアウォール規則の削除
サーバーへの特定の IP 範囲のアクセスを取り消すには、[az postgres server firewall-rule delete](/cli/azure/postgres/server/firewall-rule) コマンドを実行して既存のファイアウォール規則を削除します。 既存のファイアウォール規則の名前を入力します。
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
正常に完了すると、出力はありません。 失敗した場合は、エラー メッセージ テキストが返されます。

## <a name="next-steps"></a>次のステップ
- 同様に、Web ブラウザー上で [Azure Portal を使用して Azure Database for PostgreSQL ファイアウォール規則の作成と管理](howto-manage-firewall-using-portal.md)を行うことができます。
- [Azure Database for PostgreSQL サーバーのファイアウォール規則](concepts-firewall-rules.md)について理解を深めます。
- [Azure CLI を使用して仮想ネットワーク サービス エンドポイントと規則を作成および管理](howto-manage-vnet-using-cli.md)して、サーバーへのアクセスのセキュリティ保護を強化します。
- Azure Database for PostgreSQL サーバーに接続する方法については、「[Azure Database for PostgreSQL の接続ライブラリ](concepts-connection-libraries.md)」をご覧ください。
