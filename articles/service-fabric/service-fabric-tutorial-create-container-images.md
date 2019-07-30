---
title: Azure で Service Fabric にコンテナー イメージを作成する | Microsoft Docs
description: このチュートリアルでは、複数コンテナーの Service Fabric アプリケーションのコンテナー イメージを作成する方法を説明します。
services: service-fabric
documentationcenter: ''
author: suhuruli
manager: chackdan
editor: suhuruli
tags: servicefabric
keywords: Docker, コンテナー, マイクロサービス, Service Fabric, Azure
ms.assetid: ''
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/22/2019
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: b5dba1f7e71149c37edc909d2bb43d708192bdbb
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385229"
---
# <a name="tutorial-create-container-images-on-a-linux-service-fabric-cluster"></a>チュートリアル:Linux Service Fabric クラスターにコンテナー イメージを作成する

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

* Service Fabric 用に設定された Linux 開発環境。 [こちら](service-fabric-get-started-linux.md)の説明に従って、Linux 環境を設定します。
* このチュートリアルでは、Azure CLI バージョン 2.0.4 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。
* さらに、使用可能な Azure サブスクリプションを持っている必要があります。 無料試用版について詳しくは、[こちら](https://azure.microsoft.com/free/)をご覧ください。

## <a name="get-application-code"></a>アプリケーションのコードを入手する

このチュートリアルで使うサンプル アプリケーションは投票アプリです。 アプリケーションは、フロントエンド Web コンポーネントとバックエンド Redis インスタンスで構成されています。 コンポーネントは、コンテナー イメージにパッケージ化されています。

アプリケーションのコピーを開発環境にダウンロードするには、git を使います。

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/
```

ソリューションには 2 つのフォルダーと 'docker-compose.yml' ファイルが含まれています。 'azure-vote'フォルダーには、イメージのビルドに使用される Dockerfile と共に Python フロントエンド サービスが格納されています。 'Voting' ディレクトリには、クラスターにデプロイされる Service Fabric アプリケーション パッケージが含まれています。 これらのディレクトリには、このチュートリアルに必要な資産が含まれています。

## <a name="create-container-images"></a>コンテナー イメージを作成する

**azure-vote** ディレクトリ内で次のコマンドを実行して、フロントエンド Web コンポーネントのイメージをビルドします。 このコマンドは、このディレクトリ内の Dockerfile を使ってイメージをビルドします。

```bash
docker build -t azure-vote-front .
```
> [!Note]
> アクセス許可が拒否される場合、sudo なしで Docker を操作する方法に関する[この](https://docs.docker.com/install/linux/linux-postinstall/#manage-docker-as-a-non-root-user)ドキュメントに従います。

このコマンドは、必要なすべての依存関係を Docker Hub からプルする必要があるため、時間がかかる場合があります。 完了したら、[docker images](https://docs.docker.com/engine/reference/commandline/images/) コマンドを使って、作成されたイメージを確認します。

```bash
docker images
```

2 つのイメージがダウンロードまたは作成されたことに注目してください。 *azure-vote-front* イメージにアプリケーションが含まれています。 これは、Docker Hub の *python* イメージから派生されたものです。

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB

```

## <a name="deploy-azure-container-registry"></a>Azure Container Registry のデプロイ

最初に、**az login** コマンドを実行して Azure アカウントにサインインします。

```bash
az login
```

次に、**az account** コマンドを使って、Azure Container Registry を作成するためのサブスクリプションを選びます。 <subscription_id> には、お使いの Azure サブスクリプションのサブスクリプション ID を入力する必要があります。

```bash
az account set --subscription <subscription_id>
```

Azure Container Registry をデプロイする場合、まず、リソース グループが必要です。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

**az group create** コマンドでリソース グループを作成します。 この例では、*myResourceGroup* という名前のリソース グループが *westus* リージョンに作成されます。

```bash
az group create --name <myResourceGroup> --location westus
```

**az acr create** コマンドで Azure Container Registry を作成します。 \<acrName> は、お使いのサブスクリプション下に作成するコンテナー レジストリの名前に置き換える必要があります。 この名前は、英数字を使用して一意にする必要があります。

```bash
az acr create --resource-group <myResourceGroup> --name <acrName> --sku Basic --admin-enabled true
```

このチュートリアルの残りの部分では、選択したコンテナー レジストリ名のプレースホルダーとして「acrName」を使用します。 この値をメモしておいてください。

## <a name="sign-in-to-your-container-registry"></a>コンテナー レジストリにサインインする

イメージをプッシュする前に、ACR のインスタンスにサインインします。 **az acr login** コマンドを使用して、操作を完了します。 コンテナー レジストリの作成時に割り当てられた一意名を入力します。

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
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB
```

loginServer 名を取得するには、次のコマンドを実行します。

```bash
az acr show --name <acrName> --query loginServer --output table
```

これにより、以下のような結果がテーブルに出力されます。 この結果は、次の手順でコンテナー レジストリにプッシュする前に、**azure-vote-front** イメージのタグ付けに使用されます。

```bash
Result
------------------
<acrName>.azurecr.io
```

ここでは、*azure-vote-front* イメージにお使いのコンテナー レジストリの loginServer をタグ付けします。 また、イメージ名の末尾に `:v1` を付加します。 このタグは、イメージのバージョンを示します。

```bash
docker tag azure-vote-front <acrName>.azurecr.io/azure-vote-front:v1
```

タグを付けた後、"docker images" を実行して動作を確認します。

出力:

```bash
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                       latest              052c549a75bf        23 minutes ago      708MB
<acrName>.azurecr.io/azure-vote-front   v1                  052c549a75bf       23 minutes ago      708MB
tiangolo/uwsgi-nginx-flask             python3.6           590e17342131        5 days ago          707MB

```

## <a name="push-images-to-registry"></a>イメージをレジストリにプッシュ

*azure-vote-front* イメージをレジストリにプッシュします。 

次の例を使用して、ACR loginServer 名を環境の loginServer に置き換えます。

```bash
docker push <acrName>.azurecr.io/azure-vote-front:v1
```

docker push コマンドが完了までに数分かかります。

## <a name="list-images-in-registry"></a>レジストリ内のイメージの一覧表示

お使いの Azure Container Registry にプッシュされたイメージの一覧を返すには、[az acr repository list](/cli/azure/acr/repository) コマンドを使用します。 ACR のインスタンス名でコマンドを更新します。

```bash
az acr repository list --name <acrName> --output table
```

出力:

```bash
Result
----------------
azure-vote-front
```

チュートリアル完了時には、コンテナー イメージがプライベートの Azure Container Registry インスタンスに格納されています。 このイメージは、以降のチュートリアルで、ACR から Service Fabric クラスターにデプロイされます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、アプリケーションを GitHub から取得し、コンテナー イメージを作成して、レジストリにプッシュしました。 次の手順を完了しました。

> [!div class="checklist"]
> * GitHub からアプリケーション ソースを複製する
> * アプリケーション ソースからコンテナー イメージを作成する
> * Azure Container Registry (ACR) インスタンスをデプロイする
> * ACR のコンテナー イメージにタグを付ける
> * イメージを ACR にアップロードする

次のチュートリアルに進み、Yeoman を使ってコンテナーを Service Fabric アプリケーションにパッケージ化する方法を学習してください。

> [!div class="nextstepaction"]
> [Service Fabric アプリケーションとしてのコンテナーのパッケージ化とデプロイ](service-fabric-tutorial-package-containers.md)
