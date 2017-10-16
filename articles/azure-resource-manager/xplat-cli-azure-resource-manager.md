---
title: "Azure CLI を使用したリソースの管理 | Microsoft Docs"
description: "Azure コマンド ライン インターフェイス (CLI) を使用して Azure のリソースとグループを管理します。"
editor: 
manager: timlt
documentationcenter: 
author: tfitzmac
services: azure-resource-manager
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: 2e3fdf06316bbf68abefe06024f63668bdf07b05
ms.contentlocale: ja-jp
ms.lasthandoff: 09/15/2017

---
# <a name="use-the-azure-cli-to-manage-azure-resources-and-resource-groups"></a>Azure CLI を使用して Azure のリソースとリソース グループを管理する

この記事では、Azure CLI と Azure Resource Manager でソリューションを管理する方法について説明します。 Resource Manager に慣れていない場合は、[Resource Manager の概要](resource-group-overview.md)に関するページをご覧ください。 このトピックは管理タスクに重点を置いています。 このチュートリアルの内容は次のとおりです。

1. リソース グループの作成
2. リソース グループへのリソースの追加
3. リソースへのタグの追加
4. 名前とタグの値に基づいたリソースへのクエリ実行
5. リソースへのロック適用と解除
6. リソース グループの削除

この記事では、Resource Manager テンプレートをサブスクリプションにデプロイする方法については説明しません。 詳細については、「[Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](resource-group-template-deploy-cli.md)」をご覧ください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をインストールしてローカルで使用するには、「[Install Azure CLI 2.0 (Azure CLI 2.0 のインストール)](/cli/azure/install-azure-cli)」をご覧ください。

## <a name="set-subscription"></a>サブスクリプションの設定

複数のサブスクリプションがある場合は、別のサブスクリプションに切り替えることができます。 最初に、自分のアカウントのすべてのサブスクリプションを見てみましょう。

```azurecli-interactive
az account list
```

有効および無効なサブスクリプションの一覧を返します。

```json
[
  {
    "cloudName": "AzureCloud",
    "id": "<guid>",
    "isDefault": true,
    "name": "Example Subscription One",
    "registeredProviders": [],
    "state": "Enabled",
    "tenantId": "<guid>",
    "user": {
      "name": "example@contoso.org",
      "type": "user"
    }
  },
  ...
]
```

サブスクリプションの 1 つが既定値としてマークされていることに注意してください。 このサブスクリプションが、現在操作しているコンテキストです。 別のサブスクリプションに切り替えるには、**az account set** コマンドを使用してサブスクリプション名を指定します。

```azurecli-interactive
az account set -s "Example Subscription Two"
```

現在のサブスクリプションのコンテキストを表示するには、パラメーターを付けずに **az account show** を使用してください。

```azurecli-interactive
az account show
```

## <a name="create-a-resource-group"></a>リソース グループの作成
サブスクリプションにリソースをデプロイする前に、そのリソースを含めるリソース グループを作成する必要があります。

リソース グループを作成するには、**az group create** コマンドを使用します。 このコマンドは **name** パラメーターを使用してリソース グループの名前を指定し、**location** パラメーターを使用して場所を指定します。

```azurecli-interactive
az group create --name TestRG1 --location "South Central US"
```

出力の形式は次のとおりです。

```json
{
  "id": "/subscriptions/<subscription-id>/resourceGroups/TestRG1",
  "location": "southcentralus",
  "managedBy": null,
  "name": "TestRG1",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

後でリソース グループを取得する必要がある場合は、次のコマンドを使用します。

```azurecli-interactive
az group show --name TestRG1
```

ご利用のサブスクリプションにあるすべてのリソース グループを取得するには、以下を使用します。

```azurecli-interactive
az group list
```

## <a name="add-resources-to-a-resource-group"></a>リソース グループへのリソースの追加
リソースをリソース グループに追加するには、**az resource create** コマンド、または作成するリソースの種類に固有のコマンド (**az storage account create** など) を使用します。 リソースの種類に固有のコマンドには、新しいリソースが必要とするプロパティのパラメーターが含まれているため、こちらを使用する方が簡単です。 **az resource create** を使用するには、設定するすべてのプロパティを把握しておく必要があります。これらを入力するよう求めるメッセージは表示されません。

ただし、スクリプトを使ってリソースを追加した場合、その新しいリソースは Resource Manager テンプレートに存在しないため、将来的に混乱が生じる可能性があります。 テンプレートを使用すると、ソリューションを繰り返し、かつ確実にデプロイすることができます。

次のコマンドは、ストレージ アカウントを作成します。 この例で示されている名前ではなく、ストレージ アカウントの一意の名前を指定してください。 名前の長さは 3 ～ 24 文字で、使用できるのは数字と小文字のみです。 この例で示されている名前は既に使用済みのため、その名前を使うと、エラーが発生します。

```azurecli-interactive
az storage account create -n myuniquestorage -g TestRG1 -l westus --sku Standard_LRS
```

後でこのリソースを取得する必要がある場合は、次のコマンドを使用します。

```azurecli-interactive
az storage account show --name myuniquestorage --resource-group TestRG1
```

## <a name="add-a-tag"></a>タグを追加します

タグを使用すると、さまざまなプロパティに基づいてリソースを整理できます。 たとえば、同じ部門に属しているさまざまなリソース グループに、複数のリソースが含まれていることがあります。 こうしたリソースに部門タグと値を適用して、同じカテゴリに属するものとしてマークできます。 また、運用環境で使用されているリソースか、テスト環境のリソースかをマークすることもできます。 このトピックでは、1 つのリソースにのみタグを適用しますが、環境内では、ほとんどの場合、すべてのリソースにタグを適用すると便利です。

次のコマンドは、2 つのタグをお使いのストレージ アカウントに適用します。

```azurecli-interactive
az resource tag --tags Dept=IT Environment=Test -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

タグは 1 つのオブジェクトとして更新されます。 タグが既に含まれているリソースにタグを追加するには、最初に既存のタグを取得します。 新しいタグを、既存のタグが含まれるオブジェクトに追加したら、すべてのタグをリソースに再度適用します。

```azurecli-interactive
jsonrtag=$(az resource show -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

## <a name="search-for-resources"></a>リソースの検索

**az resource list** コマンドを使用して、さまざまな検索条件でリソースを取得します。

* 名前をもとにリソースを取得するには、**name** パラメーターを指定します。

  ```azurecli-interactive
  az resource list -n myuniquestorage
  ```

* リソース グループのすべてのリソースを取得するには、**resource-group** パラメーターを指定します。

  ```azurecli-interactive
  az resource list --resource-group TestRG1
  ```

* タグ名と値ですべてのリソースを取得するには、**tag** パラメーターを指定します。

  ```azurecli-interactive
  az resource list --tag Dept=IT
  ```

* 特定のリソースの種類のリソースをすべて取得するには、**resource-type** パラメーターを指定します。

  ```azurecli-interactive
  az resource list --resource-type "Microsoft.Storage/storageAccounts"
  ```

## <a name="lock-a-resource"></a>リソースのロック

重要なリソースが誤って削除または変更されないようにするには、そのリソースにロックを適用します。 **CanNotDelete** または **ReadOnly** のいずれかを指定できます。

管理ロックを作成または削除するには、`Microsoft.Authorization/*` または `Microsoft.Authorization/locks/*` アクションにアクセスできる必要があります。 組み込みロールのうち、所有者とユーザー アクセス管理者にのみこれらのアクションが許可されています。

ロックを適用するには、次のコマンドを使用します。

```azurecli-interactive
az lock create --lock-type CanNotDelete --resource-name myuniquestorage --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --name storagelock
```

前の例でロックされたリソースは、ロックが解除されるまで削除できません。 ロックを解除するには、次を使用します。

```azurecli-interactive
az lock delete --name storagelock --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --resource-name myuniquestorage
```

ロックの詳細については、[Azure Resource Manager でのリソースのロック](resource-group-lock-resources.md)に関するページをご覧ください。

## <a name="remove-resources-or-resource-group"></a>リソースまたはリソース グループの削除
リソースまたはリソース グループを削除できます。 リソース グループを削除すると、そのリソース グループ内のリソースもすべて削除されます。

* リソース グループからリソースを削除するには、削除するリソースの種類に対して delete コマンドを使用します。 このコマンドはリソースを削除しますが、リソース グループは削除しません。

  ```azurecli-interactive
  az storage account delete -n myuniquestorage -g TestRG1
  ```

* リソース グループとそのすべてのリソースを削除するには、**az group delete** コマンドを使用します。

  ```azurecli-interactive
  az group delete -n TestRG1
  ```

どちらのコマンドについても、リソースまたはリソース グループを削除するかどうかを確認するメッセージが表示されます。

## <a name="next-steps"></a>次のステップ
* リソース マネージャーのテンプレートの作成の詳細については、[Azure リソース マネージャーのテンプレートの作成](resource-group-authoring-templates.md)に関するページを参照してください。
* テンプレートをデプロイする方法の詳細については、「[Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy-cli.md)」を参照してください。
* 新しいリソース グループに、既存のリソースを移動できます。 例については、「 [新しいリソース グループまたはサブスクリプションへのリソースの移動](resource-group-move-resources.md)」を参照してください。
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](resource-manager-subscription-governance.md)」を参照してください。
