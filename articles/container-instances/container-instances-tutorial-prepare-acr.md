---
title: "Azure Container Instances チュートリアル - Azure Container Registry の準備 | Microsoft Docs"
description: "Azure Container Instances チュートリアル - Azure Container Registry の準備"
services: container-instances
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, コンテナー, マイクロサービス, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: seanmck
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: cc96ba9f5abd45a7503ba3327b30e1f809391384
ms.contentlocale: ja-jp
ms.lasthandoff: 08/28/2017

---

# <a name="deploy-and-use-azure-container-registry"></a>Azure Container Registry をデプロイして使用する

これは 3 つのパートで構成されるチュートリアルの 2 番目のタスクです。 [前のステップ](./container-instances-tutorial-prepare-app.md)では、[Node.js](http://nodejs.org) で記述されたシンプルな Web アプリケーションに対して、コンテナー イメージが作成されました。 このチュートリアルでは、このイメージを Azure Container Registry にプッシュします。 コンテナー イメージを作成していない場合、[チュートリアル 1 - コンテナー イメージの作成](./container-instances-tutorial-prepare-app.md)に関するページに戻ってください。 

Azure Container Registry は、Docker コンテナー イメージ用の Azure ベースのプライベート レジストリです。 このチュートリアルでは、Azure Container Registry インスタンスのデプロイ、およびこのインスタンスへのコンテナー イメージのプッシュについて説明します。 手順は次のとおりです。

> [!div class="checklist"]
> * Azure Container Registry インスタンスのデプロイ
> * Azure Container Registry のコンテナー イメージのタグ付け
> * Azure Container Registry へのイメージのアップロード

以降のチュートリアルでは、コンテナーをプライベート レジストリから Azure Container Instances にデプロイします。

## <a name="before-you-begin"></a>開始する前に

このチュートリアルでは、Azure CLI バージョン 2.0.4 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。

## <a name="deploy-azure-container-registry"></a>Azure Container Registry のデプロイ

Azure Container Registry をデプロイする場合、まず、リソース グループが必要です。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コレクションです。

[az group create](/cli/azure/group#create) コマンドでリソース グループを作成します。 この例では、*myResourceGroup* という名前のリソース グループが *eastus* リージョンに作成されます。

```azurecli
az group create --name myResourceGroup --location eastus
```

[az acr create](/cli/azure/acr#create) コマンドで Azure Container Registry を作成します。 コンテナー レジストリの名前は**一意でなければなりません**。 次の例では、*mycontainerregistry082* という名前を使用します。

```azurecli
az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
```

このチュートリアルの残りの部分では、選択したコンテナー レジストリ名のプレースホルダーとして `<acrname>` を使用します。

## <a name="container-registry-login"></a>Container Registry のログイン

イメージをプッシュする前に、ACR のインスタンスにログインする必要があります。 [az acr login](https://docs.microsoft.com/en-us/cli/azure/acr#login) コマンドを使用して、操作を完了します。 コンテナー レジストリの作成時に割り当てられた一意の名前が必要です。

```azurecli
az acr login --name <acrName>
```

このコマンドは、完了すると 'Login Succeeded’(ログインに成功しました) というメッセージを返します。

## <a name="tag-container-image"></a>コンテナー イメージのタグ付け

コンテナー イメージをプライベート レジストリからデプロイするには、レジストリの `loginServer` 名でイメージにタグを付ける必要があります。

現在のイメージの一覧を表示するには、`docker images` コマンドを使用します。

```bash
docker images
```

出力:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

loginServer 名を取得するには、次のコマンドを実行します。

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

コンテナー レジストリの loginServer で *aci-tutorial-app* イメージにタグを付けます。 また、イメージ名の末尾に `:v1` を付加します。 このタグは、イメージのバージョン番号を示します。

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

タグ付けが完了したら、`docker images` を実行して操作を確認します。

```bash
docker images
```

出力:

```bash
REPOSITORY                                                TAG                 IMAGE ID            CREATED             SIZE
aci-tutorial-app                                          latest              5c745774dfa9        39 seconds ago      68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app        v1                  a9dace4e1a17        7 minutes ago       68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Azure Container Registry へのイメージのプッシュ

*aci-tutorial-app* イメージをレジストリにプッシュします。

次の例を使用して、コンテナー レジストリ loginServer 名を環境の loginServer に置き換えます。

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

## <a name="list-images-in-azure-container-registry"></a>Azure Container Registry のイメージの一覧表示

Azure Container Registry にプッシュされたイメージの一覧を返すには、[az acr repository list](/cli/azure/acr/repository#list) コマンドを使用します。 コンテナー レジストリ名でコマンドを更新します。

```azurecli
az acr repository list --name <acrName> --output table
```

出力:

```azurecli
Result
----------------
aci-tutorial-app
```

次に特定のイメージのタグを表示するには、[az acr repository show-tags](/cli/azure/acr/repository#show-tags) コマンドを使用します。

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

出力:

```azurecli
Result
--------
v1
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Container Registry が Azure Container Instances で使用できるように準備され、コンテナー イメージがプッシュされました。 次の手順を完了しました。

> [!div class="checklist"]
> * Azure Container Registry インスタンスのデプロイ
> * Azure Container Registry のコンテナー イメージのタグ付け
> * Azure Container Registry へのイメージのアップロード

次のチュートリアルでは、Azure Container Instances を使用してコンテナーを Azure にデプロイする方法について学習します。

> [!div class="nextstepaction"]
> [コンテナーを Azure Container Instances にデプロイする](./container-instances-tutorial-deploy-app.md)

