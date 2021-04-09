---
title: Apache Spark の主要概念
description: Azure Synapse Analytics の Apache Spark に関する主要な概要を紹介します。
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 51b2e8cd968c4c14777d196d90686b13158aef42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98120310"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Azure Synapse Analytics の Apache Spark の主要な概念

Apache Spark は、ビッグデータ分析アプリケーションのパフォーマンスを向上させるメモリ内処理をサポートする並列処理フレームワークです。 Azure Synapse Analytics の Apache Spark は、Apache Spark を Microsoft がクラウドに実装したものです。 

Azure Synapse では、Spark の機能を Azure に簡単に作成して構成することができます。 Azure Synapse には異なる Spark 機能の実装が備わっており、このドキュメントではそれらの機能について取り上げています。

## <a name="spark-pools"></a>Spark プール

サーバーレス Apache Spark プールは Azure portal で作成します。 インスタンス化時、データを処理する Spark インスタンスの作成には、Spark プールの定義が使用されます。 作成した Spark プールはメタデータとしてのみ存在します。リソースが消費されたり実行されたりすることはなく、課金されることもありません。 Spark プールには、Spark インスタンスの特性を制御する一連のプロパティがあります。 そのような特性には、名前、サイズ、拡大縮小動作、有効期限がありますがそれらに限定されません。

Spark プールは、その作成に関して料金もリソース コストも発生しないので、異なる構成でいくつでも作成することができます。 Spark プールにアクセス許可を適用することで、ユーザーが利用できる Spark プールを限定することもできます。

ベスト プラクティスとして、開発とデバッグ用に作成する Spark プールは小さく、運用環境のワークロードの実行用に作成する Spark プールは大きくすることをお勧めします。

Spark プールの作成方法とそのすべてのプロパティは、[Azure Synapse Analytics の Spark プールの概要](../quickstart-create-apache-spark-pool-portal.md)に関するページでご覧いただけます。

## <a name="spark-instances"></a>Spark インスタンス

Spark インスタンスは、Spark プールに接続してセッションを作成し、ジョブを実行するときに作成されます。 1 つの Spark プールを複数のユーザーが利用するときは、接続するユーザーごとに新しい Spark インスタンスが作成されます。 

2 つ目のジョブを送信するとき、プールに容量がある場合、既存の Spark にも容量があります。 その後、既存のインスタンスによってジョブが処理されます。 容量がない場合、プール レベルで容量が利用できるなら、新しい Spark インスタンスが作成されます。

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

## <a name="quotas-and-resource-constraints-in-apache-spark-for-azure-synapse"></a>Apache Spark for Azure Synapse におけるクォータとリソースの制約

### <a name="workspace-level"></a>ワークスペースレベル

Azure Synapse ワークスペースにはそれぞれ、Spark に使用できる既定の仮想コア クォータが割り当てられています。 ユーザー クォータとデータフロー クォータのどちらの使用パターンもワークスペース内の仮想コアをすべて使い尽くしてしまうことのないよう、クォータは、その両者間で分け合うようになっています。 クォータはサブスクリプションの種類によって異なりますが、ユーザーとデータフローとの間で均等に割り当てられます。 ただし、ワークスペースに残っている仮想コアを超える要求を行った場合は、次のエラーが発生します。

```console
Failed to start session: [User] MAXIMUM_WORKSPACE_CAPACITY_EXCEEDED
Your Spark job requested 480 vcores.
However, the workspace only has xxx vcores available out of quota of yyy vcores.
Try reducing the numbers of vcores requested or increasing your vcore quota. Click here for more information - https://go.microsoft.com/fwlink/?linkid=213499
```

メッセージ内のリンク先は、この記事です。

ワークスペースの仮想コア クォータ引き上げを要求する方法については、以下の記事で説明されています。

- サービスの種類として "Azure Synapse Analytics" を選択します。
- [クォータの詳細] ウィンドウで、[Apache Spark (vCore) per workspace]\(ワークスペースごとの Apache Spark (仮想コア)\) を選択します。

[Azure portal でキャパシティの引き上げを要求する](../../azure-portal/supportability/per-vm-quota-requests.md#request-a-standard-quota-increase-from-help--support)

### <a name="spark-pool-level"></a>Spark プール レベル

Spark プールを定義するとき、実質的には、そのプールに対するユーザーごとのクォータを定義することになります。複数のノートブックまたはジョブ (あるいはその両方) を実行した場合、プールのクォータを使い果たしてしまう可能性があります。 その場合、次のようなエラー メッセージが表示されます。

```console
Failed to start session: Your Spark job requested xx vcores.
However, the pool is consuming yy vcores out of available zz vcores.Try ending the running job(s) in the pool, reducing the numbers of vcores requested, increasing the pool maximum size or using another pool
```

この問題を解決するには、プール リソースの使用量を減らしたうえで、ノートブックまたはジョブを実行し、新しいリソース要求を送信する必要があります。

## <a name="next-steps"></a>次のステップ

- [Azure Synapse Analytics](../index.yml)
- [Apache Spark ドキュメント](https://spark.apache.org/docs/2.4.5/)