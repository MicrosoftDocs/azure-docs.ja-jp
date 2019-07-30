---
title: このリリースの新機能
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service と Machine Learning および Data Prep Python SDK の最新情報について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: larryfr
author: Blackmist
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: d43bef902b66976c32735b6d45029f41bb5e3264
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514048"
---
# <a name="azure-machine-learning-service-release-notes"></a>Azure Machine Learning service のリリース ノート

この記事では、Azure Machine Learning service の各リリースについて説明します。  各 SDK の詳細については、以下に関するリファレンス ドキュメントを参照してください。
+ Azure Machine Learning の[**メインの SDK for Python**](https://aka.ms/aml-sdk)
+ Azure Machine Learning の[**Data Prep SDK**](https://aka.ms/data-prep-sdk)

バグおよび対処法については、[既知の問題のリスト](resource-known-issues.md)を参照してください。


## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>Azure Machine Learning Data Prep SDK v1.1.7

パフォーマンス向上のために行った変更は、Azure Databricks を使う一部のお客様で問題の原因になったため、元に戻されました。 Azure Databricks で問題が発生していた場合は、次のいずれかの方法を使ってバージョン 1.1.7 にアップグレードできます。
1. このスクリプトを実行してアップグレードします: `%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
2. クラスターを再作成します。そうすると、最新バージョンの Data Prep SDK がインストールされます。

## <a name="2019-06-25"></a>2019-06-25

### <a name="azure-machine-learning-sdk-for-python-v1045"></a>Azure Machine Learning SDK for Python v1.0.45

+ **新機能**
  + azureml-explain-model パッケージの説明を模倣するためのデシジョン ツリー サロゲート モデルを追加します
  + 推論イメージにインストールされる CUDA のバージョンを指定する機能。 CUDA 9.0、9.1、10.0 のサポート。
  + Azure ML トレーニングの基本イメージに関する情報を、[Azure ML コンテナーの GitHub リポジトリ](https://github.com/Azure/AzureML-Containers)および [DockerHub](https://hub.docker.com/_/microsoft-azureml) で入手できるようになります
  + パイプライン スケジュールの CLI サポートが追加されました。 詳細については "az ml pipeline -h" を実行してください
  + AKS Web サービスのデプロイ構成と CLI に、カスタム Kubernetes 名前空間パラメーターが追加されました。
  + パイプラインのすべてのステップで hash_paths パラメーターが非推奨になりました
  + Model.register で、`child_paths` パラメーターを使って複数の個別ファイルを 1 つのモデルとして登録することがサポートされるようになりました。
  
+ **プレビュー機能**
    + スコアリング Explainer で、より信頼性の高いシリアル化と逆シリアル化のために、必要に応じて Conda と PIP の情報を保存できるようになりました。
    + 自動機能選択のバグの修正。
    + mlflow.azureml.build_image を新しい API に更新し、新しい実装によって発生するようになったバグに修正プログラムを適用しました。

+ **重大な変更**

+ **バグの修正と機能強化**
  + azureml-core から paramiko の依存関係が削除されました。 従来のコンピューティング先のアタッチ方法に対する非推奨の警告が追加されました。
  + run.create_children のパフォーマンスが向上しました
  + バイナリ分類器を使う Mimic Explainer で、教師の確率が SHAP 値のスケーリングに使われるときの、確率の順序が修正されました。
  + 自動化された機械学習に対するエラー処理とメッセージが改善されました。 
  + 自動化された機械学習のイテレーションのタイムアウトの問題が修正されました。
  + 自動化された機械学習に対する時系列変換のパフォーマンスが向上しました。

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>Azure Machine Learning Data Prep SDK v1.1.6

+ **新機能**
  + 上位の値 (`SummaryFunction.TOPVALUES`) と下位の値 (`SummaryFunction.BOTTOMVALUES`) に対する集計関数が追加されました。

+ **バグの修正と機能強化**
  + `read_pandas_dataframe` のパフォーマンスが大幅に向上しました。
  + バイナリ ファイルを指し示すデータフローで `get_profile()` が失敗する原因のバグを修正しました。
  + テレメトリの収集をプログラムで有効/無効にできる `set_diagnostics_collection()` が公開されました。
  + `get_profile()` の動作が変更されました。 Min、Mean、Std、Sum に対して NaN 値が無視されるようになります。これは、Pandas の動作と一致します。


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>Azure Machine Learning SDK for Python v1.0.43

+ **新機能**
  + Azure Machine Learning で、人気のある機械学習およびデータ分析フレームワークである Scikit-learn の優れたサポートが提供されるようになりました。 [`SKLearn` 見積もりツール](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) を使うと、ユーザーは Scikit-learn のモデルを簡単にトレーニングしてデプロイできます。
    + [HyperDrive を使用して Scikit-learn でハイパーパラメーター調整を実行する](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb)方法をご覧ください。
  + 再利用可能なコンピューティング ユニットを管理するための Module および ModuleVersion クラスと共に、パイプラインで ModuleStep を作成するためのサポートが追加されました。
  + ACI Web サービスで、更新の間の永続的な scoring_uri がサポートされるようになりました。 scoring_uri が IP から FQDN に変更されます。 FQDN の DNS 名ラベルは、deploy_configuration で dns_name_label を設定することによって構成できます。 
  + 自動化された機械学習の新機能:
    + 予測用の STL フィーチャライザー
    + K-Means クラスタリングが機能のスイープに対して有効になります
  + AmlCompute クォータの承認が速くなりました。 しきい値内でのクォータの要求を承認するプロセスが自動化されました。 クォータの動作方法の詳細については、[クォータの管理方法](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas)に関する記事をご覧ください。

+ **プレビュー機能**
    + azureml-mlflow パッケージによる [MLflow](https://mlflow.org) 1.0.0 の追跡との統合 ([ノートブックの例](https://aka.ms/azureml-mlflow-examples))。
    + 実行として Jupyter Notebook を送信します。 [API リファレンス ドキュメント](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + azureml-contrib-datadrift パッケージによる [Data Drift Detector](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift?view=azure-ml-py) のパブリック プレビュー ([ノートブックの例](https://aka.ms/azureml-datadrift-example))。 データの誤差は、モデルの精度が時間の経過と共に低下する主な理由の 1 つです。 これは運用環境のモデルに提供されるデータが、モデルがトレーニングされたデータと異なる場合に発生します。 AML Data Drift Detector は、お客様がデータの誤差を監視し、誤差が検出されるたびにアラートを送信するのに役立ちます。 

+ **重大な変更**

+ **バグの修正と機能強化**
  + RunConfiguration の読み込みと保存では、以前の動作に対する完全な下位互換性を備えた完全なファイル パスの指定がサポートされます。
  + ServicePrincipalAuthentication でのキャッシュ機能が追加されました。既定ではオフになります。
  + 同じメトリック名で複数のプロットをログ記録できます。
  + Model クラスを、azureml.core (`from azureml.core import Model`) から正しくインポートできるようになりました。
  + パイプラインのステップで、`hash_path` パラメーターが非推奨になりました。 新しい動作では、.amlignore または .gitignore にリストされているファイルを除き、完全な source_directory がハッシュされます。
  + パイプラインのパッケージでは、さまざまな `get_all` および `get_all_*` メソッドが非推奨になり、それぞれ代わりに `list` および `list_*` が使用されます。
  + azureml.core.get_run では、元の実行型を返す前に、クラスをインポートする必要がなくなります。
  + WebService Update の一部の呼び出しで更新がトリガーされなかった問題を修正しました。
  + AKS Web サービスでのスコアリング タイムアウは、5 ミリ秒から 300000 ミリ秒の間である必要があります。 スコアリング要求に対して許容される最大 scoring_timeout_ms が、1 分から 5 分に引き上げられました。
  + LocalWebservice オブジェクトに、`scoring_uri` プロパティと `swagger_uri` プロパティが追加されました。
  + 出力ディレクトリの作成と出力ディレクトリのアップロードが、ユーザー プロセスの外に移動されました。 すべてのユーザー プロセス内で、実行履歴 SDK を実行できるようになりました。 これにより、分散トレーニングの実行で発生していたいくつかの同期の問題が解決されるはずです。
  + ユーザーのプロセス名から書き込まれる azureml ログの名前に、プロセス名 (分散トレーニングの場合のみ) と PID が含まれるようになりました。

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>Azure Machine Learning Data Prep SDK v1.1.5

+ **バグの修正と機能強化**
  + 2 桁の年形式を使用する解釈された datetime 値では、有効な年の範囲が Windows May リリースと一致するように更新されました。 範囲が、1930 - 2029 から 1950 - 2049 に変更されました。
  + ファイルおよび設定 `handleQuotedLineBreaks=True` を読み取るときに、`\r` は改行として扱われます。
  + 場合によって `read_pandas_dataframe` が失敗する原因であったバグが修正されました。
  + `get_profile` のパフォーマンスが向上しました。
  + エラー メッセージが改善されました。

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>Azure Machine Learning Data Prep SDK v1.1.4

+ **新機能**
  + 次の式言語関数を使用して、datetime 値を新しい列に抽出して解析できるようになりました。
    + `RegEx.extract_record()` は datetime 要素を新しい列に抽出します。
    + `create_datetime()` は別の datetime 要素から datetime オブジェクトを作成します。
  + `get_profile()` を呼び出すとき、分位点の列に (est.) というラベルが付けられ、値が近似値であることを明確に判別できるようになりました。
  + Azure Blob Storage からの読み取り時に ** グロビングを使用できるようになりました。
    + 例: `dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **バグの修正**
  + リモート ソース (Azure Blob) からの Parquet ファイルの読み取りに関連するバグを修正しました。

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>Azure Machine Learning SDK for Python v1.0.39
+ **変更点**
  + 実行構成の auto_prepare_environment オプションは非推奨になり、自動準備が既定になります。

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>Azure Machine Learning Data Prep SDK v1.1.3

+ **新機能**
  + PostgresSQL データベースから読み取るためのサポートが追加されました。read_postgresql を呼び出すか、データストアを使用します。
    + ハウツー ガイドの例を参照してください。
      + [データ インジェスト ノートブック](https://aka.ms/aml-data-prep-ingestion-nb)
      + [データストア ノートブック](https://aka.ms/aml-data-prep-datastore-nb)

+ **バグの修正と機能強化**
  + 列の型変換の問題が修正されました。
  + ブール値または数値の列がブール値の列に正しく変換されるようになりました。
  + 日付列に日付型を設定しようとしたときに失敗することはなくなりました。
  + JoinType 型と付属のドキュメントが改善されました。 2 つのデータフローを結合するときに、次の結合型のいずれかを指定できるようになりました。
    + NONE、MATCH、INNER、UNMATCHLEFT、LEFTANTI、LEFTOUTER、UNMATCHRIGHT、RIGHTANTI、RIGHTOUTER、FULLANTI、FULL
  + より多くの日付の形式を認識するようにデータ型の推論が改善されました。

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Azure ポータル

Azure portal で、次のことが可能になりました。
+ 自動化された ML の実験を作成して実行する 
+ Notebook VM を作成して、サンプルの Jupyter ノートブック、または独自のノートブックを試す。
+ Machine Learning service ワークスペースのまったく新しい作成セクション (プレビュー)。自動化された機械学習、ビジュアル インターフェイス、ホストされている Notebook VM が含まれます
    + 自動化された機械学習を使用して自動的にモデルを作成する 
    + ドラッグ アンド ドロップのビジュアル インターフェイスを使用して実験を実行する
    + Notebook VM を作成してデータの探索、モデルの作成、サービスのデプロイを行う。
+ 実行レポートと実行詳細ページでのライブ グラフとメトリックの更新
+ 実行詳細ページでのログ、出力、スナップショットのファイル ビューアーの更新。
+ 新しくなった、または改善された [実験] タブのレポート作成エクスペリエンス。 
+ Azure Machine Learning service ワークスペースの [概要] ページから config.json ファイルをダウンロードする機能の追加。
+ Azure Databricks ワークスペースからの Machine Learning service ワークスペース作成のサポート 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>Azure Machine Learning SDK for Python v1.0.33
+ **新機能**
  + _Workspace.create_ メソッドは、CPU および GPU クラスターの既定のクラスター構成を受け入れるようになりました。
  + ワークスペースの作成に失敗する場合、依存リソースはクリーンアップされます。
  + 既定の Azure Container Registry SKU が Basic に切り替えられました。
  + Azure Container Registry は、実行またはイメージの作成に必要なときに遅延作成されます。
  + トレーニングの実行に対する環境をサポートします。

### <a name="notebook-virtual-machine"></a>Notebook Virtual Machine 

Notebook VM は Jupyter ノートブック向けのセキュリティで保護された、エンタープライズ対応のホスティング環境で使用します。ここで、機械学習の実験をプログラミングしたり、モデルを Web エンドポイントとしてデプロイしたり、Python を使用して Azure Machine Learning SDK でサポートされる他のすべての操作を実行したりすることができます。 次のような機能が提供されます。
+ 最新バージョンの Azure Machine Learning SDK と関連パッケージが含まれる、[構成済みのノートブック VM の迅速な起動](quickstart-run-cloud-notebook.md) 。
+ アクセスは、HTTPS、Azure Active Directory の認証と認可など、実績のあるテクノロジによってセキュリティ保護されます。
+ Azure Machine Learning ワークスペースの BLOB ストレージ アカウント内にある、ノートブックとコードの信頼性の高いクラウド ストレージ。 作業内容を失わずに、ノートブックの VM を安全に削除できます。
+ Azure Machine Learning service の機能を使用して探索と実験を行うためにプレインストールされたサンプル ノートブック。
+ Azure VM、すべての種類の VM、すべてのパッケージ、すべてのドライバーの完全なカスタマイズ機能。 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning SDK for Python v1.0.33 がリリースされました。

+ [FPGA](concept-accelerate-with-fpgas.md) で Azure ML Hardware Accelerated Models の一般提供が開始されました。
  + [azureml-accel-models パッケージ](how-to-deploy-fpga-web-service.md)を使用して次のことができるようになりました。
    + サポートされているディープ ニューラル ネットワーク (ResNet 50、ResNet 152、DenseNet-121、VGG-16、および SSD-VGG) の重み付けトレーニング
    + サポートされている DNN を使用した転移学習の使用
    + モデル管理サービスへのモデルの登録と、モデルのコンテナー化
    + Azure Kubernetes Service (AKS) クラスターの FPGA を使用した Azure VM へのモデルのデプロイ
  + [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview) サーバー デバイスへのコンテナーのデプロイ
  + この[サンプル](https://github.com/Azure-Samples/aml-hardware-accelerated-models)を使用した gRPC エンドポイントでのデータのスコア付け

### <a name="automated-machine-learning"></a>自動化された機械学習

+ パフォーマンスの最適化のため、機能を一掃し、動的に追加されるフィーチャライザーを有効化。 新しいフィーチャライザー: 作業の埋め込み、証拠の重み付け、ターゲット エンコード、テキスト ターゲット エンコード、クラスターの距離
+ 自動化された ML 内でトレーニング/有効な分割を処理するスマート CV
+ わずかなメモリ最適化の変更とランタイムのパフォーマンス向上
+ モデルの説明でのパフォーマンス向上
+ ローカル実行用 ONNX モデルの変換
+ サブサンプリング サポートの追加
+ 終了条件が定義されていない場合のインテリジェントな停止
+ スタッキング アンサンブル

+ 時系列予測
  + 新しい予測関数   
  + 時系列データに対してローリング オリジンのクロス検証を使用できるようになりました
  + 時系列の遅れを構成する新機能の追加 
  + ローリング ウィンドウの集計機能をサポートする新機能の追加
  + 実験の設定で国番号が定義されている場合の、新しい祝日の検出とフィーチャライザー

+ Azure Databricks
  + 時系列予測と、モデルの説明可能性/解釈可能性機能を有効化しました
  + 自動化された ML の実験の取り消しと再開 (続行) ができるようになりました
  + マルチコア処理のサポートを追加しました

### <a name="mlops"></a>MLOps
+ **スコア付けコンテナーのローカル デプロイとデバッグ**<br/> ML モデルをローカルにデプロイして、スコアリング ファイルと依存関係上ですばやく反復できるようになったため、これらが確実に予期したとおりに動作するようになりました。

+ **InferenceConfig と Model.deploy() の導入**<br/> モデル デプロイで、エントリ スクリプトが含まれるソース フォルダーの指定がサポートされるようになりました (RunConfig と同様)。  また、モデル デプロイが 1 つのコマンドに簡素化されました。

+ **Git 参照の追跡**<br/> お客様から、エンドツーエンドの監査証跡の維持に役立つ、基本的な Git 統合機能についてのご希望をいただいてきました。 Microsoft は Azure ML の主要エンティティに対する Git 関連メタデータ (リポジトリ、コミット、クリーンな状態) の追跡を実装しました。 この情報は、SDK と CLI によって自動的に収集されます。

+ **モデルのプロファイルと検証のサービス**<br/> お客様からよくいただくご不満のなかに、推論サービスに対応付けるコンピューティングの適切なサイズ設定が難しいというものがありました。 Microsoft のモデル プロファイル サービスではユーザーがサンプル入力を指定できるため、16 種類の CPU/メモリ設定をプロファイルして、デプロイに最適なサイズを決定できます。

+ **推論に独自の基本イメージを使用可能**<br/> もう 1 つ多くいただいたご不満は、実験から推論の依存関係の再共有に移行することが難しいというものでした。 基本イメージの新しい共有機能により、実験の基本イメージ、依存関係などすべてを推論に再利用できるようになりました。 これにより、デプロイが高速化し、内側から外側のループへのギャップが少なくなります。

+ **Swagger スキーマの生成エクスペリエンスの向上**<br/> 以前の Swagger 生成メソッドではエラーが発生しやすく、自動化ができませんでした。 デコレーターを使用してどの Python 関数からでも Swagger スキーマをインラインで生成できるようになりました。 Microsoft はこのコードをオープンソース化しました。このスキーマ生成プロトコルは Azure ML プラットフォームと結合されていません。

+ **Azure ML CLI が一般公開 (GA)**<br/> 1 つの CLI コマンドでモデルをデプロイできるようになりました。 ユーザーから、Jupyter ノートブックから誰も ML モデルをデプロイしていないという共通したフィードバックを受け取っています。 [**CLI リファレンス ドキュメント**](https://aka.ms/azmlcli)が更新されました。


## <a name="2019-04-22"></a>2019-04-22

Azure Machine Learning SDK for Python v1.0.30 がリリースされました。

同じエンドポイントを維持しながら公開されたパイプラインの新しいバージョンを追加するための [`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py) が導入されました。

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Azure Machine Learning Data Prep SDK v1.1.2

注:Data Prep Python SDK では、`numpy` および `pandas` パッケージがインストールされなくなります。 [更新されたインストール手順](https://aka.ms/aml-data-prep-installation)に関するページを参照してください。

+ **新機能**
  + ピボット変換を使用できるようになりました。
    + 攻略ガイド: [ピボット ノートブック](https://aka.ms/aml-data-prep-pivot-nb)
  + ネイティブ関数内に正規表現を使用できるようになりました。
    + 次に例を示します。
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + 式言語内で `to_upper`  関数と `to_lower`  関数を使用できるようになりました。
  + データ プロファイル内に各列の一意の値が表示されるようになりました。
  + 一般的に使用されるリーダー手順の一部で、`infer_column_types` 引数を渡せるようになりました。 それが `True` に設定されている場合、Data Prep では列の型の検出およびその自動変換が試みられます。
    + `inference_arguments` は非推奨となりました。
  + `Dataflow.shape` を呼び出せるようになりました。

+ **バグの修正と機能強化**
  + 省略可能な追加の引数 `validate_column_exists` が `keep_columns`  によって受け入れられるようになりました。それが渡されると、`keep_columns` の結果に任意の列が含まれているかどうかが確認されます。
  + すべてのリーダー手順 (ファイルから読み取られる) で、省略可能な追加の引数 `verify_exists` が受け入れられるようになりました。
  + Pandas データ フレームからの読み取り、およびデータ プロファイルの取得のパフォーマンスが改善されました。
  + 単一インデックスの場合にデータ フローからの単一手順のスライシングが失敗するというバグが修正されました。

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure ポータル
  + 既存のリモート コンピューティング クラスターで実行されている既存のスクリプトを再送信できるようになりました。 
  + [パイプライン] タブで、新しいパラメーターで発行されたパイプラインを実行できるようになりました。 
  + 詳細の実行で、新しいスナップショット ファイル ビューアーがサポートされるようになりました。 特定の実行を送信したときのディレクトリのスナップショットを表示することができます。 また、実行を開始するために送信されたノートブックをダウンロードすることもできます。
  + Azure portal からの親の実行をキャンセルできるようになりました。

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK for Python v1.0.23

+ **新機能**
  + Azure Machine Learning SDK で Python 3.7 がサポートされるようになりました。
  + Azure Machine Learning DNN Estimator で、組み込みマルチバージョン サポートが提供されるようになりました。 たとえば、`TensorFlow`  Estimator は `framework_version` パラメーターを受け入れるようになり、ユーザーはバージョン '1.10' や '1.12' を指定することができます。 現在の SDK リリースでサポートされているバージョンの一覧については、目的のフレームワーク クラスで `get_supported_versions()` を呼び出します (例: `TensorFlow.get_supported_versions()`)。
  最新の SDK リリースでサポートされているバージョンの一覧については、[DNN Estimator のドキュメント](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py)を参照してください。

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Azure Machine Learning Data Prep SDK v1.1.1

+ **新機能**
  + read_* transforms を使用して複数の Datastore/DataPath/DataReference ソースを読み取ることができます。
  + 列で次の演算を実行して新しい列を作成することができます: division、floor、modulo、power、length。
  + Data Prep が Azure ML 診断スイートの一部になり、既定で診断情報がログに記録されるようになりました。
    + これをオフにするには、次の環境変数を true に設定します:DISABLE_DPREP_LOGGER

+ **バグの修正と機能強化**
  + 一般的に使用されるクラスと関数のコード ドキュメントが改善されました。
  + Excel ファイルの読み取りに失敗した auto_read_file のバグが修正されました。
  + read_pandas_dataframe のフォルダーを上書きするオプションが追加されました。
  + dotnetcore2 依存関係のインストールのパフォーマンスが改善され、Fedora 27/28 と Ubuntu 1804 のサポートが追加されました。
  + Azure Blob からの読み取りのパフォーマンスが改善されました。
  + 列の型の検出で、Long 型の列がサポートされるようになりました。
  + 一部の日付値が Python の datetime オブジェクトではなく timestamp として表示されていたバグが修正されました。
  + 一部の型カウントが integer ではなく double として表示されていたバグを修正しました。

  
## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK for Python v1.0.21

+ **新機能**
  + *azureml.core.Run.create_children* メソッドにより、1 回の呼び出しで複数の子の実行を少ない待機時間で作成することができます。

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Azure Machine Learning Data Prep SDK v1.1.0

+ **重大な変更**
  + Data Prep パッケージの概念は非推奨になり、サポートされなくなりました。 1 つのパッケージで複数のデータフローを保持する代わりに、データフローを個別に保持することができます。
    + 攻略ガイド: [データフローのノートブックの開始と保存](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **新機能**
  + Data Prep で特定のセマンティックの種類と一致する列を認識して、適切に分割できるようになりました。 現在サポートされているセマンティックの種類には、メール アドレス、地理的座標 (緯度と経度)、IPv4 と IPv6 のアドレス、米国の電話番号、米国の郵便番号などがあります。
    + 攻略ガイド: [セマンティックの種類のノートブック](https://aka.ms/aml-data-prep-semantic-types-nb)
  + Data Prep で、2 つの数値列から結果列を生成する減算、乗算、除算、剰余の操作をサポートするようになりました。
  + データフローで `verify_has_data()` を呼び出し、データフローが実行された場合にレコードが生成されるかどうかをチェックすることができます。

+ **バグの修正と機能強化**
  + 数値列のプロファイルで、ヒストグラムに使用するビンの数を指定できるようになりました。
  + `read_pandas_dataframe` 変換で、データフレームに文字列型またはバイト型の列名が求められるようになりました。
  + `fill_nulls` 変換の列が入力されていない場合に値が正しく入力されないバグを修正しました。

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK for Python v1.0.18

 + **変更点**
   + azureml-contrib-tensorboard が azureml-tensorboard パッケージに置き換えられました。
   + このリリースでは、マネージド コンピューティング クラスター (amlcompute) の作成時にユーザー アカウントを設定できます。 これは、これらのプロパティをプロビジョニング構成に渡すことで実行できます。 詳細については、[SDK リファレンス ドキュメント](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none-)をご覧ください。

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning Data Prep SDK v1.0.17

+ **新機能**
  + 式言語を使用して結果列を生成するために、2 つの数値列の追加がサポートされるようになりました。

+ **バグの修正と機能強化**
  + random_split のドキュメントとパラメーター チェックが改善されました。
  
## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning Data Prep SDK v1.0.16

+ **バグの修正**
  + API の変更が原因で発生していたサービス プリンシパルの認証の問題を修正しました。

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK for Python v1.0.17

+ **新機能**

  + Azure Machine Learning で、一般的な DNN フレームワーク Chainer のファースト クラスのサポートが提供されるようになりました。 [`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) クラスを使用すると、Chainer モデルを簡単にトレーニングしてデプロイできます。
    + [ChainerMN を使用して分散トレーニングを実行する](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)方法をご覧ください。
    + [HyperDrive を使用して Chainer でハイパーパラメーター調整を実行する](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)方法をご覧ください。
  + Azure Machine Learning パイプラインに、データストアの変更に基づいてパイプライン実行をトリガーする機能が追加されました。 この機能を紹介するために、パイプラインの[スケジュール ノートブック](https://aka.ms/pl-schedule)が更新されました。

+ **バグの修正と機能強化**
  + [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) に提供される [RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) で、source_directory_data_store プロパティを必要なデータストア (Blob Storage など) に設定するために、Azure Machine Learning パイプラインのサポートが追加されました。 既定では、各ステップでバッキング データストアとして Azure File ストアが使用されますが、多数のステップが同時に実行されると、調整の問題が発生する可能性があります。

### <a name="azure-portal"></a>Azure ポータル

+ **新機能**
  + テーブル エディターでレポートを作成する際の新しいドラッグ アンド ドロップ エクスペリエンス。 ユーザーは、ウェルから、テーブルのプレビューが表示されるテーブル領域に列をドラッグできます。 列を並べ替えることができます。
  + 新しいログ ファイル ビューアー
  + アクティビティ タブから実験の実行、計算、モデル、イメージ、デプロイへのリンク

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Azure Machine Learning Data Prep SDK v1.0.15

+ **新機能**
  + Data Prep で、データフローからのファイル ストリームの書き込みがサポートされるようになりました。 また、ファイル ストリーム名を操作して新しいファイル名を作成する機能も用意されています。
    + 攻略ガイド: [File Streams ノートブックの操作](https://aka.ms/aml-data-prep-file-stream-nb)

+ **バグの修正と機能強化**
  + 大規模なデータセットでの T-Digest のパフォーマンスが向上しました。
  + Data Prep で、DataPath からのデータの読み取りがサポートされるようになりました。
  + ブール値列と数値列でワン ホット エンコードが機能するようになりました。
  + その他の各種バグ修正。

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>Azure Machine Learning SDK for Python v1.0.15

+ **新機能**
  + Azure Machine Learning パイプラインに、AzureBatchStep ([ノートブック](https://aka.ms/pl-azbatch))、HyperDriveStep (ノートブック)、時間ベースのスケジューリング機能 ([ノートブック](https://aka.ms/pl-schedule)) が追加されました。
  +  Azure SQL Server および PostgreSQL 用の Azure データベース ([ノートブック](https://aka.ms/pl-data-trans)) で機能するように DataTranferStep が更新されました。

+ **変更点**
  + `PublishedPipeline.get` を優先して、`PublishedPipeline.get_published_pipeline` を非推奨にしました。
  + `Schedule.get` を優先して、`Schedule.get_schedule` を非推奨にしました。

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Azure Machine Learning Data Prep SDK v1.0.12

+ **新機能**
  + Data Prep で、データ ストアを使用した Azure SQL データベースからの読み取りがサポートされるようになりました。
 
+ **変更点**
  + 大きなデータに対する特定の操作のメモリ パフォーマンスが向上しました。
  + `read_pandas_dataframe()` では `temp_folder` を指定することが必要になりました。
  + `ColumnProfile` での `name` プロパティは非推奨です。代わりに `column_name` を使用してください。

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Azure Machine Learning SDK for Python v1.0.10

+ **変更点**: 
  + Azure ML SDK では、依存関係としての azure-cli パッケージは不要になりました。 具体的には、azureml-core から azure-cli-core 依存関係と azure-cli-profile 依存関係が削除されました。 ユーザーに影響がある変更を以下に示します。
    + "az login" を実行し、その後 azureml-sdk を使用すると、この SDK はブラウザーまたはデバイス コードのログインをもう一度行います。 "az login" によって作成された資格情報の状態は使用されません。
    + "az login" を使用するなどの Azure CLI 認証の場合、_azureml.core.authentication.AzureCliAuthentication_ クラスを使用します。 Azure CLI 認証では、azureml-sdk をインストールした Python 環境で _pip install azure-cli_ を実行します。
    + 自動化のためのサービス プリンシパルを使用して "az login" を実行する場合、azureml-sdk では、azure CLI によって作成された資格情報の状態が使用されないため、_azureml.core.authentication.ServicePrincipalAuthentication_ クラスを使用することをお勧めします。 

+ **バグの修正**:このリリースには主に、軽微なバグの修正が含まれます

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Azure Machine Learning Data Prep SDK v1.0.8

+ **バグの修正**
  + データ プロファイルの取得におけるパフォーマンスが向上しました。
  + エラー報告に関連する軽微なバグが修正されました。
  
### <a name="azure-portal-new-features"></a>Azure portal: 新機能
+ レポート作成のためのドラッグ アンド ドロップによる新しいグラフ作成エクスペリエンス。 ユーザーは、列または属性を元の場所からグラフ領域にドラッグできます。ドラッグすると、データの種類に基づき、ユーザーに対して適切なグラフの種類が自動的に選択されます。 ユーザーは、グラフの種類を別の適用可能な種類に変更することや、属性をさらに追加することができます。

    サポートされるグラフの種類:
    - 折れ線グラフ
    - ヒストグラム
    - 積み上げ横棒グラフ
    - 箱ひげ図
    - 散布図
    - バブル プロット
+ ポータルでは、実験のレポートが動的に生成されるようになりました。 ユーザーが実験に対して実行を発行すると、ログに記録されたメトリックとグラフを含むレポートが自動的に生成され、異なる実行を比較できます。 

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Azure Machine Learning SDK for Python v1.0.8

+ **バグの修正**:このリリースには主に、軽微なバグの修正が含まれます

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Azure Machine Learning Data Prep SDK v1.0.7

+ **新機能**
  + データストアの機能強化 ([データストアのハウツーガイド](https://aka.ms/aml-data-prep-datastore-nb)参照)
    + スケールアップ時に Azure ファイル共有と ADLS データストアに対する読み取りと書き込みを行う機能が追加されました。
    + データストアの使用時に、データの準備で対話型認証ではなくサービス プリンシパル認証の使用がサポートされるようになりました。
    + WASB URL と WASBS URL のサポートが追加されました。

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Azure Machine Learning Data Prep SDK v1.0.6

+ **バグの修正**
  + Spark 上のパブリックに読み取り可能な Azure BLOB コンテナーからの読み取りでのバグを修正しました

## <a name="2018-12-20"></a>2018-12-20 

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Azure Machine Learning SDK for Python v1.0.6
+ **バグの修正**:このリリースには主に、軽微なバグの修正が含まれます

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Azure Machine Learning Data Prep SDK v1.0.4

+ **新機能**
  + `to_bool` 関数では、一致しない値を Error 値に変換できるようになりました。 これは、`to_bool` および `set_column_types` の新しい既定の不一致動作です。一方、以前の既定の動作では一致しない値を False に変換していました。
  + `to_pandas_dataframe` を呼び出すときに、数値列の null 値/欠落値を NaN と解釈する新しいオプションがあります。
  + 一部の式の戻り値の型をチェックして、型の一貫性と失敗を早期に確認する機能が追加されました。
  + `parse_json` を呼び出して、列内の値を JSON オブジェクトとして解析し、それらを複数の列に拡張できるようになりました。

+ **バグの修正**
  + Python 3.5.2 で `set_column_types` をクラッシュしていたバグを修正しました。
  + AML イメージを使用してデータストアに接続するときにクラッシュしていたバグを修正しました。

+ **更新プログラム**
  * 使用開始チュートリアル、ケース スタディ、および攻略ガイドの[サンプルの Notebook](https://aka.ms/aml-data-prep-notebooks)。

## <a name="2018-12-04-general-availability"></a>2018-12-04: 一般公開

Azure Machine Learning service の一般提供が開始されました。

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning コンピューティング
このリリースでは、[Azure Machine Learning コンピューティング](how-to-set-up-training-targets.md#amlcompute)を通じて、新しいマネージド コンピューティング エクスペリエンスを発表します。 このコンピューティング ターゲットは、Azure Machine Learning の Azure Batch AI コンピューティングの後継となります。 

このコンピューティング ターゲットは、
+ モデル トレーニングとバッチ推論に使用されます
+ シングルノードからマルチノードのコンピューティングです
+ クラスター管理とジョブ スケジューリングをユーザーに代わって実行します
+ 既定で自動スケーリングを実行します
+ CPU と GPU の両方のリソースをサポートします 
+ コストを抑えるために低優先度の VM を使用できるようにします

Azure Machine Learning コンピューティングは、Python、Azure portal、または CLI を使用して作成できます。 お客様のワークスペースのリージョンに作成する必要があり、他のワークスペースにアタッチすることはできません。 このコンピューティング ターゲットでは、お客様の実行に Docker コンテナーを使用しており、お客様の依存関係をパッケージ化することですべてのノードで同じ環境を再現します。

> [!Warning]
> Azure Machine Learning コンピューティングを使用するには、新しいワークスペースを作成することをお勧めします。 ユーザーが Azure Machine Learning コンピューティングを既存のワークスペースで作成しようとしてエラーが発生することはまずありません。 お客様のワークスペースにある既存のコンピューティングは、引き続き支障なく動作します。

### <a name="azure-machine-learning-sdk-for-python-v102"></a>Azure Machine Learning SDK for Python v1.0.2
+ **重大な変更**
  + このリリースでは、Azure Machine Learning で VM の作成のサポートが廃止されます。 引き続き、既存のクラウド VM またはリモートのオンプレミス サーバーをアタッチすることができます。 
  + また、Batch AI のサポートも廃止となります。そのすべては今後、Azure Machine Learning コンピューティングを通じてサポートされます。

+ **[新規]**
  + 機械学習パイプライン:
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **更新**
  + 機械学習パイプライン:
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) で runconfig が使用可能になりました
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) で SQL データソースとの間のコピーが可能になりました
    + パブリッシュ済みのパイプラインの実行に関するスケジュールを作成および更新できる SDK のスケジュール機能

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Azure Machine Learning Data Prep SDK v0.5.2
+ **重大な変更** 
  * `SummaryFunction.N` の名前が `SummaryFunction.Count` に変更されました。
  
+ **バグの修正**
  * リモート実行でデータストアに対する読み書きを行う際、最新の AML Run Token を使用します。 以前は、AML Run Token が Python で更新された場合、Data Prep ランタイムが最新の AML Run Token によって更新されませんでした。
  * より明確なエラー メッセージの追加
  * Spark によって `Kryo` シリアル化が使用される際に to_spark_dataframe() がクラッシュすることがなくなりました
  * 値のカウント インスペクターで表示できる一意の値が 1,000 件を超えました
  * ランダム分割が、元の Dataflow に名前がない場合に失敗しなくなりました  

+ **詳細情報**
  * [Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>ドキュメントとノートブック
+ ML パイプライン
  + パイプラインの概要、バッチ スコーピング、スタイル転送の例についての新しいノートブックと更新されたノートブック: https://aka.ms/aml-pipeline-notebooks
  + [最初のパイプラインを作成する](how-to-create-your-first-pipeline.md)方法について
  + [パイプラインを使用してバッチ予測を実行](how-to-run-batch-predictions.md)する方法について
+ Azure Machine Learning コンピューティング ターゲット
  + 新しいマネージド コンピューティングを使用するように[サンプル ノートブック](https://aka.ms/aml-notebooks)が更新されました。
  + [このコンピューティングについて](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Azure portal: 新機能
+ ポータルで [Azure Machine Learning コンピューティング](how-to-set-up-training-targets.md#amlcompute) タイプを作成して管理します。
+ Azure Machine Learning コンピューティングのクォータ使用率を監視し、[クォータを要求](how-to-manage-quotas.md)します。
+ Azure Machine Learning コンピューティング クラスターの状態をリアルタイムで表示します。
+ Azure Machine Learning コンピューティングと Azure Kubernetes Service の作成用に仮想ネットワークのサポートが追加されました。
+ 既存のパラメーターを使用してお客様のパブリッシュ済みのパイプラインを再実行します。
+ 分類モデル (リフト、ゲイン、キャリブレーション、モデルの説明可能性を備えた特徴重要度グラフ) と回帰モデル (残差、およびモデルの説明可能性を備えた特徴重要度グラフ) のための新しい[自動化された機械学習グラフ](how-to-track-experiments.md#auto)。 
+ パイプラインを Azure portal で表示できます




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Azure Machine Learning SDK for Python v0.1.80

+ **重大な変更** 
  * *azureml.train.widgets* 名前空間は、*azureml.widgets* に移動されました。
  * *azureml.core.compute.AmlCompute* では、*azureml.core.compute.BatchAICompute* クラスと *azureml.core.compute.DSVMCompute* クラスが非推奨になりました。 前者のクラスは、今後のリリースで削除されます。 現在、AmlCompute クラスには以前の定義があり、単に vm_size と max_nodes が必要で、ジョブが送信されたときにご利用のクラスターは 0 から max_nodes に自動的にスケーリングされます。 [サンプル ノートブック](https://github.com/Azure/MachineLearningNotebooks/tree/master/training)は、この情報で更新されており、使用方法の例が提供されます。 この単純化と今後のリリースで登場するより魅力あふれる多くの機能にご満足いただければ幸いです。

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Azure Machine Learning Data Prep SDK v0.5.1 

Data Prep SDK の詳細については、[リファレンス ドキュメント](https://aka.ms/data-prep-sdk)を参照してください。
+ **新機能**
   * DataPrep パッケージを実行して、データセットまたはデータフローに対するデータ プロファイルを表示するために、新しい DataPrep CLI が作成されました
   * 使いやすさを向上させるために SetColumnType API が再設計されました
   * smart_read_file から auto_read_file に名前が変更されました
   * データ プロファイルに傾斜と尖度が含まれるようになりました
   * 層化抽出法でサンプリングすることができます
   * CSV ファイルを含む zip ファイルからの読み取りが可能になりました
   * ランダム分割でデータセットを行型に分割できます (例: test-train セットに分割)
   * `.dtypes` を呼び出すことでデータフローまたはデータ プロファイルから列のデータ型をすべて取得できます
   * `.row_count` を呼び出すことでデータフローまたはデータ プロファイルから行数を取得できます

+ **バグの修正**
   * 長整数型から倍精度浮動小数点型の変換が修正されました 
   * 列の追加後のアサートが修正されました 
   * 場合によってはグループが検出されない FuzzyGrouping での問題が修正されました
   * 複数列の並べ替え順を優先する並べ替え関数が修正されました
   * `pandas` で処理される方法と同様になるように AND/OR 式が修正されました
   * dbfs パスからの読み取りが修正されました
   * エラー メッセージがわかりやすくなりました 
   * AML トークンを使用してリモート コンピューティング先で読み取るときに、失敗しないようになりました
   * Linux DSVM 上で失敗しないようになりました
   * 文字列以外の値が文字列の述語にある場合にクラッシュしなくなりました
   * Dataflow が正常に失敗する必要があるときに、アサーション エラーが処理されるようになりました
   * Azure Databricks 上に保存場所をマウントした dbutils がサポートされるようになりました

## <a name="2018-11-05"></a>2018-11-05

### <a name="azure-portal"></a>Azure ポータル 
Azure Machine Learning サービスの Azure portal では、次の更新が加えられました。
  * パブリッシュされたパイプライン用の新しい **[パイプライン]** タブ。
  * 既存の HDInsight クラスターをコンピューティング ターゲットとしてアタッチする操作が新たにサポートされました。

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Azure Machine Learning SDK for Python v0.1.74

+ **重大な変更** 
  * \* Workspace.compute_targets、datastores、experiments、images、models、および *webservices* が、メソッドではなく、プロパティになりました。 たとえば、*Workspace.compute_targets()* は、*Workspace.compute_targets* に置き換えます。
  * *Run.get_submitted_run* が廃止され、*Run.get_context* に置き換えられました。 前者のメソッドは、今後のリリースで削除されます。
  * *PipelineData* クラスは、データストア オブジェクトを datastore_name ではなく、パラメーターとして受け付けるようになりました。 同様に、*パイプライン*は default_datastore_name ではなく default_datastore を受け入れます。

+ **新機能**
  * Azure Machine Learning Pipelines の[サンプル ノートブック](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb)で、MPI の手順が使われるようになりました。
  * Jupyter ノートブック用の RunDetails ウィジェットが更新され、パイプラインの視覚化が表示されるようになりました。

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>Azure Machine Learning Data Prep SDK v0.4.0 
 
+ **新機能**
  * データ プロファイルにタイプ カウントが追加されました 
  * 値のカウントとヒストグラムが使用可能になりました
  * データ プロファイル内のパーセンタイルが追加されました
  * 集計で中央値が使用可能になりました
  * Python 3.7 が新たにサポートされました
  * データストアを含んだデータ フローを DataPrep パッケージに保存すると、データ ストア情報が DataPrep パッケージの一部としてを永続化されます
  * データ ストアへの書き込みが新たにサポートされました 
        
+ **バグ修正**
  * 64 ビット符号なし整数のオーバーフローが Linux で正しく処理されるようになりました
  * smart_read でのプレーン テキスト ファイルの不適切なテキスト ラベルが修正されました
  * 文字列の列タイプがメトリック ビューで表示されるようになりました
  * タイプ カウントが修正され、各種ではなく、1 つの FieldType にマップされた ValueKinds が表示されるようになりました
  * Write_to_csv で、パスが文字列として指定されている場合に処理が失敗しないようになりました
  * Replace を使用する際に、"find" を空白のままにしても処理が失敗しないようになりました 

## <a name="2018-10-12"></a>2018 年 10 月 12 日

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Azure Machine Learning SDK for Python v0.1.68

+ **新機能**
  * 新しいワークスペースの作成時、マルチ テナントがサポートされます。

+ **修正済みのバグ**
  * Web サービスをデプロイするときに、pynacl ライブラリのバージョンをピン留めする必要がなくなりました。

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>Azure Machine Learning Data Prep SDK v0.3.0

+ **新機能**
  * メソッド transform_partition_with_file(script_path) が追加されました。このメソッドにより、実行する Python ファイルのパスを渡すことができます。

## <a name="2018-10-01"></a>2018 年 10 月 1 日

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>Azure Machine Learning SDK for Python v0.1.65
[バージョン 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65) には、いくつかの新機能、追加のドキュメント、バグ修正、および追加の[サンプル ノートブック](https://aka.ms/aml-notebooks)が同梱されています。

バグおよび対処法については、[既知の問題のリスト](resource-known-issues.md)を参照してください。

+ **重大な変更**
  * Workspace.experiments、Workspace.models、Workspace.compute_targets、Workspace.images、Workspace.web_services 返却辞書、以前返却されたリスト。 [azureml.core.Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) API ドキュメントを参照してください。

  * 自動化された機械学習で、正規化平均二乗誤差がプライマリ メトリックから削除されました。

+ **HyperDrive**
  * ベイジアンに関する HyperDrive のさまざまなバグが修正されました。また、メトリックを取得する呼び出しのパフォーマンスが改善されました。 
  * Tensorflow が 1.9 から 1.10 にアップグレードされました。 
  * Docker イメージがコールド スタート用に最適化されました。 
  * 終了時に 0 以外のエラー コードが発生した場合でも、ジョブの状態が正確に報告されます。 
  * SDK で RunConfig 属性が検証されます。 
  * HyperDrive 実行オブジェクトで、通常の実行と同様のキャンセルがサポートされます。パラメーターの受け渡しは不要です。 
  * 分散実行および HyperDrive 実行でドロップダウンの値の状態が維持されるようにウィジェットが改善されました。 
  * TensorBoard およびその他のログ ファイルのサポートが、パラメーター サーバー用に修正されました。 
  * サービス側で Intel(R) MPI がサポートされます。 
  * BatchAI での検証時における、分散実行修正用のパラメーター調整のバグが修正されました。 
  * コンテキスト マネージャーがプライマリ インスタンスを識別するようになりました。 

+ **Azure portal での操作**
  * [Run details]\(実行の詳細\) で log_table() と log_row() がサポートされます。 
  * 1、2、または 3 個の数値列、および 1 個のカテゴリ列 (オプション) を含むテーブルおよび行のグラフが自動的に作成されます。

+ **自動化された機械学習**
  * エラー処理とドキュメントが改善されました。 
  * 実行プロパティ取得のパフォーマンスの問題が修正されました。 
  * 実行の継続に関する問題が修正されました。 
  * 繰り返しのアンサンブルに関する問題が修正されました。
  * MAC OS 上でトレーニングがハングするバグが修正されました。
  * カスタム検証シナリオで、マクロ平均 PR/ROC 曲線がダウンサンプリングされます。
  * 余分なインデックス ロジックが削除されました。
  * get_output API からフィルターが削除されました。

+ **パイプライン**
  * パイプラインを直接バブリッシュするメソッド Pipeline.publish() が追加され、最初の実行が不要になりました。   
  * パブリッシュ済みのパイプラインから生成されたパイプライン実行をフェッチするメソッド PipelineRun.get_pipeline_runs() が追加されました。

+ **Project Brainwave**
  * FPGA 上で使用可能な新しい AI モデルのサポートが更新されました。

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Azure Machine Learning Data Prep SDK v0.2.0
[バージョン 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) には、以下の機能とバグ修正が組み込まれています。

+ **新機能**
  * ワンホット エンコードがサポートされます。
  * 分位変換がサポートされます。
   
+ **バグ修正:**
  * どのバージョンの Tornado でも機能します。Tornado のバージョンをダウングレードする必要はありません。
  * 上位 3 つの値だけでなく、すべての値のカウントが評価されます。

## <a name="2018-09-public-preview-refresh"></a>2018 年 9 月 (パブリック プレビューを更新)

Azure Machine Learning が更新されてリリースされました。このリリースの詳細については、 https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/ を参照してください。


## <a name="next-steps"></a>次の手順

[Azure Machine Learning service](../service/overview-what-is-azure-ml.md) の概要をご覧ください。
