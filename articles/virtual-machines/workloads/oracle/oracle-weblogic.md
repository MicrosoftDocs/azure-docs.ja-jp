---
title: Azure の Oracle WebLogic Server とは
description: Microsoft Azure で Oracle WebLogic Server を実行する方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: edburns
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2020
ms.author: edburns
ms.openlocfilehash: fe247e75040f658beb94a66176f802993268a7d8
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87851862"
---
# <a name="what-is-oracle-weblogic-server-on-azure"></a>Azure の Oracle WebLogic Server とは

このページでは、Azure Virtual Machines で WebLogic Server (WLS) を実行するためのソリューションについて説明します。  これらのソリューションは、Oracle と Microsoft が共同で開発しています。

Oracle WebLogic Server は、多層分散型エンタープライズ アプリケーションを開発してデプロイするための、世界初のクラウド ネイティブのエンタープライズ Java プラットフォーム アプリケーション サーバーです。 Azure WebLogic Server のオファーにより、クラウド コンピューティングを導入することができます。  Java EE アプリケーションの Azure Cloud へのリフト アンド シフトなど、WebLogic 移行の選択肢が増え、柔軟性が向上します。   Azure 上の WLS は、わずかな労力で大きな影響をもたらすことができます。 このオファーにより、基幹業務 (LOB) アプリケーションを迅速に開始できます。  各オファーでは、仮想ネットワーク、ストレージ、および Linux のリソースが自動的にプロビジョニングされます。  手間を一切かけることなく、WebLogic Server がインストールされます。  Azure 上の WLS では、ネットワーク セキュリティ グループを使用してセキュリティを設定し、Azure App Gateway によって負荷分散を行い、Azure Active Directory で認証を行い、既存のデータベースに自動的に接続します。

:::image type="content" source="media/oracle-weblogic/wls-on-azure.gif" alt-text="Azure portal を使用して、WebLogic Server を Azure にデプロイできる":::

さまざまなシナリオに対応できるよう、管理サーバーがある単一ノード、管理サーバーがない単一ノード、クラスター、動的クラスターの 4 つのオファーが用意されています。  このオファーをぜひお試しください。これらは、無料でご利用いただけます。

_これらのオファーはライセンス持ち込み型です_。 ここでは、Oracle ですでに適切なライセンスを取得していること、および Microsoft Azure でオファーを実行するための適切なライセンスをお持ちであることを前提としています。

_オファーを開発しているエンジニアリング チームと密接に連携しながら移行シナリオに取り組みたい場合は、_ [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) の [[CONTACT ME]\(お問い合わせ\)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) ボタンを選択してください。 プログラム マネージャー、アーキテクト、エンジニアからすぐに連絡があり、コラボレーションが開始されます。

## <a name="oracle-weblogic-server-single-node"></a>Oracle WebLogic Server Single Node

このオファーでは、単一の仮想マシンがプロビジョニングされ、そこに WLS がインストールされます。 ドメインの作成や管理サーバーの起動は行われません。 単一ノードは、高度にカスタマイズしたドメイン構成を使用するシナリオで役立ちます。

## <a name="oracle-weblogic-server-with-admin-server"></a>Oracle WebLogic Server with Admin Server

このオファーでは、単一の仮想マシンがプロビジョニングされ、そこに WLS がインストールされます。 さらに、ドメインが作成され、管理サーバーが起動されて、ドメインの管理が可能になります。

## <a name="oracle-weblogic-server-cluster"></a>Oracle WebLogic Server Cluster

このオファーでは、WLS 仮想マシンの高可用性クラスターが作成されます。 管理サーバーとすべてのマネージド サーバーが既定で起動されて、ドメインの管理が可能になります。

## <a name="oracle-weblogic-server-dynamic-cluster"></a>Oracle WebLogic Server Dynamic Cluster

このオファーでは、可用性が高くてスケーラブルな、WLS 仮想マシンの動的クラスターが作成されます。 管理サーバーとすべてのマネージド サーバーが既定で起動されて、ドメインの管理が可能になります。

## <a name="next-steps"></a>次のステップ

Azure Marketplace で各オファーについて調べます。

> [!div class="nextstepaction"]
> [Oracle WebLogic Server Single Node](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server with Admin Server](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server Cluster](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server Dynamic Cluster](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)
