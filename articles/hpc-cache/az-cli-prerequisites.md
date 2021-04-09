---
title: Azure HPC Cache の Azure CLI の前提条件
description: Azure CLI を使用して Azure HPC Cache を作成または変更する前のセットアップ手順
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 13f45c96a830110bd0f4a2d4a2b422921d7a2e31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94654458"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Azure HPC Cache 向けに Azure CLI を設定する

Azure CLI を使用して Azure HPC Cache を作成または管理する前に、次の手順に従って環境を準備します。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Azure HPC Cache には、Azure CLI のバージョン 2.7 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="set-default-resource-group-optional"></a>既定のリソース グループを設定する (省略可能)

ほとんどの hpc-cache コマンドでは、キャッシュのリソース グループを渡す必要があります。 既定のリソース グループを設定するには、[az configure](/cli/azure/reference-index#az-configure) を使用します。

## <a name="next-steps"></a>次のステップ

Azure CLI 拡張機能をインストールしてログインすると、Azure CLI を使用して Azure HPC Cache システムを作成および管理できます。

* [Azure HPC キャッシュを作成する](hpc-cache-create.md)
* [Azure CLI hpc-cache のドキュメント](/cli/azure/ext/hpc-cache/hpc-cache)
