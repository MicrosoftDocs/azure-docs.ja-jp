---
title: Apache Spark の主要概念
description: この記事では、Azure Synapse Analytics の Apache Spark の概要と、さまざまな概念について説明します。
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 806f4dff49e9650dba073721109e7d54a18ecbbe
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87052327"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Azure Synapse Analytics の Apache Spark の主要な概念

Apache Spark は、ビッグデータ分析アプリケーションのパフォーマンスを向上させるメモリ内処理をサポートする並列処理フレームワークです。 Azure Synapse Analytics の Apache Spark は、Apache Spark を Microsoft がクラウドに実装したものです。 

Azure Synapse では、Spark の機能を Azure に簡単に作成して構成することができます。 Azure Synapse には異なる Spark 機能の実装が備わっており、このドキュメントではそれらの機能について取り上げています。

## <a name="spark-pools-preview"></a>Spark プール (プレビュー)

Spark プール (プレビュー) は Azure portal で作成します。 インスタンス化時、データを処理する Spark インスタンスの作成には、Spark プールの定義が使用されます。 作成した Spark プールはメタデータとしてのみ存在します。つまり、リソースが消費されたり実行されたりすることはなく、課金されることもありません。 Spark プールには、Spark インスタンスの特性を制御する一連のプロパティがあります。たとえば、名前やサイズ、スケーリングの動作、Time to Live といった特性がその例です。

Spark プールは、その作成に関して料金もリソース コストも発生しないので、異なる構成でいくつでも作成することができます。 Spark プールにアクセス許可を適用することで、ユーザーが利用できる Spark プールを限定することもできます。

ベスト プラクティスとして、開発とデバッグ用に作成する Spark プールは小さく、運用環境のワークロードの実行用に作成する Spark プールは大きくすることをお勧めします。

Spark プールの作成方法とそのすべてのプロパティは、[Synapse Analytics の Spark プールの概要](../quickstart-create-apache-spark-pool-portal.md)に関するページでご覧いただけます。

## <a name="spark-instances"></a>Spark インスタンス

Spark インスタンスは、Spark プールに接続してセッションを作成し、ジョブを実行するときに作成されます。 1 つの Spark プールを複数のユーザーが利用するときは、接続するユーザーごとに新しい Spark インスタンスが作成されます。 

別のジョブを送信したとき、プールにキャパシティがあり、既存の Spark インスタンスにもキャパシティがあれば、そのジョブは既存のインスタンスで処理されます。プールにキャパシティはないものの、プール レベルでキャパシティがあれば、新しい Spark インスタンスが作成されます。

## <a name="examples"></a>例

### <a name="example-1"></a>例 1

- SP1 という Spark プールを作成します。このプールのクラスター サイズは 20 ノード固定です。
- 10 ノードを使用するノートブック ジョブ (J1) を送信すると、そのジョブを処理する Spark インスタンス (SI1) が作成されます。
- ここで、10 ノードを使用する別のジョブ (J2) を送信すると、そのプールとインスタンスにはまだキャパシティがあるため、J2 は SI1 によって処理されます。
- このとき、仮に J2 が 11 ノードを要求した場合、SP1 にも SI1 にもキャパシティは存在しません。 その場合、J2 がノートブックからのジョブであれば J2 は拒否され、バッチ ジョブからのジョブであれば J2 はキューに格納されます。

### <a name="example-2"></a>例 2

- 10 から 20 ノードの自動スケーリングを有効にした SP2 という Spark プールを作成します。
- 10 ノードを使用するノートブック ジョブ (J1) を送信すると、そのジョブを処理する Spark インスタンス (SI1) が作成されます。
- ここで、10 ノードを使用する別のジョブ (J2) を送信すると、そのプールとインスタンスにはまだキャパシティがあるため、インスタンスは自動的に 20 ノードに拡張され、そこで J2 が処理されます。

### <a name="example-3"></a>例 3

- SP1 という Spark プールを作成します。このプールのクラスター サイズは 20 ノード固定です。
- 10 ノードを使用するノートブック ジョブ (J1) を送信すると、そのジョブを処理する Spark インスタンス (SI1) が作成されます。
- 別のユーザー (U2) が、10 ノードを使用するジョブ (J3) を作成すると、そのジョブを処理するための新しい Spark インスタンス (SI2) が作成されます。
- ここで、最初のユーザーが、10 ノードを使用する別のジョブ (J2) を送信した場合、プールとインスタンスにはまだキャパシティがあるため、J2 は SI1 によって処理されます。

## <a name="next-steps"></a>次のステップ

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark ドキュメント](https://spark.apache.org/docs/2.4.4/)
