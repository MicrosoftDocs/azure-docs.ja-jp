---
title: チュートリアル:Azure Cosmos DB でデータを分析して視覚化するノートブックを作成する
description: チュートリアル:組み込みの Jupyter ノートブックを使用して、Azure Cosmos DB へのデータのインポート、データの分析、出力の視覚化を行う方法について説明します。
author: deborahc
ms.topic: tutorial
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 11/05/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: beb58922172a045242f4f9bcaf647b3cfc8b5551
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100380799"
---
# <a name="tutorial-create-a-notebook-in-azure-cosmos-db-to-analyze-and-visualize-the-data"></a>チュートリアル:Azure Cosmos DB でデータを分析して視覚化するノートブックを作成する
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

この記事では、組み込みの Jupyter ノートブックを使用して、サンプルの小売データを Azure Cosmos DB にインポートする方法について説明します。 SQL と Azure Cosmos DB のマジック コマンドを使用してクエリを実行し、データを分析して、結果を視覚化する方法をご覧ください。

## <a name="prerequisites"></a>前提条件

* [Azure Cosmos アカウントでノートブックを有効にする](enable-notebooks.md)

## <a name="create-the-resources-and-import-data"></a>リソースを作成してデータをインポートする
 
このセクションでは、Azure Cosmos データベースとコンテナーを作成し、コンテナーに小売データをインポートします。

1. Azure Cosmos アカウントに移動して、**Data Explorer** を開きます。

1. **[Notebooks]** タブに移動し、 **[マイ ノートブック]** の横の [`…`] を選択して **新しいノートブック** を作成します。 既定のカーネルとして **[Python 3]** を選択します。

   :::image type="content" source="./media/create-notebook-visualize-data/create-new-notebook.png" alt-text="新しいノートブックを作成する":::

1. 新しいノートブックが作成された後、その名前を変更できます (**VisualizeRetailData.ipynb** など)。

1. 次に、小売データの格納先として、"RetailDemo" という名前のデータベースと "WebsiteData" という名前のコンテナーを作成します。 パーティション キーとしては /CartID を使用できます。 次のコードをコピーし、ノートブック内の新しいセルに貼り付けて実行します。

   ```python
   import azure.cosmos
   from azure.cosmos.partition_key import PartitionKey

   database = cosmos_client.create_database_if_not_exists('RetailDemo')
   print('Database RetailDemo created')

   container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
   print('Container WebsiteData created')
   ```

   セルを実行するには、`Shift + Enter` を選択します。または、セルを選択して、Data Explorer のナビゲーション バーの **[Run Active Cell]\(アクティブ セルの実行\)** オプションを選択します。

   :::image type="content" source="./media/create-notebook-visualize-data/run-active-cell.png" alt-text="アクティブ セルを実行する":::

   データベースとコンテナーは、現在の Azure Cosmos アカウントに作成されます。 コンテナーは、400 RU/秒でプロビジョニングされます。 データベースとコンテナーが作成された後、次の出力結果が表示されます。 

   ```console
    Database RetailDemo created
    Container WebsiteData created
   ```

   **[データ]** タブを最新の情報に更新することによって、新しく作成されたリソースを確認することもできます。

   :::image type="content" source="media/create-notebook-visualize-data/refresh-data-tab.png" alt-text="[データ] タブを最新の情報に更新して新しいコンテナーを表示する":::

1. 次に、サンプルの小売データを Azure Cosmos コンテナーにインポートします。 小売データの項目の形式は次のとおりです。

   ```json
    {
       "CartID":5399,
       "Action":"Viewed",
       "Item":"Cosmos T-shirt",
       "Price":350,
       "UserName":"Demo.User10",
       "Country":"Iceland",
       "EventDate":"2015-06-25T00:00:00",
       "Year":2015,"Latitude":-66.8673,
       "Longitude":-29.8214,
       "Address":"852 Modesto Loop, Port Ola, Iceland",
       "id":"00ffd39c-7e98-4451-9b91-b2bcf2f9a32d"
    }
   ```

   チュートリアルの目的上、サンプルの小売データは Azure Blob Storage に格納されています。 新しいセルに次のコードを貼り付けることで、Azure Cosmos コンテナーにそれをインポートできます。 データが正常にインポートされたことは、項目の件数を選択するクエリを実行することで確認できます。

   ```python
    # Read data from storage
    import urllib.request, json

    with urllib.request.urlopen("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/websiteData.json") as url:
      data = json.loads(url.read().decode())

    print("Importing data. This will take a few minutes...\n")

    for event in data:
     try: 
        container.upsert_item(body=event)
     except errors.CosmosHttpResponseError as e:
        raise
        
    ## Run a query against the container to see number of documents
    query = 'SELECT VALUE COUNT(1) FROM c'
    result = list(container.query_items(query, enable_cross_partition_query=True))

    print('Container with id \'{0}\' contains \'{1}\' items'.format(container.id, result[0]))
   ```

   前述のクエリを実行すると、次の出力が返されます。

   ```console
   Importing data. This will take a few minutes...

   Container with id 'WebsiteData' contains '2654' items
   ```

## <a name="get-your-data-into-a-dataframe"></a>データフレームにデータを取得する

データを分析するためのクエリを実行する前に、コンテナーから [Pandas データフレーム](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html)に分析対象のデータを読み取ることができます。 データフレームにデータを読み取るには、次の SQL マジック コマンドを使用します。

```bash
%%sql --database {database_id} --container {container_id} --output outputDataframeVar 
{Query text}
```

詳細については、[Azure Cosmos DB の組み込みのノートブック コマンドと機能](use-python-notebook-features-and-commands.md)に関する記事を参照してください。 クエリ `SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c` を実行します。 結果は、df_cosmos という名前の Pandas データフレームに保存されます。 新しいノートブック セルに次のコマンドを貼り付けて実行してください。

```python
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```

新しいノートブック セルで、次のコードを実行して、出力から最初の 10 項目を読み取ります。

```python
# See a sample of the result
df_cosmos.head(10)
```

:::image type="content" source="./media/create-notebook-visualize-data/run-query-get-top10-items.png" alt-text="クエリを実行して最初の 10 項目を取得する":::

## <a name="run-queries-and-analyze-your-data"></a>クエリを実行してデータを分析する

このセクションでは、取得したデータに対して、いくつかのクエリを実行します。

* **クエリ 1**: データフレームにグループ化クエリを実行して、国または地域ごとの総売上収益の合計を取得し、結果から 5 項目を表示します。 新しいノートブック セルで、次のコードを実行します。

   ```python
   df_revenue = df_cosmos.groupby("Country").sum().reset_index()
   display(df_revenue.head(5))
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/total-sales-revenue-output.png" alt-text="総売上収益出力":::

* **クエリ 2**:購入された項目の上位 5 件のリストを取得するために、新しいノートブック セルを開いて次のコードを実行します。

   ```python
   import pandas as pd

   ## What are the top 5 purchased items?
   pd.DataFrame(df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False).head(5), columns=['Count'])
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/top5-purchased-items.png" alt-text="購入された項目の上位 5 件":::

## <a name="visualize-your-data"></a>データを視覚化する  

1. Azure Cosmos コンテナーから収益に関するデータが得られたら、好きな視覚化ライブラリを使用してデータを視覚化できます。 このチュートリアルでは、Bokeh ライブラリを使用します。 新しいノートブック セルを開き、次のコードを実行して Bokeh ライブラリをインストールします。 すべての要件が満たされた後、ライブラリがインストールされます。

   ```python
   import sys
   !{sys.executable} -m pip install bokeh --user
   ```

1. 次に、地図上にデータをプロットするための準備を行います。 Azure Cosmos DB 内のデータと Azure Blob Storage に格納されている国または地域情報とを結合し、結果を GeoJSON 形式に変換します。 新しいノートブック セルに次のコードをコピーして実行してください。

   ```python
   import urllib.request, json
   import geopandas as gpd

   # Load country/region information for mapping
   countries = gpd.read_file("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/countries.json")

   # Merge the countries/regions dataframe with our data in Azure Cosmos DB, joining on country/region code
   df_merged = countries.merge(df_revenue, left_on = 'admin', right_on = 'Country', how='left')

   # Convert to GeoJSON so bokeh can plot it
   merged_json = json.loads(df_merged.to_json())
   json_data = json.dumps(merged_json)
   ```

1. 新しいノートブック セルで次のコードを実行し、世界地図上にさまざまな国または地域の売上収益を視覚化します。

   ```python
   from bokeh.io import output_notebook, show
   from bokeh.plotting import figure
   from bokeh.models import GeoJSONDataSource, LinearColorMapper, ColorBar
   from bokeh.palettes import brewer
    
   #Input GeoJSON source that contains features for plotting.
   geosource = GeoJSONDataSource(geojson = json_data)
    
   #Choose our choropleth color palette: https://bokeh.pydata.org/en/latest/docs/reference/palettes.html
   palette = brewer['YlGn'][8]
    
   #Reverse color order so that dark green is highest revenue
   palette = palette[::-1]
    
   #Instantiate LinearColorMapper that linearly maps numbers in a range, into a sequence of colors.
   color_mapper = LinearColorMapper(palette = palette, low = 0, high = 1000)
    
   #Define custom tick labels for color bar.
   tick_labels = {'0': '$0', '250': '$250', '500':'$500', '750':'$750', '1000':'$1000', '1250':'$1250', '1500':'$1500','1750':'$1750', '2000': '>$2000'}
    
   #Create color bar. 
   color_bar = ColorBar(color_mapper=color_mapper, label_standoff=8,width = 500, height = 20,
   border_line_color=None,location = (0,0), orientation = 'horizontal', major_label_overrides = tick_labels)
    
   #Create figure object.
   p = figure(title = 'Sales revenue by country', plot_height = 600 , plot_width = 1150, toolbar_location = None)
   p.xgrid.grid_line_color = None
   p.ygrid.grid_line_color = None
    
   #Add patch renderer to figure. 
   p.patches('xs','ys', source = geosource,fill_color = {'field' :'ItemRevenue', 'transform' : color_mapper},
              line_color = 'black', line_width = 0.25, fill_alpha = 1)
    
   #Specify figure layout.
   p.add_layout(color_bar, 'below')
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
   
   #Display figure.
   show(p)
   ```

   出力には、世界地図がさまざまな色で表示されます。 色が濃いほど収益が高い国または地域を、色が薄いほど収益が低い国または地域を表します。

   :::image type="content" source="./media/create-notebook-visualize-data/countries-revenue-map-visualization.png" alt-text="各国または地域での収益を視覚化した地図":::

1. データを視覚化するケースをもう 1 つ見てみましょう。 WebsiteData コンテナーには、項目を表示し、カートに追加し、購入したユーザーのレコードが含まれています。 購入された項目のコンバージョン率をプロットしてみましょう。 新しいセルで次のコードを実行すると、各項目のコンバージョン率が視覚化されます。

   ```python
   from bokeh.io import show, output_notebook
   from bokeh.plotting import figure
   from bokeh.palettes import Spectral3
   from bokeh.transform import factor_cmap
   from bokeh.models import ColumnDataSource, FactorRange
       
   # Get the top 10 items as an array
   top_10_items = df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False)[:10].index.values.tolist()
    
   # Filter our data to only these 10 items
   df_top10 = df_cosmos[df_cosmos['Item'].isin(top_10_items)]
    
   # Group by Item and Action, sorting by event count
   df_top10_sorted = df_top10.groupby(['Item', 'Action']).count().rename(columns={'Country':'ResultCount'}, inplace=False).reset_index().sort_values(['Item', 'ResultCount'], ascending = False).set_index(['Item', 'Action'])
    
   # Get sorted X-axis values - this way, we can display the funnel of view -> add -> purchase
   x_axis_values = df_top10_sorted.index.values.tolist()
    
   group = df_top10_sorted.groupby(['Item', 'Action'])
    
   # Specifiy colors for X axis
   index_cmap = factor_cmap('Item_Action', palette=Spectral3, factors=sorted(df_top10.Action.unique()), start=1, end=2)
    
   # Create the plot
    
   p = figure(plot_width=1200, plot_height=500, title="Conversion rate of items from View -> Add to cart -> Purchase", x_range=FactorRange(*x_axis_values), toolbar_location=None, tooltips=[("Number of events", "@ResultCount_max"), ("Item, Action", "@Item_Action")])
    
   p.vbar(x='Item_Action', top='ItemRevenue_max', width=1, source=group,
           line_color="white", fill_color=index_cmap, )
    
   #Configure how the plot looks
   p.y_range.start = 0
   p.x_range.range_padding = 0.05
   p.xgrid.grid_line_color = None
   p.xaxis.major_label_orientation = 1.2
   p.outline_line_color = "black"
   p.xaxis.axis_label = "Item"
   p.yaxis.axis_label = "Count"
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
    
   #Display figure.
   show(p)
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/visualize-purchase-conversion-rate.png" alt-text="購入のコンバージョン率を視覚化する":::

## <a name="next-steps"></a>次のステップ

* Python ノートブック コマンドの詳細については、[Azure Cosmos DB の組み込みのノートブック コマンドと機能の使用方法](use-python-notebook-features-and-commands.md)に関する記事を参照してください。
