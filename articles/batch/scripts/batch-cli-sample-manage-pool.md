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
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/20/2017
ms.author: antisch
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 7cec2a8c66868c601c38dc2dea61dda38d3cd3b2
ms.lasthandoff: 03/24/2017

---

# <a name="managing-azure-batch-pools-with-azure-cli"></a>Azure CLI を使用した Azure Batch プールの管理

これらのスクリプトでは、Azure Batch サービスでコンピューティング ノードのプールを作成して管理するために、Azure CLI で使用できるツールの一部を示します。

これらのスクリプトの実行では、Batch アカウントが既に設定されており、プールとアプリケーションが構成されていることを前提としています。 詳細については、これらの各トピックに関連した[サンプル スクリプト](../batch-cli-samples.md)を参照してください。

> [!NOTE]
> このサンプルのコマンドでは、Azure Virtual Machines を作成します。 Vm を実行すると、ご自分のアカウントへの課金が発生します。 この料金を最小限に抑えるには、サンプルの実行が完了したら VM を削除してください。 「[プールのクリーンアップ](#clean-up-pools)」を参照してください。

必要に応じて、[Azure CLI インストール ガイド](https://docs.microsoft.com/cli/azure/install-azure-cli)に関するページの手順に従って Azure CLI をインストールし、`az login` を実行して Azure にログインします。

Batch プールは、クラウド サービスの構成 (Windows のみ)、または仮想マシンの構成 (Windows および Linux) という 2 つの方法を使用して構成できます。

## <a name="pool-with-cloud-service-configuration-sample-script"></a>クラウド サービス構成サンプル スクリプトによるプール

[!code-azurecli[メイン](../../../cli_scripts/batch/manage-pool/manage-pool-windows.sh "クラウド サービスのプールを管理する")]

## <a name="pool-with-virtual-machine-configuration-sample-script"></a>仮想マシン構成サンプル スクリプトによるプール

[!code-azurecli[メイン](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "仮想マシンのプールを管理する")]

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
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#login) | Batch アカウントに対して認証します。  |
| [az batch application summary list](https://docs.microsoft.com/cli/azure/batch/application/summary#list) | Batch アカウントで使用できるアプリケーションを一覧表示します。  |
| [az batch pool create](https://docs.microsoft.com/cli/azure/batch/pool#create) | VM のプールを作成します。  |
| [az batch pool set](https://docs.microsoft.com/cli/azure/batch/pool#set) | プールのプロパティを更新します。  |
| [az batch pool node-agent-skus list](https://docs.microsoft.com/cli/azure/batch/pool/node-agent-skus#list) | 使用できるノード エージェント SKU とイメージの情報を一覧表示します。  |
| [az batch pool resize](https://docs.microsoft.com/cli/azure/batch/pool#resize) | 指定したプールで実行されている VM の数を変更します。  |
| [az batch pool show](https://docs.microsoft.com/cli/azure/batch/pool#show) | プールのプロパティを表示します。  |
| [az batch pool delete](https://docs.microsoft.com/cli/azure/batch/pool#delete) | 指定したプールを削除します。  |
| [az batch pool autoscale enable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#enable) | プールで自動スケーリングを有効にし、式を適用します。  |
| [az batch pool autoscale disable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#disable) | プールで自動スケーリングを無効にします。  |
| [az batch node list](https://docs.microsoft.com/cli/azure/batch/node#list) | 指定したプール内のすべてのコンピューティング ノードを一覧表示します。  |
| [az batch node reboot](https://docs.microsoft.com/cli/azure/batch/node#reboot) | 指定したコンピューティング ノードを再起動します。  |
| [az batch node delete](https://docs.microsoft.com/cli/azure/batch/node#delete) | 指定したプールから、一覧表示されているノードを削除します。  |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の Batch CLI サンプル スクリプトは、[Azure Batch CLI のドキュメント](../batch-cli-samples.md)のページにあります。


