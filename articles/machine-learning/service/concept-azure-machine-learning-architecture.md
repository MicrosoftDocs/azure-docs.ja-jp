---
title: アーキテクチャと主要な概念
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service を構成するアーキテクチャ、用語、概念、およびワークフローについて説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 07/12/2019
ms.custom: seodec18
ms.openlocfilehash: 887c27c765855424dbfb9a77f0b452da0f5de647
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868863"
---
# <a name="how-azure-machine-learning-service-works-architecture-and-concepts"></a>Azure Machine Learning service のしくみ:アーキテクチャと概念

Azure Machine Learning service のアーキテクチャ、概念、ワークフローについて説明します。 サービスの主要なコンポーネントと、サービスを使用する場合の一般的なワークフローを、以下の図に示します。

![Azure Machine Learning service のアーキテクチャとワークフロー](./media/concept-azure-machine-learning-architecture/workflow.png)

## <a name="workflow"></a>ワークフロー

機械学習モデルのワークフローでは、通常、このシーケンスに従います。

1. **トレーニング**
    + **Python** またはビジュアル インターフェイスを使用して、機械学習トレーニング スクリプトを開発します。
    + **コンピューティング ターゲット** を作成して構成します。
    + 構成したコンピューティング ターゲットに **スクリプトを送信** して、その環境で実行します。 トレーニング中、このスクリプトは、**データストア**の読み取りと書き込みを行うことができます。 そして実行の記録は、**ワークスペース**に**実行**として保存され、**実験**の下でグループ化されます。

1. **パッケージ** - 満足できる実行が見つかった場合は、永続化されたモデルを**モデル レジストリ**に登録します。

1. **確認** - 現在と過去の実行からログに記録されたメトリックについて、**実験のクエリ**を行います。 メトリックで目的の結果が示されない場合は、手順 1 に戻ってスクリプトを繰り返します。

1. **デプロイ** - モデルを使用するスコアリング スクリプトを開発し、**Web サービス**として Azure で、または **IoT Edge デバイス**に**モデルをデプロイ**します。

1. **監視** - デプロイ済みモデルのトレーニング データセットと推論データ間の**データの誤差**を監視します。 必要に応じて、手順 1 に戻り、新しいトレーニング データでモデルを再トレーニングします。

## <a name="tools-for-azure-machine-learning"></a>Azure Machine Learning 用のツール 

Azure Machine Learning 用のこれらのツールを使用します。

+  [Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) を使用して、Python 環境でサービスを操作します。
+ [Azure Machine Learning CLI](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli) を使用して、機械学習アクティビティを自動化します。
+ [Azure Machine Learning VS Code 拡張機能](how-to-vscode-tools.md)を使用して、Visual Studio Code でコードを書き込みます 
+ [Azure Machine Learning service 用のビジュアル インターフェイス (プレビュー)](ui-concept-visual-interface.md) を使用して、コードを記述せずにワークフローの手順を行います。

## <a name="glossary-of-concepts"></a>概念の用語集

+ <a href="#workspaces">ワークスペース</a>
+ <a href="#experiments">実験</a>
+ <a href="#models">モデル</a>
+ <a href="#run-configurations">実行構成</a>
+ <a href="#datasets-and-datastores">データセットとデータストア</a>
+ <a href="#compute-targets">コンピューティング先</a>
+ <a href="#training-scripts">トレーニング スクリプト</a>
+ <a href="#runs">Run</a>
+ <a href="#github-tracking-and-integration">Git の追跡</a>
+ <a href="#snapshots">スナップショット</a>
+ <a href="#activities">アクティビティ</a>
+ <a href="#images">Image</a>
+ <a href="#deployment">Deployment</a>
+ <a href="#web-service-deployments">Web サービス</a>
+ <a href="#iot-module-deployments">IoT モジュール</a>
+ <a href="#ml-pipelines">ML パイプライン</a>
+ <a href="#logging">Logging</a>

> [!NOTE]
> この記事では、Azure Machine Learning service で使用される用語と概念を定義しますが、Azure プラットフォームに関する用語と概念は定義しません。 Azure プラットフォームの用語について詳しくは、[Microsoft Azure 用語集](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology)に関するページを参照してください。


### <a name="workspaces"></a>Workspaces

[ワークスペース](concept-workspace.md)は、Azure Machine Learning service の最上位のリソースです。 Azure Machine Learning service を使用するときに作成する、すべての成果物を操作するための一元的な場所が提供されます。

ワークスペースの分類を次の図に示します。

[![ワークスペースの分類](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

ワークスペースの詳細については、「[Azure Machine Learning ワークスペースの管理](concept-workspace.md)」をご覧ください。

### <a name="experiments"></a>実験

実験は、指定されたスクリプトからの多くの実行のグループです。 実験は、常に 1 つのワークスペースに属します。 実行を送信するときは、実験名を指定します。 実行に関する情報は、その実験に格納されます。 実行を送信するときに、存在しない実験名を指定すると、新たに指定された名前を持つ新しい実験が自動的に作成されます。

実験の使用例については、[クイック スタート:Azure Machine Learning service の利用の開始](quickstart-run-cloud-notebook.md)に関するページを参照してください。

### <a name="models"></a>モデル

簡単に言うと、モデルとは入力を受け取って出力を生成するひとまとまりのコードです。 機械学習モデルの作成には、アルゴリズムの選択、アルゴリズムへのデータの提供、およびハイパーパラメーターのチューニングが含まれます。 トレーニングはトレーニング済みモデルを生成する反復的なプロセスであり、トレーニング プロセス中にモデルが学習した内容がカプセル化されています。

モデルは、Azure Machine Learning での実行によって生成されます。 Azure Machine Learning の外部でトレーニングされるモデルを使用することもできます。 Azure Machine Learning service ワークスペースにモデルを登録することができます。

Azure Machine Learning service はフレームワークに依存しません。 モデルを作成するときは、Scikit-learn、XGBoost、PyTorch、TensorFlow、Chainer などの任意の人気のある機械学習フレームワークを使用できます。

モデルのトレーニング例については、[チュートリアル: Azure Machine Learning service で画像分類モデルをトレーニングする](tutorial-train-models-with-aml.md)。

**モデル レジストリ**により、Azure Machine Learning service ワークスペース内のすべてのモデルが追跡されます。

モデルは、名前とバージョンによって識別されます。 既存のモデルと同じ名前でモデルを登録するたびに、レジストリではそれが新しいバージョンと見なされます。 バージョンはインクリメントされ、新しいモデルは同じ名前で登録されます。

モデルを登録するときに、追加のメタデータ タグを指定し、モデルを検索するときにそのタグを使用することができます。

> [!TIP]
> 登録済みモデルは、モデルを構成する 1 つまたは複数のファイルの論理コンテナーです。 たとえば、複数のファイルに格納されているモデルがある場合は、Azure Machine Learning ワークスペースに単一モデルとしてそれらを登録することができます。 登録後は、その登録済みモデルをダウンロードするかデプロイし、登録されたすべてのファイルを受信することができます。

アクティブなデプロイで使用されている登録済みモデルを削除することはできません。

モデルの登録例については、[Azure Machine Learning での画像分類モデルのトレーニング](tutorial-train-models-with-aml.md)に関するページを参照してください。

### <a name="run-configurations"></a>実行構成

実行構成は、指定されたコンピューティング先でのスクリプトの実行方法を定義する一連の命令です。 構成には、既存の Python 環境を使用するかどうかや、仕様から構築される Conda 環境を使用するかどうかなど、広範な動作の定義セットが含まれます。

実行構成は、トレーニング スクリプトが含まれるディレクトリ内のファイルに保持することも、メモリ内オブジェクトとして構築して実行の送信に使用することもできます。

実行構成の例については、[モデルをトレーニングするためのコンピューティング先の選択と使用](how-to-set-up-training-targets.md)に関するページを参照してください。

### <a name="datasets-and-datastores"></a>データセットとデータストア

**Azure Machine Learning Datasets (プレビュー)** によって、データへのアクセスと操作がより容易になります。 データセットは、モデルのトレーニングやパイプラインの作成など、さまざまなシナリオでデータを管理します。 Azure Machine Learning SDK を使用すると、基礎となるストレージへのアクセス、データの探索と準備、異なるデータセット定義のライフ サイクルの管理、トレーニングと運用環境で使用されるデータセット間の比較が可能になります。

Datasets には、`from_delimited_files()` や `to_pandas_dataframe()` を使用するなど、一般的なフォーマットでデータを操作する方法が用意されています。

詳細については、[Azure Machine Learning Datasets の作成と登録](how-to-create-register-datasets.md)に関するページを参照してください。  データセットのその他の使用例については、[サンプル ノートブック](https://aka.ms/dataset-tutorial)を参照してください。

**データストア**は、Azure ストレージ アカウントに対するストレージの抽象化です。 データストアでは、バックエンド ストレージとして Azure BLOB コンテナーまたは Azure ファイル共有を使用できます。 各ワークスペースには既定のデータストアがあり、ユーザーは追加のデータストアを登録できます。 データストアのファイルを格納および取得するには、Python SDK API または Azure Machine Learning CLI を使用します。

### <a name="compute-targets"></a>コンピューティング ターゲット

[コンピューティング ターゲット](concept-compute-target.md)では、トレーニング スクリプトを実行したり、サービスのデプロイをホストしたりする場所であるコンピューティング リソースを指定します。 この場所は、ローカル コンピューターでも、クラウドベースのコンピューティング リソースでもかまいません。 コンピューティング ターゲットを使用すれば、コードを変更しなくてもコンピューティング環境を簡単に変更できます。 

詳細については、[トレーニングおよびデプロイ用の利用可能なコンピューティング先](concept-compute-target.md)に関するページを参照してください。 

### <a name="training-scripts"></a>トレーニング スクリプト

モデルをトレーニングするには、トレーニング スクリプトおよび関連ファイルが格納されているディレクトリを指定します。 実験名も指定します。これは、トレーニング中に収集される情報を格納するために使用されます。 トレーニング中に、ディレクトリ全体がトレーニング環境 (コンピューティング先) にコピーされて、実行構成で指定されるスクリプトが開始されます。 ディレクトリのスナップショットも、ワークスペース内の実験の下に格納されます。

例については、「[チュートリアル:Azure Machine Learning service で画像分類モデルをトレーニングする](tutorial-train-models-with-aml.md)。

### <a name="runs"></a>実行

実行は、次の情報を含むレコードです。

* 実行に関するメタデータ (タイムスタンプ、期間など)
* スクリプトによって記録されるメトリック
* 実験によって自動収集される、またはユーザーによって明示的にアップロードされる、出力ファイル
* 実行前の、スクリプトを含むディレクトリのスナップショット

モデルをトレーニングするためにスクリプトを送信するときに実行を生成します。 実行は、0 個以上の子実行を持つことができます。 たとえば、最上位レベルの実行は 2 つの子実行を持つ可能性があり、それぞれが独自の子実行を持つ場合があります。

モデルのトレーニングによって生成される実行を表示する例については、[クイック スタート: Azure Machine Learning service の利用の開始](quickstart-run-cloud-notebook.md)に関するページを参照してください。

### <a name="github-tracking-and-integration"></a>GitHub の追跡と統合

ソース ディレクトリがローカル Git リポジトリであるトレーニング実行を開始すると、リポジトリに関する情報が実行履歴に格納されます。 たとえば、リポジトリの現在のコミット ID が履歴の一部としてログに記録されます。 これは、推定器、ML パイプライン、またはスクリプトの実行を使用して送信した実行で機能します。 SDK または Machine Learning CLI から送信された実行でも機能します。

### <a name="snapshots"></a>スナップショット

実行を送信するときに、Azure Machine Learning によって、スクリプトが含まれているディレクトリが zip ファイルとして圧縮され、コンピューティング先に送られます。 その後、zip ファイルが抽出され、そこでスクリプトが実行されます。 Azure Machine Learning では、zip ファイルもスナップショットとして実行レコード内に格納されます。 ワークスペースにアクセスできるすべてのユーザーは、実行レコードを参照し、スナップショットをダウンできます。

> [!NOTE]
> 不要なファイルがスナップショットに含まれないようにするため、無視ファイル (.gitignore または .amlignore) を作成します。 このファイルをスナップショット ディレクトリに配置し、無視するファイルの名前をその中に追加します。 .amlignore ファイルには、[.gitignore ファイルと同じ構文とパターン](https://git-scm.com/docs/gitignore)が使用されます。 両方のファイルが存在する場合、.amlignore ファイルが優先されます。

### <a name="activities"></a>Activities

アクティビティは、実行時間の長い操作を表します。 次の操作はアクティビティの例です。

* コンピューティング ターゲットの作成または削除
* コンピューティング ターゲット上でのスクリプトの実行

アクティビティでは、ユーザーがこれらの操作の進行状況を簡単に監視できるように、SDK または Web UI 経由で通知を提供できます。

### <a name="images"></a>イメージ

イメージによって、モデルを使用するために必要なすべてのコンポーネントと共に、モデルを確実にデプロイする方法が提供されます。 イメージには次の項目が含まれます。

* モデル。
* スコアリング スクリプトまたはアプリケーション。 スクリプトを使用して、モデルに入力を渡し、モデルから出力を戻します。
* モデルまたはスコアリング スクリプトあるいはアプリケーションで必要な依存関係。 たとえば、Python パッケージの依存関係が列記されている Conda 環境ファイルを含めることがあります。

Azure Machine Learning では、次の 2 種類のイメージを作成できます。

* **FPGA イメージ**:Azure のフィールド プログラマブル ゲート アレイにデプロイするときに使用されます。
* **Docker イメージ**:FPGA 以外のコンピューティング先にデプロイするときに使用されます。 例として、Azure Container Instances や Azure Kubernetes Service があります。

Azure Machine Learning service は基本イメージを提供し、それが既定で使用されます。 独自のカスタム イメージを指定することもできます。

### <a name="image-registry"></a>イメージ レジストリ

イメージは、ワークスペースの**イメージ レジストリ**に分類されます。 後でクエリを実行してイメージを見つけられるように、イメージの作成時に追加のメタデータ タグを指定することができます。

イメージの作成例については、「[Azure Container Instances に画像分類モデルをデプロイする](tutorial-deploy-models-with-aml.md)」を参照してください。

カスタム イメージを使用するモデルのデプロイ例については、[カスタム Docker イメージを使用するモデルのデプロイ方法](how-to-deploy-custom-docker-image.md)に関するページを参照してください。

### <a name="deployment"></a>Deployment

デプロイは、クラウドでホストできる Web サービスまたは統合デバイスのデプロイ用 IoT モジュールへのモデルのインスタンス化です。

#### <a name="web-service-deployments"></a>Web サービスのデプロイ

デプロイされた Web サービスでは、Azure Container Instances、Azure Kubernetes Service、または FPGA を使用できます。 モデル、スクリプト、および関連ファイルからサービスを作成します。 これらはイメージにカプセル化されており、Web サービスにランタイム環境を提供します。 イメージには、Web サービスに送信されるスコアリング要求を受け取る、負荷分散された HTTP エンドポイントがあります。

Azure には Application Insights のテレメトリやモデルのテレメトリを収集する機能があり、それを有効にすると、Web サービスのデプロイを監視するのに役立ちます。 利用統計情報にアクセスできるのは機能を有効にしたユーザーだけであり、情報はそのユーザーの Application Insights インスタンスとストレージ アカウント インスタンスに格納されます。

自動スケールを有効にしてある場合は、Azure でデプロイが自動的にスケーリングされます。

Web サービスとしてのモデルのデプロイ例については、[Azure Container Instances での画像分類モデルのデプロイ](tutorial-deploy-models-with-aml.md)に関するページを参照してください。

#### <a name="iot-module-deployments"></a>IoT モジュールのデプロイ

デプロイされる IoT モジュールは Docker コンテナーであり、モデルとそれに関連付けられているスクリプトまたはアプリケーション、および追加の依存関係が含まれます。 エッジ デバイス上の Azure IoT Edge を使用して、これらのモジュールをデプロイします。

監視を有効にしてある場合、Azure では Azure IoT Edge モジュール内のモデルから利用統計情報を収集します。 利用統計情報にアクセスできるのは機能を有効にしたユーザーだけであり、情報はそのユーザーのストレージ アカウント インスタンスに格納されます。

Azure IoT Edge ではモジュールが実行されるのを保証し、モジュールをホストしているデバイスを監視します。

### <a name="ml-pipelines"></a>ML パイプライン

機械学習パイプラインを使用し、機械学習フェーズをつなげるワークフローを作成して管理します。 たとえば、パイプラインには、データ準備、モデル トレーニング、モデル デプロイ、推論/スコアリングの各フェーズが含まれることが考えられます。 それぞれのフェーズには、複数のステップを含めることができ、各ステップは、さまざまなコンピューティング先において無人実行することができます。

このサービスを使用した機械学習パイプラインの詳細については、[パイプラインと Azure Machine Learning](concept-ml-pipelines.md) に関するページを参照してください。

### <a name="logging"></a>ログの記録

ソリューションを開発するときは、Python スクリプトで Azure Machine Learning Python SDK を使用して、任意のメトリックを記録します。 実行後にメトリックのクエリを行って、デプロイするモデルが実行によって生成されたかどうかを判断します。

### <a name="next-steps"></a>次の手順

Azure Machine Learning service の利用を開始する場合は、以下を参照してください。

* [Azure Machine Learning service とは](overview-what-is-azure-ml.md)
* [Azure Machine Learning service のワークスペースを作成する](setup-create-workspace.md)
* [チュートリアル (パート 1): モデルをトレーニングする](tutorial-train-models-with-aml.md)
