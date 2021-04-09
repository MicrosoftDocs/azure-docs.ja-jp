---
title: Azure Red Hat OpenShift で Azure Container Registry を使用する
description: Azure Red Hat OpenShift クラスターで Azure Container Registry からコンテナーをプルして実行する方法について説明します。
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 01/10/2021
ms.openlocfilehash: 651b73db084e8090f59faeffa9991c2ac468ca08
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100634415"
---
# <a name="use-azure-container-registry-with-azure-red-hat-openshift-aro"></a>Azure Red Hat OpenShift (ARO) で Azure Container Registry を使用する

Azure Container Registry (ACR) は、geo レプリケーションなどのエンタープライズ機能を備えたプライベート Docker コンテナー イメージを格納するために使用できる、マネージド コンテナー レジストリ サービスです。 ARO クラスターから ACR にアクセスするために、クラスターは、Docker ログイン資格情報を Kubernetes シークレットに格納して ACR で認証できます。  同様に、ARO クラスターは、ポッド仕様の imagePullSecret を使用して、イメージをプルするときにレジストリに対して認証を行うことができます。 この記事では、プライベート Docker コンテナー イメージを格納およびプルするために、Azure Red Hat OpenShift クラスターで Azure Container Registry を設定する方法について説明します。

## <a name="prerequisites"></a>前提条件

このガイドでは、既存の Azure Container Registry があることを前提としています。 そうでない場合、Azure portal または [Azure CLI の手順](../container-registry/container-registry-get-started-azure-cli.md)を使用して、コンテナー レジストリを作成します。

また、この記事では、既存の Azure Red Hat OpenShift クラスターがあり、`oc` CLI がインストールされていることを前提としています。 そうでない場合、[ARO クラスターの作成に関するチュートリアル](tutorial-create-cluster.md)の指示に従ってください。

## <a name="get-a-pull-secret"></a>プル シークレットを取得する

ARO クラスターからレジストリにアクセスするには、ACR のプル シークレットが必要です。

プル シークレット資格情報を取得するには、Azure portal または Azure CLI のいずれかを使用できます。

Azure portal を使用する場合は、ACR インスタンスに移動し、 **[アクセス キー]** を選択します。  `docker-username` は、お使いのコンテナー レジストリの名前です。`docker-password`には password または password2 のいずれかを使用します。

![アクセス キー](./media/acr-access-keys.png)

代わりに、Azure CLI を使用してこれらの資格情報を取得できます。
```azurecli
az acr credential show -n <your registry name>
```

## <a name="create-the-kubernetes-secret"></a>Kubernetes シークレットを作成する

ここでは、これらの資格情報を使用して、Kubernetes シークレットを作成します。 自分の ACR 資格情報を使用して次のコマンドを実行します。

```
oc create secret docker-registry \
    --docker-server=<your registry name>.azurecr.io \
    --docker-username=<your registry name> \
    --docker-password=******** \
    --docker-email=unused \
    acr-secret
```

>[!NOTE]
>このシークレットは、現在の OpenShift プロジェクト (Kubernetes 名前空間) に格納され、そのプロジェクトで作成されたポッドによってのみ参照可能になります。  クラスター全体のプル シークレットを作成する手順については、こちらの[ドキュメント](https://docs.openshift.com/container-platform/4.4/openshift_images/managing_images/using-image-pull-secrets.html)を参照してください。

## <a name="create-a-pod-using-a-private-registry-image"></a>プライベート レジストリ イメージを使用してポッドを作成する

ARO クラスターを ACR に接続したので、ACR からイメージをプルしてポッドを作成してみましょう。

まず podSpec を使用して、作成したシークレットを imagePullSecret として指定します。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-world
spec:
  containers:
  - name: hello-world
    image: <your registry name>.azurecr.io/hello-world:v1
  imagePullSecrets:
  - name: acr-secret
```

ポッドが起動して実行されていることをテストするには、このコマンドを実行し、状態が **[実行中]** になるまで待ちます。

```bash
$ oc get pods --watch
NAME         READY   STATUS    RESTARTS   AGE
hello-world  1/1     Running   0          30s
```

## <a name="next-steps"></a>次の手順

* [Azure Container Registry](../container-registry/container-registry-concepts.md)
* [クイック スタート:Azure CLI を使用したプライベート コンテナー レジストリの作成](../container-registry/container-registry-get-started-azure-cli.md)
