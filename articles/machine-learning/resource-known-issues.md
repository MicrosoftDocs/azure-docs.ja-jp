---
title: 既知の問題とトラブルシューティング
titleSuffix: Azure Machine Learning
description: Azure Machine Learning のエラーを見つけて修正するための支援を得ます。 既知の問題、トラブルシューティング、および対処方法について説明します。
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: troubleshooting, contperfq4
ms.date: 08/13/2020
ms.openlocfilehash: 02c733c7849c89f9d48ddbe75ffbb2235e1be58e
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2020
ms.locfileid: "88757287"
---
# <a name="known-issues-and-troubleshooting-in-azure-machine-learning"></a>Azure Machine Learning の既知の問題とトラブルシューティング

この記事は、Azure Machine Learning の使用時に発生する可能性がある既知の問題のトラブルシューティングに役立ちます。 

トラブルシューティングの詳細については、この記事の末尾の「[次のステップ](#next-steps)」を参照してください。

> [!TIP]
> エラーまたはその他の問題が、Azure Machine Learning を操作する際に生じる[リソース クォータ](how-to-manage-quotas.md)の問題の結果である場合があります。 

## <a name="access-diagnostic-logs"></a>診断ログにアクセスする

サポートを依頼するときに診断情報を提供できると、役に立つ場合があります。 いくつかのログを表示するには: 
1. [Azure Machine Learning Studio](https://ml.azure.com) にアクセスします。 
1. 左側の **[実験]** を選択します。 
1. 実験を選択します。
1. 実行を選択します。
1. 上部の **[出力 + ログ]** を選択します。

> [!NOTE]
> Azure Machine Learning では、AutoML やトレーニング ジョブを実行する Docker コンテナーなど、トレーニング中にさまざまなソースからの情報がログに記録すされます。 これらのログの多くについては、ドキュメントに記載されていません。 問題が発生し、Microsoft サポートに問い合わせた場合、サポートはトラブルシューティングの際にこれらのログを使用できる可能性があります。


## <a name="installation-and-import"></a>インストールとインポート
                           
* **pip のインストール:依存関係は単一行のインストールとの整合性が保証されていない**: 

   これは pip の既知の制限であり、単一行としてインストールするときに、機能する依存関係競合回避モジュールがないことが原因です。 pip によって参照されるのは、最初の固有の依存関係のみです。 

   次のコードでは、`azureml-datadrift` と `azureml-train-automl` の両方が単一行の pip インストールを使用してインストールされます。 
     ```
       pip install azureml-datadrift, azureml-train-automl
     ```
   この例で、`azureml-datadrift` にはバージョン > 1.0 が必要で、`azureml-train-automl` にはバージョン < 1.2 が必要であるとします。 `azureml-datadrift` の最新バージョンが 1.3 の場合、古いバージョンの `azureml-train-automl` パッケージ要件に関係なく、両方のパッケージが 1.3 にアップグレードされます。 

   パッケージに適切なバージョンがインストールされていることを確認するには、次のコードのように複数行を使用してインストールします。 pip は次の行呼び出しの一部として明示的にダウングレードされるため、ここでは順序は問題になりません。 そのため、適切なバージョンの依存関係が適用されます。
    
     ```
        pip install azureml-datadrift
        pip install azureml-train-automl 
     ```
     
* **azureml-train-automl-client をインストールする際に、説明パッケージのインストールが保証されていない:** 
   
   モデルの説明を有効にして、リモートの AutoML を実行すると、"Please install azureml-explain-model package for model explanations (モデルの説明のために azureml-explain-model パッケージをインストールしてください)" というエラー メッセージが表示されます。 これは既知の問題です。 回避策として、次のいずれかの手順に従います。
  
  1. azureml-explain-model をローカルでインストールします。
   ```
      pip install azureml-explain-model
   ```
  2. AutoML 構成で model_explainability=False を渡すことによって、説明機能を完全に無効にします。
   ```
      automl_config = AutoMLConfig(task = 'classification',
                             path = '.',
                             debug_log = 'automated_ml_errors.log',
                             compute_target = compute_target,
                             run_configuration = aml_run_config,
                             featurization = 'auto',
                             model_explainability=False,
                             training_data = prepped_data,
                             label_column_name = 'Survived',
                             **automl_settings)
    ``` 
    
* **panda のエラー:通常は AutoML 実験中に見られる**:
   
   pip を使用して手動で環境を設定すると、サポートされていないパッケージ バージョンがインストールされるため、(特に pandas の) 属性エラーが表示される場合があります。 このようなエラーを回避するには、[automl_setup.cmd を使用して AutoML SDK をインストールしてください](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md)。
   
    1. Anaconda プロンプトを開き、一連のサンプル ノートブックの GitHub リポジトリをクローンします。

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```
    
    2. cd を使用して、サンプル ノートブックが抽出された how-to-use-azureml/automated-machine-learning フォルダーに移動し、次のように実行します。
    
    ```bash
    automl_setup
    ```
    
* **KeyError: ローカル コンピューティングまたは Azure Databricks クラスターで AutoML を実行すると、'ブランド' になる**

    SDK 1.7.0 以前を使用して 2020 年 6 月 10 日より後に新しい環境を作成した場合、py-cpuinfo パッケージの更新によって、トレーニングがこのエラーで失敗することがあります。 (2020 年 6 月 10 日以前に作成された環境、およびリモート コンピューティングで実行された実験ではキャッシュされたトレーニング画像が使用されるため、影響を受けません)。この問題を回避するには、次の 2 つの手順のいずれかを実行します。
    
    * SDK のバージョンを 1.8.0 以降に更新します (これにより、py-cpuinfo も 5.0.0 にダウングレードされます)。
    
      ```bash
      pip install --upgrade azureml-sdk[automl]
      ```
    
    * インストールされている py-cpuinfo のバージョンを 5.0.0 にダウングレードします。
    
      ```bash
      pip install py-cpuinfo==5.0.0
      ```
  
* **エラー メッセージ:"Cannot uninstall 'PyYAML'" ('PyYAML' をアンインストールできません)**

    Azure Machine Learning SDK for Python:PyYAML は `distutils` によってインストールされるプロジェクトです。 したがって、部分的なアンインストールが行われた場合、それに属しているファイルを正確に判別できません。 このエラーを無視して SDK のインストールを続行するには、次を使用します。
    
    ```Python
    pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
    ```

* **Azure Machine Learning SDK のインストールが次の例外で失敗する:ModuleNotFoundError:'ruamel' という名前のモジュールはありません、または 'ImportError:ruamel.yaml という名前のモジュールはありません'**
   
   この問題は、Python 用 Azure Machine Learning SDK のすべてのリリース バージョンの conda ベース環境で、最新の pip (>20.1.1) に Python 用 Azure Machine Learning SDK をインストールしたときに発生します。 次の回避策を参照してください。

    * conda ベース環境に Python SDK をインストールしないでください。代わりに conda 環境を作成し、新しく作成されたユーザー環境に SDK をインストールします。 最新の pip は、その新しい conda 環境で動作します。

    * Docker でイメージを作成するときに、conda ベース環境から切り替えることができない場合は、pip<=20.1.1 を Docker ファイルにピン留めしてください。

    ```Python
    conda install -c r -y conda python=3.6.2 pip=20.1.1
    ```
    
* **パッケージをインストールする際の Databricks のエラー**

    追加のパッケージがインストールされていると、Azure Databricks で Azure Machine Learning SDK のインストールが失敗します。 `psutil` のようなパッケージでは、競合が発生することがあります。 インストール エラーを回避するには、ライブラリ バージョンを止めてパッケージをインストールします。 この問題は Databricks に関連したもので、Azure Machine Learning SDK には関連はありません。 他のライブラリでもこの問題が発生する場合があります。 例:
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    別の方法として、Python ライブラリでインストールの問題が発生し続けている場合は、初期化スクリプトを使用することができます。 この方法は、公式にはサポートされていません。 詳細については、「[Cluster-scoped init scripts](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)」を参照してください。

* **Databricks インポート エラー: `pandas._libs.tslibs`** から名前 `Timedelta` をインポートできません:自動機械学習を使用するときにこのエラーが表示される場合は、notebook で次の 2 行を実行します。
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **Databricks インポート エラー:'pandas.core.indexes' という名前のモジュールはありません**:自動化された機械学習を使用しているときにこのエラーが表示される場合は、以下を実行します。

    1. 次のコマンドを実行して、Azure Databricks クラスターに 2 つのパッケージをインストールします。
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. クラスターをデタッチし、次にクラスターをノートブックに再アタッチします。
    
    これらの手順で問題が解決しない場合は、クラスターを再起動してみてください。

* **Databricks FailToSendFeather**:Azure Databricks クラスター上のデータの読み取り時に `FailToSendFeather` エラーが表示された場合は、次の解決策を参照してください。
    
    * `azureml-sdk[automl]` パッケージを最新バージョンにアップグレードします。
    * `azureml-dataprep` バージョン 1.1.8 以降を追加します。
    * `pyarrow` バージョン 0.11 以降を追加します。
    
## <a name="create-and-manage-workspaces"></a>ワークスペースの作成と管理

> [!WARNING]
> Azure Machine Learning ワークスペースを別のサブスクリプションに移動したり、所有するサブスクリプションを新しいテナントに移動したりすることは、サポートされていません。 エラーの原因になります。

* **Azure ポータル**:SDK またはポータルで共有リンクからワークスペースを直接表示した場合、拡張機能のサブスクリプション情報を含む通常の **[概要]** ページは表示できません。 また、別のワークスペースに切り替えることもできません。 別のワークスペースを表示する必要がある場合は、[Azure Machine Learning Studio](https://ml.azure.com) に直接移動し、そのワークスペース名を検索してください。

* **Azure Machine Learning スタジオの Web ポータルでサポートされているブラウザー**:オペレーティング システムと互換性のある最新ブラウザーを使うことをお勧めします。 次のブラウザーがサポートされています。
  * Microsoft Edge (新しい Microsoft Edge の最新バージョンです。 Microsoft Edge レガシではありません)。
  * Safari (最新バージョン、Mac のみ)
  * Chrome (最新バージョン)
  * Firefox (最新バージョン)

## <a name="set-up-your-environment"></a>環境の設定方法

* **AmlCompute の作成に関する問題**:まれにですが、GA リリースの前に Azure portal から Azure Machine Learning ワークスペースを作成したユーザーが、そのワークスペースに AmlCompute を作成できないことがあります。 サービスにサポート リクエストを送るか、ポータルまたは SDK を使って新しいワークスペースを作成することで、すぐにブロックを解除することができます。

## <a name="work-with-data"></a>データの処理

### <a name="overloaded-azurefile-storage"></a>オーバーロードされた AzureFile ストレージ

エラー `Unable to upload project files to working directory in AzureFile because the storage is overloaded` が発生する場合は、次の回避策を適用してください。

データ転送などの他のワークロードにファイル共有を使用している場合は、BLOB を使用して、ファイル共有を実行の送信のために自由に使用できるようにすることをお勧めします。 2 つの異なるワークスペース間でワークロードを分割することもできます。

### <a name="passing-data-as-input"></a>入力としてのデータの引き渡し

*  **TypeError:FileNotFound:そのようなファイルまたはディレクトリはありません**:このエラーは、指定したファイル パスにファイルがない場合に発生します。 ファイルを参照する方法が、コンピューティング先でデータセットをマウントした場所と一致していることを確認する必要があります。 確定的な状態を確保するには、データセットをコンピューティング先にマウントするときに抽象パスを使用することをお勧めします。 たとえば、次のコードでは、コンピューティング先のファイルシステムのルート `/tmp` にデータセットをマウントしています。 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    先頭のスラッシュ "/" を含めない場合は、データセットをマウントする場所を示すために、コンピューティング先の作業ディレクトリをプレフィックスとして付ける必要があります (たとえば、`/mnt/batch/.../tmp/dataset`)。

### <a name="data-labeling-projects"></a>プロジェクトのラベル付けデータ

|問題  |解決方法  |
|---------|---------|
|BLOB データストアに作成されたデータセットしか使用できない。     |  これは、現在のリリースの既知の制限です。       |
|作成後、プロジェクトで "Initializing (初期化しています)" と長時間にわたり表示される。     | ページを手動で最新の情報に更新してください。 初期化の進行速度は、1 秒あたり約 20 データポイントです。 自動更新が実行されない問題が確認されています。         |
|画像をレビューする際に、新しくラベル付けされた画像が表示されない。     |   ラベル付けされたすべての画像を読み込むには、 **[First]\(1 番目\)** ボタンを選択します。 **[First]\(1 番目\)** ボタンを選択すると、リストの先頭に戻りますが、ラベル付けされたデータはすべて読み込まれます。      |
|オブジェクト検出のラベル付け中に Esc キーを押すと、左上隅にゼロ サイズのラベルが作成されます。 この状態でラベルを送信することはできません。     |   ラベルの横にある交差マークをクリックして、ラベルを削除してください。  |

### <a name="data-drift-monitors"></a><a name="data-drift"></a> データ ドリフト モニター

データ ドリフト モニターに関する制限事項と既知の問題

* 履歴データ分析時の時間範囲は、モニターの頻度設定の 31 間隔までに制限されます。 
* 特徴一覧が指定されていない (すべての特徴を使用する) 場合、200 の特徴に制限されます。
* コンピューティング サイズは、データを処理できる十分な大きさにする必要があります。
* 特定のモニターの実行について、開始日と終了日の範囲に該当するデータがデータセットに存在することを確認します。
* データセット モニターは、50 行以上を含むデータセットでのみ機能します。
* データセット内の列、つまり特徴は、次の表の条件に基づいてカテゴリまたは数値として分類されます。 特徴がこれらの条件を満たしていない場合 (たとえば、string 型の列に一意の値が 100 個以上含まれる場合)、その特徴はデータ ドリフト アルゴリズムから削除されますが、プロファイリングは引き続き行われます。 

    | 特徴の種類 | データ型 | 条件 | 制限事項 | 
    | ------------ | --------- | --------- | ----------- |
    | Categorical | string、bool、int、float | 特徴内の一意の値の数は、100 個未満であり、かつ行数の 5% 未満であること。 | null 値は独自のカテゴリとして扱われます。 | 
    | 数値 | int、float | 特徴内の値は数値データ型で、カテゴリの特徴の条件を満たしていません。 | 値の数の 15% を超える null が含まれる場合、その特徴は削除されます。 | 

* [データ ドリフト モニターを作成](how-to-monitor-datasets.md)したが、Azure Machine Learning Studio の **[データセット モニター]** ページにデータが表示されない場合は、次を試してください。

    1. ページの一番上で正しい日付範囲が選択されているかどうかを確認します。  
    1. **[データセット モニター]** タブで、実験リンクを選択し、実行状態を確認します。  このリンクはテーブルの右端にあります。
    1. 実行が正常に完了したら、生成されているメトリックの数や警告メッセージがあるかどうかをドライバー ログで確認します。  実験をクリックしたら、 **[出力 + ログ]** タブでドライバー ログを見つけます。

* SDK の `backfill()` 関数で予期された出力が生成されない場合は、認証の問題が原因である可能性があります。  この関数に渡す計算を作成するときに、`Run.get_context().experiment.workspace.compute_targets` を使用しないでください。  代わりに、次のような [ServicePrincipalAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) を使用して、その `backfill()` 関数に渡す計算を作成します。 

  ```python
   auth = ServicePrincipalAuthentication(
          tenant_id=tenant_id,
          service_principal_id=app_id,
          service_principal_password=client_secret
          )
   ws = Workspace.get("xxx", auth=auth, subscription_id="xxx", resource_group"xxx")
   compute = ws.compute_targets.get("xxx")
   ```

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning デザイナー

* **計算の準備時間が長い:**

初回の接続時またはコンピューティング ターゲットの作成時に、数分またはそれ以上の時間がかかることがあります。 

モデル データ コレクターからは、BLOB ストレージ アカウントにデータが到着するまでに最大で 10 分 (通常は 10 分未満) かかることがあります。 次のセルが実行されるように、10 分間待機します。

```python
import time
time.sleep(600)
```

* **リアルタイム エンドポイントのためのログ記録:**

リアルタイム エンドポイントのログは、お客様のデータです。 リアルタイム エンドポイントのトラブルシューティングでは、次のコードを使用してログを有効にできます。 

Web サービス エンドポイントの監視の詳細については、[この記事](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights#query-logs-for-deployed-models)を参照してください。

```python
from azureml.core import Workspace
from azureml.core.webservice import Webservice

ws = Workspace.from_config()
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```
複数のテナントがある場合は、`ws = Workspace.from_config()` の前に次の認証コードを追加する必要が生じることがあります

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="the tenant_id in which your workspace resides")
```

## <a name="train-models"></a>モデルをトレーニングする

* **ModuleErrors (モジュール名が指定されていない)** :Azure ML で実験を送信する際に ModuleErrors が発生した場合、トレーニング スクリプトではパッケージがインストールされていることを期待しているのに、それが追加されていないことを意味します。 パッケージ名を指定すると、Azure ML により、トレーニングの実行に使用される環境にパッケージがインストールされます。 

    [Estimator](concept-azure-machine-learning-architecture.md#estimators) を使用して実験を送信する場合は、パッケージのインストール元に基づく Estimator 内の `pip_packages` または `conda_packages` パラメータ―を使って、パッケージ名を指定できます。 また、`conda_dependencies_file` を使用してすべての依存関係を含む yml ファイルを指定したり、`pip_requirements_file` パラメーターを使用して txt ファイル内のすべての pip 要件を一覧表示したりすることも可能です。 Estimator で使用される既定のイメージをオーバーライドする独自の Azure ML 環境オブジェクトがある場合は、Estimator コンストラクターの `environment` パラメーターを使用してその環境を指定できます。

    Azure ML では、TensorFlow、PyTorch、Chainer、および SKLearn に対応するフレームワーク固有の Estimator も提供されています。 これらの Estimator を使用すると、ユーザーに代わって、トレーニングに使用される環境にコア フレームワークの依存関係が確実にインストールされます。 前述のように、追加の依存関係を指定することもできます。 
 
    Azure ML によって保守される docker イメージとそのコンテンツは、[AzureML のコンテナー](https://github.com/Azure/AzureML-Containers)内で確認できます。
    フレームワーク固有の依存関係は、それぞれのフレームワークのドキュメント ([Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks)、[PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks)、[TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks)、[SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks)) に示されています。

    > [!Note]
    > 特定のパッケージが Azure ML によって保守されるイメージと環境に追加できるほど十分に一般的だと考えられる場合は、[AzureML のコンテナー](https://github.com/Azure/AzureML-Containers)に関するページで、GitHub の問題を作成してください。 
 
* **NameError (名前が定義されていない)、AttributeError (オブジェクトに属性がない)** :この例外は、トレーニング スクリプトに起因しているはずです。 Azure portal からログ ファイルを参照して、定義されていない特定の名前または属性エラーに関する詳細情報を取得できます。 SDK から、`run.get_details()` を使用して、エラー メッセージを確認できます。 これにより、実行に対して生成されたすべてのログ ファイルも一覧表示されます。 実行を再送信する前に、トレーニング スクリプトを確認し、エラーを修正してください。 

* **Horovod がシャットダウンされている**:"AbortedError:Horovod がシャットダウンされました" に遭遇した場合のほとんどで、この例外は、プロセスの 1 つにおいて horovod のシャットダウンを引き起こす基となる例外が発生していたことを意味します。 MPI ジョブの各ランクでは、Azure ML 内にある固有の専用ログ ファイルが取得されます。 これらのログは、`70_driver_logs` という名前です。 分散トレーニングの場合、ログを区別しやすいようにログ名の末尾に `_rank` が付与されます。 実際に Horovod シャットダウンの原因となったエラーを見つけるには、すべてのログ ファイルを確認して、driver_log ファイルの末尾にある `Traceback` を探します。 これらのファイルの 1 つから、基になる実際の例外がわかります。 

* **実行または実験の削除**:実験をアーカイブするには、[Experiment.archive](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--) メソッドを使用するか、[Archive experiment]\(アーカイブ実験\) ボタンを介して Azure Machine Learning Studio クライアントの [実験] タブ ビューを使用します。 この操作により、実験はリスト クエリおよびビューから非表示になりますが、削除はされません。

    個々の実験または実行を完全に削除することは現在サポートされていません。 ワークスペース アセットの削除の詳細については、「[Machine Learning service のワークスペース データをエクスポートまたは削除する](how-to-export-delete-data.md)」を参照してください。

* **メトリック ドキュメントが大きすぎる**:Azure Machine Learning には、トレーニングの実行から一度にログに記録できるメトリック オブジェクトのサイズに関する内部制限があります。 リスト値メトリックのログ記録時に "Metric Document is too large (メトリック ドキュメントが大きすぎます)" エラーが発生した場合は、次の例のように、リストを小さいチャンクに分割してみてください。

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    内部的には、Azure ML は同じメトリック名を持つブロックを連続したリストへと連結します。

## <a name="automated-machine-learning"></a>自動化された機械学習

* **TensorFlow**: SDK のバージョン 1.5.0 以降の自動機械学習では、TensorFlow モデルは既定ではインストールされません。 自動 ML 実験で TensorFlow をインストールして使用するには、CondaDependecies を使用して tensorflow==1.12.0 をインストールしてください。 
 
   ```python
   from azureml.core.runconfig import RunConfiguration
   from azureml.core.conda_dependencies import CondaDependencies
   run_config = RunConfiguration()
   run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
  ```
* **実験グラフ**:自動化された ML の実験のイテレーションで示される二項分類グラフ (精度と再現率、ROC、ゲイン カーブなど) は、4/12 以降のユーザー インターフェイスでは正しくレンダリングされません。 グラフのプロットは現在、逆の結果を示しており、パフォーマンスが良いモデルほど低い結果で示されています。 解決策を調査中です。

* **Databricks での自動化された機械学習の実行をキャンセルする**:自動化された機械学習機能を Azure Databricks で使用しているときに、実行をキャンセルして新しい実験の実行を開始するには、Azure Databricks クラスターを再起動してください。

* **Databricks での自動化された機械学習の 10 回を超える繰り返し**:自動化された機械学習の設定で、繰り返し回数が 10 回を超えている場合は、実行を送信するときに `show_output` を `False` に設定します。

* **Azure Machine Learning SDK 用の Databricks ウィジェットと自動化された機械学習**:Azure Machine Learning SDK ウィジェットは、Databricks ノートブックではサポートされていません。この理由は、ノートブックが HTML ウィジェットを解析できないからです。 Azure Databricks のノートブック セルで次の Python コードを使用することにより、portal でウィジェットを表示することができます。

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```
* **automl_setup が失敗する**: 
    * Windows の場合は、Anaconda プロンプトから automl_setup を実行します。 Miniconda をインストールするには、[ここ](https://docs.conda.io/en/latest/miniconda.html)をクリックします。
    * `conda info` コマンドを実行して、Conda 64 ビット (32 ビットではなく) がインストールされていることを確認します。 `platform` は、Windows の場合は `win-64`、Mac の場合は `osx-64` にする必要があります。
    * Conda 4.4.10 以降がインストールされていることを確認します。 バージョンは、コマンド `conda -V` を使用して確認できます。 以前のバージョンがインストールされている場合は、`conda update conda` コマンドを使用して更新できます。
    * Linux - `gcc: error trying to exec 'cc1plus'`
      *  `gcc: error trying to exec 'cc1plus': execvp: No such file or directory` エラーが発生した場合は、コマンド `sudo apt-get install build-essential` を使用して build essential をインストールします。
      * 新しい名前を最初のパラメーターとして automl_setup に渡して、新しい Conda 環境を作成します。 `conda env list` を使用して既存の Conda 環境を表示し、`conda env remove -n <environmentname>` を使用してそれらを削除します。
      
* **automl_setup_linux が失敗する**:Ubuntu Linux で automl_setup_linus.sh がエラーで失敗する場合: `unable to execute 'gcc': No such file or directory`-
  1. 送信ポート 53 および 80 が有効になっていることを確認します。 Azure VM でこれを行うには、Azure portal で VM を選択し、[ネットワーク] をクリックします。
  2. `sudo apt-get update` コマンドを実行します
  3. `sudo apt-get install build-essential --fix-missing` コマンドを実行します
  4. `automl_setup_linux.sh` をもう一度実行します

* **configuration.ipynb が失敗する**:
  * ローカル Conda の場合は、最初に automl_setup が正常に実行されていることを確認します。
  * subscription_id が正しいことを確認します。 Azure portal で [すべてのサービス]、[サブスクリプション] の順に選択して、subscription_id を見つけます。 subscription_id 値に文字 "<" と ">" を含めることはできません。 たとえば、`subscription_id = "12345678-90ab-1234-5678-1234567890abcd"` は有効な形式です。
  * 共同作成者または所有者がサブスクリプションにアクセスできることを確認します。
  * リージョンがサポートされているリージョン (`eastus2`、`eastus`、`westcentralus`、`southeastasia`、`westeurope`、`australiaeast`、`westus2`、`southcentralus`) のいずれかであることを確認します。
  * Azure portal を使用してリージョンにアクセスできることを確認します。
  
* **AutoMLConfig のインポートが失敗する**:自動機械学習バージョン 1.0.76 にはパッケージの変更があり、新しいバージョンに更新する前に、以前のバージョンをアンインストールする必要があります。 v1.0.76 より前の SDK バージョンから v1.0.76 以降にアップグレードした後で `ImportError: cannot import name AutoMLConfig` が発生した場合は、`pip uninstall azureml-train automl` を実行してから `pip install azureml-train-auotml` を実行してエラーを解決します。 これは、automl_setup.cmd スクリプトによって自動的に行われます。 

* **workspace.from_config が失敗する**:呼び出し ws = Workspace.from_config()' が失敗する場合 -
  1. configuration.ipynb ノートブックが正常に実行されていることを確認します。
  2. ノートブックが、`configuration.ipynb` が実行されたフォルダーの配下ではないフォルダーから実行されている場合は、フォルダー aml_config と、それに含まれているファイル config.json を新しいフォルダーにコピーします。 Workspace.from_config により、ノートブック フォルダーまたはその親フォルダーの config.json が読み取られます。
  3. 新しいサブスクリプション、リソース グループ、ワークスペース、またはリージョンが使用されている場合は、もう一度 `configuration.ipynb` ノートブックを実行してください。 指定されたサブスクリプションの指定されたリソース グループにワークスペースが既に存在する場合にのみ、config.json を直接変更することができます。
  4. リージョンを変更する場合は、ワークスペース、リソース グループ、またはサブスクリプションを変更してください。 指定されたリージョンが異なる場合でも、ワークスペースが既に存在する場合は、`Workspace.create` によりワークスペースが作成または更新されることはありません。
  
* **サンプル ノートブックが失敗する**:プロパティ、メソッド、またはライブラリが存在しないというエラーでサンプル ノートブックが失敗する場合:
  * Jupyter Notebook で正しいカーネルが選択されていることを確認します。 カーネルがノートブック ページの右上に表示されます。 既定値は azure_automl です。 カーネルがノートブックの一部として保存されていることに注意してください。 そのため、新しい Conda 環境に切り替える場合は、ノートブックで新しいカーネルを選択する必要があります。
      * Azure Notebooks の場合は、Python 3.6 にする必要があります。 
      * ローカルの Conda 環境の場合は、automl_setup で指定した Conda 環境名にする必要があります。
  * ノートブックが、使用している SDK のバージョンに対応していることを確認します。 Jupyter Notebook セルで `azureml.core.VERSION` を実行することで、SDK のバージョンを確認できます。 GitHub から以前のバージョンのサンプル ノートブックをダウンロードするには、[`Branch`] ボタンをクリックし、[`Tags`] タブを選択して、バージョンを選択します。

* **NumPy のインポートが Windows で失敗する**:一部の Windows 環境で、Python の最新バージョン 3.6.8 を使用した NumPy の読み込みでエラーが発生することがあります。 この問題が発生した場合は、Python バージョン 3.6.7 を試してください。

* **NumPy のインポートが失敗する**:自動 ML Conda 環境で TensorFlow のバージョンを確認します。 サポートされているバージョンは、1.13 未満です。 バージョンが 1.13 以降の場合は、環境から TensorFlow をアンインストールします。TensorFlow のバージョンを確認して、次のようにアンインストールできます。
  1. コマンド シェルを起動し、自動 ML パッケージがインストールされている Conda 環境をアクティブにします。
  2. `pip freeze` を入力して `tensorflow`を探します。見つかった場合は、表示されるバージョンは 1.13 未満になるはずです。
  3. 表示されているバージョンがサポートされているバージョンでない場合は、コマンド シェルで `pip uninstall tensorflow` を入力し、確認のために「y」を入力します。

## <a name="deploy--serve-models"></a>モデルをデプロイおよび提供する

次のエラーに対して、これらのアクションを実行します。

|エラー  | 解決方法  |
|---------|---------|
|Web サービスのデプロイ時のイメージ構築エラー     |  イメージ構成用の pip の依存関係として "pynacl==1.2.1" を Conda ファイルに追加します。       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   デプロイで使用される VM の SKU を、メモリがより多い SKU に変更します。 |
|FPGA エラー     |  FPGA クォータを要求して承認されるまでは、FPGA にモデルをデプロイできません。 アクセスを要求するには、クォータ要求フォーム https://aka.ms/aml-real-time-ai に入力します。       |

### <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>AKS クラスター内の Azure Machine Learning コンポーネントの更新

Azure Kubernetes Service クラスターにインストールされている Azure Machine Learning コンポーネントの更新プログラムは、手動で適用する必要があります。 

これらの更新プログラムを適用するには、Azure Machine Learning ワークスペースからクラスターをデタッチしてから、クラスターをワークスペースに再アタッチします。 クラスターで TLS が有効な場合は、クラスターを再アタッチするときに TLS/SSL 証明書と秘密キーを指定する必要があります。 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

TLS/SSL 証明書と秘密キーがなくなった場合、または Azure Machine Learning によって生成された証明書を使用している場合は、`kubectl` を使用してクラスターに接続し、シークレット `azuremlfessl` を取得することで、クラスターをデタッチする前にファイルを取得できます。

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes では、base-64 でエンコードされた形式でシークレットが格納されます。 シークレットの `cert.pem` コンポーネントと `key.pem` コンポーネントを `attach_config.enable_ssl` に提供する前に、base-64 でデ コードする必要があります。 

### <a name="webservices-in-azure-kubernetes-service-failures"></a>Azure Kubernetes Service の Web サービスのエラー

Azure Kubernetes Service の多くの Web サービスのエラーは、`kubectl` を使用してクラスターに接続することでデバッグできます。 以下を実行して、Azure Kubernetes Service クラスターの `kubeconfig.json` を取得できます

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="authentication-errors"></a>認証エラー

リモート ジョブからコンピューティング ターゲットで管理操作を実行すると、次のいずれかのエラーが発生します。 

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

たとえば、リモート実行のために送信される ML パイプラインからコンピューティング ターゲットを作成またはアタッチしようとすると、エラーが発生します。

## <a name="missing-user-interface-items-in-studio"></a>スタジオにユーザー インターフェイス項目が見つからない

Azure のロールベースのアクセス制御を使用すると、Azure Machine Learning で実行できるアクションを制限できます。 これらの制限によって、Azure Machine Learning Studio にユーザー インターフェイス項目を表示しないようにすることができます。 たとえば、コンピューティング インスタンスを作成できないロールがユーザーに割り当てられている場合、コンピューティング インスタンスを作成するオプションは、スタジオには表示されません。

詳細については、「[ユーザーとロールを管理する](how-to-assign-roles.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Machine Learning のその他のトラブルシューティング記事を参照します。

* [Azure Machine Learning での Docker デプロイ トラブルシューティング](how-to-troubleshoot-deployment.md)
* [機械学習パイプラインのデバッグ](how-to-debug-pipelines.md)
* [Azure Machine Learning SDK からの ParallelRunStep クラスのデバッグ](how-to-debug-parallel-run-step.md)
* [VS Code を使用した機械学習コンピューティング インスタンスの対話型デバッグ](how-to-debug-visual-studio-code.md)
* [Application Insights を使用した機械学習パイプラインのデバッグ](how-to-debug-pipelines-application-insights.md)
