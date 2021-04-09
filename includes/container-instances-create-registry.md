---
title: インクルード ファイル
description: インクルード ファイル
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 08/13/2020
ms.author: danlep
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 173c9156f253e43111299b53287e97ab7b2c0aa5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92746958"
---
## <a name="create-azure-container-registry"></a>Azure Container Registry の作成

コンテナー レジストリを作成する前に、そのデプロイ先となる *リソース グループ* が必要です。 リソース グループは、Azure リソースをまとめてデプロイして管理するための論理上のコレクションです。

[az group create][az-group-create] コマンドを使用して、リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループが *eastus* リージョンに作成されます。

```azurecli
az group create --name myResourceGroup --location eastus
```

リソース グループを作成したら、[az acr create][az-acr-create] コマンドを使用して Azure コンテナー レジストリを作成します。 コンテナー レジストリ名は、Azure 内で一意にする必要があります。また、5 ～ 50 文字の英数字を含める必要があります。 `<acrName>` を、レジストリの一意の名前に置き換えます。

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

*mycontainerregistry082* という名前の新しい Azure Container Registry の出力の一部を次に示します。

```output
{
  "creationDate": "2020-07-16T21:54:47.297875+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry082",
  "location": "eastus",
  "loginServer": "mycontainerregistry082.azurecr.io",
  "name": "mycontainerregistry082",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

このチュートリアルの残りの部分では、この手順で選択したコンテナー レジストリ名のプレースホルダーとして `<acrName>` を使用します。

## <a name="log-in-to-container-registry"></a>コンテナー レジストへのにログイン

イメージをプッシュする前に、Azure Container Registry インスタンスにログインする必要があります。 [az acr login][az-acr-login] コマンドを使用して、操作を完了します。 コンテナー レジストリの作成時に選んだ一意名を指定する必要があります。

```azurecli
az acr login --name <acrName>
```

次に例を示します。

```azurecli
az acr login --name mycontainerregistry082
```

このコマンドは、完了すると `Login Succeeded` を返します。

```output
Login Succeeded
```

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-group-create]: /cli/azure/group#az-group-create
