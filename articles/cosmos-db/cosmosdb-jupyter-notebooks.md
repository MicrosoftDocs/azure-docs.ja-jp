---
title: Azure Cosmos DB に組み込まれている Jupyter Notebook のサポートの概要 (プレビュー)
description: Azure Cosmos DB に組み込まれている Jupyter Notebook のサポートを使用して、クエリを対話的に実行する方法について説明します。
ms.service: cosmos-db
ms.topic: overview
ms.date: 05/19/2020
author: deborahc
ms.author: dech
ms.openlocfilehash: 4f57d2793a28f4bbb201764c67af82f31d0b9333
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100369800"
---
# <a name="built-in-jupyter-notebooks-support-in-azure-cosmos-db-preview"></a>Azure Cosmos DB に組み込まれている Jupyter Notebook のサポート (プレビュー)
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Jupyter Notebook はオープン ソースの Web アプリケーションであり、ライブ コード、数式、視覚化、および説明テキストが含まれるドキュメントを作成して共有することができます。 

Azure Cosmos DB の組み込みの Jupyter Notebook は、Azure portal とご自分の Azure Cosmos DB アカウントに直接統合され、便利で使いやすいものになります。 開発者、データ サイエンティスト、エンジニア、およびアナリストは使い慣れた Jupyter Notebook エクスペリエンスを利用して、データ探索、データ クリーニング、データ変換、数値シミュレーション、統計モデリング、データ視覚化、および機械学習を行うことができます。

:::image type="content" source="./media/cosmosdb-jupyter-notebooks/cosmos-notebooks-overview.png" alt-text="Azure Cosmos DB における Jupyter Notebook の視覚化":::

Azure Cosmos DB では、Core (SQL)、Cassandra、Gremlin、Table、および MongoDB 用 API など、すべての API に対して C# と Python の両方のノートブックがサポートされます。 ノートブック内で、組み込みのコマンドと機能を活用して、Azure Cosmos DB リソースの作成、データのアップロード、Azure Cosmos DB でのデータのクエリと視覚化を簡単に行うことができます。 

:::image type="content" source="./media/cosmosdb-jupyter-notebooks/jupyter-notebooks-portal.png" alt-text="Azure Cosmos DB における Jupyter Notebook のサポート":::

## <a name="benefits-of-jupyter-notebooks"></a>Jupyter Notebook の利点

Jupyter Notebook は、もともと Python と R で記述されたデータ サイエンス アプリケーション向けに開発されましたが、次のような異なる種類のプロジェクトに対してさまざまな方法で使用することができます。

**データの視覚化:** Jupyter Notebook を使用して、データ セットをグラフィックとしてレンダリングする共有ノートブックの形式でデータを視覚化できます。 視覚化を作成し、共有コードとデータ セットに対して対話形式の変更を行い、結果を共有することができます。

**コードの共有:** GitHub のようなサービスには、コードを共有する方法が用意されていますが、その大部分は対話形式ではありません。 Jupyter Notebook を使用すると、コードを表示して実行し、結果を Azure portal に直接表示できます。

**ライブでのコード操作:** Jupyter Notebook のコードは動的です。編集してリアルタイムで増分更新内容を実行できます。 コード、デモ、または概念実証 (POCs) の入力ソースとして使用されるユーザー コントロール (スライダーやテキスト入力フィールドなど) を埋め込むこともできます。

**コード サンプルとデータ探索の結果のドキュメント:** コードがあり、その動作を 1 行ずつ説明する場合、Jupyter Notebook に埋め込むことができます。 ドキュメントと対話機能を同時に追加することができます。

**Azure Cosmos DB 用の組み込みコマンド:** Azure Cosmos DB の組み込みのマジック コマンドを使用すると、アカウントを簡単に操作できるようになります。 %%upload や %%sql などのコマンドを使用してコンテナーにデータをアップロードし、[SQL API 構文](sql-query-getting-started.md)を使用してクエリを実行できます。 追加のカスタム コードを記述する必要はありません。

**オールインワン環境:** Jupyter Notebook では、コード、リッチ テキスト、画像、動画、アニメーション、数学式、プロット、マップ、対話型の図表、ウィジェット、およびグラフィカル ユーザー インターフェイスが単一のドキュメントに結合されます。

## <a name="components-of-a-jupyter-notebook"></a>Jupyter Notebook の構成要素

Jupyter Notebook には、複数の種類のコンポーネントを含めることができ、それぞれを個別のブロックまたはセルにまとめます。

**テキストと HTML:** プレーン テキスト (HTML を生成するマークダウン構文に注釈を付けるテキスト) を、ドキュメントの任意の場所に挿入できます。 CSS スタイルをインラインで含めたり、ノートブックを生成するために使用されるテンプレートに追加したりすることもできます。

**コードと出力:** Jupyter Notebook では、Python コードと C# コードがサポートされます。 実行されたコードの結果は、コード ブロックの直後に表示され、コード ブロックは任意の順序で複数回実行できます。

**視覚化:** Matplotlib、Plotly、Bokeh などのモジュールを使用して、コードからグラフィックスとグラフを生成できます。 出力と同様に、これらの視覚化は、それらを生成するコードの横にインラインで表示されます。 出力と同様に、これらの視覚化は、それらを生成するコードの横にインラインで表示されます。

**マルチメディア:** Jupyter Notebook は Web テクノロジ上に構築されているため、Web ページでサポートされているすべての種類のマルチメディアを表示できます。 これらの要素をノートブック内に HTML 要素として含めることも、`IPython.display` モジュールを使用してプログラムで生成することもできます。

**データ:** プログラムを使用して、Azure Cosmos コンテナーのデータまたはクエリの結果を Jupyter Notebook にインポートできます。 組み込みのマジック コマンドを使用して Azure Cosmos DB のデータをアップロードまたは照会します。 

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB で組み込みの Jupyter Notebook の使用を開始するには、次の記事を参照してください。

* [Azure Cosmos アカウントでノートブックを有効にする](enable-notebooks.md)
* [ノートブックのサンプル ギャラリーを参照する](https://cosmos.azure.com/gallery.html)
* [Python ノートブックの機能とコマンドを使用する](use-python-notebook-features-and-commands.md)
* [C# ノートブックの機能とコマンドを使用する](use-csharp-notebook-features-and-commands.md)
* [GitHub リポジトリからノートブックをインポートする](import-github-notebooks.md)