---
title: このリリースの新機能
titleSuffix: Azure Machine Learning
description: Azure Machine Learning および Machine Learning SDK と Data Prep Python SDK の最新の更新プログラムについて説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: larryfr
author: BlackMist
ms.date: 02/18/2021
ms.openlocfilehash: 04ed2e76797474a65d5c7c669066926225c1c1f9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103471743"
---
# <a name="azure-machine-learning-release-notes"></a>Azure Machine Learning のリリース ノート

この記事では、Azure Machine Learning の各リリースについて説明します。  SDK リファレンス コンテンツの詳細については、Azure Machine Learning の [**メインの SDK for Python**](/python/api/overview/azure/ml/intro) のリファレンス ページを参照してください。

__RSS フィード__:ご自身のフィード リーダーに次の URL をコピーして貼り付けることで、このページの更新時に通知を受け取ることができます。`https://docs.microsoft.com/api/search/rss?search=%22Azure+machine+learning+release+notes%22&locale=en-us`


## <a name="2021-03-08"></a>2021-03-08

### <a name="azure-machine-learning-sdk-for-python-v1240"></a>Azure Machine Learning SDK for Python v1.24.0
+ **バグの修正と機能強化**
  + **azureml-automl-core**
    + `azureml.automl.core.shared` から下位互換性のあるインポートが削除されました。 `azureml.automl.core.shared` 名前空間での「Module not found (モジュールが見つからない)」エラーは、`azureml.automl.runtime.shared` からインポートすることによって解決できます。
  + **azureml-contrib-automl-dnn-vision**
    + オブジェクト検出の YOLO モデルが公開されました。
  + **azureml-contrib-dataset**
    + 列の値で表形式データセット、メタデータでファイル データセットをフィルター処理する機能が追加されました。
  + **azureml-contrib-fairness**
    + `azureml-contrib-fairness` 内に JSON スキーマが組み込まれました。
  + **azureml-contrib-k8s**
    + リソース グループとクラスター名ではなく、アタッチする resource_id を指定することが必要になりました。
  + **azureml-contrib-mir**
    + モデルをデプロイするときに show_output を True に設定すると、要求をサーバーに送信する前に、推論構成とデプロイ構成が表示されます。
  + **azureml-core**
    + 列の値で表形式データセット、メタデータでファイル データセットをフィルター処理する機能が追加されました。
    + 従来は、ユーザーは、`admin_user_password` フィールドのパスワード強度要件 (つまり、1 つの小文字、1 つの大文字、1 つの数字、および ``\`~!@#$%^&*()=+_[]{}|;:./'",<>?`` セット内の 1 つの特殊文字のうち少なくとも 3 つが含まれていなければならない) を満たしていない ComputeTarget 用のプロビジョニング構成を作成することが可能でした。 ユーザーが脆弱なパスワードを使って構成を作成し、その構成を使用してジョブを実行した場合、ジョブは実行時に失敗しました。 現在は、`AmlCompute.provisioning_configuration` を呼び出すと、`ComputeTargetException` がスローされ、パスワード強度要件を説明するエラー メッセージが表示されるようになりました。 
    + また、場合によっては、最大ノード数に負の数値を使用して構成を指定することも可能でした。 これはもう行えなくなりました。 `max_nodes` 引数が負の整数の場合は、`AmlCompute.provisioning_configuration` によって `ComputeTargetException` がスローされるようになりました。
    + モデルをデプロイするときに show_output を True に設定すると、推論構成とデプロイ構成が表示されます。
    + モデル デプロイが完了するのを待っているときに show_output を True に設定すると、デプロイ操作の進行状況が表示されます。
    + ユーザーは、環境変数 AZUREML_AUTH_CONFIG_DIR を使用して AzureML 認証構成ディレクトリを指定できます。
    + 以前は、最小ノード数は最大ノード数より小さいと指定してプロビジョニング構成を作成することが可能でした。 ジョブは実行されますが、実行時に失敗しました。 このバグは修正されました。 `min_nodes < max_nodes` を使ってプロビジョニング構成を作成しようとすると、SDK によって `ComputeTargetException` が出されるようになりました。
  + **azureml-interpret**
    + スパース エンジニアリングの説明に関して特徴量の重要度の集計が説明ダッシュボードに表示されない問題が修正されました
    + azureml-interpret パッケージでの ExplanationClient のメモリ使用量が最適化されました
  + **azureml-train-automl-client**
    +  Spark を使用して実行しているときにユーザーに制御が返されるように show_output=False が修正されました。

## <a name="2021-02-28"></a>2021-02-28
### <a name="azure-machine-learning-studio-notebooks-experience-february-update"></a>Azure Machine Learning スタジオのノートブック エクスペリエンス (2 月の更新)
+ **新機能**
  + [ネイティブ ターミナル (GA)](https://docs.microsoft.com/azure/machine-learning/how-to-access-terminal). ユーザーは、統合ターミナル、およびその統合ターミナルを使用して Git 操作にもアクセスできるようになりました。
  + [ノートブック スニペット (プレビュー)](https://azure.github.io/azureml-web/docs/vs-code-snippets/snippets)。 一般的な Azure ML コードの抜粋が、すぐに入手できるようになりました。 ツールバーからアクセスできるコード スニペット パネルに移動するか、Ctrl + Space キーを使用してコード内スニペット メニューをアクティブにします。  
  + [キーボード ショートカット](https://docs.microsoft.com/azure/machine-learning/how-to-run-jupyter-notebooks#shortcut-keys)。 Jupyter で使用できるキーボード ショートカットと完全に一致します。 
  + セル パラメーターを示します。 ノートブック内のどのセルがパラメーター セルであり、コンピューティング インスタンスで [Papermill](https://github.com/nteract/papermill) を使用してバラメーター化されたノートブックを実行できるかをユーザーに示します。
  + ターミナルおよびカーネル セッション マネージャー: ユーザーは、コンピューティング上で実行されているすべてのカーネルとターミナル セッションを管理できます。
  + 共有ボタン。 ユーザーは、ファイルを右クリックして共有ボタンを使用することにより、ノートブックのエクスプローラーで任意のファイルを共有できるようになりました。


+ **バグの修正と機能強化**
  + ページ読み込み時間の短縮
  + パフォーマンスの向上 
  + 高速化およびカーネルの信頼性の向上
  + 実行中のすべての[コンピューティング インスタンス操作](https://docs.microsoft.com/azure/machine-learning/how-to-run-jupyter-notebooks#status-indicators)の進行状況を示すスピナーが追加されました。
  + エクスプローラーで右クリックします。 任意のファイルを右クリックすると、ファイルの操作が開くようになりました。 


## <a name="2021-02-16"></a>2021-02-16

### <a name="azure-machine-learning-sdk-for-python-v1230"></a>Azure Machine Learning SDK for Python v1.23.0
+ **バグの修正と機能強化**
  + **azureml-core**
    + [試験的な機能] Synapse ワークスペースをリンク サービスとして AML にリンクするためのサポートが追加されています
    + [試験的な機能] Synapse Spark プールをコンピューティングとして AML にアタッチするためのサポートが追加されています
    + [試験的な機能] ID ベースのデータ アクセスに対するサポートが追加されています。 ユーザーは、資格情報を指定せずにデータストアまたはデータセットを登録できます。 そのような場合は、ユーザーの AAD トークンまたはコンピューティング ターゲットのマネージド ID が認証に使用されます。 [こちら](https://aka.ms/data-access)をご覧ください。
  + **azureml-pipeline-steps**
    + [試験的な機能] [SynapseSparkStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.synapsesparkstep) に対するサポートが追加されています
  + **azureml-synapse**
    + [試験的な機能] Synapse Spark プールで対話型セッションを実行するための Spark マジックに対するサポートが追加されています。
+ **バグの修正と機能強化**
  + **azureml-automl-runtime**
    + この更新プログラムでは、AutoML SDK の予測ツールボックスに Holt-Winters の指数平滑化法が追加されました。 時系列の場合、最適なモデルは [AICc (補正赤池情報量基準)](https://otexts.com/fpp3/selecting-predictors.html#selecting-predictors) によって選択され、返されます。
    + AutoML では、1 つではなく 2 つのログ ファイルが生成されるようになりました。 ログ ステートメントは、ログ ステートメントが生成されたプロセスに応じて、いずれかに書き込まれます。
    + クロス検証を使用したモデル トレーニング中、不要なサンプル内予測は削除されます。 これにより、いくつかのケース (特に時系列予測モデル) で、モデルのトレーニング時間が短縮される場合があります。
  + **azureml-contrib-fairness**
    + dashboardDictionary のアップロード用の JSON スキーマが追加されています。
  + **azureml-contrib-interpret**
    + パッケージが 10 月から非推奨になった後に次の更新で削除されることを反映するために、azureml-contrib-interpret の README が更新されました。代わりに azureml-interpret パッケージを使用してください。
  + **azureml-core**
    + 以前は、最小ノード数は最大ノード数より小さいと指定してプロビジョニング構成を作成することが可能でした。 これは、修正されました。 `min_nodes < max_nodes` を使ってプロビジョニング構成を作成しようとすると、SDK によって `ComputeTargetException` が出されるようになりました。
    +  操作が実際に完了する前に関数から制御フローが返される AmlCompute での wait_for_completion のバグが修正されました。
    + Run.fail() が非推奨になりました。Run.tag() を使用して実行に失敗のマークを付けるか、Run.cancel() を使用して実行に取り消し済みのマークを付けてください。
    + 指定された環境名が文字列ではない場合に、「Environment name expected str, {} found (環境名では文字列が予期されていますが、{} が検出されました)」というエラー メッセージが表示されます。
  + **azureml-train-automl-client**
    + Azure Databricks クラスターで実行された AutoML 実験の取り消しを妨げていたバグを修正しました。


## <a name="2021-02-09"></a>2021-02-09

### <a name="azure-machine-learning-sdk-for-python-v1220"></a>Azure Machine Learning SDK for Python v1.22.0
+ **バグの修正と機能強化**
  + **azureml-automl-core**
    + ビジョン モデルの conda yml ファイルに余分な pip 依存関係が追加されていたバグを修正しました。
  + **azureml-automl-runtime**
    + 従来の予測モデル (例: AutoArima) が、補完されたターゲット値を含む行が存在しないトレーニング データを受け取ることができたバグを修正しました。 これは、これらのモデルのデータ コントラクトに違反していました。 * 時系列遅延演算子の lag-by-occurrence 動作に関するさまざまなバグを修正しました。 以前は、補完されたすべての行が lag-by-occurrence 操作によって正しくマークされなかったため、常に正しい発生遅延値が生成されるとは限りませんでした。 また、遅延演算子と、lag-by-occurrence 動作を含むローリング ウィンドウ演算子との間のいくつか互換性の問題も修正しました。 これが原因で、以前はローリング ウィンドウ演算子によって本来なら使用される必要があるトレーニング データのいくつかの行が削除されていました。
  + **azureml-core**
    + 対象ユーザーによるトークン認証のサポートの追加。
    + マルチプロセス マルチノード PyTorch ジョブをサポートするために `process_count` を [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration) に追加します。
  + **azureml-pipeline-steps**
    + [CommandStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.commandstep) の一般提供が開始され、試験段階ではなくなりました。
    + [ParallelRunConfig](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunconfig): ミニ バッチ レベルでのエラーしきい値を確認するために、引数 allowed_failed_count と allowed_failed_percent を追加します。 エラーしきい値には現在、3 つのフレーバーがあります。
       + error_threshold - 失敗したミニ バッチ項目の許容数。 
       + allowed_failed_count - 失敗したミニ バッチの許容数。 
       + allowed_failed_percent - 失敗したミニ バッチの許容される割合。 
       
       これらのいずれかを超過すると、ジョブは停止します。 error_threshold は、下位互換性を維持するために必要です。 これを無視するには、値を -1 に設定してください。
    + AutoMLStep 名に含まれる空白文字の処理を修正しました。
    + ScriptRunConfig が HyperDriveStep でサポートされるようになりました
  + **azureml-train-core**
    + ScriptRun から呼び出された HyperDrive 実行は、子実行と見なされるようになります。
    + マルチプロセス マルチノード PyTorch ジョブをサポートするために `process_count` を [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration) に追加します。
  + **azureml-widgets**
    + ParallelRunStep の状態を視覚化するために、ウィジェット ParallelRunStepDetails を追加します。
    + ハイパードライブ ユーザーが並列座標グラフに追加の軸を表示できるようになります。これには、各子実行のハイパーパラメーターの各セットに対応するメトリック値が表示されます。


 ## <a name="2021-01-31"></a>2021-01-31
### <a name="azure-machine-learning-studio-notebooks-experience-january-update"></a>Azure Machine Learning Studio のノートブック エクスペリエンス (1 月の更新)
+ **新機能**
  + AzureML のネイティブ Markdown エディター。 ユーザーは、AzureML Studio で Markdown ファイルをネイティブに表示および編集できるようになりました。
  + [スクリプトの [実行] ボタン (.py、.R と .sh)](./how-to-run-jupyter-notebooks.md#run-a-notebook-or-python-script)。 ユーザーは、AzureML で Python、R および Bash スクリプトを簡単に実行できるようになりました
  + [変数エクスプローラー](./how-to-run-jupyter-notebooks.md#explore-variables-in-the-notebook)。 ポップアップ パネルで変数とデータ フレームの内容を調べます。 ユーザーは、データ型、サイズ、および内容を簡単に確認できます。
  + [目次](./how-to-run-jupyter-notebooks.md#navigate-with-a-toc)。 Markdown のヘッダーで示されているノートブックのセクションに移動します。
  + ノートブックを Latex/HTML/Py としてエクスポートします。 LaTex、HTML、または .py にエクスポートすることで、共有しやすいノートブック ファイルを作成します
  + IntelliCode。 ML を利用した結果により、[インテリジェントなオート コンプリート エクスペリエンス](/visualstudio/intellicode/overview)が強化されます。

+ **バグの修正と機能強化**
  + ページ読み込み時間の短縮
  + パフォーマンスの向上 
  + 高速化およびカーネルの信頼性の向上
  

 ## <a name="2021-01-25"></a>2021-01-25

### <a name="azure-machine-learning-sdk-for-python-v1210"></a>Azure Machine Learning SDK for Python v1.21.0
+ **バグの修正と機能強化**
  + **azure-cli-ml**
    + UserAssigned ID で AmlCompute を使用しているときの CLI ヘルプ テキストが修正されました。
  + **azureml-contrib-automl-dnn-vision**
    + AutoML ビジョンの実行で [デプロイ] および [ダウンロード] ボタンが表示され、他の AutoML 実行と同様にモデルをデプロイまたはダウンロードできます。 推論を実行するスクリプトを含む 2 つの新しいファイル (scoring_file_v_1_0_0.py と conda_env_v_1_0_0.yml) と、conda 環境を再作成するための yml ファイルが存在します。 また、'model.pth' ファイルも '.pt' 拡張子を使用するように名前が変更されました。
  + **azureml-core**
    + MSI での azure-cli-ml のサポート
    + ユーザー割り当てマネージド ID のサポート。
    + この変更により、顧客は、保存時の暗号化のためにカスタマー キー コンテナーからキーをフェッチするために使用できるユーザー割り当て ID を指定できるようになります。
    +  非常に大きなファイルのプロファイルでの row_count=0 の修正 - 空白のパディングを含む区切られた値に対する 2 段階の変換でのエラーの修正
    + 出力データセット GA の試験的なフラグの削除
    + 特定のバージョンのモデルをフェッチする方法に関するドキュメントの更新
    + Private Link の場合の混合モード アクセスでのワークスペースの更新の許可
    + 実行再開機能のためにデータストアでの追加の登録を削除するための修正
    + ワークスペースのプライマリ ユーザー割り当て ID を更新するための CLI/SDK サポートの追加
  + **azureml-interpret**
    + azureml-interpret の interpret-community 0.16.0 への更新
    + azureml-interpret での説明クライアントのためのメモリ最適化
  + **azureml-train-automl-runtime**
    + ADB 実行でのストリーミングの有効化
  + **azureml-train-core**
    + 実行再開機能のためにデータストアでの追加の登録を削除するための修正
  + **azureml-widgets**
    + 顧客は、このウィジェットを使用して既存の実行データ視覚化への変更を表示できませんが、オプションで条件付きハイパーパラメーターを使用する場合はサポートされるようになりました。
    + ユーザー実行ウィジェットに、実行がキューに入った状態になっている理由を示す詳細な説明が含まれるようになりました。


 ## <a name="2021-01-11"></a>2021-01-11

### <a name="azure-machine-learning-sdk-for-python-v1200"></a>Azure Machine Learning SDK for Python v1.20.0
+ **バグの修正と機能強化**
  + **azure-cli-ml**
    + OptimizationConfig に framework_version が追加されました。 これは、モデルがフレームワーク MULTI に登録されるときに使用されます。
  + **azureml-contrib-optimization**
    + OptimizationConfig に framework_version が追加されました。 これは、モデルがフレームワーク MULTI に登録されるときに使用されます。
  + **azureml-pipeline-steps**
    + 処理するコマンドを受け取る CommandStep の導入。 コマンドには、実行可能ファイル、シェル コマンド、スクリプトなどを含めることができます。
  + **azureml-core**
    + ワークスペースの作成で、ユーザー割り当て ID がサポートされるようになりました。 SDK/CLI からの UAI サポートの追加。
    + ローカル デプロイで score.py に関する変更を取得するために、service.reload() での問題が修正されました。
    + `run.get_details()` には、この実行の作成者の名前を表示する "submittedBy" という名前の追加のフィールドがあります。
    + 実行から直接モデルを登録する方法を説明するために、Model.register メソッドのドキュメントが編集されました。
    + IOT-Server の接続状態の変更処理に関する問題が修正されました。
   

## <a name="2020-12-31"></a>2020-12-31
### <a name="azure-machine-learning-studio-notebooks-experience-december-update"></a>Azure Machine Learning Studio のノートブック エクスペリエンス (12 月の更新)
+ **新機能**
  + ユーザー ファイル名の検索。 ユーザーは、ワークスペースに保存されているすべてのファイルを検索できるようになりました。
  + ノートブック セルごとでのマークダウンの横並びのサポート。 ノートブック セルで、ユーザーは、レンダリングされたマークダウンとマークダウン構文を横並びに表示できるようになりました。
  + セルのステータス バー。 ステータス バーは、コード セルの状態、セルの実行が成功したかどうか、および実行にかかった時間を示します。 
   
+ **バグの修正と機能強化**
  + ページ読み込み時間の短縮
  + パフォーマンスの向上 
  + 高速化およびカーネルの信頼性の向上

  
## <a name="2020-12-07"></a>2020-12-07

### <a name="azure-machine-learning-sdk-for-python-v1190"></a>Azure Machine Learning SDK for Python v1.19.0
+ **バグの修正と機能強化**
  + **azureml-automl-core**
    + AutoMLStep にテスト データの実験的なサポートを追加しました。
    + テスト セット インジェスト機能の最初のコア実装を追加しました。
    + sklearn.externals.joblib の参照を移動して、joblib に直接依存するようにしました。
    + "image-instance-segmentation" という新しい AutoML タスクの種類を導入します。
  + **azureml-automl-runtime**
    + テスト セット インジェスト機能の最初のコア実装を追加しました。
    + テキスト列にあるすべての文字列の長さが厳密に 1 文字の場合、TfIdf ワードグラム フィーチャライザーは機能しません。そのトークナイザーでは 2 文字未満の文字列が無視されるためです。 現在のコード変更により、このユース ケースを AutoML で処理できるようになります。
    + "image-instance-segmentation" という新しい AutoML タスクの種類を導入します。
  + **azureml-contrib-automl-dnn-nlp**
    + 新しい dnn-nlp パッケージの最初の PR
  + **azureml-contrib-automl-dnn-vision**
    + "image-instance-segmentation" という新しい AutoML タスクの種類を導入します。
  + **azureml-contrib-automl-pipeline-steps**
    + この新しいパッケージは、多くのモデルのトレーニング/推論シナリオに必要な手順を作成する役割を担います。 また、トレーニング/推論コードを azureml.train.automl.runtime パッケージに移動します。これにより、今後の修正プログラムは、キュレーション環境リリースを通じて自動的に使用できるようになります。
  + **azureml-contrib-dataset**
    + "image-instance-segmentation" という新しい AutoML タスクの種類を導入します。
  + **azureml-core**
    + テスト セット インジェスト機能の最初のコア実装を追加しました。
    + azureml-core パッケージのドキュメントの xref 警告を修正しています
    + SDK のコマンド サポート機能のドキュメント文字列の修正
    + RunConfiguration にコマンド プロパティを追加しています。 この機能を使用すると、ユーザーは AzureML SDK を介してコンピューティングで実際のコマンドまたは実行可能ファイルを実行できます。
    + ユーザーは、空の実験を、その実験の ID を指定して削除できます。
  + **azureml-dataprep**
    + Scala 2.12 で構築された Spark のデータセットのサポートを追加しました。 これは既存の 2.11 のサポートに追加されます。
  + **azureml-mlflow**
    + AzureML-MLflow によって、送信された実行が早期に終了しないようにする保護がリモート スクリプトに追加されます。
  + **azureml-pipeline-core**
    + UI で作成されたパイプライン エンドポイントの既定のパイプラインを設定するときのバグを修正しました
  + **azureml-pipeline-steps**
    + AutoMLStep にテスト データの実験的なサポートを追加しました。
  + **azureml-tensorboard**
    + azureml-core パッケージのドキュメントの xref 警告を修正しています
  + **azureml-train-automl-client**
    + AutoMLStep にテスト データの実験的なサポートを追加しました。
    + テスト セット インジェスト機能の最初のコア実装を追加しました。
    + "image-instance-segmentation" という新しい AutoML タスクの種類を導入します。
  + **azureml-train-automl-runtime**
    + テスト セット インジェスト機能の最初のコア実装を追加しました。
    + validation_size 設定を使用して AutoML モデルがトレーニングされている場合の、最適な AutoML モデルの生の説明の計算を修正します。
    + sklearn.externals.joblib の参照を移動して、joblib に直接依存するようにしました。
  + **azureml-train-core**
    + HyperDriveRun.get_children_sorted_by_primary_metric() はより高速に完了するようになりました
    + HyperDrive SDK でのエラー処理が改善されました。
    +  実験の実行を構成するために ScriptRunConfig を使用することを優先して、すべての Estimator クラスが非推奨になりました。 非推奨のクラスは次のとおりです。
        + MMLBase
        + エスティメーター
        + PyTorch 
        + TensorFlow 
        + Chainer 
        + SKLearn
    + PyTorchConfiguration を ScriptRunConfig と共に使用することを優先して、Estimator クラスの有効な入力型として Nccl と Gloo を使用することは非推奨になりました。
    + MpiConfiguration を ScriptRunConfig と共に使用することを優先して、Estimator クラスの有効な入力型として Mpi を使用することは非推奨になりました。
    + runconfiguration にコマンド プロパティを追加しています。 この機能を使用すると、ユーザーは AzureML SDK を介してコンピューティングで実際のコマンドまたは実行可能ファイルを実行できます。

    +  実験の実行を構成するために ScriptRunConfig を使用することを優先して、すべての Estimator クラスが非推奨になりました。 非推奨のクラスは次のとおりです。+ MMLBaseEstimator + Estimator + PyTorch + TensorFlow + Chainer + SKLearn
    + PyTorchConfiguration を ScriptRunConfig と共に使用することを優先して、Estimator クラスの有効な入力の型として Nccl と Gloo を使用することは非推奨になりました。 
    + MpiConfiguration を ScriptRunConfig と共に使用することを優先して、Estimator クラスの有効な入力の型として Mpi を使用することは非推奨になりました。

## <a name="2020-11-30"></a>2020-11-30
### <a name="azure-machine-learning-studio-notebooks-experience-november-update"></a>Azure Machine Learning Studio のノートブック エクスペリエンス (11 月の更新)
+ **新機能**
   + ネイティブ ターミナル。 ユーザーは、統合ターミナル、およびその[統合ターミナル](./how-to-access-terminal.md)を使用して Git 操作にもアクセスできるようになりました。
  + フォルダーの複製 
  + [Costing for Compute]\(コンピューティングのコスト計算\) ドロップダウン 
  + オフライン コンピューティング Pylance 

+ **バグの修正と機能強化**
  + ページ読み込み時間の短縮
  + パフォーマンスの向上 
  + 高速化およびカーネルの信頼性の向上
  + 大きなファイルのアップロード。 95 MB を超えるファイルをアップロードできるようになりました

## <a name="2020-11-09"></a>2020-11-09

### <a name="azure-machine-learning-sdk-for-python-v1180"></a>Azure Machine Learning SDK for Python v1.18.0
+ **バグの修正と機能強化**
  + **azureml-automl-core**
    +  ガウス ノイズによる埋め込みを可能にすることで、短い時系列の処理が改善されました。
  + **azureml-automl-runtime**
    + DateTime 列に OutOfBoundsDatetime 値が含まれている場合に、ConfigException がスローされる
    + ガウス ノイズによる埋め込みを可能にすることで、短い時系列の処理が改善されました。
    + 各テキスト列で、そのテキスト列の文字列の長さに基づいた n-gram 範囲の char-gram 変換を利用できることを確認します
    + ユーザーのローカル コンピューティングで実行されている AutoML 実験の最適モードに対して未加工の特徴の説明を提供する
  + **azureml-core**
    + pyjwt パッケージをピン留めして、今後のリリースで最新のバージョンがプルされないようにします。
    + 実験を作成すると、その実験が存在する場合や新しい実験があった場合に、同じ名前を持つアクティブまたは最後にアーカイブされた実験が返されます。
    + 名前で get_experiment を呼び出すと、その指定された名前を持つアクティブまたは最後にアーカイブされた実験が返されます。
    + 実験の再アクティブ化中にユーザーが名前を変更することはできません。
    + データセットが誤って実験に渡された場合の修正候補を含めるように、エラー メッセージが改善されました (例: ScriptRunConfig)。 
    + 名前が既に存在する場合の動作についての説明を含めるように、`OutputDatasetConfig.register_on_complete` のドキュメントが改善されました。
    + 共通の環境変数と競合する可能性があるデータセットの入力と出力の名前を指定すると、警告が表示されるようになりました。
    + データストアの登録時に、`grant_workspace_access` パラメーターが再利用されるようにしました。 Machine Learning Studio から仮想ネットワークの背後にあるデータにアクセスするには、これを `True` に設定します。
      [詳細情報](./how-to-enable-studio-virtual-network.md)
    + リンクされたサービス API は改善されています。 リソース ID を指定する代わりに、構成で定義されている 3 つの個別のパラメーター sub_id、rg、name があります。
    + 顧客がトークンの破損の問題を自己解決できるようにするために、ワークスペース トークンの同期をパブリック メソッドにできるようにします。
    + この変更により、空の文字列を script_param の値として使用できるようになります。
  + **azureml-train-automl-client**
    +  ガウス ノイズによる埋め込みを可能にすることで、短い時系列の処理が改善されました。
  + **azureml-train-automl-runtime**
    + DateTime 列に OutOfBoundsDatetime 値が含まれている場合に、ConfigException がスローされる
    + ユーザーのローカル コンピューティングで実行されている AutoML 実験の最適モードに対して未加工の特徴の説明を提供するためのサポートの追加
    + ガウス ノイズによる埋め込みを可能にすることで、短い時系列の処理が改善されました。
  + **azureml-train-core**
    + この変更により、空の文字列を script_param の値として使用できるようになります。
  + **azureml-train-restclients-hyperdrive**
    + より多くのコンテキストを提供するために README が変更されています
  + **azureml-widgets**
    + ウィジェットのグラフまたは並列座標ライブラリに文字列サポートを追加します。

## <a name="2020-11-05"></a>2020-11-05

### <a name="data-labeling-for-image-instance-segmentation-polygon-annotation-preview"></a>画像インスタンスのセグメント化 (ポリゴン アノテーション) のためのデータのラベル付け (プレビュー)

データのラベル付けで画像インスタンスのセグメント化 (ポリゴン アノテーション) プロジェクトの種類を使用できるようになりました。これにより、ユーザーは画像内のオブジェクトの輪郭の周囲にポリゴンを使って描画したり注釈を付けたりすることができます。 ユーザーは、画像内で対象とする各オブジェクトに対して、クラスとポリゴンを割り当てることができます。

画像インスタンスのセグメント化のラベル付けに関する詳細については、[こちら](how-to-label-images.md)を参照してください。



## <a name="2020-10-26"></a>2020-10-26

### <a name="azure-machine-learning-sdk-for-python-v1170"></a>Azure Machine Learning SDK for Python v1.17.0
+ **新しい例**
  + 例の新しいコミュニティ主導のリポジトリは、 https://github.com/Azure/azureml-examples で利用できます。
+ **バグの修正と機能強化**
  + **azureml-automl-core**
    + get_output から XGBoostError が報告されることがある問題を修正しました。
  + **azureml-automl-runtime**
    + AutoML によって作成された時間またはカレンダーベースの機能にプレフィックスが付けられるようになります。
    + 数多くのクラスとサブサンプリングが有効な分類データセットの StackEnsemble のトレーニング中に発生する IndexError を修正しました。
    + モデルを再調整した後、VotingRegressor の予測が不正確になるおそれがある問題を修正しました。
  + **azureml-core**
    + AKS のデプロイ構成と Azure Kubernetes Service の概念の間の関係について、詳細情報を追加しました。
    + 環境クライアント ラベルがサポートされています。 ユーザーは、環境にラベルを付け、ラベルを使用してそれらを参照することができます。
  + **azureml-dataprep**
    + 現在サポートされていない Spark を Scala 2.12 と共に使用する場合のエラー メッセージを改善しました。
  + **azureml-explain-model**
    + azureml-explain-model パッケージは正式に非推奨になりました
  + **azureml-mlflow**
    + 終了処理中の状態が適切に処理されていなかった、azureml バックエンドに対する mlflow.projects.run のバグを解決しました。
  + **azureml-pipeline-core**
    + 1 つのパイプライン エンドポイントに基づいてパイプライン スケジュールを作成、一覧表示、取得するためのサポートを追加します。
    +  無効な使用例が含まれている PipelineData.as_dataset のドキュメントを改善しました。PipelineData.as_dataset を不適切に使用すると、ValueException がスローされるようになりました
    + HyperDriveStep の実行直後に PipelineStep 内に最適なモデルを登録するように、HyperDriveStep パイプライン ノートブックを変更しました。
  + **azureml-pipeline-steps**
    + HyperDriveStep の実行直後に PipelineStep 内に最適なモデルを登録するように、HyperDriveStep パイプライン ノートブックを変更しました。
  + **azureml-train-automl-client**
    + get_output から XGBoostError が報告されることがある問題を修正しました。

### <a name="azure-machine-learning-studio-notebooks-experience-october-update"></a>Azure Machine Learning Studio のノートブック エクスペリエンス (10 月の更新)
+ **新機能**
  + [仮想ネットワークの完全サポート](./how-to-enable-studio-virtual-network.md)
  + [フォーカス モード](./how-to-run-jupyter-notebooks.md#focus-mode)
  + ノートブックの保存 Ctrl-S
  + 行番号

+ **バグの修正と機能強化**
  + 速度とカーネルの信頼性の向上
  + Jupyter ウィジェットの UI の更新

## <a name="2020-10-12"></a>2020-10-12

### <a name="azure-machine-learning-sdk-for-python-v1160"></a>Azure Machine Learning SDK for Python v1.16.0
+ **バグの修正と機能強化**
  + **azure-cli-ml**
    + AKSWebservice と AKSEndpoints は、ポッドレベルの CPU とメモリのリソース制限をサポートするようになりました。 これらのオプション制限は、該当する CLI 呼び出しで `--cpu-cores-limit` フラグと `--memory-gb-limit` フラグを設定することで利用できます。
  + **azureml-core**
    + azureml-core の直接の依存関係のメジャー バージョンを固定します
    + AKSWebservice と AKSEndpoints は、ポッドレベルの CPU とメモリのリソース制限をサポートするようになりました。 [Kubernetes のリソースと制限事項](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#requests-and-limits)に関する詳細
    + 個々の行をログに記録できるように run.log_table を更新しました。
    + ワークスペースの使用でのみ実行を取得するため、静的メソッド `Run.get(workspace, run_id)` を追加しました 
    + ワークスペース内で実行を取得するため、インスタンス メソッド `Workspace.get_run(run_id)` を追加しました
    + 実行構成に、スクリプトと引数ではなくコマンドを送信できるようになるコマンド プロパティの導入。
  + **azureml-interpret**
    + azureml-interpret のクライアントの is_raw フラグの動作に関する説明を修正しました
  + **azureml-sdk**
    + `azureml-sdk` は Python 3.8 を正式にサポートします。
  + **azureml-train-core**
    + TensorFlow 2.3 キュレーション環境の追加
    + 実行構成に、スクリプトと引数ではなくコマンドを送信できるようになるコマンド プロパティの導入。
  + **azureml-widgets**
    + スクリプト実行ウィジェットのインターフェイスを再設計しました。


## <a name="2020-09-28"></a>2020-09-28

### <a name="azure-machine-learning-sdk-for-python-v1150"></a>Azure Machine Learning SDK for Python v1.15.0
+ **バグの修正と機能強化**
  + **azureml-contrib-interpret**
    + LIME Explainer が azureml-contrib-interpret から interpret-community パッケージに移動され、Image Explainer が azureml-contrib-interpret パッケージから削除されました
    + 視覚化ダッシュボードが azureml-contrib-interpret パッケージから削除され、説明クライアントが azureml-interpret パッケージに移動され、azureml-contrib-interpret パッケージで非推奨になり、ノートブックが改善された API を反映するように更新されました
    + azureml-interpret、azureml-explain-model、azureml-contrib-interpret、azureml-contrib、および azureml-tensorboard での PyPI パッケージの説明を修正しました
  + **azureml-contrib-notebook**
    + Papermill 1.x が引き続き機能するように、nbcovert の依存関係を 6 未満にピン留めしました。
  + **azureml-core**
    + TensorflowConfiguration と MpiConfiguration コンストラクターにパラメーターを追加して、ユーザーに個々の属性を設定するよう要求しなくてもクラス属性を簡単に初期化できるようにしました。 ScriptRunConfig で分散 PyTorch ジョブを構成するための PyTorchConfiguration クラスを追加しました。
    + 認証エラーを修正するために azure-mgmt-resource のバージョンをピン留めしました。
    + Triton のコードなしのデプロイがサポートされるようになりました
    + 対話形式での実行シナリオを使用したときに、Run.start_logging() に指定された出力ディレクトリが追跡されるようになります。 Run.complete() を呼び出すと、追跡対象ファイルが ML Studio に表示されます
    + `encoding` 引数を渡すことによって、`Dataset.Tabular.from_delimited_files` と `Dataset.Tabular.from_json_lines_files` を使用したデータセットの作成中にファイルのエンコードを指定できるようになりました。 サポートされているエンコードは、"utf8"、"iso88591"、"latin1"、"ascii"、"utf16"、"utf32"、"utf8bom"、"windows1252" です。
    + 環境オブジェクトが ScriptRunConfig コンストラクターに渡されない場合のバグを修正しました。
    + 別のコンピューターからのローカル実行の取り消しを許可するように Run.cancel() を更新しました。
  + **azureml-dataprep**
    +  データセットのマウント タイムアウトに関する問題を修正しました。
  + **azureml-explain-model**
    + azureml-interpret、azureml-explain-model、azureml-contrib-interpret、azureml-contrib、および azureml-tensorboard での PyPI パッケージの説明を修正しました
  + **azureml-interpret**
    + 視覚化ダッシュボードが azureml-contrib-interpret パッケージから削除され、説明クライアントが azureml-interpret パッケージに移動され、azureml-contrib-interpret パッケージで非推奨になり、ノートブックが改善された API を反映するように更新されました
    + azureml-interpret パッケージが interpret-community 0.15.0 に依存するように更新されました
    + azureml-interpret、azureml-explain-model、azureml-contrib-interpret、azureml-contrib、および azureml-tensorboard での PyPI パッケージの説明を修正しました
  + **azureml-pipeline-core**
    +  `name` パラメーターを既存のデータセット名に設定して `register_on_complete` を呼び出すと、システムが応答を停止する可能性がある、`OutputFileDatasetConfig` に関するパイプラインの問題を修正しました。
  + **azureml-pipeline-steps**
    + 古くなった Databricks ノートブックを削除しました。
  + **azureml-tensorboard**
    + azureml-interpret、azureml-explain-model、azureml-contrib-interpret、azureml-contrib、および azureml-tensorboard での PyPI パッケージの説明を修正しました
  + **azureml-train-automl-runtime**
    + 視覚化ダッシュボードが azureml-contrib-interpret パッケージから削除され、説明クライアントが azureml-interpret パッケージに移動され、azureml-contrib-interpret パッケージで非推奨になり、ノートブックが改善された API を反映するように更新されました
  + **azureml-widgets**
    + 視覚化ダッシュボードが azureml-contrib-interpret パッケージから削除され、説明クライアントが azureml-interpret パッケージに移動され、azureml-contrib-interpret パッケージで非推奨になり、ノートブックが改善された API を反映するように更新されました

## <a name="2020-09-21"></a>2020-09-21

### <a name="azure-machine-learning-sdk-for-python-v1140"></a>Azure Machine Learning SDK for Python v1.14.0
+ **バグの修正と機能強化**
  + **azure-cli-ml**
    + グリッド プロファイルが SDK から削除され、サポートされなくなりました。
  + **azureml-accel-models**
    + azureml-accel-models パッケージで、Tensorflow 2.x がサポートされるようになりました
  + **azureml-automl-core**
    + pandas/sklearn のローカル バージョンがトレーニングの間に使用されたものと一致しない場合のエラー処理が get_output に追加されました
  + **azureml-automl-runtime**
    + AutoArima のイテレーションが PredictionException と次のメッセージで失敗するバグを修正しました: "Silent failure occurred during prediction." (予測の間にサイレント エラーが発生しました。)
  + **azureml-cli-common**
    + グリッド プロファイルが SDK から削除され、サポートされなくなりました。
  + **azureml-contrib-server**
    + pypi の概要ページのパッケージの説明を更新します。
  + **azureml-core**
    + グリッド プロファイルが SDK から削除され、サポートされなくなりました。
    + ワークスペースの取得に失敗した場合のエラー メッセージの数を減らします。
    + メタデータのフェッチに失敗したときに警告を表示しません
    + 新しい Kusto ステップと Kusto コンピューティング先。
    + sku パラメーターのドキュメントを更新します。 CLI および SDK のワークスペース更新機能で sku を削除します。
    + pypi の概要ページのパッケージの説明を更新します。
    + AzureML 環境のドキュメントを更新しました。
    + SDK で AML ワークスペースに対するサービス管理対象リソースの設定を公開します。
  + **azureml-dataprep**
    + データセット マウント用にファイルに対する実行アクセス許可を有効にします。
  + **azureml-mlflow**
    + AzureML MLflow のドキュメントとノートブックのサンプルを更新しました 
    + AzureML バックエンドでの MLflow プロジェクトの新しいサポート
    + MLflow モデル レジストリのサポート
    + AzureML-MLflow 操作に対する Azure RBAC サポートを追加しました 
    
  + **azureml-pipeline-core**
    + PipelineOutputFileDataset.parse_* メソッドのドキュメントを改善しました。
    + 新しい Kusto ステップと Kusto コンピューティング先。
    + pipeline-endpoint エンティティに Swaggerurl プロパティを提供しました。それを使用して、ユーザーは公開されたパイプライン エンドポイントのスキーマ定義を確認できます。
  + **azureml-pipeline-steps**
    + 新しい Kusto ステップと Kusto コンピューティング先。
  + **azureml-telemetry**
    + pypi の概要ページのパッケージの説明を更新します。
  + **azureml-train**
    + pypi の概要ページのパッケージの説明を更新します。
  + **azureml-train-automl-client**
    + pandas/sklearn のローカル バージョンがトレーニングの間に使用されたものと一致しない場合のエラー処理が get_output に追加されました
  + **azureml-train-core**
    + pypi の概要ページのパッケージの説明を更新します。
    
## <a name="2020-08-31"></a>2020-08-31

### <a name="azure-machine-learning-sdk-for-python-v1130"></a>Azure Machine Learning SDK for Python v1.13.0
+ **プレビュー機能**
  + **azureml-core** 新しい出力データセット機能を使用すると、BLOB、ADLS Gen 1、ADLS Gen 2、および FileShare などのクラウド ストレージに書き戻すことができます。 データを出力する場所、データを出力する方法 (マウントまたはアップロード経由)、後で再利用して共有するために出力データを登録するかどうか、パイプライン ステップ間で中間データをシームレスに渡すかどうかを構成できます。 これにより、再現と共有が可能となり、データの重複を防ぎ、結果としてコスト効率と生産性が向上します。 [使用方法の確認](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig)
    
+ **バグの修正と機能強化**
  + **azureml-automl-core**
    + AutoML のすべての pip 依存関係を固定するために、validated_{platform}_requirements.txt ファイルを追加しました。
    + このリリースでは、4 Gb を超えるモデルがサポートされています。
    + アップグレードされた AutoML の依存関係: `scikit-learn` (現在 0.22.1)、`pandas` (現在 0.25.1)、`numpy` (現在 1.18.2)。
  + **azureml-automl-runtime**
    + テキスト DNN の horovod を、常に fp16 圧縮を使用するように設定します。
    + このリリースでは、4 Gb を超えるモデルがサポートされています。
    + "ImportError: 名前 `RollingOriginValidator` をインポートできません" で AutoML が失敗する問題を修正しました。
    + アップグレードされた AutoML の依存関係: `scikit-learn` (現在 0.22.1)、`pandas` (現在 0.25.1)、`numpy` (現在 1.18.2)。
  + **azureml-contrib-automl-dnn-forecasting**
    + アップグレードされた AutoML の依存関係: `scikit-learn` (現在 0.22.1)、`pandas` (現在 0.25.1)、`numpy` (現在 1.18.2)。
  + **azureml-contrib-fairness**
    + azureml-contrib-fairness の簡単な説明を指定します。
  + **azureml-contrib-pipeline-steps**
    + このパッケージが非推奨であり、ユーザーは代わりに azureml-pipeline-steps を使用する必要があることを示すメッセージを追加しました。
  + **azureml-core**
    + ワークスペースのリスト キー コマンドを追加しました。
    + Workspace SDK と CLI で tags パラメーターを追加します。
    + データセットを使用する子実行を送信すると、`TypeError: can't pickle _thread.RLock objects` によって失敗するバグを修正しました。
    + Model list() の page_count の既定値とドキュメントを追加しています。
    + CLI と SDK を adbworkspace パラメーターを受け取るように変更し、ワークスペース adb の lin および unlink ランナーを追加しました。
    + 更新で呼び出されたデータセットのバージョンではなく、最新のデータセットのバージョンが更新される、Dataset.update のバグを修正しました。 
    + 特定のより古いバージョンが取得された場合にも、最新のデータセットのバージョンのタグが表示される、Dataset.get_by_name のバグを修正しました。
  + **azureml-interpret**
    + 元の explainer の shap_values_output パラメーターに基づいて、azureml-interpret の shap スコアリング explainer に確率出力を追加しました。
  + **azureml-pipeline-core**
    + `PipelineOutputAbstractDataset.register` のドキュメントを改善しました。
  + **azureml-train-automl-client**
    + アップグレードされた AutoML の依存関係: `scikit-learn` (現在 0.22.1)、`pandas` (現在 0.25.1)、`numpy` (現在 1.18.2)。
  + **azureml-train-automl-runtime**
    + アップグレードされた AutoML の依存関係: `scikit-learn` (現在 0.22.1)、`pandas` (現在 0.25.1)、`numpy` (現在 1.18.2)。
  + **azureml-train-core**
    + ユーザーは、HyperDriveConfig の作成時に、有効な hyperparameter_sampling 引数の指定が必要になりました。 また、HyperDriveRunConfig が非推奨になったことをユーザーに知らせるように、HyperDriveRunConfig のドキュメントが編集されました。
    + PyTorch の既定のバージョンが 1.4 に戻されました。
    + PyTorch 1.6 および Tensorflow 2.2 のイメージとキュレートされた環境が追加されました。

### <a name="azure-machine-learning-studio-notebooks-experience-august-update"></a>Azure Machine Learning Studio のノートブック エクスペリエンス (8 月の更新)
+ **新機能**
  + 新しくなった作業開始のランディング ページ 
  
+ **プレビュー機能**
    + ノートブックの機能を収集します。 [収集](./how-to-run-jupyter-notebooks.md#clean-your-notebook-preview)機能を使用して、ユーザーはノートブックを簡単にクリーンアップできるようになりました。収集によってノートブックの自動化された依存関係分析が使用され、重要なコードを保持しつつ、関連性のない部分を削除できます。

+ **バグの修正と機能強化**
  + 速度と信頼性の向上
  + ダーク モードのバグが修正されました
  + 出力スクロールのバグが修正されました
  + サンプル検索で、Azure Machine Learning サンプル ノートブック リポジトリ内のすべてのファイルのすべての内容が検索されるようになりました
  + 複数行の R セルを実行できるようになりました
  + [I trust contents of this file]\(このファイルの内容を信頼しています\) が、2 回目以降は自動でオンになるようになりました
  + 新しい [コピーの作成] オプションにより、[競合の解決] ダイアログの機能が強化されました
  
## <a name="2020-08-17"></a>2020-08-17

### <a name="azure-machine-learning-sdk-for-python-v1120"></a>Azure Machine Learning SDK for Python v1.12.0

+ **バグの修正と機能強化**
  + **azure-cli-ml**
    + ビルドしたパッケージ イメージの名前を変更できるよう、image_name と image_label パラメーターを Model.package() に追加しました。
  + **azureml-automl-core**
    + AutoML で、読み取り中にコンテンツが変更されたときに、dataprep から新しいエラー コードが生成されるようになりました。
  + **azureml-automl-runtime**
    + データに欠損値が含まれているが、特徴量化がオフになっている場合に表示されるユーザー アラートを追加しました。
    + データに NAN が含まれ、特徴量化がオフになっている場合に発生する子実行のエラーを修正しました。
    + AutoML で、読み取り中にコンテンツが変更されたときに、dataprep から新しいエラー コードが生成されるようになりました。
    + 予測メトリックの正規化をグレイン単位で行うよう更新しました。
    + ルックバック機能が無効になっている場合の予測分位点の計算を改善しました。
    + AutoML の後に説明を計算する際のブール疎行列の処理を修正しました。
  + **azureml-core**
    + 新しいメソッド `run.get_detailed_status()` で、現在の実行状態の詳細な説明が表示されるようになりました。 現在は、`Queued` 状態の説明のみが表示されています。
    + ビルドしたパッケージ イメージの名前を変更できるよう、image_name と image_label パラメーターを Model.package() に追加しました。
    + [`CondaDependencies`](/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies) の PIP セクション全体を一度に設定するための新しいメソッド `set_pip_requirements()`。
    + 資格情報のない ADLS Gen2 データストアを登録できるようにしました。
    + 不適切なデータセットの種類をダウンロードまたはマウントしようとした時のエラー メッセージを改善しました。
    + 時系列データセット フィルター サンプル ノートブックに、フィルターを最適化する partition_timestamp の例を追加して更新しました。
    + プライベート エンドポイント接続を削除するときに、ArmResourceId ではなく、パラメーターとして subscriptionId、resourceGroup、workspaceName、peConnectionName を受け入れるよう SDK と CLI を変更しました。
    + 簡単に見分けられるよう、実験的な Decorator にクラス名が表示されるようになりました。
    + モデル内のアセットの説明が、実行に基づいて自動的に生成されなくなりました。
  + **azureml-datadrift**
    + DataDriftDetector の create_from_model API を非推奨としてマークしました。
  + **azureml-dataprep**
    + 不適切なデータセットの種類をダウンロードまたはマウントしようとした時のエラー メッセージを改善しました。
  + **azureml-pipeline-core**
    + 登録されたデータセットを含むパイプライン グラフを逆シリアル化した時に発生するバグを修正しました。
  + **azureml-pipeline-steps**
    + RScriptStep で azureml.core.environment からの RSection がサポートされるようになりました。
    + `AutoMLStep` パブリック API から passthru_automl_config パラメーターを削除し、これを内部専用のパラメーターに変換しました。
  + **azureml-train-automl-client**
    + 非同期のローカルにあるマネージド環境の実行を、AutoML から削除しました。 すべてのローカル実行は、実行が開始された環境で実行されます。
    + ユーザー指定のスクリプトを使用せずに AutoML 実行を送信した時に発生するスナップショットに関する問題を修正しました。
    + データに NAN が含まれ、特徴量化がオフになっている場合に発生する子実行のエラーを修正しました。
  + **azureml-train-automl-runtime**
    + AutoML で、読み取り中にコンテンツが変更されたときに、dataprep から新しいエラー コードが生成されるようになりました。
    + ユーザー指定のスクリプトを使用せずに AutoML 実行を送信した時に発生するスナップショットに関する問題を修正しました。
    + データに NAN が含まれ、特徴量化がオフになっている場合に発生する子実行のエラーを修正しました。
  + **azureml-train-core**
    + `pip_requirements_file` パラメーターを通じて [`Estimator`](/python/api/azureml-train-core/azureml.train.estimator.estimator) に渡される PIP の要件ファイル内の PIP オプション (例: --extra-index-url) を指定するためのサポートを追加しました。


## <a name="2020-08-03"></a>2020-08-03

### <a name="azure-machine-learning-sdk-for-python-v1110"></a>Azure Machine Learning SDK for Python v1.11.0

+ **バグの修正と機能強化**
  + **azure-cli-ml**
    + モデル フレームワークおよび CLI モデル登録パスの実行オブジェクトに渡されないモデル フレームワークを修正しました
    + テナント ID とプリンシパル ID を表示するように CLI amlcompute identity show コマンドを修正しました 
  + **azureml-train-automl-client**
    + 関連付けられたモデルをダウンロードせずに AutoML の実行に最適な子の実行をフェッチするための get_best_child () を AutoMLRun に追加しました。
    + ローカル環境にモデルをダウンロードすることなく、リモート トレーニング環境で予測を実行できる ModelProxy オブジェクトが追加されました。
    + AutoML でのハンドルされない例外で、エラーの詳細がわかる既知の問題の HTTP ページがポイントされるようになりました。
  + **azureml-core**
    + モデル名は 255 文字の長さにすることができます。
    + Environment.get_image_details() で返されるオブジェクトの型が変更されました。 `DockerImageDetails` クラスは `dict` に置き換えられました。イメージの詳細は、新しいクラスのプロパティから入手できます。 変更には下位互換性があります。
    + 依存関係の構造を保持するように、Environment.from_pip_requirements() のバグが修正されました
    + int と double が同じリストに含まれている場合に log_list が失敗するバグを修正しました。
    + 既存のワークスペースでプライベート リンクを有効にするときは、ワークスペースに関連付けられたコンピューティング先が存在するかどうかに注意してください。そのようなターゲットは、ワークスペースのプライベート エンドポイントと同じ仮想ネットワークの内側にないと機能しません。
    + 実験でデータセットを使用するときに `as_named_input` を省略可能にし、`as_mount` と `as_download` を `FileDataset` に追加しました。 `as_mount` または `as_download` が呼び出されると、入力名が自動的に生成されます。
  + **azureml-automl-core**
    + AutoML でのハンドルされない例外で、エラーの詳細がわかる既知の問題の HTTP ページがポイントされるようになりました。
    + 関連付けられたモデルをダウンロードせずに AutoML の実行に最適な子の実行をフェッチするための get_best_child () を AutoMLRun に追加しました。
    + ローカル環境にモデルをダウンロードすることなく、リモート トレーニング環境で予測を実行できる ModelProxy オブジェクトが追加されました。
  + **azureml-pipeline-steps**
    + `AutoMLStep` に `enable_default_model_output` フラグと `enable_default_metrics_output` フラグを追加しました。 これらのフラグを使用して、既定の出力を有効または無効にできます。


## <a name="2020-07-20"></a>2020-07-20

### <a name="azure-machine-learning-sdk-for-python-v1100"></a>Azure Machine Learning SDK for Python v1.10.0

+ **バグの修正と機能強化**
  + **azureml-automl-core**
    + AutoML を使用するとき、パスが AutoMLConfig オブジェクトに渡され、それがまだ存在していない場合は、自動的に作成されます。
    + ユーザーは、`freq` パラメーターを使用して、予測タスクの時系列の頻度を指定できるようになりました。
  + **azureml-automl-runtime**
    + AutoML を使用するとき、パスが AutoMLConfig オブジェクトに渡され、それがまだ存在していない場合は、自動的に作成されます。
    + ユーザーは、`freq` パラメーターを使用して、予測タスクの時系列の頻度を指定できるようになりました。
    + AutoML の予測で、ローリング評価がサポートされるようになりました。これは、テストまたは検証セットの長さが入力期間より長く、既知の y_pred 値が予測コンテキストとして使用される場合に適用されます。
  + **azureml-core**
    + 実行でデータストアからファイルがダウンロードされなかった場合、警告メッセージが出力されます。
    + `skip_validation` のドキュメントが `Datastore.register_azure_sql_database method` に追加されました。
    + 自動承認されるプライベート エンドポイントを作成するには、ユーザーは、SDK v1.10.0 以降にアップグレードする必要があります。 これには、VNet の背後で使用できる Notebook リソースが含まれます。
    + ワークスペースの取得の応答で、NotebookInfo が公開されます。
    + コンピューティング先一覧表示およびコンピューティング先取得の呼び出しがリモート実行で成功するように変更されました。 コンピューティング先取得およびワークスペース コンピューティング先一覧表示の Sdk 関数が、リモート実行で動作するようになりました。
    + azureml.core.image クラスの説明に非推奨のメッセージが追加されました。
    + ワークスペースのプライベート エンドポイントの作成が失敗した場合は、例外をスローし、ワークスペースと依存リソースをクリーンアップします。
    + ワークスペース更新メソッドでのワークスペース sku アップグレードがサポートされるようになりました。
  + **azureml-datadrift**
    + Python 3.8 をサポートするため、matplotlib のバージョンが 3.0.2 から 3.2.1 に更新されました。
  + **azureml-dataprep**
    + `Range` または `Head` 要求での Web URL データ ソースのサポートが追加されました。 
    + ファイル データセットのマウントとダウンロードの安定性が向上しました。
  + **azureml-train-automl-client**
    + setuptools からの `RequirementParseError` の削除に関する問題が修正されました。
    + "compute_target='local'" を使用して送信されたローカル実行に、Conda ではなく Docker が使用されるようになりました
    + コンソールに出力されるイテレーションの期間が修正されました。 以前は、イテレーションの期間は、実行終了時刻から実行作成時刻を引いたものとして出力されることがありました。 それが、実行終了時刻から実行開始時刻を引いた値に修正されました。
    + AutoML を使用するとき、パスが AutoMLConfig オブジェクトに渡され、それがまだ存在していない場合は、自動的に作成されます。
    + ユーザーは、`freq` パラメーターを使用して、予測タスクの時系列の頻度を指定できるようになりました。
  + **azureml-train-automl-runtime**
    + 最適なモデルの説明が失敗したときのコンソール出力が向上しました。
    + 微妙な用語を削除するために、入力パラメーターの名前が "blocked_models" に変更されました。
      + 微妙な用語を削除するために、入力パラメーターの名前が "allowed_models" に変更されました。
    + ユーザーは、`freq` パラメーターを使用して、予測タスクの時系列の頻度を指定できるようになりました。

  
## <a name="2020-07-06"></a>2020-07-06

### <a name="azure-machine-learning-sdk-for-python-v190"></a>Azure Machine Learning SDK for Python v1.9.0

+ **バグの修正と機能強化**
  + **azureml-automl-core**
    + AutoML 自動生成スコアリング スクリプトの get_model_path() を AZUREML_MODEL_DIR 環境変数で置き換えました。 init() 中に発生したエラーを追跡するテレメトリも追加しました。
    + AutoMLConfig の一部として `enable_cache` を指定する機能を削除しました。
    + 特定の予測を実行中にサービス エラーが発生して実行が失敗する可能性のあるバグを修正しました。
    + `get_output` での特定のモデルに関するエラー処理を改善しました。
    + y トランスフォーマーで分類するための fitted_model (X, y) の呼び出しを修正しました。
    + 予測タスクのためのカスタマイズ事前入力を有効にしました
    + 新しい ForecastingParameters クラスが、辞書形式の予測パラメーターの代わりに使用されます。
    + ターゲットのラグの自動検出を改善しました。
    + BERT を使用したマルチノードおよびマルチ GPU の分散特徴量化が制限付きで使用できるようになりました。
  + **azureml-automl-runtime**
    + Prophet は、乗法ではなく加法の季節性モデリングを行うようになりました。
    + 短いグレインの間隔が長いグレインと異なるときに実行が失敗する問題を修正しました。
  + **azureml-contrib-automl-dnn-vision**
    + トレーニングとスコアリングのためにシステム/GPU 統計およびログ平均を収集します。
  + **azureml-contrib-mir**
    + ManagedInferencing の enable-app-insights フラグのサポートを追加しました。
  + **azureml-core**
    + 現在のコンピューティングからデータ ソースにアクセスできないときに検証をスキップできるようにすることで、これらの API に対してパラメーターを検証します。
      + TabularDataset.time_before(end_time, include_boundary=True, validate=True)
      + TabularDataset.time_after(start_time, include_boundary=True, validate=True)
      + TabularDataset.time_recent(time_delta, include_boundary=True, validate=True)
      + TabularDataset.time_between(start_time, end_time, include_boundary=True, validate=True)
    + モデル リストのフレームワーク フィルター処理のサポートを追加し、ノートブックに NCD AutoML サンプルを再び追加しました
    + Datastore.register_azure_blob_container および Datastore.register_azure_file_share (SAS トークンをサポートするオプションのみ) で、`sas_token` フィールドの doc 文字列を更新し、一般的な読み取りと書き込みのシナリオに必要な最小限のアクセス許可を追加しました。
    + ws.get_mlflow_tracking_uri() の _with_auth パラメーターが非推奨になりました。
  + **azureml-mlflow**
    + ローカルの file:// モデルを AzureML-MLflow にデプロイするためのサポートを追加しました。
    + ws.get_mlflow_tracking_uri() の _with_auth パラメーターが非推奨になりました。
  + **azureml-opendatasets**
    + 最近公開された Covid-19 の追跡データセットが、SDK で使用できるようになりました。
  + **azureml-pipeline-core**
    + "azureml-defaults" が pip 依存関係の一部として含まれていない場合のログアウト警告
    + ノートの表示を改善しました。
    + 区切られたファイルを PipelineOutputFileDataset に解析する際の引用符で囲まれた改行のサポートを追加しました。
    + PipelineDataset クラスは非推奨になりました。 詳細については、 https://aka.ms/dataset-deprecation を参照してください。 パイプラインでデータセットを使用する方法については、「 https://aka.ms/pipeline-with-dataset 」を参照してください。
  + **azureml-pipeline-steps**
    + azureml-pipeline-steps のドキュメントを更新しました。
    +  ユーザーが環境をその他の構成と一緒にインラインで定義するか、別のファイルに定義できるように、ParallelRunConfig の `load_yaml()` のサポートを追加しました。
  + **azureml-train-automl-client**.
    + AutoMLConfig の一部として `enable_cache` を指定する機能を削除しました。
  + **azureml-train-automl-runtime**
    + BERT を使用したマルチノードおよびマルチ GPU の分散特徴量化が制限付きで使用できるようになりました。
    + ADB ベースの自動機械学習の実行に互換性がないパッケージのエラー処理を追加しました。
  + **azureml-widgets**
    + azureml-widgets のドキュメントを更新しました。

  
## <a name="2020-06-22"></a>2020-06-22

### <a name="azure-machine-learning-sdk-for-python-v180"></a>Azure Machine Learning SDK for Python v1.8.0
  
  + **プレビュー機能**
    + **azureml-contrib-fairness** `azureml-contrib-fairness` パッケージによって、公平性を評価して不公平を軽減するオープンソース パッケージ [Fairlearn](https://fairlearn.github.io) と Azure Machine Learning Studio が統合されます。 具体的には、このパッケージによって、モデル公平性評価ダッシュボードを AzureML 実行の一部としてアップロードし、Azure Machine Learning Studio に表示できるようになります。

+ **バグの修正と機能強化**
  + **azure-cli-ml**
    + init コンテナーのログの取得をサポートします。
    + ComputeInstance を管理する新しい CLI コマンドを追加しました。
  + **azureml-automl-core**
    + ユーザーは、時系列タスクのスタック アンサンブル イテレーションを有効化できるようになりましたが、オーバーフィットの可能性があると警告されます。
    + キャッシュ ストアの内容が改ざんされた場合に発生する新しい種類のユーザー例外を追加しました
  + **azureml-automl-runtime**
    + ユーザーが特徴量化を無効にした場合に、クラス分散スイープを有効化できなくなりました。  
  + **azureml-contrib-itp**
    + CmAks コンピューティングの種類がサポートされます。 トレーニング ジョブのワークスペースに独自の AKS クラスターをアタッチできます。
  + **azureml-contrib-notebook**
    + azureml-contrib-notebook パッケージのドキュメントを改善しました。
  + **azureml-contrib-pipeline-steps**
    + azureml-contrib--pipeline-steps パッケージのドキュメントを改善しました。
  + **azureml-core**
    + ユーザーがワークスペース接続リソースを操作するために、set_connection、get_connection、list_connections、delete_connection の関数を追加しました。
    + azureml-coore/azureml.exceptions パッケージのドキュメントを更新しました。
    + azureml-core パッケージのドキュメントを更新しました。
    + ComputeInstance クラスのドキュメントを更新しました。
    + azureml-core/azureml.core.compute パッケージのドキュメントを改善しました。
    + azureml-core の webservice 関連クラスのドキュメントを改善しました。
    + ユーザーが選択したデータストアへのプロファイル データの格納をサポートします
    + モデル リスト API の expand および page_count プロパティを追加しました。
    + 上書きプロパティを削除すると送信済みの実行が逆シリアル化エラーで失敗するバグを修正しました。
    + 1 つのファイルを参照している FileDataset をダウンロードまたはマウントする際の一貫性のないフォルダー構造を修正しました。
    + parquet ファイルのデータセットの to_spark_dataframe への読み込みが高速になり、すべての parquet および Spark SQL データ型がサポートされるようになりました。
    + init コンテナーのログの取得をサポートします。
    + AutoML 実行は、ParallelRunStep の子実行としてマークされるようになりました。
  + **azureml-datadrift**
    + azureml-contrib-notebook パッケージのドキュメントを改善しました。
  + **azureml-dataprep**
    + parquet ファイルのデータセットの to_spark_dataframe への読み込みが高速になり、すべての parquet および Spark SQL データ型がサポートされるようになりました。
    + to_pandas_dataframe の OutOfMemory の問題に対するメモリ処理を改善しました。
  + **azureml-interpret**
    + azureml-interpret が interpret-community version 0.12 を使用するようにアップグレードしました。*
  + **azureml-mlflow**
    + azureml-mlflow のドキュメントを改善しました。
    + MLFlow に対する AML モデル レジストリのサポートを追加しました。
  + **azureml-opendatasets**
    + Python 3.8 のサポートを追加しました
  + **azureml-pipeline-core**
    + 内部クラスであることが明確にするため `PipelineDataset` のドキュメントを更新しました。
    + 1 つの引数で複数の値を受け取るように ParallelRunStep を更新しました。例: "--group_column_names", "Col1", "Col2", "Col3"
    + Pipelines での AutoMLStep による中間データ使用のための passthru_automl_config 要件を削除しました。
  + **azureml-pipeline-steps**
    + azureml-pipeline-steps パッケージのドキュメントを改善しました。
    + Pipelines での AutoMLStep による中間データ使用のための passthru_automl_config 要件を削除しました。
  + **azureml-telemetry**
    + azureml-telemetry のドキュメントを改善しました。
  + **azureml-train-automl-client**
    + `AutoMLConfig` オブジェクトで `experiment.submit()` が 2 回呼び出されたときに動作が異なるバグを修正しました。
    + ユーザーは、時系列タスクのスタック アンサンブル イテレーションを有効化できるようになりましたが、オーバーフィットの可能性があると警告されます。
    + サービスがユーザー エラーをスローした場合に UserErrorException を発生させるように AutoML の実行動作を変更しました。
    + リモート コンピューティング ターゲットで AutoML 実験を実行するときに、azureml_automl.log が生成されない、またはログが見つからない原因のバグを修正しました。
    + 不均衡なクラスを含む分類データ セットについて、サブサンプリング データに対して特徴スウィーパーによりそれが判別された場合に、重み分散が適用されます。重み分散によって、分類タスクのパフォーマンスが一定のしきい値まで向上します。
    + AutoML 実行は、ParallelRunStep の子実行としてマークされるようになりました。
  + **azureml-train-automl-runtime**
    + サービスがユーザー エラーをスローした場合に UserErrorException を発生させるように AutoML の実行動作を変更しました。
    + AutoML 実行は、ParallelRunStep の子実行としてマークされるようになりました。

  
## <a name="2020-06-08"></a>2020-06-08

### <a name="azure-machine-learning-sdk-for-python-v170"></a>Azure Machine Learning SDK for Python v1.7.0

+ **バグの修正と機能強化**
  + **azure-cli-ml**
    + CLI のコマンドおよびパッケージの依存関係をクリーンアップして、mir contrib からのモデル プロファイルの削除が完了しました。モデル プロファイルはコアで利用できます。
    + 最小 Azure CLI バージョンを 2.3.0 にアップグレードしました
  + **azureml-automl-core**
    + 特徴量化ステップ fit_transform() のカスタム トランスフォーマー パラメーターによる例外メッセージを改善しました。
    + 自動 ML にディープ ラーニング トランスフォーマー モデル ( BERT など) の複数言語のサポートを追加しました。
    + 非推奨の lag_length パラメーターをドキュメントから削除しました。
    + 予測パラメーターのドキュメントを改善しました。 lag_length パラメーターが非推奨になりました。
  + **azureml-automl-runtime**
    + 予測/テスト時にカテゴリ列の 1 つが空の場合に発生するエラーを修正しました。
    + ルックバック機能が有効になっているときにデータに短いグレインが含まれると発生する実行エラーを修正しました。
    + ラグすなわちローリング ウィンドウが 'auto' に設定された場合に、重複時間インデックスのエラー メッセージが表示される問題を修正しました。
    + データ セットに対する Prophet および Arima モデル (ルックバック機能を含む) の問題を修正しました。
    + 予測タスクにおいて日付時刻列以外の列での、1677-09-21 よりも前、または 2262-04-11 よりも後の日付のサポートを追加しました。 エラー メッセージが改善されました。
    + 予測パラメーターのドキュメントを改善しました。 lag_length パラメーターが非推奨になりました。
    + 特徴量化ステップ fit_transform() のカスタム トランスフォーマー パラメーターによる例外メッセージを改善しました。
    + 自動 ML にディープ ラーニング トランスフォーマー モデル ( BERT など) の複数言語のサポートを追加しました。
    + OSErrors が生成されるキャッシュ操作によってユーザー エラーが発生します。
    + トレーニング データと検証データの列が同数で同じ組み合わせであることを確認するチェックを追加しました。
    + データに引用符が含まれる場合の自動生成 AutoML スコアリング スクリプトに関する問題を修正しました。
    + AutoML Prophet および Prophet モデルを含むアンサンブル モデルの説明を有効にします。
    + 最近の顧客の問題によって、クラス分散ロジックが適切に有効化されていないときでさえ、Class-Balancing-Sweeping に合わせてメッセージが記録されるというライブサイトのバグが明らかになりました。 この PR ではそれらのログ/メッセージを削除しています。
  + **azureml-cli-common**
    + CLI のコマンドおよびパッケージの依存関係をクリーンアップして、mir contrib からのモデル プロファイルの削除が完了しました。モデル プロファイルはコアで利用できます。
  + **azureml-contrib-reinforcementlearning**
    + ロード テスト ツール
  + **azureml-core**
    + Script_run_config.py についてドキュメントを変更しました。
    + submit-pipeline CLI の実行の出力を印刷する際のバグを修正しました。
    + azureml-core/azureml.data のドキュメントを改善しました。
    + hdfs getconf コマンドを使用してストレージ アカウントを取得する際の問題を修正しました。
    + register_azure_blob_container および register_azure_file_share のドキュメントを改善しました。
  + **azureml-datadrift**
    + データセット ドリフト モニターの無効化および有効化の実装を改善しました。
  + **azureml-interpret**
    + 説明クライアントで、成果物からアップロードする際に、json シリアル化の前の NaN または Inf を削除しました。
    + 多くの機能とクラスを含むグローバル説明のメモリ不足エラーを改善するため、interpret-community の最新バージョンに更新しました。
    + スタジオ UI のその他の機能を有効にするため、説明アップロードに true_ys オプション パラメーターを追加しました。
    + download_model_explanations() および list_model_explanations() のパフォーマンスを改善しました。
    + デバッグに役立つようにノートブックを微調整しました。
  + **azureml-opendatasets**
    + azureml-opendatasets には azureml-dataprep バージョン 1.4.0 以上が必要です。 下位バージョンが検出された場合の警告を追加しました。
  + **azureml-pipeline-core**
    + この変更によって、ユーザーが module.Publish_python_script を呼び出すときにオプションの runconfig を moduleVersion に指定できるようになります。
    + ノード アカウントは、azureml.pipeline.steps で ParallelRunStep のパイプライン パラメーターにすることができます。
  + **azureml-pipeline-steps**
    + この変更によって、ユーザーが module.Publish_python_script を呼び出すときにオプションの runconfig を moduleVersion に指定できるようになります。
  + **azureml-train-automl-client**
    + 自動 ML にディープ ラーニング トランスフォーマー モデル ( BERT など) の複数言語のサポートを追加しました。
    + 非推奨の lag_length パラメーターをドキュメントから削除しました。
    + 予測パラメーターのドキュメントを改善しました。 lag_length パラメーターが非推奨になりました。
  + **azureml-train-automl-runtime**
    + AutoML Prophet および Prophet モデルを含むアンサンブル モデルの説明を有効にします。
    + azureml-train-automl-* パッケージのドキュメントを更新しました。
  + **azureml-train-core**
    + PyTorch Estimator で TensorFlow バージョン 2.1 のサポート
    + azureml-train-core パッケージを改善しました。
  
## <a name="2020-05-26"></a>2020-05-26

### <a name="azure-machine-learning-sdk-for-python-v160"></a>Azure Machine Learning SDK for Python v1.6.0

+ **新機能**
  + **azureml-automl-runtime**
    + AutoML の予測で、モデルを再トレーニングしなくても、事前に指定した期間の最大値を超える顧客の予測がサポートされるようになりました。 予測の対象が指定した期間の最大値よりも未来になる場合でも、forecast() 関数によって、再帰的操作モードを使用してそれ以降の日付に対してポイント予測が作成されます。 この新機能の説明については、[フォルダー](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)内の "forecasting-forecast-function" ノートブックの「期間の最大値を超える予測」セクションを参照してください。"
  
  + **azureml-pipeline-steps**
    + ParallelRunStep がリリースされ、**azureml-pipeline-steps** パッケージに含まれるようになりました。 **azureml-contrib-pipeline-steps** パッケージに含まれる既存の ParallelRunStep は非推奨になります。 パブリック プレビュー バージョンからの変更点は次のとおりです。
      + 指定した任意のバッチに対してメソッドを実行する最大呼び出しを制御するための、省略可能な構成可能パラメーター `run_max_try` が追加されました (既定値は 3)。
      + PipelineParameter が自動生成されなくなりました。 次の構成可能な値は、PipelineParameter として明示的に設定できます。
        + mini_batch_size
        + node_count
        + process_count_per_node
        + logging_level
        + run_invocation_timeout
        + run_max_try
      + process_count_per_node の既定値が 1 に変更されます。 パフォーマンスを向上させるには、ユーザーがこの値を調整する必要があります。 ベスト プラクティスは、GPU または CPU ノードが持つ数として設定することです。
      + ParallelRunStep ではパッケージが挿入されません。ユーザーが **azureml-core** および **azureml-dataprep[pandas, fuse]** パッケージを環境定義に含める必要があります。 user_managed_dependencies と共にカスタム Docker イメージを使用する場合は、ユーザーがイメージに Conda をインストールする必要があります。
      
+ **重大な変更**
  + **azureml-pipeline-steps**
    + AutoMLConfig に対する有効な種類の入力としての azureml.dprep.Dataflow の使用が非推奨になりました
  + **azureml-train-automl-client**
    + AutoMLConfig に対する有効な種類の入力としての azureml.dprep.Dataflow の使用が非推奨になりました

+ **バグの修正と機能強化**
  + **azureml-automl-core**
    + ユーザーにクライアントのダウングレードを要求した `get_output` 中に、警告が出力されることがあるバグを修正しました。
    + cudatoolkit=9.0 に依存するように Mac を更新しました (バージョン 10 ではまだ使用できないため)。
    + リモート コンピューティングでトレーニングを行ったときの prophet および xgboost モデルに対する制限が削除されます。
    + AutoML でのログ記録が改善されました
    + 予測タスクにおけるカスタム特徴量化のエラー処理が改善されました。
    + ユーザーがタイム ラグ機能を含めて予測を生成できるようにする機能が追加されました。
    + ユーザー エラーを正しく表示するためのエラー メッセージに対する更新。
    + training_data と共に使用される cv_split_column_names のサポート
    + 例外メッセージとトレースバックのログ記録が更新されます。
  + **azureml-automl-runtime**
    + 欠落値の補完を予測するためのガードレールが有効になります。
    + AutoML でのログ記録が改善されました
    + データ準備例外に対する詳細なエラー処理を追加しました
    + リモート コンピューティングでトレーニングを行ったときの phrophet および xgboost モデルに対する制限が削除されます。
    + `azureml-train-automl-runtime` および `azureml-automl-runtime` の `pytorch`、`scipy`、および `cudatoolkit` に対する依存関係が更新されました。 `pytorch==1.4.0`、`scipy>=1.0.0,<=1.3.1`、および `cudatoolkit==10.1.243` がサポートされるようになりました。
    + 予測タスクにおけるカスタム特徴量化のエラー処理が改善されました。
    + データセットの予測頻度の検出メカニズムが改善されました。
    + 一部のデータセットでトレーニングする Prophet モデルに関する問題を修正しました。
    + 予測中の期間の最大値の自動検出が改善されました。
    + ユーザーがタイム ラグ機能を含めて予測を生成できるようにする機能が追加されました。
    +  予測関数に機能が追加され、予測モデルを再トレーニングすることなくトレーニングされた期間を超えて予測を提供できるようになります。
    + training_data と共に使用される cv_split_column_names のサポート
  + **azureml-contrib-automl-dnn-forecasting**
    + AutoML でのログ記録が改善されました
  + **azureml-contrib-mir**
    + ManagedInferencing に Windows サービスのサポートが追加されます
    + MIR コンピューティングのアタッチ、SingleModelMirWebservice クラスなどの古い MIR ワークフローが削除されます。contrib-mir パッケージに配置されているモデルのプロファイルが削除されます
  + **azureml-contrib-pipeline-steps**
    + YAML サポートに対するマイナー修正
    + ParallelRunStep が一般提供としてリリースされます。azureml.contrib.pipeline.steps に非推奨の通知があり、azureml.pipeline.steps に移行されます
  + **azureml-contrib-reinforcementlearning**
    + RL ロード テスト ツール
    + RL 推定器でのスマートな既定値の使用
  + **azureml-core**
    + MIR コンピューティングのアタッチ、SingleModelMirWebservice クラスなどの古い MIR ワークフローが削除されます。contrib-mir パッケージに配置されているモデルのプロファイルが削除されます
    + プロファイル エラーが発生した場合にユーザーに表示される情報を修正しました。要求 ID が記載され、メッセージがよりわかりやすく変更されました。 プロファイル ランナーに新しいプロファイル ワークフローが追加されました
    + データセットの実行エラーが発生した場合のエラー テキストが改善されました。
    + ワークスペースのプライベート リンク CLI のサポートが追加されました。
    + 省略可能なパラメーター `invalid_lines` が `Dataset.Tabular.from_json_lines_files` に追加されました。これにより、無効な JSON を含む行の処理方法を指定できます。
    + 次のリリースでは、コンピューティングの実行ベースの作成が非推奨になります。 実際の Amlcompute クラスターを永続的なコンピューティング先として作成し、そのクラスター名を実行構成でのコンピューティング先として使用することをお勧めします。 こちらのノートブックの例を参照してください: aka.ms/amlcomputenb
    + データセットの実行エラーが発生した場合のエラー メッセージが改善されました。
  + **azureml-dataprep**
    + pyarrow バージョンのアップグレードに対する警告がより明示的になりました。
    + データフローの実行に失敗した場合のエラー処理と返されるメッセージが改善されました。
  + **azureml-interpret**
    + azureml-interpret パッケージのドキュメントが更新されます。
    + 解釈可能パッケージとノートブックが最新の sklearn 更新プログラムと互換性を持つように修正されました
  + **azureml-opendatasets**
    + 返されるデータがない場合に None が返されます。
    + to_pandas_dataframe のパフォーマンスが改善されます。
  + **azureml-pipeline-core**
    + YAML からの読み込みが破損していた ParallelRunStep のクイック修正
    + ParallelRunStep が一般提供としてリリースされます。azureml.contrib.pipeline.steps に非推奨の通知があり、azureml.pipeline.steps に移行されます。次のような新機能があります。1. PipelineParameter としての データセット 2. 新しいパラメーター run_max_retry 3. 構成可能な append_row 出力ファイル名
  + **azureml-pipeline-steps**
    + 入力データの有効な種類として azureml.dprep.Dataflow を非推奨にしました。
    + YAML からの読み込みが破損していた ParallelRunStep のクイック修正
    + ParallelRunStep が一般提供としてリリースされます。azureml.contrib.pipeline.steps に非推奨の通知があり、azureml.pipeline.steps に移行されます。次のような新機能があります。
      + PipelineParameter としての データセット
      + 新しいパラメーター run_max_retry
      + 構成可能な append_row 出力ファイル名
  + **azureml-telemetry**
    + 例外メッセージとトレースバックのログ記録が更新されます。
  + **azureml-train-automl-client**
    + AutoML でのログ記録が改善されました
    + ユーザー エラーを正しく表示するためのエラー メッセージに対する更新。
    + training_data と共に使用される cv_split_column_names のサポート
    + 入力データの有効な種類として azureml.dprep.Dataflow を非推奨にしました。
    + cudatoolkit=9.0 に依存するように Mac を更新しました (バージョン 10 ではまだ使用できないため)。
    + リモート コンピューティングでトレーニングを行ったときの phrophet および xgboost モデルに対する制限が削除されます。
    + `azureml-train-automl-runtime` および `azureml-automl-runtime` の `pytorch`、`scipy`、および `cudatoolkit` に対する依存関係が更新されました。 `pytorch==1.4.0`、`scipy>=1.0.0,<=1.3.1`、および `cudatoolkit==10.1.243` がサポートされるようになりました。
    + ユーザーがタイム ラグ機能を含めて予測を生成できるようにする機能が追加されました。
  + **azureml-train-automl-runtime**
    + AutoML でのログ記録が改善されました
    + データ準備例外に対する詳細なエラー処理を追加しました
    + リモート コンピューティングでトレーニングを行ったときの phrophet および xgboost モデルに対する制限が削除されます。
    + `azureml-train-automl-runtime` および `azureml-automl-runtime` の `pytorch`、`scipy`、および `cudatoolkit` に対する依存関係が更新されました。 `pytorch==1.4.0`、`scipy>=1.0.0,<=1.3.1`、および `cudatoolkit==10.1.243` がサポートされるようになりました。
    + ユーザー エラーを正しく表示するためのエラー メッセージに対する更新。
    + training_data と共に使用される cv_split_column_names のサポート
  + **azureml-train-core**
    + 新しい HyperDrive 固有の例外セットが追加されました。 azureml.train.hyperdrive で詳細な例外がスローされるようになります。
  + **azureml-widgets**
    + AzureML ウィジェットが JupyterLab で表示されません
  

## <a name="2020-05-11"></a>2020-05-11

### <a name="azure-machine-learning-sdk-for-python-v150"></a>Azure Machine Learning SDK for Python v1.5.0

+ **新機能**
  + **プレビュー機能**
    + **azureml-contrib-reinforcementlearning**
        + Azure Machine Learning は、[Ray](https://ray.io) フレームワークを使用した強化学習のプレビュー サポートをリリースします。 `ReinforcementLearningEstimator` を使用すると、Azure Machine Learning の GPU および CPU コンピューティング先全体にわたり、強化学習エージェントをトレーニングできます。

+ **バグの修正と機能強化**
  + **azure-cli-ml**
    + 以前の PR で誤って残されていた警告ログを修正しました。 ログはデバッグに使用されたもので、誤って残されていました。
    + バグの修正: プロファイリング中の部分的な障害についてクライアントに通知します
  + **azureml-automl-core**
    + データセットに複数の時系列が含まれている場合に時系列の並列調整を有効にすることで、AutoML の予測で Prophet/AutoArima モデルが高速化されます。 この新機能を活用するために、AutoMLConfig で "max_cores_per_iteration =-1" (使用可能なすべての CPU コアを使用) を設定することをお勧めします。
    + コンソール インターフェイスの印刷ガードレールのキー エラーを修正しました
    + Experimentation_timeout_hours のエラーメッセージを修正しました
    + AutoML の Tensorflow モデルを非推奨にしました。
  + **azureml-automl-runtime**
    + Experimentation_timeout_hours のエラーメッセージを修正しました
    + キャッシュ ストアから逆シリアル化しようとしたときの未分類の例外を修正しました
    + データセットに複数の時系列が含まれている場合に時系列の並列調整を有効にすることで、AutoML の予測で Prophet/AutoArima モデルが高速化されます。
    + テスト/予測セットにトレーニング セットのグレインのいずれかが含まれていないデータ セットで、有効なローリング ウィンドウを使用した予測を修正しました。
    + 欠損データの処理を改善しました
    + 時系列を含むデータ セットの予測時の予測間隔に関する問題を修正しました。予測間隔は時間に沿っていませんでした。
    + 予測タスクのデータ シェイプの検証が向上しました。
    + 頻度の検出が向上しました。
    + 予測タスクのクロス検証の分割が生成されない場合に、より適切なエラー メッセージが作成されるようになりました。
    + 欠損値ガードレールを正しく印刷するために、コンソール インターフェイスを修正しました。
    + AutoMLConfig の cv_split_indices 入力にデータ型チェックを適用しました。
  + **azureml-cli-common**
    + バグの修正: プロファイリング中の部分的な障害についてクライアントに通知します
  + **azureml-contrib-mir**
    + 現在展開されている MIR リビジョンと、ユーザーが指定した最新のバージョンに関する情報をリレーする azureml.contrib.mir.RevisionStatus クラスを追加しました。 このクラスは、'deployment_status' 属性の下にある MirWebservice オブジェクトに含まれています。
    + MirWebservice 型の Web サービスとその子クラス SingleModelMirWebservice の更新を有効にしました。
  + **azureml-contrib-reinforcementlearning**
    + Ray 0.8.3 のサポートを追加しました
    + AmlWindowsCompute は、マウントされたストレージとして Azure Files のみをサポートします
    + Health_check_timeout を health_check_timeout_seconds に名称変更しました
    + いくつかのクラス/メソッドの説明を修正しました。
  + **azureml-core**
    + Azure Government および China のクラウドで WASB から Blob への変換が有効になりました。
    + 閲覧者ロールで az ml run CLI コマンドを使用して実行情報を取得できるようにバグを修正しました
    + 入力データ セットを使用した Azure ML リモート実行中に行っていた不要なログ記録を廃止しました。
    + RCranPackage で、CRAN パッケージ バージョンの "version" パラメーターがサポートされるようになりました。
    + バグの修正: プロファイリング中の部分的な障害についてクライアントに通知します
    + azureml-core のヨーロッパ スタイルの float 処理を追加しました。
    + Azure ml sdk でワークスペースのプライベート リンク機能が有効になりました。
    + `from_delimited_files` を使用して TabularDataset を作成する場合、ブール型の引数 `empty_as_string` を設定して、空の値を None として読み込むか、空の文字列として読み込むかを指定できます。
    + データ セットのヨーロッパ スタイルの float 処理を追加しました。
    + データ セットのマウントのエラーに関するエラーメッセージが改善されました。
  + **azureml-datadrift**
    + SDK からのデータ ドリフトの結果クエリに、特徴量の最小値、最大値、平均値メトリックを区別しないバグがあり、その結果、重複する値が生成されていました。 メトリック名にターゲットかベースラインをプレフィックスとして付けることで、このバグを修正しました。 修正前: 最小値、最大値、平均値を複製。 修正後: target_min、target_max、target_mean、baseline_min、baseline_max、baseline_mean。
  + **azureml-dataprep**
    + データ配信に必要な .NET 依存関係を確認する際の、書き込み制限付き Python 環境の処理を改善しました。
    + 先頭に空のレコードを含むファイルでのデータフローの作成を修正しました。
    + `to_pandas_dataframe` に類似した `to_partition_iterator` のエラー処理オプションが追加されました。
  + **azureml-interpret**
    + Windows の制限を超える可能性を減らすために、説明パスの長さ制限を引き下げました
    + 線形サロゲートモデルを使用して Mimic Explainer で作成されたスパースの説明のバグを修正しました。
  + **azureml-opendatasets**
    + MNIST の列が文字列として解析される問題を修正します (int である必要があります)。
  + **azureml-pipeline-core**
    + ModuleStep に埋め込まれているモジュールを使用する場合に、出力を再生成できるオプションが許可されました。
  + **azureml-train-automl-client**
    + AutoML の Tensorflow モデルを非推奨にしました。
    + ローカル モードでユーザーがサポートされていないアルゴリズムを許可リストに登録できるバグを修正しました
    + AutoMLConfig のドキュメントを修正しました。
    + AutoMLConfig の cv_split_indices 入力にデータ型チェックを適用しました。
    + show_output での AutoML 実行に関する問題を修正しました
  + **azureml-train-automl-runtime**
    + モデル ダウンロードのタイムアウトの正常な動作を妨げていたアンサンブル イテレーションのバグを修正しました。
  + **azureml-train-core**
    + azureml.train.dnn.Nccl クラスのタイポを修正しました。
    + PyTorch Estimator での PyTorch バージョン 1.5 のサポート
    + トレーニング フレームワーク推定器を使用すると、Azure Government リージョンでフレームワーク イメージをフェッチできないという問題を修正しました

  
## <a name="2020-05-04"></a>2020-05-04
**新しいノートブック エクスペリエンス**

Azure Machine Learning の Studio Web エクスペリエンス内で、機械学習のノートブックとファイルを直接作成、編集、共有できるようになりました。 これらのノートブック内から、[Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro) で利用可能なすべてのクラスとメソッドを使用できます。[こちら](./how-to-run-jupyter-notebooks.md)から始めてください

**導入された新機能:**

+ VS Code によって使用される改良されたエディター (Monaco Editor) 
+ UI/UX の機能強化
+ セル ツールバー
+ 新しいノートブック ツールバーとコンピューティング コントロール
+ ノートブックのステータス バー 
+ インライン カーネル切り替え
+ R のサポート
+ アクセシビリティとローカライズの機能強化
+ コマンド パレット
+ 追加のキーボード ショートカット
+ 自動保存
+ パフォーマンスと信頼性の向上

Studio から、次の Web ベースの作成ツールにアクセスします。
    
| Web ベースのツール  |     説明  |
|---|---|
| Azure ML Studio ノートブック   |     ノートブック ファイル用の初のクラス内作成ツールであり、Azure ML Python SDK で利用可能なすべての操作をサポートします。 | 

## <a name="2020-04-27"></a>2020-04-27

### <a name="azure-machine-learning-sdk-for-python-v140"></a>Azure Machine Learning SDK for Python v1.4.0

+ **新機能**
  + AmlCompute クラスターで、プロビジョニング時にクラスターのマネージド ID の設定がサポートされるようになりました。 システムによって割り当てられた ID とユーザーによって割り当てられた ID のどちらを使用するかを指定するだけです。後者の場合は identityId を渡します。 次に、AmlCompute で現在採用されているトークンベースのアプローチではなく、データに安全にアクセスするためにコンピューティングの ID を使用する方法で、アクセス許可を設定して、ストレージや ACR などのさまざまなリソースにアクセスできます。 パラメーターの詳細については、SDK リファレンスを参照してください。
  

+ **重大な変更**
  + AmlCompute クラスターで、実行ベースの作成に関するプレビュー機能がサポートされましたが、2 週間後に非推奨になる予定です。 Amlcompute クラスを使用すると、常に永続的なコンピューティング ターゲットを作成し続けることができますが、実行構成でコンピューティング ターゲットとして識別子 "amlcompute" を指定するこの特殊なアプローチは、近い将来サポートされなくなります。 

+ **バグの修正と機能強化**
  + **azureml-automl-runtime**
    + 列内の一意の値の数を計算するときに、ハッシュ化できない型のサポートを有効にします。
  + **azureml-core**
    + TabularDataset を使用して Azure Blob Storage から読み取るときの安定性が向上しました。
    + `Datastore.register_azure_blob_store` の `grant_workspace_msi` パラメーターについてのドキュメントが改善されました。
    + `/` または `\` で終わる `src_dir` 引数をサポートする `datastore.upload` のバグを修正しました。
    + アクセス キーまたは SAS トークンがない Azure Blob Storage データトアにアップロードしようとしたときの実用的なエラーメッセージが追加されました。
  + **azureml-interpret**
    + アップロードされた説明について、視覚化データのファイル サイズに上限を追加しました。
  + **azureml-train-automl-client**
    + AutoMLConfig の label_column_name および weight_column_name パラメーターが文字列型であることを明示的にチェックします。
  + **azureml-contrib-pipeline-steps**
    + ParallelRunStep は、パイプライン パラメーターとしてデータセットをサポートするようになりました。 ユーザーは、サンプル データセットを使用してパイプラインを作成できます。また、新しいパイプラインの実行用に同じ種類 (ファイルまたはテーブル) の入力データセットを変更できます。

  
## <a name="2020-04-13"></a>2020-04-13

### <a name="azure-machine-learning-sdk-for-python-v130"></a>Azure Machine Learning SDK for Python v1.3.0

+ **バグの修正と機能強化**
  + **azureml-automl-core**
    + トレーニング後の操作に関するテレメトリが追加されました。
    + 100 より長いとき、CSS (Conditional Sum of Squares、条件付き平方和) を利用し、自動 ARIMA トレーニングを高速化します。 使用される長さは、/src/azureml-automl-core/azureml/automl/core/shared/constants.py で、TimeSeriesInternal クラス内に定数 ARIMA_TRIGGER_CSS_TRAINING_LENGTH として保存されます
    + 予測実行のユーザー ログが改善されました。現在実行されている段階に関してログに表示される情報が増えました
    + target_rolling_window_size を 2 未満の値に設定することが禁止されました
  + **azureml-automl-runtime**
    + 重複するタイムスタンプが見つかったときに表示されるエラー メッセージが改善されました。
    + target_rolling_window_size を 2 未満の値に設定することが禁止されました。
    + 遅延を補完できない問題を解消しました。 この問題は、連続を期間別に分解するために必要となる観察数が十分ではないことが原因でした。 この "期間調整された" データは、遅延の長さを判断する目的で偏自己相関関数 (PACF) を計算するために使用されます。
    + 特徴付け構成による予測タスクのために、列の目的の特徴付けカスタマイズを有効にしました。予測タスクの列の目的として数値別とカテゴリ別を使用できるようになりました。
    + 特徴付け構成による予測タスクのために、列の削除の特徴付けカスタマイズを有効にしました。
    + 特徴付け構成による予測タスクのために、補完カスタマイズを有効にしました。ターゲット列の定数値補完と、データをトレーニングするための平均値、中央値、最頻値、定数値補完がサポートされるようになりました。
  + **azureml-contrib-pipeline-steps**
    + ParallelRunConfig に渡す文字列計算名を受け取ります
  + **azureml-core**
    +  Environment オブジェクトのコピーを作成する目的で Environment.clone(new_name) API を追加しました
    +  Environment.docker.base_dockerfile でファイルパスを受け取ります。 ファイルを解決できる場合、コンテンツは base_dockerfile 環境プロパティに読み込まれます
    + ユーザーが Environment.docker で値を手動設定するとき、base_image と base_dockerfile の相互に排他的な値が自動的にリセットされます
    + 環境がユーザーまたは AzureML のどちらによって管理されているかを示す user_managed フラグが RSection に追加されました。
    + データセット: データ パスに Unicode 文字が含まれる場合、データセットをダウンロードできない問題を修正しました。
    + データセット: Azure Machine Learning コンピューティングの最小ディスク容量要件を満たすよう、データセット マウント キャッシュ メカニズムが改善されました。ノードを使用できなくなったり、ジョブがキャンセルされたりする事態を回避します。
    + データセット: pandas データフレームとしての時系列データセットにアクセスすると、時系列の列にインデックスが追加されます。このインデックスは、時系列ベースのデータへのアクセスを高速化する目的で使用されます。  以前は、このインデックスにタイムスタンプ列と同じ名前が与えられていました。それだと、どちらが現在のタイムスタンプ列でどちらがインデックスなのか、ユーザーを混乱させます。 インデックスは列として使用するべきではないため、今後は特定の名前を付けることはありません。 
    + データセット: ソブリン クラウドのデータセット認証問題を修正しました。
    + データセット: Azure PostgreSQL データストアから作成されたデータセットの `Dataset.to_spark_dataframe` エラーを修正しました。
  + **azureml-interpret**
    + ローカルの重要度値が少ない場合、視覚化にグローバル スコアを追加しました
    + azureml-interpret が interpret-community 0.9 に更新されました。*
    + 評価データが少ない説明をダウンロードできない問題を修正しました
    + AutoML で形式データが少ない説明オブジェクトのサポートを追加しました
  + **azureml-pipeline-core**
    + パイプラインの計算ターゲットとして ComputeInstance をサポート
  + **azureml-train-automl-client**
    + トレーニング後の操作に関するテレメトリが追加されました。
    + 早期停止時の回帰を修正しました
    + 入力データの有効な種類として azureml.dprep.Dataflow を非推奨にしました。
    +  AutoML の既定の実験タイムアウトを 6 日間に変更。
  + **azureml-train-automl-runtime**
    + トレーニング後の操作に関するテレメトリが追加されました。
    + データの少ない AutoML エンドツーエンド サポートを追加しました
  + **azureml-opendatasets**
    + サービス モニターのテレメトリを追加しました。
    + 安定性を上げるために BLOB のフロント ドアが有効になります 

## <a name="2020-03-23"></a>2020-03-23

### <a name="azure-machine-learning-sdk-for-python-v120"></a>Azure Machine Learning SDK for Python v1.2.0

+ **重大な変更**
  + Python 2.7 のサポートは終了します

+ **バグの修正と機能強化**
  + **azure-cli-ml**
    + CLI の `az ml model/computetarget/service` コマンドに "--subscription-id" が追加されます
    + ACI デプロイ用にカスタマーマネージド キー (CMK) vault_url、key_name、および key_version を渡すためのサポートが追加されます
  + **azureml-automl-core** 
    + X と Y の両方のデータ予測タスクに対して、定数値を使用したカスタマイズされた補完を使用できるようになりました。
    + ユーザーに対するエラー メッセージの表示に関する問題が修正されました。    
  + **azureml-automl-runtime**
    + 1 行のみのグレインが含まれるデータ セットの予測に関する問題が修正されました
    + 予測タスクに必要なメモリ量が少なくなりました。
    + 時刻列の形式が正しくない場合の、より適切なエラー メッセージが追加されました。
    + X と Y の両方のデータ予測タスクに対して、定数値を使用したカスタマイズされた補完を使用できるようになりました。
  + **azureml-core**
    + 次の環境変数から ServicePrincipal を読み込むためのサポートが追加されました: AZUREML_SERVICE_PRINCIPAL_ID、AZUREML_SERVICE_PRINCIPAL_TENANT_ID、および AZUREML_SERVICE_PRINCIPAL_PASSWORD
    + 新しいパラメーター `support_multi_line` が `Dataset.Tabular.from_delimited_files` に導入されました。既定 (`support_multi_line=False`) では、すべての改行 (引用符で囲まれたフィールド値内のものも含む) がレコード区切りとして解釈されます。 この方法でデータを読み込むと、複数の CPU コアでの並列実行で速度と最適化が向上します。 ただし、フィールド値が不整合なレコードが警告なしで多数生成される可能性があります。 引用符で囲まれた改行が区切りファイルに含まれていることがわかっている場合は、これを `True` に設定する必要があります。
    + Azure Machine Learning CLI に ADLS Gen2 を登録する機能が追加されました
    + パラメーターの使用方法をより正確に反映するため、TabularDataset の with_timestamp_columns() メソッドのパラメーター 'fine_grain_timestamp' の名前が 'timestamp' に変更され、パラメーター 'coarse_grain_timestamp' の名前が 'partition_timestamp' に変更されました。
    + 実験名の最大長が 255 に増加されました。
  + **azureml-interpret**
    + azureml-interpret が interpret-community 0.7 に更新されました。*
  + **azureml-sdk**
    + プレリリース版と安定版リリースでの修正プログラムの適用をサポートするために、互換バージョンの Tilde での依存関係に変更されます。


## <a name="2020-03-11"></a>2020-03-11

### <a name="azure-machine-learning-sdk-for-python-v115"></a>Azure Machine Learning SDK for Python v1.1.5

+ **機能の非推奨**
  + **Python 2.7**
    + Python 2.7 をサポートする最後のバージョンです

+ **重大な変更**
  + **セマンティック バージョニング 2.0.0**
    + バージョン 1.1 以降、Azure ML Python SDK では、セマンティック バージョニング 2.0.0 が採用されています。 詳細については、[こちら](https://semver.org/)を参照してください。 以降のすべてのバージョンは、新しい番号付けスキームとセマンティック バージョニング コントラクトに従います。 

+ **バグの修正と機能強化**
  + **azure-cli-ml**
    + 一貫性を保つために、エンドポイントの CLI コマンド名が 'az ml endpoint aks' から 'az ml endpoint real time' に変更されます。
    + 安定した実験用ブランチ CLI の CLI インストール手順を更新します
    + 単一インスタンスのプロファイリングが、レコメンデーションを生成するために修正され、コアの SDK で使用できるようになりました。
  + **azureml-automl-core**
    + AutoML ONNX モデルのバッチ モード推論 (複数行を 1 回取得) が有効になりました
    + データ セットの周波数の検出、データの不足、または不規則なデータ ポイントの検出の改善
    + 主要な周波数に準拠していないデータ ポイントを削除する機能が追加されました。
    + 対応する列の補完オプションを適用するためのオプション一覧を取得するように、コンストラクターの入力を変更しました。
    + エラーのログ記録が改善されました。
  + **azureml-automl-runtime**
    + トレーニング セットに存在しないグレインがテスト セットに含まれていた場合にスローされるエラーの問題を修正しました
    + 予測サービスでのスコアリング中に y_query 要件が削除されました
    + データセットに長い時間差の短いグレインが含まれている場合の予測に関する問題を修正しました。
    + 自動最大期間が有効になっていて、日付列に文字列形式の日付が含まれている場合の問題を修正しました。 日付への変換ができない場合の、適切な変換およびエラーのメッセージを追加しました
    + FileCacheStore の中間データのシリアル化と逆シリアル化にネイティブの NumPy と SciPy を使用します (ローカル AutoML の実行に使用)
    + 失敗した子の実行が実行状態のままになるバグを修正しました。
    + 特性付けの速度が向上しました。
    + スコアリング中の頻度チェックを修正しました。予測タスクでは、トレーニングとテスト セット間で頻度が厳密に同一である必要はなくなりました。
    + 対応する列の補完オプションを適用するためのオプション一覧を取得するように、コンストラクターの入力を変更しました。
    + ラグの種類の選択に関連するエラーを修正しました。
    + データ セットで発生した未分類エラーを修正し、粒度を単一行にしました
    + 周波数検出のパフォーマンスが低下する問題を修正しました。
    + トレーニング エラーの実際の原因が AttributeError に置き換えられる、AutoML 例外処理のバグを修正します。
  + **azureml-cli-common**
    + 単一インスタンスのプロファイリングが、レコメンデーションを生成するために修正され、コアの SDK で使用できるようになりました。
  + **azureml-contrib-mir**
    + アクセス トークンを取得する機能を MirWebservice クラスに追加
    + MirWebservice.run() の呼び出し中、MirWebservice に対して既定ではトークン認証を使用し、呼び出しが失敗した場合のみ更新します
    + Mir webservice のデプロイでは、[Ds2v2、A2v2、および F16] ではなく、それぞれ適切な SKU である [Standard_DS2_v2、Standard_F16、Standard_A2_v2] が必要になりました。
  + **azureml-contrib-pipeline-steps**
    + 省略可能なパラメーター side_inputs が ParallelRunStep に追加されました。 このパラメーターを使用して、コンテナーにフォルダーをマウントできます。 現在サポートされている型は、DataReference と PipelineData です。
    + ParallelRunConfig で渡されたパラメーターが、パイプライン パラメーターを渡すことによって上書きできるようになりました。 サポートされる新しいパイプライン パラメーターは、aml_mini_batch_size、aml_error_threshold、aml_logging_level、aml_run_invocation_timeout (aml_node_count と aml_process_count_per_node は以前のリリースに既に含まれています) です。
  + **azureml-core**
    + デプロイされた AzureML Webservices は、既定値で `INFO`ログになります。 これは、デプロイされたサービスで `AZUREML_LOG_LEVEL` 環境変数を設定することによってコントロールできます。
    + Python sdk は、検出サービスを使用して「パイプライン」の代わりに 「api」エンドポイントを使用します。
    + すべての SDK 呼び出しの新しいルートにスワップします。
    + ModelManagementService への呼び出しのルート指定を新しい統合構造体に変更しました。
      + ワークスペースの更新メソッドを公開しました。
      + ユーザーがイメージ ビルドの計算を更新できるように、ワークスペース更新メソッドに image_build_compute パラメーターを追加しました。
    + 古いプロファイル ワークフローに非推奨のメッセージを追加しました。 プロファイルの CPU とメモリの制限を修正しました。
    + R ジョブを実行する環境の一部として RSection を追加しました。
    + データセットのソースにアクセスできない場合、またはデータが含まれていない場合にエラーを発生させるために、`Dataset.mount` に検証を追加しました。
    + Azure BLOB コンテナーを登録するためのデータストア CLI の追加パラメーターとして `--grant-workspace-msi-access` を追加しました。これにより、VNet の背後にある BLOB コンテナーを登録できるようになります。
    + 単一インスタンスのプロファイリングが、レコメンデーションを生成するために修正され、コアの SDK で使用できるようになりました。
    + aks.py _deploy の問題を修正しました。
    + サイレント ストレージ障害を回避するために、アップロードされるモデルの整合性が検証されます。
    + ユーザーは、Web サービス用のキーを再生成するときに、認証キーの値を指定できるようになりました。
    + データセットの入力名として大文字を使用できないバグを修正しました。
  + **azureml-defaults**
    + `azureml-dataprep` は `azureml-defaults` の一部としてインストールされるようになりました。 データセットをマウントするためにコンピューティング先に data prep[fuse] を手動でインストールする必要がなくなりました。
  + **azureml-interpret**
    + azureml-interpret を interpret-community 0.6 へと更新しました。*
    + azureml-interpret が interpret-community 0.5.0 に依存するように更新されました
    + azureml-interpret に azureml-style 例外を追加しました
    + keras モデルの DeepScoringExplainer シリアル化を修正しました
  + **azureml-mlflow**
    + ソブリン クラウドのサポートを azureml.mlflow に追加
  + **azureml-pipeline-core**
    + パイプライン バッチ スコアリング ノートブックで ParallelRunStep が使用されるようになりました
    + 引数リストを変更しても PythonScriptStep の結果が誤って再利用される可能性のあるバグを修正しました
    + `PipelineOutputFileDataset` で parse_* メソッドを呼び出すときに、列の型を設定する機能を追加しました
  + **azureml-pipeline-steps**
    + `AutoMLStep` を `azureml-pipeline-steps` パッケージに移動しました。 `azureml-train-automl-runtime` 内の `AutoMLStep` は非推奨になりました。
    + PythonScriptStep 入力としてのデータセットのドキュメント例を追加しました
  + **azureml-tensorboard**
    + azureml-tensorboard を更新して、tensorflow 2.0 をサポートしました
    + コンピューティング インスタンスでカスタムの Tensorboard ポートを使用するときに、正しいポート番号を表示します
  + **azureml-train-automl-client**
    + リモート実行で特定のパッケージが正しくないバージョンでインストールされる可能性がある問題を修正しました。
    + カスタムの特性付け構成をフィルター処理する FeaturizationConfig のオーバーライドの問題を修正しました。
  + **azureml-train-automl-runtime**
    + リモート実行での周波数の検出に関する問題を修正
    + `azureml-pipeline-steps` パッケージ内の `AutoMLStep` を移動しました。 `azureml-train-automl-runtime` 内の `AutoMLStep` は非推奨になりました。
  + **azureml-train-core**
    + PyTorch Estimator での PyTorch バージョン 1.4 のサポート
  
## <a name="2020-03-02"></a>2020 年 03 月 02 日

### <a name="azure-machine-learning-sdk-for-python-v112rc0-pre-release"></a>Azure Machine Learning SDK for Python v1.1.2rc0 (プレリリース)

+ **バグの修正と機能強化**
  + **azureml-automl-core**
    + AutoML ONNX モデルのバッチ モード推論 (複数行を 1 回取得) が有効になりました
    + データ セットの周波数の検出、データの不足、または不規則なデータ ポイントの検出の改善
    + 主要な周波数に準拠していないデータ ポイントを削除する機能が追加されました。
  + **azureml-automl-runtime**
    + トレーニング セットに存在しないグレインがテスト セットに含まれていた場合にスローされるエラーの問題を修正しました
    + 予測サービスでのスコアリング中に y_query 要件が削除されました
  + **azureml-contrib-mir**
    + アクセス トークンを取得する機能を MirWebservice クラスに追加
  + **azureml-core**
    + デプロイされた AzureML Webservices は、既定値で `INFO`ログになります。 これは、デプロイされたサービスで `AZUREML_LOG_LEVEL` 環境変数を設定することによってコントロールできます。
    + ワークスペースに登録されているすべてのデータ セットを返すために `Dataset.get_all` の繰り返しを修正しました。
    + データセット作成 API の `path` 引数に無効な型が渡されると、エラー メッセージが改善されます。
    + Python sdk は、検出サービスを使用して「パイプライン」の代わりに 「api」エンドポイントを使用します。
    + すべての SDK 呼び出しの新しいルートにスワップする
    + ModelManagementService への呼び出しのルート指定を新しい統合構造体に変更します。
      + ワークスペースの更新メソッドを公開しました。
      + ユーザーがイメージ ビルドの計算を更新できるように、ワークスペース更新メソッドに image_build_compute パラメーターを追加
    +  古いプロファイル ワークフローに非推奨のメッセージを追加しました。 プロファイルの cpu とメモリの制限を修正
  + **azureml-interpret**
    + azureml-interpret から interpret-community 0.6 に更新されました。*
  + **azureml-mlflow**
    + ソブリン クラウドのサポートを azureml.mlflow に追加
  + **azureml-pipeline-steps**
    + `AutoMLStep` を `azureml-pipeline-steps package`に移動しました。 `azureml-train-automl-runtime` 内の `AutoMLStep` は非推奨になりました。
  + **azureml-train-automl-client**
    + リモート実行で特定のパッケージが正しくないバージョンでインストールされる可能性がある問題を修正しました。
  + **azureml-train-automl-runtime**
    + リモート実行での周波数の検出に関する問題を修正
    + `AutoMLStep` を `azureml-pipeline-steps package`に移動しました。 `azureml-train-automl-runtime` 内の `AutoMLStep` は非推奨になりました。
  + **azureml-train-core**
    + `AutoMLStep` を `azureml-pipeline-steps package`に移動しました。 `azureml-train-automl-runtime` 内の `AutoMLStep` は非推奨になりました。

## <a name="2020-02-18"></a>2020-02-18

### <a name="azure-machine-learning-sdk-for-python-v111rc0-pre-release"></a>Azure Machine Learning SDK for Python v1.1.1rc0 (プレリリース)

+ **バグの修正と機能強化**
  + **azure-cli-ml**
    + 単一インスタンスのプロファイリングが、レコメンデーションを生成するために修正され、コアの SDK で使用できるようになりました。
  + **azureml-automl-core**
    + エラーのログ記録が改善されました。
  + **azureml-automl-runtime**
    + データセットに長い時間差の短いグレインが含まれている場合の予測に関する問題を修正しました。
    + 自動最大期間が有効になっていて、日付列に文字列形式の日付が含まれている場合の問題を修正しました。 日付への変換ができない場合の適切な変換と適切なエラーを追加しました
    + FileCacheStore の中間データのシリアル化と逆シリアル化にネイティブの NumPy と SciPy を使用します (ローカル AutoML の実行に使用)
    + 失敗した子の実行が実行状態のままになるバグを修正しました。
  + **azureml-cli-common**
    + 単一インスタンスのプロファイリングが、レコメンデーションを生成するために修正され、コアの SDK で使用できるようになりました。
  + **azureml-core**
    + Azure BLOB コンテナーを登録するためのデータストア CLI の追加パラメーターとして `--grant-workspace-msi-access` を追加しました。これにより、VNet の背後にある BLOB コンテナーを登録できるようになります
    + 単一インスタンスのプロファイリングが、レコメンデーションを生成するために修正され、コアの SDK で使用できるようになりました。
    + aks.py _deploy の問題を修正しました
    + サイレント ストレージ障害を回避するために、アップロードされるモデルの整合性が検証されます。
  + **azureml-interpret**
    + azureml-interpret に azureml-style 例外を追加しました
    + keras モデルの DeepScoringExplainer シリアル化を修正しました
  + **azureml-pipeline-core**
    + パイプライン バッチ スコアリング ノートブックで ParallelRunStep が使用されるようになりました
  + **azureml-pipeline-steps**
    + `azureml-pipeline-steps` パッケージ内の `AutoMLStep` を移動しました。 `azureml-train-automl-runtime` 内の `AutoMLStep` は非推奨になりました。
  + **azureml-contrib-pipeline-steps**
    + 省略可能なパラメーター side_inputs が ParallelRunStep に追加されました。 このパラメーターを使用して、コンテナーにフォルダーをマウントできます。 現在サポートされている型は、DataReference と PipelineData です。
  + **azureml-tensorboard**
    + azureml-tensorboard を更新して、tensorflow 2.0 をサポートしました
  + **azureml-train-automl-client**
    + カスタムの特性付け構成をフィルター処理する FeaturizationConfig のオーバーライドの問題を修正しました。
  + **azureml-train-automl-runtime**
    + `azureml-pipeline-steps` パッケージ内の `AutoMLStep` を移動しました。 `azureml-train-automl-runtime` 内の `AutoMLStep` は非推奨になりました。
  + **azureml-train-core**
    + PyTorch Estimator での PyTorch バージョン 1.4 のサポート
  
## <a name="2020-02-04"></a>2020-02-04

### <a name="azure-machine-learning-sdk-for-python-v110rc0-pre-release"></a>Azure Machine Learning SDK for Python v1.1.0rc0 (プレリリース)

+ **重大な変更**
  + **セマンティック バージョニング 2.0.0**
    + バージョン 1.1 以降、Azure ML Python SDK では、セマンティック バージョニング 2.0.0 が採用されています。 詳細については、[こちら](https://semver.org/)を参照してください。 以降のすべてのバージョンは、新しい番号付けスキームとセマンティック バージョニング コントラクトに従います。 
  
+ **バグの修正と機能強化**
  + **azureml-automl-runtime**
    + 特性付けの速度が向上しました。
    + スコアリング中の頻度チェックを修正しました。現在、予測タスクでは、トレーニングとテストのセット間で頻度が厳密に同一である必要はありません。
  + **azureml-core**
    + ユーザーは、Web サービス用のキーを再生成するときに、認証キーの値を指定できるようになりました。
  + **azureml-interpret**
    + azureml-interpret が interpret-community 0.5.0 に依存するように更新されました
  + **azureml-pipeline-core**
    + 引数リストを変更しても PythonScriptStep の結果が誤って再利用される可能性のあるバグを修正しました
  + **azureml-pipeline-steps**
    + PythonScriptStep 入力としてのデータセットのドキュメント例を追加しました
  + **azureml-contrib-pipeline-steps**
    + ParallelRunConfig で渡されたパラメーターが、パイプライン パラメーターを渡すことによって上書きできるようになりました。 サポートされる新しいパイプライン パラメーターは、aml_mini_batch_size、aml_error_threshold、aml_logging_level、aml_run_invocation_timeout (aml_node_count と aml_process_count_per_node は以前のリリースに既に含まれています) です。
  
## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>Azure Machine Learning SDK for Python v1.0.85

+ **新機能**
  + **azureml-core**
    + 特定のワークスペースとサブスクリプションで AmlCompute リソースの現在のコア使用率とクォータ制限を取得する
  
  + **azureml-contrib-pipeline-steps**
    + 前の手順から parallelrunstep への中間結果として表形式データセットを渡すことをユーザーに許可する

+ **バグの修正と機能強化**
  + **azureml-automl-runtime**
    + デプロイされた予測サービスへの要求で y_query 列の要件を削除しました。 
    + 'y_query' が、Dominick のオレンジジュース ノートブックのサービス リクエスト セクションから削除されました。
    + デプロイされたモデルでの予測を妨げ、日付と時刻の列を含むデータセットを操作するバグを修正しました。
    + 二項分類と多クラス分類の両方において、マシューズ相関係数を分類メトリックとして追加しました。
  + **azureml-contrib-interpret**
    + テキストの説明が、間もなくリリースされる interpret-text リポジトリに移動されたため、azureml-contrib-interpret から Text Explainer が削除されました。
  + **azureml-core**
    + データセット: ファイル データセットの使用は、python env にインストールされる numpy と pandas に依存しなくなりました。
    + 正常性エンドポイントに ping を実行する前にローカル Docker コンテナーの状態を確認するよう、LocalWebservice.wait_for_deployment() を変更しました。これにより、失敗したデプロイの報告にかかる時間を大幅に短縮しました。
    + LocalWebservice.reload() コンストラクターを使用して、既存のデプロイからサービス オブジェクトが作成されるときに、LocalWebservice() で使用される内部プロパティの初期化を修正しました。
    + より明確にするため、エラー メッセージを編集しました。
    + get_access_token という新しいメソッドを AksWebservice に追加しました。このメソッドには、アクセス トークン、タイムスタンプの後の更新、タイムスタンプの有効期限、およびトークンの種類が含まれている AksServiceAccessToken オブジェクトが返されます。 
    + 新しいメソッドは、AksWebservice の既存の get_token () メソッドが返すすべての情報を返すため、非推奨としました。
    + az ml サービス get-access-token コマンドの出力が変更されました。 トークンの名前を accessToken に変更し、refreshBy を refreshAfter に変更しました。 expiryOn および tokenType プロパティが追加されました。
    + Fixed get_active_runs
  + **azureml-explain-model**
    + shap を 0.33.0 に更新し、interpret-community を 0.4 に変更しました。*
  + **azureml-interpret**
    + shap を 0.33.0 に更新し、interpret-community を 0.4 に変更しました。*
  + **azureml-train-automl-runtime**
    + 二項分類と多クラス分類の両方において、マシューズ相関係数を分類メトリックとして追加しました。
    + コードから前処理フラグを廃止し、特性付けに置き換えました。特性付けは既定でオンになっています

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>Azure Machine Learning SDK for Python v1.0.83

+ **新機能**
  + データセット: `to_pandas_dataframe` に対して `on_error` および `out_of_range_datetime` の2 つのオプションを追加します。データにエラーがある場合は、`None` を使用して入力するのではなく、エラーが発生します。
  + ワークスペース:機密データを含むワークスペースの `hbi_workspace` フラグが追加されました。これにより、さらに暗号化したり、ワークスペースの高度な診断を無効にしたりできます。 また、ワークスペースの作成時に `cmk_keyvault` パラメーターと `resource_cmk_uri` パラメーターを指定することによって、関連付けられている Cosmos DB インスタンスに対して独自のキーを取り込むためのサポートを追加しました。これにより、ワークスペースのプロビジョニングの再にサブスクリプションに Cosmos DB インスタンスが作成されます。 [詳細については、こちらを参照してください。](./concept-data-encryption.md#azure-cosmos-db)

+ **バグの修正と機能強化**
  + **azureml-automl-runtime**
    + バージョン 3.5.4 より下の Python でAutoML を実行するときに TypeError が発生する原因となった回帰を修正しました。
  + **azureml-core**
    + `./` で開始されていない相対パスを使用できない `datastore.upload_files` のバグを修正しました。
    + すべての Image クラスのコード パスについて非推奨のメッセージを追加しました
    + Azure China 21Vianet リージョンのモデル管理 URL の構成を修正しました。
    + source_dir を使用するモデルを Azure Functions 用にパッケージ化できない問題を修正しました。    
    + イメージを AzureML ワークスペース コンテナー レジストリにプッシュするためのオプションを [Environment.build_local()](/python/api/azureml-core/azureml.core.environment.environment)に追加しました。
    + Azure Synapse の新しいトークン ライブラリを後方互換性のある方法で使用するように SDK を更新しました。
  + **azureml-interpret**
    + ダウンロードに対する説明が使用できなかったときに None が返されるバグを修正しました。 例外を発生させ、それ以外では動作が一致します。
  + **azureml-pipeline-steps**
    + `Estimator` が `EstimatorStep` で使用される場合、`DatasetConsumptionConfig` を `Estimator` の `inputs` パラメーターに渡すことはできません。
  + **azureml-sdk**
    + azureml-sdk パッケージに AutoML クライアントを追加しました。完全な AutoML パッケージをインストールしなくても、リモート AutoML の実行をサブミットすることができます。
  + **azureml-train-automl-client**
    + AutoML 実行用のコンソール出力の配置を修正しました
    + リモートの amlcompute に正しくないバージョンの pandas がインストールされる可能性があるバグを修正しました。

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>Azure Machine Learning SDK for Python v1.0.81

+ **バグの修正と機能強化**
  + **azureml-contrib-interpret**
    + interpret-community に対する shap の依存関係を azureml-interpret から保留します
  + **azureml-core**
    + コンピューティング先を、対応するデプロイ構成オブジェクトのパラメーターとして指定できるようになりました。 これは、具体的には、SDK オブジェクトではなく、デプロイ先となるコンピューティング先の名前です。
    + Model および Service オブジェクトに CreatedBy 情報を追加しました。 through.created_by でアクセスできます
    + Docker コンテナーの HTTP ポートを正しく設定していなかった ContainerImage.run() を修正しました。
    + `az ml dataset register` CLI コマンドの `azureml-dataprep` を省略可能にします
    + `TabularDataset.to_pandas_dataframe` が代替リーダーに誤ってフォールバックし、警告を出力するバグを修正しました。
  + **azureml-explain-model**
    + interpret-community に対する shap の依存関係を azureml-interpret から保留します
  + **azureml-pipeline-core**
    + パイプラインのステップとしてローカル ノートブックを実行するための新しいパイプライン ステップ `NotebookRunnerStep` を追加しました。
    + PublishedPipelines、Schedules、および PipelineEndpoints の非推奨の get_all 関数を削除しました。
  + **azureml-train-automl-client**
    + AutoML への入力として data_script の非推奨を開始しました。


## <a name="2019-12-09"></a>2019-12-09

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>Azure Machine Learning SDK for Python v1.0.79

+ **バグの修正と機能強化**
  + **azureml-automl-core**
    + featurizationConfig をログの記録から除外しました
      + "auto"/"off"/"customized" のみを記録するようにログ方式を更新しました。
  + **azureml-automl-runtime**
    + 列のデータ型を検出するため、pandas. Series と pandas. Categorical のサポートを追加しました。 以前は numpy.ndarray のみがサポートされていました
      + カテゴリ型 dtype を正しく処理するための関連するコード変更を追加しました。
    + 予測関数インターフェイスが改善されました。y_pred パラメーターが省略可能になりました。 -docstring が改善されました。
  + **azureml-contrib-dataset**
    + ラベルが付けられたデータセットをマウントできないバグを修正しました。
  + **azureml-core**
    + `Environment.from_existing_conda_environment(name, conda_environment_name)` のバグ修正。 ユーザーは、ローカル環境の完全なレプリカである環境のインスタンスを作成できます
    + 時系列に関連するデータセット メソッドが既定で `include_boundary=True` に変更されました。
  + **azureml-train-automl-client**
    + show output が false に設定されている場合に検証結果が出力されない問題を修正しました。


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>Azure Machine Learning SDK for Python v1.0.76

+ **重大な変更**
  + Azureml-Train-AutoML のアップグレードに関する問題
    + 1\.0.76 よりも前の azureml-train-automl 1.0.76 から1.0.76 以降の azureml-train-automl にアップグレードすると、部分的なインストールが発生し、一部の AutoML のインポートが失敗する可能性があります。 これを解決するには、 https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmd で見つかったセットアップ スクリプトを実行します。 または、pip を直接使用している場合は、以下を実行することができます。
      + "pip install --upgrade azureml-train-automl"
      + "pip install --ignore-installed azureml-train-automl-client"
    + または、アップグレードする前に古いバージョンをアンインストールすることもできます。
      + "pip uninstall azureml-train-automl"
      + "pip install azureml-train-automl"

+ **バグの修正と機能強化**
  + **azureml-automl-runtime**
    + AutoML では、二項分類タスクの平均スカラーメトリックを計算するときに、true と false の両方のクラスが考慮されるようになりました。
    + AzureML-AutoML-Core の機械学習とトレーニング コードを新しいパッケージ AzureML-AutoML-Runtime に移動しました。
  + **azureml-contrib-dataset**
    + ダウンロード オプションを使用してラベル付きのデータセットで `to_pandas_dataframe` を呼び出すと、既存のファイルを上書きするかどうかを指定できるようになりました。
    + time series、label、または image 列が削除される `keep_columns` または `drop_columns` を呼び出すと、データセットの対応する機能も削除されます。
    + 物体検出タスク用の PyTorch ローダーに関する問題を修正しました。
  + **azureml-contrib-interpret**
    + azureml-contrib-interpret から説明ダッシュボード ウィジェットを削除しました、interpret_community の新しいものを参照するようにパッケージを変更しました
    + interpret-community のバージョンを 0.2.0 に更新しました
  + **azureml-core**
    + `workspace.datasets` のパフォーマンスが向上します。
    + ユーザー名とパスワード認証を使用して Azure SQL Database データストアを登録する機能が追加されました。
    + 相対パスから RunConfigurations を読み込むように修正します。
    + time series 列が削除される `keep_columns` または `drop_columns` を呼び出すと、データセットの対応する機能も削除されます。
  + **azureml-interpret**
    + interpret-community のバージョンを 0.2.0 に更新しました
  + **azureml-pipeline-steps**
    + Azure Machine Learning パイプラインの手順の `runconfig_pipeline_params` でサポートされている値を記載しました。
  + **azureml-pipeline-core**
    + パイプライン コマンドの json 形式で出力をダウンロードするための CLI オプションが追加されました。
  + **azureml-train-automl**
    + AzureML-Train-AutoML を 2 つのパッケージに分割します (クライアント パッケージ AzureML-Train-AutoML-Client と ML トレーニング パッケージ AzureML-Train-AutoML-Runtime)
  + **azureml-train-automl-client**
    + 機械学習の依存関係をローカルにインストールすることなく、AutoML 実験を送信できるシン クライアントが追加されました。
    + リモート実行で自動的に検出されたラグ、ローリング ウィンドウ サイズ、最大期間のログ記録を修正しました。
  + **azureml-train-automl-runtime**
    + 新しい AutoML パッケージを追加して、機械学習とランタイム コンポーネントをクライアントから分離しました。
  + **azureml-contrib-train-rl**
    + SDK に強化学習サポートが追加されました。
    + RL SDK に AmlWindowsCompute サポートが追加されました。


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>Azure Machine Learning SDK for Python v1.0.74

  + **プレビュー機能**
    + **azureml-contrib-dataset**
      + azureml-contrib-dataset をインポートすると、`._Labeled` の代わりに `Dataset.Labeled.from_json_lines` を呼び出して、ラベル付きのデータセットを作成できます。
      + ダウンロード オプションを使用してラベル付きのデータセットで `to_pandas_dataframe` を呼び出すと、既存のファイルを上書きするかどうかを指定できるようになりました。
      + time series、label、または image 列が削除される `keep_columns` または `drop_columns` を呼び出すと、データセットの対応する機能も削除されます。
      + `dataset.to_torchvision()` を呼び出すときの PyTorch ローダーの問題が修正されました。

+ **バグの修正と機能強化**
  + **azure-cli-ml**
    + プレビュー CLI にモデルのプロファイルが追加されました。
    + AzureML CLI が失敗する原因となる Azure Storage の破壊的変更を修正します。
    + AKS タイプの MLC に Load Balancer タイプが追加されました
  + **azureml-automl-core**
    + 欠損値と複数のグレインを含む、時系列の最大期間の検出に関する問題を修正しました。
    + 交差検証分割の生成時のエラーに関する問題を修正しました。
    + このセクションをマークダウン形式のメッセージと置き換え、リリース ノートに表示されようにします。- 予測データセットの短いグレインが処理しやすくなりました。
    + ログ記録時に一部のユーザー情報がマスキングされる問題を修正しました。 -予測実行中のエラーのログ記録が改善されました。
    + conda 依存関係としての psutil を自動生成された yml デプロイ ファイルに追加します。
  + **azureml-contrib-mir**
    + AzureML CLI が失敗する原因となる Azure Storage の破壊的変更を修正します。
  + **azureml-core**
    + Azure Functions にデプロイされたモデルによって 500 番台が生成される原因となったバグを修正します。
    + スナップショットに amlignore ファイルが適用されない問題を修正しました。
    + 新しい API amlcompute.get_active_runs が追加されました。この API は、指定された amlcompute で実行するまたは実行キューに入れるジェネレーターを返します。
    + AKS タイプの MLC に Load Balancer タイプが追加されました。
    + run.py の download_files と artifacts_client の download_artifacts_from_prefix に append_prefix ブール パラメーターを追加しました。 このフラグは、元のファイル パスを選択的にフラット化するために使用されます。ファイル名またはフォルダー名のみが output_directory に追加されます
    + データセットの使用状況に関する `run_config.yml` の逆シリアル化の問題を修正しました。
    + time series 列が削除される `keep_columns` または `drop_columns` を呼び出すと、データセットの対応する機能も削除されます。
  + **azureml-interpret**
    + interpret-community バージョンを 0.1.0.3 に更新しました
  + **azureml-train-automl**
    + automl_step が検証の問題を出力しない可能性がある問題を修正しました。
    + モデルの環境にローカルで依存関係がない場合でも成功するように register_model を修正しました。
    + 一部のリモート実行で docker が有効になっていない問題を修正しました。
    + ローカル実行が早期に失敗する原因となっている例外のログ記録を追加します。
  + **azureml-train-core**
    + 自動化されたハイパーパラメーターのチューニングの最適な子実行の計算で resume_from の実行を検討します。
  + **azureml-pipeline-core**
    + パイプラインの引数の構築時のパラメーターの処理を修正しました。
    + パイプラインの説明とステップの種類の yaml パラメーターを追加しました。
    + パイプラインステップの新しい yaml 形式と、古い形式の非推奨警告を追加しました。



## <a name="2019-11-04"></a>2019-11-04

### <a name="web-experience"></a>Web エクスペリエンス

[https://ml.azure.com](https://ml.azure.com) での共同ワークスペースのランディング ページは、Azure Machine Learning Studio として強化およびブランド化されています。

この Studio からは、データセット、パイプライン、モデル、エンドポイントなどの Azure Machine Learning の資産をトレーニング、テスト、デプロイ、管理することができます。

Studio から、次の Web ベースの作成ツールにアクセスします。

| Web ベースのツール | 説明 | 
|-|-|-|
| ノートブック VM (プレビュー) | 完全に管理されたクラウドベースのワークステーション | 
| [自動化された機械学習](tutorial-first-experiment-automated-ml.md) (プレビュー) | 機械学習モデルの開発に向けた、コードを使用しないエクスペリエンス | 
| [Designer](concept-designer.md) | 以前はビジュアルインターフェイスと呼ばれていた、ドラッグ アンド ドロップによる機械学習モデリングツール | 


### <a name="azure-machine-learning-designer-enhancements"></a>Azure Machine Learning デザイナーの強化

+ 以前はビジュアル インターフェイスと呼ばれていました 
+    11 個の新しい[モジュール](algorithm-module-reference/module-reference.md)には、特徴エンジニアリング、クロス検証、データ変換など、レコメンダー、分類子、トレーニング ユーティリティが含まれています。

### <a name="r-sdk"></a>R SDK 
 
データ サイエンティストと AI 開発者は、[Azure Machine Learning SDK for R](https://github.com/Azure/azureml-sdk-for-r) を使用して、Azure Machine Learning で機械学習のワークフローをビルドして実行します。

Azure Machine Learning SDK for R では、Python SDK にバインドする `reticulate` パッケージが使用されます。 Python に直接バインドすることにより、R 用の SDK では、選択した R 環境から、Python SDK で実装されているコア オブジェクトおよびメソッドにアクセスできます。

SDK の主な機能は次のとおりです。

+    機械学習の実験を監視、ログ記録、整理するためのクラウド リソースを管理します。
+    GPU アクセラレーション モデルのトレーニングなどのクラウド リソースを使用して、モデルをトレーニングします。
+    Azure Container Instances (ACI) および Azure Kubernetes Service (AKS) で、モデルを Webservice としてデプロイします。

すべてのドキュメントについては、[パッケージ Web サイト](https://azure.github.io/azureml-sdk-for-r)を参照してください。

### <a name="azure-machine-learning-integration-with-event-grid"></a>Azure Machine Learning と Event Grid の統合 

Azure Machine Learning が Event Grid 用のリソース プロバイダーになりました。Azure portal または Azure CLI を使用して機械学習イベントを構成できます。 ユーザーは、実行の完了、モデルの登録、モデル デプロイ、データ ドリフト検出のイベントを作成できます。 これらのイベントは、Event Grid でサポートされているイベント ハンドラーにルーティングして使用できます。 詳細については、機械学習イベントの[スキーマ](../event-grid/event-schema-machine-learning.md)、および[チュートリアル](how-to-use-event-grid.md)に関する記事を参照してください。

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>Azure Machine Learning SDK for Python v1.0.72

+ **新機能**
  + [**azureml-datadrift**](/python/api/azureml-datadrift) パッケージを通じてデータセット モニターが追加されました。これにより、データ ドリフトや、その他の時間の経過による統計上の変化に関する時系列データセットの監視が可能になります。 アラートとイベントは、誤差が検知された場合、またはデータ上でその他の条件が満たされた場合にトリガーされます。 詳細については、[ドキュメント](how-to-monitor-datasets.md)を参照してください。
  + Azure Machine Learning で、2 つの新しいエディション (SKU とも呼ばれます) について発表します。 このリリースでは、Basic または Enterprise の Azure Machine Learning ワークスペースを作成できるようになりました。 すべての既存のワークスペースは、Basic エディションに既定で設定されます。また、Azure portal または Studio にアクセスして、いつでもワークスペースをアップグレードできます。 Azure portal から、Basic または Enterprise のワークスペースを作成できます。 詳細については、[こちらのドキュメント](./how-to-manage-workspace.md)を参照してください。 SDK では、ワークスペース オブジェクトの "sku" プロパティを使用して、ワークスペースのエディションを特定できます。
  + また、Azure Machine Learning コンピューティングの強化を行いました。デバッグ用の診断ログを表示するだけでなく、Azure Monitor でクラスターのメトリック (合計ノード、実行中のノード、合計コア クォータなど) を表示できるようになりました。 さらに、クラスター上の現在実行中またはキューに登録された実行や、クラスター上のさまざまなノードの IP などの詳細を表示することもできます。 これらは、ポータルで、または SDK や CLI で対応する関数を使用して表示できます。

  + **プレビュー機能**
    + Azure Machine Learning コンピューティングでのローカル SSD のディスク暗号化に対するプレビュー サポートをリリースしています。 テクニカル サポート チケットを作成し、サブスクリプションを許可リストに登録してもらって、この機能を使用してください。
    + Azure Machine Learning バッチ推論のパブリック プレビュー。 Azure Machine Learning バッチ推論は、時間の制約を受けない大規模な推論ジョブを対象とします。 バッチ推論では、非同期アプリケーション向けの比類なきスループットで、コスト効率のよい推論コンピューティングのスケーリングが提供されます。 大規模なデータ コレクションにわたって、高スループットのファイア アンド フォーゲット推論に対して最適化されています。
    + [**azureml-contrib-dataset**](/python/api/azureml-contrib-dataset)
        + ラベル付きデータセットに対して有効な機能
        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore, Dataset
        import azureml.contrib.dataset
        from azureml.contrib.dataset import FileHandlingOption, LabeledDatasetTask

        # create a labeled dataset by passing in your JSON lines file
        dataset = Dataset._Labeled.from_json_lines(datastore.path('path/to/file.jsonl'), LabeledDatasetTask.IMAGE_CLASSIFICATION)

        # download or mount the files in the `image_url` column
        dataset.download()
        dataset.mount()

        # get a pandas dataframe
        from azureml.data.dataset_type_definitions import FileHandlingOption
        dataset.to_pandas_dataframe(FileHandlingOption.DOWNLOAD)
        dataset.to_pandas_dataframe(FileHandlingOption.MOUNT)

        # get a Torchvision dataset
        dataset.to_torchvision()
        ```

+ **バグの修正と機能強化**
  + **azure-cli-ml**
    + CLI でモデル パッケージがサポートされるようになりました。
    + データセット CLI が追加されました。 詳細については、`az ml dataset --help` を参照してください
    + サポートされているモデル (ONNX、scikit-learn、TensorFlow) の InferenceConfig インスタンスを使用しないデプロイとパッケージ化のサポートが追加されました。
    + SDK および CLI でのサービスのデプロイ (ACI および AKS) の上書きフラグが追加されました。 指定した場合、既存の名前が付けられたサービスについては、既存のサービスを上書きします。 サービスが存在しない場合、新しいサービスが作成されます。
    + モデルは、Onnx および Tensorflow という 2 つの新しいフレームワークに登録できます。 - モデルの登録では、モデルのサンプルの入力データ、サンプルの出力データ、リソース構成が受け入れられます。
  + **azureml-automl-core**
    + イテレーションのトレーニングは、実行時の制約が設定されている場合にのみ、子プロセスで実行されます。
    + 予測タスクのガードレールが追加されました。指定された max_horizon が特定のコンピューターでメモリの問題を引き起こしているかどうかを確認します。 その場合、ガードレール メッセージが表示されます。
    + 2 年 1 か月というような複雑な頻度に対するサポートが追加されました。 \- 頻度を特定できない場合に対して、わかりやすいエラーメッセージが追加されました。
    + モデル デプロイの失敗を解決するために、自動生成された conda env に azureml-default を追加します
    + Azure Machine Learning パイプラインの中間データを表形式のデータセットに変換し、`AutoMLStep` で使用できるようにします。
    + ストリーミング用の列の目的の更新が実装されました。
    + ストリーミング用の Imputer および HashOneHotEncoder のトランスフォーマー パラメーターの更新プログラムが実装されました。
    + 検証エラー メッセージに、現在のデータ サイズと最低限必要なデータ サイズが追加されました。
    + 各検証フォールドに最低 2 つのサンプルが確実に提供されるよう、クロス検証のために最低限必要なデータ サイズが更新されました。
  + **azureml-cli-common**
    + CLI でモデル パッケージがサポートされるようになりました。
    + モデルは、Onnx および Tensorflow という 2 つの新しいフレームワークに登録できます。
    + モデルの登録では、モデルのサンプルの入力データ、サンプルの出力データ、リソース構成が受け入れられます。
  + **azureml-contrib-gbdt**
    + ノートブックのリリース チャネルが修正されました
    + サポートされていない AmlCompute 以外のコンピューティング先に対する警告が追加されました
    + azureml-contrib-gbdt パッケージに LightGMB Estimator が追加されました
  + [**azureml-core**](/python/api/azureml-core)
    + CLI でモデル パッケージがサポートされるようになりました。
    + 非推奨のデータセット API に対する非推奨警告が追加されます。 https://aka.ms/tabular-dataset でデータセット API の変更通知を参照してください。
    + データセットが登録されている場合は、登録名とバージョンを返すように [`Dataset.get_by_id`](/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-) を変更します。
    + 引数としてのデータセットを伴う ScriptRunConfig を実験の実行を送信するために繰り返し使用することができないというバグが修正されます。
    + 実行中に取得されたデータセットは、追跡され、実行の詳細ページに、または実行の完了後に [`run.get_details()`](/python/api/azureml-core/azureml.core.run%28class%29#get-details--) を呼び出すことで表示することができます。
    + Azure Machine Learning パイプラインの中間データを表形式のデータセットに変換し、[`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep) で使用できるようにします。
    + サポートされているモデル (ONNX、scikit-learn、TensorFlow) の InferenceConfig インスタンスを使用しないデプロイとパッケージ化のサポートが追加されました。
    + SDK および CLI でのサービスのデプロイ (ACI および AKS) の上書きフラグが追加されました。 指定した場合、既存の名前が付けられたサービスについては、既存のサービスを上書きします。 サービスが存在しない場合、新しいサービスが作成されます。
    +  モデルは、Onnx および Tensorflow という 2 つの新しいフレームワークに登録できます。 モデルの登録では、モデルのサンプルの入力データ、サンプルの出力データ、リソース構成が受け入れられます。
    + Azure Database for MySQL の新しいデータストアが追加されました。 Azure Machine Learning パイプラインに DataTransferStep の Azure Database for MySQL を使用するための例が追加されました。
    + 機能が追加され、実験にタグが追加されて削除されました。機能が追加され、実行からタグが削除されました
    + SDK および CLI でのサービスのデプロイ (ACI および AKS) の上書きフラグが追加されました。 指定した場合、既存の名前が付けられたサービスについては、既存のサービスを上書きします。 サービスが存在しない場合、新しいサービスが作成されます。
  + [**azureml-datadrift**](/python/api/azureml-datadrift)
    + `azureml-contrib-datadrift` から `azureml-datadrift` への移動
    + ドリフトやその他の統計メジャーの時系列データセットの監視に対するサポートの追加
    + [`DataDriftDetector`](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector%28class%29) クラスの新しいメソッド (`create_from_model()` および `create_from_dataset()`)。 `create()` メソッドが非推奨となる予定。
    + Azure Machine Learning Studio の Python および UI での視覚エフェクトの調整。
    + データセットの毎日の監視に加え、毎週および毎月の監視のスケジュール設定をサポート。
    + データセット モニターの履歴データを分析するためのデータ モニター メトリックのバックフィルをサポート。
    + さまざまなバグの修正
  + [**azureml-pipeline-core**](/python/api/azureml-pipeline-core)
    + パイプラインの `yaml` ファイルから Azure Machine Learning パイプラインの実行を送信するのに、azureml-dataprep は不要になりました。
  + [**azureml-train-automl**](/python/api/azureml-train-automl-runtime/)
    + モデル デプロイの失敗を解決するために、自動生成された conda env に azureml-default を追加します
    + AutoML リモートトレーニングに azureml-default が含まれるようになり、推論にトレーニング env を再利用できるようになりました。
  + **azureml-train-core**
    + [`PyTorch`](/python/api/azureml-train-core/azureml.train.dnn.pytorch) Estimator への PyTorch 1.3 サポートの追加

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>ビジュアル インターフェイス (プレビュー)

+ Azure Machine Learning ビジュアル インターフェイス (プレビュー) は、[Azure Machine Learning パイプライン](concept-ml-pipelines.md)で実行するために全面的に見直されました。 ビジュアル インターフェイスで作成されたパイプライン (以前は実験と呼ばれていました) は、コア Azure Machine Learning エクスペリエンスと完全に統合されました。
  + SDK 資産と統合された管理エクスペリエンス
  + ビジュアル インターフェイスのモデル、パイプライン、およびエンドポイントのバージョン管理と追跡
  + 新しい UI デザイン
  + バッチ推論のデプロイの追加
  + 推論コンピューティング先に対する Azure Kubernetes Service (AKS) のサポートの追加
  + 新しい Python ステップのパイプライン作成ワークフロー
  + ビジュアルの作成ツールの新しい[ランディング ページ](https://ml.azure.com)

+ **新しいモジュール**
  + 算術演算の適用
  + SQL 変換の適用
  + クリップの値
  + データの集計
  + SQL Database からのインポート

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Azure Machine Learning SDK for Python v1.0.69

+ **バグの修正と機能強化**
  + **azureml-automl-core**
    + 最適に実行されるよう、実行ごとに説明を計算せずにモデル説明をが制限されるようになりました。 ローカル、リモート、および ADB で動作変更が実施されます。
    + UI のオンデマンドのモデル説明のサポートを追加
    + `automl` の依存関係として psutil を追加し、amlcompute に conda 依存関係として psutil を含めました。
    + いくつかの系列で線形代数エラーを引き起こしていた予測データにおけるヒューリスティックの遅延とウィンドウのサイズが元に戻る問題を修正しました
      + 予測実行にヒューリスティックによって決定されたパラメーターの印刷出力を追加しました。
  + **azureml-contrib-datadrift**
    + データセット レベルの誤差が最初のセクションに含まれていない場合、出力メトリックの作成に保護を追加しました。
  + **azureml-contrib-interpret**
    + azureml-contrib-explain-model パッケージの名前が azureml-contrib-interpret に変更されました
  + **azureml-core**
    + 未登録のデータ セットに API を追加しました。 `dataset.unregister_all_versions()`
    + azureml-contrib-explain-model パッケージの名前が azureml-contrib-interpret に変更されました。
  + **[azureml-core](/python/api/azureml-core)**
    + 未登録のデータ セットに API を追加しました。 dataset.[unregister_all_versions()](/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--)。
    + データの変更時間を確認するデータ セット API が追加されました。 `dataset.data_changed_time`.
    + Azure Machine Learning パイプラインで `PythonScriptStep`、`EstimatorStep`、`HyperDriveStep` への入力として `FileDataset` および `TabularDataset` を使用できるようになりました
    + 多数のファイルを含むフォルダーの `FileDataset.mount` のパフォーマンスが向上しました
    + [FileDataset](/python/api/azureml-core/azureml.data.filedataset) および [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) を、[PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep)、[EstimatorStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)、[HyperDriveStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) への入力として Azure Machine Learning パイプラインで使用できるようになりました。
    + 多数のファイルを含むフォルダーの FileDataset.[mount()](/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none----kwargs-) のパフォーマンスが向上しました
    + 実行の詳細で既知のエラーに対する推奨事項に関する URL を追加しました。
    + run.get_metrics において実行の子が多すぎる場合に要求が失敗するバグを修正しました
    + [run.get_metrics](/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) において実行の子が多すぎる場合に要求が失敗するバグを修正しました
    + Arcadia クラスターでの認証のサポートが追加されました。
    + 実験オブジェクトを作成すると、実行履歴の追跡を行うため [Azure Machine Learning] ワークスペースで実験が取得または作成されます。 実験 ID およびアーカイブされた時間は、作成時に実験オブジェクトに設定されます。 例: experiment = Experiment (ワークスペース、"新しい実験")。experiment_id = experiment.id archive() とおよび reactivate() は、実験に対して呼び出すことができる関数で、UX に表示されるか、または実験の一覧を表示するよう規定で返されます。 アーカイブされた実験と同じ名前の新しい実験を作成した場合は、新しい名前を渡すことで、再アクティブ化するときにアーカイブされた実験の名前を変更できます。 存在できる同じ名前のアクティブな実験は 1 つだけです。 例: experiment1 = Experiment (ワークスペース、"アクティブな実験") experiment1.archive() # アーカイブと同じ名前で新しいアクティブな実験を作成します。 experiment2. = Experiment (ワークスペース、"アクティブな実験") experiment1.reactivate (新しい名前 = "以前アクティブだった実験")。Experiment の静的メソッドの list() は、名前フィルターと ViewType フィルターを受け取ることができます。 ViewType 値は、"ACTIVE_ONLY"、"ARCHIVED_ONLY"、および "ALL" です。例: archived_experiments = Experiment.list (ワークスペース、view_type = "ARCHIVED_ONLY") all_first_experiments = Experiment.list (ワークスペース、name = "最初の実験"、view_type = "ALL")
    + モデル デプロイおよびサービス更新向けの環境の使用をサポートします
  + **azureml-datadrift**
    + DataDriftDector クラスの show 属性では、省略可能な引数 'with_details' がサポートされなくなります。 show 属性では、特徴列のデータ誤差の係数とデータ誤差の影響のみが表示されます。
    + DataDriftDetector 属性 'get_output' の動作変更:
      + 入力パラメーター start_time、end_time は必須ではなく省略可能です。
      + 同じ呼び出しで特定の run_id を使用している入力固有の start_time および/または end_time は相互に排他的であるため、値エラーの例外が発生します
      + 入力固有の start_time または end_time では、スケジュールされた実行の結果のみが返されます。
      + パラメーター 'daily_latest_only' は推薦されません。
    + データセット ベースのデータ誤差の出力の取得がサポートされるようになりました。
  + **azureml-explain-model**
    + AzureML-explain-model パッケージの名前を AzureML-interpret に変更し、現時点では旧バージョンとの互換性を維持するために古いパッケージを保持します
    + ExplanationClient からのダウンロード時に、未加工の説明が既定の回帰ではなく分類タスクに設定される `automl` バグを修正しました
    + `MimicWrapper` を使用して直接作成する `ScoringExplainer` のサポートを追加しました
  + **azureml-pipeline-core**
    + 大規模なパイプライン作成のパフォーマンスが向上しました
  + **azureml-train-core**
    + TensorFlow Estimator で TensorFlow 2.0 がサポートされるようになりました
  + **azureml-train-automl**
    + [実験](/python/api/azureml-core/azureml.core.experiment.experiment)オブジェクトを作成すると、実行履歴の追跡を行うための Azure Machine Learning ワークスペースで実験が取得または作成されます。 実験 ID およびアーカイブされた時間は、作成時に実験オブジェクトに設定されます。 例:

        ```python
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        [archive()](/python/api/azureml-core/azureml.core.experiment.experiment#archive--) および [reactivate()](/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) は、実験を呼び出し、その実験を非表示にして UX での表示から復元するか、既定で返されて実験の一覧を表示できる関数です。 アーカイブされた実験と同じ名前の新しい実験を作成した場合は、新しい名前を渡すことで、再アクティブ化するときにアーカイブされた実験の名前を変更できます。 存在できる同じ名前のアクティブな実験は 1 つだけです。 例:

        ```python
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        実験の静的メソッド [list()](/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-) は、名前フィルターと ViewType フィルターを受け取ることができます。 ViewType 値は、"ACTIVE_ONLY"、"ARCHIVED_ONLY"、"ALL" です。 例:

        ```python
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + モデル デプロイおよびサービス更新向けの環境の使用をサポートします。
  + **[azureml-datadrift](/python/api/azureml-datadrift)**
    + [DataDriftDetector](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) クラスの show 属性では、省略可能な引数 'with_details' がサポートされなくなります。 show 属性では、特徴列のデータ誤差の係数とデータ誤差の影響のみが表示されます。
    + DataDriftDetector 関数 [get_output]python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#get-output-start-time-none--end-time-none--run-id-none-) の動作が次のように変更されました。
      + 入力パラメーター start_time、end_time は必須ではなく省略可能です。
      + 同じ呼び出しで特定の run_id を使用している入力固有の start_time および/または end_time は相互に排他的であるため、値エラーの例外が発生します。
      + 入力固有の start_time または end_time では、スケジュールされた実行の結果のみが返されます。
      + パラメーター 'daily_latest_only' は推薦されません。
    + データセット ベースのデータ誤差の出力の取得がサポートされるようになりました。
  + **azureml-explain-model**
    + MimicWrapper を使用して直接作成する [ScoringExplainer](/python/api/azureml-interpret/azureml.interpret.scoring.scoring_explainer.scoringexplainer) のサポートを追加しました
  + **[azureml-pipeline-core](/python/api/azureml-pipeline-core)**
    + 大規模なパイプライン作成のパフォーマンスが向上しました。
  + **[azureml-train-core](/python/api/azureml-train-core)**
    + [TensorFlow](/python/api/azureml-train-core/azureml.train.dnn.tensorflow) Estimator で TensorFlow 2.0 がサポートされるようになりました。
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + オーケストレーションで既に処理が行われているため、セットアップの反復処理に失敗した場合でも親の実行が失敗しなくなりました。
    + AutoML 実験で local-docker および local-conda がサポートされるようになりました
    + AutoML 実験で local-docker および local-conda がサポートされるようになりました。


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Azure Machine Learning ワークスペースの新しい Web エクスペリエンス (プレビュー)

[新しいワークスペース ポータル](https://ml.azure.com)の [実験] タブが更新され、データ サイエンティストがより高性能な方法で実験を監視できるようになりました。 次の機能を調査できます。
+ 実験の一覧を簡単にフィルター処理して並べ替えることができる試験的なメタデータ
+ 実行を視覚化して比較できる、簡略化された高性能な実験の詳細ページ
+ トレーニング実行を理解して監視するために詳細ページを実行するための新しいデザイン

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>Azure Machine Learning SDK for Python v1.0.65

  + **新機能**
    + キュレートされた環境が追加されました。 これらの環境は、一般的な機械学習タスク用のライブラリを使用してあらかじめ構成されており、実行時間を短縮するため、Docker イメージとして事前にビルドおよびキャッシュされています。 既定で、"AzureML" というプレフィックスが付けられて、ワークスペースの環境の一覧に表示されます。
    + キュレートされた環境が追加されました。 これらの環境は、一般的な機械学習タスク用のライブラリを使用してあらかじめ構成されており、実行時間を短縮するため、Docker イメージとして事前にビルドおよびキャッシュされています。 既定で、"AzureML" というプレフィックスが付けられて、[ワークスペース](/python/api/azureml-core/azureml.core.workspace%28class%29)の環境の一覧に表示されます。

  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + ADB と HDI に対する ONNX の変換サポートが追加されました

+ **プレビュー機能**
  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + テキスト フィーチャライザーとして BERT と BiLSTM がサポートされました (プレビューのみ)
    + 列の目的およびトランスフォーマーのパラメーターに対して特性付けのカスタマイズがサポートされました (プレビューのみ)
    + トレーニングの間にユーザーがモデルの説明を有効にしたときに、生の説明がサポートされるようになりました (プレビューのみ)
    + トレーニング可能なパイプラインとして、`timeseries` 予測用の Prophet が追加されました (プレビューのみ)

  + **azureml-contrib-datadrift**
    + azureml-contrib-datadrift から azureml-datadrift にパッケージが再配置されました。`contrib` パッケージは、今後のリリースで削除される予定です

+ **バグの修正と機能強化**
  + **azureml-automl-core**
    + FeaturizationConfig が AutoMLConfig と AutoMLBaseSettings に導入されました
    + FeaturizationConfig が [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) と AutoMLBaseSettings に導入されました
      + 特性付けの列の目的が特定の列と特徴の種類でオーバーライドされます
      + トランスフォーマー パラメーターがオーバーライドされます
    + explain_model() および retrieve_model_explanations() の非推奨メッセージが追加されました
    + トレーニング可能なパイプラインとして Prophet が追加されました (プレビューのみ)
    + explain_model() および retrieve_model_explanations() の非推奨メッセージが追加されました。
    + トレーニング可能なパイプラインとして Prophet が追加されました (プレビューのみ)。
    + ターゲットのラグ、ローリング ウィンドウのサイズ、最大の水平の自動検出のサポートが追加されました。 target_lags、target_rolling_window_size、または max_horizon のいずれかが "auto" に設定されている場合、トレーニング データに基づいて対応するパラメーターの値を推定するために、ヒューリスティックが適用されます。
    + データ セットに 1 つのグレイン列が含まれている場合の予測を修正しました。このグレインは数値型であり、トレーニング セットとテスト セットの間にギャップがあります
    + 予測タスクでのリモート実行での重複インデックスに関するエラー メッセージを修正しました
    + データ セットに 1 つのグレイン列が含まれている場合の予測を修正しました。このグレインは数値型であり、トレーニング セットとテスト セットの間にギャップがあります。
    + 予測タスクでのリモート実行での重複インデックスに関するエラー メッセージを修正しました。
    + データセットが不均衡かどうかを確認するためのガードレールが追加されました。 そうである場合は、ガードレール メッセージがコンソールに書き込まれます。
  + **azureml-core**
    + モデル オブジェクトを使用してストレージ内のモデルへの SAS URL を取得する機能が追加されました。 例: model.get_sas_url()
    + 送信された実行に関連付けられたデータセットを取得するための `run.get_details()['datasets']` が導入されます
    + JSON Lines ファイルから TabularDataset を作成する API `Dataset.Tabular.from_json_lines_files` が追加されます。 TabularDataset での JSON Lines ファイルのこの表形式データの詳細については、ドキュメントの[この記事](how-to-create-register-datasets.md)を参照してください。
    + 追加の VM サイズ フィールド (OS ディスク、GPU の数) が supported_vmsizes() 関数に追加されました
    + 実行、プライベートおよびパブリック IP、ポートなどを表示する追加のフィールドが、list_nodes() 関数に追加されました。
    + クラスターのプロビジョニング時に新しいフィールドを指定できるようになりました。--remotelogin_port_public_access は、クラスターの作成時に SSH ポートを開いたままにするか、閉じるかに応じて、有効または無効に設定できます。 それを指定しない場合は、クラスターが VNet の内部にデプロイされるかどうかに応じて、ポートはサービスによってスマートに開くか、閉じるかされます。
  + **azureml-explain-model**
  + **[azureml-core](/python/api/azureml-core/azureml.core)**
    + モデル オブジェクトを使用してストレージ内のモデルへの SAS URL を取得する機能が追加されました。 例: model.[get_sas_url()](/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + 送信された実行に関連付けられたデータセットを取得するための run.[get_details](/python/api/azureml-core/azureml.core.run%28class%29#get-details--)['datasets'] が導入されます
    + JSON Lines ファイルから TabularDataset を作成する API `Dataset.Tabular`.[from_json_lines_files()](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) が追加されます。 TabularDataset での JSON Lines ファイルのこの表形式データの詳細については、 https://aka.ms/azureml-data のドキュメントを参照してください。
    + 追加の VM サイズ フィールド (OS ディスク、GPU の数) が [supported_vmsizes()](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-) 関数に追加されました
    + 実行、プライベートおよびパブリック IP、ポートなどを表示する追加のフィールドが、[list_nodes()](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) 関数に追加されました。
    + クラスターの[プロビジョニング](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--)時に新しいフィールドを指定できるようになりました。クラスターの作成時に SSH ポートを開いたままにするか、閉じるかに応じて、enabled または disabled に設定できます。 それを指定しない場合は、クラスターが VNet の内部にデプロイされるかどうかに応じて、ポートはサービスによってスマートに開くか、閉じるかされます。
  + **azureml-explain-model**
    + 分類シナリオでの説明の出力に関するドキュメントが改善されました。
    + 評価の例の説明で予測された y 値をアップロードする機能が追加されました。 視覚化がいっそう有用になります。
    + 説明プロパティが MimicWrapper に追加され、基になる MimicExplainer を取得できるようになりました。
  + **azureml-pipeline-core**
    + Module、ModuleVersion、ModuleStep について説明するノートブックが追加されました
  + **azureml-pipeline-steps**
    + AML パイプラインによる R スクリプトの実行をサポートする RScriptStep が追加されました。
    + "パラメーター SubscriptionId の割り当てが指定されていない" というエラー メッセージの原因になっていた AzureBatchStep でのメタデータ パラメーターの解析を修正しました。
  + **azureml-train-automl**
    + データ入力形式として、training_data、validation_data、label_column_name、weight_column_name がサポートされるようになりました
    + explain_model() および retrieve_model_explanations() の非推奨メッセージが追加されました
  + **[azureml-pipeline-core](/python/api/azureml-pipeline-core)**
    + [Module](/python/api/azureml-pipeline-core/azureml.pipeline.core.module%28class%29)、[ModuleVersion、および [ModuleStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep) について説明する[ノートブック](https://aka.ms/pl-modulestep)が追加されました。
  + **[azureml-pipeline-steps](/python/api/azureml-pipeline-steps)**
    + AML パイプラインによる R スクリプトの実行をサポートする [RScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) が追加されました。
    + "パラメーター SubscriptionId の割り当てが指定されていない" というエラー メッセージの原因になっていた [AzureBatchStep でのメタデータ パラメーターの解析を修正しました。
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + データ入力形式として、training_data、validation_data、label_column_name、weight_column_name がサポートされるようになりました。
    + [explain_model()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) および [retrieve_model_explanations()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-) の非推奨メッセージが追加されました。


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Azure Machine Learning SDK for Python v1.0.62

+ **新機能**
  + TabularDataset に `timeseries` 特性が導入されました。 この特性により、ある期間のすべてのデータまたは最新のデータを取得するなど、TabularDataset のデータに対するタイムスタンプのフィルター処理が容易になります。  https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb のノートブックの例を参照してください。
  + TabularDataset と FileDataset でのトレーニングが有効になりました。 

  + **azureml-train-core**
      + PyTorch Estimator に `Nccl` および `Gloo` のサポートが追加されました

+ **バグの修正と機能強化**
  + **azureml-automl-core**
    + AutoML の設定 "lag_length" および LaggingTransformer が非推奨になりました。
    + データフロー形式で指定されている場合の入力データの正しい検証を修正しました
    + グラフ json を生成して成果物にアップロードするように fit_pipeline.py を変更しました。
    + `Cytoscape` を使用して `userrun` の下にグラフを表示しました。
  + **azureml-core**
    + ADB コードでの例外処理を再検討し、新しいエラー処理に従って変更しました。
    + Notebook VM の自動 MSI 認証を追加しました。
    + 再試行失敗のために破損したモデルまたは空のモデルがアップロードされるバグを修正しました。
    + `DataReference` モードが変更されたときに (`as_upload`、`as_download`、`as_mount` を呼び出したときなど) `DataReference` の名前が変更されるバグを修正しました。
    + `FileDataset.mount` および `FileDataset.download` に対する `mount_point` と `target_path` を省略可能にしました
    + 細かいタイムスタンプ列が割り当てられずに時刻のシリアル関連 API が呼び出された場合や、割り当てられたタイムスタンプ列が削除された場合、タイムスタンプ列が見つからないという例外がスローされます。
    + Date 型列には時刻のシリアル列が割り当てられている必要があり、そうでないと例外が予想されます
    + 時刻のシリアル列割り当て API "with_timestamp_columns" は、細かい/粗いタイムスタンプ列名として None 値を受け取ることができ、これにより以前に割り当てられたタイムスタンプ列がクリアされます。
    + 粗い粒度または細かい粒度のタイムスタンプ列が削除されると例外がスローされ、ユーザーに対して、削除リストのタイムスタンプ列を除外するか、None 値を指定して with_time_stamp を呼び出すことによりタイムスタンプを解放した後で、削除できることが示されます
    + 粗い粒度または細かい粒度のタイムスタンプ列が保持列リストに含まれていないと例外がスローされ、ユーザーに対して、保持列リストにタイムスタンプ列を含めるか、None 値を指定して with_time_stamp を呼び出すことによりタイムスタンプを解放した後で、保持できることが示されます。
    + 登録済みモデルのサイズのログ記録を追加しました。
  + **azureml-explain-model**
    + "packaging" Python パッケージがインストールされていない場合にコンソールに出力される警告を修正しました: "Using older than supported version of lightgbm, please upgrade to version greater than 2.2.1" (サポートされているバージョンより古い lightgbm を使用しています。2.2.1 より後のバージョンにアップグレードしてください)
    + 多くの機能でグローバルな説明に対するシャーディングに関するダウンロード モデルの説明を修正しました
    + 出力の説明で初期化の例がない mimic explainer を修正しました
    + 2 つの異なる種類のモデルを使用する説明クライアントでアップロードするときの設定プロパティの不変エラーを修正しました
    + 1 つのスコアリング Explainer でエンジニアリングされた値と生の値の両方を返すことができるように、スコアリング explainer.explain() に get_raw パラメーターを追加しました。
  + **azureml-train-automl**
    + `automl` 説明 SDK からの説明をサポートするために、AutoML からのパブリック API が導入されました - AutoML の特徴付け SDK と説明 SDK を分離することによる、AutoML の説明をサポートする新しい方法 - AutoML モデルに対して AzureML 説明 SDK からの生の説明のサポートを統合しました。
    + リモート トレーニング環境からの azureml-defaults の削除。
    + 既定のキャッ シュストアの場所を、FileCacheStore ベースの場所から、Azure Databricks コード パス上の AutoML に対する AzureFileCacheStore の場所に変更しました。
    + データフロー形式で指定されている場合の入力データの正しい検証を修正しました
  + **azureml-train-core**
    + source_directory_data_store の非推奨が元に戻されました。
    + AzureML でインストールされるパッケージのバージョンをオーバーライドする機能が追加されました。
    + Estimator の `environment_definition` パラメーターに dockerfile のサポートが追加されました。
    + Estimator での分散トレーニング パラメーターが簡略化されました。

         ```python
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Azure Machine Learning ワークスペースの新しい Web エクスペリエンス (プレビュー)
データ サイエンティストやデータ エンジニアは、新しい Web エクスペリエンスで、データの準備および視覚化から、モデルのトレーニングおよびデプロイまでの機械学習のエンドツーエンドのライフサイクルを 1 つの場所で完了できます。

![Azure Machine Learning ワークスペースの UI (プレビュー)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**主な機能:**

新しい Azure Machine Learning インターフェイスでは、次のことができるようになりました。
+ ご自分のノートブックまたはリンクを Jupyter 外で管理する
+ [自動 ML の実験を実行する](tutorial-first-experiment-automated-ml.md)
+ [ローカル ファイル、データストアおよび Web ファイルからデータセットを作成する](how-to-create-register-datasets.md)
+ モデル作成のためにデータセットを調査および準備する
+ ご自身のモデルのデータ誤差を監視する
+ ダッシュボードに最近使用したリソースを表示する

このリリースの時点では、次のブラウザーがサポートされています:Chrome、Firefox、Safari、Microsoft Edge プレビュー。

**既知の問題:**

1. デプロイ中に "Something went wrong! Error loading chunk files" (問題が発生しました。チャンク ファイルの読み込み中にエラーが発生しました) と表示される場合、ご使用のブラウザーを更新します。

1. Notebooks と Files でファイルを削除または名前変更できない。 パブリック プレビュー中に、Notebook VM の Jupyter UI またはターミナルを使用して、ファイルの更新操作を実行できます。 これはマウントされたネットワーク ファイル システムであるため、Notebook VM に対して行ったすべての変更は、直ちに Notebook ワークスペースに反映されます。

1. Notebook VM に SSH 接続するには:
   1. VM のセットアップ時に作成された SSH キーを検索します。 または、Azure Machine Learning ワークスペースでそのキーを探します。[コンピューティング] タブを開き、一覧から Notebook VM を見つけ、そのプロパティを開いて、そのダイアログからキーをコピーします。
   1. それらの公開および秘密 SSH キーをご自分のローカル コンピューターにインポートします。
   1. それらを Notebook VM への SSH 接続に使用します。

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Azure Machine Learning SDK for Python v1.0.60

+ **新機能**
  + データストアまたはパブリック URL 内の 1 つまたは複数のファイルを参照する FileDataset が導入されました。 ファイルの形式は任意です。 FileDataset では、ファイルをダウンロードしたり、コンピューターにマウントしたりできます。 
  + PythonScript Step、Adla Step、Databricks Step、DataTransferStep、AzureBatch Step にパイプライン YAML サポートを追加しました

+ **バグの修正と機能強化**
  + **azureml-automl-core**
    + AutoArima は、プレビュー専用のサジェスト可能パイプラインになりました。
    + 予測のエラー報告を改善しました。
    + 予測タスクの汎用ではなくカスタムの例外を使用してログ記録を改善しました。
    + イテレーションの合計数よりも少なくなるように、max_concurrent_iterations のチェックを削除しました。
    + AutoML モデルで AutoMLExceptions が返されるようになりました
    + このリリースでは、自動化された機械学習のローカル実行の実行パフォーマンスが向上しています。
  + **azureml-core**
    + 登録名でキーが指定された `TabularDataset` と `FileDataset` のオブジェクトのディクショナリを返す Dataset.get_all(workspace) が導入されました。

    ```python
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + `Dataset.Tabular.from_delimited_files` と `Dataset.Tabular.from_parquet.files` の引数として `parition_format` が導入されました。 各データ パスのパーティション情報は、指定された形式に基づいて列に抽出されます。 '{column_name}' では文字列の列、'{column_name:yyyy/MM/dd/HH/mm/ss}' では datetime の列が作成されます。ここで、'yyyy'、'MM'、'dd'、'HH'、'mm'、'ss' は datetime 型の年、月、日、時間、分、秒の抽出に使用されます。 partition_format は、最初のパーティション キーの位置から始まり、ファイル パスの末尾までになります。 たとえば、パーティションが国と時間のパス '../USA/2019/01/01/data.csv' の場合、partition_format='/{Country}/{PartitionDate:yyyy/MM/dd}/data.csv' では値が 'USA' の文字列の列 'Country' と、値が '2019-01-01' の datetime 列 'PartitionDate' が作成されます。
        ```python
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + `Dataset.Tabular.from_delimited_files` と `Dataset.Tabular.from_parquet.files` の引数として `partition_format` が導入されました。 各データ パスのパーティション情報は、指定された形式に基づいて列に抽出されます。 '{column_name}' では文字列の列、'{column_name:yyyy/MM/dd/HH/mm/ss}' では datetime の列が作成されます。ここで、'yyyy'、'MM'、'dd'、'HH'、'mm'、'ss' は datetime 型の年、月、日、時間、分、秒の抽出に使用されます。 partition_format は、最初のパーティション キーの位置から始まり、ファイル パスの末尾までになります。 たとえば、パーティションが国と時間のパス '../USA/2019/01/01/data.csv' の場合、partition_format='/{Country}/{PartitionDate:yyyy/MM/dd}/data.csv' では値が 'USA' の文字列の列 'Country' と、値が '2019-01-01' の datetime 列 'PartitionDate' が作成されます。
    + `to_csv_files` と `to_parquet_files` のメソッドが `TabularDataset` に追加されました。 これらのメソッドにより、指定された形式のファイルにデータが変換され、`TabularDataset` と `FileDataset` の間の変換が可能になります。
    + Model.package() によって生成された Dockerfile を保存するときに、基本イメージ レジストリに自動的にログインします。
    + 'gpu_support' は、不要になりました。AML は nvidia docker 拡張機能が利用可能になったときに自動的にそれを検出し、使用するようになりました。 将来のリリースでは削除される予定です。
    + PipelineDrafts を作成、更新、および使用するためのサポートが追加されました。
    + このリリースでは、自動化された機械学習のローカル実行の実行パフォーマンスが向上しています。
    + ユーザーは、実行履歴のメトリックを名前でクエリできます。
    + 予測タスクの汎用ではなくカスタムの例外を使用してログ記録を改善しました。
  + **azureml-explain-model**
    + feature_maps パラメーターが新しい MimicWrapper に追加され、ユーザーが未加工の機能の説明を取得できるようになりました。
    + 説明のアップロードでは、データセットのアップロードが既定でオフになり、upload_datasets=True を指定して再度有効にすることができます
    + "Is_law" フィルター処理パラメーターが説明リストとダウンロード機能に追加されました。
    + グローバルとローカルの両方の説明オブジェクトに `get_raw_explanation(feature_maps)` メソッドが追加されます。
    + サポートされているバージョンを下回る場合に警告を出力するバージョン チェックが lightgbm に追加されました
    + 説明をバッチ処理するときのメモリ使用量が最適化されました
    + AutoML モデルで AutoMLExceptions が返されるようになりました
  + **azureml-pipeline-core**
    + PipelineDrafts を作成、更新、および使用するためのサポートが追加されました。変更可能なパイプライン定義を維持し、対話形式で実行するために使用できます
  + **azureml-train-automl**
    + GPU 対応 PyTorch v1.1.0 や :::no-loc text="cuda":::CUDA Toolkit 9.0、およびリモートの Python ランタイム環境で BERT/XLNet を有効にするために必要な pytorch-transformer の特定のバージョンをインストールするための機能が作成されました。
  + **azureml-train-core**
    + 一部のハイパーパラメーター空間定義エラーの初期エラーが、サーバー側ではなく SDK で直接発生します。

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure Machine Learning Data Prep SDK v1.1.14
+ **バグの修正と機能強化**
  + 未加工のパスと資格情報を使用した ADLS/ADLSGen2 への書き込みが有効になりました。
  + `include_path=True` が `read_parquet` で使用できなかった原因となったバグを修正しました。
  + "無効なプロパティ値: hostsecret" の例外が原因で発生した `to_pandas_dataframe()` エラーを修正しました。
  + Spark モードで DBFS のファイルを読み取ることができなかったバグを修正しました。

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Azure Machine Learning SDK for Python v1.0.57
+ **新機能**
  + `TabularDataset` を AutomatedML で使用できるようにしました。 `TabularDataset` の詳細については、 https://aka.ms/azureml/howto/createdatasets を参照してください。

+ **バグの修正と機能強化**
  + **azure-cli-ml**
    + Microsoft が生成した証明書と顧客証明書の両方について、AKS クラスターにデプロイされたスコアリング エンドポイントの TLS/SSL 証明書を更新できるようになりました。
  + **azureml-automl-core**
    + ラベルのない行が正常に削除されなかった AutoML の問題を修正しました。
    + AutoML のエラー ログ記録を改良し、常にエラー メッセージ全文がログ ファイルに書き込まれるようになりました。
    + AutoML のパッケージのピン留めを更新し、`azureml-defaults`、`azureml-explain-model`、および `azureml-dataprep` が追加されました。 AutoML で、パッケージの不一致に関する警告は表示されなくなりました (`azureml-train-automl` パッケージを除く)。
    + cv の分割によるサイズの不一致が原因でビンの計算が失敗する、`timeseries` の問題を修正しました。
    + 種類がクロス検証のトレーニングに対してアンサンブル イテレーションを実行するときに、データセット全体でトレーニングされたモデルのダウンロードに問題が発生した場合、モデルの重みと投票アンサンブルに取り込まれていたモデルとの間に矛盾がありました。
    + トレーニングおよび検証ラベル (y と y_valid) が NumPy 配列ではなく、Pandas データフレームの形式で提供されるときに発生するエラーを修正しました。
    + 入力テーブルのブール値の列が None になったときの予測タスクの問題を修正しました。
    + AutoML ユーザーが予測時に十分な長さではないトレーニング シリーズをドロップできるようにしました。 - AutoML ユーザーが予測時にトレーニング セットに存在しないテスト セットからグレインをドロップできるようにしました。
  + **azureml-core**
    + Blob_cache_timeout パラメーターの順序付けに関する問題を修正しました。
    + 外部例外 fit 型 および transform 型をシステム エラーに追加しました。
    + リモート実行のための Key Vault シークレットのサポートが追加されました。 ご自身のワークスペースに関連付けられている keyvault のシークレットを追加、取得、一覧表示するための azureml.core.keyvault.Keyvault クラスを追加しました。 サポートされている操作は次のとおりです。
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.keyvault.Keyvault.set_secret(name, value)
      + azureml.core.keyvault.Keyvault.set_secrets(secrets_dict)
      + azureml.core.keyvault.Keyvault.get_secret(name)
      + azureml.core.keyvault.Keyvault.get_secrets(secrets_list)
      + azureml.core.keyvault.Keyvault.list_secrets()
    + リモート実行中に既定のキー コンテナーやシークレットを取得する、次のメソッドを追加しました。
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.run.Run.get_secret(name)
      + azureml.core.run.Run.get_secrets(secrets_list)
    + submit-hyperdrive CLI コマンドを送信するオーバーライド パラメーターをさらに追加しました。
    + API 呼び出しの信頼性を向上させ、再試行を一般的な要求のライブラリ例外に拡大します。
    + 送信済みの実行からの実行を送信するためのサポートを追加します。
    + 最初のトークンの有効期限が切れた後にファイルがアップロードされない原因となった FileWatcher の期限が切れる SAS トークンの問題を修正しました。
    + データセット Python SDK への HTTP csv/tsv ファイルのインポートがサポートされるようになりました。
    + Workspace.setup() メソッドを非推奨にしました。 ユーザーに表示される警告メッセージとして、代わりに create() または get()/from_config() の使用が推奨されるようになりました。
    + Environment.add_private_pip_wheel() を追加しました。これにより、カスタマイズしたプライベート Python パッケージ `whl` をワークスペースにアップロードし、それらを安全に利用して環境をビルド/具体化できるようにしました。
    + Microsoft が生成した証明書と顧客証明書の両方について、AKS クラスターにデプロイされたスコアリング エンドポイントの TLS/SSL 証明書を更新できるようになりました。
  + **azureml-explain-model**
    + アップロードの説明にモデル ID を追加するためのパラメーターを追加しました。
    + メモリとアップロードの説明に `is_raw` のタグを追加しました。
    + PyTorch のサポートと azureml-explain-model パッケージに対するテストを追加しました。
  + **azureml-opendatasets**
    + 自動テスト環境の検出とログ記録をサポートします。
    + 米国の人口を郡および ZIP コード別に取得するクラスを追加しました。
  + **azureml-pipeline-core**
    + 入力ポートと出力ポートの定義にラベル プロパティが追加されました。
  + **azureml-telemetry**
    + 不適切なテレメトリ構成を修正しました。
  + **azureml-train-automl**
    + セットアップ失敗時に、セットアップ実行に対して [エラー] フィールドにログが記録されず、そのため親の実行の [エラー] に格納されなかったバグを修正しました。
    + ラベルのない行が正常に削除されなかった AutoML の問題を修正しました。
    + AutoML ユーザーが予測時に十分な長さではないトレーニング シリーズをドロップできるようにしました。
    + AutoML ユーザーが予測時にトレーニング セットに存在しないテスト セットからグレインをドロップできるようにしました。
    + AutoMLStep で、新しい構成パラメーターの変更や追加に関する問題を回避するために、`automl` の構成がバックエンドにパススルーされるようになりました。
    + AutoML データ ガードレールがパブリック プレビューになりました。 トレーニング後にデータ ガードレール レポート (分類/回帰タスク用) が表示され、ユーザーがそれに SDK API を使用してアクセスすることもできます。
  + **azureml-train-core**
    + PyTorch エスティメーターに torch 1.2 のサポートが追加されました。
  + **azureml-widgets**
    + 分類トレーニングのために混同行列グラフを改良しました。

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning Data Prep SDK v1.1.12
+ **新機能**
  + 文字列のリストを入力として `read_*` メソッドに渡すことができるようになりました。

+ **バグの修正と機能強化**
  + `read_parquet` を Spark で実行するときのパフォーマンスが向上しています。
  + あいまいな日付形式の列が 1 つ存在することで `column_type_builder` が失敗する問題を修正しました。

### <a name="azure-portal"></a>Azure portal
+ **プレビュー機能**
  + ログと出力ファイルのストリーミングが、[実行の詳細] ページで使用できるようになりました。 プレビューの切り替えが有効になっている場合、ファイルによってリアルタイムで更新がストリーミングされます。
  + ワークスペース レベルでクォータを設定する機能がプレビューでリリースされます。 AmlCompute クォータはサブスクリプション レベルで割り当てられますが、ワークスペース間でそのクォータを配分し、公平な共有とガバナンスのために割り当てることができるようになりました。 ワークスペースの左側のナビゲーション バーにある **[Usages+Quotas]\(使用量 + クォータ\)** ブレードをクリックして、 **[Configure Quotas]\(クォータの構成\)** タブを選択します。これはワークスペースをまたぐ操作であるため、ワークスペース レベルでクォータを設定できるようにするには、サブスクリプション管理者である必要があります。

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Azure Machine Learning SDK for Python v1.0.55

+ **新機能**
  + AKS にデプロイされたスコアリング エンドポイントへの呼び出しに対して、トークンベースの認証がサポートされるようになりました。 現在のキー ベースの認証は引き続きサポートされ、ユーザーはこれらの認証メカニズムのいずれか一方を使用できます。
  + 仮想ネットワーク (VNet) の内側にある BLOB ストレージをデータストアとして登録する機能。

+ **バグの修正と機能強化**
  + **azureml-automl-core**
    + CV 分割の検証サイズが小さくて、回帰や予測の真のグラフに対して予測が不適切になるバグを修正しました。
    + リモート実行での予測タスクのログ記録が改善され、実行が失敗した場合に、ユーザーに包括的なエラー メッセージが提供されるようになりました。
    + 前処理フラグが True の場合の `Timeseries` のエラーを修正しました。
    + いくつかの予測データ検証エラー メッセージをより実用的なものにしました。
    + データセットの削除や遅延読み込みによって (特にプロセスの生成間)、AutoML のメモリ消費量を削減しました
  + **azureml-contrib-explain-model**
    + Explainer に model_task フラグを追加し、ユーザーがモデルの種類の既定の自動推論ロジックをオーバーライドできるようにしました
    + ウィジェットの変更:`contrib` で自動的にインストールされます。`nbextension` ではインストールも有効化もされなくなります。グローバルな機能の重要性 (Permutative など) に関する説明がサポートされます
    + ダッシュボードの変更: - [概要] ページでの `beeswarm` プロットに加えて、box プロットと violin プロット - "Top -k'" スライダー変更での `beeswarm` プロットのレンダリングの高速化 - top-k 計算方法を説明する便利なメッセージ - データが提供されないときのグラフに代わる便利なカスタマイズ可能なメッセージ
  + **azureml-core**
    + モデルとその依存関係をカプセル化する Docker イメージと Dockerfile を作成するための Model.package() メソッドを追加しました。
    + Environment オブジェクトを含む InferenceConfig を受け入れるようにローカルな Web サービスを更新しました。
    + model_path パラメーターとして "." (現在のディレクトリ) が渡されたときに無効なモデルを生成する Model.register() を修正しました。
    + Run.submit_child を追加しました。機能は Experiment.submit と同じですが、送信された子実行の親として実行を指定します。
    + Run.register_model で Model.register からの構成オプションがサポートされるようになりました。
    + 既存のクラスターで JAR ジョブを実行する機能。
    + instance_pool_id パラメーターと cluster_log_dbfs_path パラメーターをサポートするようになりました。
    + Web サービスにモデルをデプロイするときに、Environment オブジェクトの使用のサポートを追加しました。 InferenceConfig オブジェクトの一部として Environment オブジェクトを提供できるようになりました。
    + 次の新しいリージョンに対する appinsifht のマッピングを追加しました - centralus - westus - northcentralus
    + すべての Datastore クラスのすべての属性に関するドキュメントを追加しました。
    + blob_cache_timeout パラメーターを `Datastore.register_azure_blob_container` に追加しました。
    + save_to_directory メソッドと load_from_directory メソッドを azureml.core.environment.Environment に追加しました。
    + "az ml environment download" コマンドと "az ml environment register" コマンドを CLI に追加しました。
    + Environment.add_private_pip_wheel メソッドを追加しました。
  + **azureml-explain-model**
    + Dataset サービス を使用する説明に対してデータセットの追跡を追加しました (プレビュー)。
    + グローバルな説明をストリーミングするときの既定のバッチ サイズを、10k から 100 に減らしました。
    + Explainer に model_task フラグを追加し、ユーザーがモデルの種類の既定の自動推論ロジックをオーバーライドできるようにしました。
  + **azureml-mlflow**
    + 入れ子になったディレクトリが無視される mlflow.azureml.build_image のバグを修正しました。
  + **azureml-pipeline-steps**
    + 既存の Azure Databricks クラスターで JAR ジョブを実行する機能を追加しました。
    + DatabricksStep ステップに対する instance_pool_id パラメーターと cluster_log_dbfs_path パラメーターのサポートを追加しました。
    + DatabricksStep ステップでのパイプライン パラメーターのサポートを追加しました。
  + **azureml-train-automl**
    + アンサンブル関連のファイルに対する `docstrings` を追加しました。
    + `max_cores_per_iteration` および `max_concurrent_iterations` に対する言語がより適切になるようにドキュメントを更新しました
    + リモート実行での予測タスクのログ記録が改善され、実行が失敗した場合に、ユーザーに包括的なエラー メッセージが提供されるようになりました。
    + パイプラインの `automlstep` ノートブックから get_data を削除しました。
    + `automlstep`での `dataprep` のサポートを開始しました。

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning Data Prep SDK v1.1.10

+ **新機能**
  + 特定の列に対して特定のインスペクター (ヒストグラム、散布図など) の実行を要求できるようになりました。
  + `append_columns` に並列化引数を追加しました。 True の場合、データはメモリに読み込まれますが、実行は並列で実行されます。False の場合、実行はストリーミングされますが、シングル スレッドです。

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Azure Machine Learning SDK for Python v1.0.53

+ **新機能**
  + 自動化された機械学習で、リモート コンピューティング ターゲットでの ONNX モデルのトレーニングがサポートされるようになりました
  + Azure Machine Learning で、以前の実行、チェックポイント、またはモデル ファイルからトレーニングを再開できるようになりました。
    + [推定器を使用して前回の実行からトレーニングを再開する](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)方法を確認してください

+ **バグの修正と機能強化**
  + **azure-cli-ml**
    + CLI コマンド "model deploy" と "service update" は、パラメーター、構成ファイル、またはこれらの 2 つの組み合わせを受け入れるようになりました。 パラメーターは、ファイル内の属性よりも優先されます。
    + 登録後にモデルの説明を更新できるようになりました
  + **azureml-automl-core**
    + NimbusML 依存関係を 1.2.0 バージョン (現在の最新バージョン) に更新します。
    + AutoML 推定器内で使用される NimbusML 推定器とパイプラインのサポートの追加。
    + アンサンブルの選択手順で、スコアが一定のままであっても、結果のアンサンブルが過剰に増加するバグの修正。
    + 予測タスクのための CV 分割全体での一部の特徴量化を再利用できるようにします。 これにより、遅延やローリング時間帯のような負荷の高い特徴付けで、セットアップ実行の実行時間がほぼ n_cross_validations 倍高速になります。
    + 時間が Pandas でサポートされている時間の範囲外である場合の問題への対処。 時間が pd.Timestamp.min より小さいか pd.Timestamp.max より大きい場合、DataException を発生させるようになりました
    + 予測で、調整可能な場合は、トレーニングとテストのセットで異なる周波数を使用できるようになりました。 たとえば、"1 月に開始される四半期" と "10 月に開始される四半期" を調整できます。
    + プロパティ "parameters" が TimeSeriesTransformer に追加されました。
    + 古い例外クラスを削除します。
    + 予測タスクで、パラメーター `target_lags` が 1 つの整数値または整数のリストを受け入れるようになりました。 整数が指定されている場合は、1 つのラグだけが作成されます。 リストが指定されている場合は、ラグの一意の値が取得されます。 target_lags=[1, 2, 2, 4] では、1、2、および 4 つの期間のラグが作成されます。
    + 変換後の列の型の損失に関するバグを修正します (リンクされているバグ)。
    + `model.forecast(X, y_query)` では、y_query を、先頭に None を含んだオブジェクト型にできるようにします (#459519)。
    + `automl` 出力に予期される値を追加します
  + **azureml-contrib-datadrift**
    +  azureml-contrib-opendatasets の代わりに azureml-opendatasets への切り替えを含むノートブックの例の向上と、データを強化する際のパフォーマンスの向上
  + **azureml-contrib-explain-model**
    + azureml-contrib-explain-model パッケージの生の特徴の重要度のための LIME Explainer の変換引数を修正しました
    + AzureML-contrib-explain-model パッケージの Image Explainer のイメージの説明にセグメント化を追加しました
    + LimeExplainer の scipy sparse のサポートを追加します
    + グローバル説明を一括でストリーミングして DecisionTreeExplainableModel の実行時間を向上させるために、`include_local=False` の場合の mimic explainer に `batch_size` を追加します
  + **azureml-contrib-featureengineering**
    + set_featurizer_timeseries_params () の呼び出しを修正しました: dict 値の型変更と null チェック - `timeseries` フィーチャライザーのノートブックを追加しました
    + NimbusML 依存関係を 1.2.0 バージョン (現在の最新バージョン) に更新します。
  + **azureml-core**
    + AzureML CLI で DBFS データストアをアタッチする機能を追加しました
    + データストアのアップロードに関する問題 (`target_path` が `/` で始まる場合に空のフォルダーが作成されるバグ) を修正しました
    + ServicePrincipalAuthentication の `deepcopy` の問題を修正しました。
    + "az ml environment show" コマンドと "az ml environment list" コマンドを CLI に追加しました。
    + 環境で、already-built base_image の代わりに base_dockerfile をサポートするようになりました。
    + 使用されていない RunConfiguration 設定、auto_prepare_environment は非推奨としてマークされるようになっています。
    + 登録後にモデルの説明を更新できるようになりました
    + バグの修正:モデルとイメージの削除の際、アップストリームの依存関係が原因で削除が失敗した場合に、それらに依存しているアップストリーム オブジェクトの取得に関する詳細情報が提供されるようになりました。
    + 一部の環境でワークスペースの作成時に発生する、デプロイの空白の期間が出力されたバグを修正しました。
    + ワークスペース作成に対するエラーの例外が改善されました。 ユーザーに対して "Unable to create workspace. Unable to find..." (ワークスペースを作成できません... を検索できません) というメッセージは表示されず、実際の作成エラーが代わりに表示されます。
    + AKS Web サービスのトークン認証のサポートが追加されました。
    + `Webservice` オブジェクトに `get_token()` メソッドを追加します。
    + 機械学習データセットを管理するための CLI サポートを追加しました。
    + `Datastore.register_azure_blob_container` は、必要に応じて、このデータストアのキャッシュの有効期限を有効にする blobfuse のマウント パラメーターを構成する `blob_cache_timeout` 値 (秒単位) を取得するようになりました。 既定では、BLOB が読み込まれたときのようなタイムアウトはなく、ジョブが終了するまでローカル キャッシュにとどまります。 ほとんどのジョブでこの設定が優先されますが、一部のジョブは、そのノードに収まりきらない大きなデータセットからデータを読み取る必要があります。 このようなジョブでは、このパラメーターを調整すると成功します。 このパラメーターを調整する場合は注意が必要です。値を小さく設定すると、エポックで使用されるデータが再度使用される前に期限切れになる場合があるため、パフォーマンスが低下する可能性があります。 すべての読み取りがローカル キャッシュではなく BLOB ストレージやネットワークから実行され、これがトレーニング時間に悪影響を及ぼします。
    + 登録後にモデルの説明を適切に更新できるようになりました
    + モデルとイメージを削除する場合に、それらに依存するアップストリーム オブジェクト (削除が失敗する原因となります) に関する詳細情報が提供されるようになりました
    + azureml.mlflow を使用するリモート実行のリソース使用率を向上させます。
  + **azureml-explain-model**
    + azureml-contrib-explain-model パッケージの生の特徴の重要度のための LIME Explainer の変換引数を修正しました
    + LimeExplainer の scipy sparse のサポートを追加します
    + 線形モデルを説明する Shape Linear Explainer ラッパーと、別のレベルの Tabular Explainer を追加しました
    + 説明モデル ライブラリの Mimic Explainer で、スパース データ入力で include_local = False の場合のエラーを修正しました
    + `automl` 出力に予期される値を追加します
    + 生の特徴の重要度を取得するために変換引数が指定された場合の、permutation feature importance を修正しました
    + グローバル説明を一括でストリーミングして DecisionTreeExplainableModel の実行時間を向上させるために、`include_local=False` の場合の mimic explainer に `batch_size` を追加します
    + モデル説明ライブラリで、予測のために Pandas データフレーム入力が必要な Blackbox Explainer を修正しました
    + `explanation.expected_values` で、float を含むリストではなく、float が返されることがあったバグを修正しました。
  + **azureml-mlflow**
    + mlflow.set_experiment(experiment_name) のパフォーマンスを向上させます
    + mlflow tracking_uri の InteractiveLoginAuthentication の使用に関するバグを修正します
    + azureml.mlflow を使用するリモート実行のリソース使用率を向上させます。
    + azureml-mlflow パッケージのドキュメントが改善されます
    + mlflow.log_artifacts("my_dir") で、"<artifact-paths>" ではなく "my_dir/<artifact-paths>" の下にアーティファクトが保存されるバグにパッチを適用します
  + **azureml-opendatasets**
    + 新たに発生したメモリの問題により、`opendatasets` の `pyarrow` を古いバージョン (< 0.14.0) に固定します。
    + azureml-contrib-opendatasets を azureml-opendatasets に移動します。
    + オープン データセット クラスを Azure Machine Learning ワークスペースに登録し、AML データセットの機能をシームレスに利用できるようにします。
    + 非 SPARK バージョンでの NoaaIsdWeather のエンリッチ パフォーマンスを大幅に改善しました。
  + **azureml-pipeline-steps**
    + DatabricksStep の入力と出力に対して DBFS Datastore がサポートされるようになりました。
    + Azure Batch Step の入力/出力に関するドキュメントが更新されました。
    + AzureBatchStep で、*delete_batch_job_after_finish* の既定値を *true* に変更しました。
  + **azureml-telemetry**
    +  azureml-contrib-opendatasets を azureml-opendatasets に移動します。
    + オープン データセット クラスを Azure Machine Learning ワークスペースに登録し、AML データセットの機能をシームレスに利用できるようにします。
    + 非 SPARK バージョンでの NoaaIsdWeather のエンリッチ パフォーマンスを大幅に改善しました。
  + **azureml-train-automl**
    + 実際の戻り値の型を反映し、主なプロパティの取得に関する注意事項を追加するために、get_output のドキュメントを更新しました。
    + NimbusML 依存関係を 1.2.0 バージョン (現在の最新バージョン) に更新します。
    + `automl` 出力に予期される値を追加します
  + **azureml-train-core**
    + 自動化されたハイパーパラメーター チューニングのコンピューティング ターゲットとして、文字列が受け入れられるようになりました
    + 使用されていない RunConfiguration 設定、auto_prepare_environment は非推奨としてマークされるようになっています。

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure Machine Learning Data Prep SDK v1.1.9

+ **新機能**
  + http または https の url からファイルを直接読み取るためのサポートが追加されました。

+ **バグの修正と機能強化**
  + リモート ソースから Parquet データセットを読み取ろうとしたとき (現時点ではサポートされていません) に表示されるエラー メッセージが改善されました。
  + ADLS Gen 2 で Parquet ファイル形式に書き込むとき、およびパスの ADLS Gen 2 コンテナー名を更新するときのバグが修正されました。

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>ビジュアル インターフェイス
+ **プレビュー機能**
  + ビジュアル インターフェイスに "R スクリプトの実行" モジュールを追加しました。

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Azure Machine Learning SDK for Python v1.0.48

+ **新機能**
  + **azureml-opendatasets**
    + **azureml-contrib-opendatasets** は、**azureml-opendatasets** として使用できるようになりました。 古いパッケージも引き続き機能しますが、より高度な機能と機能強化を実現するために、今後は **azureml-opendatasets** を使用することをお勧めします。
    + この新しいパッケージを使用すると、オープン データセットを Azure Machine Learning ワークスペースのデータセットとして登録し、そのデータセットで提供される任意の機能を活用できます。
    + これには、オープン データセットを Pandas/SPARK データフレームとして使用したり、天気などの一部のデータセット用に場所を結合するなど、既存の機能も含まれます。

+ **プレビュー機能**
    + HyperDriveConfig では、パイプライン オブジェクトをパラメーターとして受け取れるようになりました。これにより、パイプラインを使用したハイパーパラメーターのチューニングがサポートされます。

+ **バグの修正と機能強化**
  + **azureml-train-automl**
    + 変換後の列の型の損失に関するバグを修正しました。
    + y_query を、先頭に None を含んだオブジェクト型にできるバグを修正しました。
    + アンサンブルの選択手順で、スコアが一定のままであっても、結果のアンサンブルが過剰に増加する問題を修正しました。
    + AutoMLStep での許可 list_models と ブロック list_models の設定に関する問題を修正しました。
    + Azure ML パイプラインのコンテキストで AutoML が使用された場合に、前処理の使用が妨げられる問題を修正しました。
  + **azureml-opendatasets**
    + azureml-contrib-opendatasets を azureml-opendatasets に移動しました。
    + オープン データセット クラスを Azure Machine Learning ワークスペースに登録し、AML データセットの機能をシームレスに利用できるようにしました。
    + 非 SPARK バージョンでの NoaaIsdWeather のエンリッチ パフォーマンスを大幅に改善しました。
  + **azureml-explain-model**
    + 解釈可能性オブジェクトのオンライン ドキュメントを更新しました。
    + グローバル説明を一括でストリーミングして、モデル説明ライブラリの DecisionTreeExplainableModel の実行時間を向上させるために、`include_local=False` の場合の mimic explainer に `batch_size` を追加しました。
    + `explanation.expected_values` で、float を含むリストではなく、float が返されることがあった問題を修正しました。
    + 説明モデル ライブラリの mimic explainer の `automl` 出力に、予期される値が追加されました。
    + 生の特徴の重要度を取得するために変換引数が指定された場合の、permutation feature importance を修正しました。
  + **azureml-core**
    + AzureML CLI で DBFS データストアをアタッチする機能を追加しました。
    + データストアのアップロードに関する問題 (`target_path` が `/` で始まる場合に空のフォルダーが作成される問題) を修正しました。
    + 2 つのデータセットを比較できるようになりました。
    + モデルとイメージの削除の際、アップストリームの依存関係が原因で削除が失敗した場合に、それらに依存しているアップストリーム オブジェクトの取得に関する詳細情報が提供されるようになりました。
    + auto_prepare_environment の使用されていない RunConfiguration 設定が非推奨になりました。
  + **azureml-mlflow**
    + azureml.mlflow を使用するリモート実行のリソース使用率が向上しました。
    + azureml-mlflow パッケージのドキュメントが改善されました。
    + mlflow.log_artifacts("my_dir") で、"artifact-paths" ではなく "my_dir/artifact-paths" の下にアーティファクトが保存される問題を修正しました。
  + **azureml-pipeline-core**
    + すべてのパイプライン ステップの hash_paths パラメーターは非推奨となり、今後削除される予定です。 既定では、source_directory の内容はハッシュ化されます (`.amlignore` または `.gitignore` に記載されているファイルを除く)
    + コンピューティングの種類に固有のモジュールをサポートするために、Module および ModuleStep の改善を続けています (RunConfiguration の統合と、コンピューティングの種類に固有のモジュールをパイプラインで自由に使用できるようにするためのさらなる変更に備えるため)。
  + **azureml-pipeline-steps**
    + AzureBatchStep:入力/出力に関するドキュメントが改善されました。
    + AzureBatchStep:delete_batch_job_after_finish の既定値を true に変更しました。
  + **azureml-train-core**
    + 自動化されたハイパーパラメーター チューニングのコンピューティング ターゲットとして、文字列が受け入れられるようになりました。
    + auto_prepare_environment の使用されていない RunConfiguration 設定が非推奨になりました。
    + `conda_dependencies_file_path` および `pip_requirements_file_path` パラメーターが非推奨となり、`conda_dependencies_file` と `pip_requirements_file` がそれぞれ推奨パラメーターになりました。
  + **azureml-opendatasets**
    + 非 SPARK バージョンでの NoaaIsdWeather のエンリッチ パフォーマンスを大幅に改善しました。

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning SDK for Python v1.0.33 がリリースされました。

+ [FPGA](how-to-deploy-fpga-web-service.md) で Azure ML Hardware Accelerated Models の一般提供が開始されました。
  + [azureml-accel-models パッケージ](how-to-deploy-fpga-web-service.md)を使用して次のことができるようになりました。
    + サポートされているディープ ニューラル ネットワーク (ResNet 50、ResNet 152、DenseNet-121、VGG-16、および SSD-VGG) の重み付けトレーニング
    + サポートされている DNN を使用した転移学習の使用
    + モデル管理サービスへのモデルの登録と、モデルのコンテナー化
    + Azure Kubernetes Service (AKS) クラスターの FPGA を使用した Azure VM へのモデルのデプロイ
  + [Azure Data Box Edge](../databox-online/azure-stack-edge-overview.md) サーバー デバイスへのコンテナーのデプロイ
  + この[サンプル](https://github.com/Azure-Samples/aml-hardware-accelerated-models)を使用した gRPC エンドポイントでのデータのスコア付け

### <a name="automated-machine-learning"></a>自動化された機械学習

+ パフォーマンス最適化のため、機能を一掃し、動的に追加される:::no-loc text="featurizers":::フィーチャライザーを有効化。 新しい:::no-loc text="featurizers":::フィーチャライザー: 作業の埋め込み、証拠の重み付け、ターゲット エンコード、テキスト ターゲット エンコード、クラスターの距離
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

+ **Git 参照の追跡**<br/> お客様から、完全な監査証跡の維持に役立つ、基本的な Git 統合機能についてのご希望をいただいてきました。 Microsoft は Azure ML の主要エンティティに対する Git 関連メタデータ (リポジトリ、コミット、クリーンな状態) の追跡を実装しました。 この情報は、SDK と CLI によって自動的に収集されます。

+ **モデルのプロファイルと検証のサービス**<br/> お客様からよくいただくご不満のなかに、推論サービスに対応付けるコンピューティングの適切なサイズ設定が難しいというものがありました。 Microsoft のモデル プロファイル サービスではユーザーがサンプル入力を指定できるため、16 種類の CPU/メモリ設定をプロファイルして、デプロイに最適なサイズを決定できます。

+ **推論に独自の基本イメージを使用可能**<br/> もう 1 つ多くいただいたご不満は、実験から推論の依存関係の再共有に移行することが難しいというものでした。 基本イメージの新しい共有機能により、実験の基本イメージ、依存関係などすべてを推論に再利用できるようになりました。 これにより、デプロイが高速化し、内側から外側のループへのギャップが少なくなります。

+ **Swagger スキーマの生成エクスペリエンスの向上**<br/> 以前の Swagger 生成メソッドではエラーが発生しやすく、自動化ができませんでした。 デコレーターを使用してどの Python 関数からでも Swagger スキーマをインラインで生成できるようになりました。 Microsoft はこのコードをオープンソース化しました。このスキーマ生成プロトコルは Azure ML プラットフォームと結合されていません。

+ **Azure ML CLI が一般公開 (GA)**<br/> 1 つの CLI コマンドでモデルをデプロイできるようになりました。 ユーザーから、Jupyter ノートブックから誰も ML モデルをデプロイしていないという共通したフィードバックを受け取っています。 [**CLI リファレンス ドキュメント**](./reference-azure-machine-learning-cli.md)が更新されました。


## <a name="2019-04-22"></a>2019-04-22

Azure Machine Learning SDK for Python v1.0.30 がリリースされました。

同じエンドポイントを維持しながら、新しいバージョンの公開されたパイプラインを追加するための [`PipelineEndpoint`](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint) が導入されました。

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure portal
  + 既存のリモート コンピューティング クラスターで実行されている既存のスクリプトを再送信できるようになりました。
  + [パイプライン] タブで、新しいパラメーターで発行されたパイプラインを実行できるようになりました。
  + 詳細の実行で、新しいスナップショット ファイル ビューアーがサポートされるようになりました。 特定の実行を送信したときのディレクトリのスナップショットを表示することができます。 また、実行を開始するために送信されたノートブックをダウンロードすることもできます。
  + Azure portal からの親の実行をキャンセルできるようになりました。

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK for Python v1.0.23

+ **新機能**
  + Azure Machine Learning SDK で Python 3.7 がサポートされるようになりました。
  + Azure Machine Learning DNN Estimator で、組み込みマルチバージョン サポートが提供されるようになりました。 たとえば、`TensorFlow` Estimator は `framework_version` パラメーターを受け入れるようになり、ユーザーはバージョン '1.10' や '1.12' を指定することができます。 現在の SDK リリースでサポートされているバージョンの一覧については、目的のフレームワーク クラスで `get_supported_versions()` を呼び出します (例: `TensorFlow.get_supported_versions()`)。
  最新の SDK リリースでサポートされているバージョンの一覧については、[DNN Estimator のドキュメント](/python/api/azureml-train-core/azureml.train.dnn)を参照してください。

## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK for Python v1.0.21

+ **新機能**
  + *azureml.core.Run.create_children* メソッドにより、1 回の呼び出しで複数の子の実行を少ない待機時間で作成することができます。

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK for Python v1.0.18

 + **変更点**
   + azureml-contrib-tensorboard が azureml-tensorboard パッケージに置き換えられました。
   + このリリースでは、マネージド コンピューティング クラスター (amlcompute) の作成時にユーザー アカウントを設定できます。 これは、これらのプロパティをプロビジョニング構成に渡すことで実行できます。 詳細については、[SDK リファレンス ドキュメント](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--)をご覧ください。

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning Data Prep SDK v1.0.17

+ **新機能**
  + 式言語を使用して結果列を生成するために、2 つの数値列の追加がサポートされるようになりました。

+ **バグの修正と機能強化**
  + random_split のドキュメントとパラメーター チェックが改善されました。

## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning Data Prep SDK v1.0.16

+ **バグ修正**
  + API の変更が原因で発生していたサービス プリンシパルの認証の問題を修正しました。

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK for Python v1.0.17

+ **新機能**
  + Azure Machine Learning で、一般的な DNN フレームワーク Chainer のファースト クラスのサポートが提供されるようになりました。 [`Chainer`](/python/api/azureml-train-core/azureml.train.dnn.chainer) クラスを使用すると、Chainer モデルを簡単にトレーニングしてデプロイできます。
    + [ChainerMN を使用して分散トレーニングを実行する](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/distributed-chainer/distributed-chainer.ipynb)方法をご覧ください。
    + [HyperDrive を使用して Chainer でハイパーパラメーター調整を実行する](https://github.com/Azure/MachineLearningNotebooks/blob/b881f78e4658b4e102a72b78dbd2129c24506980/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)方法をご覧ください。
  + Azure Machine Learning パイプラインに、データストアの変更に基づいてパイプライン実行をトリガーする機能が追加されました。 この機能を紹介するために、パイプラインの[スケジュール ノートブック](https://aka.ms/pl-schedule)が更新されました。

+ **バグの修正と機能強化**
  + [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep) に提供される [RunConfigurations](/python/api/azureml-core/azureml.core.runconfig.runconfiguration) で、source_directory_data_store プロパティを必要なデータストア (Blob ストレージなど) に設定するために、Azure Machine Learning パイプラインのサポートが追加されました。 既定では、各ステップでバッキング データストアとして Azure File ストアが使用されますが、多数のステップが同時に実行されると、調整の問題が発生する可能性があります。

### <a name="azure-portal"></a>Azure portal

+ **新機能**
  + テーブル エディターでレポートを作成する際の新しいドラッグ アンド ドロップ エクスペリエンス。 ユーザーは、ウェルから、テーブルのプレビューが表示されるテーブル領域に列をドラッグできます。 列を並べ替えることができます。
  + 新しいログ ファイル ビューアー
  + アクティビティ タブから実験の実行、計算、モデル、イメージ、デプロイへのリンク

## <a name="next-steps"></a>次のステップ

[Azure Machine Learning](overview-what-is-azure-ml.md) の概要をご覧ください。
