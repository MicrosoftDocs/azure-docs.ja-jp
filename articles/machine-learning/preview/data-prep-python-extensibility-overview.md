---
title: "Azure Machine Learning Data Preparation での Python 拡張機能の使用 | Microsoft Docs"
description: "このドキュメントでは、Python コードを使用して Data Prep の機能を拡張する方法の概要と、いくつかの詳細な例を示します"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: 4e1935a7830b8174796ac12792fbbc0ed110d081
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="data-prep-python-extensions"></a>Data Prep Python 拡張機能
組み込み機能間の機能性ギャップを埋める方法として、Data Prep には複数のレベルの拡張機能が含まれています。 このドキュメントでは、Python スクリプトを介して拡張機能を説明します。 

## <a name="custom-code-steps"></a>カスタム コード ステップ 
Data Prep には、ユーザーがコードを記述できる次のカスタム ステップがあります。 
1. ファイル リーダー*
2. ライター*
3. 列の追加
4. 高度なフィルター
5. データ フローの変換
6. パーティションの変換

*これらのステップは現在、Spark 実行でサポートされていません。 

## <a name="code-block-types"></a>コード ブロック タイプ 
これらのステップごとに、2 つのコード ブロック タイプをサポートします。 まず、そのまま実行される基本の Python 拡張機能をサポートします。 次に、Python モジュールをサポートします。このモジュールでは、ユーザーが記述するコードにおいて、既知のシグネチャを使用して特定の関数を呼び出します。

たとえば、次の 2 つの方法で、別の列の対数を計算する 2 つの列を追加できます。式: 

```python    
    math.log(row["Score"])
```

モジュール: 
    
```python
def newvalue(row): 
        return math.log(row["Score"])
```


モジュール モードの Add Column (列の追加) 変換では、行変数を受け取って列の値を返す `newvalue` という関数が見つかることを期待します。 このモジュールには、他の関数、import などを含む任意の分量の Python コードを含めることができます。 

それぞれの拡張機能の詳細については、以下のセクションで説明します。 

## <a name="imports"></a>インポートする 
Expression (式) ブロック タイプを使用している場合でも、import ステートメントをコードに追加することはできますが、それらはコードの先頭のほうの行にグループ化する必要があります。 正しい: 

```python
import math 
import numpy 
math.log(row["Score"])
```
 

エラー:  

```python
import math  
math.log(row["Score"])  
import numpy
```
 
 
Module (モジュール) ブロック タイプを使用している場合、‘import’ ステートメントの使用に関して、通常の Python ルールのすべてに従うことができます。 

## <a name="default-imports"></a>既定の import
次の import は常に含まれており、コードで使用できます。 それらを再インポートする必要はありません。 

```python
import math  
import numbers  
import datetime  
import re  
import pandas as pd  
import numpy as np  
import scipy as sp
```
  

## <a name="installing-new-packages"></a>新しいパッケージのインストール
既定でインストールされていないパッケージを使用するには、まず Data Prep が使用する環境にそのパッケージをインストールする必要があります。 このインストールは、ローカル コンピューターと、実行場所になるすべてのコンピューティング ターゲットの両方で行う必要があります。

コンピューティング ターゲットにパッケージをインストールするには、プロジェクトのルート下の aml_config フォルダーにある conda_dependencies.yml ファイルを変更する必要があります。

### <a name="windows"></a>Windows 
Windows で場所を探すには、python のアプリ固有インストールとその scripts ディレクトリを探します。既定では次のとおりです。  

`C:\Users\<user>\AppData\Local\AmlWorkbench\Python\Scripts.` 

その後、次のコマンドのいずれかを実行します。 

`conda install <libraryname>` 

or 

`pip install <libraryname> `

### <a name="mac"></a>Mac 
Windows で場所を探すには、python のアプリ固有インストールとその scripts ディレクトリを探します。既定の場所は次のとおりです。 

`/Users/<user>/Library/Caches/AmlWorkbench>/Python/bin` 

その後、次のコマンドのいずれかを実行します。 

`./conda install <libraryname>`

or 

`./pip install <libraryname>`

## <a name="column-data"></a>列のデータ 
ドット表記またはキー/値表記を使用して、行から列のデータにアクセスできます。 スペースや特殊文字が含まれている列名は、ドット表記を使用してアクセスできません。 python 実行の両方のモード (モジュールおよび式) で、`row` 変数が常に定義されている必要があります。 

次に例を示します。 

```python
    row.ColumnA + row.ColumnB  
    row["ColumnA"] + row["ColumnB"]
```

## <a name="file-reader"></a>ファイル リーダー 
### <a name="purpose"></a>目的 
この拡張機能ポイントにより、データ フローへのファイル読み取りのプロセスを完全に制御できます。 システムは、処理すべきファイルのリストを渡してユーザーのコードを呼び出します。ユーザーのコードは、Pandas データフレームを作成して返す必要があります。 

>[!NOTE]
>この拡張機能ポイントは、Spark では機能しません。 


### <a name="how-to-use"></a>使用方法 
この拡張機能ポイントには、[Open Data Source] (データ ソースを開く) ウィザードからアクセスします。 最初のページで [File] (ファイル) を選択し、ファイルの場所を選択します。 [Choose File Parameters] (ファイル パラメーターの選択) ページで、[File Type] (ファイルの種類) ドロップダウンから [Custom File (Script)] (カスタム ファイル (スクリプト)) を選択します。 

読み取る必要のあるファイルに関する情報を含む、‘df’ という名前の Pandas データフレームがコードに与えられます。 複数のファイルを含むディレクトリを開くことを選択した場合、データフレームには複数の行が含まれます。  

このデータフレームには、次の列があります。 
- Path – 読み取るファイル。
- PathHint – ファイルの場所を示します。 値: ‘Local’、‘AzureBlobStorage’、‘AzureDataLakeStorage’
- AuthenticationType – ファイルにアクセスするために使用する認証の種類。 値: ‘None’、‘SasToken’、‘OAuthToken’
- AuthenticationValue - None または使用するトークン。

### <a name="syntax"></a>構文 
式: 

```python
    paths = df['Path'].tolist()  
    df = pd.read_csv(paths[0])
```


モジュール:  
```python
PathHint = Local  
def read(df):  
    paths = df['Path'].tolist()  
    filedf = pd.read_csv(paths[0])  
    return filedf  
```
 

## <a name="writer"></a>ライター 
### <a name="purpose"></a>目的 
ライター拡張機能ポイントにより、データ フローからのデータ書き込みのプロセスを完全に制御できます。 システムはデータフレームを渡してユーザーのコードを呼び出し、ユーザーのコードはデータフレームを使用して自由にデータを書き込むことができます。 

>[!NOTE]
>ライター拡張機能ポイントは Spark では機能しません。 


### <a name="how-to-use"></a>使用方法 
この拡張機能ポイントは ‘Write Dataflow (Script)’ (データ フローの書き込み (スクリプト)) ブロックを使用して追加できます。 トップレベルの [Transformations] (変換) メニューから使用できます。 

### <a name="syntax"></a>構文 
式: 

```python
    df.to_csv('c:\\temp\\output.csv')
```

モジュール:

```python
def write(df):  
    df.to_csv('c:\\temp\\output.csv')  
    return df
```
 
 
このカスタムの書き込みブロックはステップのリストの半ばに置くことができるため、モジュールを使用する場合、ユーザーの書き込み関数は、後続のステップへの入力であるデータフレームを返す必要があります。 

## <a name="add-column"></a>列の追加 
### <a name="purpose"></a>目的
この拡張機能ポイントを使用して、新しい列を計算する Python を記述できます。 記述するコードは行全体にアクセスできます。 コードは行ごとに新しい列値を返す必要があります。 

### <a name="how-to-use"></a>使用方法
この拡張機能ポイントは ‘Add Column (Script)’ (列の追加 (スクリプト)) ブロックを使用して追加できます。 トップレベルの [Transformations] (変換) メニューと、列のコンテキスト メニューから使用できます。 

### <a name="syntax"></a>構文
式: 

```python
    math.log(row["Score"])
```

モジュール: 

```python
def newvalue(row):  
     return math.log(row["Score"])
```
 

## <a name="advanced-filter"></a>高度なフィルター
### <a name="purpose"></a>目的 
この拡張機能ポイントを使用して、カスタム フィルターを記述できます。 ユーザーは行全体にアクセスでき、ユーザーのコードは True (行を含める) または False (行を除外する) を返す必要があります。 

### <a name="how-to-use"></a>使用方法
この拡張機能ポイントは ‘Advanced Filter (Script)’ (高度なフィルター (スクリプト)) ブロックを使用して追加できます。 トップレベルの [Transformations] (変換) メニューから使用できます。 

### <a name="syntax"></a>構文

式: 

```python
    row["Score"] > 95
```

モジュール:  

```python
def includerow(row):  
    return row["Score"] > 95
```
 

## <a name="transform-dataflow"></a>変換データ フロー
### <a name="purpose"></a>目的 
この拡張機能ポイントを使用して、データ フローを完全に変換できます。 ユーザーは、処理中のすべての列と行を含む Pandas データフレームにアクセスできます。ユーザーのコードは、新しいデータが入った Pandas データフレームを返す必要があります。 

>[!NOTE]
>Python では、この拡張機能を使用する場合に、Pandas データフレームでメモリに読み込まれるすべてのデータです。 

Spark では、すべてのデータは 1 つのワーカー ノードに集められます。 この結果、データが非常に大きい場合にワーカーでメモリが不足する可能性があります。 慎重に使用してください。

### <a name="how-to-use"></a>使用方法 
この拡張機能ポイントは ‘Transform Dataflow (Script)’ (データ フローの変換 (スクリプト)) ブロックを使用して追加できます。 トップレベルの [Transformations] (変換) メニューから使用できます。 
### <a name="syntax"></a>構文 

式: 

```python
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

モジュール: 

```python
def transform(df):  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()  
    return df
```
  

## <a name="transform-partition"></a>パーティションの変換  
### <a name="purpose"></a>目的 
この拡張機能ポイントを使用して、データ フローのパーティションを変換できます。 ユーザーは、そのパーティションのすべての列と行を含む Pandas データフレームにアクセスできます。ユーザーのコードは、新しいデータが入った Pandas データフレームを返す必要があります。 

>[!NOTE]
>Python では、データのサイズによって、最終的にパーティションは 1 つまたは複数になる可能性があります。 Spark では、ある特定のワーカー ノード上のパーティションのデータを保持するデータフレームを扱うことになります。 どちらの場合も、データ セット全体にアクセスできると想定することはできません。 


### <a name="how-to-use"></a>使用方法
この拡張機能ポイントは ‘Transform Partition (Script)’ (パーティションの変換 (スクリプト)) ブロックを使用して追加できます。 トップレベルの [Transformations] (変換) メニューから使用できます。 

### <a name="syntax"></a>構文 

式: 

```python
    df['partition-id'] = index  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

モジュール: 

```python
def transform(df, index):
    df['partition-id'] = index
    df['index-column'] = range(1, len(df) + 1)
    df = df.reset_index()
    return df
```


## <a name="datapreperror"></a>DataPrepError  
### <a name="error-values"></a>エラー値  
Data Prep には、エラー値の概念が存在します。 これは作成物であり、存在理由についてはここ<link to error values doc>で説明します。 

カスタムの python コードでエラー値に遭遇する可能性があります。 エラー値は、`DataPrepError` という Python クラスのインスタンスです。 このクラスは Python 例外をラップし、2 つのプロパティを持ちます。プロパティには、元の値の処理時に発生したエラーに関する情報と、元の値が含まれています。 


### <a name="datapreperror-class-definition"></a>DataPrepError クラス定義
```python 
class DataPrepError(Exception): 
    def __bool__(self): 
        return False 
``` 
Data Prep の python フレームワークにおける DataPrepError の作成は、一般的には次のようになります。 
```python 
DataPrepError({ 
   'message':'Cannot convert to numeric value', 
   'originalValue': value, 
   'exceptionMessage': e.args[0], 
   '__errorCode__':'Microsoft.DPrep.ErrorValues.InvalidNumericType' 
}) 
``` 
#### <a name="how-to-use"></a>使用方法 
以前からの作成方法を使用して、戻り値として DataPrepErrors を生成するために、拡張機能ポイントで Python が実行される可能性があります。 拡張機能ポイントでのデータ処理時に DataPrepErrors に遭遇する可能性はずっと高くなります。 現時点で、カスタムの Python コードは、DataPrepError を有効なデータ型として処理する必要があります。 

#### <a name="syntax"></a>構文 
式:  
```python 
    if (isinstance(row["Score"], DataPrepError)): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
```python 
    if (hasattr(row["Score"], "originalValue")): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
モジュール:  
```python 
def newvalue(row): 
    if (isinstance(row["Score"], DataPrepError)): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
``` 
```python 
def newvalue(row): 
    if (hasattr(row["Score"], "originalValue")): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
```  
