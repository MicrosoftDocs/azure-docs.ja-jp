---
title: Azure Application Gateway イングレス コントローラーとは
description: この記事では、Application Gateway イングレス コントローラーの概要について説明します。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: c1bd41587e4f56fb0a7f3eb8285d301751f558d1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84668102"
---
# <a name="what-is-application-gateway-ingress-controller"></a>Application Gateway イングレス コントローラーとは
Application Gateway イングレス コントローラー (AGIC) は Kubernetes アプリケーションであり、これにより、[Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) のお客様が、Azure のネイティブ [Application Gateway](https://azure.microsoft.com/services/application-gateway/) L7 ロード バランサーを活用してクラウド ソフトウェアをインターネットに公開できるようにします。 AGIC では、ホストされている Kubernetes クラスターを監視し、Application Gateway を継続的に更新して、選択されたサービスがインターネットに公開されるようにします。

イングレス コントローラーは、お客様の AKS の独自のポッドで実行されます。 AGIC では、Kubernetes リソースのサブセットに変更がないかを監視します。 AKS クラスターの状態は Application Gateway の特定の構成に変換され、[Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) に適用されます。

## <a name="benefits-of-application-gateway-ingress-controller"></a>Azure Application Gateway イングレス コントローラーの利点
AGIC を使用すると、AKS クラスターの前に別のロード バランサー/パブリック IP を用意する必要がなくなり、要求が AKS クラスターに到達する前に、データパス内の複数のホップを回避できます。 Application Gateway は、プライベート IP を直接使用してポッドと通信し、NodePort および KubeProxy サービスを必要としません。 これにより、デプロイのパフォーマンスも向上します。

イングレス コントローラーは、Standard_v2 および WAF_v2 SKU でのみサポートされ、自動スケールの利点も得られます。 Application Gateway は、トラフィックの負荷の増減に反応し、それに応じてスケールでき、ご利用の AKS クラスターのリソースを消費しません。

AGIC に加えて Application Gateway を使用すると、TLS ポリシーと Web アプリケーション ファイアウォール (WAF) 機能を提供して、ご利用の AKS クラスターを保護することもできます。

![Azure Application Gateway + AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

AGIC は、サービスおよびデプロイ/ポッドと共に、Kubernetes の[イングレス リソース](https://kubernetes.io/docs/user-guide/ingress/)を使用して構成されます。 Azure のネイティブ Application Gateway L7 ロード バランサーを活用したさまざまな機能が用意されています。 以下にいくつか例を挙げます。
  - URL ルーティング
  - Cookie ベースのアフィニティ
  - TLS 終了
  - エンドツーエンド TLS
  - パブリック、プライベート、ハイブリッド Web サイトのサポート
  - Web アプリケーション ファイアウォールを統合

## <a name="difference-between-helm-deployment-and-aks-add-on"></a>Helm デプロイと AKS アドオンの違い
AKS クラスター用の AGIC をデプロイする方法は 2 つあります。 最初の方法は、Helm を通すことです。2 つ目は、AKS をアドオンとして使用することです。 AGIC を AKS アドオンとしてデプロイすることの主な利点は、Helm を通したデプロイよりもはるかにシンプルであることです。 新しいセットアップでは、Azure CLI の 1 つの行で、新しいアプリケーション ゲートウェイと AKS クラスターを、アドオンとして有効にした AGIC と共にデプロイできます。 アドオンは、フル マネージド サービスでもあり、自動更新やサポートの強化などの追加のメリットが提供されます。 Helm を通してデプロイされた AGIC は AKS によってサポートされませんが、AKS アドオンとしてデプロイされた AGIC は AKS によってサポートされます。 

AGIC アドオンは顧客の AKS クラスターにポッドとしてデプロイされますが、AGIC の Helm デプロイ バージョンとアドオン バージョンにはいくつかの違いがあります。 この 2 つのバージョンの違いを次に示します。 
  - AKS アドオンで Helm デプロイの値を変更することはできません。
    - `verbosityLevel` は、既定では 5 に設定される
    - `usePrivateIp` は、既定では false に設定される。これは [use-private-ip](ingress-controller-annotations.md#use-private-ip) 注釈によって上書き可能
    - `shared` は、アドオンではサポートされない 
    - `reconcilePeriodSeconds` は、アドオンではサポートされない
    - `armAuth.type` は、アドオンではサポートされない
  - Helm を通してデプロイされた AGIC では ProhibitedTargets がサポートされます。つまり、AGIC では、他の既存のバックエンドに影響を与えずに、AKS クラスター専用のアプリケーション ゲートウェイを構成できます。 AGIC アドオンでは、現時点ではこれはサポートされていません。 
  - AGIC アドオンはマネージド サービスであるため、顧客の AGIC アドオンは自動的に最新バージョンに更新されます。これは、顧客が手動で更新する必要がある Helm を通してデプロイされた AGIC とは異なります。 

> [!NOTE]
> AGIC の AKS アドオンでのデプロイ方法は、現在プレビューの段階です。 まだプレビュー段階にある機能に対して運用環境のワークロードを実行することはお勧めしません。そのため、試してみる場合は、新しいクラスターをセットアップしてテストすることをお勧めします。 

次の表に、AGIC の Helm デプロイ バージョンと AKS アドオン バージョンで現在サポートされているシナリオの分類を示します。 

### <a name="aks-add-on-agic-single-aks-cluster"></a>AKS アドオンの AGIC (単一の AKS クラスター)
|                  |1 つのアプリケーション ゲートウェイ |2 つ以上のアプリケーション ゲートウェイ |
|------------------|---------|--------|
|**1 つの AGIC**|はい、これはサポートされます |いいえ、これはバックログに入っています |
|**2 つ以上の AGIC**|いいえ。クラスターごとにサポートされる AGIC は 1 つだけです |いいえ。クラスターごとにサポートされる AGIC は 1 つだけです |

### <a name="helm-deployed-agic-single-aks-cluster"></a>Helm でデプロイされた AGIC (単一の AKS クラスター)
|                  |1 つのアプリケーション ゲートウェイ |2 つ以上のアプリケーション ゲートウェイ |
|------------------|---------|--------|
|**1 つの AGIC**|はい、これはサポートされます |いいえ、これはバックログに入っています |
|**2 つ以上の AGIC**|共有された ProhibitedTarget 機能を使用して別の名前空間を監視する必要があります |はい、これはサポートされます |

### <a name="helm-deployed-agic-2-aks-clusters"></a>Helm でデプロイされた AGIC (2 つ以上の AKS クラスター)
|                  |1 つのアプリケーション ゲートウェイ |2 つ以上のアプリケーション ゲートウェイ |
|------------------|---------|--------|
|**1 つの AGIC**|該当なし |該当なし |
|**2 つ以上の AGIC**|共有された ProhibitedTarget 機能を使用する必要があります |該当なし |

## <a name="next-steps"></a>次のステップ
- [**AKS アドオンのグリーンフィールド デプロイ**](tutorial-ingress-controller-add-on-new.md):白紙状態のインフラストラクチャに、AGIC アドオン、AKS、Application Gateway をインストールする手順です。
- [**AKS アドオンのブラウンフィールド デプロイ**](tutorial-ingress-controller-add-on-existing.md):既存のアプリケーション ゲートウェイがある AKS クラスターに AGIC アドオンをインストールします。
- [**Helm のグリーンフィールド デプロイ**](ingress-controller-install-new.md):白紙状態のインフラストラクチャに、Helm を通した AGIC、新しい AKS クラスター、および新しい Application Gateway をインストールします。
- [**Helm のブラウンフィールド デプロイ**](ingress-controller-install-existing.md):既存の AKS クラスターとアプリケーション ゲートウェイに、Helm を通して AGIC をデプロイします。

