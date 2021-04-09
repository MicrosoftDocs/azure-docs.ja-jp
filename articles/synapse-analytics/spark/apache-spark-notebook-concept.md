---
title: Azure Synapse Analytics ノートブックの概要
description: この記事では、Azure Synapse Analytics ノートブックで利用できる機能の概要について説明します。
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 5efdfcea7b6d727038bad124368179b9395bfafd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97093279"
---
# <a name="azure-synapse-analytics-notebooks"></a>Azure Synapse Analytics ノートブック

Synapse Studio ノートブックは、ライブ コード、視覚化、および説明テキストを含むファイルを作成するための Web インターフェイスです。 ノートブックは、アイデアを確認し、簡単な実験を使用してデータから分析情報を得るのに最適な場所です。 

Azure Synapse Studio ノートブックでは、次のことができます。

* 設定作業をせずに使用を開始する。
* 組み込みのエンタープライズ セキュリティ機能を使用してデータを安全に保つ。
* 未加工の形式 (CSV、txt、JSON など) 全体のデータ、処理されたファイル形式 (parquet、Delta Lake、ORC など)、および Spark と SQL に対する SQL の表形式データ ファイルを分析する。
* 高度な作成機能と組み込みのデータの視覚化で生産性を高める。

このセクションには、言語の混在、データ視覚化の作成、ノートブックのパラメーター化、パイプラインの構築などに関する記事があります。 また、ノートブックの開発を開始する方法に関するリファレンスとチュートリアルも含まれています。

## <a name="create-manage-and-use-notebooks"></a>ノートブックを作成、管理、使用する
ノートブックは、Synapse Studio UI を使用して管理できます。 

ノートブックを作成および管理する方法の詳細については、次の記事を参照してください。
  - ノートブックを管理する
    - [ ノートブックを作成する](./spark/../apache-spark-development-using-notebooks.md#create-a-notebook)
    - [ノートブックを開発する](./spark/../apache-spark-development-using-notebooks.md#develop-notebooks)
    - [データをノートブックに取り込む](./spark/../apache-spark-development-using-notebooks.md#bring-data-to-a-notebook)
    - [マジック コマンドと一時テーブルを使用して複数の言語を使用する](./spark/../apache-spark-development-using-notebooks.md#integrate-a-notebook)
    - [セル マジック コマンドを使用する](./spark/../apache-spark-development-using-notebooks.md#magic-commands)
  - 開発
    - [Spark セッション設定を構成する](./spark/../apache-spark-development-using-notebooks.md#spark-session-config)
    - [Microsoft Spark ユーティリティを使用する](./spark/../microsoft-spark-utilities.md)
    - [ノートブックとライブラリを使用してデータを視覚化する](./spark/../apache-spark-data-visualization.md)
    - [ノートブックをパイプラインに統合する](./spark/../apache-spark-development-using-notebooks.md#integrate-a-notebook)


## <a name="next-steps"></a>次のステップ
ノートブックは、データの準備、データの視覚化、機械学習、およびその他のビッグ データのシナリオでも広く使用されています。 データ分析とビッグ データのシナリオでノートブックを使用する方法の詳細については、次のチュートリアルを参照してください。
  - [ノートブックを作成する](./spark/../../quickstart-apache-spark-notebook.md)
  - [Synapse Studio ノートブックを使用して視覚化を作成する](./spark/../apache-spark-data-visualization-tutorial.md)
  - [Apache Spark MLlib を使用して機械学習モデルを構築する](./spark/../apache-spark-machine-learning-mllib-notebook.md)
  - [Azure 自動 ML を使用して機械学習モデルを構築する](./spark/../apache-spark-azure-machine-learning-tutorial.md)