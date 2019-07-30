---
title: MLOps:ML モデルを管理、デプロイ、および監視する
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service をモデルの MLOps (デプロイ、管理、および監視) に使用して、継続的にモデルを向上させる方法について説明します。 トレーニングしたモデルを、ローカル コンピューターまたは他のソースから Azure Machine Learning service を使用してデプロイできます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 11a4a17d7816d2302b6549cffb9517e10ad1258d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442347"
---
# <a name="mlops-manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>MLOps:Azure Machine Learning service でモデルを管理、デプロイ、および監視する

この記事では、Azure Machine Learning service を使用して、モデルのライフサイクルを管理する方法について説明します。 Azure Machine Learning では Machine Learning Operations (MLOps) のアプローチが使用され、機械学習ソリューションの品質と一貫性を高めています。 

Azure Machine Learning で提供される MLOps 機能は次のとおりです。

- **任意の場所から ML プロジェクトをデプロイする**
- **運用および ML 関連の問題について ML アプリケーションを監視する** - トレーニングと推論の間でのモデル入力の比較、モデル固有のメトリックの調査、および ML インフラストラクチャに関する監視とアラートの提供を行います。
- **ML ライフサイクルのエンド ツー エンドの監査証跡を確立するために必要なデータを取り込む** - これには、モデルを公開しているユーザー、変更が行われている理由、モデルがいつ運用環境でデプロイまたは使用されたのかが含まれます。
- **Azure Machine Learning および Azure DevOps でエンド ツー エンドの ML ライフサイクルを自動化する** - これにより、頻繁にモデルを更新し、新しいモデルをテストし、他のアプリケーションおよびサービスとともに新しい ML モデルを継続的にロールアウトします。

MLOps の基本的な概念とそれらを Azure Machine Learning service に適用する方法については、次のビデオをご覧ください。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GX]

## <a name="deploy-ml-projects-from-anywhere"></a>任意の場所から ML プロジェクトをデプロイする

### <a name="turn-your-training-process-into-a-reproducible-pipeline"></a>トレーニング プロセスを、再現可能なパイプラインに変換する
Azure Machine Learning の ML パイプラインを使用して、データの準備から、特徴抽出、ハイパーパラメーター調整、モデルの評価まで、モデル トレーニング プロセスに含まれるすべてのステップを結合できます。

詳細については、「[ML パイプライン](concept-ml-pipelines.md)」をご覧ください。

### <a name="register-and-track-ml-models"></a>ML モデルを登録および追跡する

モデルの登録によって、モデルを Azure クラウドに格納し、ワークスペースでバージョンを管理できます。 モデルのレジストリでは、トレーニングしたモデルの整理と追跡が容易に行えます。

> [!TIP]
> 登録済みモデルは、モデルを構成する 1 つまたは複数のファイルの論理コンテナーです。 たとえば、複数のファイルに格納されているモデルがある場合は、Microsoft Azure Machine Learning ワークスペースに単一モデルとしてそれらを登録できます。 登録後は、その登録済みモデルをダウンロードするかデプロイし、登録されたすべてのファイルを受信できます。
 
登録されたモデルは、名前とバージョンによって識別されます。 モデルを登録するたびに、既存のモデルと同じ名前で登録され、レジストリによってバージョンがインクリメントされます。 モデルの検索時に使用できる追加のメタデータ タグを、登録中に指定することもできます。 Azure Machine Learning service は、Python 3.5.2 以上を使用して読み込むことができる任意のモデルをサポートしています。

> [!TIP]
> Azure Machine Learning service の外部でトレーニングされたモデルを登録することもできます。

アクティブなデプロイで使用されている登録済みモデルは削除できません。
詳細については、[モデルのデプロイ](how-to-deploy-and-where.md#registermodel)に関するページの、モデルの登録のセクションを参照してください。

### <a name="package-and-debug-models"></a>モデルをパッケージ化しデバッグする

モデルは、運用環境にデプロイされる前に、Docker イメージにパッケージ化されます。 ほとんどの場合、イメージの作成は、バックグラウンドでデプロイ時に自動的に行われます。 高度なシナリオでは、イメージを手動で指定できます。

デプロイで問題が発生した場合は、トラブルシューティングとデバッグを行うためにローカルの開発環境にデプロイできます。

詳細については、「[モデルをデプロイする](how-to-deploy-and-where.md#registermodel)」と「[デプロイのトラブルシューティング](how-to-troubleshoot-deployment.md)」を参照してください。

### <a name="validate-and-profile-models"></a>モデルを検証しプロファイルする

Azure Machine Learning service は、プロファイリングを使用して、モデルのデプロイ時に使用する最適な CPU およびメモリの設定を判断できます。 モデルの検証は、プロファイリング プロセスに提供したデータを使用して、このプロセスの一部として行われます。

### <a name="convert-and-optimize-models"></a>モデルを変換して最適化する

モデルを [Open Neural Network Exchange](https://onnx.ai) (ONNX) に変換するとパフォーマンスが向上することがあります。 平均すると、ONNX への変換によりパフォーマンスは 2 倍上昇します。

Azure Machine Learning service での ONNX の詳細については、「[ML モデルの作成と能率化](concept-onnx.md)」の記事を参照してください。

### <a name="use-models"></a>モデルを使用する

トレーニング済みの機械学習モデルは、クラウド上で、またはローカルの開発環境で、Web サービスとしてデプロイできます。 また、Azure IoT Edge デバイスにモデルをデプロイすることもできます。 デプロイでは、推論に CPU、GPU、またはフィールド プログラマブル ゲート アレイ (FPGA) を使用できます。 Power BI からのモデルを使用することもできます。

モデルを Web サービスまたは IoT Edge デバイスとして使用する場合は、次の項目を指定します。

* サービス/デバイスに送信されたデータのスコア付けに使用されるモデル。
* エントリ スクリプト。 このスクリプトは、要求を受け入れ、モデルを使用してデータにスコアを付け、応答を返します。
* モデルとエントリ スクリプトで必要とされる依存関係について記した conda 環境ファイル。
* モデルとエントリ スクリプトで必要とされるテキストやデータなどすべての追加アセット。

これらのアセットは、Docker イメージにパッケージ化され、Web サービスまたは IoT Edge モジュールとしてデプロイされます。

必要に応じて、デプロイをさらに調整するために次のパラメーターを使用できます。

* GPU の有効化:Docker イメージで GPU サポートを有効にするために使用されます。 イメージは、Azure Container Instances、Azure Kubernetes Service、Azure Machine Learning コンピューティング、Azure Virtual Machines などの Microsoft Azure サービスで使用する必要があります。
* 追加の docker ファイルの作成手順:Docker イメージを作成するときに実行する追加の Docker 手順を含むファイル。
* 基本イメージ:基本イメージとして使用するカスタム イメージ。 カスタム イメージを使用しない場合、基本イメージは、Azure Machine Learning service から提供されます。

また、ターゲット デプロイ プラットフォームの構成も提供します。 たとえば、Azure Kubernetes Service をデプロイするときの VM ファミリの種類、使用可能なメモリ、およびコアの数です。

イメージが作成されると、Azure Machine Learning service で必要なコンポーネントも追加されます。 たとえば、Web サービスを実行し、IoT Edge を操作するために必要なアセットです。

> [!NOTE]
> Docker イメージで使用される Web サーバーまたは IoT Edge のコンポーネントは変更できません。 Azure Machine Learning service では、Microsoft によってテストされサポートされている Web サーバー構成と IoT Edge コンポーネントを使用します。

#### <a name="web-service"></a>Web サービス

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

Microsoft Power BI は、データ分析への機械学習モデルの使用をサポートします。 詳しくは、「[Azure Machine Learning の Power BI への統合 (プレビュー)](https://docs.microsoft.com/power-bi/service-machine-learning-integration)」をご覧ください。


## <a name="monitor-ml-applications-for-operational-and-ml-related-issues"></a>運用および ML 関連の問題について ML アプリケーションを監視する

監視により、モデルにどのようなデータが送信されているか、および返される予測を理解することができます。

この情報は、自分のモデルがどのように使用されているかを理解するのに役立ちます。 収集された入力データは、モデルの将来のバージョンのトレーニングにも役立つ可能性があります。

詳細については、[モデル データ収集を有効にする方法](how-to-enable-data-collection.md)に関するページを参照してください。


## <a name="capture-an-end-to-end-audit-trail-of-the-ml-lifecycle"></a>ML ライフサイクルのエンド ツー エンドの監査証跡を取り込む

Azure ML では、すべての ML 資産のエンド ツー エンドの監査証跡を追跡できます。 具体的には次の処理が行われます。

- Azure ML が Git と統合し、コードがどのリポジトリ/ブランチ/コミットから発生しているのかについての情報が追跡されます。
- Azure ML データセットにより、データの追跡とバージョン管理が容易になります。
- Azure ML 実行履歴により、モデルをトレーニングするために使用されるコード、データ、コンピューティングが管理されます。
- Azure ML モデル レジストリにより、モデルに関連するすべてのメタデータ (それをトレーニングした実験、それがデプロイされている場所、そのデプロイが正常かどうかなど) が取り込まれます。

## <a name="automate-the-end-to-end-ml-lifecycle"></a>エンド ツー エンドの ML ライフサイクルを自動化する 

GitHub と Azure Pipelines を使用して、モデルをトレーニングする継続的インテグレーション プロセスを作成できます。 一般的なシナリオでは、データ サイエンティストがプロジェクトの Git リポジトリへの変更をチェックするときに、Azure Pipeline はトレーニングの実行を開始します。 続いて、実行の結果を調べて、トレーニング済みモデルのパフォーマンス特性を確認できます。 Web サービスとしてモデルをデプロイするパイプラインを作成することもできます。

[Azure Machine Learning の拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml)により簡単に Azure Pipelines と連携できるようになります。 次の拡張機能が Azure Pipelines にもたらされます。

* サービス接続を定義するときに、ワークスペースの選択を有効にする。
* トレーニング パイプラインに作成されたトレーニング済みモデルによってトリガーされるリリース パイプラインを有効にする。

Azure Machine Learning との Azure Pipelines の使用に関する詳細については、[Azure Pipelines との ML モデルの継続的インテグレーションとデプロイ](/azure/devops/pipelines/targets/azure-machine-learning)に関する記事と [Azure Machine Learning サービス MLOps](https://aka.ms/mlops) のリポジトリを参照してください。

## <a name="next-steps"></a>次の手順

Azure Machine Learning サービスを使用して[モデルをデプロイする方法と場所](how-to-deploy-and-where.md)を確認します。 デプロイの例については、「[チュートリアル: Azure Container Instances に画像分類モデルをデプロイする](tutorial-deploy-models-with-aml.md)」を参照してください。

[Azure Pipelines を使用して ML モデルの継続的な統合とデプロイ](/azure/devops/pipelines/targets/azure-machine-learning)を作成する方法について説明します。 

[デプロイされたモデルを Web サービスとして使用する](how-to-consume-web-service.md)クライアント アプリケーションとサービスの作成方法を確認します。
