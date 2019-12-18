---
title: MLOps:ML モデル管理
titleSuffix: Azure Machine Learning
description: 'Azure Machine Learning (MLOps) を使用したモデル管理について説明します。 モデルをデプロイ、管理、および監視して、継続的に向上させます。 '
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 11/22/2019
ms.custom: seodec18
ms.openlocfilehash: a56096ffa760bcddd6bd423d1262e57dc1db3e3c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973562"
---
# <a name="mlops-model-management-deployment-and-monitoring-with-azure-machine-learning"></a>MLOps:Azure Machine Learning を使用したモデル管理、デプロイおよび監視

この記事では、Azure Machine Learning を使用して、モデルのライフサイクルを管理する方法について説明します。 Azure Machine Learning では、Machine Learning Operations (MLOps) の手法を使用します。 MLOps によって、機械学習ソリューションの品質と一貫性が向上します。 

Azure Machine Learning で提供される MLOps 機能は次のとおりです。

- **再現可能な ML パイプラインを作成する**。 パイプラインを使用すると、データの準備、トレーニング、およびスコア付けプロセスに対して、反復可能かつ再利用可能な手順を定義できます。
- **どこからでもモデルを登録、パッケージ化、およびデプロイ**して、モデルの仕様に必要な関連するメタデータを追跡する。
- **エンドツーエンドの ML ライフサイクルをキャプチャするために必要な管理データを取得する**。モデルを公開しているユーザー、変更が行われている理由、モデルがいつ運用環境でデプロイまたは使用されたかが含まれます。
- **ML ライフサイクルでのイベントに対する通知とアラート**。実験の完了、モデル登録、モデル デプロイ、およびデータ誤差の検出などです。
- **運用および ML 関連の問題について ML アプリケーションを監視する**。 トレーニングと推論の間でのモデル入力の比較、モデル固有のメトリックの調査、および ML インフラストラクチャに関する監視とアラートの提供を行います。
- **Azure Machine Learning および Azure DevOps によって、エンドツーエンドの ML ライフサイクルを自動化する**。これにより、頻繁にモデルを更新し、新しいモデルをテストし、他のアプリケーションおよびサービスとともに新しい ML モデルを継続的にロールアウトします。

## <a name="create-reproducible-ml-pipelines"></a>再現可能な ML パイプラインを作成する

Azure Machine Learning から ML パイプラインを使用して、モデルのトレーニング プロセスに関連するすべての手順をまとめることができます。

ML パイプラインには、データ準備から特徴抽出、さらにはハイパーパラメータ―調整、モデル評価までの手順を含めることができます。 詳細については、「[ML パイプライン](concept-ml-pipelines.md)」をご覧ください。

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

### <a name="package-and-debug-models"></a>モデルをパッケージ化しデバッグする

モデルは、運用環境にデプロイされる前に、Docker イメージにパッケージ化されます。 ほとんどの場合、イメージの作成は、バックグラウンドでデプロイ時に自動的に行われます。 イメージは、手動で指定できます。

デプロイで問題が発生した場合は、トラブルシューティングとデバッグを行うためにローカルの開発環境にデプロイできます。

詳細については、「[モデルをデプロイする](how-to-deploy-and-where.md#registermodel)」と「[デプロイのトラブルシューティング](how-to-troubleshoot-deployment.md)」を参照してください。

### <a name="validate-and-profile-models"></a>モデルを検証しプロファイルする

Azure Machine Learning は、プロファイリングを使用して、モデルのデプロイ時に使用する最適な CPU とメモリの設定を判断できます。 モデルの検証は、プロファイリング プロセスに提供したデータを使用して、このプロセスの一部として行われます。

### <a name="convert-and-optimize-models"></a>モデルを変換して最適化する

モデルを [Open Neural Network Exchange](https://onnx.ai) (ONNX) に変換するとパフォーマンスが向上することがあります。 平均すると、ONNX への変換によりパフォーマンスは 2 倍上昇します。

Azure Machine Learning での ONNX の詳細については、「[ML モデルの作成と能率化](concept-onnx.md)」の記事を参照してください。

### <a name="use-models"></a>モデルを使用する

トレーニング済みの機械学習モデルは、クラウド上またはローカルで、Web サービスとしてデプロイされます。 また、Azure IoT Edge デバイスにモデルをデプロイすることもできます。 デプロイでは、推論に CPU、GPU、またはフィールド プログラマブル ゲート アレイ (FPGA) を使用します。 Power BI からのモデルを使用することもできます。

モデルを Web サービスまたは IoT Edge デバイスとして使用する場合は、次の項目を指定します。

* サービス/デバイスに送信されたデータのスコア付けに使用されるモデル。
* エントリ スクリプト。 このスクリプトは、要求を受け入れ、モデルを使用してデータにスコアを付け、応答を返します。
* モデルとエントリ スクリプトで必要とされる依存関係について記した conda 環境ファイル。
* モデルとエントリ スクリプトで必要とされるテキストやデータなどすべての追加アセット。

また、ターゲット デプロイ プラットフォームの構成も提供します。 たとえば、Azure Kubernetes Service をデプロイするときの VM ファミリの種類、使用可能なメモリ、およびコアの数です。

イメージが作成されると、Azure Machine Learning で必要なコンポーネントも追加されます。 たとえば、Web サービスを実行し、IoT Edge を操作するために必要なアセットです。

#### <a name="batch-scoring"></a>Batch スコアリング
Batch スコアリングは、ML パイプライン経由でサポートされます。 詳細については、[ビッグ データでのバッチ予測](how-to-run-batch-predictions.md)に関するページをご覧ください。

#### <a name="real-time-web-services"></a>リアルタイム Web サービス

次のコンピューティング ターゲットを持つ **Web サービス**でモデルを使用することができます。

* Azure Container Instances
* Azure Kubernetes Service
* ローカル開発環境

モデルを Web サービスとしてデプロイするには、次の項目を指定する必要があります。

* モデル、またはモデルのアンサンブル。
* モデルを使用するために必要な依存関係。 たとえば、要求を受け取って、モデルや Conda 依存関係などを起動するスクリプト。
* モデルのデプロイ方法とデプロイ先を記述するデプロイ構成。

詳細については、「[モデルのデプロイ](how-to-deploy-and-where.md)」を参照してください。

#### <a name="iot-edge-devices"></a>IoT Edge デバイス

**Azure IoT Edge モジュール**を介してモデルを IoT デバイスで使用することができます。 IoT Edge モジュールはハードウェア デバイスに展開されるため、デバイス上で推論、つまりモデルのスコアリングを使用できます。

詳細については、「[モデルのデプロイ](how-to-deploy-and-where.md)」を参照してください。

### <a name="analytics"></a>Analytics

Microsoft Power BI は、データ分析への機械学習モデルの使用をサポートします。 詳細については、[Azure Machine Learning の Power BI への統合 (プレビュー)](https://docs.microsoft.com/power-bi/service-machine-learning-integration)に関する記事をご覧ください。

## <a name="capture-the-governance-data-required-for-capturing-the-end-to-end-ml-lifecycle"></a>エンドツーエンドの ML ライフサイクルをキャプチャするために必要な管理データを取得する

Azure ML を使用すると、すべての ML 資産のエンドツーエンドの監査証跡を追跡できます。 具体的には次の処理が行われます。

- Azure ML が [Git と統合](how-to-set-up-training-targets.md#gitintegration)され、コードが由来しているリポジトリ、ブランチ、コミットについての情報が追跡されます。
- [Azure ML データセット](how-to-create-register-datasets.md)を使用すると、データの追跡、プロファイル、およびバージョン管理が可能です。 
- Azure ML 実行履歴により、モデルをトレーニングするために使用されたコード、データ、およびコンピューティングのスナップショットが保存されます。
- Azure ML モデル レジストリにより、モデルに関連するすべてのメタデータ (それをトレーニングした実験、それがデプロイされている場所、そのデプロイが正常かどうかなど) が取り込まれます。

## <a name="notify-automate-and-alert-on-events-in-the-ml-lifecycle"></a>ML ライフサイクルでのイベントに関する通知、自動化、アラートを行う
Azure ML では、Azure EventGrid に重要なイベントが発行され、ML ライフサイクルでのイベントに関する通知と自動化に利用できます。 詳細については、[こちらのドキュメント](how-to-use-event-grid.md)を参照してください。


## <a name="monitor-for-operational--ml-issues"></a>運用上の問題と ML の問題を監視する

監視により、モデルにどのようなデータが送信されているか、および返される予測を理解することができます。

この情報は、自分のモデルがどのように使用されているかを理解するのに役立ちます。 収集された入力データは、モデルの将来のバージョンのトレーニングにも役立つ可能性があります。

詳細については、[モデル データ収集を有効にする方法](how-to-enable-data-collection.md)に関するページを参照してください。

## <a name="automate-the-ml-lifecycle"></a>ML ライフサイクルを自動化する 

GitHub と Azure Pipelines を使用して、モデルをトレーニングする継続的インテグレーション プロセスを作成できます。 一般的なシナリオでは、データ サイエンティストがプロジェクトの Git リポジトリへの変更をチェックするときに、Azure Pipeline はトレーニングの実行を開始します。 続いて、実行の結果を調べて、トレーニング済みモデルのパフォーマンス特性を確認できます。 Web サービスとしてモデルをデプロイするパイプラインを作成することもできます。

[Azure Machine Learning の拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml)により簡単に Azure Pipelines と連携できるようになります。 次の拡張機能が Azure Pipelines にもたらされます。

* サービス接続を定義するときに、ワークスペースの選択を有効にする。
* トレーニング パイプラインに作成されたトレーニング済みモデルによってトリガーされるリリース パイプラインを有効にする。

Azure Machine Learning との Azure Pipelines の使用に関する詳細については、[Azure Pipelines との ML モデルの継続的インテグレーションとデプロイ](/azure/devops/pipelines/targets/azure-machine-learning)に関する記事と [Azure Machine Learning MLOps](https://aka.ms/mlops) のリポジトリを参照してください。

## <a name="next-steps"></a>次の手順

次のリソースをじっくり読んで理解を深めましょう。

+ Azure Machine Learning を使用した[モデルのデプロイ方法とデプロイ先](how-to-deploy-and-where.md)

+ [チュートリアル:ACI に画像分類モデルをデプロイする](tutorial-deploy-models-with-aml.md)

+ [エンドツーエンドの MLOps サンプル リポジトリ](https://github.com/microsoft/MLOps)

+ [Azure Pipelines を使用した ML モデルの CI/CD](/azure/devops/pipelines/targets/azure-machine-learning)

+ [デプロイされたモデルを利用する](how-to-consume-web-service.md)クライアントを作成する

+ [大規模な機械学習](/azure/architecture/data-guide/big-data/machine-learning-at-scale)

+ [Azure AI リファレンス アーキテクチャとベスト プラクティスのリポジトリ](https://github.com/microsoft/AI)
