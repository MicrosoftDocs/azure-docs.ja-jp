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
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: antisch
ms.openlocfilehash: fd2f4682a04c557b69bbfce115f41c54a96d462c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-batch-account-with-the-azure-cli"></a>Azure CLI で Batch アカウントを作成する

このスクリプトは、Azure Batch アカウントを作成し、アカウントのさまざまなプロパティを照会および更新する方法を示します。

## <a name="prerequisites"></a>前提条件

Azure CLI をまだインストールしていない場合は、[Azure CLI インストール ガイド](https://docs.microsoft.com/cli/azure/install-azure-cli)の手順に従ってインストールします。

## <a name="batch-account-sample-script"></a>Batch アカウントのサンプル スクリプト

Batch アカウントを作成するときに、既定で、Batch サービスによって内部的にコンピューティング ノードが割り当てられます。 割り当てられるコンピューティング ノードは個々のコア クォータによって変わります。また、アカウントは、共有キー資格情報または Azure Active Dirctory トークンによって認証できます。

[!code-azurecli[main](../../../cli_scripts/batch/create-account/create-account.sh "Create Account")]

## <a name="batch-account-using-user-subscription-sample-script"></a>ユーザー サブスクリプションのサンプル スクリプトを使用した Batch アカウント

Batch で Azure サブスクリプション内にコンピューティング ノードを作成することもできます。
サブスクリプションにコンピューティング ノードを割り当てるアカウントは、Azure Active Directory トークンを使用して認証される必要があります。また、割り当てられるコンピューティング ノードは、サブスクリプション クォータに加算されます。 このモードでアカウントを作成するには、アカウントの作成時に Key Vault 参照を指定する必要があります。

[!code-azurecli[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh  "Create Account using User Subscription")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

上記のサンプル スクリプトのいずれかを実行した後は、次のコマンドを実行して、リソース グループと、関連するすべてのリソース (Batch アカウント、Azure ストレージ アカウント、Azure Key Vault など) を削除してください。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、Batch アカウント、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az batch account create](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_create) | Batch アカウントを作成します。  |
| [az batch account set](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_set) | Batch アカウントのプロパティを更新します。  |
| [az batch account show](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_show) | 指定された Batch アカウントの詳細を取得します。  |
| [az batch account keys list](https://docs.microsoft.com/cli/azure/batch/account/keys#az_batch_account_keys_list) | 指定された Batch アカウントのアクセス キーを取得します。  |
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login) | さらに CLI と対話できるように、指定された Batch アカウントを認証します。  |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_create) | ストレージ アカウントを作成します。 |
| [az keyvault create](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_create) | Key Vault を作成します。 |
| [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_set_policy) | 指定した Key Vault のセキュリティ ポリシーを更新します。 |
| [az group delete](https://docs.microsoft.com/cli/azure/group#az_group_delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の Batch CLI サンプル スクリプトは、[Azure Batch CLI のドキュメント](../batch-cli-samples.md)のページにあります。
