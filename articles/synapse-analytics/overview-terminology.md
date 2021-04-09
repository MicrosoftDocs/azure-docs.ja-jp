---
title: 用語 - Azure Synapse Analytics
description: Azure Synapse Analytics をユーザーに紹介するリファレンス ガイド
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 11/18/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 828f37030ae567cacbaad25849b7ba24c561c20c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98132768"
---
# <a name="azure-synapse-analytics-terminology"></a>Azure Synapse Analytics の用語

このドキュメントでは、Azure Synapse Analytics の基本的な概念を紹介します。

## <a name="basics"></a>基本

**Synapse ワークスペース** は、Azure でクラウドベースのエンタープライズ分析を行うための、セキュリティ保護可能なコラボレーション境界です。 ワークスペースは、特定のリージョンにデプロイされ、関連付けられた ADLS Gen2 アカウントと (一時データを格納するための) ファイル システムを持ちます。 ワークスペースは、リソース グループに属します。

ワークスペースを使用すると、SQL および Apache Spark で分析できます。 SQL および Spark の分析に利用できるリソースは SQL および Spark **プール** にまとめられます。 

## <a name="linked-services"></a>リンクされたサービス

ワークスペースには、任意の数の **リンクされたサービス** を含めることができます。これは、基本的には、ワークスペースが外部リソースに接続するために必要な接続情報を定義する接続文字列です。

## <a name="synapse-sql"></a>Synapse SQL

**Synapse SQL** は、Synapse ワークスペースで T-SQL に基づいて分析するための機能です。 Synapse SQL には、専用とサーバーレスという 2 つの消費モデルがあります。  専用モデルの場合、**専用の SQL プール** を使用します。 ワークスペースには、このようなプールをいくらでも与えることができます。 サーバーレスモデルを使用するには、**サーバーレス SQL プール** を使用します。 ワークスペースごとにこのようなプールが 1 つ与えられます。

Synapse Studio 内で、**SQL スクリプト** を作成および実行することによって、SQL プールを操作できます。

## <a name="apache-spark-for-synapse"></a>Synapse 用の Apache Spark

Spark 分析を使用するには、Synapse ワークスペースで **サーバーレス Apache Spark プール** を作成し、使用します。 Spark プールの使用を開始すると、ワークスペースによって **Spark セッション** が作成され、そのセッションに関連付けられているリソースが処理されます。 

Synapse では、次の 2 とおりの方法で Spark を使用できます。
* **Spark Notebooks**。データ サイエンスやエンジニアリングを行う用途で、Scala、PySpark、C#、SparkSQL が使用されます。
* **Spark ジョブ定義**。jar ファイルを使用したバッチ Spark ジョブの実行に使用されます。

## <a name="pipelines"></a>パイプライン

Azure Synapse では、パイプラインを使用してデータ統合を提供します。これにより、サービス間でデータを移動したり、アクティビティを調整したりすることができます。

* **パイプライン** は、1 つのタスクを連携して実行するアクティビティの論理的なグループです。
* **アクティビティ** は、データのコピー、ノートブックや SQL スクリプトの実行など、データに対して実行する、パイプライン内のアクションを定義します。
* **データ フロー** は、内部的に Synapse Spark を使用するデータ変換を実行するための、コードなしのエクスペリエンスを提供する、特定の種類のアクティビティです。
* **トリガー** - パイプラインを実行します。 手動または自動で実行できます (スケジュール、タンブリング ウィンドウ、またはイベントベース)
* **統合データセット** - アクティビティで入力および出力として使用されるデータを単にポイントまたは参照する、データの名前付きビュー。 リンクされたサービスに属します。

## <a name="next-steps"></a>次のステップ

* [Azure Synapse Analytics の使用を開始する](get-started.md)
* [ワークスペースを作成する](quickstart-create-workspace.md)
* [サーバーレス SQL プールを使用する](quickstart-sql-on-demand.md)

