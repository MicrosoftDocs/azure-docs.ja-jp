---
title: AutoML と Azure Databricks を使用して開発する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning と Azure Databricks に開発環境をセットアップすることについて説明します。 Databricks 用の Azure ML SDK と、AutoML を含む Databricks を使用します。
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 10/21/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 9bf167f5be9aeb65f0d7c1d69e6687589ebea8a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102498882"
---
# <a name="set-up-a-development-environment-with-azure-databricks-and-automl-in-azure-machine-learning"></a>Azure Machine Learning 内に Azure Databricks と AutoML を含む開発環境をセットアップする 

Azure Databricks と自動 ML を使用する開発環境を Azure Machine Learning 内に構成する方法について説明します。

Azure Databricks は、Azure クラウド内のスケーラブルな Apache Spark プラットフォームで大規模な集中型機械学習ワークフローを実行する場合に最適です。 これは、CPU または GPU ベースのコンピューティング クラスターにより、コラボレーションに適した Notebook ベースの環境を提供するものです。

その他の機械学習開発環境については、[Python 開発環境のセットアップ](how-to-configure-environment.md)に関するページを参照してください。


## <a name="prerequisite"></a>前提条件

Azure Machine Learning ワークスペース。 Azure Machine Learning ワークスペースがない場合は、[Azure portal](how-to-manage-workspace.md)、[Azure CLI](how-to-manage-workspace-cli.md#create-a-workspace)、[Azure Resource Manager テンプレート](how-to-create-workspace-template.md)を使用して作成できます。


## <a name="azure-databricks-with-azure-machine-learning-and-automl"></a>Azure Machine Learning と AutoML を使用する Azure Databricks

Azure Databricks は、Azure Machine Learning と、その AutoML 機能と統合されています。 

Azure Databricks は、以下のように使用できます。

+ Spark MLlib を使用してモデルをトレーニングし、そのモデルを ACI/AKS にデプロイするため。
+ Azure ML SDK を使用して[自動機械学習](concept-automated-ml.md)機能と一緒に。
+ [Azure Machine Learning パイプライン](concept-ml-pipelines.md)からのコンピューティング先として。

## <a name="set-up-a-databricks-cluster"></a>Databricks クラスターをセットアップする

[Databricks クラスター](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)を作成します。 一部の設定は、Databricks に自動化された機械学習用の SDK をインストールする場合にのみ適用されます。

**クラスターの作成には数分かかります。**

次の設定を使用します。

| 設定 |適用対象| 値 |
|----|---|---|
| クラスター名 |常時| yourclustername |
| Databricks Runtime のバージョン |常時| Runtime 7.1 (scala 2.21、spark 3.0.0) - ML 以外|
| Python バージョン |常時| 3 |
| worker の種類 <br>(同時実行反復処理の最大数を決定) |自動化された ML<br>のみ| メモリ最適化 VM 優先 |
| ワーカー |常時| 2 以上 |
| 自動スケールの有効化 |自動化された ML<br>のみ| オフ |

クラスターが実行中になるまで待機してから、次に進んでください。

## <a name="add-the-azure-ml-sdk-to-databricks"></a>Databricks に Azure ML SDK を追加する

クラスターが実行されたら、[ライブラリを作成](https://docs.databricks.com/user-guide/libraries.html#create-a-library)して、適切な Azure Machine Learning SDK パッケージをクラスターにアタッチします。 

自動 ML を使用するには、[AutoML を含む Azure ML SDK の追加](#add-the-azure-ml-sdk-with-automl-to-databricks)に関するセクションに進んでください。


1. ライブラリを保存する現在のワークスペース フォルダーを右クリックします。 **[作成]**  >  **[ライブラリ]** の順に選択します。
    
    > [!TIP]
    > 前のバージョンの SDK がある場合は、クラスターのインストール済みライブラリでその選択を解除し、ごみ箱に移動します。 新しいバージョンの SDK をインストールし、クラスターを再起動します。 再起動後に問題がある場合は、クラスターをデタッチし、再アタッチします。

1. 次のオプションを選択します (他の SDK のインストールはサポートされていません)

   |SDK&nbsp;パッケージ&nbsp;extras|source|PyPi&nbsp;名&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Databricks 用| Python Egg または PyPI のアップロード | azureml-sdk[databricks]|

   > [!WARNING]
   > 他の SDK extras はインストールできません。 [`databricks`] オプションのみを選択します。

   * **[すべてのクラスターに自動的にアタッチする]** は選択しないでください。
   * クラスター名の横にある **[アタッチ]** を選択します。

1. 状態が **[アタッチ済み]** に変わるまで、エラーを監視します。これには数分かかる場合があります。  このステップが失敗した場合は、次の操作を行います。

   次のようにして、クラスターを再起動してみます。
   1. 左側のウィンドウで、 **[クラスター]** を選択します。
   1. テーブルでクラスター名を選択します。
   1. **[ライブラリ]** タブで **[再起動]** を選択します。

   インストールが成功すると次のようになります。 

  ![Databricks 用の Azure Machine Learning SDK](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg) 

## <a name="add-the-azure-ml-sdk-with-automl-to-databricks"></a>AutoML を含む Azure ML SDK を Databricks に追加する
クラスターが Databricks Runtime 7.1 以降 (ML *以外*) で作成された場合は、ノートブックの最初のセルで次のコマンドを実行し、AML SDK をインストールします。

```
%pip install --upgrade --force-reinstall -r https://aka.ms/automl_linux_requirements.txt
```
Databricks Runtime 7.0 以前の場合は、[init スクリプト](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/automl/README.md)を使用して Azure Machine Learning SDK をインストールします。

### <a name="automl-config-settings"></a>AutoML 構成の設定

AutoML 構成では、Azure Databricks を使用するときに次のパラメーターを追加してください。

- ```max_concurrent_iterations``` は、ご利用のクラスター内のワーカー ノードの数に基づきます。
- ```spark_context=sc``` は、既定の Spark コンテキストに基づきます。

## <a name="ml-notebooks-that-work-with-azure-databricks"></a>Azure Databricks と連携動作する ML ノートブック

実際に試してみましょう。
+ サンプル ノートブックがたくさんありますが、**Azure Databricks で動作するのは [これらのサンプル ノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks)のみです。**

+ これらのサンプルは、ワークスペースから直接インポートしてください。 次を参照してください。![インポートの選択](./media/how-to-configure-environment/azure-db-screenshot.png)
![パネルのインポ―ト](./media/how-to-configure-environment/azure-db-import.png)

+ [トレーニング コンピューティングとして Databricks を使用してパイプラインを作成](./how-to-create-machine-learning-pipelines.md)する方法を確認します。

## <a name="troubleshooting"></a>トラブルシューティング

* **Databricks での自動化された機械学習の実行をキャンセルする**:自動化された機械学習機能を Azure Databricks で使用しているときに、実行をキャンセルして新しい実験の実行を開始するには、Azure Databricks クラスターを再起動してください。

* **Databricks での自動化された機械学習の 10 回を超える繰り返し**:自動化された機械学習の設定で、繰り返し回数が 10 回を超えている場合は、実行を送信するときに `show_output` を `False` に設定します。

* **Azure Machine Learning SDK 用の Databricks ウィジェットと自動化された機械学習**:Azure Machine Learning SDK ウィジェットは、Databricks ノートブックではサポートされていません。この理由は、ノートブックが HTML ウィジェットを解析できないからです。 Azure Databricks のノートブック セルで次の Python コードを使用することにより、portal でウィジェットを表示することができます。

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```

* **パッケージをインストールする際の失敗**

    追加のパッケージがインストールされていると、Azure Databricks で Azure Machine Learning SDK のインストールが失敗します。 `psutil` のようなパッケージでは、競合が発生することがあります。 インストール エラーを回避するには、ライブラリ バージョンを止めてパッケージをインストールします。 この問題は Databricks に関連したもので、Azure Machine Learning SDK には関連はありません。 他のライブラリでもこの問題が発生する場合があります。 例:
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    別の方法として、Python ライブラリでインストールの問題が発生し続けている場合は、初期化スクリプトを使用することができます。 この方法は、公式にはサポートされていません。 詳細については、「[Cluster-scoped init scripts](/azure/databricks/clusters/init-scripts#cluster-scoped-init-scripts)」を参照してください。

* **インポート エラー: `pandas._libs.tslibs`** から名前 `Timedelta` をインポートできません:自動機械学習を使用するときにこのエラーが表示される場合は、notebook で次の 2 行を実行します。
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **インポート エラー:'pandas.core.indexes' という名前のモジュールはありません**:自動化された機械学習を使用しているときにこのエラーが表示される場合は、以下を実行します。

    1. 次のコマンドを実行して、Azure Databricks クラスターに 2 つのパッケージをインストールします。
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. クラスターをデタッチし、次にクラスターをノートブックに再アタッチします。
    
    これらの手順で問題が解決しない場合は、クラスターを再起動してみてください。

* **FailToSendFeather**:Azure Databricks クラスター上のデータの読み取り時に `FailToSendFeather` エラーが表示された場合は、次の解決策を参照してください。
    
    * `azureml-sdk[automl]` パッケージを最新バージョンにアップグレードします。
    * `azureml-dataprep` バージョン 1.1.8 以降を追加します。
    * `pyarrow` バージョン 0.11 以降を追加します。
  

## <a name="next-steps"></a>次のステップ

- MNIST データセットを使用して Azure Machine Learning で[モデルをトレーニング](tutorial-train-models-with-aml.md)します。
- [Azure Machine Learning SDK for Python のリファレンス](/python/api/overview/azure/ml/intro)に関するページを参照してください。