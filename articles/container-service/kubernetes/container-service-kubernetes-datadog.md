---
title: (非推奨) Datadog を使用した Azure Kubernetes クラスターの監視
description: Datadog を使用した Azure Container Service での Kubernetes クラスターの監視
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 6a682c199b40035bfd44fc5611a7d44b49f7b3ab
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52995354"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-datadog"></a>(非推奨) DataDog を使用した Azure Container Service クラスターの監視

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>前提条件
このチュートリアルでは、[Azure Container Service を使用して Kubernetes クラスターを作成](container-service-kubernetes-walkthrough.md)したことを想定します。

また、`az` Azure CLI と `kubectl` ツールをインストールしていることも想定します。

`az` ツールがインストールされていることを確認するには、次を実行します。

```console
$ az --version
```

`az` ツールをインストールしていないない場合、[ここ](https://github.com/azure/azure-cli#installation)に手順が記載されています。

`kubectl` ツールがインストールされていることを確認するには、次を実行します。

```console
$ kubectl version
```

`kubectl` をインストールしていない場合、次を実行できます。

```console
$ az acs kubernetes install-cli
```

## <a name="datadog"></a>Datadog
Datadog は監視サービスです。Azure コンテナー サービス クラスター内のコンテナーから監視データを収集することができます。 Datadog には、コンテナー内の特定のメトリックを表示できる Docker 統合ダッシュボードがあります。 コンテナーから収集されたメトリックは、CPU、メモリ、ネットワーク、I/O 別に整理されます。 メトリックは Datadog によってコンテナーとイメージに分割されます。

最初に[アカウントを作成](https://www.datadoghq.com/lpg/)する必要があります

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>DaemonSet を使用した Datadog エージェントのインストール
DaemonSet は Kubernetes によって使用され、クラスターのホストごとに 1 つのコンテナーの 1 つのインスタンスを実行します。
この DaemonSet は、監視エージェントの実行に最適です。

Datadog にログインしたら、[Datadog の手順](https://app.datadoghq.com/account/settings#agent/kubernetes)に従って、DaemonSet を使用して Datadog エージェントをクラスターにインストールします。

## <a name="conclusion"></a>まとめ
これで完了です。 エージェントが実行されると、数分後にデータがコンソールに表示されます。 統合 [kubernetes ダッシュボード](https://app.datadoghq.com/screen/integration/kubernetes)にアクセスすると、クラスターの概要を確認できます。
