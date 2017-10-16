---
title: "Azure Service Fabric のコンテナー イメージを作成する | Microsoft Docs"
description: "複数コンテナーの Service Fabric アプリケーションのコンテナー イメージを作成する方法を説明します。"
services: service-fabric
documentationcenter: 
author: suhuruli
manager: timlt
editor: suhuruli
tags: servicefabric
keywords: "Docker, コンテナー, マイクロサービス, Service Fabric, Azure"
ms.assetid: 
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 08b3cc4a52c09ee03818b563794ef9b009d12ef4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-container-images-for-service-fabric"></a>Service Fabric のコンテナー イメージを作成する

このチュートリアルは、Linux Service Fabric クラスター内のコンテナーの使い方を実演するるチュートリアル シリーズの第 1 部です。 このチュートリアルでは、複数コンテナーのアプリケーションを Service Fabric で使うことができるように準備します。 以降のチュートリアルでは、これらのイメージを Service Fabric アプリケーションの一部として使います。 このチュートリアルで学習する内容は次のとおりです。 

> [!div class="checklist"]
> * GitHub からアプリケーション ソースを複製する  
> * アプリケーション ソースからコンテナー イメージを作成する
> * Azure Container Registry (ACR) インスタンスをデプロイする
> * ACR のコンテナー イメージにタグを付ける
> * イメージを ACR にアップロードする

このチュートリアル シリーズで学習する内容は次のとおりです。 

> [!div class="checklist"]
> * Service Fabric のコンテナー イメージを作成する
> * [コンテナーで Service Fabric アプリケーションをビルドして実行する](service-fabric-tutorial-package-containers.md)
> * [Service Fabric でのフェールオーバーとスケーリングの処理方法](service-fabric-tutorial-containers-failover.md)

## <a name="prerequisites"></a>前提条件

- Service Fabric 用に設定された Linux 開発環境。 [こちら](service-fabric-get-started-linux.md)の説明に従って、Linux 環境を設定します。 
- このチュートリアルでは、Azure CLI バージョン 2.0.4 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 
- さらに、使用可能な Azure サブスクリプションを持っている必要があります。 無料試用版について詳しくは、[こちら](https://azure.microsoft.com/free/)をご覧ください。

## <a name="get-application-code"></a>アプリケーションのコードを入手する

このチュートリアルで使うサンプル アプリケーションは投票アプリです。 アプリケーションは、フロントエンド Web コンポーネントとバックエンド Redis インスタンスで構成されています。 コンポーネントは、コンテナー イメージにパッケージ化されています。 

アプリケーションのコピーを開発環境にダウンロードするには、git を使います。

```bash
git clone https://github.com/Azure-Samples/service-fabric-dotnet-containers.git

cd service-fabric-dotnet-containers/Linux/container-tutorial/
```

"container-tutorial" ディレクトリの下には、"azure-vote" という名前のフォルダーがあります。 "azure-vote" フォルダーには、フロントエンドのソース コードと、フロントエンドをビルドするための Dockerfile が含まれています。 "container-tutorial" ディレクトリには "redis" ディレクトリも含まれ、このディレクトリには redis イメージをビルドするための Dockerfile があります。 これらのディレクトリには、このチュートリアル セットに必要な資産が含まれています。 

## <a name="create-container-images"></a>コンテナー イメージを作成する

"azure-vote" ディレクトリ内で次のコマンドを実行して、フロントエンド Web コンポーネントのイメージをビルドします。 このコマンドは、このディレクトリ内の Dockerfile を使ってイメージをビルドします。 

```bash
docker build -t azure-vote-front .
```

"redis" ディレクトリ内で次のコマンドを実行して、redis バックエンドのイメージをビルドします。 このコマンドは、ディレクトリ内の Dockerfile を使ってイメージをビルドします。 

```bash
docker build -t azure-vote-back .
```

完了したら、[docker images](https://docs.docker.com/engine/reference/commandline/images/) コマンドを使って、作成されたイメージを確認します。

```bash
docker images
```

4 つのイメージがダウンロードまたは作成されたことに注目してください。 *azure-vote-front* イメージにアプリケーションが含まれています。 これは、Docker Hub の *python* イメージから派生されたものです。 Redis イメージは、Docker Hub からダウンロードされました。

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-back              latest              bf9a858a9269        3 seconds ago        107MB
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
redis                        latest              9813a7e8fcc0        2 days ago           107MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB

```

## <a name="deploy-azure-container-registry"></a>Azure Container Registry のデプロイ

最初に、[az login](/cli/azure/login) コマンドを実行して Azure アカウントにログインします。 

次に、[az account](/cli/azure/account#set) コマンドを使って、Azure Container Registry を作成するためのサブスクリプションを選びます。 

```bash
az account set --subscription <subscription_id>
```

Azure Container Registry をデプロイする場合、まず、リソース グループが必要です。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[az group create](/cli/azure/group#create) コマンドでリソース グループを作成します。 この例では、*myResourceGroup* という名前のリソース グループが *westus* リージョンに作成されます。 ユーザーの近くにあるリージョンのリソース グループを選んでください。 

```bash
az group create --name myResourceGroup --location westus
```

[az acr create](/cli/azure/acr#create) コマンドで Azure Container Registry を作成します。 コンテナー レジストリの名前は**一意でなければなりません**。

```bash
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic --admin-enabled true
```

このチュートリアルの残りの部分では、選択したコンテナー レジストリ名のプレースホルダーとして「acrname」を使用します。

## <a name="log-in-to-your-container-registry"></a>コンテナー レジストリにログインする

イメージをプッシュする前に、ACR のインスタンスにログインします。 [az acr login](/cli/azure/acr?view=azure-cli-latest#az_acr_login) コマンドを使用して、操作を完了します。 コンテナー レジストリの作成時に割り当てられた一意名を入力します。

```bash
az acr login --name <acrName>
```

このコマンドは、完了すると 'Login Succeeded’(ログインに成功しました) というメッセージを返します。

## <a name="tag-container-images"></a>コンテナー イメージのタグ付け

各コンテナー イメージは、レジストリの名前 loginServer でタグ付けする必要があります。 このタグは、イメージ レジストリにコンテナー イメージをプッシュするときに、ルーティングするために使用されます。

現在のイメージの一覧を表示するには、[docker images](https://docs.docker.com/engine/reference/commandline/images/) コマンドを使用します。

```bash
docker images
```

出力:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-back              latest              bf9a858a9269        3 seconds ago        107MB
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
redis                        latest              9813a7e8fcc0        2 days ago           107MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB
```

loginServer 名を取得するには、次のコマンドを実行します。

```bash
az acr show --name <acrName> --query loginServer --output table
```

ここで、*azure-vote-front* イメージにコンテナー レジストリの loginServer をタグ付けします。 また、イメージ名の末尾に `:v1` を付加します。 このタグは、イメージのバージョンを示します。

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

次に、*azure-vote-back* イメージにコンテナー レジストリの loginServer でタグを付けます。 また、イメージ名の末尾に `:v1` を付加します。 このタグは、イメージのバージョンを示します。

```bash
docker tag azure-vote-back <acrLoginServer>/azure-vote-back:v1
```

タグを付けた後、"docker images" を実行して動作を確認します。


出力:

```bash
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
azure-vote-back                        latest              bf9a858a9269        22 minutes ago      107MB
<acrName>.azurecr.io/azure-vote-back    v1                  bf9a858a9269        22 minutes ago      107MB
azure-vote-front                       latest              052c549a75bf        23 minutes ago      708MB
<acrName>.azurecr.io/azure-vote-front   v1                  052c549a75bf        23 minutes ago      708MB
redis                                  latest              9813a7e8fcc0        2 days ago          107MB
tiangolo/uwsgi-nginx-flask             python3.6           590e17342131        5 days ago          707MB

```

## <a name="push-images-to-registry"></a>イメージをレジストリにプッシュ

*azure-vote-front* イメージをレジストリにプッシュします。 

次の例を使用して、ACR loginServer 名を環境の loginServer に置き換えます。

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

*azure-vote-back* イメージをレジストリにプッシュします。 

次の例を使用して、ACR loginServer 名を環境の loginServer に置き換えます。

```bash
docker push <acrLoginServer>/azure-vote-back:v1
```

docker push コマンドが完了までに数分かかります。

## <a name="list-images-in-registry"></a>レジストリ内のイメージの一覧表示

お使いの Azure Container Registry にプッシュされたイメージの一覧を返すには、[az acr repository list](/cli/azure/acr/repository#list) コマンドを使用します。 ACR のインスタンス名でコマンドを更新します。

```bash
az acr repository list --name <acrName> --output table
```

出力:

```bash
Result
----------------
azure-vote-back
azure-vote-front
```

チュートリアル完了時には、コンテナー イメージがプライベートの Azure Container Registry インスタンスに格納されています。 このイメージは、以降のチュートリアルで、ACR から Service Fabric クラスターにデプロイされます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、アプリケーションを Github から取得し、コンテナー イメージを作成して、レジストリにプッシュしました。 次の手順を完了しました。

> [!div class="checklist"]
> * GitHub からアプリケーション ソースを複製する  
> * アプリケーション ソースからコンテナー イメージを作成する
> * Azure Container Registry (ACR) インスタンスをデプロイする
> * ACR のコンテナー イメージにタグを付ける
> * イメージを ACR にアップロードする

次のチュートリアルに進み、Yeoman を使ってコンテナーを Service Fabric アプリケーションにパッケージ化する方法を学習してください。 

> [!div class="nextstepaction"]
> [Service Fabric アプリケーションとしてのコンテナーのパッケージ化とデプロイ](service-fabric-tutorial-package-containers.md)
