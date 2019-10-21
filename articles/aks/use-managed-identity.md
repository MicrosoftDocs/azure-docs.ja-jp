---
title: Azure Kubernetes Service でマネージド ID を使用する
description: Azure Kubernetes Service (AKS) でマネージド ID を使用する方法について説明します。
services: container-service
author: saudas
manager: saudas
ms.service: container-service
ms.topic: article
ms.date: 09/11/2019
ms.author: saudas
ms.openlocfilehash: a5717d8ee44e4d2e086a6e7bc1b7c3d0deb614c8
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827541"
---
# <a name="preview---use-managed-identities-in-azure-kubernetes-service"></a>プレビュー - Azure Kubernetes Service でマネージド ID を使用する

現在、AKS クラスター (具体的には Kubernetes クラウド プロバイダー) で Azure 内にロード バランサーやマネージド ディスクなどの追加リソースを作成するには、ユーザーがサービス プリンシパルを指定するか、AKS が代理で作成する必要があります。 サービス プリンシパルは、通常、有効期限付きで作成されます。 最終的には、クラスターはサービス プリンシパルを更新する必要がある状態になります。そうならないと、クラスターは機能しません。 サービス プリンシパルを管理すると、複雑さが増します。 マネージド ID は、本質的にサービス プリンシパルのラッパーであり、管理がより簡単になります。 詳細については、[マネージド ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) に関する記事を参照してください。

AKS では、2 つのマネージド ID が作成されます。1 つはシステム割り当てのマネージド ID、もう 1 つはユーザー割り当ての ID です。 システム割り当てのマネージド ID は、ユーザーに代わって Azure リソースを作成するために、Kubernetes クラウド プロバイダーによって使用されます。 このシステム割り当てのマネージド ID のライフ サイクルは、クラスターのそれに関連付けられ、クラスターが削除されると削除されます。 また、AKS ではユーザー割り当てのマネージド ID も作成されます。これは、AKS が ACR にアクセスすること、kubelet が Azure からメタデータを取得することなどを承認するために使用されます。

このプレビュー期間中は、まだサービス プリンシパルが必要です。 これは監視、仮想ノード、Azure ポリシー、http アプリケーション ルーティングなどのアドオンの承認に使用されます。 SPN に対するアドオンの依存関係を削除するために進行中の作業があり、最終的に AKS の SPN の要件は完全に削除されます。

> [!IMPORTANT]
> AKS のプレビュー機能は、セルフサービスのオプトインです。 プレビューは、"現状有姿のまま" および "利用可能な限度" で提供され、サービス レベル契約および限定保証から除外されるものとします。 AKS プレビューは、カスタマー サポートによってベスト エフォートで部分的にカバーされます。 そのため、これらの機能は、運用環境での使用を意図していません。 詳細については、次のサポートに関する記事を参照してください。
>
> * [AKS のサポート ポリシー](support-policies.md)
> * [Azure サポートに関する FAQ](faq.md)

## <a name="before-you-begin"></a>開始する前に

次のものが必要です。

* Azure CLI バージョン 2.0.70 以降および aks-preview 0.4.14 拡張機能も必要です。

## <a name="install-latest-aks-cli-preview-extension"></a>最新の AKS CLI プレビュー拡張機能をインストールする

**aks-preview 0.4.14** 拡張機能以降が必要です。

```azurecli
az extension update --name aks-preview 
az extension list
```

> [!CAUTION]
> サブスクリプションで機能を登録する場合、現時点ではその機能を登録解除することはできません。 一部のプレビュー機能を有効にした後、すべての AKS クラスターに対して既定値が使用され、サブスクリプション内に作成されます。 運用サブスクリプションではプレビュー機能を有効にしないでください。 プレビュー機能をテストし、フィードバックを集めるには、別のサブスクリプションを使用してください。

```azurecli-interactive
az feature register --name MSIPreview --namespace Microsoft.ContainerService
```

状態が "*登録済み*" と表示されるまでに数分かかることがあります。 [az feature list][az-feature-list] コマンドを使用して登録状態を確認できます。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MSIPreview')].{Name:name,State:properties.state}"
```

状態が登録されたら、[az provider register][az-provider-register] コマンドを使用して *Microsoft.ContainerService* リソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-managed-identity"></a>マネージド ID を指定して AKS クラスターを作成する

次の CLI コマンドを使用し、マネージド ID を指定して AKS クラスターを作成できるようになりました。
```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

## <a name="create-an-aks-cluster"></a>AKS クラスターの作成
```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

## <a name="get-credentials-to-access-the-cluster"></a>クラスターにアクセスする資格情報を取得する
```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
数分でクラスターが作成されたら、アプリケーションのワークロードをデプロイし、サービス プリンシパル ベースの AKS クラスターの場合と同様に、対話することができます。 

> [!IMPORTANT]
> * マネージド ID を指定した AKS クラスターは、クラスターの作成時にのみ有効にすることができます。
> * 既存の AKS クラスターを更新またはアップグレードしてマネージド ID を有効にすることはできません