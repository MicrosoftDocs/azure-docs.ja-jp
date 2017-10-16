---
title: "Azure クイック スタート - Azure CLI を使用したストレージ アカウントの作成 | Microsoft Docs"
description: "Azure CLI を使用して新しいストレージ アカウントを作成する方法をすばやく学習します。"
services: storage
documentationcenter: na
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/28/2017
ms.author: marsma
ms.openlocfilehash: b1fb2da4acf6e06219d790f2354cada4f1e34285
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-storage-account-using-the-azure-cli"></a>Azure CLI を使用してストレージ アカウントを作成する

Azure CLI は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。 このクイック スタートでは、Azure CLI を使用して Azure Storage アカウントを作成する方法について詳細に説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このクイック スタートを実施するには、Azure CLI バージョン 2.0.4 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。 

## <a name="create-resource-group"></a>Create resource group

[az group create](/cli/azure/group#create) コマンドで Azure リソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 この例では、*eastus* リージョン内に *myResourceGroup* という名前のリソース グループを作成します。

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus
```

`--location` パラメーターに指定するリージョンがわからない場合は、[az account list-locations](/cli/azure/account#list) コマンドで、サブスクリプションに対してサポートされているリージョンの一覧を取得できます。

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

## <a name="create-a-general-purpose-standard-storage-account"></a>汎用の標準ストレージ アカウントを作成する

それぞれ 1 つ以上のストレージ サービス (BLOB、ファイル、テーブル、またはキュー) をサポートする、さまざまな使用シナリオに適したいくつかの種類のストレージ アカウントが存在します。 次の表は、使用可能なストレージ アカウントの種類を詳細に示しています。

|**ストレージ アカウントの種類**|**汎用 (Standard)**|**汎用 (Premium)**|**Blob Storage (ホット アクセス レベルとクール アクセス レベル)**|
|-----|-----|-----|-----|
|**サポートされるサービス**| BLOB、ファイル、テーブル、キュー サービス | Blob service | Blob service|
|**サポートされる BLOB の種類**|ブロック BLOB、ページ BLOB、追加 BLOB | ページ blob | ブロック BLOB と追加 BLOB|

[az storage account create](/cli/azure/storage/account#create) コマンドで汎用の標準ストレージ アカウントを作成します。

```azurecli-interactive
az storage account create \
    --name mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --sku Standard_LRS \
    --encryption blob
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このクイック スタートで作成したストレージ アカウントも含め、リソース グループ内のどのリソースも必要なくなった場合は、[az group delete](/cli/azure/group#delete) コマンドでリソース グループを削除します。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、リソース グループと汎用の標準ストレージ アカウントを作成しました。 ストレージ アカウントとの間でデータを転送する方法を学習するには、Blob Storage のクイック スタートに進みます。

> [!div class="nextstepaction"]
> [Azure CLI を使用した Azure Blob Storage との間でのオブジェクトの転送](../blobs/storage-quickstart-blobs-cli.md)