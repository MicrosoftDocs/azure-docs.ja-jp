---
title: Azure CLI サンプル スクリプト - Traffic Manager を使用してアプリを世界規模でスケーリングする | Microsoft Docs
description: Azure CLI のサンプル スクリプト - 高可用性アーキテクチャの App Service アプリを世界規模でスケーリングする
services: appservice
documentationcenter: appservice
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: e4033a50-0e05-4505-8ce8-c876204b2acc
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 3165904eb7757b43d35a4fa3051d29d10bce70a6
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53632910"
---
# <a name="scale-an-app-service-app-worldwide-with-a-high-availability-architecture-using-azure-cli"></a>Azure CLI を使用して高可用性アーキテクチャの App Service アプリを世界規模でスケーリングする

このサンプル スクリプトでは、1 つのリソース グループ、2 つの App Service プラン、2 つのアプリ、1 つの Traffic Manager プロファイル、および 2 つの Traffic Manager エンドポイントを作成します。 この手順を実施すると、アプリを最も短いネットワーク待ち時間でグローバルに利用できる高可用性アーキテクチャが完成します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合は、Azure CLI バージョン 2.0 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/scale-geographic/scale-geographic.sh "Geographic Scale")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、App Service アプリ、Traffic Manager プロファイル、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | App Service プランを作成します。 |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | App Service アプリを作成します。 |
| [`az network traffic-manager profile create`](/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-create) | Azure Traffic Manager プロファイルを作成します。 |
| [`az network traffic-manager endpoint create`](/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-create) | Azure Traffic Manager プロファイルにエンドポイントを追加します。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

その他の App Service の CLI サンプル スクリプトは、[Azure App Service のドキュメント](../samples-cli.md)のページにあります。
