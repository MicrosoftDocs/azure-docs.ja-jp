---
title: Azure Kubernetes Service で Oracle WebLogic Server を実行するためのソリューションとは
description: Azure Kubernetes Service で Oracle WebLogic Server を実行する方法について説明します。
author: rezar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 02/23/2021
ms.author: rezar
ms.reviewer: cynthn
ms.openlocfilehash: ac9f81fbde33bdd10bc8374a566a4f2ba83fc253
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101669037"
---
# <a name="what-are-solutions-for-running-oracle-weblogic-server-on-the-azure-kubernetes-service"></a>Azure Kubernetes Service で Oracle WebLogic Server を実行するためのソリューションとは

このページでは、Azure Kubernetes Service (AKS) で Oracle WebLogic Server (WLS) を実行するためのソリューションについて説明します。 これらのソリューションは、Oracle と Microsoft が共同で開発およびサポートしています。

WebLogic Server を Azure Virtual Machines で実行することもできます。 これを行うためのソリューションについては、[こちらの Microsoft の記事](./oracle-weblogic.md)を参照してください。

WebLogic Server は、世界中の最もミッション クリティカルなエンタープライズ Java アプリケーションを複数実行している最先端の Java アプリケーション サーバーです。 WebLogic Server は、Oracle ソフトウェア スイートのミドルウェア基盤を形成しています。 Oracle と Microsoft は、最先端のクラウド プラットフォームとして Azure 上でワークロードを実行するための選択肢と柔軟性を WebLogic Server の顧客に与えることに取り組んでいます。

## <a name="wls-on-aks-certified-and-supported"></a>AKS 上の WLS の認定とサポート
WebLogic Server は、AKS で正常に動作することが Oracle と Microsoft によって認定されています。 AKS 上の WebLogic Server ソリューションは、Docker および Kubernetes インフラストラクチャ上でコンテナー化されて調整されている Java アプリケーションを可能な限り簡単に実行できるようにすることを目的としています。 これらのソリューションは、信頼性、スケーラビリティ、管理容易性、エンタープライズ サポートに重点を置いています。

Kubernetes での WebLogic Server クラスターの実行は、WebLogic Kubernetes Operator (ここでは単に "Operator" と呼びます) を介して完全に有効になります。 Operator は、標準の Kubernetes Operator のパターンに従います。 手動のタスクを自動化し、運用の信頼性に関する機能をさらに追加することで、Kubernetes 上の WebLogic ドメインとデプロイの管理と運用を簡素します。 Operator によって Oracle WebLogic Server 12c、Oracle Fusion Middleware Infrastructure 12c 以降がサポートされています。 WebLogic Server 12.2.1.3 および 12.2.1.4 用の公式 Docker イメージを Operator でテスト済みです。 Operator の詳細については、[Oracle の公式ドキュメント](https://oracle.github.io/weblogic-kubernetes-operator/)を参照してください。

## <a name="guidance-scripts-and-samples-for-wls-on-aks"></a>AKS 上の WLS のガイダンス、スクリプト、サンプル
Oracle と Microsoft は、AKS 上の WebLogic Server を認定するだけでなく、AKS 上で WebLogic Server を実行するための詳細な手順、スクリプト、サンプルを共同で提供しています。 このガイダンスは、[Operator ドキュメント](https://oracle.github.io/weblogic-kubernetes-operator/samples/simple/azure-kubernetes-service/)の Azure Kubernetes Service サンプルのセクションに組み込まれています。 このガイダンスは、AKS デプロイでの運用環境の WebLogic Server をできるだけ簡単にすることを目的としています。 このガイダンスでは、Oracle によって提供される公式の WebLogic Server Docker イメージを使用しています。 フェールオーバーは、Kubernetes 永続ボリューム要求を介してアクセスされる Azure Files によって実現されます。 Azure Load Balancer は、種類が 'LoadBalancer' の Kubernetes Service を使用してプロビジョニングされた場合にサポートされます。 Azure Container Registry (ACR) は、カスタム Docker イメージ内の WLS ドメインのデプロイでサポートされています。 このガイダンスでは、高レベルの構成とカスタマイズが可能です。

:::image type="content" source="media/oracle-weblogic/wls-on-aks.gif" alt-text="サンプル スクリプトを使用して AKS に WebLogic Server をデプロイできる":::

これらのソリューションには、WLS ドメインを AKS にデプロイするための方法が 2 つ含まれています。 ドメインは、Kubernetes 永続ボリュームに直接デプロイできます。 このデプロイ オプションは、AKS に移行する一方で、WLS の管理には管理コンソールまたは WebLogic Scripting Tool (WLST) を引き続き使用する場合に適しています。 このオプションを使用すると、Docker 開発を採用せずに AKS に移行することもできます。 WLS ドメインを AKS にデプロイする際、より Kubernetes ネイティブな方法は、Oracle Container Registry からの公式の WLS イメージに基づいてカスタム Docker イメージを構築し、そのカスタム イメージを ACR に発行し、Operator　を使用してドメインを AKS にデプロイすることです。 このオプションをソリューションで使用すると、デプロイの完了後に Kubernetes ConfigMaps を使用してドメインを更新することもできます。

Azure Virtual Machines 上の Oracle WebLogic Server ソリューションをミラーリングする Marketplace オファリングによって、今後さらなる使いやすさと Azure サービス統合が実現可能になります。

"_これらのソリューションはライセンス持ち込み型です_"。 ここでは、Oracle で既に適切なライセンスを取得していること、および Azure でオファーを実行するための適切なライセンスをお持ちであることを前提としています。

"_これらのソリューションを開発しているエンジニアリング チームと密接に連携しながら移行シナリオに取り組むことに関心がある場合は、[こちらの簡単なアンケート](https://aka.ms/wls-on-azure-survey)に記入し、連絡先情報を入力してください_"。 プログラム マネージャー、アーキテクト、エンジニアからすぐに連絡があり、密接なコラボレーションが開始されます。 移行シナリオで共同作業を行う機会は、ソリューションの初期開発が進められている間は無料です。

## <a name="deployment-architectures"></a>デプロイ アーキテクチャ

Azure Kubernetes Service で Oracle WebLogic Server を実行するためのソリューションによって、運用環境に対応したさまざまなデプロイ アーキテクチャを比較的簡単に実現できるようになります。

:::image type="content" source="media/oracle-weblogic/weblogic-architecture-aks.png" alt-text="複雑な WebLogic Server のデプロイが AKS 上で実現される":::

顧客は、ソリューションによって提供されるもの以外に、さらにデプロイをカスタマイズできる完全な柔軟性を得ます。 おそらく顧客は、アプリケーションのデプロイに加え、Azure リソースをそれらのデプロイと統合します。 ソリューションのさらなる改善に関するフィードバックをアンケートで提供することをお勧めします。

## <a name="next-steps"></a>次のステップ

Azure Kubernetes Service での Oracle WebLogic Server の実行について確認します。

> [!div class="nextstepaction"]
> [AKS で WLS を実行するためのガイダンス、スクリプト、サンプル](https://oracle.github.io/weblogic-kubernetes-operator/samples/simple/azure-kubernetes-service/)

> [!div class="nextstepaction"]
> [WebLogic Kubernetes Operator](https://oracle.github.io/weblogic-kubernetes-operator/)
