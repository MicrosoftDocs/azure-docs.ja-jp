---
title: Azure で Service Fabric 上のコンテナー アプリをフェールオーバーおよびスケーリングする | Microsoft Docs
description: このチュートリアルでは、Azure Service Fabric コンテナー アプリケーションでのフェールオーバーの処理方法について説明します。  また、クラスターで実行されているコンテナーとサービスをスケーリングする方法についても説明します。
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
ms.openlocfilehash: a13ce98eeebf60a6b61ee9aff01107c61fa94641
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385110"
---
# <a name="tutorial-demonstrate-fail-over-and-scaling-of-container-services-with-service-fabric"></a>チュートリアル:Service Fabric でのコンテナー サービスのフェールオーバーとスケーリングのデモンストレーション

このチュートリアルは、シリーズの第 3 部です。 このチュートリアルでは、Service Fabric コンテナー アプリケーションでのフェールオーバーの処理方法について説明します。 さらに、コンテナーのスケーリング方法を説明します。 このチュートリアルでは、次のことを行いました。

> [!div class="checklist"]
> * Service Fabric クラスターでのコンテナーのフェールオーバーについて学習する
> * アプリケーションの Web フロントエンド コンテナーをスケーリングする

## <a name="prerequisites"></a>前提条件

[パート 2](service-fabric-tutorial-package-containers.md) のアプリケーションは、アクティブな Service Fabric クラスターで動作しています。

## <a name="fail-over-a-container-in-a-cluster"></a>クラスター内のコンテナーをフェールオーバーする

障害が発生すると、Service Fabric はクラスター内の他のノードにコンテナー インスタンスを自動的に移動します。 また、コンテナーのノードを手動でドレインして、クラスター内の他のノードに適切に移動することもできます。 サービスをスケーリングするには複数の方法があります。この例では、Service Fabric Explorer を使います。

フロントエンド コンテナーのフェールオーバーは、次の手順で行います。

1. クラスターで Service Fabric Explorer を開きます (例: `http://lin4hjim3l4.westus.cloudapp.azure.com:19080`)。
2. ツリー ビューの **fabric:/TestContainer/azurevotefront** ノードをクリックし、パーティション ノード (GUID で表されます) を展開します。 ツリー ビューのノード名に注意してください。この名前は、コンテナーが現在実行しているノードを示します (例: `_nodetype_1`)。
3. ツリー ビューの **Nodes** ノードを展開します。 コンテナーを実行しているノードの横にある省略記号 (3 つのドット) をクリックします。
4. **[再起動]** を選んでそのノードを再起動し、再起動操作を確認します。 再起動により、コンテナーはクラスター内の別のノードにフェールオーバーします。

![noderestart][noderestart]

ノード名 (フロントエンド コンテナーが実行されている場所を示す) がクラスター内の別のノードに変わることを確認します。 しばらくすると、再度アプリケーションを参照し、アプリケーションが異なるノードで実行していることを確認できます。

## <a name="scale-containers-and-services-in-a-cluster"></a>クラスター内のコンテナーとサービスをスケーリングする

Service Fabric コンテナーは、サービスに対する負荷に対応して、クラスター内でスケーリングすることができます。 コンテナーをスケーリングするには、クラスターで実行されるインスタンスの数を変更します。

Web フロントエンドをスケーリングするには、次の手順のようにします。

1. クラスターで Service Fabric Explorer を開きます (例: `http://lin4hjim3l4.westus.cloudapp.azure.com:19080`)。
2. ツリー ビューで **fabric:/TestContainer/azurevotefront** ノードの横にある省略記号 (3 つの点) をクリックし、 **[Scale Service]\(サービスのスケーリング\)** を選びます。

![sfxscale][sfxscale]

これで Web フロントエンドのインスタンス数をスケーリングできる状態になりました。

1. この数値を **2** に変更し、 **[Scale Service]\(サービスのスケール\)** をクリックします。
1. ツリー ビューの **fabric:/TestContainer/azurevotefront** ノードをクリックし、パーティション ノード (GUID で表されます) を展開します。

![sfxscaledone][sfxscaledone]

サービスに 2 つのインスタンスがあることがわかります。 ツリー ビューでは、インスタンスが実行しているノードを確認できます。

たったこれだけの管理タスクにより、フロントエンド サービスでユーザー負荷を処理するためのリソースが 2 倍になりました。 実行するサービスの信頼性を高めるために、サービスのインスタンスを複数用意する必要はないことに注目してください。 サービスで障害が発生した場合、Service Fabric によって新しいサービス インスタンスがクラスターで実行されます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、コンテナーのフェールオーバーとアプリケーションのスケーリングを実際に行いました。 次の手順を完了しました。

> [!div class="checklist"]
> * Service Fabric クラスターでのコンテナーのフェールオーバーについて学習する
> * アプリケーションの Web フロントエンド コンテナーをスケーリングする

このチュートリアル シリーズで学習した内容は次のとおりです。
> [!div class="checklist"]
> * コンテナー イメージを作成する
> * Azure Container Registry にコンテナー イメージをプッシュする
> * Yeoman を使って Service Fabric のコンテナーをパッケージ化する
> * コンテナーで Service Fabric アプリケーションをビルドして実行する
> * Service Fabric でのフェールオーバーとスケーリングの処理方法

[noderestart]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialnoderestart.png
[sfxscale]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialscale.png
[sfxscaledone]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialscaledone.png