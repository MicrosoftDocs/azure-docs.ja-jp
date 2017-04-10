---
title: "Azure Kubernetes クラスターの監視 - Sysdig | Microsoft Docs"
description: "Sysdig を使用した Azure Container Service での Kubernetes クラスターの監視"
services: container-service
documentationcenter: 
author: bburns
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: bburns
translationtype: Human Translation
ms.sourcegitcommit: 0aa9b3ae14f586fc79e6ebee898e794d526c19bd
ms.openlocfilehash: daabc3ccda51cfd165e2ae6e1d329e866b3ebbcb


---

# <a name="monitor-an-azure-container-service-kubenrnetes-cluster-using-sysdig"></a>Sysdig を使って Azure Container Service Kubenrnetes クラスターを監視する

## <a name="prerequisites"></a>前提条件
このチュートリアルでは、[Azure Container Service を使用して Kubernetes クラスターを作成](container-service-kubernetes-walkthrough.md)したことを想定します。

また、azure cli と kubectl ツールをインストールしていることも想定します。

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

## <a name="sysdig"></a>Sysdig
Sysdig は、Azure で実行されている Kubernetes クラスター内のコンテナーを監視できる、サービス会社としての外部監視です。 Sysdig を使用するには、アクティブな Sysdig アカウントが必要になります。
アカウントには、その[サイト](https://app.sysdigcloud.com)でサインアップできます。

Sysdig クラウドの Web サイトへのログイン後に自分のユーザー名をクリックすると、該当する "アクセス キー" がそのページに表示されます。 

![Sysdig API key](./media/container-service-monitoring-sysdig/sysdig2.png)

## <a name="installing-the-sysdig-agents-to-kubernetes"></a>Kubernetes への Sysdig エージェントのインストール
コンテナーを監視するには、Sysdig で、Kubernetes `DaemonSet` を使用して各コンピューターでプロセスを実行します。
DaemonSet は、コンピューターごとに&1; つのコンテナーの&1; つのインスタンスを実行する Kubernetes API オブジェクトです。
Sysdig の監視エージェントなどのツールをインストールする場合に最適です。

Sysdig DaemonSet をインストールするには、まず[テンプレート](https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml)を sysdig からダウンロードします。 そのファイルを `sysdig-daemonset.yaml` という名前で保存します。

Linux および OS X 上では次のように実行することができます。

```console
$ curl -O https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml
```

PowerShell では次のとおりです。

```console
$ Invoke-WebRequest -Uri https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml | Select-Object -ExpandProperty Content > sysdig-daemonset.yaml
```

次に、そのファイルを編集して、Sysdig アカウントから取得したアクセス キーを挿入します。

最後に、DaemonSet を作成します。

```console
$ kubectl create -f sysdig-daemonset.yaml
```

## <a name="view-your-monitoring"></a>監視の表示
インストールして実行したら、エージェントが Sysdig にデータを返します。  [Sysdig ダッシュボード](https://app.sysdigcloud.com)に戻ると、コンテナーに関する情報が表示されます。

[新しいダッシュボード ウィザード](https://app.sysdigcloud.com/#/dashboards/new)を使用して、Kubernetes 固有のダッシュボードをインストールすることもできます。



<!--HONumber=Jan17_HO4-->


