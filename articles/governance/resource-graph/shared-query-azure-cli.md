---
title: クイック スタート:Azure CLI で共有クエリを作成する
description: このクイックスタートでは、手順に従って Azure CLI の Resource Graph 拡張機能を有効にし、共有クエリを作成します。
ms.date: 02/05/2021
ms.topic: quickstart
ms.openlocfilehash: ec1b010771790339a13777624f04c7bd2db01f11
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594385"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-cli"></a>クイック スタート:Azure CLI を使用して Resource Graph 共有クエリを作成する

[Azure CLI](/cli/azure/) で Azure Resource Graph を使用する最初の手順は、拡張機能がインストールされていることを確認することです。 このクイック スタートでは、Azure CLI のインストールに拡張機能を追加するプロセスについて説明します。 ローカルにインストールされている Azure CLI または [Azure Cloud Shell](https://shell.azure.com) により拡張機能を使用することができます。

このプロセスの最後では、選択した Azure CLI のインストールに拡張機能を追加し、Resource Graph 共有クエリを作成します。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-extension"></a>Resource Graph 拡張機能を追加する

Azure CLI を Azure Resource Graph と連動させるには、拡張機能を追加する必要があります。 [Windows 10上の bash](/windows/wsl/install-win10)、 [Cloud Shell](https://shell.azure.com) (スタンドアロンと内部の両方のポータル)、 [Azure CLI Docker 画像](https://hub.docker.com/_/microsoft-azure-cli)、または、ローカルにインストールされた場合を含め、この拡張機能はAzure CLI を使用できる場合はいつでも動作します。

1. 最新の Azure CLI がインストールされていることを確認します (**2.8.0** 以降)。 インストールされていない場合は、こちらの[手順](/cli/azure/install-azure-cli-windows)に従ってください。

1. 選択した Azure CLI 環境で [az extension add](/cli/azure/extension#az_extension_add) を使用し、次のコマンドで Resource Graph 拡張機能をインポートします。

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. 拡張機能がインストールされていて、必要なバージョン (**1.1.0** 以降) であることを [az extension list](/cli/azure/extension#az_extension_list) で検証します。

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="create-a-resource-graph-shared-query"></a>Resource Graph 共有クエリを作成する

選択した環境に Azure CLI 拡張機能が追加されたので、今度は Resource Graph 共有クエリです。 共有クエリは、Azure Resource Graph エクスプローラーで権限を与えたり、実行したりできる Azure Resource Manager オブジェクトです。 このクエリでは、すべてのリソースの総数が "_場所_" 別にグループ化され、まとめられます。

1. Azure Resource Graph 共有クエリを格納するリソース グループを [az group create](/cli/azure/group#az_group_create) で作成します。 このリソース グループの名前は `resource-graph-queries` であり、場所は `westus2` です。

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Create the resource group
   az group create --name 'resource-graph-queries' --location 'westus2'
   ```

1. `graph` 拡張機能と [az graph shared-query create](/cli/azure/ext/resource-graph/graph/shared-query#ext_resource_graph_az_graph_shared_query_create) コマンドを使用し、Azure Resource Graph 共有クエリを作成します。

   ```azurecli-interactive
   # Create the Azure Resource Graph shared query
   az graph shared-query create --name 'Summarize resources by location' \
      --description 'This shared query summarizes resources by location for a pinnable map graphic.' \
      --graph-query 'Resources | summarize count() by location' \
      --resource-group 'resource-graph-queries'
   ```

1. 新しいリソース グループの共有クエリを一覧表示します。 [az graph shared-query list](/cli/azure/ext/resource-graph/graph/shared-query#ext_resource_graph_az_graph_shared_query_list) コマンドによって値の配列が返されます。

   ```azurecli-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   az graph shared-query list --resource-group 'resource-graph-queries'
   ```

1. 共有クエリの結果を 1 つだけ取得するには、[az graph shared-query show](/cli/azure/ext/resource-graph/graph/shared-query#ext_resource_graph_az_graph_shared_query_show) コマンドを使用します。

   ```azurecli-interactive
   # Show a specific Azure Resource Graph shared query
   az graph shared-query show --resource-group 'resource-graph-queries' \
      --name 'Summarize resources by location'
   ```

1. Azure CLI の [az graph query](/cli/azure/ext/resource-graph/graph#ext_resource_graph_az_graph_query) コマンド内で `{{shared-query-uri}}` 構文を利用し、共有クエリを実行します。
   まず、前の `show` コマンドの結果から `id` フィールドをコピーします。 例の `shared-query-uri` テキストを `id` フィールドからの値で置換しますが、前後の `{{` 文字と `}}` 文字はそのままにします。

   ```azurecli-interactive
   # Run a Azure Resource Graph shared query
   az graph query --graph-query "{{shared-query-uri}}"
   ```

   > [!NOTE]
   > `{{shared-query-uri}}` 構文は **プレビュー** 機能です。

Resource Graph 共有クエリを見つけるもう 1 つの方法は Azure portal を利用することです。 ポータルで検索バーを使用し、"Resource Graph のクエリ" を検索します。 共有クエリを選択します。 **[概要]** ページの **[クエリ]** タブに保存済みのクエリが表示されます。 **[編集]** ボタンをクリックすると、[[Resource Graph エクスプローラー]](./first-query-portal.md) でそれが開きます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Resource Graph の共有クエリ、リソース グループ、拡張機能を Azure CLI 環境から削除する場合、次のコマンドを使用してそれを行うことができます。

- [az graph shared-query delete](/cli/azure/ext/resource-graph/graph/shared-query#ext_resource_graph_az_graph_shared_query_delete)
- [az group delete](/cli/azure/group#az_group_delete)
- [az extension remove](/cli/azure/extension#az_extension_remove)

```azurecli-interactive
# Delete the Azure Resource Graph shared query
az graph shared-query delete --resource-group 'resource-graph-queries' \
   --name 'Summarize resources by location'

# Remove the resource group
# WARNING: This command deletes ALL resources you've added to this resource group without prompting for confirmation
az group delete --resource-group 'resource-graph-queries' --yes

# Remove the Azure Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure CLI 環境に Resource Graph 拡張機能を追加し、共有クエリを作成しました。 Resource Graph 言語の詳細については、クエリ言語の詳細ページに進んでください。

> [!div class="nextstepaction"]
> [クエリ言語に関する詳細情報を入手します](./concepts/query-language.md)