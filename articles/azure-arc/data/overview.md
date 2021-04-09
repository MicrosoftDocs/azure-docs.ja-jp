---
title: Azure Arc 対応データ サービスとは
description: Azure Arc 対応データ サービスの概要
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 09/22/2020
ms.topic: overview
ms.openlocfilehash: ef16a4c0b6ea40fb4934307916f1fe79ccea72f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97609141"
---
# <a name="what-are-azure-arc-enabled-data-services-preview"></a>Azure Arc 対応データ サービス (プレビュー) とは

Azure Arc を使用すると、Kubernetes と選択したインフラストラクチャを使用して、オンプレミス、エッジ、パブリック クラウドで Azure データ サービスを実行できます。

現在、次の Azure Arc 対応データ サービスのプレビュー版を利用できます。

- SQL Managed Instance
- PostgreSQL Hyperscale

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="always-current"></a>常に最新

Azure Arc 対応 SQL Managed Instance や Azure Arc 対応 PostgreSQL Hyperscale などの Azure Arc 対応データ サービスでは、Azure でのエクスペリエンスと同様に、サービス パッチや新機能などの更新が頻繁に行われます。 Microsoft Container Registry からの更新プログラムが提供され、デプロイ頻度がポリシーに従って設定されます。 これにより、オンプレミスのデータベースを最新の状態に保ちながら、制御を維持することができます。 Azure Arc 対応データ サービスはサブスクリプション サービスであるため、データベースのサポート終了に直面する状況はなくなります。

## <a name="elastic-scale"></a>エラスティック スケール

クラウドに似た柔軟性をオンプレミスで利用することで、インフラストラクチャの利用可能容量に基づいて、Azure 内とほぼ同じ方法でデータベースを動的にスケールアップまたはスケールダウンできます。 この機能により、リアルタイム、任意のスケール、1 秒未満の応答時間でのデータの取り込みやクエリを必要とするシナリオなど、揮発性のニーズを持つバースト シナリオに対応することができます。 さらに、Azure Database for PostgreSQL Hyperscale の固有のハイパー スケール デプロイ オプションを使用して、データベース インスタンスをスケールアウトすることもできます。 この機能により、データ ワークロードの容量の最適化が向上します。これには、一意のスケール "*アウト*" 読み取りと書き込みが使用されます。

## <a name="self-service-provisioning"></a>セルフサービス プロビジョニング

Azure Arc には、高速デプロイや大規模な自動化など、クラウドの利点が他にもあります。 Kubernetes ベースのオーケストレーションにより、GUI または CLI のいずれかのツールを使用して、データベースを数秒でデプロイできます。

## <a name="unified-management"></a>統合された管理

Azure portal、Azure Data Studio、[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] などの使い慣れたツールを使用して、Azure Arc によってデプロイされたすべてのデータ資産の統合ビューを取得できるようになりました。お使いの環境と Azure でさまざまなリレーショナル データベースを表示および管理できるだけでなく、Kubernetes API からログとテレメトリを取得して、基盤となるインフラストラクチャの容量と正常性を分析することもできます。 ローカライズされたログ分析とパフォーマンスの監視に加えて、Azure Monitor を利用して、資産全体にわたる総合的なオペレーション インサイトを実現できるようになりました。

## <a name="disconnected-scenario-support"></a>切断されたシナリオのサポート

セルフサービス プロビジョニング、自動バックアップ/復元、監視などのサービスの多くは、Azure への直接接続を使用して、または使用せずに、インフラストラクチャ内でローカルに実行できます。 Azure に直接接続すると、Azure Monitor などの他の Azure サービスとの統合に関する追加のオプションを利用でき、世界中のどこからでも Azure portal と Azure Resource Manager API を使用して Azure Arc 対応データ サービスを管理することができます。

## <a name="next-steps"></a>次のステップ

> **試してみたい場合**  
> Azure Kubernetes Service (AKS)、AWS Elastic Kubernetes Service (EKS)、Google Cloud Kubernetes Engine (GKE)、または Azure VM 上で [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) をすぐに開始できます。

[クライアント ツールをインストールする](install-client-tools.md)

[Azure Arc データ コントローラーを作成する](create-data-controller.md) (先にクライアント ツールをインストールする必要があります)

[Azure Arc で Azure SQL マネージド インスタンスを作成する](create-sql-managed-instance.md) (先に Azure Arc データ コントローラーを作成する必要があります)

[Azure Arc に Azure Database for PostgreSQL Hyperscale サーバー グループを作成する](create-postgresql-hyperscale-server-group.md) (先に Azure Arc データ コントローラーを作成する必要があります)
