---
title: Arc 対応 PostgreSQL Hyperscale サーバー グループを使用したデータ分散とスケールアウトの概念
titleSuffix: Azure Arc enabled data services
description: Arc 対応 PostgreSQL Hyperscale サーバー グループを使用したデータ分散の概念
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: c01da4aed9e27296ea7b570420bb190b16749848
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90931547"
---
# <a name="concepts-for-distributing-data-with-arc-enabled-postgresql-hyperscale-server-group"></a>Arc 対応 PostgreSQL Hyperscale サーバー グループを使用したデータ分散の概念

この記事では、Azure Arc 対応 PostgreSQL Hyperscale から最大のメリットを得るために重要な主な概念について説明します。
以下のリンク先の記事では、Azure Database for PostgreSQL Hyperscale (Citus) について説明した概念を参照しています。 これは Azure Arc 対応 PostgreSQL Hyperscale と同じテクノロジであるため、同じ概念と分析観点が適用されます。

**両者の相違点**
- _Azure Database for PostgreSQL Hyperscale (Citus)_

これは、Azure でのサービスとしてのデータベース (PaaS) として使用できる Postgres データベース エンジンのハイパースケール フォーム ファクターです。 Hyperscale エクスペリエンスを有効にする Citus 拡張を備えています。 このフォーム ファクターでは、サービスは Microsoft のデータセンターで実行され、Microsoft によって運用されます。

- _Azure Arc 対応 PostgreSQL Hyperscale_

これは、Azure Arc 対応データ サービスで提供される Postgres データベース エンジンのハイパースケール フォーム ファクターです。 このフォームファクターでは、システムをホストするインフラストラクチャを お客様ご自身で用意し、運用していただきます。

Azure Arc 対応 PostgreSQL Hyperscale に関する主な概念を以下にまとめます。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="nodes-and-tables"></a>ノードとテーブル
Azure Arc 対応 PostgreSQL Hyperscale から最大のメリットを得るには、次の概念について理解しておくことが重要です。
- Azure Arc 対応 PostgreSQL Hyperscale の特殊な Postgres ノード: コーディネーターとワーカー
- テーブルの種類: 分散テーブル、参照テーブル、およびローカル テーブル
- シャード

詳しくは、「[Azure Database for PostgreSQL のノードとテーブル - Hyperscale (Citus)](../../postgresql/concepts-hyperscale-nodes.md)」をご覧ください。 

## <a name="determine-the-application-type"></a>アプリケーションの種類の特定
構築するアプリケーションの種類を明確に特定することが重要です。 なぜですか? Azure Arc 対応 PostgreSQL Hyperscale サーバー グループに対して効率的なクエリを実行するには、テーブルがサーバー間で適切に分散されている必要があるからです。 推奨される分散は、アプリケーションの種類とそのクエリ パターンによって異なります。 Azure Arc 対応 PostgreSQL Hyperscale で適切に機能するアプリケーションの種類は大きく 2 つあります。
- マルチテナント アプリケーション
- リアルタイム アプリケーション

データ モデリングの最初の手順は、アプリケーションにより近く似ているものを特定することです。

詳しくは、「[アプリケーションの種類の判断](../../postgresql/concepts-hyperscale-app-type.md)」をご覧ください。


## <a name="choose-a-distribution-column"></a>ディストリビューション列の選択
分散列を選択する理由

これは、モデリングに関する最も重要な決定事項の 1 つです。 Azure Arc 対応 PostgreSQL Hyperscale では、行のディストリビューション列の値に基づいて、行をシャードに格納します。 適切な選択では、同じ物理ノード上の関連データがまとめてグループ化され、クエリが高速になり、すべての SQL 機能のサポートが追加されます。 不適切な選択では、システムの動作が遅くなり、ノード全体ですべての SQL 機能をサポートできなくなります。 この記事では、最も一般的な 2 つのハイパースケール シナリオに関するディストリビューション列のヒントを紹介します。

詳しくは、[ディストリビューション列の選択](../../postgresql/concepts-hyperscale-choose-distribution-column.md)に関する記事をご覧ください。


## <a name="table-colocation"></a>テーブル コロケーション

コロケーションの目的は、同じノード上に関連情報をまとめて格納することです。 ネットワーク トラフィックを使用せずに必要なすべてのデータを入手できる場合に、クエリは高速化できます。 関連データを異なるノードに併置することで、各ノードでクエリを並列に効率的に実行できます。

詳しくは、[テーブル コロケーション](../../postgresql/concepts-hyperscale-colocation.md)に関する記事をご覧ください。


## <a name="next-steps"></a>次のステップ
- [Azure Arc 対応 PostgreSQL Hyperscale の作成について確認する](create-postgresql-hyperscale-server-group.md)
- [Arc データ コントローラーに作成された Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのスケールアウトについて確認する](scale-out-postgresql-hyperscale-server-group.md)
- [Azure Arc 対応データ サービスについて確認する](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
- [Azure Arc について確認する](https://aka.ms/azurearc)

