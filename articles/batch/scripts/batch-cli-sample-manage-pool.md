---
title: "Azure CLI のサンプル スクリプト - Batch でのプールの管理 |Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - Batch でのプールの管理"
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
ms.openlocfilehash: ae7eab97c1da1113b0248b74a9dd67de8ce49e36
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="managing-azure-batch-pools-with-azure-cli"></a>Azure CLI を使用した Azure Batch プールの管理

これらのスクリプトでは、Azure Batch サービスでコンピューティング ノードのプールを作成して管理するために、Azure CLI で使用できるツールの一部を示します。

> [!NOTE]
> このサンプルのコマンドでは、Azure Virtual Machines を作成します。 VM を実行すると、ご自分のアカウントへの課金が発生します。 この料金を最小限に抑えるには、サンプルの実行が完了したら VM を削除してください。 「[プールのクリーンアップ](#clean-up-pools)」を参照してください。

Batch プールは、クラウド サービスの構成 (Windows のみ)、または仮想マシンの構成 (Windows および Linux) という 2 つの方法を使用して構成できます。 以下のサンプル スクリプトでは、両方の構成でプールを作成する方法を説明します。

## <a name="prerequisites"></a>前提条件

- Azure CLI をまだインストールしていない場合は、[Azure CLI インストール ガイド](https://docs.microsoft.com/cli/azure/install-azure-cli)の手順に従ってインストールします。
- Batch アカウントをまだお持ちでない場合は、作成します。 アカウント作成のサンプル スクリプトについては、「[Azure CLI で Batch アカウントを作成する](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-create-account)」を参照してください。
- 開始タスクからアプリケーションを実行するように構成していない場合は、そのように構成します。 アプリケーションを作成し、アプリケーション パッケージを Azure にアップロードするサンプル スクリプトについては、「[Azure CLI を使用した Azure Batch へのアプリケーションの追加](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-add-application)」を参照してください。

## <a name="pool-with-cloud-service-configuration-sample-script"></a>クラウド サービス構成サンプル スクリプトによるプール

[!code-azurecli[main](../../../cli_scripts/batch/manage-pool/manage-pool-windows.sh "Manage Cloud Services Pools")]

## <a name="pool-with-virtual-machine-configuration-sample-script"></a>仮想マシン構成サンプル スクリプトによるプール

[!code-azurecli[main](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "Manage Virtual Machine Pools")]

## <a name="clean-up-pools"></a>プールのクリーンアップ

上記のサンプル スクリプトを実行した後は、次のコマンドを実行してプールを削除してください。
```azurecli
az batch pool delete --pool-id mypool-windows
az batch pool delete --pool-id mypool-linux
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、以下のコマンドを使用して Batch プールを作成および操作します。
表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login) | Batch アカウントに対して認証します。  |
| [az batch application summary list](https://docs.microsoft.com/cli/azure/batch/application/summary#az_batch_application_summary_list) | Batch アカウントで使用できるアプリケーションを一覧表示します。  |
| [az batch pool create](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_create) | VM のプールを作成します。  |
| [az batch pool set](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_set) | プールのプロパティを更新します。  |
| [az batch pool node-agent-skus list](https://docs.microsoft.com/cli/azure/batch/pool/node-agent-skus#az_batch_pool_node_agent_skus_list) | 使用できるノード エージェント SKU とイメージの情報を一覧表示します。  |
| [az batch pool resize](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_resize) | 指定したプールで実行されている VM の数を変更します。  |
| [az batch pool show](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_show) | プールのプロパティを表示します。  |
| [az batch pool delete](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_delete) | 指定したプールを削除します。  |
| [az batch pool autoscale enable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_enable) | プールで自動スケーリングを有効にし、式を適用します。  |
| [az batch pool autoscale disable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_disable) | プールで自動スケーリングを無効にします。  |
| [az batch node list](https://docs.microsoft.com/cli/azure/batch/node#az_batch_node_list) | 指定したプール内のすべてのコンピューティング ノードを一覧表示します。  |
| [az batch node reboot](https://docs.microsoft.com/cli/azure/batch/node#az_batch_node_reboot) | 指定したコンピューティング ノードを再起動します。  |
| [az batch node delete](https://docs.microsoft.com/cli/azure/batch/node#az_batch_node_delete) | 指定したプールから、一覧表示されているノードを削除します。  |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の Batch CLI サンプル スクリプトは、[Azure Batch CLI のドキュメント](../batch-cli-samples.md)のページにあります。

