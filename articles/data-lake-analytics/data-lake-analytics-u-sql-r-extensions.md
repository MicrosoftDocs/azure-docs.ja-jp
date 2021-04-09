---
title: Azure Data Lake Analytics で R を使用した U-SQL スクリプトの拡張
description: Azure Data Lake Analytics を使用して U-SQL スクリプトで R コードを実行する方法を説明します。 ファイルから R コード インラインまたは参照を埋め込みます。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/20/2017
ms.openlocfilehash: ab28ba1bb6185f5f0ab90df99f0abea7895b1bf4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92218002"
---
# <a name="extend-u-sql-scripts-with-r-code-in-azure-data-lake-analytics"></a>Azure Data Lake Analytics で R コードを使用した U-SQL スクリプトの拡張

R コードをデプロイする基本的な手順を以下の例で示します。

* `REFERENCE ASSEMBLY` ステートメントを使用して、U-SQL スクリプト用の R 拡張機能を有効にします。
* `REDUCE` 操作を使用してキーの入力データをパーティション化します。
* U-SQL 用の R 拡張機能には、レジューサ (`Extension.R.Reducer`) が組み込まれており、レジューサに割り当てられている各頂点で R コードを実行します。
* それぞれ `inputFromUSQL` と `outputToUSQL` という名前の専用の名前付きデータ フレームを使用して、U-SQL と R の間でデータをやり取りします。入出力データ フレーム識別子の名前は固定されています (つまり、ユーザーがこれらの入出力データ フレーム識別子の定義済みの名前を変更することはできません)。

## <a name="embedding-r-code-in-the-u-sql-script"></a>U-SQL スクリプトへの R コードの埋め込み

`Extension.R.Reducer` のコマンド パラメーターを使用して、U-SQL スクリプトに R コードをインライン化できます。 たとえば、R スクリプトを文字列変数として宣言し、それをパラメーターとしてレジューサーに渡すことができます。

```usql
REFERENCE ASSEMBLY [ExtR];

DECLARE @myRScript = @"
inputFromUSQL$Species = as.factor(inputFromUSQL$Species)
lm.fit=lm(unclass(Species)~.-Par, data=inputFromUSQL)
#do not return readonly columns and make sure that the column names are the same in usql and r cripts,
outputToUSQL=data.frame(summary(lm.fit)$coefficients)
colnames(outputToUSQL) <- c(""Estimate"", ""StdError"", ""tValue"", ""Pr"")
outputToUSQL
";

@RScriptOutput = REDUCE … USING new Extension.R.Reducer(command:@myRScript, ReturnType:"dataframe");
```

## <a name="keep-the-r-code-in-a-separate-file-and-reference-it--the-u-sql-script"></a>R コードを個別のファイルに保持し、それを U-SQL スクリプトで参照する

次の例は、より複雑な使用法を示しています。 この場合、R コードは U-SQL スクリプトである RESOURCE としてデプロイされます。

この R コードを個別のファイルとして保存します。

```usql
load("my_model_LM_Iris.rda")
outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
```

U-SQL スクリプトを使用して、その R スクリプトを DEPLOY RESOURCE ステートメントでデプロイします。

```usql
REFERENCE ASSEMBLY [ExtR];
DEPLOY RESOURCE @"/usqlext/samples/R/RinUSQL_PredictUsingLinearModelasDF.R";
DEPLOY RESOURCE @"/usqlext/samples/R/my_model_LM_Iris.rda";
DECLARE @IrisData string = @"/usqlext/samples/R/iris.csv";
DECLARE @OutputFilePredictions string = @"/my/R/Output/LMPredictionsIris.txt";
DECLARE @PartitionCount int = 10;
@InputData =
    EXTRACT
        SepalLength double,
        SepalWidth double,
        PetalLength double,
        PetalWidth double,
        Species string
    FROM @IrisData
    USING Extractors.Csv();
@ExtendedData =
    SELECT
        Extension.R.RandomNumberGenerator.GetRandomNumber(@PartitionCount) AS Par,
        SepalLength,
        SepalWidth,
        PetalLength,
        PetalWidth
    FROM @InputData;
// Predict Species
@RScriptOutput = REDUCE @ExtendedData ON Par
    PRODUCE Par, fit double, lwr double, upr double
    READONLY Par
    USING new Extension.R.Reducer(scriptFile:"RinUSQL_PredictUsingLinearModelasDF.R", rReturnType:"dataframe", stringsAsFactors:false);
    OUTPUT @RScriptOutput TO @OutputFilePredictions USING Outputters.Tsv();
```

## <a name="how-r-integrates-with-u-sql"></a>R と U-SQL を統合する方法

### <a name="datatypes"></a>データ型

* R データ フレームと U-SQL の間では、U-SQL の文字列と数値列は現状のまま変換されます (サポートされる型: `double`、`string`、`bool`、`integer`、`byte`)。
* `Factor` データ型は U-SQL ではサポートされていません。
* `byte[]` は、base64 でエンコードされた`string`としてシリアル化する必要があります。
* U-SQL で R の入力データ フレームを作成するか、レジューサ パラメーター `stringsAsFactors: true` を設定することによって、U-SQL の文字列を R コードの因子に変換できます。

### <a name="schemas"></a>スキーマ

* U-SQL データセットでは、重複する列名を持つことはできません。
* U-SQL データセットの列名は文字列でなければなりません。
* 列名は、U-SQL と R スクリプトで同じである必要があります。
* 読み取り専用の列を出力データ フレームに含めることはできません。 読み取り専用の列は、UDO の出力スキーマに含まれている場合、U-SQL テーブルに自動的に戻されるためです。

### <a name="functional-limitations"></a>機能制限

* R エンジンを同じプロセス内で 2 回インスタンス化することはできません。
* 現在、U-SQL では、レジューサ UDO を使用して生成されたパーティション分割モデルを使用した予測のためのコンバイナー UDO はサポートされていません。 ユーザーは、パーティション分割モデルをリソースとして宣言し、それらを R スクリプトで使用できます (サンプル コード `ExtR_PredictUsingLMRawStringReducer.usql` を参照)。

### <a name="r-versions"></a>R のバージョン

R 3.2.2 のみがサポートされています。

### <a name="standard-r-modules"></a>標準の R モジュール

```usql
base
boot
Class
Cluster
codetools
compiler
datasets
doParallel
doRSR
foreach
foreign
Graphics
grDevices
grid
iterators
KernSmooth
lattice
MASS
Matrix
Methods
mgcv
nlme
Nnet
Parallel
pkgXMLBuilder
RevoIOQ
revoIpe
RevoMods
RevoPemaR
RevoRpeConnector
RevoRsrConnector
RevoScaleR
RevoTreeView
RevoUtils
RevoUtilsMath
Rpart
RUnit
spatial
splines
Stats
stats4
survival
Tcltk
Tools
translations
utils
XML
```

### <a name="input-and-output-size-limitations"></a>入力と出力のサイズ制限

各頂点には、割り当てられたメモリ量に制限があります。 R コードでは入力と出力の DataFrame はメモリ内に存在する必要があるため、入力と出力の合計サイズが 500 MB を超えることはできません。

### <a name="sample-code"></a>サンプル コード

U-SQL の高度な分析拡張機能をインストールした後は、Data Lake Store アカウントでその他のサンプル コードを使用できます。 他のサンプル コードのパスは `<your_account_address>/usqlext/samples/R` です。

## <a name="deploying-custom-r-modules-with-u-sql"></a>U-SQL でのカスタム R モジュールのデプロイ

最初に、R カスタム モジュールを作成し、それを zip ファイル化してから、その zip ファイル化された R カスタム モジュール ファイルを ADL ストアにアップロードします。 この例では、magittr_1.5.zip を、使用している ADLA アカウントの既定の ADLS アカウントのルートにアップロードします。 モジュールを ADL ストアにアップロードしたら、それを DEPLOY RESOURCE を使用するように宣言して U-SQL スクリプトで使用できるようにした後、"`install.packages`" を呼び出してそれをインストールします。

```usql
REFERENCE ASSEMBLY [ExtR];
DEPLOY RESOURCE @"/magrittr_1.5.zip";
DECLARE @IrisData string =  @"/usqlext/samples/R/iris.csv";
DECLARE @OutputFileModelSummary string = @"/R/Output/CustomPackages.txt";
// R script to run
DECLARE @myRScript = @"
# install the magrittr package,
install.packages('magrittr_1.5.zip', repos = NULL),
# load the magrittr package,
require(magrittr),
# demonstrate use of the magrittr package,
2 %>% sqrt
";
@InputData =
EXTRACT SepalLength double,
SepalWidth double,
PetalLength double,
PetalWidth double,
Species string
FROM @IrisData
USING Extractors.Csv();
@ExtendedData =
SELECT 0 AS Par,
*
FROM @InputData;
@RScriptOutput = REDUCE @ExtendedData ON Par
PRODUCE Par, RowId int, ROutput string
READONLY Par
USING new Extension.R.Reducer(command:@myRScript, rReturnType:"charactermatrix");
OUTPUT @RScriptOutput TO @OutputFileModelSummary USING Outputters.Tsv();
```

## <a name="next-steps"></a>次のステップ

* [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)
* [Data Lake Tools for Visual Studio を使用して U-SQL スクリプトを開発する](data-lake-analytics-data-lake-tools-get-started.md)
* [Azure Data Lake Analytics ジョブに U-SQL ウインドウ関数を使用する](./data-lake-analytics-u-sql-get-started.md)