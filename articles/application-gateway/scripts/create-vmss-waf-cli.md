---
title: Azure CLI スクリプト サンプル - Web トラフィックの制限 | Microsoft Docs
description: Azure CLI スクリプトのサンプル - OWASP 規則を使用してトラフィックを制限する Web アプリケーション ファイアウォールと仮想マシン スケール セットがあるアプリケーション ゲートウェイを作成します。
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
ms.openlocfilehash: 68c0b1c01d79f591b5023d2e048d1788a3eaffd4
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56117376"
---
# <a name="restrict-web-traffic-using-the-azure-cli"></a>Azure CLI を使用して Web トラフィックを制限する

このスクリプトでは、バックエンド サーバーに仮想マシン スケール セットを使用する Web アプリケーション ファイアウォールのあるアプリケーション ゲートウェイを作成します。 Web アプリケーション ファイアウォールは、OWASP 規則に基づいて Web トラフィックを制限します。 スクリプトを実行したら、パブリック IP アドレスを使用してアプリケーション ゲートウェイをテストできます。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/application-gateway/create-vmss/create-vmss-waf.sh "Create application gateway")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、アプリケーション ゲートウェイ、すべての関連リソースを削除します。

```azurecli-interactive 
az group delete --name myResourceGroupAG --yes
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、以下のコマンドを実行してデプロイを作成します。 表内の各項目は、コマンドごとのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) | 仮想ネットワークを作成します。 |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) | 仮想ネットワーク内にサブネットを作成します。 |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest) | アプリケーション ゲートウェイのパブリック IP アドレスを作成します。 |
| [az network application-gateway create](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest) | アプリケーション ゲートウェイを作成します。 |
| [az vmss create](https://docs.microsoft.com/cli/azure/vmss#az-vmss-create) | 仮想マシン スケール セットを作成します。 |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | ストレージ アカウントを作成します。 |
| [az monitor diagnostic-settings create](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) | ストレージ アカウントを作成します。 |
| [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-show) | アプリケーション ゲートウェイのパブリック IP アドレスを取得します。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

追加のアプリケーション ゲートウェイの CLI スクリプトのサンプルは、[Azure Application Gateway のドキュメント](../cli-samples.md)にあります。
