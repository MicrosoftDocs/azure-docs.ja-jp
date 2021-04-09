---
title: Azure Stream Analytics と Azure Machine Learning の統合
description: この記事では、Azure Stream Analytics ジョブを Azure Machine Learning モデルと統合する方法について説明します。
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/21/2020
ms.custom: devx-track-js
ms.openlocfilehash: c35d5d2f63f4a7abe80a0ff19e5994013355c386
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020521"
---
# <a name="integrate-azure-stream-analytics-with-azure-machine-learning-preview"></a>Azure Stream Analytics と Azure Machine Learning の統合 (プレビュー)

機械学習モデルをユーザー定義関数 (UDF) として Azure Stream Analytics ジョブに実装し、ストリーミング入力データに対してリアルタイムのスコアリングと予測を行うことができます。 [Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) では、Tensorflow、scikit-learn、PyTorch などの一般的なオープンソース ツールを使用して、モデルの準備、トレーニング、およびデプロイを行うことができます。

## <a name="prerequisites"></a>前提条件

機械学習モデルを関数として Stream Analytics ジョブに追加する前に、次の手順を完了します。

1. Azure Machine Learning を使用して、[モデルを Web サービスとしてデプロイ](../machine-learning/how-to-deploy-and-where.md)します。

2. スコアリング スクリプトには、スキーマ仕様を生成するために Azure Machine Learning によって使用される[サンプルの入力と出力](../machine-learning/how-to-deploy-and-where.md)が必要です。 Stream Analytics は、スキーマを使用して Web サービスの関数シグネチャを理解します。 この[サンプルの Swagger 定義](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/AzureML/swagger-example.json)を、それが正しく設定されていることを確認するための参照として使用できます。

3. Web サービスが JSON シリアル化データを受け入れて返すことを確認します。

4. 高スケールの運用デプロイ向けの [Azure Kubernetes Service](../machine-learning/how-to-deploy-and-where.md#choose-a-compute-target) にモデルをデプロイします。 Web サービスがジョブからの要求数を処理できない場合、Stream Analytics ジョブのパフォーマンスが低下し、待機時間に影響します。 Azure Container Instances にデプロイされたモデルは、Azure portal を使用する場合にのみサポートされます。 [Azure Machine Learning デザイナー](../machine-learning/concept-designer.md)を使用して作成されるモデルは、Stream Analytics ではまだサポートされていません。

## <a name="add-a-machine-learning-model-to-your-job"></a>機械学習モデルをジョブに追加する

Azure portal または Visual Studio Code から直接、Azure Machine Learning 関数を Stream Analytics ジョブに追加できます。

### <a name="azure-portal"></a>Azure portal

1. Azure portal で Stream Analytics ジョブに移動し、 **[ジョブ トポロジ]** で **[関数]** を選択します。 次に、 **[+ 追加]** ドロップダウン メニューから **[Azure Machine Learning Service]** を選択します。

   ![Azure Machine Learning UDF を追加する](./media/machine-learning-udf/add-azure-machine-learning-udf.png)

2. **[Azure Machine Learning Service function]\(Azure Machine Learning Service 関数\)** フォームに次のプロパティ値を入力します。

   ![Azure Machine Learning UDF を構成する](./media/machine-learning-udf/configure-azure-machine-learning-udf.png)

### <a name="visual-studio-code"></a>Visual Studio Code

1. Visual Studio Code で Stream Analytics プロジェクトを開き、 **[関数]** フォルダーを右クリックします。 次に、 **[関数の追加]** を選択します。 ドロップダウン リストで **[Machine Learning UDF]** を選択します。

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-add-function.png" alt-text="VS Code で UDF を追加する":::

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-add-function-2.png" alt-text="VS Code で Azure Machine Learning UDF を追加する":::

2. 関数名を入力し、CodeLens の **[Select from your subscriptions]\(サブスクリプションから選択する\)** を使用して、構成ファイルの設定を指定します。

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-function-name.png" alt-text="VS Code で Azure Machine Learning UDF を選択する":::

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-configure-settings.png" alt-text="VS Code で Azure Machine Learning UDF を構成する":::

次の表は、Stream Analytics の Azure Machine Learning Service 関数の各プロパティの説明です。

|プロパティ|説明|
|--------|-----------|
|関数のエイリアス|クエリ内で関数を呼び出すための名前を入力します。|
|サブスクリプション|お使いの Azure サブスクリプション。|
|Azure Machine Learning ワークスペース|モデルを Web サービスとしてデプロイするために使用した Azure Machine Learning ワークスペース。|
|デプロイメント|モデルをホストしている Web サービス。|
|関数シグネチャ|API のスキーマ仕様から推測される Web サービスのシグネチャ。 シグネチャの読み込みに失敗した場合は、スキーマを自動生成するためにスコアリング スクリプトでサンプルの入力と出力を提供したことを確認してください。|
|パーティションごとの並列要求の数|これは、高スケールのスループットを最適化するための高度な構成です。 この数は、ジョブの各パーティションから Web サービスに送信される同時要求を表します。 ストリーミング ユニット (SU) が 6 つ以下のジョブには、1 つのパーティションがあります。 SU が 12 個のジョブには 2 つのパーティションがあり、SU が 18 個であれば 3 つのパーティションがあり、以下同様です。<br><br> たとえば、ジョブに 2 つのパーティションがあり、このパラメーターを 4 に設定した場合、ジョブから Web サービスへの同時要求数は 8 になります。 パブリック プレビューのこの時点では、この値は既定で 20 であり、更新できません。|
|最大バッチ カウント|これは、高スケールのスループットを最適化するための高度な構成です。 この数は、Web サービスに送信される 1 つの要求にバッチ化されるイベントの最大数を表します。|

## <a name="supported-input-parameters"></a>サポートされている入力パラメーター

Stream Analytics クエリが Azure Machine Learning UDF を呼び出すと、Web サービスへの JSON シリアル化要求がジョブによって作成されます。 要求はモデル固有のスキーマに基づいています。 [スキーマを自動的に生成する](../machine-learning/how-to-deploy-and-where.md)には、スコアリング スクリプトでサンプルの入力と出力を指定する必要があります。 このスキーマによって、Stream Analytics は、numpy、pandas、PySpark などのサポートされている任意のデータ型に対する JSON シリアル化要求を構築できます。 複数の入力イベントを 1 つの要求にバッチ化することができます。

次の Stream Analytics クエリは、Azure Machine Learning UDF を呼び出す方法の例です。

```SQL
SELECT udf.score(<model-specific-data-structure>)
INTO output
FROM input
```

Stream Analytics では、Azure Machine Learning 関数に対して 1 つのパラメーターを渡す動作のみがサポートされています。 Machine Learning UDF に入力として渡す前に、データを準備することが必要な場合があります。 ML UDF への入力が null にならないようにする必要があります。null 入力はジョブ失敗の原因になります。

## <a name="pass-multiple-input-parameters-to-the-udf"></a>複数の入力パラメーターを UDF に渡す

Machine Learning モデルへの入力の最も一般的な例は、numpy 配列と DataFrame です。 JavaScript UDF を使用して配列を作成し、`WITH` 句を使用して JSON シリアル化 DataFrame を作成することができます。

### <a name="create-an-input-array"></a>入力配列の作成

*N* 個の入力を受け取って配列を作成する JavaScript UDF を作成できます。この配列は、Azure Machine Learning UDF への入力として使用できます。

```javascript
function createArray(vendorid, weekday, pickuphour, passenger, distance) {
    'use strict';
    var array = [vendorid, weekday, pickuphour, passenger, distance]
    return array;
}
```

JavaScript UDF をジョブに追加したら、次のクエリを使用して Azure Machine Learning UDF を呼び出すことができます。

```SQL
WITH 
ModelInput AS (
#use JavaScript UDF to construct array that will be used as input to ML UDF
SELECT udf.createArray(vendorid, weekday, pickuphour, passenger, distance) as inputArray
FROM input
)

SELECT udf.score(inputArray)
INTO output
FROM ModelInput
#validate inputArray is not null before passing it to ML UDF to prevent job from failing
WHERE inputArray is not null
```

次の JSON は要求の例です。

```JSON
{
    "data": [
        ["1","Mon","12","1","5.8"],
        ["2","Wed","10","2","10"]
    ]
}
```

### <a name="create-a-pandas-or-pyspark-dataframe"></a>Pandas または PySpark DataFrame の作成

次に示すように、`WITH` 句を使用して JSON シリアル化 DataFrame を作成できます。この DataFrame を、入力として Azure Machine Learning UDF に渡すことができます。

次のクエリでは、必要なフィールドを選択することによって DataFrame を作成し、その DataFrame を Azure Machine Learning UDF への入力として使用します。

```SQL
WITH 
Dataframe AS (
SELECT vendorid, weekday, pickuphour, passenger, distance
FROM input
)

SELECT udf.score(Dataframe)
INTO output
FROM Dataframe
```

次の JSON は、前のクエリからの要求の例です。

```JSON
{
    "data": [{
            "vendorid": "1",
            "weekday": "Mon",
            "pickuphour": "12",
            "passenger": "1",
            "distance": "5.8"
        }, {
            "vendorid": "2",
            "weekday": "Tue",
            "pickuphour": "10",
            "passenger": "2",
            "distance": "10"
        }
    ]
}
```

## <a name="optimize-the-performance-for-azure-machine-learning-udfs"></a>Azure Machine Learning UDF のパフォーマンスの最適化

モデルを Azure Kubernetes Service にデプロイするときに、[モデルをプロファイルしてリソース使用率を調べる](../machine-learning/how-to-deploy-profile-model.md)ことができます。 要求率、応答時間、および失敗率を理解するために、[デプロイに対して App Insights を有効にする](../machine-learning/how-to-enable-app-insights.md)こともできます。

イベントのスループットが高いシナリオがある場合、短いエンドツーエンド待機時間で最適なパフォーマンスを達成するために、Stream Analytics で次のパラメーターを変更することが必要な場合があります。

1. 最大バッチ カウント。
2. パーティションごとの並列要求の数。

### <a name="determine-the-right-batch-size"></a>適切なバッチ サイズの決定

Web サービスをデプロイした後、バッチ サイズを変えながらサンプル要求を送信します。バッチ サイズは 50 から始めて、100 単位で増やしていきます。 たとえば、200、500、1000、2000 のようにします。 特定のバッチ サイズを超えると、応答の待機時間が長くなることがわかります。 そこを超えると応答の待機時間が長くなるポイントを、ジョブの最大バッチ カウントにするのが適切です。

### <a name="determine-the-number-of-parallel-requests-per-partition"></a>パーティションあたりの並列要求数の決定

最適なスケーリングでは、Stream Analytics ジョブが複数の並列要求を Web サービスに送信して応答を取得できるまで数ミリ秒以内です。 Web サービスの応答の待機時間は、Stream Analytics ジョブの待機時間とパフォーマンスに直接影響する可能性があります。 ジョブから Web サービスへの呼び出しに時間がかかる場合、ウォーターマークの遅延が増加する可能性が高く、バックログされる入力イベントの数も増加する可能性があります。

このような待機時間を回避するには、[適切な数のノードとレプリカ](../machine-learning/how-to-deploy-azure-kubernetes-service.md#using-the-cli)が Azure Kubernetes Service (AKS) クラスターにプロビジョニングされていることを確認してください。 Web サービスの可用性が高く、正常な応答を返すことが重要です。 ジョブが Web サービスからサービス利用不可応答 (503) を受信する場合、継続的に再試行し、指数関数的なバックオフが伴います。 成功 (200) およびサービス利用不可 (503) 以外の応答は、ジョブを失敗状態にします。

## <a name="next-steps"></a>次のステップ

* [チュートリアル:Azure Stream Analytics の JavaScript ユーザー定義関数](stream-analytics-javascript-user-defined-functions.md)
* [Azure Machine Learning Studio (クラシック) 関数を使用した Stream Analytics ジョブのスケーリング](stream-analytics-scale-with-machine-learning-functions.md)
