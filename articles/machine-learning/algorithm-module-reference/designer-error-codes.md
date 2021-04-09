---
title: デザイナー モジュール エラーのトラブルシューティング
titleSuffix: Azure Machine Learning
description: Azure Machine Learning デザイナーの自動モジュール エラー コードの読み取りとトラブルシューティングを行う方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.custom: troubleshooting
author: likebupt
ms.author: keli19
ms.date: 11/25/2020
ms.openlocfilehash: b917e3fc93c59de85c5236c18e31d7bbc9d891f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98065475"
---
# <a name="exceptions-and-error-codes-for-the-designer"></a>デザイナーの例外とエラー コード

この記事では、Machine Learning パイプラインのトラブルシューティングに役立つ Azure Machine Learning デザイナーのエラー メッセージと例外コードについて説明します。

デザイナーでエラー メッセージを確認するには、次の手順に従います。  

- 失敗したモジュールを選択し、 **[Outputs+logs]\(出力とログ\)** タブにアクセスすると、**azureml-logs** カテゴリの下の **70_driver_log.txt** ファイルに詳細なログがあります。

- 詳細なモジュール エラーについては、**module_statistics** カテゴリの下にある error_info.json で確認できます。

デザイナーにおけるモジュールのエラー コードを次に示します。

## <a name="error-0001"></a>エラー 0001  
 データ セットで指定した列のうち 1 つまたは複数が見つからない場合、例外が発生します。  

 モジュールで列を選択していても、選択された列が入力データ セットに存在しない場合、このエラーを受け取ります。 このエラーは、列名を手入力した場合、またはパイプラインを実行したときにデータセットに存在しない列が列セレクターによって提案された場合、発生する可能性があります。  

**解決策:** この例外がスローされているモジュールを見直し、列の名前 (1 つまたは複数) が正しいこと、および参照されているすべての列が存在することを確認します。  

|例外メッセージ|
|------------------------|
|One or more specified columns were not found. (指定された列のうち 1 つ以上が見つかりませんでした。)|
|Column with name or index "{column_id}" not found. (名前またはインデックス "{column_id}" の列が見つかりません。)|
|Column with name or index "{column_id}" does not exist in "{arg_name_missing_column}". (名前またはインデックス "{column_id}" の列が "{arg_name_missing_column}" に存在しません。)|
|Column with name or index "{column_id}" does not exist in "{arg_name_missing_column}", but exists in "{arg_name_has_column}". (名前またはインデックス "{column_id}" の列は "{arg_name_missing_column}" には存在せず、"{arg_name_has_column}" に存在します。)|
|Columns with name or index "{column_names}" not found. (名前またはインデックス "{column_names}" の列が見つかりません。)|
|Columns with name or index "{column_names}" does not exist in "{arg_name_missing_column}". (名前またはインデックス "{column_names}" の列が "{arg_name_missing_column}" に存在しません。)|
|Columns with name or index "{column_names}" does not exist in "{arg_name_missing_column}", but exists in "{arg_name_has_column}". (名前またはインデックス "{column_names}" の列は "{arg_name_missing_column}" には存在せず、"{arg_name_has_column}" に存在します。)|


## <a name="error-0002"></a>エラー 0002  
 1 つまたは複数のパラメーターを解析できない場合、または指定された型からターゲット メソッドで必要な型に変換できない場合、例外が発生します。  

 Azure Machine Learning では、入力としてパラメーターを指定し、値の型が予想される型と異なり、暗黙的な変換を実行できない場合、このエラーが発生します。  

**解決策:** モジュールの要件を確認し、必要な値の型を特定します (文字列、整数、倍精度浮動小数点数型など)  

|例外メッセージ|
|------------------------|
|Failed to parse parameter. (パラメーターを解析できませんでした。)|
|Failed to parse "{arg_name_or_column}" parameter. ("{arg_name_or_column}" パラメーターを解析できませんでした。)|
|Failed to convert "{arg_name_or_column}" parameter to "{to_type}". ("{arg_name_or_column}" パラメーターを "{to_type}" に変換できませんでした。)|
|Failed to convert "{arg_name_or_column}" parameter from "{from_type}" to "{to_type}". ("{arg_name_or_column}" パラメーターを "{from_type}" から "{to_type}" に変換できませんでした。)|
|Failed to convert "{arg_name_or_column}" parameter value "{arg_value}" from "{from_type}" to "{to_type}". ("{arg_name_or_column}" パラメーター値 "{arg_value}" を "{from_type}" から "{to_type}" に変換できませんでした。)|
|Failed to convert value "{arg_value}" in column "{arg_name_or_column}" from "{from_type}" to "{to_type}" with usage of the format "{fmt}" provided. (指定された形式 "{fmt}" を使用して、列 "{arg_name_or_column}" の値 "{arg_value}" を "{from_type}" から "{to_type}" に変換できませんでした。)|


## <a name="error-0003"></a>エラー 0003  
 1 つまたは複数の入力が null または空の場合、例外が発生します。  

 Azure Machine Learning では、モジュールに対するいずれかの入力またはパラメーターが null または空の場合、このエラーを受け取ります。  たとえば、パラメーターに値を入力しなかった場合、このエラーが発生する可能性があります。 欠損値のあるデータセットまたは空のデータセットを選択した場合にも、発生する可能性があります。  

**解決策:**

+ 例外が発生したモジュールを開き、すべての入力が指定されていることを確認します。 すべての必須入力が指定されていることを確認します。 
+ Azure ストレージから読み込まれるデータにアクセスできること、およびアカウント名またはキーが変更されていないことを確認します。  
+ 入力データで欠損値または null 値を確認します。
+ データ ソースに対するクエリを使用している場合は、必要な形式でデータが返されることを確認します。 
+ 入力ミスや、データの指定でのその他の変更を確認します。
  
|例外メッセージ|
|------------------------|
|One or more of inputs are null or empty. (1 つまたは複数の入力が null または空です。)|
|Input "{name}" is null or empty. (入力 "{name}" が null または空です。)|


## <a name="error-0004"></a>エラー 0004  
 パラメーターが特定の値以下の場合、例外が発生します。  

 Azure Machine Learning では、メッセージのパラメーターが、モジュールでデータを処理するために必要な境界値より小さい場合、このエラーを受け取ります。  

**解決策:** 例外がスローされているモジュールを見直し、指定された値より大きくなるようにパラメーターを修正します。  

|例外メッセージ|
|------------------------|
|Parameter should be greater than boundary value. (パラメーターは、境界値より大きい値にする必要があります。)|
|Parameter "{arg_name}" value should be greater than {lower_boundary}. (パラメーター "{arg_name}" の値は {lower_boundary} より大きくする必要があります。)|
|Parameter "{arg_name}" has value "{actual_value}" which should be greater than {lower_boundary}. (パラメーター "{arg_name}" には値 "{actual_value}" が含まれていますが、これは {lower_boundary} より大きくする必要があります。)|


## <a name="error-0005"></a>エラー 0005  
 パラメーターが特定の値より小さい場合、例外が発生します。  

 Azure Machine Learning では、メッセージのパラメーターが、モジュールでデータを処理するために必要な境界値以下の場合、このエラーを受け取ります。  

**解決策:** 例外がスローされているモジュールを見直し、指定された値以上になるように、パラメーターを修正します。  

|例外メッセージ|
|------------------------|
|Parameter should be greater than or equal to boundary value. (パラメーターは、境界値以上にする必要があります。)|
|Parameter "{arg_name}" value should be greater than or equal to {lower_boundary}. (パラメーター "{arg_name}" の値は {lower_boundary} 以上とする必要があります。)|
|Parameter "{arg_name}" has value "{value}" which should be greater than or equal to {lower_boundary}. (パラメーター "{arg_name}" には値 "{value}" が含まれていますが、これは {lower_boundary} 以上とする必要があります。)|


## <a name="error-0006"></a>エラー 0006  
 パラメーターが指定された値以上の場合、例外が発生します。  

 Azure Machine Learning では、メッセージのパラメーターが、モジュールでデータを処理するために必要な境界値以上の場合、このエラーを受け取ります。  

**解決策:** 例外がスローされているモジュールを見直し、指定された値より小さくなるようにパラメーターを修正します。  

|例外メッセージ|
|------------------------|
|Parameters mismatch. (パラメーターが一致しません。) One of the parameters should be less than another. (パラメーターの 1 つは他より小さくする必要があります。)|
|Parameter "{arg_name}" value should be less than parameter "{upper_boundary_parameter_name}" value. (パラメーター "{arg_name}" の値は、パラメーター "{upper_boundary_parameter_name}" の値より小さくする必要があります。)|
|Parameter "{arg_name}" has value "{value}" which should be less than {upper_boundary_parameter_name}. (パラメーター "{arg_name}" には値 "{value}" が含まれていますが、これは {upper_boundary_parameter_name} より小さくする必要があります。)|


## <a name="error-0007"></a>エラー 0007  
 パラメーターが特定の値より大きい場合、例外が発生します。  

 Azure Machine Learning では、モジュールのプロパティで許容されるより大きい値を指定した場合、このエラーを受け取ります。 たとえば、サポートされる日付の範囲外にあるデータを指定した場合や、3 列のみ使用可能なときに 5 列を使用することを示した場合です。 

 何らかの方法で一致する必要があるデータの 2 つのセットを指定した場合も、このエラーが表示される可能性があります。 たとえば、列の名前を変更していて、インデックスで列を指定する場合、指定する名前の数と列インデックスの数が一致する必要があります。 もう 1 つの例として、2 つの列を使用する算術演算では、列に同じ数の行が含まれる必要があります。 

**解決策:**

 + 問題のモジュールを開き、数値プロパティの設定を確認します。
 + すべてのパラメーター値が、そのプロパティに対してサポートされている値の範囲内にあることを確認します。
 + モジュールが複数の入力を受け取る場合は、入力が同じサイズであることを確認します。
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + データセットまたはデータ ソースが変更されたかどうかを確認します。 以前のバージョンのデータで動作していた値が、列の数、列のデータ型、またはデータのサイズを変更すると失敗することがあります。  

|例外メッセージ|
|------------------------|
|Parameters mismatch. (パラメーターが一致しません。) One of the parameters should be less than another. (パラメーターの 1 つが他より小さい必要があります。)|
|Parameter "{arg_name}" value should be less than or equal to parameter "{upper_boundary_parameter_name}" value. (パラメーター "{arg_name}" の値は、パラメーター "{upper_boundary_parameter_name}" の値以下とする必要があります。)|
|Parameter "{arg_name}" has value "{actual_value}" which should be less than or equal to {upper_boundary}. (パラメーター "{arg_name}" には値 "{actual_value}" が含まれていますが、これは {upper_boundary} 以下とする必要があります。)|
|Parameter "{arg_name}" value {actual_value} should be less than or equal to parameter "{upper_boundary_parameter_name}" value {upper_boundary}. (パラメーター "{arg_name}" の値 {actual_value} は、パラメーター "{upper_boundary_parameter_name}" の値 {upper_boundary} 以下とする必要があります。)|
|Parameter "{arg_name}" value {actual_value} should be less than or equal to {upper_boundary_meaning} value {upper_boundary}. (パラメーター "{arg_name}" の値 {actual_value} は、"{upper_boundary_meaning}" の値 {upper_boundary} 以下とする必要があります。)|


## <a name="error-0008"></a>エラー 0008  
 パラメーターが範囲内にない場合、例外が発生します。  

 Azure Machine Learning では、メッセージのパラメーターがモジュールでデータを処理するために必要な境界の外側にある場合、このエラーを受け取ります。  

 たとえば、[行の追加](add-rows.md)を使って列の数が異なる 2 つのデータセットを結合しようとすると、このエラーが表示されます。  

**解決策:** 例外がスローされているモジュールを見直し、指定された範囲内になるようにパラメーターを修正します。  

|例外メッセージ|
|------------------------|
|Parameter value is not in the specified range. (パラメーターの値が、指定された範囲内ではありません。)|
|Parameter "{arg_name}" value is not in range. (パラメーター "{arg_name}" の値が範囲内ではありません。)|
|Parameter "{arg_name}" value should be in the range of [{lower_boundary}, {upper_boundary}]. (パラメーター "{arg_name}" の値は、[{lower_boundary}, {upper_boundary}] の範囲内に収める必要があります。)|
|Parameter "{arg_name}" value is not in range. (パラメーター "{arg_name}" の値が範囲内ではありません。) {reason}|


## <a name="error-0009"></a>エラー 0009  
 Azure ストレージ アカウント名またはコンテナー名が正しく指定されていない場合、例外が発生します。  

Azure Machine Learning デザイナーでは、Azure ストレージ アカウントのパラメーターを指定していても、名前またはパスワードを解決できない場合、このエラーが発生します。 パスワードまたはアカウント名に関するエラーは、多くの理由で発生する可能性があります。

 + アカウントの種類が正しくありません。 一部の新しいアカウントの種類では、Machine Learning デザイナーでの使用がサポートされていません。 詳しくは、「[Import Data (データのインポート)](import-data.md)」をご覧ください。
 + 正しくないアカウント名を入力しました
 + アカウントはもう存在しません
 + ストレージ アカウントのパスワードが間違っているか、変更されています
 + コンテナー名を指定していないか、コンテナーが存在しません
 + ファイルのパス (BLOB へのパス) を完全に指定していません
   

**解決策:**

このような問題は、アカウント名、パスワード、またはコンテナーのパスを手入力しようとすると発生することがよくあります。 [データのインポート](import-data.md) モジュールの新しいウィザードを使うことをお勧めします。名前の検索と確認に役立ちます。

また、アカウント、コンテナー、または BLOB が削除されているかどうかも確認します。 アカウント名とパスワードが正しく入力されたこと、およびコンテナーが存在することを確認するには、別の Azure ストレージ ユーティリティを使います。 

Azure Machine Learning では、一部の新しいアカウントの種類はサポートされていません。 たとえば、新しい "ホット" または "コールド" ストレージの種類は、Machine Learning には使用できません。 従来のストレージ アカウントおよび "汎用" として作成されたストレージ アカウントはどちらも、正常に動作します。

BLOB への完全なパスを指定した場合は、パスが "**コンテナー/BLOB 名**" として指定されていること、およびコンテナーと BLOB の両方がアカウントに存在することを確認します。  

 パスの先頭にスラッシュを含めることはできません。 たとえば、 **/container/blob** は正しくなく、**container/blob** と入力する必要があります。  


|例外メッセージ|
|------------------------|
|The Azure storage account name or container name is incorrect. (Azure ストレージ アカウント名またはコンテナー名が正しくありません。)|
|The Azure storage account name "{account_name}" or container name "{container_name}" is incorrect; a container name of the format container/blob was expected. (Azure ストレージ アカウント名 "{account_name}" またはコンテナー名 "{container_name}" が正しくありません。コンテナー名は "コンテナー/BLOB" の形式であることが予想されていました。)|


## <a name="error-0010"></a>エラー 0010  
 入力データセットで一致している必要のある列名があっても、そうなっていない場合、例外が発生します。  

 Azure Machine Learning では、メッセージ内の列インデックスに対応する列名が 2 つの入力データセットで異なる場合、このエラーを受け取ります。  

**解決策:** [メタデータの編集](edit-metadata.md)を使うか、または元のデータセットを変更して指定された列インデックスの列名を同じにします。  

|例外メッセージ|
|------------------------|
|Columns with corresponding index in input datasets have different names. (入力データセット内の対応するインデックスに伴う列の名前が異なります。)|
|Column names are not the same for column {col_index} (zero-based) of input datasets ({dataset1} and {dataset2} respectively). (入力データセット ({dataset1} と {dataset2}) それぞれの列 {col_index} (ゼロ ベース) に対する列の名前が同じではありません。)|


## <a name="error-0011"></a>エラー 0011  
 渡された列セット引数がデータセットのどの列にも適用されない場合、例外が発生します。  

 Azure Machine Learning では、指定された列の選択が指定されたデータセットのどの列とも一致しない場合、このエラーを受け取ります。  

 モジュールが動作するために少なくとも 1 つの列が必要なときに列が選択されていない場合も、このエラーが発生することがあります。  

**解決策:** データセット内の列に適用されるように、モジュールでの列の選択を変更します。  

 特定の列 (ラベル列など) を選択する必要があるモジュールの場合、正しい列が選択されていることを確認します。  

 不適切な列が選択されている場合は、それらを削除し、パイプラインを再実行します。  

|例外メッセージ|
|------------------------|
|Specified column set does not apply to any of dataset columns. (指定された列セットは、データセットのどの列にも適用されません。)|
|Specified column set "{column_set}" does not apply to any of dataset columns. (指定された列セット "{column_set}" が、データセットのどの列にも適用されません。)|


## <a name="error-0012"></a>エラー 0012  
 渡された引数のセットでクラスのインスタンスを作成できなかった場合、例外が発生します。  

**解決策:** このエラーはユーザーでは対処できず、将来のリリースで非推奨になります。  

|例外メッセージ|
|------------------------|
|Untrained model, please train model first. (未トレーニングのモデルです。先に、モデルをトレーニングしてください。)|
|Untrained model ({arg_name}), use trained model. (未トレーニングのモデル ({arg_name}) です。トレーニング済みのモデルを使用してください。)|


## <a name="error-0013"></a>エラー 0013  
 モジュールに渡された学習器が無効な種類の場合、例外が発生します。  

 このエラーは、トレーニング済みモデルと接続されたスコアリング モジュールとの間に互換性がないときに、常に発生します。 <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the pipeline is run.  -->

**解決策:**

トレーニング モジュールによって生成される学習器の種類を特定し、学習器に対する適切なスコアリング モジュールを決定します。 

モデルが特別なトレーニング モジュールを使ってトレーニングされた場合、対応する特別なスコアリング モジュールにのみ、トレーニング済みモデルを接続します。 


|モデルの種類|トレーニング モジュール| スコアリング モジュール|
|----|----|----|
|任意の分類子|[モデルのトレーニング](train-model.md) |[モデルのスコア付け](score-model.md)|
|任意の回帰モデル|[モデルのトレーニング](train-model.md) |[モデルのスコア付け](score-model.md)|

<!--| clustering models| [Train Clustering Model](train-clustering-model.md) or [Sweep Clustering](sweep-clustering.md)| [Assign Data to Clusters](assign-data-to-clusters.md)|
| anomaly detection - One-Class SVM | [Train Anomaly Detection Model](train-anomaly-detection-model.md) |[Score Model](score-model.md)|
| anomaly detection - PCA |[Train Model](train-model.md) |[Score Model](score-model.md) </br> Some additional steps are required to evaluate the model. |
| anomaly detection - time series|  [Time Series Anomaly Detection](time-series-anomaly-detection.md) |Model trains from data and generates scores. The module does not create a trained learner and no additional scoring is required. |
| recommendation model| [Train Matchbox Recommender](train-matchbox-recommender.md) | [Score Matchbox Recommender](score-matchbox-recommender.md) |
| image classification | [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md) | [Score Model](score-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) | [Score Vowpal Wabbit Version 7-4 Model](score-vowpal-wabbit-version-7-4-model.md) |   
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-10 Model](train-vowpal-wabbit-version-7-10-model.md) | [Score Vowpal Wabbit Version 7-10 Model](score-vowpal-wabbit-version-7-10-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) | [Score Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) |-->

|例外メッセージ|
|------------------------|
|Learner of invalid type is passed. (無効な種類の学習器が渡されました。)|
|Learner "{arg_name}" has invalid type. (学習器 "{arg_name}" の種類が無効です。)|
|Learner "{arg_name}" has invalid type "{learner_type}". (学習器 "{arg_name}" の種類 "{learner_type}" は無効です。)|
|Learner of invalid type is passed. (無効な種類の学習器が渡されました。) Exception message: {exception_message} (例外メッセージ: {exception_message})|


## <a name="error-0014"></a>エラー 0014  
 列の一意の値の数が許容されているより多い場合、例外が発生します。  

 このエラーは、ID 列やテキスト列など、列に含まれる一意の値の数が多すぎる場合に発生します。 列がカテゴリ データとして処理するように指定されていても、列の一意の値が多すぎて処理を完了できない場合、このエラーが発生する可能性があります。 また、2 つの入力の間で一意の値の数が一致していない場合にも、このエラーが発生する可能性があります。   

次の条件の **両方** を満たした場合に、一意の値が許可される数を上回るエラーが発生します。

- 1 つの列の 97% を超えるインスタンスが一意の値であるため、ほぼすべてのカテゴリが互いに異なっています。
- 1 つの列に 1,000 個を超える一意の値が含まれています。

**解決策:**

エラーが発生したモジュールを開き、入力として使用されている列を特定します。 一部のモジュールでは、データセット入力を右クリックして **[視覚化]** を選択し、一意の値の数とその分布が含まれる個々の列の統計情報を取得できます。

グループ化または分類に使用する列の場合は、列の一意の値の数を減らすための手順を実行します。 列のデータ型に応じて、さまざまな方法で減らすことができます。 

モデル トレーニング中の意味のある特徴ではない ID の場合、[[メタデータの編集]](../algorithm-module-reference/edit-metadata.md) を使用してその列を **[Clear feature]\(特徴のクリア\)** としてマークすれば、モデルのトレーニングで使用されなくなります。 

テキスト列の場合、[特徴ハッシュ](../algorithm-module-reference/feature-hashing.md)や[テキストからの N gram 特徴抽出モジュール](../algorithm-module-reference/extract-n-gram-features-from-text.md)を利用し、テキスト列を事前処理できます。
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> シナリオに合った解決策が見つかりませんか。 このトピックのフィードバックとして、エラーが発生したモジュールの名前と、列のデータ型およびカーディナリティを提供してください。 その情報を使用して、一般的なシナリオに対してさらに対象を絞り込んだトラブルシューティング手順を提供します。   

|例外メッセージ|
|------------------------|
|Amount of column unique values is greater than allowed. (列の一意の値の量が許容値を超えています。)|
|Number of unique values in column: "{column_name}" is greater than allowed. (列 "{column_name}" の一意の値の数が許容される数を超えています。)|
|Number of unique values in column: "{column_name}" exceeds tuple count of {limitation}. (列 "{column_name}" の一意の値の数がタプル数 {limitation} を超えています。)|


## <a name="error-0015"></a>エラー 0015  
 データベース接続が失敗した場合、例外が発生します。  

 正しくない SQL アカウント名、パスワード、データベース サーバー、またはデータベース名を入力した場合、またはデータベースやサーバーの問題が原因でデータベースとの接続を確立できない場合、このエラーを受け取ります。  

**解決策:** アカウント名、パスワード、データベース サーバー、およびデータベースを正しく入力したこと、および指定したアカウントに適切なレベルのアクセス許可があることを確認します。 データベースが現在アクセス可能であることを確認します。  

|例外メッセージ|
|------------------------|
|Error making database connection. (データベースとの接続でエラーが発生しました。)|
|Error making database connection: {connection_str}. (データベースとの接続中にエラーが発生しました: {connection_str}。)|


## <a name="error-0016"></a>エラー 0016  
 モジュールに渡される入力データセットには互換性のある列の型が含まれる必要があっても、そうなっていない場合、例外が発生します。  

 Azure Machine Learning では、2 つ以上のデータセットで渡される列の型の間に互換性がない場合、このエラーを受け取ります。  

**解決策:** [メタデータの編集](edit-metadata.md)を使うか、または元の入力データセットを修正して<!--, or use [Convert to Dataset](convert-to-dataset.md)--> 列の型が互換性を備えるようにします。  

|例外メッセージ|
|------------------------|
|Columns with corresponding index in input datasets do have incompatible types. (入力データセット内の対応するインデックスを伴う列に互換性のない型が含まれています。)|
|Columns '{first_col_names}' are incompatible between train and test data. (トレーニングとテストのデータの間で、列 '{first_col_names}' に互換性がありません。)|
|Columns '{first_col_names}' and '{second_col_names}' are incompatible. (列 '{first_col_names}' と '{second_col_names}' には互換性がありません。)|
|Column element types are not compatible for column '{first_col_names}' (zero-based) of input datasets ({first_dataset_names} and {second_dataset_names} respectively). (入力データセット ({first_dataset_names} と {second_dataset_names}) の列 '{first_col_names}' (ゼロから始まる) で、列の要素型に互換性がありません。)|


## <a name="error-0017"></a>エラー 0017  
 選択した列で現在のモジュールによってサポートされていないデータ型が使われている場合、例外が発生します。  

 Azure Machine Learning では、たとえば、列の選択にモジュールによって処理できないデータ型の列が含まれている場合 (算術演算に対する文字列型の列、カテゴリ特徴列が必要な場合のスコア列など)、このエラーを受け取る可能性があります。  

**解決策:**
 1. 問題となっている列を特定します。
 2. モジュールの要件を確認します。
 3. 要件に準拠するように列を修正します。 列、および試みている変換によっては、次のモジュールをいくつか使用して変更を行うことが必要な場合があります。
    + [メタデータの編集](edit-metadata.md)を使って、列のデータ型を変更するか、列の使用法を変更します (特徴から数値、カテゴリから非カテゴリなど)。
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. 最後の手段として、元の入力データセットの修正が必要になる可能性があります。

> [!TIP]
> シナリオに合った解決策が見つかりませんか。 このトピックのフィードバックとして、エラーが発生したモジュールの名前と、列のデータ型およびカーディナリティを提供してください。 その情報を使用して、一般的なシナリオに対してさらに対象を絞り込んだトラブルシューティング手順を提供します。 

|例外メッセージ|
|------------------------|
|Cannot process column of current type. (現在の型の列を処理することができません。) The type is not supported by the module. (型がモジュールでサポートされていません。)|
|Cannot process column of type {col_type}. (型 {col_type} の列を処理できません。) The type is not supported by the module. (型がモジュールでサポートされていません。)|
|Cannot process column "{col_name}" of type {col_type}. (型 {col_type} の列 "{col_name}" を処理できません。) The type is not supported by the module. (型がモジュールでサポートされていません。)|
|Cannot process column "{col_name}" of type {col_type}. (型 {col_type} の列 "{col_name}" を処理できません。) The type is not supported by the module. (型がモジュールでサポートされていません。) Parameter name: {arg_name}. (パラメーター名: {arg_name}。)|


## <a name="error-0018"></a>エラー 0018  
 入力データセットが有効でない場合、例外が発生します。  

**解決策:** Azure Machine Learning ではこのエラーが多くのコンテキストで発生するので、解決策は 1 つではありません。 一般に、このエラーは、モジュールへの入力として提供されたデータの列の数が間違っていること、またはデータ型がモジュールの要件と一致しないことを示します。 次に例を示します。  

-   モジュールにはラベル列が必要ですが、ラベルとしてマークされた列がないか、またはラベル列がまだ選択されていません。  
  
-   モジュールではデータはカテゴリである必要がありますが、データが数値です。  

<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->

-   データの形式が正しくありません。  
  
-   インポートされたデータに、無効な文字、無効な値、または範囲外の値が含まれています。  
-   列が空か、または欠損値が多く含みすぎています。  

 要件およびデータをどうすればよいか確認するには、入力としてデータセットを使用するモジュールのヘルプ トピックをご覧ください。  

 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->.  

|例外メッセージ|
|------------------------|
|Dataset is not valid. (データセットが無効です。)|
|{dataset1} contains invalid data. ({dataset1} に無効なデータが含まれています。)|
|{dataset1} and {dataset2} should be consistent columnwise. ({dataset1} と {dataset2} には、列方向で一貫性がある必要があります。)|
|{dataset1} contains invalid data, {reason}. ({dataset1} に無効なデータが含まれています {reason}。)|
|{dataset1} contains {invalid_data_category}. ({dataset1} に {invalid_data_category} が含まれています。) {troubleshoot_hint}|
|{dataset1} is not valid, {reason}. ({dataset1} は無効です。{reason}。) {troubleshoot_hint}|


## <a name="error-0019"></a>エラー 0019  
 並べ替えられた値が含まれると予想される列がそうなっていない場合、例外が発生します。  

 Azure Machine Learning では、指定された列の値が正しい順序になっていない場合、このエラーを受け取ります。  

**解決策:** 入力データセットを手動で変更して列の値を並べ替えた後、モジュールを再実行します。  

|例外メッセージ|
|------------------------|
|Values in column are not sorted. (列の値が並べ替えられていません。)|
|Values in column "{col_index}" are not sorted. (列 "{col_index}" の値が並べ替えられていません。)|
|Values in column "{col_index}" of dataset "{dataset}" are not sorted. (データセット "{dataset}" の列 "{col_index}" の値が並べ替えられていません。)|
|Values in argument "{arg_name}" are not sorted in "{sorting_order}" order. (引数 "{arg_name}" の値が "{sorting_order}" の順序で並べ替えられていません。)|


## <a name="error-0020"></a>エラー 0020  
 モジュールに渡された一部のデータセットの列の数が少なすぎる場合、例外が発生します。  

 Azure Machine Learning では、モジュールに対して十分な列が選択されてされていない場合、このエラーを受け取ります。  

**解決策:** モジュールを見直し、列セレクターで正しい数の列が選択されていることを確認します。  

|例外メッセージ|
|------------------------|
|Number of columns in input dataset is less than allowed minimum. (入力データセット内の列数が、許容される最少数未満です。)|
|Number of columns in input dataset "{arg_name}" is less than allowed minimum. (入力データセット "{arg_name}" 内の列数が、許容される最小値を下回っています。)|
|Number of columns in input dataset is less than allowed minimum of {required_columns_count} column(s). (入力データセットの列数が、許容される最小数の {required_columns_count} 列未満です。)|
|Number of columns in input dataset "{arg_name}" is less than allowed minimum of {required_columns_count} column(s). (入力データセット "{arg_name}" の列数が、許容される最小数の {required_columns_count} 列未満です。)|


## <a name="error-0021"></a>エラー 0021  
 モジュールに渡された一部のデータセットの行の数が少なすぎる場合、例外が発生します。  

 Azure Machine Learning では、指定された操作を実行しても、十分な行がデータセット内にない場合、このエラーが表示されます。 たとえば、入力データセットが空の場合、または最少数の行を有効にするために必要な操作を実行しようとした場合、このエラーが表示される可能性があります。 そのような操作の例としては、統計的手法に基づくグループ化や分類、特定の種類のビン分割、カウントを使用した学習などがあります。  

**解決策:**

 + エラーを返したモジュールを開き、入力データセットとモジュールのプロパティを確認します。 
 + 入力データセットが空ではないこと、およびモジュールのヘルプで説明されている要件を満たすのに十分な行数のデータがあることを確認します。  
 + データが外部ソースから読み込まれる場合は、データ ソースが使用可能であること、およびインポート処理で取得される行数が少なくなる原因となるエラー、またはデータ定義の変更がないことを確認します。
 + データの型や値の数に影響する可能性のある操作 (クリーニング、分割、結合操作など) をモジュールのデータ アップストリームで実行している場合は、それらの操作の出力を調べて、返される行数を確認します。  

|例外メッセージ|
|------------------------|
|Number of rows in input dataset is less than allowed minimum. (入力データセット内の行数が、許容される最小数未満です。)|
|Number of rows in input dataset is less than allowed minimum of {required_rows_count} row(s). (入力データセット内の行数が、許容される最小数の {required_rows_count} 行未満です。)|
|Number of rows in input dataset is less than allowed minimum of {required_rows_count} row(s). (入力データセット内の行数が、許容される最小数の {required_rows_count} 行未満です。) {reason}|
|Number of rows in input dataset "{arg_name}" is less than allowed minimum of {required_rows_count} row(s). (入力データセット "{arg_name}" 内の行数が、許容される最小数の {required_rows_count} 行未満です。)|
|Number of rows in input dataset "{arg_name}" is {actual_rows_count}, less than allowed minimum of {required_rows_count} row(s). (入力データセット "{arg_name}" 内の行数は {actual_rows_count} で、許容される最小数の {required_rows_count} 行未満です。)|
|Number of "{row_type}" rows in input dataset "{arg_name}" is {actual_rows_count}, less than allowed minimum of {required_rows_count} row(s). (入力データセット "{arg_name}" 内の "{row_type}" 行の数は {actual_rows_count} で、許容される最小数の {required_rows_count} 行未満です。)|


## <a name="error-0022"></a>エラー 0022  
 入力データセットで選択された列の数が、予想される数と等しくない場合、例外が発生します。  

 Azure Machine Learning では、ダウンストリームのモジュールまたは操作で特定の数の列または入力が必要なときに、提供する列または入力が多すぎるか少なすぎる場合、このエラーが発生します。 次に例を示します。  

-   1 つのラベル列またはキー列を指定するときに、誤って複数の列を選択している。  
  
-   列の名前を変更するときに、列より多い、または少ない数の名前を指定した。  
  
-   ソースまたはターゲットの列の数が変更された、またはモジュールによって使用される列の数と一致しない。  
  
-   入力に対して値のコンマ区切りリストを指定したが、値の数が一致しない、または複数の入力がサポートされていない。  

**解決策:** モジュールを見直し、列の選択を調べて、正しい数の列が選択されていることを確認します。 アップストリームのモジュールの出力と、ダウンストリームの操作の要件を確認します。  

 複数の列を選択できる列選択オプションのいずれかを使った場合 (列のインデックス、すべての特徴、すべての数字など)、選択によって返される列の正確な数を確認します。  

 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->

 アップストリームの列の数または型が変更されていないことを確認します。  

 推奨データセットを使ってモデルをトレーニングしている場合、レコメンダーでは、ユーザーと項目のペアまたはユーザーと項目とランキングに対応する限られた列数が予想されることに忘れないでください。 モデルをトレーニングする前、または推奨データセットを分割する前に、追加の列を削除します。 詳しくは、「[Split Data (データの分割)](split-data.md)」をご覧ください。  

|例外メッセージ|
|------------------------|
|Number of selected columns in input dataset does not equal to the expected number. (入力データセットで選択された列の数が、予想される数と等しくありません。)|
|Number of selected columns in input dataset does not equal to {expected_col_count}. (入力データセットで選択された列の数が、{expected_col_count} と等しくありません。)|
|Column selection pattern "{selection_pattern_friendly_name}" provides number of selected columns in input dataset not equal to {expected_col_count}. (列選択パターン "{selection_pattern_friendly_name}" で提供される入力データセット内で選択された列の数が、{expected_col_count} と等しくありません。)|
|Column selection pattern "{selection_pattern_friendly_name}" is expected to provide {expected_col_count} column(s) selected in input dataset, but {selected_col_count} column(s) is/are actually provided. (列選択パターン "{selection_pattern_friendly_name}" では入力データセット内で選択された {expected_col_count} 列が指定される必要がありますが、実際には {selected_col_count} 列が指定されています。)|


## <a name="error-0023"></a>エラー 0023  

入力データセットのターゲット列が現在のトレーナー モジュールに対して有効ではない場合、例外が発生します。  

Azure Machine Learning では、(モジュールのパラメーターで選択されている) ターゲット列が、有効なデータ型ではない場合、欠損値がすべて含まれる場合、または予想されるカテゴリではない場合、このエラーが発生します。  

**解決策:** モジュールの入力を見直し、ラベル/ターゲット列の内容を調べます。 欠損の値がすべてないことを確認します。 モジュールでターゲット列がカテゴリである必要がある場合、ターゲット列に複数の個別値があることを確認します。  

|例外メッセージ|
|------------------------|
|Input dataset has unsupported target column. (入力データセットにサポートされていないターゲット列があります。)|
|Input dataset has unsupported target column "{column_index}". (入力データセットにサポートされていないターゲット列 "{column_index}" があります。)|
|Input dataset has unsupported target column "{column_index}" for learner of type {learner_type}. (入力データセットに {learner_type} 型の学習器に対してサポートされていないターゲット列 "{column_index}" があります。)|


## <a name="error-0024"></a>エラー 0024  
データセットにラベル列が含まれていない場合、例外が発生します。  

 Azure Machine Learning では、モジュールでラベル列が必要であってもデータセットにラベル列がない場合、このエラーが発生します。 たとえば、スコアリングされたデータセットの評価では、通常、正確さのメトリックを計算するためにラベル列が存在する必要があります。  

また、ラベル列がデータセットに存在していても、Azure Machine Learning によって正しく検出されない場合にも、発生することがあります。

**解決策:**

+ エラーを生成したモジュールを開き、ラベル列が存在するかどうかを確認します。 予測しようとしている単一の結果 (または従属変数) が列に含まれてさえいれば、列の名前またはデータ型は関係ありません。 どの列にラベルがあるか不明な場合は、*クラス* や *ターゲット* などの汎用的な名前を探します。 
+  データセットにラベル列が含まれていない場合は、ラベル列がアップストリームで明示的に、または誤って削除された可能性があります。 また、データセットがアップストリームのスコアリング モジュールの出力ではない可能性もあります。
+ ラベル列として列を明示的にマークするには、[メタデータの編集](edit-metadata.md)モジュールを追加して、データセットを接続します。 ラベル列だけを選択し、 **[フィールド]** ドロップダウン リストから **[ラベル]** を選択します。 
+ 間違った列をラベルとして選択した場合は、 **[フィールド]** から **[ラベルのクリア]** を選択して、列のメタデータを修正できます。 
  
|例外メッセージ|
|------------------------|
|There is no label column in dataset. (データセットにラベル列がありません。)|
|There is no label column in "{dataset_name}". ("{dataset_name}" にはラベル列がありません。)|


## <a name="error-0025"></a>エラー 0025  
 データセットにスコア列が含まれていない場合、例外が発生します。  

 Azure Machine Learning では、評価モデルへの入力に有効なスコア列が含まれていない場合、このエラーが発生します。 たとえば、適切なトレーニング済みモデルでデータセットがスコアリングされる前にユーザーがデータセットを評価しようとしているか、またはアップストリームでスコア列が明示的に削除されています。 2 つのデータセットのスコア列に互換性がない場合も、この例外が発生します。 たとえば、線形リグレッサーの精度をバイナリ分類器と比較しようとしている可能性があります。  

**解決策:** 評価モデルへの入力を見直し、スコア列が 1 つ以上含まれているかどうかを調べます。 そうでない場合、データセットがスコアリングされなかったか、またはアップストリームのモジュールでスコア列が削除されています。  

|例外メッセージ|
|------------------------|
|There is no score column in dataset. (データセットにスコア列がありません。)|
|There is no score column in "{dataset_name}". ("{dataset_name}" にスコア列がありません。)|
|There is no score column in "{dataset_name}" that is produced by a "{learner_type}". ("{learner_type}" によって生成されたスコア列が "{dataset_name}" にありません。) Score the dataset using the correct type of learner. (適切な種類の学習器を使用してデータセットをスコアリングしてください。)|


## <a name="error-0026"></a>エラー 0026  
 同じ名前の列が許可されていない場合、例外が発生します。  

 Azure Machine Learning では、複数の列の名前が同じ場合、このエラーが発生します。 このエラーを受け取る方法の 1 つとして、データセットにヘッダー行がなく、次のような列名が自動的に割り当てられる場合があります。(Col0、Col1 など)  

**解決策:** 複数の列に同じ名前が付けられている場合は、入力データセットとモジュールの間に [メタデータの編集](edit-metadata.md)モジュールを挿入します。 [メタデータの編集](edit-metadata.md)で列セレクターを使って名前を変更する列を選択し、 **[新しい列名]** テキスト ボックスに新しい名前を入力します。  

|例外メッセージ|
|------------------------|
|Equal column names are specified in arguments. (同じ列名が引数で指定されています。) Equal column names are not allowed by module. (モジュールでは同じ列名は許可されません。)|
|Equal column names in arguments "{arg_name_1}" and "{arg_name_2}" are not allowed. (引数 "{arg_name_1}" と "{arg_name_2}" での同じ列名は許可されません。) Please specify different names. (別の名前を指定してください。)|


## <a name="error-0027"></a>エラー 0027  
 同じサイズでなければならない 2 つのオブジェクトのサイズが異なる場合、例外が発生します。  

 これは Azure Machine Learning の一般的なエラーであり、多くの状況で発生することがあります。  

**解決策:** 特定の解決策はありません。 ただし、次のような状況を確認できます。  

-   列の名前を変更する場合、各リスト (入力列と新しい名前の一覧) に同じ数の項目があることを確認します。  
  
-   2 つのデータセットを結合または連結する場合、同じスキーマであることを確認します。  
  
-   複数の列を含む 2 つのデータセットを結合する場合は、キー列が同じデータ型であることを確認し、 **[Allow duplicates and preserve column order in selection]\(選択範囲内で重複を許可し、順序を維持する\)** オプションを選択します。  

|例外メッセージ|
|------------------------|
|The size of passed objects is inconsistent. (渡されたオブジェクトのサイズが一致していません。)|
|The size of "{friendly_name1}" is inconsistent with size of "{friendly_name2}". ("{friendly_name1}" のサイズが "{friendly_name2}" のサイズと一致していません。)|


## <a name="error-0028"></a>エラー 0028  
 列セットに重複する列名が含まれていて、それが許可されない場合、例外が発生します。  

 Azure Machine Learning では、列名が重複している場合、つまり一意ではない場合、このエラーが発生します。  

**解決策:** 同じ名前の列がある場合は、入力データセットと、エラーが発生したモジュールの間に、[メタデータの編集](edit-metadata.md)のインスタンスを追加します。 [メタデータの編集](edit-metadata.md)で列セレクターを使って名前を変更する列を選択し、 **[新しい列名]** テキスト ボックスに新しい列名を入力します。 複数の列の名前を変更する場合は、 **[新しい列名]** に入力する値が一意であることを確認します。  

|例外メッセージ|
|------------------------|
|Column set contains duplicated column name(s). (列セットに重複する列名が含まれます。)|
|The name "{duplicated_name}" is duplicated. (名前 "{duplicated_name}" が重複しています。)|
|The name "{duplicated_name}" is duplicated in "{arg_name}". (名前 "{duplicated_name}" が "{arg_name}" で重複しています。)|
|The name "{duplicated_name}" is duplicated. (名前 "{duplicated_name}" が重複しています。) Details: {details} (詳細: {details})|


## <a name="error-0029"></a>エラー 0029  
 無効な URI が渡された場合、例外が発生します。  

 Azure Machine Learning では、無効な URI が渡された場合、このエラーが発生します。  次の条件のいずれかに該当する場合は、このエラーが発生します。  

-   読み取りまたは書き込みのために Azure Blob Storage に対して提供されたパブリック URI または SAS URI に、エラーが含まれます。  
  
-   SAS の時間枠の期限が切れています。  
  
-   HTTP ソースによる Web URL が、ファイルまたはループバック URI を表しています。  
  
-   HTTP による Web URL に、正しくない形式の URL が含まれています。  
  
-   リモート ソースで URL を解決できません。  

**解決策:** モジュールを見直し、URI の形式を検証します。 データ ソースが HTTP による Web URL の場合は、目的のソースがファイルまたはループバック URI (localhost) ではないことを確認します。  

|例外メッセージ|
|------------------------|
|Invalid Uri is passed. (無効な URI が渡されました。)|
|The Uri "{invalid_url}" is invalid. (URI "{invalid_url}" が無効です。)|


## <a name="error-0030"></a>エラー 0030  
 ファイルをダウンロードできない場合、例外が発生します。  

 Azure Machine Learning では、ファイルをダウンロードできない場合、この例外が発生します。 再試行の試みが 3 回失敗した後に HTTP ソースからの読み取りの試みが失敗すると、この例外を受け取ります。  

**解決策:** HTTP ソースに対する URI が正しいこと、および現在インターネット経由でサイトにアクセスできることを確認します。  

|例外メッセージ|
|------------------------|
|Unable to download a file. (ファイルをダウンロードできません。)|
|Error while downloading the file: {file_url} (ファイルのダウンロード中にエラーが発生しました: {file_url}。)|


## <a name="error-0031"></a>エラー 0031  
 列セット内の列の数が必要とされるより少ない場合、例外が発生します。  

 Azure Machine Learning では、選択された列の数が必要とされるより少ない場合、このエラーが発生します。  必要最少数の列が選択されていない場合、このエラーを受け取ります。  

**解決策:** **列セレクター** を使って、列の選択に列を追加します。  

|例外メッセージ|
|------------------------|
|Number of columns in column set is less than required. (列セット内の列の数が必要な数に達していません。)|
|At least {required_columns_count} column(s) should be specified for input argument "{arg_name}". (入力引数 "{arg_name}" には、少なくとも {required_columns_count} 個の列を指定する必要があります。)|
|At least {required_columns_count} column(s) should be specified for input argument "{arg_name}". (入力引数 "{arg_name}" には、少なくとも {required_columns_count} 個の列を指定する必要があります。) The actual number of specified columns is {input_columns_count}. (実際に指定された列の数は {input_columns_count} です。)|


## <a name="error-0032"></a>エラー 0032  
 引数が数値ではない場合、例外が発生します。  

 Azure Machine Learning では、引数が倍精度浮動小数点数型または NaN の場合、このエラーを受け取ります。  

**解決策:** 有効な値を使うように、指定された引数を修正します。  

|例外メッセージ|
|------------------------|
|Argument is not a number. (引数が数値ではありません。)|
|"{arg_name}" is not a number. ("{arg_name}" が数値ではありません。)|


## <a name="error-0033"></a>エラー 0033  
 引数が無限である場合、例外が発生します。  

 Azure Machine Learning では、引数が無限である場合、このエラーが発生します。 引数が `double.NegativeInfinity` または `double.PositiveInfinity` のいずれかの場合、このエラーを受け取ります。  

**解決策:** 有効な値になるように、指定された引数を修正します。  

|例外メッセージ|
|------------------------|
|Argument must be finite. (引数は有限である必要があります。)|
|"{arg_name}" is not finite. ("{arg_name}" が有限ではありません。)|
|Column "{column_name}" contains infinite values. (列 "{column_name}" に無限の値が含まれています。)|


## <a name="error-0034"></a>エラー 0034  
 特定のユーザーと項目のペアに対して複数の評価が存在する場合、例外が発生します。  

 Azure Machine Learning では、ユーザーと項目のペアに複数の評価値がある場合、推奨でこのエラーが発生します。  

**解決策:** ユーザーと項目のペアで評価値が 1 つのみ所有されていることを確認します。  

|例外メッセージ|
|------------------------|
|More than one rating exists for the value(s) in dataset. (データセット内の値に対して複数の評価が存在します。)|
|More than one rating for user {user} and item {item} in rating prediction data table. (評価予測データ テーブルに、ユーザー {user} と項目 {item} に対する複数の評価があります。)|
|More than one rating for user {user} and item {item} in {dataset}. ({dataset} に、ユーザー {user} と項目 {item} に対する複数の評価があります。)|


## <a name="error-0035"></a>エラー 0035  
 特定のユーザーまたは項目に対して特徴が提供されなかった場合、例外が発生します。  

 Azure Machine Learning では、スコアリングに対して推奨モデルを使おうとしていても、特徴ベクターが見つからない場合、このエラーが発生します。  

**解決策:**

マッチボックス レコメンダーには、項目の特徴またはユーザーの特徴のいずれかを使うときに満たす必要がある特定の要件があります。  このエラーは、入力として指定したユーザーまたは項目に特徴ベクターがないことを示します。 各ユーザーまたは項目のデータで特徴ベクターが使用できることを確認します。  

 たとえば、ユーザーの年齢、場所、収入などの特徴を使って推奨モデルをトレーニングしたものの、トレーニング中に認識されなかった新しいユーザーのスコアを作成する場合、それらを適切に予測するには、新しいユーザーに対する同等の特徴のセット (つまり、年齢、場所、収入の値) を指定する必要があります。 

 これらのユーザーに対する特徴がない場合は、適切な特徴を生成するための特徴エンジニアリングを検討します。  たとえば、個々のユーザーの年齢や収入の値がない場合は、ユーザーのグループに対して使用するおおよその値を生成する場合があります。 

<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).

For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->

 > [!TIP]
 > 解決策が自分のケースに該当しませんか。 この記事のフィードバックを送信し、モジュールや列の行数など、シナリオについての情報を提供してください。 この情報を使用して、今後さらに詳細なトラブルシューティング手順を提供します。

|例外メッセージ|
|------------------------|
|No features were provided for a required user or item. (必要なユーザーまたは項目に特徴が提供されませんでした。)|
|Features for {required_feature_name} required but not provided. ({Required_feature_name} に特徴が必須ですが、指定されていません。)|


## <a name="error-0036"></a>エラー 0036  
 特定のユーザーまたは項目に対して複数の特徴ベクターが提供された場合、例外が発生します。  

 Azure Machine Learning では、特徴ベクターが複数回定義された場合、このエラーが発生します。  

**解決策:** 特徴ベクターが複数回定義されていないことを確認します。  

|例外メッセージ|
|------------------------|
|Duplicate feature definition for a user or item. (ユーザーまたは項目に対する特徴の定義が重複しています。)|


## <a name="error-0037"></a>エラー 0037  
 複数のラベル列が指定されているときに、ラベル列が 1 つのみ許可される場合、例外が発生します。  

 Azure Machine Learning では、新しいラベル列として複数の列が選択された場合、このエラーが発生します。 ほとんどの教師あり学習アルゴリズムでは、1 つの列がターゲットまたはラベルとしてマークされている必要があります。  

**解決策:** 新しいラベル列として 1 つの列を選択します。  

|例外メッセージ|
|------------------------|
|Multiple label columns are specified. (複数のラベル列が指定されています。)|
|Multiple label columns are specified in "{dataset_name}". ("{dataset_name}" に複数のラベル列が指定されています。)|


## <a name="error-0039"></a>エラー 0039  
 操作が失敗した場合、例外が発生します。  

 Azure Machine Learning では、内部操作を完了できない場合、このエラーが発生します。  

**解決策:** このエラーは多くの状況によって発生し、特定の解決策はありません。  
 次の表はこのエラーに対する一般的なメッセージであり、後で状況について具体的に説明します。 

 使用可能な詳細情報がない場合は、[Microsoft Q&A 質問ページでフィードバックを送信](/answers/topics/azure-machine-learning-studio-classic.html)して、エラーが発生したモジュールおよび関連する状況に関する情報を提供してください。

|例外メッセージ|
|------------------------|
|操作に失敗しました。|
|Error while completing operation: "{failed_operation}". (操作 "{failed_operation}" の完了中にエラーが発生しました。)|
|Error while completing operation: "{failed_operation}". (操作 "{failed_operation}" の完了中にエラーが発生しました。) Reason: "{reason}". (理由: "{reason}"。)|


## <a name="error-0042"></a>エラー 0042  
 列を別の型に変換できないと、例外が発生します。  

 Azure Machine Learning では、列を指定された型に変換できない場合、このエラーが発生します。  モジュールで特定のデータ型 (日時、テキスト、浮動小数点数、整数など) が必要であっても、既存の列を必要な型に変換できない場合、このエラーが発生します。  

たとえば、列を選択し、算術演算で使用するために数値データ型に変換しようとした場合、列に無効なデータが含まれていると、このエラーが発生する可能性があります。 

このエラーが発生する可能性のあるもう 1 つの理由としては、浮動小数点数または多くの一意の値が含まれる列をカテゴリ列として使おうとする場合です。 

**解決策:**

+ エラーが生成されたモジュールのヘルプ ページを開き、データ型の要件を確認します。
+ 入力データセットで列のデータ型を確認します。
+ いわゆるスキーマレス データ ソースで発生するデータを調べます。
+ 目的のデータ型への変換を妨げる可能性がある欠損値または特殊文字がないか、データセットを確認します。 
    + 数値データ型は一貫している必要があります。たとえば、整数の列で浮動小数点数を確認します。
    + 数値列でテキスト文字列または NA 値を探します。 
    + ブール値は、必要なデータ型に応じて、適切な表現に変換できます。
    + 非 Unicode 文字、タブ文字、または制御文字がないか、テキスト列を調べます
    + モデリング エラーを回避するには日時データが一貫している必要がありますが、形式が多いためクリーンアップが複雑になることがあります。 次の使用を検討します <!--the [Execute R Script](execute-r-script.md) or -->[Python スクリプトの実行](execute-python-script.md)モジュールを使ってクリーンアップを実行します。  
+ 必要な場合は、列を正常に変換できるように、入力データセット内の値を修正します。 修正には、ビン分割、切り捨てまたは丸め処理、外れ値の削除、または欠損値の補完などがあります。 機械学習での一般的なデータ変換シナリオについては、次の記事をご覧ください。
    + [見つからないデータのクリーンアップ](clean-missing-data.md)
    + [データの正規化](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->

> [!TIP]
> 解決策がわからない、または自分のケースに該当しませんか。 この記事のフィードバックを送信し、モジュールや列のデータ型など、シナリオについての情報を提供してください。 この情報を使用して、今後さらに詳細なトラブルシューティング手順を提供します。  

|例外メッセージ|
|------------------------|
|Not allowed conversion. (変換は許可されていません。)|
|Could not convert column of type {type1} to column of type {type2}. (型 {type1} の列を型 {type2} の列に変換できませんでした。)|
|Could not convert column "{col_name1}" of type {type1} to column of type {type2}. (型 {type1} の列 "{col_name1}" を型 {type2} の列に変換できませんでした。)|
|Could not convert column "{col_name1}" of type {type1} to column "{col_name2}" of type {type2}. (型 {type1} の列 "{col_name1}" を型 {type2} の列 "{col_name2}" に変換できませんでした。)|


## <a name="error-0044"></a>エラー 0044  
 既存の値から列の要素型を派生できないと、例外が発生します。  

 Azure Machine Learning では、データセットの 1 つまたは複数の列の型を推論できない場合、このエラーが発生します。 これは通常、要素型が異なる複数のデータセットを連結するときに発生します。 Azure Machine Learning では、情報を失うことなく、複数の列のすべての値を表すことができる一般的な型を決定できない場合、このエラーが生成されます。  

**解決策:** 結合される両方のデータセットの特定の列のすべての値が、同じ型であるか (数値、ブール値、カテゴリ、文字列、日付など)、または同じ型に変換できることを確認します。  

|例外メッセージ|
|------------------------|
|Cannot derive element type of the column. (列の要素型を派生できません。)|
|Cannot derive element type for column "{column_name}" -- all the elements are null references. (列 "{column_name}" の要素型を派生できません -- すべての要素が null 参照です。)|
|Cannot derive element type for column "{column_name}" of dataset "{dataset_name}" -- all the elements are null references. (データセット "{dataset_name}" の列 "{column_name}" の要素型を派生できません -- すべての要素が null 参照です。)|


## <a name="error-0045"></a>エラー 0045  
 ソースでの混合要素型のため列を作成できないと、例外が発生します。  

 Azure Machine Learning では、結合される 2 つのデータセットの要素型が異なる場合、このエラーが生成されます。  

**解決策:** 結合される両方のデータセットの特定の列のすべての値が、同じ型 (数値、ブール値、カテゴリ、文字列、日付など) であることを確認します。  

|例外メッセージ|
|------------------------|
|Cannot create column with mixed element types. (混合要素型の列は作成できません。)|
|Cannot create column with id "{column_id}" of mixed element types: (混合要素型の ID "{column_id}" の列を作成できません:)<br />Type of data[{row_1}, {column_id}] is "{type_1}". (data[{row_1}、{column_id}] の型は "{type_1}" です。) <br />Type of data[{row_2}, {column_id}] is "{type_2}". (data[{row_2}、{column_id}] の型は "{type_2}" です。)|
|Cannot create column with id "{column_id}" of mixed element types: (混合要素型の ID "{column_id}" の列を作成できません:)<br />Type in chunk {chunk_id_1} is "{type_1}". (チャンク {chunk_id_1} の型は "{type_1}" です。) <br />Type in chunk {chunk_id_2} is "{type_2}" with chunk size: {chunk_size}. (chunk {chunk_id_2} の型は chunk size: {chunk_size} の "{type_2}" です。)|


## <a name="error-0046"></a>エラー 0046  
 指定されたパスにディレクトリを作成できないと、例外が発生します。  

 Azure Machine Learning では、指定されたパスにディレクトリを作成できない場合、このエラーが発生します。 Hive クエリの出力ディレクトリへのパスのいずれかの部分が正しくない、またはアクセスできない場合、このエラーが発生します。  

**解決策:** モジュールを見直し、ディレクトリ パスの形式が正しいこと、および現在の資格情報でアクセス可能であることを検証します。  

|例外メッセージ|
|------------------------|
|Please specify a valid output directory. (有効な出力ディレクトリを指定してください。)|
|Directory: {path} cannot be created. (ディレクトリ: {path} を作成できません。) Please specify valid path. (有効なパスを指定してください。)|


## <a name="error-0047"></a>エラー 0047  
 モジュールに渡された一部のデータセットの特徴列の数が少なすぎる場合、例外が発生します。  

 Azure Machine Learning では、トレーニングへの入力データセットにアルゴリズムで必要とされる最少数の列が含まれない場合、このエラーが発生します。 通常、データセットが空か、トレーニング列のみが含まれています。  

**解決策:** 入力データセットを見直し、ラベル列とは別に 1 つ以上の列があることを確認します。  

|例外メッセージ|
|------------------------|
|Number of feature columns in input dataset is less than allowed minimum. (入力データセット内の特徴列の数が許容される最少数未満です。)|
|Number of feature columns in input dataset is less than allowed minimum of {required_columns_count} column(s). (入力データセット内の特徴列の数が、許容される最小数の {required_columns_count} 列未満です。)|
|Number of feature columns in input dataset "{arg_name}" is less than allowed minimum of {required_columns_count} column(s). (入力データセット "{arg_name}" 内の特徴列の数が、許容される最小数の {required_columns_count} 列未満です。)|


## <a name="error-0048"></a>エラー 0048  
 ファイルを開くことができない場合、例外が発生します。  

 Azure Machine Learning では、読み取りまたは書き込み用のファイルを開くことができない場合、このエラーが発生します。 このエラーは、次の理由で発生する可能性があります。  

-   コンテナーまたはファイル (BLOB) が存在しない  
  
-   ファイルまたはコンテナーのアクセス レベルで、ファイルへのアクセスが許可されていない  
  
-   ファイルが大きすぎて読み取ることができないか、または形式が正しくない  

**解決策:** モジュールおよび読み取ろうとしているファイルを見直します。  

 コンテナーとファイルの名前が正しいことを確認します。  

 クラシック Azure portal または Azure ストレージ ツールを使って、ファイルにアクセスするためのアクセス許可があることを確認します。  

  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->

|例外メッセージ|
|------------------------|
|Unable to open a file. (ファイルを開くことができません。)|
|Error while opening the file: {file_name}. (ファイルを開くときにエラーが発生しました: {file_name}。)|
|Error while opening the file: {file_name}. (ファイルを開くときにエラーが発生しました: {file_name}。) Storage exception message: {exception}. (ストレージの例外メッセージ: {exception}。)|


## <a name="error-0049"></a>エラー 0049  
 ファイルを解析できない場合、例外が発生します。  

 Azure Machine Learning では、ファイルを解析することができない場合、このエラーが発生します。 [データのインポート](import-data.md) モジュールで選択されているファイル形式がファイルの実際の形式と一致しない場合、またはファイルに認識できない文字が含まれている場合、このエラーが発生します。  

**解決策:** モジュールを見直し、ファイル形式の選択がファイルの形式と一致しない場合は修正します。 可能であれば、ファイルを調べて、無効な文字が含まれていないことを確認します。  

|例外メッセージ|
|------------------------|
|Unable to parse a file. (ファイルを解析できません。)|
|Error while parsing the {file_format} file. ({file_format} ファイルの解析中にエラーが発生しました。)|
|Error while parsing the {file_format} file: {file_name}. ({file_format} ファイルの解析中にエラーが発生しました: {file_name}。)|
|Error while parsing the {file_format} file. ({file_format} ファイルの解析中にエラーが発生しました。) Reason: {failure_reason}. (理由: {failure_reason}。)|
|Error while parsing the {file_format} file: {file_name}. ({file_format} ファイルの解析中にエラーが発生しました: {file_name}。) Reason: {failure_reason}. (理由: {failure_reason}。)|


## <a name="error-0052"></a>エラー 0052  
 正しくない Azure ストレージ アカウント キーが指定された場合、例外が発生します。  

 Azure Machine Learning では、Azure ストレージ アカウントへのアクセスに使用するキーが正しくない場合、このエラーが発生します。 たとえば、コピーして貼り付けるときに Azure ストレージ キーが切り捨てられた場合、または間違ったキーを使用した場合、このエラーが表示される可能性があります。  

 Azure ストレージ アカウントのキーの取得方法について詳しくは、[ストレージ アクセス キーの表示、コピー、再生成](../../storage/common/storage-account-create.md)に関する記事をご覧ください。  

**解決策:** モジュールを見直し、Azure ストレージ キーがアカウントに対して正しいことを確認します。必要な場合は、クラシック Azure portal からキーをもう一度コピーします。  

|例外メッセージ|
|------------------------|
|The Azure storage account key is incorrect. (Azure ストレージ アカウント キーが正しくありません。)|


## <a name="error-0053"></a>エラー 0053  
 マッチボックスの推奨に対するユーザーの特徴や項目がない場合、例外が発生します。  

 Azure Machine Learning では、特徴ベクターが見つからないと、このエラーが生成されます。  

**解決策:** 入力データセットに特徴ベクターが存在することを確認します。  

|例外メッセージ|
|------------------------|
|User features or/and items are required but not provided. (ユーザーの特徴や項目が必要ですが提供されていません。)|


## <a name="error-0056"></a>エラー 0056  
 操作に対して選択した列が要件に違反している場合、例外が発生します。  

 Azure Machine Learning では、特定のデータ型の列が必要な操作に対して列を選択する場合、このエラーが発生します。 

 このエラーは、列のデータ型が正しくても、列が特徴列、ラベル列、またはカテゴリ列としてもマークされる必要のあるモジュールを使用している場合に発生する可能性があります。  

  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->

**解決策:**

1.  現在選択されている列のデータ型を見直します。 

2. 選択した列がカテゴリ列、ラベル列、または特徴列かどうかを確認します。  
  
3.  列を選択したモジュールのヘルプ トピックを調べて、データ型または列の使用方法について特定の要件があるかどうかを確認します。  
  
3.  [メタデータの編集](edit-metadata.md)を使って、この操作を行っている間、列の型を変更します。 ダウンストリームの操作で列が必要な場合は、[メタデータの編集](edit-metadata.md)の別のインスタンスを使って、忘れずに列の型を元の値に戻します。  

|例外メッセージ|
|------------------------|
|One or more selected columns were not in an allowed category. (選択した列の 1 つ以上が、許可されたカテゴリに含まれませんでした。)|
|Column with name "{col_name}" is not in an allowed category. ("{col_name}" という名前の列は、許可されたカテゴリに含まれていません。)|


## <a name="error-0057"></a>エラー 0057  
 既に存在しているファイルまたは BLOB を作成しようとすると、例外が発生します。  

 [データのエクスポート](export-data.md) モジュールまたは他のモジュールを使って Azure Machine Learning でのパイプラインの結果を Azure Blob Storage に保存するときに、既に存在しているファイルまたは BLOB を作成しようとすると、この例外が発生します。   

**解決策:**

 以前に **[Azure blob storage write mode]\(Azure Blob Storage 書き込みモード\)** プロパティを **[エラー]** に設定した場合にのみ、このエラーを受け取ります。 仕様により、このモジュールでは既に存在する BLOB にデータセットを書き込もうとするとエラーが発生します。

 - モジュールのプロパティを開き、 **[Azure blob storage write mode]\(Azure Blob Storage 書き込みモード\)** プロパティを **[上書き]** に変更します。
 - または、書き込み先の BLOB またはファイルとして別の名前を入力し、必ずまだ存在していない BLOB を指定します。  

|例外メッセージ|
|------------------------|
|File or Blob already exists. (ファイルまたは BLOB は既に存在しています。)|
|File or Blob "{file_path}" already exists. (ファイルまたは BLOB "{file_path}" は既に存在しています。)|


## <a name="error-0058"></a>エラー 0058  
 Azure Machine Learning では、予想されるラベル列がデータセットに含まれない場合、このエラーが発生します。  

 この例外は、指定されたラベル列が、学習器で予想されているデータやデータ型と一致しない場合、または正しくない値を含む場合に発生する可能性もあります。 たとえば、バイナリ分類器をトレーニングするときに、実数値のラベル列を使うと、この例外が生成されます。  

**解決策:** 解決策は、使用している学習器やトレーナー、およびデータセット内の列のデータ型によって異なります。 最初に、機械学習アルゴリズムまたはトレーニング モジュールの要件を確認します。  

 入力データセットを見直します。 ラベルとして扱う予定の列に、作成しているモデルに対して正しいデータ型が含まれることを確認します。  

 欠損値がないか入力を確認し、必要に応じてそれらを除去または置換します。  

 必要な場合は、[メタデータの編集](edit-metadata.md)モジュールを追加し、ラベル列をラベルとして確実にマークします。  

|例外メッセージ|
|------------------------|
|The label column values and scored label column values are not comparable. (ラベル列の値とスコアリングされたラベル列の値は比較できません。)|
|The label column is not as expected in "{dataset_name}". (ラベル列が "{dataset_name}" で予期されているものではありません。)|
|The label column is not as expected in "{dataset_name}", {reason}. (ラベル列が "{dataset_name}" で予期されているものではありません。{reason}。)|
|The label column "{column_name}" is not expected in "{dataset_name}". (ラベル列 "{column_name}" は "{dataset_name}" で予期されていません。)|
|The label column "{column_name}" is not expected in "{dataset_name}", {reason}. (ラベル列 "{column_name}" は "{dataset_name}" で予期されていません。{reason}。)|


## <a name="error-0059"></a>エラー 0059  
 列ピッカーで指定されている列インデックスを解析できない場合、例外が発生します。  

 Azure Machine Learning では、列セレクターを使って指定された列インデックスを解析できない場合、このエラーが発生します。  列インデックスが解析できない無効な形式の場合、このエラーを受け取ります。  

**解決策:** 有効なインデックス値を使うように、列インデックスを修正します。  

|例外メッセージ|
|------------------------|
|One or more specified column indexes or index ranges could not be parsed. (指定された 1 つまたは複数の列インデックスまたはインデックス範囲を解析できませんでした。)|
|Column index or range "{column_index_or_range}" could not be parsed. (列のインデックスまたは範囲 "{column_index_or_range}" を解析できませんでした。)|


## <a name="error-0060"></a>エラー 0060  
 列ピッカーで範囲外の列範囲を指定すると、例外が発生します。  

 Azure Machine Learning では、列セレクターで範囲外の列範囲を指定する場合、このエラーが発生します。 列ピッカーの列範囲がデータセット内の列に対応していない場合、このエラーを受け取ります。  

**解決策:** データセット内の列に対応するように、列ピッカーで列範囲を修正します。  

|例外メッセージ|
|------------------------|
|Invalid or out of range column index range specified. (無効または範囲外の列インデックス範囲が指定されました。)|
|Column range "{column_range}" is invalid or out of range. (列範囲 "{column_range}" が無効または範囲外です。)|


## <a name="error-0061"></a>エラー 0061  
 列の数がテーブルとは異なる DataTable に行を追加しようとすると、例外が発生します。  

 Azure Machine Learning では、あるデータセットとは列の数が異なるデータセットに行を追加しようとする場合、このエラーが発生します。  データセットに追加されている行が、入力データセットとは異なる列数の場合、このエラーを受け取ります。  列の数が異なる場合、データセットに行を追加できません。  

**解決策:** 追加される行と同じ列数になるように入力データセットを修正するか、またはデータセットと同じ列数になるように追加される行を修正します。  

|例外メッセージ|
|------------------------|
|All tables must have the same number of columns. (すべてのテーブルは同じ列数である必要があります。)|
|Columns in chunk "{chunk_id_1}" is different with chunk "{chunk_id_2}" with chunk size: {chunk_size}. (チャンク "{chunk_id_1}" の列は、チャンク サイズ {chunk_size} のチャンク "{chunk_id_2}" と異なります。)|
|Column count in file "{filename_1}" (count={column_count_1}) is different with file "{filename_2}" (count={column_count_2}). (ファイル "{filename_1}" (count={column_count_1}) の列数が、ファイル "{filename_2}" (count={column_count_2}) と異なります。)|


## <a name="error-0062"></a>エラー 0062  
 学習器の種類が異なる 2 つのモデルを比較しようとすると、例外が発生します。  

 Azure Machine Learning では、2 つの異なるスコアリング済みデータセットの評価メトリックを比較できない場合、このエラーが生成されます。 この場合、2 つのスコアリング済みデータセットの生成に使用されたモデルの有効性を比較できません。  

**解決策:** スコアリングの結果が、同じ種類の機械学習モデル (バイナリ分類、回帰、多クラス分類、推奨、クラスタリング、異常検出など) によって生成されていることを確認します。比較するモデルはすべて、学習器の種類が同じである必要があります。  

|例外メッセージ|
|------------------------|
|All models must have the same learner type. (すべてのモデルは学習器の種類が同じでなければなりません。)|
|Got incompatible learner type: "{actual_learner_type}". (互換性のない学習器の種類 "{actual_learner_type}" を取得しました。) Expected learner types are: "{expected_learner_type_list}". (予期されている学習器の種類は "{expected_learner_type_list}" です。)|


## <a name="error-0064"></a>エラー 0064  
 正しくない Azure ストレージ アカウント名またはストレージ キーが指定された場合、例外が発生します。  

 Azure Machine Learning では、Azure ストレージ アカウントの名前またはストレージ キーが誤って指定された場合、このエラーが発生します。 ストレージ アカウントに対して正しくないアカウント名またはパスワードを入力すると、このエラーを受け取ります。 これは、アカウント名またはパスワードを手入力したときに発生する可能性があります。 アカウントが削除された場合にも発生する可能性があります。  

**解決策:** アカウント名とパスワードを正しく入力したこと、およびアカウントが存在することを確認します。  

|例外メッセージ|
|------------------------|
|The Azure storage account name or storage key is incorrect. (Azure ストレージ アカウント名またはストレージ キーが正しくありません。)|
|The Azure storage account name "{account_name}" or storage key for the account name is incorrect. (Azure ストレージ アカウント名 "{account_name}" またはアカウント名のストレージ キーが正しくありません。)|


## <a name="error-0065"></a>エラー 0065  
 正しくない Azure BLOB 名が指定された場合、例外が発生します。  

 Azure Machine Learning では、Azure BLOB 名が誤って指定された場合、このエラーが発生します。  次の場合、エラーを受け取ります。  

-   指定されたコンテナーで BLOB が見つからない。  

 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->

-   形式がエンコードされた Excel または CSV のときに、[データのインポート](import-data.md)要求でソースとしてコンテナーのみが指定された。コンテナー内のすべての BLOB の内容の連結が、これらの形式では許可されていない。  
  
-   SAS URI に、有効な BLOB の名前が含まれません。  

**解決策:** 例外がスローされているモジュールを見直します。 指定された BLOB がストレージ アカウントのコンテナーに存在していること、および BLOB を表示できるアクセス許可があることを確認します。 エンコード形式の Excel または CSV を使用している場合、入力が "**コンテナー名/ファイル名**" の形式であることを確認します。 SAS URI に有効な BLOB の名前が含まれていることを確認します。  

|例外メッセージ|
|------------------------|
|The Azure storage blob name is incorrect. (Azure Storage の BLOB 名が正しくありません。)|
|The Azure storage blob name "{blob_name}" is incorrect. (Azure Storage の BLOB 名 "{blob_name}" が正しくありません。)|
|The Azure storage blob name with prefix "{blob_name_prefix}" does not exist. (プレフィックス "{blob_name_prefix}" を持つ、Azure Storage の BLOB 名は存在しません。)|
|Failed to find any Azure storage blobs under container "{container_name}". (コンテナー "{container_name}" で、Azure Storage の BLOB を検出できませんでした。)|
|Failed to find any Azure storage blobs with wildcard path "{blob_wildcard_path}". (ワイルドカードパス "{blob_wildcard_path}" を持つ、Azure Storage の BLOB を検出できませんでした。)|


## <a name="error-0066"></a>エラー 0066  
 リソースを Azure BLOB にアップロードできない場合、例外が発生します。  

 Azure Machine Learning では、リソースを Azure BLOB にアップロードできなかった場合、このエラーが発生します。  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> どちらも、入力ファイルを含むアカウントと同じ Azure ストレージ アカウントに保存されます。  

**解決策:** モジュールを見直します。 Azure アカウント名、ストレージ キー、コンテナーが正しいこと、およびアカウントにコンテナーへの書き込みアクセス許可があることを確認します。  

|例外メッセージ|
|------------------------|
|The resource could not be uploaded to Azure storage. (リソースを Azure Storage にアップロードできませんでした。)|
|The file "{source_path}" could not be uploaded to Azure storage as "{dest_path}". (ファイル "{source_path}" を Azure Storage に "{dest_path}" としてアップロードできませんでした。)|


## <a name="error-0067"></a>エラー 0067  
 データセットの列数が予想とは異なる場合、例外が発生します。  

 Azure Machine Learning では、データセットの列数が予想とは異なる場合、このエラーが発生します。  データセット内の列の数が、実行中にモジュールで予想される列の数と異なる場合、このエラーを受け取ります。  

**解決策:** 入力データセットまたはパラメーターを修正します。  

|例外メッセージ|
|------------------------|
|Unexpected number of columns in the datatable. (データ テーブル内の列数が予想と異なります。)|
|Unexpected number of columns in the dataset "{dataset_name}". (データセット "{dataset_name}" に予期しない数の列があります。)|
|Expected "{expected_column_count}" column(s) but found "{actual_column_count}" column(s) instead. ("{expected_column_count}" 列が予期されていますが、代わりに "{actual_column_count}" 列が見つかりました。)|
|In input dataset "{dataset_name}", expected "{expected_column_count}" column(s) but found "{actual_column_count}" column(s) instead. (入力データセット "{dataset_name}" では、"{expected_column_count}" 列が予期されていますが、代わりに "{actual_column_count}" 列が見つかりました。)|


## <a name="error-0068"></a>エラー 0068  
 指定された Hive スクリプトが正しくない場合、例外が発生します。  

 Azure Machine Learning では、Hive QL スクリプトに構文エラーがある場合、またはクエリやスクリプトの実行中に Hive インタープリターでエラーが発生した場合、このエラーが発生します。  

**解決策:**

通常 Hive からのエラー メッセージがエラー ログで報告されるので、特定のエラーに基づいてアクションを実行できます。 

+ モジュールを開き、クエリで誤りを調べます。  
+ Hadoop クラスターの Hive コンソールにログインしてクエリを実行することにより、Azure Machine Learning の外部でクエリが正しく動作することを確認します。  
+ Hive スクリプトで実行可能なステートメントとコメントを単一の行に混在させるのではなく、コメントを別の行に配置することを試みます。  

### <a name="resources"></a>リソース

機械学習での Hive クエリの使用については、次の記事をご覧ください。

+ [Hive テーブルを作成して Azure Blob Storage からデータを読み込む](../team-data-science-process/move-hive-tables.md)
+ [Hive クエリを使用してテーブルのデータを探索する](../team-data-science-process/explore-data-hive-tables.md)
+ [Hive クエリを使用して Hadoop クラスターのデータの特徴を作成する](../team-data-science-process/create-features-hive.md)
+ [SQL ユーザー向け Hive のチート シート (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|例外メッセージ|
|------------------------|
|Hive script is incorrect. (Hive スクリプトが正しくありません。)|


## <a name="error-0069"></a>エラー 0069  
 指定された SQL スクリプトが正しくない場合、例外が発生します。  

 Azure Machine Learning では、指定された SQL スクリプトの構文に問題がある場合、またはスクリプトで指定された列やテーブルが有効ではない場合、このエラーが発生します。 

 クエリまたはスクリプトの実行中に SQL エンジンでエラーが発生した場合、このエラーを受け取ります。 通常 SQL のエラー メッセージがエラー ログで報告されるので、特定のエラーに基づいてアクションを実行できます。  

**解決策:** モジュールを見直し、SQL クエリで誤りを調べます。  

 データベース サーバーに直接ログインしてクエリを実行することにより、Azure ML の外部でクエリが正しく動作することを確認します。  

 モジュールの例外により報告された、SQL によって生成されたメッセージがある場合、報告されたエラーに基づいて対処を実行します。 たとえば、エラー メッセージには、発生する可能性の高いエラーに関する具体的なガイダンスが含まれる場合があります。
+ "*No such column or missing database*" (そのような列はないか、またはデータベースが存在しません) は、列名を誤って入力した可能性があることを示します。 列名が正しいことが確実な場合は、角かっこまたは引用符を使って列の識別子を囲んでみてください。
+ "*SQL logic error near \<SQL keyword\>* " ("\<SQL keyword\> の近くに SQL ロジック エラーがあります") は、指定されたキーワードの前に構文エラーが存在する可能性があることを示します

  
|例外メッセージ|
|------------------------|
|SQL script is incorrect. (SQL スクリプトが正しくありません。)|
|SQL query "{sql_query}" is not correct. (SQL クエリ "{sql_query}" が正しくありません。)|
|SQL query "{sql_query}" is not correct. (SQL クエリ "{sql_query}" が正しくありません。) Exception message: {exception}. (例外メッセージ: {exception}。)|


## <a name="error-0070"></a>エラー 0070  
 存在しない Azure テーブルにアクセスしようとすると、例外が発生します。  

 Azure Machine Learning では、存在しない Azure テーブルにアクセスしようとする場合、このエラーが発生します。 指定した Azure Storage 内のテーブルが、Azure Table Storage の読み取りまたは書き込み時に存在しない場合、このエラーを受け取ります。 これは、目的のテーブルの名前を誤って入力した場合、またはターゲットの名前とストレージの種類の間に不一致がある場合に発生する可能性があります。 たとえば、テーブルから読み取ろうとして、代わりに BLOB の名前を入力したような場合です。  

**解決策:** モジュールを見直して、テーブルの名前が正しいことを確認します。  

|例外メッセージ|
|------------------------|
|Azure table does not exist. (Azure のテーブルが存在しません。)|
|Azure table "{table_name}" does not exist. (Azure のテーブル "{table_name}" が存在しません。)|


## <a name="error-0072"></a>エラー 0072  
 接続がタイムアウトした場合、例外が発生します。  

 Azure Machine Learning では、接続がタイムアウトする場合、このエラーが発生します。現在データ ソースまたはターゲットとの接続に問題がある場合 (インターネット接続が遅い、など)、データセットが大きい場合、またはデータを読み取る SQL クエリで複雑な処理が実行されている場合、このエラーを受け取ります。  

**解決策:** 現在、Azure ストレージまたはインターネットへの接続が遅くなる問題が発生しているかどうかを特定します。  

|例外メッセージ|
|------------------------|
|Connection timeout occurred. (接続のタイムアウトが発生しました。)|


## <a name="error-0073"></a>エラー 0073  
 列を別の型に変換しているときにエラーが発生した場合、例外が発生します。  

 Azure Machine Learning では、列を別の型に変換できない場合、このエラーが発生します。  モジュールで特定の型が必要な場合に、列を新しい型に変換できないと、このエラーを受け取ります。  

**解決策:** 入力データセットを修正し、内部例外に基づいて列を変換できるようにします。  

|例外メッセージ|
|------------------------|
|Failed to convert column. (列を変換できませんでした。)|
|Failed to convert column to {target_type}. (列を {target_type} に変換できませんでした。)|


## <a name="error-0075"></a>エラー 0075  
データセットを量子化するときに無効なビン分割機能を使用すると、例外が発生します。  

Azure Machine Learning では、サポートされていない方法を使ってデータをビン分割しようとする場合、またはパラメーターの組み合わせが有効ではない場合、このエラーが発生します。  

**解決策:**

このイベントでのエラー処理は、ビン分割方法のカスタマイズがより広い範囲で可能だった以前のバージョンの Azure Machine Learning で導入されました。 現在は、ビン分割方法はすべてドロップダウン リストでの選択に基づくため、技術的には、このエラーが発生する可能性はありません。

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Microsoft Q&A question page for Azure Machine Learning](/answers/topics/azure-machine-learning-studio-classic.html), providing the data types, parameter settings, and the exact error message.  -->

|例外メッセージ|
|------------------------|
|Invalid binning function used. (無効なビン分割機能が使われました。)|


## <a name="error-0077"></a>エラー 0077  
 不明な BLOB ファイル書き込みモードが渡されると、例外が発生します。  

 Azure Machine Learning では、BLOB ファイルのターゲットまたはソースを指定するときに無効な引数が渡される場合、このエラーが発生します。  

**解決策:** Azure Blob Storage との間でデータをインポートまたはエクスポートするほとんどすべてのモジュールでは、書き込みモードを制御するパラメーターの値は、ドロップダウン リストを使って割り当てられます。そのため、無効な値を渡すことはできず、このエラーは発生しないはずです。 このエラーは、今後のリリースで非推奨になります。  

|例外メッセージ|
|------------------------|
|Unsupported blob write mode. (サポートされていない BLOB 書き込みモードです。)|
|Unsupported blob write mode: {blob_write_mode}. (サポートされていない BLOB 書き込みモードです: {blob_write_mode}。)|


## <a name="error-0078"></a>エラー 0078  
 [データのインポート](import-data.md)に対する HTTP オプションでリダイレクトを示す 3xx 状態コードを受け取ると、例外が発生します。  

 Azure Machine Learning では、[データのインポート](import-data.md)に対する HTTP オプションでリダイレクトを示す 3xx (301、302、304 など) 状態コードを受け取る場合、このエラーが発生します。 ブラウザーを別のページにリダイレクトする HTTP ソースに接続しようとした場合、このエラーを受け取ります。 セキュリティ上の理由から、リダイレクトを行う Web サイトは、Azure Machine Learning のためのデータ ソースとしては許可されません。  

**解決策:** Web サイトが信頼できる Web サイトの場合は、リダイレクト先の URL を直接入力します。  

|例外メッセージ|
|------------------------|
|Http redirection not allowed. (HTTP のリダイレクトは許可されません。)|


## <a name="error-0079"></a>エラー 0079  
 正しくない Azure ストレージ コンテナー名が指定されると、例外が発生します。  

 Azure Machine Learning では、Azure ストレージ コンテナー名が誤って指定される場合、このエラーが発生します。 Azure Blob Storage に書き込むときに、 **[Path to blob beginning with container]\(コンテナーで始まる BLOB へのパス\)** オプションを使用して、コンテナーと BLOB (ファイル) 名の両方を指定していない場合、このエラーを受け取ります。  

**解決策:** [データのエクスポート](export-data.md) モジュールを見直し、BLOB へのパスの指定に、コンテナーとファイル名の両方が **コンテナー/ファイル名** の形式で含まれていることを確認します。  

|例外メッセージ|
|------------------------|
|The Azure storage container name is incorrect. (Azure ストレージ コンテナーの名前が正しくありません。)|
|The Azure storage container name "{container_name}" is incorrect; a container name of the format container/blob was expected. (Azure ストレージ コンテナー名 "{container_name}" が正しくありません。コンテナー名は、コンテナー/BLOB 形式であると予想されていました。)|


## <a name="error-0080"></a>エラー 0080  
 すべての値が欠損している列がモジュールによって許可されない場合、例外が発生します。  

 Azure Machine Learning では、モジュールによって使用される 1 つまたは複数の列に欠損値しか含まれていないと、このエラーが生成されます。 たとえば、各列の集計統計が計算されるモジュールの場合、データが含まれない列での動作は不可能です。 そのような場合、モジュールの実行はこの例外で停止します。  

**解決策:** 入力データセットを見直し、欠損値しか含まれていない列を削除します。  

|例外メッセージ|
|------------------------|
|Columns with all values missing are not allowed. (すべて欠損値の列は許可されません。)|
|Column {col_index_or_name} has all values missing. (列 {col_index_or_name} では、すべての値が欠落しています。)|


## <a name="error-0081"></a>エラー 0081  
 削減後のディメンションの数が、少なくとも 1 つのスパース特徴列を含む入力データセットの特徴列の数と等しい場合、PCA モジュールで例外が発生します。  

 Azure Machine Learning では、次の条件が満たされる場合、このエラーが生成されます。(a) 入力データセットに少なくとも 1 つのスパース列があり、かつ、(b) 要求されたディメンションの最終的な数が入力ディメンションの数と同じである。  

**解決策:** 出力のディメンションの数を、入力のディメンションの数より少なくなるように減らすことを検討します。 これは、PCA のアプリケーションでは一般的です。   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->

|例外メッセージ|
|------------------------|
|For dataset containing sparse feature columns number of dimensions to reduce to should be less than number of feature columns. (スパース特徴列を含むデータセットでは、削減後のディメンションの数を特徴列の数より少なくする必要があります。)|


## <a name="error-0082"></a>エラー 0082  
 モデルを正常に逆シリアル化できないと、例外が発生します。  

 Azure Machine Learning では、破壊的変更の結果として、保存されている機械学習モデルまたは変換を新しいバージョンの Azure Machine Learning ランタイムで読み込むことができない場合、このエラーが発生します。  

**解決策:** モデルまたは変換を生成したトレーニング パイプラインを再度実行して、モデルまたは変換を保存し直す必要があります。  

|例外メッセージ|
|------------------------|
|Model could not be deserialized because it is likely serialized with an older serialization format. (古いシリアル化形式でシリアル化された可能性があるため、モデルを逆シリアル化できませんでした。) Please retrain and resave the model. (モデルを再トレーニングして、再度保存してください。)|


## <a name="error-0083"></a>エラー 0083  
 トレーニングに使用されたデータセットを学習器の具象型に使用できない場合、例外が発生します。  

 Azure Machine Learning では、データセットとトレーニング中の学習器の間に互換性がないと、このエラーが生成されます。 たとえば、データセットの各行に少なくとも 1 つの欠損値が含まれ、結果として、トレーニング中にデータセット全体がスキップされる可能性があります。 または、異常検出などの一部の機械学習アルゴリズムでは、ラベルの存在が予想されておらず、データセットにラベルが存在すると、この例外がスローされることがあります。  

**解決策:** 入力データセットの要件を確認するために使用されている学習器のドキュメントを参照します。 列を調べて、必要な列がすべて存在することを確認します。  

|例外メッセージ|
|------------------------|
|Dataset used for training is invalid. (トレーニングに使用されているデータセットが無効です。)|
|{data_name} contains invalid data for training. ({data_name} には、トレーニング用の無効なデータが含まれます。)|
|{data_name} contains invalid data for training. ({data_name} には、トレーニング用の無効なデータが含まれます。) Learner type: {learner_type}. (学習器の種類: {learner_type}。)|
|{data_name} contains invalid data for training. ({data_name} には、トレーニング用の無効なデータが含まれます。) Learner type: {learner_type}. (学習器の種類: {learner_type}。) Reason: {reason}. (理由: {reason}。)|
|Failed to apply "{action_name}" action on training data {data_name}. ("{action_name}" アクションをトレーニング データ {data_name} に適用できませんでした。) Reason: {reason}. (理由: {reason}。)|


## <a name="error-0084"></a>エラー 0084  
 R スクリプトから生成されたスコアが評価されると、例外が発生します。 現在、これはサポートされていません。  

 Azure Machine Learning では、スコアが含まれる R スクリプトからの出力でモデルを評価するためにモジュールのいずれかを使用しようとする場合、このエラーが発生します。  

**解決策:**

|例外メッセージ|
|------------------------|
|Evaluating scores produced by Custom Model is currently unsupported. (カスタム モデルによって生成されたスコアの評価は、現在サポートされていません。)|


## <a name="error-0085"></a>エラー 0085  
 スクリプトの評価がエラーを伴い失敗すると、例外が発生します。  

 Azure Machine Learning では、構文エラーが含まれるカスタム スクリプトを実行する場合、このエラーが発生します。  

**解決策:** 外部エディターでコードを確認し、エラーを調べます。  

|例外メッセージ|
|------------------------|
|Error during evaluation of script. (スクリプトの評価中にエラーが発生しました。)|
|The following error occurred during script evaluation, please view the output log for more information: (スクリプトの評価中に次のエラーが発生しました。詳細については、出力ログを参照してください:)<br />---------- Start of error message from {script_language} interpreter ---------- (---------- {Script_language} インタープリターからのエラー メッセージの開始 ----------)<br />{message}<br />---------- End of error message from {script_language}  interpreter  ---------- (---------- {Script_language} インタープリターからのエラー メッセージの終わり ----------)|


## <a name="error-0090"></a>エラー 0090  
 Hive テーブルの作成が失敗すると、例外が発生します。  

 Azure Machine Learning では、[データのエクスポート](export-data.md)または別のオプションを使用して HDInsight クラスターにデータを保存するときに指定された Hive テーブルを作成できない場合、このエラーが発生します。  

**解決策:** クラスターに関連付けられている Azure ストレージ アカウント名を調べて、モジュールのプロパティで同じアカウントを使用していることを確認します。  

|例外メッセージ|
|------------------------|
|The Hive table could not be created. (Hive テーブルを作成できませんでした。) For a HDInsight cluster, ensure the Azure storage account name associated with cluster is the same as what is passed in through the module parameter. (HDInsight クラスターでは、クラスターに関連付けられた Azure ストレージ アカウント名が、モジュールのパラメーターで渡されたものと同じであることを確認してください。)|
|The Hive table "{table_name}" could not be created. (Hive テーブル "{table_name}" を作成できませんでした。) For a HDInsight cluster, ensure the Azure storage account name associated with cluster is the same as what is passed in through the module parameter. (HDInsight クラスターでは、クラスターに関連付けられた Azure ストレージ アカウント名が、モジュールのパラメーターで渡されたものと同じであることを確認してください。)|
|The Hive table "{table_name}" could not be created. (Hive テーブル "{table_name}" を作成できませんでした。) For a HDInsight cluster, please ensure the Azure storage account name associated with cluster is "{cluster_name}". (HDInsight クラスターでは、クラスターに関連付けられた Azure ストレージ アカウント名を確実に "{cluster_name}" としてください。)|


## <a name="error-0102"></a>エラー 0102  
 ZIP ファイルを抽出できない場合、スローされます。  

 Azure Machine Learning では、.zip 拡張子の zip パッケージをインポートするとき、パッケージが zip ファイルではない場合、またはサポートされている zip 形式がファイルで使用されていない場合、このエラーが発生します。  

**解決策:** 選択したファイルが有効な .zip ファイルであること、およびサポートされている圧縮アルゴリズムのいずれかを使って圧縮されていることを確認します。  

 圧縮形式のデータセットをインポートするときにこのエラーが発生する場合は、含まれるすべてのファイルが、サポートされているファイル形式のいずれかが使われているものであり、かつ Unicode 形式であることを確認します。  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->

 目的のファイルを圧縮された新しい zip フォルダーに読み取り、もう一度カスタム モジュールを追加してみてください。  

|例外メッセージ|
|------------------------|
|Given ZIP file is not in the correct format. (指定された ZIP ファイルは正しい形式ではありません。)|


## <a name="error-0105"></a>エラー 0105  
 モジュール定義ファイルにサポートされていないパラメーターの型が含まれる場合、このエラーが表示されます。  
  
 Azure Machine Learning では、カスタム モジュールの xml 定義を作成するときに、定義のパラメーターまたは引数の型とサポートされている型とが一致しない場合、このエラーが生成されます。  
  
**解決策:** カスタム モジュールの xml 定義ファイル内の **Arg** 要素の型プロパティが、サポートされている型であることを確認します。  
  
|例外メッセージ|  
|------------------------|  
|Unsupported parameter type. (パラメーターの型がサポートされていません。)|  
|Unsupported parameter type '{0}' specified. (サポートされていないパラメーターの型 '\{0\}' が指定されています。)|  


## <a name="error-0107"></a>エラー 0107  
 モジュール定義ファイルで、サポートされていない出力の型が定義されている場合、スローされます。  
  
 Azure Machine Learning では、カスタム モジュールの xml 定義の出力ポートの型がサポートされている型と一致しない場合、このエラーが生成されます。  
  
**解決策:** カスタム モジュールの xml 定義ファイル内の Output 要素の type プロパティが、サポートされている型であることを確認します。  
  
|例外メッセージ|  
|------------------------|  
|Unsupported output type. (出力の型がサポートされていません。)|  
|Unsupported output type '{output_type}' specified. (サポートされていない出力の型 '{output_type}' が指定されています。)|  


## <a name="error-0125"></a>エラー 0125  
 複数のデータセットのスキーマが一致しない場合、スローされます。  

**解決策:**

|例外メッセージ|
|------------------------|
|Dataset schema does not match. (データセットのスキーマが一致しません。)|


## <a name="error-0127"></a>エラー 0127  
 画像のピクセル サイズが許容される上限を超えています  

 このエラーは、分類のために画像データセットから画像を読み取っていて、画像が大きすぎるためにモデルで処理できない場合に発生します。  

 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  

-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->

|例外メッセージ|
|------------------------|
|Image pixel size exceeds allowed limit. (画像のピクセル サイズが許容される上限を超えています。)|
|Image pixel size in the file '{file_path}' exceeds allowed limit: '{size_limit}'. (ファイル '{file_path}' 内の画像のピクセル サイズが、許容される上限を超えています: '{size_limit}'。)|


## <a name="error-0128"></a>エラー 0128  
 カテゴリ列の条件付き確率の値が制限を超えています。  

**解決策:**

|例外メッセージ|
|------------------------|
|カテゴリ列の条件付き確率の値が制限を超えています。|
|カテゴリ列の条件付き確率の値が制限を超えています。 Columns '{column_name_or_index_1}' and '{column_name_or_index_2}' are the problematic pair. (列 '{column_name_or_index_1}' および '{column_name_or_index_2}' が問題のあるペアです。)|


## <a name="error-0129"></a>エラー 0129  
 データセット内の列の数が、許可される上限を超えています。  

**解決策:**

|例外メッセージ|
|------------------------|
|データセット内の列の数が、許可される上限を超えています。|
|Number of columns in the dataset in '{dataset_name}' exceeds allowed. ('{dataset_name}' のデータセット内の列数が許容値を超えています。)|
|Number of columns in the dataset in '{dataset_name}' exceeds allowed limit of '{component_name}'. ('{dataset_name}' のデータセット内の列数が、'{component_name}' の許容される上限を超えています。)|
|Number of columns in the dataset in '{dataset_name}' exceeds allowed '{limit_columns_count}' limit of '{component_name}'. ('{dataset_name}' のデータセット内の列数が、'{component_name}' の許容される上限 '{limit_columns_count}' を超えています。)|


## <a name="error-0134"></a>エラー 0134
Exception occurs when label column is missing or has insufficient number of labeled rows. (ラベル列が存在しない場合、またはラベルの付いた行の数が不足している場合、例外が発生します。)  

モジュールでラベル列が必要であるのに列選択に含めない場合、またはラベル列の欠損値が多すぎる場合、このエラーが発生します。

前の操作によってデータセットが変更され、ダウンストリーム操作で使用できる行が不足している場合にも、このエラーが発生する可能性があります。 たとえば、**パーティションとサンプル** モジュールで、式を使って値によるデータセットの分割を行っているとします。 使用した式で一致するものが見つからない場合、パーティションによって得たデータセットの 1 つが空になります。

解決策: 

 列の選択にラベル列を含めても認識されない場合は、[メタデータの編集](edit-metadata.md)モジュールを使って、それをラベル列としてマークします。

  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->
  その後、[見つからないデータのクリーンアップ](clean-missing-data.md) モジュールを使って、ラベル列に欠損値のある行を削除できます。 

 入力データセットを調べて、有効なデータが含まれること、および操作の要件を満たすために十分な行があることを確認します。 多くのアルゴリズムでは、最低限の行数のデータが必要とされていながら、データには数行またはヘッダーしか含まれない場合、エラー メッセージが生成されます。

|例外メッセージ|
|------------------------|
|Exception occurs when label column is missing or has insufficient number of labeled rows. (ラベル列が存在しない場合、またはラベルの付いた行の数が不足している場合、例外が発生します。)|
|Exception occurs when label column is missing or has less than {required_rows_count} labeled rows. (ラベル列がないか、ラベルの付いた行 {required_rows_count} より少ない場合、例外が発生します。)|
|Exception occurs when label column in dataset {dataset_name} is missing or has less than {required_rows_count} labeled rows. (データセット {dataset_name} 内にラベル列がないか、ラベルの付いた行 {required_rows_count} より少ない場合、例外が発生します。)|


## <a name="error-0138"></a>エラー 0138  
 Memory has been exhausted, unable to complete running of module. (メモリが不足しており、モジュールの実行を完了できません。) データセットのダウンサンプリングは、問題の軽減に役立つことがあります。  

 実行中のモジュールに、Azure コンテナーで使用できるより多くのメモリが必要な場合、このエラーが発生します。 大型のデータセットを使用しており、現在の操作がメモリに収まらない場合に、これが発生することがあります。  

**解決策:** 大型のデータセットを読み取ろうとして、操作を完了できない場合は、データセットのダウンサンプリングが役立つことがあります。  

  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  

 Sometimes transient loads can lead to this error. Machine support also changes over time. 

 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->

|例外メッセージ|
|------------------------|
|Memory has been exhausted, unable to complete running of module. (メモリが不足しており、モジュールの実行を完了できません。)|
|Memory has been exhausted, unable to complete running of module. (メモリが不足しており、モジュールの実行を完了できません。) Details: {details} (詳細: {details})|


## <a name="error-0141"></a>エラー 0141  
 選択された数値列の数およびカテゴリ列と文字列列の一意の値の数が少なすぎる場合、例外が発生します。  

 Azure Machine Learning では、選択された列に操作を実行するのに十分な一意の値がない場合、このエラーが発生します。  

**解決策:** 一部の操作では、特徴列とカテゴリ列で統計的操作が実行され、十分な値がない場合は、操作が失敗するか無効な結果が返される可能性があります。 データセットを調べて、特徴列とラベル列にある値の数を確認し、実行しようとしている操作が統計的に有効かどうかを判断します。  

 ソース データセットが有効な場合は、アップストリームのデータ操作やメタデータ操作によってデータが変更されたり値が削除されたりしたかどうかもチェックします。  

 アップストリームの操作に分割、サンプリング、または再サンプリングが含まれる場合、予想される数の行と値が出力に含まれていることを確認します。  

|例外メッセージ|
|------------------------|
|The number of the selected numerical columns and unique values in the categorical and string columns is too small. (選択された数値列の数およびカテゴリ列と文字列列の一意の値の数が少なすぎます。)|
|The total number of the selected numerical columns and unique values in the categorical and string columns (currently {actual_num}) should be at least {lower_boundary}. (選択された数値列およびカテゴリ列と文字列列の一意の値の総数 (現在 {actual_num}) は、{lower_boundary} 以上である必要があります。)|


## <a name="error-0154"></a>エラー 0154  
 ユーザーが、列の型に互換性がないキー列でデータを結合しようとすると、例外が発生します。

|例外メッセージ|
|------------------------|
|Key column element types are not compatible. (キー列の要素型に互換性がありません。)|
|Key column element types are not compatible.(left: {keys_left}; right: {keys_right}) (キー列の要素型に互換性がありません。(左: {keys_left}、右: {keys_right}))|


## <a name="error-0155"></a>エラー 0155  
 Exception occurs when column names of dataset are not string. (データセットの列名が文字列でない場合、例外が発生します。)

|例外メッセージ|
|------------------------|
|データフレーム列名は文字列型である必要があります。 Column names are not string. (列名が文字列ではありません。)|
|データフレーム列名は文字列型である必要があります。 Column names {column_names} are not string. (列名 {column_names} は文字列ではありません。)|


## <a name="error-0156"></a>エラー 0156  
 Exception occurs when failed to read data from Azure SQL Database. (Azure SQL Database からデータを読み取ることができなかった場合、例外が発生します。)

|例外メッセージ|
|------------------------|
|Failed to read data from Azure SQL Database. (Azure SQL Database からデータを読み取れませんでした。)|
|Failed to read data from Azure SQL Database: {detailed_message} DB: {database_server_name}:{database_name} Query: {sql_statement} (Azure SQL Database からデータを読み取れませんでした: {detailed_message} DB: {database_server_name}: {database_name} クエリ: {sql_statement})|


## <a name="error-0157"></a>エラー 0157  
 Datastore not found. (データストアが見つかりません。)

|例外メッセージ|
|------------------------|
|Datastore information is invalid. (データストア情報が無効です。)|
|Datastore information is invalid. (データストア情報が無効です。) Failed to get AzureML datastore '{datastore_name}' in workspace '{workspace_name}'. (ワークスペース '{workspace_name}' 内の AzureML データストア '{datastore_name}' を取得できませんでした。)|


## <a name="error-0158"></a>エラー 0158
 変換ディレクトリが無効な場合にスローされます。

|例外メッセージ|
|------------------------------------------------------------|
|Given TransformationDirectory is invalid. (指定された TransformationDirectory は無効です。)|
|TransformationDirectory "{arg_name}" is invalid. (TransformationDirectory "{arg_name}" は無効です。) Reason: {reason}. (理由: {reason}。) Please rerun training experiment which generates the Transform file. (変換ファイルを生成するトレーニング実験を再実行してください。) If training experiment was deleted, please recreate and save the Transform file. (トレーニング実験が削除された場合は、変換ファイルを再作成して保存してください。)|
|TransformationDirectory "{arg_name}" is invalid. (TransformationDirectory "{arg_name}" は無効です。) Reason: {reason}. (理由: {reason}。) {troubleshoot_hint}|


## <a name="error-0159"></a>エラー 0159
 モジュール モデル ディレクトリが無効な場合に、例外が発生します。 

|例外メッセージ|
|------------------------------------------------------------|
|Given ModelDirectory is invalid. (指定された ModelDirectory が無効です。)|
|ModelDirectory "{arg_name}" is invalid. (ModelDirectory "{arg_name}" が無効です。)|
|ModelDirectory "{arg_name}" is invalid. (ModelDirectory "{arg_name}" が無効です。) Reason: {reason}. (理由: {reason}。)|
|ModelDirectory "{arg_name}" is invalid. (ModelDirectory "{arg_name}" が無効です。) Reason: {reason}. (理由: {reason}。) {troubleshoot_hint}|


## <a name="error-1000"></a>エラー 1000  
内部ライブラリの例外。  

このエラーは、他の方法では処理されない内部エンジン エラーをキャプチャするために提供されます。 そのため、このエラーの原因は、エラーが生成されたモジュールによって異なる可能性があります。  

詳細なヘルプを受けるには、エラーに付随する詳細なメッセージに、入力として使用したデータなどのシナリオの説明を添えて、[Azure Machine Learning](/answers/topics/azure-machine-learning.html) フォーラムに投稿することをお勧めします。 このフィードバックは、エラーの優先順位付けや、さらに取り組むべき最重要問題の特定に役立てられます。  

|例外メッセージ|
|------------------------|
|Library exception. (ライブラリ例外。)|
|Library exception: {exception}. (ライブラリの例外: {exception}。)|
|Unknown library exception: {exception}. (不明なライブラリの例外: {exception}。) {customer_support_guidance}。|


## <a name="execute-python-script-module"></a>Execute Python Script モジュール

**Execute Python Script モジュール** の **70_driver_logs** で **azureml_main** を検索すると、エラーが発生した行を見つけることができます。 たとえば、"azureml_main の File "/tmp/tmp01_ID/user_script.py", line 17" は、Python スクリプトの 17 行目でエラーが発生したことを示します。
