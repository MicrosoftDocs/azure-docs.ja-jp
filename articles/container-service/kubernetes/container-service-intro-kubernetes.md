---
title: (非推奨) Kubernetes 向け Azure Container Service の紹介
description: Kubernetes 向けの Azure Container Service では、Azure でコンテナー ベースのアプリケーションを簡単にデプロイして管理できます。
services: container-service
author: gabrtv
manager: jeconnoc
ms.service: container-service
ms.topic: overview
ms.date: 07/21/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: e00ac57cc36b3331cfb847ecedc6c75132cdeb6b
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52999173"
---
# <a name="deprecated-introduction-to-azure-container-service-for-kubernetes"></a>(非推奨) Kubernetes 向け Azure Container Service の紹介

> [!TIP]
> Azure Kubernetes Service を使用するこの記事の更新版については、[Azure Kubernetes Service (AKS) の概要](../../aks/intro-kubernetes.md)に関する記事をご覧ください。

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Kubernetes 向けの Azure Container Service を使用すると、コンテナー化されたアプリケーションを実行するように事前構成されている仮想マシンのクラスターを簡単に作成、構成、および管理できます。 これにより、既存のスキルを使用して、または増え続けている大規模な専門家コミュニティの知恵に頼って、Microsoft Azure にコンテナーベースのアプリケーションをデプロイおよび管理できます。

Azure Container Service を使用することで、Kubernetes とDocker イメージのフォーマットを介してアプリケーションの移植性を維持しながら、エンタープライズ レベルの Azure の機能を活用することができます。

## <a name="using-azure-container-service-for-kubernetes"></a>Kubernetes 向け Azure Container Service の使用
Azure Container Service は、今日お客様に人気のあるオープンソースのツールとテクノロジを使用することで、コンテナー ホスト環境を提供することを目的としています。 この目的のために、標準の Kubernetes API エンドポイントを公開します。 これらの標準のエンドポイントを使用することにより、Kubernetes クラスターと通信できる任意のソフトウェアを活用できます。 たとえば、[kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)、[helm](https://helm.sh/)、[draft](https://github.com/Azure/draft) を選択できます。

## <a name="creating-a-kubernetes-cluster-using-azure-container-service"></a>Azure Container Service を使用した Kubernetes クラスターの作成
Azure Container Service の使用を開始するには、[Azure CLI](container-service-kubernetes-walkthrough.md) を使用するか、ポータル (Marketplace で **Azure Container Service** を検索) 経由で、Azure Container Service クラスターをデプロイします。 Azure Resource Manager テンプレートを細かく制御する必要がある上級ユーザーの場合は、オープン ソースの [acs-engine](https://github.com/Azure/acs-engine) プロジェクトを使用して、独自のカスタム Kubernetes クラスターを構築し、これを `az` CLI 経由でデプロイできます。

### <a name="using-kubernetes"></a>Kubernetes の使用
この Kubernetes により、コンテナー化されたアプリケーションのデプロイ、スケーリング、および管理が自動化されます。 Kubernetes には、次に示す豊富な機能セットが用意されています。
* 自動ビンパッキング
* 自己復旧
* 水平スケーリング
* サービス検出と負荷分散
* 自動化されたロールアウトとロールバック
* シークレットと構成管理
* ストレージ オーケストレーション
* バッチ実行

Azure Container Service を使用してデプロイされた Kubernetes のアーキテクチャ図は以下のとおりです。

![Kubernetes を使用するように構成された Azure Container Service。](media/acs-intro/kubernetes.png)

## <a name="videos"></a>ビデオ

Azure Container Service における Kubernetes のサポート (Azure Friday、2017 年 1 月):

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Kubernetes-Support-in-Azure-Container-Services/player]
>
>

Kubernetes でのアプリケーションの開発およびデプロイに使用するツール (Azure OpenDev、2017 年 6 月):

> [!VIDEO https://channel9.msdn.com/Events/AzureOpenDev/June2017/Tools-for-Developing-and-Deploying-Applications-on-Kubernetes/player]
>
>

## <a name="next-steps"></a>次の手順

[Kubernetes クイックスタート](container-service-kubernetes-walkthrough.md)を確認し、Azure Container Service を今すぐ開始します。
