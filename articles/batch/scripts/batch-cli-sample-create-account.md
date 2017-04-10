---
title: "Azure CLI のサンプル スクリプト - Batch アカウントの作成 | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - Batch アカウントの作成"
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/20/2017
ms.author: antisch
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 4d37dfc6c7110aa32788bbd3c3f81cd84a1426d2
ms.lasthandoff: 03/24/2017

---

# <a name="create-a-batch-account-with-the-azure-cli"></a>Azure CLI で Batch アカウントを作成する

このスクリプトは、Azure Batch アカウントを作成し、アカウントのさまざまなプロパティを照会および更新する方法を示します。

必要に応じて、[Azure CLI インストール ガイド](https://docs.microsoft.com/cli/azure/install-azure-cli)に関するページの手順に従って Azure CLI をインストールし、`az login` を実行して Azure にログインします。

## <a name="batch-account-sample-script"></a>Batch アカウントのサンプル スクリプト

Batch アカウントを作成するときに、既定で、Batch サービスによって内部的にコンピューティング ノードが割り当てられます。 割り当てられるコンピューティング ノードは個々のコア クォータによって変わります。また、アカウントは、共有キー資格情報または Azure Active Dirctory トークンによって認証できます。

[!code-azurecli[メイン](../../../cli_scripts/batch/create-account/create-account.sh "アカウントを作成する")]

## <a name="batch-account-using-user-subscription-sample-script"></a>ユーザー サブスクリプションのサンプル スクリプトを使用した Batch アカウント

Batch で Azure サブスクリプション内にコンピューティング ノードを作成することもできます。
サブスクリプションにコンピューティング ノードを割り当てるアカウントは、Azure Active Directory トークンを使用して認証される必要があります。また、割り当てられるコンピューティング ノードは、サブスクリプション クォータに加算されます。 このモードでアカウントを作成するには、アカウントの作成時に Key Vault 参照を指定する必要があります。

[!code-azurecli[メイン](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh  "ユーザー サブスクリプションを使用してアカウントを作成する")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

上記のサンプル スクリプトのいずれかを実行した後は、次のコマンドを実行して、リソース グループと、関連するすべてのリソース (Batch アカウント、Azure ストレージ アカウント、Azure Key Vault など) を削除してください。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、Batch アカウント、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az batch account create](https://docs.microsoft.com/cli/azure/batch/account#create) | Batch アカウントを作成します。  |
| [az batch account set](https://docs.microsoft.com/cli/azure/batch/account#set) | Batch アカウントのプロパティを更新します。  |
| [az batch account show](https://docs.microsoft.com/cli/azure/batch/account#show) | 指定された Batch アカウントの詳細を取得します。  |
| [az batch account keys list](https://docs.microsoft.com/cli/azure/batch/account/keys#list) | 指定された Batch アカウントのアクセス キーを取得します。  |
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#login) | さらに CLI と対話できるように、指定された Batch アカウントを認証します。  |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#create) | ストレージ アカウントを作成します。 |
| [az keyvault create](https://docs.microsoft.com/cli/azure/keyvault#create) | Key Vault を作成します。 |
| [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault#set-policy) | 指定した Key Vault のセキュリティ ポリシーを更新します。 |
| [az group delete](https://docs.microsoft.com/cli/azure/group#delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の Batch CLI サンプル スクリプトは、[Azure Batch CLI のドキュメント](../batch-cli-samples.md)のページにあります。

