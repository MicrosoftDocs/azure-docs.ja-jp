---
title: "Azure Machine Learning Workbench で Jupyter Notebook を使用する方法 | Microsoft Docs"
description: "Azure Machine Learning Workbench の Jupyter Notebook 機能を使用するためのガイド"
services: machine-learning
author: jopela
ms.author: jopela
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 07759a590e3a4e061a1c5727428d2c7c2a655f51
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-jupyter-notebook-in-azure-machine-learning-workbench"></a>Azure Machine Learning Workbench で Jupyter Notebook を使用する方法

Azure Machine Learning Workbench は、Jupyter Notebook との統合を通じて対話型のデータ サイエンス実験をサポートしています。 この記事では、この機能を効果的に使用して対話型のデータ サイエンス実験の速度と質を向上させる方法について説明します。

## <a name="prerequisites"></a>前提条件
- [Azure Machine Learning をインストールおよび作成](/machine-learning/preview/quickstart-installation.md)していること。
- [Jupyter Notebook](http://jupyter.org/) に習熟していること (この記事では、Jupyter の使用方法については説明しません)。

## <a name="jupyter-notebook-architecture"></a>Jupyter Notebook のアーキテクチャ
大まかに言えば、Jupyter Notebook のアーキテクチャには次の 3 つのコンポーネントが含まれており、それぞれを異なるコンピューティング環境で実行できます。

- **クライアント**: ユーザー入力を受け取り、レンダリングされた出力を表示します
- **サーバー**: Notebook ファイル (.ipynb ファイル) をホストする Web サーバー
- **カーネル**: Notebook セルが実際に実行されるランタイム環境

詳しくは、公式の [Jupyter ドキュメント](http://jupyter.readthedocs.io/en/latest/architecture/how_jupyter_ipython_work.html)をご覧ください。 次の図は、このクライアント、サーバー、およびカーネルのアーキテクチャが Azure ML のコンポーネントにどのようにマップされるかを示しています。

![Notebook のアーキテクチャ](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-architecture.png)

## <a name="kernels-in-azure-ml-workbench-notebook"></a>Azure ML Workbench Notebook のカーネル
Azure ML Workbench では、プロジェクトの `aml_config` フォルダーに実行構成および計算ターゲットを構成するだけで、さまざまなカーネルにアクセスできます。 `az ml computetarget attach` コマンドを発行して新しい計算ターゲットを追加することは、新しいカーネルを追加することと同じです。

>[!NOTE]
>実行構成および計算ターゲットの詳細については、[実行構成](experiment-execution-configuration.md)に関する記事をご覧ください。

### <a name="kernel-naming-convention"></a>カーネルの名前付け規則
通常、カーネルには "\<プロジェクト名> \<実行構成名>" という形式の名前が付けられます。 たとえば、_myIris_ という名前のプロジェクトに _docker-python_ という名前の実行構成がある場合、Jupyter ノートブックを開くとカーネルの一覧に "myIris docker-python" という名前のカーネルが表示されます。

現在、Workbench では次の種類のカーネルがサポートされています。

### <a name="local-python-kernel"></a>ローカルの Python カーネル
この Python カーネルは、ローカル コンピューターでの実行をサポートします。 これは Azure Machine Learning の実行履歴と統合されています。 通常、このカーネルの名前は "my_project_name local" です。

### <a name="python-kernel-in-docker-local-or-remote"></a>Docker (ローカルまたはリモート) 内の Python カーネル
この Python カーネルは、ローカル コンピューターまたはリモート Linux VM 上の Docker コンテナーで実行されます。 通常、このカーネルの名前は "my_project docker" です。 関連付けられている `docker.runconfig` ファイルの `Framework` フィールドは `Python` に設定されます。

### <a name="pyspark-kernel-in-docker-local-or-remote"></a>Docker (ローカルまたはリモート) 内の PySpark カーネル
この PySpark カーネルは、ローカル コンピューターまたはリモート Linux VM 上の Docker コンテナー内で実行されている Spark コンテキストでスクリプトを実行します。 通常、このカーネルの名前は "my_project docker" です。 関連付けられている `docker.runconfig` ファイルの `Framework` フィールドは `PySpark` に設定されます。

### <a name="pyspark-kernel-on-hdinsight-cluster"></a>HDInsight クラスター上の PySpark カーネル
このカーネルは、プロジェクトの計算ターゲットとしてアタッチした、リモート HDInsight クラスターで実行されます。 通常、このカーネルの名前は "my_project my_hdi" です。 

>[!IMPORTANT]
>このカーネルを使用するには、HDI 計算ターゲットの `.compute` ファイルで `yarnDeployMode` フィールドを `client` (既定値は `cluster`) に変更する必要があります。 

## <a name="start-jupyter-server-from-the-workbench"></a>Workbench からの Jupyter サーバーの起動
Azure Machine Learning Workbench の **[ノートブック]** タブから、ノートブックにアクセスできます。_[Classifying Iris]\(アイリスの分類\)_ サンプル プロジェクトに、`iris.ipynb` サンプルのノートブックが含まれています。

![[ノートブック] タブ](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-01.png)

Azure Machine Learning Workbench でノートブックを開くと、ノートブックはそのドキュメント タブに**プレビュー モード**で表示されます。 これは Jupyter サーバーおよびカーネルを実行する必要がない読み取り専用のビューです。

![ノートブックのプレビュー](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-02.png)

**[Start Notebook Server]\(Notebook サーバーの起動)\** ボタンをクリックすると Jupyter サーバーが起動し、ノートブックが**編集モード**に切り替わります。 見慣れた Jupyter Notebook のユーザー インターフェイスが Workbench に埋め込みで表示されます。 これで、**[カーネル]** メニューからカーネルを設定して対話型のノートブック セッションを開始できます。 

>[!NOTE]
>ローカルでないカーネルの場合、最初に使用するときに起動に 1-2 分かかることがあります。 CLI ウィンドウから `az ml experiment prepare` コマンドを実行すると、計算ターゲットの準備後にカーネルをもっと迅速に起動できるように計算ターゲットを準備できます。

![編集モード](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-04.png)

これは、完全な対話型の Jupyter Notebook エクスペリエンスです。 Workbench の **[ノートブック]** タブおよび **[ファイル]** タブから実行できる一部のファイル操作を除く、通常のすべてのノートブック操作とキーボード ショートカットがこのウィンドウからサポートされます。

## <a name="start-jupyter-server-from-command-line"></a>コマンド ラインから Jupyter サーバーを起動する
コマンド ライン ウィンドウから `az ml notebook start` を発行してノートブック セッションを起動することもできます。
```
$ az ml notebook start
[I 10:14:25.455 NotebookApp] The port 8888 is already in use, trying another port.
[I 10:14:25.464 NotebookApp] Serving notebooks from local directory: /Users/johnpelak/Desktop/IrisDemo
[I 10:14:25.465 NotebookApp] 0 active kernels 
[I 10:14:25.465 NotebookApp] The Jupyter Notebook is running at: http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.465 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 10:14:25.466 NotebookApp] 
    
    Copy/paste this URL into your browser when you connect for the first time,
    to login with a token:
        http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.759 NotebookApp] Accepting one-time-token-authenticated connection from ::1
[I 10:16:52.970 NotebookApp] Kernel started: 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
[I 10:16:53.854 NotebookApp] Adapting to protocol v5.1 for kernel 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
```
既定のブラウザーが、Jupyter サーバーがプロジェクトのホーム ディレクトリをポイントしている状態で自動的に起動されます。 URL と CLI ウィンドウに表示されたトークンを使用して、他のブラウザー ウィンドウをローカルで起動することもできます。 

![プロジェクト ダッシュボード](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-07.png)

これで `.ipynb` ノートブック ファイルをクリックして開き、カーネルを設定して (まだ設定されていない場合)、対話型セッションを開始できます。

![プロジェクト ダッシュボード](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-08.png)

## <a name="next-steps"></a>次のステップ
- Jupyter Notebook の使用方法について学習するには、[Jupyter の公式ドキュメント](http://jupyter-notebook.readthedocs.io/en/latest/)を参照します。    
- Azure ML 実験実行環境をより深く理解するには、[Azure Machine Learning 実験実行サービスの概要](experiment-execution-configuration.md)を参照します。

