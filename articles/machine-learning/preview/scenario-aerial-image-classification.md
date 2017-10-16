---
title: "航空画像の分類 | Microsoft Docs"
description: "航空画像の分類に関する実際のシナリオの手順について説明します。"
author: mawah
ms.author: mawah
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.topic: article
ms.service: machine-learning
services: machine-learning
ms.date: 09/15/2017
ms.openlocfilehash: 43b124fc3eb72adc5d299b218c9e16ec83d1a240
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="aerial-image-classification"></a>航空画像の分類

この例は、Azure Machine Learning Workbench を使用して、画像分類モデルの分散トレーニングと運用化を調整する方法を示しています。 ここでは [Microsoft Machine Learning for Apache Spark (MMLSpark)](https://github.com/Azure/mmlspark) パッケージを使用し、トレーニング済みの CNTK モデルを使用して画像を特徴付けし、生成された特徴を使用して分類をトレーニングします。 次に、トレーニングしたモデルを並行してクラウド内の大規模な画像セットに適用します。 これらの手順は [Azure HDInsight Spark](https://azure.microsoft.com/en-us/services/hdinsight/apache-spark/) クラスターに対して実行されるので、worker ノードを増減することで、トレーニングと運用化の速度を上下できます。

ここで説明する転移学習の形式は、深層ニューラル ネットワークの再トレーニングや微調整よりも大きな利点があります。具体的には、GPU コンピューティングが不要、本質的に高速で任意の拡大縮小が可能、少ないパラメーターに適合するという利点があります。 そのため、この方法は、少ないトレーニング サンプルを使用可能な場合 (カスタム ユース ケースでよく見られる例) に最適です。 多くのユーザーは、転移学習によって高パフォーマンスのモデルが生成されるので、はるかに高いコストでゼロから深層ニューラル ネットワークをトレーニングしなくても済むと報告しています。

この例で使用される Spark クラスターには 40 worker ノードがあり、運用コストは 1 時間あたり 40 ドル以下です。 このチュートリアルの完了には約 2 時間かかります。 チュートリアルを完了したら、クリーンアップ手順に従って、作成したリソースを削除し、課金を停止してください。

## <a name="link-to-the-gallery-github-repository"></a>ギャラリーの GitHub リポジトリへのリンク

この実際のシナリオに対応するパブリック GitHub リポジトリには、コード サンプルなど、この例に必要なすべての素材が含まれています。

[https://github.com/Azure/MachineLearningSamples-AerialImageClassification](https://github.com/Azure/MachineLearningSamples-AerialImageClassification)

## <a name="use-case-description"></a>ユース ケースの説明

このシナリオでは、機械学習モデルをトレーニングして、224 m × 224 m プロットの航空画像に表示される土地の種類を分類します。 土地利用の分類モデルでは、定期的に収集した航空画像を使用して、都会化、森林伐採、湿地帯の消失などの主要な環境傾向を追跡することができます。 ここでは、米国National Agriculture Imagery Program の画像と、米国National Land Cover Database が公開している土地利用ラベルに基づいて設定したトレーニングと検証画像を用意しました。 各土地利用クラスの画像例を次に示します。

![各土地利用ラベルの地域例](media/scenario-aerial-image-classification/example-labels.PNG)

分類モデルのトレーニングと検証の後に、マサチューセッツ州ミドルセックス郡 (Microsoft の New England Research & Development (NERD) Center の拠点) 全体を撮影した航空画像に適用し、これらのモデルを使用して都市開発の傾向を研究することができます。

多くの場合、データ サイエンティストは、転移学習を使用して画像分類を生成するために、方法の範囲を指定して複数のモデルを構築し、最も効率的なモデルを選択します。 データ サイエンティストは Azure Machine Learning Workbench を使用して、さまざまなコンピューティング環境のトレーニングを調整し、複数のモデルのパフォーマンスを追跡して比較し、選択したモデルをクラウドの大規模なデータセットに適用することができます。

## <a name="scenario-structure"></a>シナリオの構造

この例では、画像データとトレーニング済みのモデルは Azure ストレージ アカウントに保存されます。 Azure HDInsight Spark クラスターはこれらのファイルを読み取り、MMLSpark を使用して画像分類モデルを構築します。 トレーニング済みモデルとその予測はストレージ アカウントに書き込まれます。書き込まれたデータは、ローカルで実行されている Jupyter ノートブックで分析し、視覚化することができます。 Azure Machine Learning Workbench は、Spark クラスター上のスクリプトのリモート実行を調整します。 また、異なる方法を使用してトレーニングされた複数のモデルについて精度メトリックも追跡し、最も効率的なモデルをユーザーが選択できるようにします。

![航空画像分類の実際のシナリオに関する概略図](media/scenario-aerial-image-classification/scenario-schematic.PNG)

この[手順](https://github.com/MicrosoftDocs/azure-docs-pr/tree/release-ignite-aml-v2/articles/machine-learning/)は、データ転送と依存関係のインストールなど、Azure ストレージ アカウントと Spark クラスターの作成と準備の説明から始まります。 次に、トレーニング ジョブの開始方法と、結果のモデルのパフォーマンスを比較する方法について説明します。 最後に、Spark クラスターに設定されている大規模な画像に選択したモデルを適用し、ローカルで予測結果を分析する方法について説明します。


## <a name="set-up-the-execution-environment"></a>実行環境を設定する

次の手順では、この例の実行環境を設定するプロセスを説明します。

### <a name="prerequisites"></a>前提条件
- [Azure アカウント](https://azure.microsoft.com/en-us/free/) (無料試用版もご利用いただけます)
    - このサンプルでは、worker ノード数が 40 個の HDInsight Spark クラスターを作成します (合計 168 コア)。 Azure Portal のサブスクリプションの [使用量 + クォータ] タブで、アカウントで使用できるコアが十分にあることを確認します。
    - 使用可能なコアがあまりない場合は、HDInsight クラスター テンプレートを変更して、プロビジョニングする worker 数を減らすことができます。 この手順については、「HDInsight Spark クラスターの作成」セクションを参照してください。
- [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md)
    - [インストールと作成のクイックスタート](quickstart-installation.md)に関するページを参照して Azure Machine Learning Workbench をインストールし、実験用アカウントとモデル管理アカウントを作成します。
- [AzCopy](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy)。Azure ストレージ アカウント間のファイル転送を調整する無料のユーティリティです。
    - AzCopy の実行可能ファイルを含むフォルダーが、システム PATH 環境変数にあることを確認します (環境変数の変更手順については、[こちら](https://support.microsoft.com/en-us/help/310519/how-to-manage-environment-variables-in-windows-xp)を参照してください)。この例は、Windows 10 PC でテストされましたが、Azure Data Science Virtual Machines など、任意の Windows マシンから実行することができます。 この例を macOS 上で実行するには、若干の変更が必要になる可能性があります (ファイルパスの変更など)。

### <a name="set-up-azure-resources"></a>Azure リソースの設定

この例では、関連するファイルをホストするために HDInsight Spark クラスターと Azure ストレージ アカウントが必要です。 これらの手順に従って、新しい Azure リソース グループに以下のリソースを作成します。

#### <a name="create-a-new-workbench-project"></a>新しいワークベンチ プロジェクトの作成

この例をテンプレートとして使用して新しいプロジェクトを作成します。
1.  Azure Machine Learning Workbench を開きます
2.  **[プロジェクト]** ページで **+** 記号をクリックし、**[新しいプロジェクト]** を選択します
3.  **[新しいプロジェクトの作成]** ウィンドウで、新しいプロジェクトの情報を入力します
4.  **[プロジェクト テンプレートの検索]** 検索ボックスに「Aerial Image Classification」と入力し、テンプレートを選択します
5.  **[作成]**
 
#### <a name="create-the-resource-group"></a>リソース グループの作成

1. Azure Machine Learning Workbench プロジェクトから、[ファイル]、[コマンド プロンプトを開く] の順にクリックしてコマンドライン インターフェイス (CLI) を開きます。
1. コマンド ライン インターフェイスから、次のコマンドを実行して Azure アカウントにログインします。

    ````
    az login
    ```

    You are asked to visit a URL and type in a provided temporary code; the website requests your Azure account credentials.
    
1. When login is complete, return to the CLI and run the following command to determine which Azure subscriptions are available to your Azure account:

    ```
    az account list
    ```

    This command lists all subscriptions associated with your Azure account. Find the ID of the subscription you would like to use. Write the subscription ID where indicated in the following command, then set the active subscription by executing the command:

    ```
    az account set --subscription [subscription ID]
    ```

1. The Azure resources created in this example are stored together in an Azure resource group. Choose a unique resource group name and write it where indicated, then execute both commands to create the Azure resource group:

    ```
    set AZURE_RESOURCE_GROUP=[resource group name]
    az group create --location eastus --name %AZURE_RESOURCE_GROUP%
    ```

#### Create the storage account

We now create the storage account that hosts project files that must be accessed by the HDInsight Spark.

1. Choose a unique storage account name and write it where indicated in the following `set` command, then create an Azure storage account by executing both commands:

    ```
    set STORAGE_ACCOUNT_NAME=[storage account name]
    az storage account create --name %STORAGE_ACCOUNT_NAME% --resource-group %AZURE_RESOURCE_GROUP% --sku Standard_LRS
    ```

1. Issue the following command to list the storage account keys:

    ```
    az storage account keys list --resource-group %AZURE_RESOURCE_GROUP% --account-name %STORAGE_ACCOUNT_NAME%
    ```

    Record the value of `key1` as the storage key in the following command, then run the command to store the value.
    ```
    set STORAGE_ACCOUNT_KEY=[storage account key]
    ```
1. In your favorite text editor, load the `settings.cfg` file from the Azure Machine Learning Workbench project's "Code" subdirectory, and insert the storage account name and key as indicated. Save and close the `settings.cfg` file.
1. If you have not already done so, download and install the [AzCopy](http://aka.ms/downloadazcopy) utility. Ensure that the AzCopy executable is on your system path by typing "AzCopy" and pressing Enter to show its documentation.
1. Issue the following commands to copy all of the sample data, pretrained models, and model training scripts to the appropriate locations in your storage account:

    ```
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/test /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/test /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/train /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/train /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/middlesexma2016 /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/middlesexma2016 /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    ```

    Expect file transfer to take up to 20 minutes. While you wait, you can proceed to the following section. You may need to open another Command Line Interface through Workbench and redefine the temporary variables there.

#### Create the HDInsight Spark cluster

Our recommended method to create an HDInsight cluster uses the HDInsight Spark cluster Resource Manager template included in the "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" subfolder of this project.

1. The HDInsight Spark cluster template is the "template.json" file under the "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" subfolder of this project. By default, the template creates a Spark cluster with 40 worker nodes. If you must adjust that number, open the template in your favorite text editor and replace all instances of "40" with the worker node number of your choice.
    - You may encounter out-of-memory errors if the number of worker nodes you choose is small. To combat memory errors, you may run the training and operationalization scripts on a subset of the available data as described later in this document.
2. Choose a unique name and password for the HDInsight cluster and write them where indicated in the following command. Then create the cluster by issuing the following command:

    ```
    set HDINSIGHT_CLUSTER_NAME=[HDInsight cluster name]
    set HDINSIGHT_CLUSTER_PASSWORD=[HDInsight cluster password]
    az group deployment create --resource-group %AZURE_RESOURCE_GROUP% --name hdispark --template-file "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning\template.json" --parameters storageAccountName=%STORAGE_ACCOUNT_NAME%.blob.core.windows.net storageAccountKey=%STORAGE_ACCOUNT_KEY% clusterName=%HDINSIGHT_CLUSTER_NAME% clusterLoginPassword=%HDINSIGHT_CLUSTER_PASSWORD%
    ```

Your cluster's deployment may take up to 30 minutes (including provisioning and script action execution).

### Prepare the Azure Machine Learning Workbench execution environment

#### Register the HDInsight cluster as an Azure Machine Learning Workbench compute target

Once HDInsight cluster creation is complete, register the cluster as a compute target for your project as follows:

1.  Issue the following command from the Azure Machine Learning Command Line Interface:

    ```
    az ml computetarget attach --name myhdi --address %HDINSIGHT_CLUSTER_NAME%-ssh.azurehdinsight.net --username sshuser --password %HDINSIGHT_CLUSTER_PASSWORD% -t cluster
    ```

    This command adds two files, `myhdi.runconfig` and `myhdi.compute`, to your project's `aml_config` folder.

1. Open the `myhdi.compute` file in your favorite text editor. Modify the `yarnDeployMode: cluster` line to read `yarnDeployMode: client`, then save and close the file.
1. Run the following command to prepare your environment for use:
   ```
   az ml experiment prepare -c myhdi
   ```

#### Install local dependencies

Open a CLI from Azure Machine Learning Workbench and install dependencies needed for local execution by issuing the following command:

```
pip install matplotlib azure-storage==0.36.0 pillow scikit-learn
```

## Data acquisition and understanding

This scenario uses publicly available aerial imagery data from the [National Agriculture Imagery Program](https://www.fsa.usda.gov/programs-and-services/aerial-photography/imagery-programs/naip-imagery/) at 1-meter resolution. We have generated sets of 224 pixel x 224 pixel PNG files cropped from the original NAIP data and sorted according to land use labels from the [National Land Cover Database](https://www.mrlc.gov/nlcd2011.php). A sample image with label "Developed" is shown at full size:

![A sample tile of developed land](media/scenario-aerial-image-classification/sample-tile-developed.png)

Class-balanced sets of ~44k and 11k images are used for model training and validation, respectively. We demonstrate model deployment on a ~67k image set tiling Middlesex County, MA -- home of Microsoft's New England Research and Development (NERD) center. For more information on how these image sets were constructed, see the [Embarrassingly Parallel Image Classification git repository](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

![Location of Middlesex County, Massachusetts](media/scenario-aerial-image-classification/middlesex-ma.png)

During setup, the aerial image sets used in this example were transferred to the storage account that you created. The training, validation, and operationalization images are all 224 pixel x 224 pixel PNG files at a resolution of one pixel per square meter. The training and validation images have been organized into subfolders based on their land use label. (The land use labels of the operationalization images are unknown and in many cases ambiguous; some of these images contain multiple land types.) For more information on how these image sets were constructed, see the [Embarrassingly Parallel Image Classification git repository](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

To view example images in your Azure storage account (optional):
1. Log in to the [Azure portal](https://portal.azure.com).
1. Search for the name of your storage account in the search bar along the top of your screen. Click on your storage account in the search results.
2. Click on the "Blobs" link in the storage account's main pane.
3. Click on the container named "train." You should see a list of directories named according to land use.
4. Click on any of these directories to load the list of images it contains.
5. Click on any image and download it to view the image.
6. If desired, click on the containers named "test" and "middlesexma2016" to view their contents as well.

## Modeling

### Training models with MMLSpark
The `run_mmlspark.py` script in the "Code\02_Modeling" subfolder of the Workbench project is used to train an [MMLSpark](https://github.com/Azure/mmlspark) model for image classification. The script first featurizes the training set images using an image classifier DNN pretrained on the ImageNet dataset (either AlexNet or an 18-layer ResNet). The script then uses the featurized images to train an MMLSpark model (either a random forest or a logistic regression model) to classify the images. The test image set is then featurized and scored with the trained model. The accuracy of the model's predictions on the test set is calculated and logged to Azure Machine Learning Workbench's run history feature. Finally, the trained MMLSpark model and its predictions on the test set are saved to blob storage.

Select a unique output model name for your trained model, a pretrained model type, and an MMLSpark model type. Write your selections where indicated in the following command template, then begin retraining by executing the command from an Azure ML Command Line Interface:

```
az ml experiment submit -c myhdi Code\02_Modeling\run_mmlspark.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --mmlspark_model_type {randomforest,logisticregression}
```

An additional `--sample_frac` parameter can be used to train and test the model with a subset of available data. Using a small sample fraction decreases runtime and memory requirements at the expense of trained model accuracy. For more information on this and other parameters, run `python Code\02_Modeling\run_mmlspark.py -h`.

Users are encouraged to run this script several times with different input parameters. The performance of the resulting models can then be compared in Azure Machine Learning Workbench's Run History feature.

### Comparing model performance using the Workbench Run History feature

After you have executed two or more training runs of either type, navigate to the Run History feature in Workbench by clicking the clock icon along the left-hand menu bar. Select `run_mmlspark.py` from the list of scripts at left. A pane loads comparing the test set accuracy for all runs. To see more detail, scroll down and click on the name of an individual run.

## Deployment

To apply one of your trained models to aerial images tiling Middlesex County, MA using remote execution on HDInsight, insert your desired model's name into the following command and execute it:

```
az ml experiment submit -c myhdi Code\03_Deployment\batch_score_spark.py --config_filename Code/settings.cfg --output_model_name [trained model name chosen earlier]
```

An additional `--sample_frac` parameter can be used to operationalize the model with a subset of available data. Using a small sample fraction decreases runtime and memory requirements at the expense of prediction completeness. For more information on this and other parameters, run `python Code\03_Deployment\batch_score_spark -h`.

This script writes the model's predictions to your storage account. The predictions can be examined as described in the next section.

## Visualization

The "Model prediction analysis" Jupyter notebook in the "Code\04_Result_Analysis" subfolder of the Workbench project visualizes a model's predictions. Load and run the notebook as follows:
1. Open the project in Workbench and click on the folder ("Files") icon along the left-hand menu to load the directory listing.
2. Navigate to the "Code\04_Result_Analysis" subfolder and click on the notebook named "Model prediction analysis." A preview rendering of the notebook should be displayed.
3. Click "Start Notebook Server" to load the notebook.
4. In the first cell, enter the name of the model whose results you would like to analyze where indicated.
5. Click on "Cell -> Run All" to execute all cells in the notebook.
6. Read along with the notebook to learn more about the analyses and visualizations it presents.

## Cleanup
When you have completed the example, we recommend that you delete all of the resources you have created by executing the following command from the Azure Command Line Interface:

  ```
  az group delete --name %AZURE_RESOURCE_GROUP%
  ```

## References

- [The Embarrassingly Parallel Image Classification repository](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification)
   - Describes dataset construction from freely available imagery and labels
- [MMLSpark](https://github.com/Azure/mmlspark) GitHub repository
   - Contains additional examples of model training and evaluation with MMLSpark

## Conclusions

Azure Machine Learning Workbench helps data scientists easily deploy their code on remote compute targets. In this example, local code was deployed for remote execution on an HDInsight cluster. Azure Machine Learning Workbench's run history feature tracked the performance of multiple models and helped us identify the most accurate model. Workbench's Jupyter notebooks feature helped visualize our models' predictions in an interactive, graphical environment.

## Next steps
To dive deeper into this example:
- In Azure Machine Learning Workbench's Run History feature, click the gear symbols to select which graphs and metrics are displayed.
- Examine the sample scripts for statements calling the `run_logger`. Check that you understand how each metric is being recorded.
- Examine the sample scripts for statements calling the `blob_service`. Check that you understand how trained models and predictions are stored and retrieved from the cloud.
- Explore the contents of the containers created in your blob storage account. Ensure that you understand which script or command is responsible for creating each group of files.
- Modify the training script to train a different MMLSpark model type or to change the model hyperparameters. Use the run history feature to determine whether your changes increased or decreased the model's accuracy.
