---
title: MLOps:ML モデル管理
titleSuffix: Azure Machine Learning
description: 'Azure Machine Learning (MLOps) を使用したモデル管理について説明します。 モデルをデプロイ、管理、および監視して、継続的に向上させます。 '
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: jpe316
ms.author: jordane
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 32777d9f8313457036b103777f251329687b3997
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102508012"
---
# <a name="mlops-model-management-deployment-and-monitoring-with-azure-machine-learning"></a>MLOps:Azure Machine Learning を使用したモデル管理、デプロイ、および監視

この記事では、Azure Machine Learning を使用して、モデルのライフサイクルを管理する方法について説明します。 Azure Machine Learning では、Machine Learning Operations (MLOps) の手法を使用します。 MLOps によって、機械学習ソリューションの品質と一貫性が向上します。 

## <a name="what-is-mlops"></a>MLOps とは

Machine Learning 操作 (MLOps) は、ワークフローの効率を向上させる [DevOps](https://azure.microsoft.com/overview/what-is-devops/) の原則と実践に基づいています。 たとえば、継続的インテグレーション、配信、デプロイです。 MLOps では、次のことを目標に、これらの原則を機械学習プロセスに適用します。

* モデルのより迅速な実験と開発
* 実稼働環境へのモデルのより迅速なデプロイ
* 品質保証

Azure Machine Learning で提供される MLOps 機能は次のとおりです。

- **再現可能な ML パイプラインを作成する**。 Machine Learning パイプラインを使用すると、データの準備、トレーニング、およびスコア付けプロセスに対して、反復可能かつ再利用可能な手順を定義できます。
- モデルのトレーニングとデプロイのために **再利用可能なソフトウェア環境を作成する**。
- **どこからでもモデルの登録、パッケージ化、デプロイを行う**。 モデルを使用するために必要な関連メタデータを追跡することもできます。
- **エンドツーエンドの ML ライフサイクルのために管理データを取得する**。 ログに記録される情報には、モデルを公開しているユーザー、変更が行われた理由、モデルがいつ実稼働環境にデプロイされたか、または使用されたかが含まれます。
- **ML ライフサイクルにおけるイベントについて通知とアラートを行う**。 たとえば、実験の完了、モデルの登録、モデル デプロイ、データ ドリフトの検出などです。
- **運用および ML 関連の問題について ML アプリケーションを監視する**。 トレーニングと推論の間でのモデル入力の比較、モデル固有のメトリックの調査、および ML インフラストラクチャに関する監視とアラートの提供を行います。
- **Azure Machine Learning と Azure Pipelines を使用して、エンド ツー エンドの ML ライフサイクルを自動化する**。 パイプラインの使用により、頻繁にモデルを更新し、新しいモデルをテストし、他のアプリケーションやサービスとともに新しい ML モデルを継続的にロールアウトすることができます。

## <a name="create-reproducible-ml-pipelines"></a>再現可能な ML パイプラインを作成する

Azure Machine Learning から ML パイプラインを使用して、モデルのトレーニング プロセスに関連するすべての手順をまとめることができます。

ML パイプラインには、データ準備から特徴抽出、さらにはハイパーパラメータ―調整、モデル評価までの手順を含めることができます。 詳細については、「[ML パイプライン](concept-ml-pipelines.md)」をご覧ください。

[デザイナー](concept-designer.md)を使用して ML パイプラインを作成する場合、デザイナー ページの右上にある **["..."]** をクリックし、 **[複製]** を選択します。 パイプラインを複製すると、以前のバージョンを失うことなく、パイプラインの設計を反復処理できます。  

## <a name="create-reusable-software-environments"></a>再利用可能なソフトウェア環境を作成する

Azure Machine Learning の環境を使用して、プロジェクトのソフトウェアが進化するのに応じてソフトウェアの依存関係を追跡し、再現することができます。 これらの環境により、手動でのソフトウェア構成なしに、ビルドを再現できることを保証できます。

環境にはプロジェクトの pip や Conda の依存関係が記述されているので、モデルのトレーニングとデプロイの両方に利用できます。 詳細については、「[Azure Machine Learning 環境とは?](concept-environments.md)」を参照してください。

## <a name="register-package-and-deploy-models-from-anywhere"></a>どこからでもモデルを登録、パッケージ化、およびデプロイする

### <a name="register-and-track-ml-models"></a>ML モデルを登録および追跡する

モデルの登録によって、モデルを Azure クラウドに格納し、ワークスペースでバージョンを管理できます。 モデルのレジストリでは、トレーニングしたモデルの整理と追跡が容易に行えます。

> [!TIP]
> 登録済みモデルは、モデルを構成する 1 つまたは複数のファイルの論理コンテナーです。 たとえば、複数のファイルに格納されているモデルがある場合は、Azure Machine Learning ワークスペースに単一モデルとしてそれらを登録することができます。 登録後は、その登録済みモデルをダウンロードするかデプロイし、登録されたすべてのファイルを受信できます。

登録されたモデルは、名前とバージョンによって識別されます。 モデルを登録するたびに、既存のモデルと同じ名前で登録され、レジストリによってバージョンがインクリメントされます。 追加のメタデータ タグは、登録時に指定できます。 これらのタグは、モデルを検索するときに使用されます。 Azure Machine Learning は、Python 3.5.2 以上を使用して読み込むことができる任意のモデルをサポートしています。

> [!TIP]
> Azure Machine Learning の外部でトレーニングされたモデルを登録することもできます。

アクティブなデプロイで使用されている登録済みモデルは削除できません。
詳細については、[モデルのデプロイ](how-to-deploy-and-where.md#registermodel)に関するページの、モデルの登録のセクションを参照してください。

> [!IMPORTANT]
> Azure Machine Learning スタジオの [モデル] ページの [`Tags` でフィルター] オプションを使用するとき、お客様は `TagName : TagValue` を使用するのではなく、`TagName=TagValue` を (スペースなしで) 使用してください。

### <a name="profile-models"></a>モデルのプロファイル

Azure Machine Learning は、モデルのデプロイ時に作成されるサービスの CPU とメモリの要件を把握するのに役立ちます。 モデルを実行して CPU 使用率、メモリ使用率、応答の待機時間などの情報を返す、サービスのプロファイル テストを行います。 また、リソースの使用状況に基づいて CPU とメモリに関する推奨事項が提示されます。
詳細については、[モデルのデプロイ](how-to-deploy-profile-model.md)に関するページの、プロファイルのセクションを参照してください。

### <a name="package-and-debug-models"></a>モデルをパッケージ化しデバッグする

モデルは、運用環境にデプロイされる前に、Docker イメージにパッケージ化されます。 ほとんどの場合、イメージの作成は、バックグラウンドでデプロイ時に自動的に行われます。 イメージは、手動で指定できます。

デプロイで問題が発生した場合は、トラブルシューティングとデバッグを行うためにローカルの開発環境にデプロイできます。

詳細については、「[モデルをデプロイする](how-to-deploy-and-where.md#registermodel)」と「[デプロイのトラブルシューティング](how-to-troubleshoot-deployment.md)」を参照してください。

### <a name="convert-and-optimize-models"></a>モデルを変換して最適化する

モデルを [Open Neural Network Exchange](https://onnx.ai) (ONNX) に変換するとパフォーマンスが向上することがあります。 平均すると、ONNX への変換によりパフォーマンスは 2 倍上昇します。

Azure Machine Learning での ONNX の詳細については、「[ML モデルの作成と能率化](concept-onnx.md)」の記事を参照してください。

### <a name="use-models"></a>モデルを使用する

トレーニング済みの機械学習モデルは、クラウド上またはローカルで、Web サービスとしてデプロイされます。 また、Azure IoT Edge デバイスにモデルをデプロイすることもできます。 デプロイでは、推論に CPU、GPU、またはフィールド プログラマブル ゲート アレイ (FPGA) を使用します。 Power BI からのモデルを使用することもできます。

モデルを Web サービスまたは IoT Edge デバイスとして使用する場合は、次の項目を指定します。

* サービス/デバイスに送信されたデータのスコア付けに使用されるモデル。
* エントリ スクリプト。 このスクリプトは、要求を受け入れ、モデルを使用してデータにスコアを付け、応答を返します。
* モデルやエントリ スクリプトで必要とされる pip および Conda の依存関係について記述している Azure Machine Learning 環境。
* モデルとエントリ スクリプトで必要とされるテキストやデータなどすべての追加アセット。

また、ターゲット デプロイ プラットフォームの構成も提供します。 たとえば、Azure Kubernetes Service をデプロイするときの VM ファミリの種類、使用可能なメモリ、およびコアの数です。

イメージが作成されると、Azure Machine Learning で必要なコンポーネントも追加されます。 たとえば、Web サービスを実行し、IoT Edge を操作するために必要なアセットです。

#### <a name="batch-scoring"></a>バッチ スコアリング
Batch スコアリングは、ML パイプライン経由でサポートされます。 詳細については、[ビッグ データでのバッチ予測](./tutorial-pipeline-batch-scoring-classification.md)に関するページをご覧ください。

#### <a name="real-time-web-services"></a>リアルタイム Web サービス

次のコンピューティング ターゲットを持つ **Web サービス** でモデルを使用することができます。

* Azure Container Instances
* Azure Kubernetes Service
* ローカル開発環境

モデルを Web サービスとしてデプロイするには、次の項目を指定する必要があります。

* モデル、またはモデルのアンサンブル。
* モデルを使用するために必要な依存関係。 たとえば、要求を受け取って、モデルや Conda 依存関係などを起動するスクリプト。
* モデルのデプロイ方法とデプロイ先を記述するデプロイ構成。

詳細については、「[モデルのデプロイ](how-to-deploy-and-where.md)」を参照してください。

#### <a name="controlled-rollout"></a>制御されたロールアウト

Azure Kubernetes Service にデプロイする場合は、制御されたロールアウトを使用して、次のシナリオを有効にすることができます。

* デプロイのために複数のバージョンのエンドポイントを作成します。
* トラフィックを異なるバージョンのエンドポイントにルーティングすることにより A/B テストを実行します。
* エンドポイント構成のトラフィックの割合を更新することによりエンドポイントのバージョンを切り替えます。

詳細については、[ML モデルの制御されたロールアウト](how-to-deploy-azure-kubernetes-service.md#deploy-models-to-aks-using-controlled-rollout-preview)に関するページを参照してください。

#### <a name="iot-edge-devices"></a>IoT Edge デバイス

**Azure IoT Edge モジュール** を介してモデルを IoT デバイスで使用することができます。 IoT Edge モジュールはハードウェア デバイスに展開されるため、デバイス上で推論、つまりモデルのスコアリングを使用できます。

詳細については、「[モデルのデプロイ](how-to-deploy-and-where.md)」を参照してください。

### <a name="analytics"></a>Analytics

Microsoft Power BI は、データ分析への機械学習モデルの使用をサポートします。 詳細については、[Azure Machine Learning の Power BI への統合 (プレビュー)](/power-bi/service-machine-learning-integration)に関する記事をご覧ください。

## <a name="capture-the-governance-data-required-for-capturing-the-end-to-end-ml-lifecycle"></a>エンドツーエンドの ML ライフサイクルをキャプチャするために必要な管理データを取得する

Azure ML では、メタデータを使用して、すべての ML 資産のエンド ツー エンドの監査証跡を追跡できます。

- Azure ML が [Git と統合](how-to-set-up-training-targets.md#gitintegration)され、コードが由来しているリポジトリ、ブランチ、コミットについての情報が追跡されます。
- [Azure ML データセット](how-to-create-register-datasets.md)を使用すると、データの追跡、プロファイル、およびバージョン管理が可能です。
- [解釈可能性](how-to-machine-learning-interpretability.md)を使用すると、モデルを説明したり、規制コンプライアンスを満たしたり、モデルが指定された入力の結果にどのように到達するかを理解したりできます。
- Azure ML 実行履歴には、モデルをトレーニングするのに使用されたコード、データ、およびコンピューティングのスナップショットが保存されます。
- Azure ML モデル レジストリにより、モデルに関連するすべてのメタデータ (それをトレーニングした実験、それがデプロイされている場所、そのデプロイが正常かどうかなど) が取り込まれます。
- [Azure との統合](how-to-use-event-grid.md)を使用すると、ML ライフサイクル内のイベントに対してアクションを実行できます。 たとえば、モデル登録、デプロイ、データ ドリフト、トレーニング (実行) などのイベントです。

> [!TIP]
> モデルやデータセットに関する一部の情報は自動的にキャプチャされますが、__タグ__ を使用して追加情報を付加できます。 ワークスペースで登録済みのモデルやデータセットを検索する場合は、タグをフィルターとして使用できます。
>
> データセットの登録済みのモデルとの関連付けは省略可能な手順です。 モデルを登録するときのデータセットの参照については、[Model](/python/api/azureml-core/azureml.core.model%28class%29) クラスのリファレンスを参照してください。


## <a name="notify-automate-and-alert-on-events-in-the-ml-lifecycle"></a>ML ライフサイクルでのイベントに関する通知、自動化、アラートを行う
Azure ML では、Azure EventGrid に重要なイベントが発行され、ML ライフサイクルでのイベントに関する通知と自動化に利用できます。 詳細については、[こちらのドキュメント](how-to-use-event-grid.md)を参照してください。


## <a name="monitor-for-operational--ml-issues"></a>運用上の問題と ML の問題を監視する

監視により、モデルにどのようなデータが送信されているか、および返される予測を理解することができます。

この情報は、自分のモデルがどのように使用されているかを理解するのに役立ちます。 収集された入力データは、モデルの将来のバージョンのトレーニングにも役立つ可能性があります。

詳細については、[モデル データ収集を有効にする方法](how-to-enable-data-collection.md)に関するページを参照してください。

## <a name="retrain-your-model-on-new-data"></a>新しいデータでモデルを再トレーニングする

多くの場合は、新しい情報を受け取ったとき、モデルを検証または更新したり、場合によっては最初から再トレーニングしたりしたくなります。 新しいデータを受信することが、そのドメインで想定される部分であることもあります。 また、「[データセットでデータ ドリフトを検出する (プレビュー)](how-to-monitor-datasets.md)」で説明されているように、モデルのパフォーマンスは、特定のセンサーの変化、季節的影響などのデータの自然な変化、または他の機能との関係で変化する機能などの要因によって低下する可能性があります。 

再トレーニングの必要性はどう判断するか、常に正しい答えはありません。 しかし、前述した Azure ML イベントや監視ツールは、自動化の出発点として適しています。 再トレーニングすることを決定したら、次のことを行う必要があります。 

- 反復可能な自動化されたプロセスを使用して、データを前処理する
- 新しいモデルをトレーニングする
- 新しいモデルの出力を古いモデルのものと比較する
- 定義済みの条件を使用して、古いモデルを置き換えるかどうかを選択する 

上記の手順のテーマは、再トレーニングはアドホックではなく自動化する必要があるということです。 [Azure Machine Learning パイプライン](concept-ml-pipelines.md)に関する記事では、データの準備、トレーニング、検証、およびデプロイに関連するワークフローを作成するための適切な答えが示されています。 パイプラインと Azure Machine Learning デザイナーが再トレーニング シナリオにどのように適しているかを確認するには、「[Azure Machine Learning デザイナーを使用してモデルを再トレーニングする](how-to-retrain-designer.md)」を参照してください。 

## <a name="automate-the-ml-lifecycle"></a>ML ライフサイクルを自動化する 

GitHub と Azure Pipelines を使用して、モデルをトレーニングする継続的インテグレーション プロセスを作成できます。 一般的なシナリオでは、データ サイエンティストがプロジェクトの Git リポジトリへの変更をチェックするときに、Azure Pipeline はトレーニングの実行を開始します。 続いて、実行の結果を調べて、トレーニング済みモデルのパフォーマンス特性を確認できます。 Web サービスとしてモデルをデプロイするパイプラインを作成することもできます。

[Azure Machine Learning の拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml)により簡単に Azure Pipelines と連携できるようになります。 次の拡張機能が Azure Pipelines にもたらされます。

* サービス接続を定義するときに、ワークスペースの選択を有効にする。
* トレーニング パイプラインに作成されたトレーニング済みモデルによってトリガーされるリリース パイプラインを有効にする。

Azure Machine Learning での Azure Pipelines の使用方法の詳細については、次のリンクを参照してください。

* [Azure Pipelines を使用した ML モデルの継続的インテグレーションとデプロイ](/azure/devops/pipelines/targets/azure-machine-learning) 
* [Azure Machine Learning MLOps](https://aka.ms/mlops) リポジトリ。
* [Azure Machine Learning MLOpsPython](https://github.com/Microsoft/MLOpspython) リポジトリ。

Azure Data Factory を使用して、トレーニングで使用するためのデータを準備するデータ インジェスト パイプラインを作成することもできます。 詳細については、[データ インジェスト パイプライン](how-to-cicd-data-ingestion.md)に関するページを参照してください

## <a name="next-steps"></a>次のステップ

次のリソースをじっくり読んで理解を深めましょう。

+ Azure Machine Learning を使用した[モデルのデプロイ方法とデプロイ先](how-to-deploy-and-where.md)

+ [チュートリアル:ACI に画像分類モデルをデプロイする](tutorial-deploy-models-with-aml.md)

+ [エンドツーエンドの MLOps サンプル リポジトリ](https://github.com/microsoft/MLOps)

+ [Azure Pipelines を使用した ML モデルの CI/CD](/azure/devops/pipelines/targets/azure-machine-learning)

+ [デプロイされたモデルを利用する](how-to-consume-web-service.md)クライアントを作成する

+ [大規模な機械学習](/azure/architecture/data-guide/big-data/machine-learning-at-scale)

+ [Azure AI リファレンス アーキテクチャとベスト プラクティスのリポジトリ](https://github.com/microsoft/AI)