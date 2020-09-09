---
title: Azure CLI のサンプル スクリプト - ストレージ アカウント アクセス キーのローテーション | Microsoft Docs
description: Azure Storage アカウントを作成し、そのアカウント アクセス キーを取得してローテーションします。
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: cli
ms.topic: sample
ms.date: 06/22/2017
ms.author: tamram
ms.custom: devx-track-azurecli
ms.openlocfilehash: bfda6c5315e11a4a924b82dc3eacdf692357827b
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87503686"
---
# <a name="create-a-storage-account-and-rotate-its-account-access-keys"></a>ストレージ アカウントを作成してアカウント アクセス キーのローテーションを行う

このスクリプトは、Azure Storage アカウントを作成し、新しいストレージ アカウントのアクセス キーを表示した後、キーの更新 (ローテーション) を行います。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.sh "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを実行して、リソース グループ、ストレージ アカウント、すべての関連リソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使って、ストレージ アカウントを作成し、そのアクセス キーを取得してローテーションを行います。 表内の各項目は、コマンドごとのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az group create](/cli/azure/group) | すべてのリソースを格納するリソース グループを作成します。 |
| [az storage account create](/cli/azure/storage/account) | 特定のリソース グループに Azure Storage アカウントを作成します。 |
| [az storage account keys list](/cli/azure/storage/account/keys) | 指定されたアカウントのストレージ アカウント アクセス キーを表示します。 |
| [az storage account keys renew](/cli/azure/storage/account/keys) | プライマリまたはセカンダリのストレージ アカウント アクセス キーを再生成します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他のストレージ CLI サンプル スクリプトは、[Azure Blob ストレージ用の Azure CLI サンプル](../blobs/storage-samples-blobs-cli.md)のページにあります。
