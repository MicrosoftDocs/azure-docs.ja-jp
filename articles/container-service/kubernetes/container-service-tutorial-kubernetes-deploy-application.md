---
title: "Azure Container Service チュートリアル - アプリケーションのデプロイ | Microsoft Docs"
description: "Azure Container Service チュートリアル - アプリケーションのデプロイ"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, コンテナー, マイクロサービス, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 81a6a3d5364642b2da75faf875d64d2f4a1939d4
ms.contentlocale: ja-jp
ms.lasthandoff: 07/25/2017

---

# <a name="run-applications-in-kubernetes"></a>Kubernetes でアプリケーションを実行する

このチュートリアル (4/7) では、サンプル アプリケーションを Kubernetes クラスターにデプロイします。 手順は次のとおりです。

> [!div class="checklist"]
> * Kubernetes マニフェスト ファイルをダウンロードする
> * Kubernetes でアプリケーションを実行する
> * アプリケーションをテストする

後のチュートリアルでは、このアプリケーションをスケールアウトおよび更新し、Kubernetes クラスターを監視するように Operations Management Suite を構成します。

このチュートリアルは、Kubernetes の概念についての基礎知識があることを前提としています。Kubernetes の詳細については、[Kubernetes のドキュメント](https://kubernetes.io/docs/home/)を参照してください。

## <a name="before-you-begin"></a>開始する前に

前のチュートリアルでは、アプリケーションをコンテナー イメージにパッケージ化し、このイメージを Azure Container Registry にアップロードして、Kubernetes クラスターを作成しました。 これらの手順を実行していない場合で、行いたい場合は、「[チュートリアル 1 – コンテナー イメージを作成する](./container-service-tutorial-kubernetes-prepare-app.md)」に戻ってください。 

このチュートリアルでは、少なくとも Kubernetes クラスターが必要です。

## <a name="get-manifest-file"></a>マニフェスト ファイルを取得する

このチュートリアルでは、Kubernetes マニフェストを使って [Kubernetes オブジェクト](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/)をデプロイします。 Kubernetes マニフェストは YAML または JSON 形式のファイルであり、Kubernetes オブジェクトのデプロイと構成の指示が含まれます。

このチュートリアルのアプリケーション マニフェスト ファイルは、前のチュートリアルで複製した Azure Vote アプリケーション リポジトリで入手できます。 まだ行っていない場合は、次のコマンドでリポジトリを複製してください。 

```bash
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

マニフェスト ファイルは、複製されたリポジトリの次のディレクトリにあります。

```bash
/azure-voting-app-redis/kubernetes-manifests/azure-vote-all-in-one-redis.yml
```

## <a name="update-manifest-file"></a>マニフェスト ファイルを更新する

Azure Container Registry を使ってコンテナー イメージを格納している場合は、マニフェストを ACR loginServer 名で更新する必要があります。

ACR ログイン サーバー名を取得するには、[az acr list](/cli/azure/acr#list) コマンドを使います。

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

サンプル マニフェストは、*microsoft* というリポジトリ名であらかじめ作成されています。 任意のテキスト エディターでファイルを開き、*microsoft* という値を実際の ACR インスタンスのログイン サーバー名に置き換えます。

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:redis-v1
```

## <a name="deploy-application"></a>アプリケーションをデプロイする

[kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create) コマンドを使用してアプリケーションを実行します。 このコマンドは、マニフェスト ファイルを解析し、定義されている Kubernetes オブジェクトを作成します。

```azurecli-interactive
kubectl create -f ./azure-voting-app-redis/kubernetes-manifests/azure-vote-all-in-one-redis.yml
```

出力:

```bash
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>アプリケーションをテストする

アプリケーションをインターネットに公開する [Kubernetes サービス](https://kubernetes.io/docs/concepts/services-networking/service/)が作成されます。 このプロセスには数分かかることがあります。 

進行状況を監視するには、[kubectl get service](https://review.docs.microsoft.com/en-us/azure/container-service/container-service-kubernetes-walkthrough?branch=pr-en-us-17681) コマンドを `--watch` 引数と一緒に使用します。

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

最初に、*azure-vote-front* サービスの **EXTERNAL-IP** が "*保留中*" として表示されます。 EXTERNAL-IP アドレスが "*保留中*" から "*IP アドレス*" に変わると、`CTRL-C` を使用してkubectl ウォッチ プロセスを停止します。

```bash
NAME               CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   10.0.42.158   <pending>     80:31873/TCP   1m
azure-vote-front   10.0.42.158   52.179.23.131 80:31873/TCP   2m
```

アプリケーションを表示するには、外部 IP アドレスを参照します。

![Azure 上の Kubernetes クラスターの図](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Vote アプリケーションを Azure Container Service の Kubernetes クラスターにデプロイしました。 次のタスクを行います。  

> [!div class="checklist"]
> * Kubernetes マニフェスト ファイルをダウンロードする
> * Kubernetes でアプリケーションを実行する
> * アプリケーションをテストする

次のチュートリアルに進んで、Kubernetes アプリケーションとその基になっている Kubernetes インフラストラクチャ両方のスケーリングに関して学習してください。 

> [!div class="nextstepaction"]
> [Kubernetes ポッドと Kubernetes インフラストラクチャをスケーリングする](./container-service-tutorial-kubernetes-scale.md)
