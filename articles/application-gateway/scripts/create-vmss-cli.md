---
title: Azure CLI のサンプル スクリプト - Web トラフィックの管理 | Microsoft Docs
description: Azure CLI のサンプル スクリプト - アプリケーション ゲートウェイと仮想マシン スケール セットを使用して Web トラフィックを管理します。
services: application-gateway
documentationcenter: networking
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 99d0939b30d04fbd5c0eb7a287105bb4cf27e9f4
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56116764"
---
# <a name="manage-web-traffic-using-the-azure-cli"></a>Azure CLI を使用して Web トラフィックを管理する

このスクリプトでは、バックエンド サーバーのために仮想マシン スケール セットを使用するアプリケーション ゲートウェイを作成します。 その後で、アプリケーション ゲートウェイを構成して Web トラフィックを管理できます。 スクリプトを実行したら、パブリック IP アドレスを使用してアプリケーション ゲートウェイをテストできます。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/application-gateway/create-vmss/create-vmss.sh "Create application gateway")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、アプリケーション ゲートウェイ、すべての関連リソースを削除します。

```azurecli-interactive 
az group delete --name myResourceGroupAG --yes
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、以下のコマンドを実行してデプロイを作成します。 表内の各項目は、コマンドごとのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | すべてのリソースを格納するリソース グループを作成します。 |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) | 仮想ネットワークを作成します。 |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) | 仮想ネットワーク内にサブネットを作成します。 |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest) | アプリケーション ゲートウェイのパブリック IP アドレスを作成します。 |
| [az network application-gateway create](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest) | アプリケーション ゲートウェイを作成します。 |
| [az vmss create](https://docs.microsoft.com/cli/azure/vmss) | 仮想マシン スケール セットを作成します。 |
| [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip) | アプリケーション ゲートウェイのパブリック IP アドレスを取得します。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他のアプリケーション ゲートウェイ用の CLI サンプル スクリプトは、[Azure Windows VM のドキュメント](../cli-samples.md)のページにあります。
