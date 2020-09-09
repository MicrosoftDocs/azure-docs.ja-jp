---
title: ONNX を使用して Azure SQL Edge に ML モデルをデプロイする
description: 鉄鉱石の不純物を予測する、この 3 部構成の Azure SQL Edge チュートリアルの第 3 部では、SQL Edge で ONNX 機械学習モデルを実行します。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: f38a973611cb1ab18eead4ec51e6be91ada2cc40
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2020
ms.locfileid: "85318643"
---
# <a name="deploy-ml-model-on-azure-sql-edge-using-onnx"></a>ONNX を使用して Azure SQL Edge に ML モデルをデプロイする 

Azure SQL Edge で鉄鉱石の不純物を予測する、この 3 部構成チュートリアルの第 2 部では、次を行います。

1. Azure Data Studio を使用して、Azure SQL Edge インスタンスで SQL Database に接続します。
2. Azure SQL Edge の ONNX を使用して、鉄鉱石の不純物を予測します。

## <a name="connect-to-the-sql-database-in-the-azure-sql-edge-instance"></a>Azure SQL Edge インスタンスでの SQL Database への接続

1. Azure Data Studio を開きます。

2. **[ようこそ]** タブで、次の詳細情報を使用して新しい接続を開始します。

   |_フィールド_|_Value_|
   |-------|-------|
   |接続の種類| Microsoft SQL Server|
   |サーバー|このデモのために作成された VM で示されているパブリック IP アドレス|
   |ユーザー名|sa|
   |Password|Azure SQL Edge インスタンスの作成時に使用された強力なパスワード|
   |データベース|Default|
   |[サーバー グループ]|Default|
   |Name (名前) (省略可能)|必要に応じて名前を付けます。|

3. **[接続]**

4. **[ファイル]** セクションで新しいノートブックを開くか、キーボード ショートカット Alt + Windows + N を使用します。 

5. カーネルを Python 3 に設定します。

## <a name="predict-iron-ore-impurities-with-onnx"></a>ONNX を使用した鉄鉱石の不純物の予測

次の Python コードを Azure Data Studio ノートブックに入力して実行します。

1. まず、必須のパッケージをインストールしてインポートします。

   ```python
   !pip install azureml.core -q
   !pip install azureml.train.automl -q
   !pip install matplotlib -q
   !pip install pyodbc -q
   !pip install spicy -q
   
   import logging
   from matplotlib import pyplot as plt
   import numpy as np
   import pandas as pd
   import pyodbc
   
   from scipy import stats
   from scipy.stats import skew #for some statistics
   
   import azureml.core
   from azureml.core.experiment import Experiment
   from azureml.core.workspace import Workspace
   from azureml.train.automl import AutoMLConfig
   from azureml.train.automl import constants
   ```

1. 回帰実験用の Azure AutoML ワークスペースと AutoML 実験構成を定義します。

   ```python
   ws = Workspace(subscription_id="<Azure Subscription ID>",
                  resource_group="<resource group name>",
                  workspace_name="<ML workspace name>")
   # Choose a name for the experiment.
   experiment_name = 'silic_percent2-Forecasting-onnx'
   experiment = Experiment(ws, experiment_name)
   ```

1. データセットをパンダ フレームにインポートします。 モデル トレーニングのために、[採鉱処理における品質予測](https://www.kaggle.com/edumagalhaes/quality-prediction-in-a-mining-process)のための Kaggle のトレーニング データ セットを使用します。 データ ファイルをダウンロードして、開発用コンピューターにローカルに保存します。 このデータを使用して、鉄鉱石精鉱の不純物の量を予測します。

   ```python
   df = pd.read_csv("<local path where you have saved the data file>",decimal=",",parse_dates=["date"],infer_datetime_format=True)
   df = df.drop(['date'],axis=1)
   df.describe()
   ```

1. データを分析して、歪度を識別します。 このプロセスで、データ フレーム内の各列の分布と歪度の情報を確認します。

   ```python
   ## We can use a histogram chart to view the data distribution for the Dataset. In this example, we are looking at the histogram for the "% Silica Concentrate" 
   ## and the "% Iron Feed". From the histogram, you'll notice the data distribution is skewed for most of the features in the dataset. 
   
   f, (ax1,ax2,ax3) = plt.subplots(1,3)
   ax1.hist(df['% Iron Feed'], bins='auto')
   #ax1.title = 'Iron Feed'
   ax2.hist(df['% Silica Concentrate'], bins='auto')
   #ax2.title = 'Silica Concentrate'
   ax3.hist(df['% Silica Feed'], bins='auto')
   #ax3.title = 'Silica Feed'
   ```

1. データの歪度のレベルを調べて修正します。

   ```python
   ##Check data skewness with the skew or the kurtosis function in spicy.stats
   ##Skewness using the spicy.stats skew function
   for i in list(df):
           print('Skew value for column "{0}" is: {1}'.format(i,skew(df[i])))
   
   #Fix the Skew using Box Cox Transform
   from scipy.special import boxcox1p
   for i in list(df):
       if(abs(skew(df[i])) >= 0.20):
           #print('found skew in column - {0}'.format(i))
           df[i] = boxcox1p(df[i], 0.10)
           print('Skew value for column "{0}" is: {1}'.format(i,skew(df[i])))
   ```

1. 予測の特徴量と他の特徴量との相関関係を確認します。 相関関係が高くない場合は、それらの特徴量を削除します。

   ```python
   silic_corr = df.corr()['% Silica Concentrate']
   silic_corr = abs(silic_corr).sort_values()
   drop_index= silic_corr.index[:8].tolist()
   df = df.drop(drop_index, axis=1)
   df.describe()
   ```

1. AzureML 実験を開始し、最適なアルゴリズムを見つけてトレーニングします。 このケースでは、すべての回帰アルゴリズムを使用してテストを行っており、正規化された二乗平均平方根誤差 (NRMSE) が主要メトリックです。 詳細については、[Azure ML 実験の主要メトリックに関するページ](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#primary-metric)を参照してください。 次のコードによって、ML 実験のローカル実行が開始されます。

   ```python
   ## Define the X_train and the y_train data sets for the AutoML experiments. X_Train are the inputs or the features, while y_train is the outcome or the prediction result. 
   
   y_train = df['% Silica Concentrate']
   x_train = df.iloc[:,0:-1]
   automl_config = AutoMLConfig(task = 'regression',
                                primary_metric = 'normalized_root_mean_squared_error',
                                iteration_timeout_minutes = 60,
                                iterations = 10,                        
                                X = x_train, 
                                y = y_train,
                                featurization = 'off',
                                enable_onnx_compatible_models=True)
   
   local_run = experiment.submit(automl_config, show_output = True)
   best_run, onnx_mdl = local_run.get_output(return_onnx_model=True)
   ```

1. ローカル スコアリングのために Azure SQL Edge データベースにモデルを読み込みます。

   ```python
   ## Load the Model into a SQL Database.
   ## Define the Connection string parameters. These connection strings will be used later also in the demo.
   server = '<SQL Server IP address>'
   username = 'sa' # SQL Server username
   password = '<SQL Server password>'
   database = 'IronOreSilicaPrediction'
   db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
   conn = pyodbc.connect(db_connection_string, autocommit=True)
   cursor = conn.cursor()
   
   # Insert the ONNX model into the models table
   query = f"insert into models ([description], [data]) values ('Silica_Percentage_Predict_Regression_NRMSE_New1',?)"
   model_bits = onnx_mdl.SerializeToString()
   insert_params  = (pyodbc.Binary(model_bits))
   cursor.execute(query, insert_params)
   conn.commit()
   cursor.close()
   conn.close()
   ```

1. 最後に、Azure SQL Edge モデルを使用し、トレーニング済みのモデルを使用して予測を実行します。

   ```python
   ## Define the Connection string parameters. These connection strings will be used later also in the demo.
   server = '<SQL Server IP address>'
   username = 'sa' # SQL Server username
   password = '<SQL Server password>'
   database = 'IronOreSilicaPrediction'
   db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
   conn = pyodbc.connect(db_connection_string, autocommit=True)
   #cursor = conn.cursor()
   query = \
           f'declare @model varbinary(max) = (Select [data] from [dbo].[Models] where [id] = 1);' \
           f' with d as ( SELECT  [timestamp] ,cast([cur_Iron_Feed] as real) [__Iron_Feed] ,cast([cur_Silica_Feed]  as real) [__Silica_Feed]' \
           f',cast([cur_Starch_Flow] as real) [Starch_Flow],cast([cur_Amina_Flow] as real) [Amina_Flow]' \
           f' ,cast([cur_Ore_Pulp_pH] as real) [Ore_Pulp_pH] ,cast([cur_Flotation_Column_01_Air_Flow] as real) [Flotation_Column_01_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_02_Air_Flow] as real) [Flotation_Column_02_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_03_Air_Flow] as real) [Flotation_Column_03_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_07_Air_Flow] as real) [Flotation_Column_07_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_04_Level] as real) [Flotation_Column_04_Level]' \
           f' ,cast([cur_Flotation_Column_05_Level] as real) [Flotation_Column_05_Level]' \
           f' ,cast([cur_Flotation_Column_06_Level] as real) [Flotation_Column_06_Level]' \
           f' ,cast([cur_Flotation_Column_07_Level] as real) [Flotation_Column_07_Level]' \
           f' ,cast([cur_Iron_Concentrate] as real) [__Iron_Concentrate]' \
           f' FROM [dbo].[IronOreMeasurements1]' \
           f' where timestamp between dateadd(hour,-1,getdate()) and getdate()) ' \
           f' SELECT d.*, p.variable_out1' \
           f' FROM PREDICT(MODEL = @model, DATA = d) WITH(variable_out1 numeric(25,17)) as p;' 
     
   df_result = pd.read_sql(query,conn)
   df_result.describe()
   ```

1. Python を使用して、鉄鉱石の量に対して予測されるシリカの割合、日時およびシリカの量のグラフを作成します。

   ```python
   import plotly.graph_objects as go
   fig = go.Figure()
   fig.add_trace(go.Scatter(x=df_result['timestamp'],y=df_result['__Iron_Feed'],mode='lines+markers',name='Iron Feed',line=dict(color='firebrick', width=2)))
   fig.add_trace(go.Scatter(x=df_result['timestamp'],y=df_result['__Silica_Feed'],mode='lines+markers',name='Silica Feed',line=dict(color='green', width=2)))
   fig.add_trace(go.Scatter(x=df_result['timestamp'],y=df_result['variable_out1'],mode='lines+markers',name='Silica Percent',line=dict(color='royalblue', width=3)))
   fig.update_layout(height= 600, width=1500,xaxis_title='Time')
   fig.show()
   ```

## <a name="next-steps"></a>次のステップ

Azure SQL Edge での ONNX モデルの使用について詳しくは、「[SQL Edge (プレビュー) での ONNX を使用した機械学習と AI](onnx-overview.md)」を参照してください。
