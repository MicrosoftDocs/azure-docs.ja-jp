---
title: Azure Cosmos DB で Cassandra API を使用してエラスティックにスケールする
description: Azure Cosmos DB Cassandra API アカウントのスケールに使用できるオプションと、それらの長所と短所について説明します
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: thvankra
ms.openlocfilehash: aad2e80598146be7b45a8a7b8a02cfe050163102
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93340959"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>Azure Cosmos DB Cassandra API アカウントをエラスティックにスケーリングする
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB の Cassandra 用 API のエラスティック特性を調べるためのさまざまなオプションがあります。 Azure Cosmos DB で効果的にスケールする方法を理解するには、システムのパフォーマンス要求を考慮して適切な量の要求ユニット (RU/秒) をプロビジョニングする方法を理解することが重要です。 要求ユニットの詳細については、[要求ユニット](request-units.md)に関する記事を参照してください。 

Cassandra API では、[.NET および Java SDK](./find-request-unit-charge-cassandra.md) を使用して、個々のクエリの要求ユニットの料金を取得できます。 これは、サービスでプロビジョニングする必要がある RU/秒の量を決定するのに役立ちます。

:::image type="content" source="./media/request-units/request-units.png" alt-text="データベース操作による要求ユニットの消費" border="false":::

## <a name="handling-rate-limiting-429-errors"></a>レート制限の処理 (429 エラー)

クライアントがプロビジョニングされた量よりも多くのリソースを消費する (RU/秒) と、Azure Cosmos DB によってレート制限 (429) エラーが返されます。 Azure Cosmos DB の Cassandra API は、このような例外を Cassandra ネイティブ プロトコルの過負荷エラーに変換します。 

システムが待機時間の影響を受けない場合は、再試行を使用してスループットのレート制限を処理するだけで十分な場合があります。 Java で [Cassandra 再試行ポリシー](https://docs.datastax.com/en/developer/java-driver/4.4/manual/core/retries/)に [Azure Cosmos DB 拡張機能](https://github.com/Azure/azure-cosmos-cassandra-extensions)を使用して、レート制限を透過的に処理する方法については、[Java のコード サンプル](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample)を参照してください。 [Spark 拡張機能](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper)を使用して、レート制限を処理することもできます。

## <a name="manage-scaling"></a>スケーリングの管理

待機時間を最小限に抑える必要がある場合は、Cassandra API でスケールとプロビジョニングのスループット (RU) を管理するためのさまざまなオプションがあります。

* [Azure portal を使用して手動で](#use-azure-portal)
* [コントロール プレーン機能を使用してプログラムで](#use-control-plane)
* [特定の SDK で CQL コマンドを使用してプログラムで](#use-cql-queries)
* [自動スケーリングを使用して動的に](#use-autoscale)

以降のセクションでは、それぞれの方法の長所と短所について説明します。 その後、ご使用のシステムのスケーリング ニーズ、全体的なコスト、およびソリューションの効率のニーズのバランスを取るための最適な方法を決定できるようになります。

## <a name="use-the-azure-portal"></a><a id="use-azure-portal"></a>Azure portal を使用する

Azure portal を使用して Azure Cosmos DB Cassandra API アカウントのリソースをスケールできます。 詳細については、「[コンテナーとデータベースのスループットのプロビジョニング](set-throughput.md)」を参照してください。 この記事では、Azure portal で[データベース](set-throughput.md#set-throughput-on-a-database)または[コンテナー](set-throughput.md#set-throughput-on-a-container)のいずれかのレベルでスループットを設定することによる相対的利点が説明されています。 これらの記事で言及されている "データベース" と "コンテナー" という用語は、それぞれ Cassandra API の "キースペース" と "テーブル" に対応します。

この方法の利点は、データベースのスループット容量を簡単かつすばやく管理できることです。 ただし、欠点は、多くの場合、スケーリングの方法には、コスト効率と高いパフォーマンスの両方を実現するために、ある程度の自動化が必要になる場合があることです。 以降のセクションでは、関連するシナリオと方法について説明します。

## <a name="use-the-control-plane"></a><a id="use-control-plane"></a>コントロール プレーンを使用する

Azure Cosmos DB の Cassandra 用 API には、さまざまなコントロール プレーン機能を使用して、プログラムでスループットを調整する機能が用意されています。 ガイダンスとサンプルについては、[Azure Resource Manager](./templates-samples-cassandra.md)、[Powershell](powershell-samples.md)、および [Azure CLI](cli-samples.md) に関する記事を参照してください。

この方法の利点は、ピーク時のアクティビティ、またはアクティビティの少ない時間帯を考慮して、タイマーに基づいてリソースのスケールアップまたはスケールダウンを自動化できることです。 Azure Functions と PowerShell を使用してこれを実現する方法については、[こちら](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler)のサンプルをご覧ください。

この方法の欠点は、スケールのニーズの予測不能な変化にリアルタイムで対応できないことです。 代わりに、クライアントまたは SDK レベルで、または[自動スケーリング](provision-throughput-autoscale.md)を使用して、システム内のアプリケーション コンテキストを利用することが必要になる場合があります。

## <a name="use-cql-queries-with-a-specific-sdk"></a><a id="use-cql-queries"></a>特定の SDK で CQL クエリを使用する

指定されたデータベースまたはコンテナーに対して [CQL ALTER コマンド](cassandra-support.md#keyspace-and-table-options)を実行することで、コードを使用してシステムを動的にスケールできます。

この方法の利点は、スケールのニーズに動的に対応でき、しかもご利用のアプリケーションに適したカスタムの方法で対応できることです。 この方法でも、標準 RU/秒の課金とレートを引き続き利用できます。 システムのスケール ニーズがほぼ予測可能 (約 70% 以上) な場合は、CQL で SDK を使用する方が、自動スケーリングを使用した場合よりも自動スケールのコスト効率が高くなる可能性があります。 この方法の欠点は、再試行を実装するのが非常に複雑になる上に、レート制限によって待機時間が長くなる可能性があることです。

## <a name="use-autoscale-provisioned-throughput"></a><a id="use-autoscale"></a>自動スケーリングでプロビジョニングされたスループットを使用する

標準 (手動) またはプログラムによるスループットのプロビジョニングの方法に加えて、自動スケーリングでプロビジョニングされたスループットでも Azure Cosmos のコンテナーを構成することができます。 自動スケーリングでは、SLA を損なうことなく、消費ニーズに合わせて、指定された RU 範囲内で自動的かつ即座にスケールが行われます。 詳細については、[自動スケーリングで Azure Cosmos のコンテナーとデータベースを作成する](provision-throughput-autoscale.md)方法に関する記事を参照してください。

この方法の利点は、これがシステムのスケーリング ニーズを管理する最も簡単な方法だということです。 これによりレート制限が **構成された RU 範囲内** に適用されません。 欠点は、システムのスケーリング ニーズが予測可能な場合、自動スケーリングは、スケーリング ニーズを処理する方法としては、前述のカスタムのコントロール プレーンまたは SDK レベルのアプローチを使用するよりもコスト効率に劣る可能性があることです。

CQL を使用して自動スケーリングの最大スループット (RU) を設定または変更するには、次を使用します (キースペース/テーブル名を適宜置き換えてください)。

```Bash
# to set max throughput (RUs) for autoscale at keyspace level:
create keyspace <keyspace name> WITH cosmosdb_autoscale_max_throughput=5000;

# to alter max throughput (RUs) for autoscale at keyspace level:
alter keyspace <keyspace name> WITH cosmosdb_autoscale_max_throughput=4000;

# to set max throughput (RUs) for autoscale at table level:
create table <keyspace name>.<table name> (pk int PRIMARY KEY, ck int) WITH cosmosdb_autoscale_max_throughput=5000;

# to alter max throughput (RUs) for autoscale at table level:
alter table <keyspace name>.<table name> WITH cosmosdb_autoscale_max_throughput=4000;
```

## <a name="next-steps"></a>次のステップ

- Java アプリケーションを使用した[ Cassandra API アカウント、データベースおよびテーブルの作成](create-cassandra-api-account-java.md)の開始