---
title: Azure Red Hat OpenShift の概要
description: コンテナーベースのアプリケーションをデプロイして管理するための、Microsoft Azure Red Hat OpenShift の機能とメリットについて説明します。
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: overview
ms.date: 11/13/2020
ms.custom: mvc
ms.openlocfilehash: ec934a8a9907f0c0c3aff047e003c6725c5ab25f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100636222"
---
# <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft *Azure Red Hat OpenShift* サービスを使用すると、フル マネージドの [OpenShift](https://www.openshift.com/) クラスターをデプロイできます。

Azure Red Hat OpenShift は [Kubernetes](https://kubernetes.io/) を拡張します。 Kubernetes を使用した運用環境でコンテナーを実行するには、追加のツールとリソースが必要になります。 これには、多くの場合、イメージ レジストリ、ストレージ管理、ネットワーク ソリューション、ログ記録と監視のツールなどを操作する必要があります。これらはすべて、まとめてバージョン管理およびテストする必要があります。 コンテナーベースのアプリケーションを構築するには、さらにミドルウェア、フレームワーク、データベース、CI/CD ツールとの統合作業も必要です。 Azure Red Hat OpenShift は、これらすべてを 1 つのプラットフォームに統合することで、IT チームにとって運用が容易になると共に、アプリケーション チームには実行すべきことが指定されます。

Azure Red Hat OpenShift は、Red Hat と Microsoft が共同で設計、運用、サポートを行い、統合されたサポート エクスペリエンスを実現しています。 操作すべき仮想マシンはなく、修正プログラムの適用も必要ありません。 マスター、インフラストラクチャ、およびアプリケーション ノードの修正プログラムの適用、更新、監視は、ユーザーに代わって Microsoft と Red Hat が行っています。 Azure Red Hat OpenShift クラスターは、Azure サブスクリプションにデプロイされ、Azure の課金内容に含まれます。

自動でのソース コード管理、コンテナーとアプリケーションのビルド、デプロイ、スケーリング、正常性の管理などには、独自のレジストリ、ネットワーク、ストレージ、CI/CD のソリューションを選択するか、組み込みのソリューションを使用することができます。 Azure Red Hat OpenShift は、Azure Active Directory を通じて、統合されたサインオン エクスペリエンスを提供します。

最初に、[Azure Red Hat OpenShift クラスターの作成](tutorial-create-cluster.md)に関するチュートリアルを完了してください。

## <a name="access-security-and-monitoring"></a>アクセス、セキュリティ、および監視

セキュリティと管理の強化のため、Azure Red Hat OpenShift では、Azure Active Directory (Azure AD) と統合して、Kubernetes のロールベースのアクセス制御 (Kubernetes RBAC) を使用することができます。 また、クラスターとリソースの正常性を監視することもできます。

## <a name="cluster-and-node"></a>クラスターとノード

Azure Red Hat OpenShift ノードは Azure 仮想マシン上で実行されます。 ストレージをノードやポッドに接続し、クラスター コンポーネントをアップグレードすることができます。

## <a name="service-level-agreement"></a>サービス レベル アグリーメント

Azure Red Hat OpenShift では、サービスが 99.95% の時間利用できることを保証するサービス レベル アグリーメントを提供しています。 SLA の詳細については、[Azure Red Hat OpenShift の SLA](https://azure.microsoft.com/en-au/support/legal/sla/openshift/v1_0/) に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Azure Red Hat OpenShift の前提条件を確認してください。

> [!div class="nextstepaction"]
> [開発環境のセットアップ](tutorial-create-cluster.md)
