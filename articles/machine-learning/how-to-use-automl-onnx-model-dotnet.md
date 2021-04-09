---
title: .NET で AutoML ONNX モデルを使用して予測を行う
description: .NET と ML.NET で AutoML ONNX モデルを使用して予測を行う方法を説明します
titleSuffix: Azure Machine Learning
author: luisquintanilla
ms.author: luquinta
ms.date: 10/30/2020
ms.topic: conceptual
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.custom: how-to, automl
ms.openlocfilehash: 4fb147dc5c57c3a98607a025f566fa583bf87460
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "93358815"
---
# <a name="make-predictions-with-an-automl-onnx-model-in-net"></a>.NET で AutoML ONNX モデルを使用して予測を行う

この記事では、Automated ML (AutoML) Open Neural Network Exchange (ONNX) モデルを使用して、C# .Net Core コンソール アプリケーションと ML.NET で予測を行う方法について説明します。

[ML.NET](/dotnet/machine-learning/) は、.NET エコシステム向けのオープンソースでクロスプラットフォームの機械学習フレームワークです。それを使用すると、C# または F# でコード ファースト アプローチを使用するか、[Model Builder](/dotnet/machine-learning/automate-training-with-model-builder) や [ML.NET CLI](/dotnet/machine-learning/automate-training-with-cli) のようなコードの少ないツールを使用して、カスタム機械学習モデルをトレーニングし、使用することができます。 また、フレームワークは拡張可能であり、TensorFlow や ONNX のような他の一般的な機械学習フレームワークを利用できます。

ONNX は AI モデル用のオープンソースの形式です。 ONNX は、フレームワーク間の相互運用性をサポートしています。 つまり、PyTorch などの多くの一般的な機械学習フレームワークのいずれかでモデルをトレーニングして ONNX 形式に変換し、ML.NET などの別のフレームワークで ONNX モデルを使用することができます。 詳細については、[ONNX の Web サイト](https://onnx.ai/)を参照してください。

## <a name="prerequisites"></a>前提条件

- [.NET Core SDK 3.1 以降](https://dotnet.microsoft.com/download)
- テキスト エディターまたは IDE ([Visual Studio](https://visualstudio.microsoft.com/vs/) や [Visual Studio Code](https://code.visualstudio.com/Download) など)
- ONNX モデル。 AutoML ONNX モデルをトレーニングする方法については、こちらの[銀行マーケティング分類ノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)を参照してください。
- [Netron](https://github.com/lutzroeder/netron) (省略可能)

## <a name="create-a-c-console-application"></a>C# コンソール アプリケーションの作成

このサンプルでは、.NET Core CLI を使用してアプリケーションをビルドしますが、Visual Studio を使用して同じタスクを実行できます。 [.NET Core CLI](/dotnet/core/tools/) の詳細については、こちらを参照してください。

1. ターミナルを開き、新しい C# .NET Core コンソール アプリケーションを作成します。 この例でのアプリケーションの名前は `AutoMLONNXConsoleApp` です。 アプリケーションの内容が含まれるディレクトリが、同じ名前で作成されます。

    ```dotnetcli
    dotnet new console -o AutoMLONNXConsoleApp
    ```

1. ターミナルで、*AutoMLONNXConsoleApp* ディレクトリに移動します。

    ```bash
    cd AutoMLONNXConsoleApp
    ```

## <a name="add-software-packages"></a>ソフトウェア パッケージを追加する

1. .NET Core CLI を使用して、**Microsoft.ML**、**Microsoft.ML.OnnxRuntime**、**Microsoft.ML.OnnxTransformer** の各 NuGet パッケージをインストールします。

    ```dotnetcli
    dotnet add package Microsoft.ML
    dotnet add package Microsoft.ML.OnnxRuntime
    dotnet add package Microsoft.ML.OnnxTransformer
    ```

    これらのパッケージには、.NET アプリケーションで ONNX モデルを使用するために必要な依存関係が含まれています。 ML.NET により、予測に [ONNX ランタイム](https://github.com/Microsoft/onnxruntime)を使用する API が提供されます。

1. *Program.cs* ファイルを開き、適切なパッケージを参照するため、次の `using` ステートメントを先頭に追加します。

    ```csharp
    using System.Linq;
    using Microsoft.ML;
    using Microsoft.ML.Data;
    using Microsoft.ML.Transforms.Onnx;
    ```

## <a name="add-a-reference-to-the-onnx-model"></a>ONNX モデルへの参照を追加する

コンソール アプリケーションで ONNX モデルにアクセスするには、それをビルド出力ディレクトリに追加します。  MSBuild の一般的な項目の詳細については、[MSBuild のガイド](/visualstudio/msbuild/common-msbuild-project-items)を参照してください。

アプリケーションに ONNX モデル ファイルへの参照を追加する

1. ONNX モデルをアプリケーションの *AutoMLONNXConsoleApp* ルート ディレクトリにコピーします。
1. *AutoMLONNXConsoleApp.csproj* ファイルを開き、`Project` ノード内に次の内容を追加します。

    ```xml
    <ItemGroup>
        <None Include="automl-model.onnx">
            <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
        </None>
    </ItemGroup>
    ```

    この場合、ONNX モデル ファイルの名前は *automl-model.onnx* です。

1. *Program.cs* ファイルを開き、次の行を `Program` クラスの内部に追加します。

    ```csharp
    static string ONNX_MODEL_PATH = "automl-model.onnx";
    ```

## <a name="initialize-mlcontext"></a>MLContext を初期化する

`Program` クラスの `Main` メソッドの内部で、[`MLContext`](xref:Microsoft.ML.MLContext) の新しいインスタンスを作成します。

```csharp
MLContext mlContext = new MLContext();
```

ML.NET のすべての操作は [`MLContext`](xref:Microsoft.ML.MLContext) クラスから始まり、`mlContext` を初期化することで、モデルのライフサイクルを通して共有できる新しい ML.NET 環境が作成されます。 これは Entity Framework における DbContext と概念的に似ています。

## <a name="define-the-model-data-schema"></a>モデル データ スキーマを定義する

モデルで入出力するデータは、特定の形式になっている必要があります。 ML.NET を使用すると、クラスを介してデータの形式を定義できます。 それがどのような形式か、既にわかっている場合もあります。 データの形式がわからない場合は、Netron などのツールを使用して ONNX モデルを調べることができます。

このサンプルで使用されるモデルでは、NYC TLC Taxi Trip データセットのデータを使用します。 データのサンプルは次のようになります。

|vendor_id|rate_code|passenger_count|trip_time_in_secs|trip_distance|payment_type|fare_amount|
|---|---|---|---|---|---|---|
|VTS|1|1|1140|3.75|CRD|15.5|
|VTS|1|1|480|2.72|CRD|10.0|
|VTS|1|1|1680|7.8|CSH|26.5|

### <a name="inspect-the-onnx-model-optional"></a>ONNX モデルを調べる (省略可能)

Netron などのツールを使用して、モデルの入力と出力を調べます。

1. Netron を開きます。
1. 上部のメニュー バーで **[File]\(ファイル\) > [Open]\(開く\)** を選択し、ファイル ブラウザーを使用してモデルを選択します。
1. モデルが開きます。 たとえば、*automl-model.onnx* モデルの構造は次のようになります。

    :::image type="content" source="media/how-to-use-automl-onnx-model-dotnet/netron-automl-onnx-model.png" alt-text="Netron AutoML ONNX モデル":::

1. グラフの下端にある最後のノード (この場合は `variable_out1`) を選択して、モデルのメタデータを表示します。 サイドバーの入力と出力には、モデルの想定される入力、出力、データ型が表示されます。 この情報を使用して、モデルの入力スキーマと出力スキーマを定義します。

### <a name="define-model-input-schema"></a>モデルの入力スキーマを定義する

*Program.cs* ファイル内に、次のプロパティを使用して、`OnnxInput` という名前の新しいクラスを作成します。

```csharp
public class OnnxInput
{
    [ColumnName("vendor_id")]
    public string VendorId { get; set; }

    [ColumnName("rate_code"),OnnxMapType(typeof(Int64),typeof(Single))]
    public Int64 RateCode { get; set; }

    [ColumnName("passenger_count"), OnnxMapType(typeof(Int64), typeof(Single))]
    public Int64 PassengerCount { get; set; }

    [ColumnName("trip_time_in_secs"), OnnxMapType(typeof(Int64), typeof(Single))]
    public Int64 TripTimeInSecs { get; set; }

    [ColumnName("trip_distance")]
    public float TripDistance { get; set; }

    [ColumnName("payment_type")]
    public string PaymentType { get; set; }
}
```

各プロパティは、データセット内の列にマップされます。 プロパティは、属性を使用してさらに注釈が付けられます。

[`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) 属性を使用すると、データの操作時に ML.NET によって列が参照される方法を指定できます。 たとえば、`TripDistance` プロパティは標準の .NET 名前付け規則に従いますが、モデルでは `trip_distance` という名前の列または特徴だけが認識されます。 この名前付けの不一致に対処するため、[`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) 属性により、`TripDistance` プロパティは `trip_distance` という名前の列または特徴にマップされます。
  
数値の場合、ML.NET によって処理されるのは [`Single`](xref:System.Single) 型の値だけです。 しかし、一部の列の元のデータ型は整数です。 [`OnnxMapType`](xref:Microsoft.ML.Transforms.Onnx.OnnxMapTypeAttribute) 属性により、ONNX と ML.NET の間で型がマップされます。

データ属性の詳細については、[ML.NET のデータ読み込みガイド](/dotnet/machine-learning/how-to-guides/load-data-ml-net)に関するページを参照してください。

### <a name="define-model-output-schema"></a>モデルの出力スキーマを定義する

データが処理されると、特定の形式の出力が生成されます。 データの出力スキーマを定義します。 *Program.cs* ファイル内に、次のプロパティを使用して、`OnnxOutput` という名前の新しいクラスを作成します。

```csharp
public class OnnxOutput
{
    [ColumnName("variable_out1")]
    public float[] PredictedFare { get; set; }
}
```

`OnnxInput` と同様に、[`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) 属性を使用して、`variable_out1` の出力をもっとわかりやすい名前 `PredictedFare` にマップします。

## <a name="define-a-prediction-pipeline"></a>予測パイプラインを定義する

ML.NET のパイプラインは、通常、入力データを操作して出力を生成する一連の連結された変換です。 データ変換の詳細については、[ML.NET のデータ変換ガイド](/dotnet/machine-learning/resources/transforms)に関するページを参照してください。

1. `Program` クラス内に `GetPredictionPipeline` という名前の新しいメソッドを作成します。

    ```csharp
    static ITransformer GetPredictionPipeline(MLContext mlContext)
    {

    }
    ```

1. 入力列と出力列の名前を定義します。 `GetPredictionPipeline` メソッド内に次のコードを追加します。

    ```csharp
    var inputColumns = new string []
    {
        "vendor_id", "rate_code", "passenger_count", "trip_time_in_secs", "trip_distance", "payment_type"
    };

    var outputColumns = new string [] { "variable_out1" };
    ```

1. パイプラインを定義します。 [`IEstimator`](xref:Microsoft.ML.IEstimator%601) により、パイプラインの操作、入力、および出力スキーマのブループリントが提供されます。

    ```csharp
    var onnxPredictionPipeline =
        mlContext
            .Transforms
            .ApplyOnnxModel(
                outputColumnNames: outputColumns,
                inputColumnNames: inputColumns,
                ONNX_MODEL_PATH);
    ```

    この例では、[`ApplyOnnxModel`](xref:Microsoft.ML.OnnxCatalog.ApplyOnnxModel%2A) がパイプラインでの唯一の変換であり、入力列と出力列の名前および ONNX モデル ファイルへのパスを受け取ります。

1. [`IEstimator`](xref:Microsoft.ML.IEstimator%601) により、データに適用する一連の操作のみが定義されています。 データに対する操作を行うものは、[`ITransformer`](xref:Microsoft.ML.ITransformer) と呼ばれます。 `Fit` メソッドを使用して、`onnxPredictionPipeline` からそれを作成します。

    ```csharp
    var emptyDv = mlContext.Data.LoadFromEnumerable(new OnnxInput[] {});

    return onnxPredictionPipeline.Fit(emptyDv);
    ```

    [`Fit`](xref:Microsoft.ML.IEstimator%601.Fit%2A) メソッドに対する入力としては、操作の実行対象である [`IDataView`](xref:Microsoft.ML.IDataView) が想定されています。 [`IDataView`](xref:Microsoft.ML.IDataView) は、表形式を使用して ML.NET 内でデータを表す方法です。 この場合、予測にはパイプラインのみが使用されるため、空の [`IDataView`](xref:Microsoft.ML.IDataView) を指定して、必要な入力スキーマと出力スキーマの情報を [`ITransformer`](xref:Microsoft.ML.ITransformer) に提供することができます。 その後、アプリケーションでさらに使用するために、適合された [`ITransformer`](xref:Microsoft.ML.ITransformer) が返されます。

    > [!TIP]
    > このサンプルのパイプラインは、同じアプリケーション内で定義されて使用されています。 しかし、パイプラインを定義するアプリケーションと、パイプラインを使用して予測を行うプリケーションは、別のものにすることをお勧めします。 ML.NET では、パイプラインをシリアル化して保存し、他の .NET エンド ユーザー アプリケーションでさらに使用することができます。 デスクトップ アプリケーション、Web サービス、WebAssembly アプリケーション*、その他のさまざまなデプロイ ターゲットが、ML.NET でサポートされています。 パイプラインの保存の詳細については、[ML.NET でのトレーニング済みモデルの保存と読み込みに関するガイド](/dotnet/machine-learning/how-to-guides/save-load-machine-learning-models-ml-net)のページを参照してください。
    >
    > \* WebAssembly は、.NET Core 5 以降でのみサポートされています

1. `Main` メソッドの内部で、必要なパラメーターを指定して `GetPredictionPipeline` メソッドを呼び出します。

    ```csharp
    var onnxPredictionPipeline = GetPredictionPipeline(mlContext);
    ```

## <a name="use-the-model-to-make-predictions"></a>モデルを使用して予測を行う

パイプラインが完成したので、それを使用して予測を行います。 ML.NET には、[`PredictionEngine`](xref:Microsoft.ML.PredictionEngine%602) いう名前の 1 つのデータ インスタンスに対して予測を行うための便利な API が用意されています。

1. `Main` メソッドの内部で、[`CreatePredictionEngine`](xref:Microsoft.ML.ModelOperationsCatalog.CreatePredictionEngine%2A) メソッドを使用して [`PredictionEngine`](xref:Microsoft.ML.PredictionEngine%602) を作成します。

    ```csharp
    var onnxPredictionEngine = mlContext.Model.CreatePredictionEngine<OnnxInput, OnnxOutput>(onnxPredictionPipeline);
    ```

1. テスト データ入力を作成します。

    ```csharp
    var testInput = new OnnxInput
    {
        VendorId = "CMT",
        RateCode = 1,
        PassengerCount = 1,
        TripTimeInSecs = 1271,
        TripDistance = 3.8f,
        PaymentType = "CRD"
    };
    ```

1. [`Predict`](xref:Microsoft.ML.PredictionEngineBase%602.Predict%2A) メソッドを使用し、新しい `testInput` データに基づいて予測を行うには、`predictionEngine` を使用します。

    ```csharp
    var prediction = onnxPredictionEngine.Predict(testInput);
    ```

1. 予測の結果をコンソールに出力します。

    ```csharp
    Console.WriteLine($"Predicted Fare: {prediction.PredictedFare.First()}");
    ```

1. .NET Core CLI を使用して、アプリケーションを実行します。

    ```dotnetcli
    dotnet run
    ```

    結果は次の出力のようになります。

    ```text
    Predicted Fare: 15.621523
    ```

ML.NET で予測を行う方法の詳細については、[モデルを使用して予測を行う方法のガイド](/dotnet/machine-learning/how-to-guides/machine-learning-model-predictions-ml-net)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- [ASP.NET Core Web API としてモデルをデプロイする](/dotnet/machine-learning/how-to-guides/serve-model-web-api-ml-net)
- [サーバーレスの .NET Azure 関数としてモデルをデプロイする](/dotnet/machine-learning/how-to-guides/serve-model-serverless-azure-functions-ml-net)