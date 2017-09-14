---
title: "プライベート Docker コンテナー レジストリの作成 - Azure CLI | Microsoft Docs"
description: "Azure CLI 2.0 を使用したプライベート Docker コンテナー レジストリの作成と管理についての概要"
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: na
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: nepeters
ms.custom: na
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: c7cdb1b13bf32388d18c2a25af28337a81861c1e
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="create-a-managed-container-registry-using-the-azure-cli"></a>管理されたコンテナー レジストリを Azure CLI で作成する

Azure Container Registry は、プライベート Docker コンテナー イメージを保管するための管理された Docker コンテナー レジストリ サービスです。 このガイドでは、管理された Azure Container Registry インスタンスを Azure CLI で作成する方法について詳しく説明します。

管理された Azure Container Registry はプレビュー段階であり、一部のリージョンでは利用できません。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このクイック スタートを実施するには、Azure CLI バージョン 2.0.4 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#create) コマンドでリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 

次の例では、*myResourceGroup* という名前のリソース グループを場所 *westcentralus* に作成します。

```azurecli-interactive 
az group create --name myResourceGroup --location westcentralus
```

## <a name="create-a-container-registry"></a>コンテナー レジストリの作成

[az acr create](/cli/azure/acr#create) コマンドを使用して ACR インスタンスを作成します。

> [!NOTE]
> レジストリを作成する場合は、アルファベットと数字のみを含む、グローバルに一意のトップレベル ドメイン名を指定します。

 この例のレジストリの名前は *myContainerRegistry1* ですが、独自の一意の名前に置き換えてください。

```azurecli
az acr create --name myContainerRegistry1 --resource-group myResourceGroup --sku Managed_Standard
```

レジストリが作成されると、出力は次のようになります。

```azurecli
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-29T04:50:28.607134+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry1",
  "location": "westcentralus",
  "loginServer": "mycontainerregistry1.azurecr.io",
  "name": "myContainerRegistry1",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Managed_Standard",
    "tier": "Managed"
  },
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="log-in-to-acr-instance"></a>ACR インスタンスへのログイン

コンテナー イメージをプッシュしたりプルしたりするには、あらかじめ ACR インスタンスにログインしておく必要があります。 そのためには、[az acr login](/cli/azure/acr#login) コマンドを使用します。

```azurecli-interactive
az acr login --name myAzureContainerRegistry1
```

このコマンドは、完了すると "Login Succeeded (ログインに成功しました)" というメッセージを返します。

## <a name="use-azure-container-registry"></a>Azure Container Registry の使用

### <a name="list-container-images"></a>コンテナー イメージの一覧表示

`az acr` CLI コマンドを使用して、リポジトリ内のイメージとタグを照会します。

> [!NOTE]
> 現在、Container Registry では、`docker search` コマンドによるイメージとタグの照会をサポートしていません。

### <a name="list-repositories"></a>リポジトリの一覧を表示する

次の例では、JSON (JavaScript Object Notation) 形式でレジストリ内のリポジトリの一覧を表示します。

```azurecli
az acr repository list -n myContainerRegistry1 -o json
```

### <a name="list-tags"></a>タグの一覧を表示する

次の例では、JSON 形式で **samples/nginx** リポジトリのタグの一覧を表示します。

```azurecli
az acr repository show-tags -n myContainerRegistry1 --repository samples/nginx -o json
```

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、管理された Azure Container Registry インスタンスを Azure CLI で作成しました。

> [!div class="nextstepaction"]
> [Docker CLI を使用した最初のイメージのプッシュ](container-registry-get-started-docker-cli.md)

