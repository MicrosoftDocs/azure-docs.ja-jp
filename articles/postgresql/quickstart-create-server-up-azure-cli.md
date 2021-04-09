---
title: クイック スタート:サーバーを作成する - az postgres up - Azure Database for PostgreSQL - Single Server
description: Azure CLI (コマンド ライン インターフェイス) の up コマンドを使用して Azure Database for PostgreSQL - 単一サーバーを作成するためのクイックスタート ガイド。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/06/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: c11a98c04f81e7c111faf4aa449ab2bf9a4890bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92485224"
---
# <a name="quickstart-use-an-azure-cli-command-az-postgres-up-preview-to-create-an-azure-database-for-postgresql---single-server"></a>クイック スタート:Azure CLI コマンドの az postgres up (プレビュー) を使用して Azure Database for PostgreSQL - 単一サーバーを作成する

> [!IMPORTANT]
> [az postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) という Azure CLI コマンドはプレビュー段階です。

Azure Database for PostgreSQL は、高可用性 PostgreSQL データベースをクラウドで実行、管理、および拡張することができる、管理されたサービスです。 Azure CLI は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。 このクイック スタートでは、[az postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) コマンドを使用し、Azure CLI を使って Azure Database for PostgreSQL サーバーを作成する方法を示します。 サーバーの作成に加え、`az postgres up` コマンドではサンプル データベース、データベースのルート ユーザーを作成し、Azure サービスのファイアウォールを開き、クライアント コンピューターの既定のファイアウォール規則を作成します。 これらの既定値は、開発プロセスを促進するために役立ちます。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

この記事では、Azure CLI バージョン 2.0 以降をローカルで実行している必要があります。 インストールされているバージョンを確認するには、`az --version` コマンドを実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

[az login](/cli/azure/authenticate-azure-cli) コマンドを使用してアカウントにサインインする必要があります。 コマンド出力から、対応するサブスクリプション名の **ID** プロパティを書き留めておきます。

```azurecli
az login
```

複数のサブスクリプションをお持ちの場合は、リソースが課金の対象となる適切なサブスクリプションを選択してください。 [az account set](/cli/azure/account) コマンドを使用して、アカウントの特定のサブスクリプション ID を選択します。 サブスクリプション ID プレースホルダーへのサブスクリプションを、**az login** 出力の **サブスクリプション ID** プロパティに置き換えます。

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバーの作成

コマンドを使用するには、[db-up](/cli/azure/ext/db-up) 拡張機能をインストールします。 エラーが返された場合は、最新バージョンの Azure CLI がインストールされていることを確認してください。 [Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

```azurecli
az extension add --name db-up
```

次のコマンドを使用して、Azure Database for PostgreSQL サーバーを作成します。

```azurecli
az postgres up
```

サーバーは以下の既定値で作成されます (これらを手動でオーバーライドしない限り)。

**設定** | **既定値** | **説明**
---|---|---
server-name | システム生成 | Azure Database for PostgreSQL サーバーを識別する一意の名前。
resource-group | システム生成 | 新しい Azure リソース グループ。
sku-name | GP_Gen5_2 | SKU の名前。 省略表現の {価格レベル}\_{コンピューティング世代}\_{仮想コア} という規則に従います。 既定値は、2 個の仮想コアを備えた General Purpose Gen5 サーバーです。 レベルの詳細については、[価格に関するページ](https://azure.microsoft.com/pricing/details/postgresql/)を参照してください。
backup-retention | 7 | バックアップが保持される期間。 単位は日数です。
geo-redundant-backup | 無効 | このサーバーに対して geo 冗長バックアップを有効にする必要があるかどうかどうか。
location | westus2 | サーバーの Azure の場所。
ssl-enforcement | 無効 | このサーバーに対して TLS/SSL を有効にする必要があるかどうかどうか。
storage-size | 5120 | サーバーのストレージ容量 (単位はメガバイト)。
version | 10 | PostgreSQL のメジャー バージョン。
admin-user | システム生成 | 管理者のユーザー名。
admin-password | システム生成 | 管理者ユーザーのパスワード。

> [!NOTE]
> `az postgres up` コマンドとその追加パラメーターの詳細については、[Azure CLI のドキュメント](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up)を参照してください。

サーバーが作成されると、設定は次のようになります。

- "devbox" というファイアウォール規則が作成されます。 Azure CLI では、`az postgres up` コマンドの実行元となるマシンの IP アドレスの検出が試行され、その IP アドレスが許可されます。
- [Azure サービスへのアクセスを許可] が [オン] に設定されます。 この設定により、サブスクリプションにはないリソースを含む、すべての Azure リソースからの接続を受け入れるようにサーバーのファイアウォールが構成されます。
- "sampledb" という名前の空のデータベースが作成されます
- "sampledb" に対する特権を持つ "root" という名前の新しいユーザーが作成されます

> [!NOTE]
> Azure Database for PostgreSQL はポート 5432 経由で通信を行います。 企業ネットワーク内から接続しようとしても、ポート 5432 での送信トラフィックがネットワークのファイアウォールで禁止されている場合があります。 サーバーに接続するには、IT 部門にポート 5432 を開放してもらう必要があります。

## <a name="get-the-connection-information"></a>接続情報の取得

`az postgres up` コマンドが完了した後、一般的なプログラミング言語の接続文字列のリストが返されます。 これらの接続文字列は、新しく作成した Azure Database for PostgreSQL サーバーの特定の属性で事前に構成されています。

[az postgres show-connection-string](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-show-connection-string) コマンドを使用して、これらの接続文字列をもう一度リストすることができます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

以下のコマンドを使用して、クイック スタートで作成したすべてのリソースをクリーンアップします。 このコマンドによって、Azure Database for PostgreSQL サーバーとリソース グループが削除されます。

```azurecli
az postgres down --delete-group
```

新しく作成したサーバーを削除するだけの場合は、[az postgres down](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-down) コマンドを実行できます。

```azurecli
az postgres down
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [エクスポートとインポートを使用したデータベースの移行](./howto-migrate-using-export-and-import.md)
