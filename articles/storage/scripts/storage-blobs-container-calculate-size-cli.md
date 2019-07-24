---
title: Azure CLI のサンプル スクリプト - BLOB コンテナーのサイズを計算する | Microsoft Docs
description: コンテナー内のブロブのサイズを合計して、Azure Blob ストレージ内のコンテナーのサイズを計算します。
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/28/2017
ms.author: tamram
ms.openlocfilehash: 3cb1e35617a58fcde7968ab45d437d865c91f983
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2019
ms.locfileid: "55696934"
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Blob ストレージ コンテナーのサイズを計算する

このスクリプトでは、コンテナー内のブロブのサイズを合計して、Azure Blob ストレージ内のコンテナーのサイズを計算します。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> この CLI スクリプトは、コンテナーの推定サイズを算出しますが、課金の計算には使用できません。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/storage/calculate-container-size/calculate-container-size.sh?highlight=2-3 "Calculate container size")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、コンテナー、すべての関連リソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは次のコマンドを使用して、Blob ストレージ コンテナーのサイズを計算します。 表内の各項目は、コマンドごとのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](/cli/azure/group) | すべてのリソースを格納するリソース グループを作成します。 |
| [az storage blob upload](/cli/azure/storage/account) | ローカル ファイルを Azure Blob ストレージ コンテナーにアップロードします。 |
| [az storage blob list](/cli/azure/storage/account/keys) | Azure Blob ストレージ コンテナー内の BLOB を一覧表示します。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他のストレージ CLI サンプル スクリプトは、[Azure Blob ストレージ用の Azure CLI サンプル](../blobs/storage-samples-blobs-cli.md)のページにあります。
