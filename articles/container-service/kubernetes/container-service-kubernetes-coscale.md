---
title: "Azure Kubernetes クラスターを CoScale を使用して監視する | Microsoft Docs"
description: "Azure Container Service の Kubernetes クラスターを CoScale を使用して監視します"
services: container-service
documentationcenter: 
author: fryckbos
manager: 
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/22/2017
ms.author: saudas
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: f894191baced710fc0f5a8c8692df98033341a48
ms.contentlocale: ja-jp
ms.lasthandoff: 08/12/2017

---

# <a name="monitor-an-azure-container-service-kubernetes-cluster-with-coscale"></a>Azure Kubernetes クラスターを CoScale を使用して監視する

この記事では、Azure Container Service の Kubernetes クラスター内のすべてのノードとコンテナーを監視する [CoScale](https://www.coscale.com/) エージェントのデプロイする方法を説明します。 この構成を行うには、CoScale のアカウントが必要です。 


## <a name="about-coscale"></a>CoScale について 

CoScale は、複数のオーケストレーション プラットフォームのすべてのコンテナーからメトリックとイベントを収集する監視プラットフォームです。 CoScale は、Kubernetes 環境のすべてを監視し、 積み重ねられているすべてのレイヤー (OS、Kubernetes、Docker、およびコンテナー内で実行されるアプリケーション) のグラフ化と分析を行います。 CoScale には、多様な監視ダッシュボードと、オペレーターと開発者がインフラストラクチャとアプリケーションの問題をすばやく検出できる異常検出機能が組み込まれています。

![CoScale UI](./media/container-service-kubernetes-coscale/coscale.png)

この記事に示すように、Kubernetes クラスターにエージェントをインストールして、CoScale を SaaS ソリューションとして実行できます。 データをオンサイトで維持する場合は、オンプレミス インストールで CoScale を使用することもできます。


## <a name="prerequisites"></a>前提条件

最初に [CoScale アカウントを作成する](https://www.coscale.com/free-trial)必要があります

このチュートリアルでは、[Azure Container Service を使用して Kubernetes クラスターを作成](container-service-kubernetes-walkthrough.md)したことを想定します。

また、`az`Azure CLI と `kubectl` ツールをインストールしていることも想定しています。

`az` ツールがインストールされていることを確認するには、次を実行します。

```azurecli
az --version
```

`az` ツールをインストールしていないない場合、[ここ](/cli/azure/install-azure-cli)に手順が記載されています。

`kubectl` ツールがインストールされていることを確認するには、次を実行します。

```bash
kubectl version
```

`kubectl` をインストールしていない場合、次を実行できます。

```azurecli
az acs kubernetes install-cli
```

## <a name="installing-the-coscale-agent-with-a-daemonset"></a>DaemonSet を使用した CoScale エージェントのインストール
[DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) は Kubernetes によって使用され、クラスターのホストごとに 1 つのコンテナーの 1 つのインスタンスを実行します。
この DaemonSet は、CoScale などの監視エージェントの実行に最適です。

CoScale にログインしたら、[エージェント ページ](https://app.coscale.com/)に移動し、DaemonSet エージェントを使用してクラスターに CoScale エージェントをインストールします。 CoScale UI によって、エージェントの作成とKubernetes クラスター全体の監視を開始するための構成手順が示されます。

![CoScale エージェントの構成](./media/container-service-kubernetes-coscale/installation.png)

クラスターでエージェントを起動するには、指定されたコマンドを実行します。

![CoScale エージェントを開始する](./media/container-service-kubernetes-coscale/agent_script.png)

これで完了です。 エージェントが実行されると、数分後にデータがコンソールに表示されます。 [エージェント ページ](https://app.coscale.com/)で、クラスターの概要を確認し、追加の構成手順を実行します。**Kubernetes クラスターの概要**などのダッシュボードが表示されます。

![Kubernetes クラスターの概要](./media/container-service-kubernetes-coscale/dashboard_clusteroverview.png)

CoScale エージェントは、クラスター内の新しいマシンに自動的にデプロイされます。 エージェントは、新しいバージョンがリリースされたときに自動的に更新されます。


## <a name="next-steps"></a>次のステップ

CoScale 監視ソリューションの詳細については、[CoScale のドキュメントと](http://docs.coscale.com/)と[ブログ](https://www.coscale.com/blog)を参照してください。 


