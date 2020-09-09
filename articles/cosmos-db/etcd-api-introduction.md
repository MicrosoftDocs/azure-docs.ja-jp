---
title: Azure Cosmos DB の etcd API の概要
description: この記事では、Azure Cosmos DB の etcd API の概要と主な利点について説明します。
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 208f0d73b4c8bb11adc9c73796ebc6a622f5ed50
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85118170"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Azure Cosmos DB の etcd API (プレビュー) の概要

Azure Cosmos DB は、Microsoft のミッションクリティカル アプリケーション向けグローバル分散型マルチモデル データベース サービスです。 ターンキーのグローバル配布、スループットとストレージの柔軟なスケール、99 パーセンタイルの 1 桁ミリ秒の待機時間、高可用性の保証というすべての機能が業界最先端の SLA に裏付けられています。

[etcd](https://github.com/etcd-io/etcd) は分散キー/値ストアです。 [Kubernetes](https://kubernetes.io/) では、etcd を使用して Kubernetes クラスターの状態と構成を保存します。 etcd の可用性、信頼性、パフォーマンスを確保することは、クラスター全体の正常性、スケーラビリティ、弾力性の可用性、Kubernetes クラスターのパフォーマンスにとって非常に重要です。

Azure Cosmos DB の etcd API では、Azure Cosmos DB を Azure Kubernetes のバックエンド ストアとして使用できます。 Azure Cosmos DB の etcd API は現在プレビュー段階にあります。 Azure Cosmos DB は etcd ワイヤ プロトコルを実装しています。 Azure Cosmos DB の etcd API を使用すると、開発者は信頼性、[可用性](high-availability.md)が高く、[グローバルに分散されている](distribute-data-globally.md) Kubernetes を自動的に利用できるようになります。 この API により、開発者はフル マネージド クラウドのネイティブ PaaS サービス上で Kubernetes の状態管理をスケールできます。 

> [!NOTE]
> Azure Cosmos DB の他の API とは異なり、Azure portal、CLI、または SDK を介して etcd API アカウントをプロビジョニングすることはできません。 etcd API アカウントをプロビジョニングするには、Resource Manager テンプレートのみをデプロイします。詳細な手順については、[Azure Cosmos DB を使用して Azure Kubernetes をプロビジョニングする方法](bootstrap-kubernetes-cluster.md)に関する記事を参照してください。 Azure Cosmos DB etcd API は現在、限定プレビュー段階です。 サインアップ フォームに入力することで、[プレビューにサインアップする](https://aka.ms/cosmosetcdapi-signup)ことができます。

## <a name="wire-level-compatibility"></a>ワイヤ レベルの互換性

Azure Cosmos DB は etcd バージョン 3 のワイヤ プロトコルを実装しており、[マスター ノード](https://kubernetes.io/docs/concepts/overview/components/)の API サーバーがローカルにインストールされた etcd 環境と同じように Azure Cosmos DB を使用できるようになります。 etcd API は TLS 相互認証をサポートしています。 

次の図は、Kubernetes クラスターのコンポーネントを示しています。 クラスター マスターでは、API Server はローカルにインストールされた etcd ではなく Azure Cosmos DB etcd API を使用します。 

:::image type="content" source="./media/etcd-api-introduction/etcd-api-wire-protocol.png" alt-text="etcd ワイヤ プロトコルを実装する Azure Cosmos DB" border="false":::

## <a name="key-benefits"></a>主な利点

### <a name="no-etcd-operations-management"></a>etcd 操作の管理がない

Azure Cosmos DB は、フル マネージド ネイティブ クラウド サービスなので、Kubernetes 開発者が etcd を設定および管理する必要はありません。 Azure Cosmos DB の etcd API は、スケーラブル、高可用、フォールト トレラントであり、ハイ パフォーマンスを提供します。 複数のノードにまたがるレプリケーションの設定、更新プログラムの展開の実行、セキュリティ パッチ、および etcd の正常性の監視のオーバーヘッドは、Azure Cosmos DB が処理します。

### <a name="global-distribution--high-availability"></a>グローバルな分散と高可用性 

etcd API を使用することで、Azure Cosmos DB は単一リージョン内のデータの読み取りと書き込みには 99.99% の可用性を、複数のリージョン全体では 99.999% の可用性を保証します。 

### <a name="elastic-scalability"></a>柔軟なスケーラビリティ

Azure Cosmos DB は、複数のリージョン全体の読み取りおよび書き込み要求に対して柔軟なスケーラビリティを提供します。
Kubernetes クラスターが大きくなるにつれて、Azure Cosmos DB の etcd API アカウントはダウンタイムなしで柔軟にスケールします。 Kubernetes マスター ノードではなく Azure Cosmos DB に etcd データを格納することで、マスター ノードのスケールもより柔軟になります。 

### <a name="security--enterprise-readiness"></a>セキュリティとエンタープライズの準備

etcd データが Azure Cosmos DB に格納されると、Kubernetes 開発者は、Azure Cosmos DB がサポートしている[保存時の組み込みの暗号化](database-encryption-at-rest.md)、[認定と準拠](compliance.md)、および[バックアップ機能と復元機能](online-backup-and-restore.md)を自動的に利用できるようになります。 

## <a name="next-steps"></a>次のステップ

* [Azure Cosmos DB で Azure Kubernetes を使用する方法](bootstrap-kubernetes-cluster.md)
* [Azure Cosmos DB の主な利点](introduction.md)
* [AKS エンジン クイックスタート ガイド](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)