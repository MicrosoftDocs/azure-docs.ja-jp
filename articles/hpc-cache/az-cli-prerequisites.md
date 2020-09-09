---
title: Azure HPC Cache の Azure CLI の前提条件
description: Azure CLI を使用して Azure HPC Cache を作成または変更する前のセットアップ手順
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 7d0844e699c012d987c23a75e2b0874005cf535a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87095435"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Azure HPC Cache 向けに Azure CLI を設定する

Azure CLI を使用して Azure HPC Cache を作成または管理する前に、次の手順に従って環境を準備します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="install-azure-cli"></a>Azure CLI のインストール

Azure HPC Cache には、Azure CLI のバージョン 2.7 以降が必要です。 `az --version` を実行し、インストールされているバージョンおよび依存ライブラリを検索します。 インストールまたはアップグレードする必要には、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」をご覧ください。

```azurecli-interactive
az --version
```

### <a name="install-the-azure-hpc-cache-extension"></a>Azure HPC Cache 拡張機能をインストールする

Azure HPC Cache 関数は、メインのコードベースの一部ではないため、拡張機能の参照もインストールする必要があります。 次の手順に従ってください。

1. サインイン

   ローカルにインストールされているバージョンの CLI を使用する場合は、[az login](/cli/azure/reference-index#az-login) コマンドを使用してサインインします。

    ```azurecli
    az login
    ```

    ターミナルに表示される手順に従って、認証プロセスを完了します。

   > [!TIP]
   > 複数のサブスクリプションがある場合は、サブスクリプションを 1 つ選択する必要があります。 これは、Azure Portal で Cloud Shell セッションを開始するときに選択します。または「[Azure CLI の概要](/cli/azure/get-started-with-azure-cli#sign-in)」の手順に従って、コマンド ラインからサブスクリプションを設定します。

2. Azure CLI 拡張機能をインストールする

   Azure HPC Cache 関数は、Azure CLI の拡張機能として提供されています。コア CLI パッケージにはまだ含まれていません。 使用するには、拡張機能の参照をインストールする必要があります。

   Azure CLI 拡張機能により、リリース前の実験的なコマンドにアクセスすることができます。 更新とアンインストールを含む拡張機能の詳細については、「[Azure CLI で拡張機能を使用する](/cli/azure/azure-cli-extensions-overview)」を参照してください。

   次のコマンドを実行して、Azure HPC Cache の拡張機能をインストールします。

    ```azurecli-interactive
    az extension add --name hpc-cache
   ```

## <a name="set-default-resource-group-optional"></a>既定のリソース グループを設定する (省略可能)

ほとんどの hpc-cache コマンドでは、キャッシュのリソース グループを渡す必要があります。 既定のリソース グループを設定するには、[az configure](/cli/azure/reference-index#az-configure) を使用します。

## <a name="next-steps"></a>次のステップ

Azure CLI 拡張機能をインストールしてログインすると、Azure CLI を使用して Azure HPC Cache システムを作成および管理できます。

* [Azure HPC キャッシュを作成する](hpc-cache-create.md)
* [Azure CLI hpc-cache のドキュメント](/cli/azure/ext/hpc-cache/hpc-cache)
