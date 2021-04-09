---
title: クイック スタート:az mysql up を使用して Azure Database for MySQL を作成する
description: Azure CLI (コマンド ライン インターフェイス) up コマンドを使用して Azure Database for MySQL サーバーを作成するためのクイック スタート ガイド。
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d89cc41ed26124ae4ad2e6689be6d59278c3d9da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94542169"
---
# <a name="quickstart-create-an-azure-database-for-mysql-using-a-simple-azure-cli-command---az-mysql-up-preview"></a>クイック スタート:シンプルな Azure CLI コマンド az mysql up (プレビュー) を使用して Azure Database for MySQL を作成する

> [!IMPORTANT]
> [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) という Azure CLI コマンドはプレビュー段階です。

Azure Database for MySQL は、高可用性 MySQL データベースをクラウドで実行、管理、および拡張することができる、管理されたサービスです。 Azure CLI は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。 このクイック スタートでは、[az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) コマンドを使用し、Azure CLI を使って Azure Database for MySQL サーバーを作成する方法を示します。 サーバーの作成に加え、`az mysql up` コマンドではサンプル データベース、データベースのルート ユーザーを作成し、Azure サービスのファイアウォールを開き、クライアント コンピューターの既定のファイアウォール規則を作成します。 これは開発プロセスを効率よく進めるために役立ちます。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

この記事では、Azure CLI バージョン 2.0 以降をローカルで実行している必要があります。 インストールされているバージョンを確認するには、`az --version` コマンドを実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

[az login](/cli/azure/authenticate-azure-cli) コマンドを使用して、アカウントにログインする必要があります。 対応するサブスクリプション名のコマンド出力で **id** プロパティを確認します。

```azurecli
az login
```

複数のサブスクリプションをお持ちの場合は、リソースが課金の対象となる適切なサブスクリプションを選択してください。 [az account set](/cli/azure/account) コマンドを使用して、アカウントの特定のサブスクリプション ID を選択します。 サブスクリプション ID プレースホルダーへのサブスクリプションを、**az login** 出力の **サブスクリプション ID** プロパティに置き換えます。

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーの作成

コマンドを使用するには、[db-up](/cli/azure/ext/db-up) 拡張機能をインストールします。 エラーが返された場合は、最新バージョンの Azure CLI がインストールされていることを確認してください。 [Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

```azurecli
az extension add --name db-up
```

次のコマンドを使用して、Azure Database for MySQL サーバーを作成します。

```azurecli
az mysql up
```

サーバーは以下の既定値で作成されます (これらを手動でオーバーライドしない限り)。

**設定** | **既定値** | **説明**
---|---|---
server-name | システム生成 | Azure Database for MySQL サーバーを識別する一意の名前。
resource-group | システム生成 | 新しい Azure リソース グループ。
sku-name | GP_Gen5_2 | SKU の名前。 省略表現の {価格レベル}\_{コンピューティング世代}\_{仮想コア} という規則に従います。 既定値は、2 個の仮想コアを備えた General Purpose Gen5 サーバーです。 レベルの詳細については、[価格に関するページ](https://azure.microsoft.com/pricing/details/mysql/)を参照してください。
backup-retention | 7 | バックアップを保持する必要のある時間。 単位は日数です。
geo-redundant-backup | 無効 | このサーバーに対して geo 冗長バックアップを有効にする必要があるかどうかどうか。
location | westus2 | サーバーの Azure の場所。
ssl-enforcement | Enabled | このサーバーに対して SSL を有効にする必要があるかどうかどうか。
storage-size | 5120 | サーバーのストレージ容量 (単位はメガバイト)。
version | 5.7 | MySQL のメジャー バージョン。
admin-user | システム生成 | 管理者ログインのユーザー名。
admin-password | システム生成 | 管理者ユーザーのパスワード。

> [!NOTE]
> `az mysql up` コマンドとその追加パラメーターの詳細については、[Azure CLI のドキュメント](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up)を参照してください。

サーバーが作成されると、設定は次のようになります。

- "devbox" というファイアウォール規則が作成されます。 Azure CLI では、`az mysql up` コマンドの実行元となるマシンの IP アドレスの検出が試行され、その IP アドレスが許可されます。
- [Azure サービスへのアクセスを許可] が [オン] に設定されます。 この設定により、サブスクリプションにはないリソースを含む、すべての Azure リソースからの接続を受け入れるようにサーバーのファイアウォールが構成されます。
- `wait_timeout` パラメーターは 8 時間に設定されています。
- "sampledb" という名前の空のデータベースが作成されます
- "sampledb" に対する特権を持つ "root" という名前の新しいユーザーが作成されます

> [!NOTE]
> Azure Database for MySQL は、ポート 3306 経由で通信します。 企業ネットワーク内から接続しようとしても、ポート 3306 での送信トラフィックがネットワークのファイアウォールで禁止されている場合があります。 サーバーに接続するには、IT 部門にポート 3306 を開放してもらう必要があります。

## <a name="get-the-connection-information"></a>接続情報の取得

`az mysql up` コマンドが完了した後、一般的なプログラミング言語の接続文字列のリストが返されます。 これらの接続文字列は、新しく作成した Azure Database for MySQL サーバーの特定の属性で事前に構成されています。

[az mysql show-connection-string](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-show-connection-string) コマンドを使用して、これらの接続文字列をもう一度リストすることができます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

以下のコマンドを使用して、クイック スタートで作成したすべてのリソースをクリーンアップします。 このコマンドによって、Azure Database for MySQL サーバーとリソース グループが削除されます。

```azurecli
az mysql down --delete-group
```

新しく作成したサーバーを削除するだけの場合は、[az mysql down](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-down) コマンドを実行できます。

```azurecli
az mysql down
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure CLI での MySQL データベースの設計](./tutorial-design-database-using-cli.md)
