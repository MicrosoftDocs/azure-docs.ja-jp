---
title: PREDICT を使用した機械学習モデルのスコア付け
description: 専用 SQL プールの T-SQL PREDICT 関数を使用して機械学習モデルをスコア付けする方法について説明します。
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 07/21/2020
ms.author: anjangsh
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 9e7d45a588e60cd082f1eef43d1d1b6681b9e912
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98117743"
---
# <a name="score-machine-learning-models-with-predict"></a>PREDICT を使用した機械学習モデルのスコア付け

専用 SQL プールには、使い慣れた T-SQL 言語を使用して機械学習モデルをスコア付けする機能が用意されています。 T-SQL [PREDICT](/sql/t-sql/queries/predict-transact-sql?preserve-view=true&view=azure-sqldw-latest) を使用すると、履歴データでトレーニングされた既存の機械学習モデルを、ご自分のデータ ウェアハウスのセキュリティで保護された境界内に持ち込んでスコア付けすることができます。 PREDICT 関数によって、入力として [ONNX (Open Neural Network Exchange)](https://onnx.ai/) モデルとデータが受け取られます。 この機能により、重要なデータをスコアリングのためにデータ ウェアハウスの外部に移動する手順が不要になります。 これは、データ プロフェッショナルが使い慣れた T-SQL インターフェイスを使用して機械学習モデルを簡単にデプロイできるようにするだけでなく、タスクに適切なフレームワークを使用してデータ サイエンティストとシームレスに共同作業できるようにすることを目的としています。

> [!NOTE]
> 現在、この機能はサーバーレス SQL プールではサポートされていません。

この機能には、モデルが Synapse SQL の外部でトレーニングされている必要があります。 モデルを構築したら、それをデータ ウェアハウスに読み込んで、T-SQL PREDICT 構文でスコア付けし、データから分析情報を得ます。

![predictoverview](./media/sql-data-warehouse-predict/datawarehouse-overview.png)

## <a name="training-the-model"></a>モデルのトレーニング

専用 SQL プールでは、事前トレーニング済みのモデルが想定されています。 専用 SQL プールで予測を実行するために使用される機械学習モデルをトレーニングするときは、次の点に留意してください。

- 専用 SQL プールでサポートされているのは ONNX 形式のモデルのみです。 ONNX は、さまざまなフレームワーク間でモデルを交換して相互運用性を実現できるオープンソース モデル形式です。 既存のモデルを ONNX 形式に変換するには、それをネイティブにサポートしているフレームワークか、変換パッケージを利用できるフレームワークを使用します。 たとえば、[sklearn-onnx](https://github.com/onnx/sklearn-onnx) パッケージでは、scikit-learn モデルが ONNX に変換されます。 [ONNX GitHub リポジトリ](https://github.com/onnx/tutorials#converting-to-onnx-format)によって、サポートされているフレームワークと例のリストが提供されます。

   トレーニングに [自動 ML](../../machine-learning/concept-automated-ml.md) を使用している場合は、*enable_onnx_compatible_models* パラメーターが TRUE に設定されていることを確認し、ONNX 形式モデルを生成します。 [自動機械学習ノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)には、自動 ML を使用して ONNX 形式の機械学習モデルを作成する方法の例が示されています。

- 入力データに対しては、次のデータ型がサポートされています。
    - int、bigint、real、float
    - char、varchar、nvarchar

- スコアリング データは、トレーニング データと同じ形式である必要があります。 多次元配列などの複合データ型は、PREDICT ではサポートされていません。 そのため、トレーニングでは、すべての入力を含む単一の配列を渡すのではなく、モデルの各入力がスコアリング テーブルの 1 つの列に対応していることを確認してください。

- モデル入力の名前とデータ型が、新しい予測データの列名とデータ型と一致していることを確認します。 オンラインで入手できるさまざまなオープンソース ツールを使用して ONNX モデルを視覚化すると、デバッグにさらに役立ちます。

## <a name="loading-the-model"></a>モデルを読み込む

モデルは、16 進数の文字列として専用 SQL プール ユーザー テーブルに格納されます。 モデルを識別するために、ID や説明などの追加の列をモデル テーブルに追加することができます。 モデル列のデータ型として varbinary(max) を使用します。 モデルの格納に使用できるテーブルのコード例を次に示します。

```sql
-- Sample table schema for storing a model and related data
CREATE TABLE [dbo].[Models]
(
    [Id] [int] IDENTITY(1,1) NOT NULL,
    [Model] [varbinary](max) NULL,
    [Description] [varchar](200) NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    HEAP
)
GO

```

モデルが 16 進数の文字列に変換され、テーブル定義が指定されたら、[COPY コマンド](/sql/t-sql/statements/copy-into-transact-sql?preserve-view=true&view=azure-sqldw-latest)または Polybase を使用して、モデルを専用 SQL プール テーブルに読み込みます。 次のコード サンプルでは、Copy コマンドを使用してモデルを読み込みます。

```sql
-- Copy command to load hexadecimal string of the model from Azure Data Lake storage location
COPY INTO [Models] (Model)
FROM '<enter your storage location>'
WITH (
    FILE_TYPE = 'CSV',
    CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='<enter your storage key here>')
)
```

## <a name="scoring-the-model"></a>モデルのスコアリング

モデルとデータがデータ ウェアハウスに読み込まれたら、**T-SQL PREDICT** 関数を使用してモデルをスコア付けします。 新しい入力データが、モデルの構築に使用したトレーニング データと同じ形式であることを確認します。 T-SQL PREDICT によって、モデルと新しいスコアリング入力データの 2 つの入力が受け取られ、出力用の新しい列が生成されます。モデルは、変数、リテラル、またはスカラー sub_query として指定できます。 [WITH common_table_expression](/sql/t-sql/queries/with-common-table-expression-transact-sql?preserve-view=true&view=azure-sqldw-latest) を使用して、データ パラメーターの名前付き結果セットを指定します。

次の例は、予測関数を使用したサンプル クエリを示しています。 予測結果を含む *Score* という名前の追加の列と、データ型 *float* が作成されます。 すべての入力データ列と出力予測列が、SELECT ステートメントで表示できるようになります。 詳細については、「[PREDICT (Transact-SQL)](/sql/t-sql/queries/predict-transact-sql?preserve-view=true&view=azure-sqldw-latest)」を参照してください。

```sql
-- Query for ML predictions
SELECT d.*, p.Score
FROM PREDICT(MODEL = (SELECT Model FROM Models WHERE Id = 1),
DATA = dbo.mytable AS d, RUNTIME = ONNX) WITH (Score float) AS p;
```

## <a name="next-steps"></a>次のステップ

PREDICT 関数の詳細については、「[PREDICT (Transact-SQL)](/sql/t-sql/queries/predict-transact-sql?preserve-view=true&view=azure-sqldw-latest)」を参照してください。