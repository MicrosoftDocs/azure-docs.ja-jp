---
title: Jupyter Notebook を使用して、Azure Data Explorer 内のデータを分析する
description: このトピックでは、Jupyter Notebook と Kqlmagic 拡張機能を使用して、Azure Data Explorer 内のデータを分析する方法を説明します。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 896a5d13279c15f0035f214da3d5a7d7e6f1861f
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045067"
---
# <a name="use-a-jupyter-notebook-and-kqlmagic-extension-to-analyze-data-in-azure-data-explorer"></a>Jupyter Notebook と Kqlmagic 拡張機能を使用して、Azure Data Explorer 内のデータを分析します。

Jupyter Notebook はオープン ソースの Web アプリケーションであり、ライブ コード、数式、視覚化、説明テキストを含むドキュメントを作成して共有するために使用できます。 用途には、データのクリーニングと変換、数値シミュレーション、統計モデリング、データの視覚化、機械学習などが含まれています。
[Jupyter Notebook](https://jupyter.org/) では、追加コマンドをサポートすることによってカーネルの機能を拡張するマジック関数がサポートされています。 KQL magic は、Kusto 言語のクエリをネイティブに実行できるように、Jupyter Notebook で Python カーネルの機能を拡張するコマンドです。 Python と Kusto クエリ言語を簡単に組み合わせて、`render` コマンドに統合されたリッチな Plot.ly ライブラリを使用してデータのクエリと視覚化を実行できます。 クエリを実行するためのデータ ソースがサポートされています。 このようなデータ ソースとしては、ログとテレメトリ データのための高速でスケーラブルなデータ探索サービスである Azure Data Explorer や、Azure Monitor ログ、Application Insights などがあります。 KQL magic は、Azure Notebooks、Jupyter Lab、および Visual Studio Code Jupyter 拡張機能でも動作します。

## <a name="prerequisites"></a>前提条件

- Azure Active Directory (AAD) のメンバーである、組織の電子メール アカウント。
- ローカル コンピューターにインストールされた Jupyter Notebook、または Azure Notebook を使用してサンプルの [Azure ノートブック](https://kustomagicsamples-manojraheja.notebooks.azure.com/j/notebooks/Getting%20Started%20with%20kqlmagic%20on%20Azure%20Data%20Explorer.ipynb)を複製します

## <a name="install-kql-magic-library"></a>KQL magic ライブラリのインストール

1. KQL magic をインストールします。

    ```python
    !pip install Kqlmagic --no-cache-dir  --upgrade
    ```
    > [!NOTE]
    > Azure Notebooks を使用するときは、この手順は必要ありません。

1. KQL magic を読み込みます。

    ```python
    reload_ext Kqlmagic
    ```

## <a name="connect-to-the-azure-data-explorer-help-cluster"></a>Azure Data Explorer のヘルプ クラスターに接続する

次のコマンドを使用して、*Help* クラスターでホストされている *Samples* データベースに接続します。 Microsoft 以外の AAD ユーザーの場合は、テナント名 `Microsoft.com` をお使いの AAD テナントに置き換えてください。

```python
%kql AzureDataExplorer://tenant="Microsoft.com";code;cluster='help';database='Samples'
```

## <a name="query-and-visualize"></a>クエリと視覚化を実行する

[render 演算子](/azure/kusto/query/renderoperator)を使用してデータのクエリを実行し、ploy.ly ライブラリを使用してデータを視覚化します。 このクエリと視覚化では、ネイティブの KQL を使用する統合されたエクスペリエンスが提供されます。 Kqlmagic では、`timepivot`、`pivotchart`、`ladderchart` を除くほとんどのグラフがサポートされています。 レンダリングは、`kind`、`ysplit`、`accumulate` を除くすべての属性でサポートされています。 

### <a name="query-and-render-piechart"></a>クエリを実行して円グラフをレンダリングする

```python
%%kql
StormEvents
| summarize statecount=count() by State
| sort by statecount 
| limit 10
| render piechart title="My Pie Chart by State"
```

### <a name="query-and-render-timechart"></a>クエリを実行して時間グラフをレンダリングする

```python
%%kql
StormEvents
| summarize count() by bin(StartTime,7d)
| render timechart
```

> [!NOTE]
> これらのグラフは対話形式です。 特定の時間を拡大するには、時間範囲を選択します。

### <a name="customize-the-chart-colors"></a>グラフの色をカスタマイズする

既定のカラー パレットが好みでない場合は、パレット オプションを使用してグラフをカスタマイズします。 使用できるパレットは次の場所にあります。[KQL magic クエリ グラフ結果のカラー パレットを選択する](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)

1. パレットの一覧の場合:

    ```python
    %kql --palettes -popup_window
    ```

1. `cool` カラー パレットを選択し、もう一度クエリをレンダリングします。

    ```python
    %%kql -palette_name "cool"
    StormEvents
    | summarize statecount=count() by State
    | sort by statecount
    | limit 10
    | render piechart title="My Pie Chart by State"
    ```

## <a name="parameterize-a-query-with-python"></a>Python でクエリをパラメーター化する

KQL magic により、Kusto クエリ言語と Python の間での簡単なインターチェンジが可能になります。 詳細については、以下を参照してください。[Python を使って KQL magic のクエリをパラメーター化する](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb)

### <a name="use-a-python-variable-in-your-kql-query"></a>KQL のクエリで Python 変数を使用する

Python 変数の値をクエリで使用して、データをフィルター処理できます。

```python
statefilter = ["TEXAS", "KANSAS"]
```

```python
%%kql
let _state = statefilter;
StormEvents 
| where State in (_state) 
| summarize statecount=count() by bin(StartTime,1d), State
| render timechart title = "Trend"
```

### <a name="convert-query-results-to-pandas-dataframe"></a>クエリの結果を Pandas データフレームに変換する

Pandas データフレームで KQL クエリの結果にアクセスできます。 次のように、変数 `_kql_raw_result_` で最後に実行されたクエリ結果にでアクセスし、Pandas データフレームに結果を簡単に変換できます。

```python
df = _kql_raw_result_.to_dataframe()
df.head(10)
```

### <a name="example"></a>例

多くの分析シナリオでは、多数のクエリを含む再利用可能なノートブックを作成し、あるクエリから後続のクエリに結果をフィードすることが必要な場合があります。 次の例では、Python 変数 `statefilter` を使用してデータをフィルター処理しています。

1. クエリを実行し、`DamageProperty` が最大の上位 10 個の状態を表示します。

    ```python
    %%kql
    StormEvents
    | summarize max(DamageProperty) by State
    | order by max_DamageProperty desc
    | limit 10
    ```

1. クエリを実行して、上位の状態を抽出し、Python 変数に設定します。

    ```python
    df = _kql_raw_result_.to_dataframe()
    statefilter =df.loc[0].State
    statefilter
    ```

1. `let` ステートメントと Python 変数を使用してクエリを実行します。

    ```python
    %%kql
    let _state = statefilter;
    StormEvents 
    | where State in (_state)
    | summarize statecount=count() by bin(StartTime,1d), State
    | render timechart title = "Trend"
    ```

1. help コマンドを実行します。

    ```python
    %kql --help "help"
    ```

## <a name="next-steps"></a>次の手順

help コマンドを実行して、サポートされるすべての機能が含まれている次のサンプル ノートブックを調べます。
- [Azure Data Explorer に対して KQL magic を使ってみる](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStart.ipynb) 
- [Application Insights に対して KQL magic を使ってみる](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartAI.ipynb) 
- [Azure Monitor ログに対して KQL magic を使ってみる](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartLA.ipynb) 
- [Python を使って KQL magic のクエリをパラメーター化する](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) 
- [KQL magic クエリ グラフ結果のカラー パレットを選択する](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)
