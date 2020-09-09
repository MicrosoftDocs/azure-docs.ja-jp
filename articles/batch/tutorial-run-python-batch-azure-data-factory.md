---
title: Data Factory から Python スクリプトを実行する
description: チュートリアル - Azure Batch を使用して Azure Data Factory のパイプラインから Python スクリプトを実行する方法について説明します。
author: mammask
ms.devlang: python
ms.topic: tutorial
ms.date: 08/12/2020
ms.author: komammas
ms.custom: mvc, devx-track-python
ms.openlocfilehash: f4c71cffe00faa6dd8cc440c59f94b8c2d60f712
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185113"
---
# <a name="tutorial-run-python-scripts-through-azure-data-factory-using-azure-batch"></a>チュートリアル:Azure Batch を使用して Azure Data Factory から Python スクリプトを実行する

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Batch アカウントおよびストレージ アカウントで認証する
> * Python スクリプトを開発して実行する
> * アプリケーションを実行するコンピューティング ノードのプールを作成する
> * Python ワークロードをスケジュールする
> * 分析パイプラインを監視する
> * ログファイルにアクセスする

以下の例で実行する Python スクリプトは、Blob Storage コンテナーから CSV 入力を受け取ってデータ操作処理を実行し、その出力を別の Blob Storage コンテナーに書き込むものです。

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。

## <a name="prerequisites"></a>前提条件

* ローカル テスト用にインストールされた [Python](https://www.python.org/downloads/) ディストリビューション。
* [Azure](https://pypi.org/project/azure/) `pip` パッケージ。
* [iris.csv データセット](https://www.kaggle.com/uciml/iris/version/2#Iris.csv)
* Azure Batch アカウントおよびリンクされた Azure ストレージ アカウント。 Batch アカウントを作成してストレージ アカウントにリンクさせる方法の詳細については、「[Batch アカウントを作成する](quick-create-portal.md#create-a-batch-account)」を参照してください。
* Azure Data Factory アカウント。 Azure portal からデータ ファクトリを作成する方法の詳細については、「[Data Factory の作成](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)」を参照してください。
* [Batch Explorer](https://azure.github.io/BatchExplorer/)。
* [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="create-a-batch-pool-using-batch-explorer"></a>Batch Explorer を使用して Batch プールを作成する

このセクションでは、Azure Data Factory のパイプラインで使用する Batch プールを Batch Explorer から作成します。 

1. ご自分の Azure 資格情報を使用して、Batch Explorer にサインインします。
1. Batch アカウントを選択します。
1. 左側のバーにある **[Pools]\(プール\)** 、検索フォームの上にある **[Add]\(追加\)** ボタンの順に選択して、プールを作成します。 
    1. ID と表示名を選択します。 この例では、`custom-activity-pool` を使用します。
    1. スケールの種類を **[Fixed size]\(固定サイズ\)** に設定し、専用ノードの数を 2 に設定します。
    1. **[データ サイエンス]** で、オペレーティング システムとして **[Dsvm Windows]** を選択します。
    1. 仮想マシンのサイズとして [`Standard_f2s_v2`] を選択します。
    1. 開始タスクを有効にし、コマンド `cmd /c "pip install pandas"` を追加します。 ユーザー ID は、既定値の **Pool user** のままでかまいません。
    1. **[OK]** を選択します。

## <a name="create-blob-containers"></a>BLOB コンテナーを作成する

ここで、OCR Batch ジョブの実際の入力および出力ファイルを格納する BLOB コンテナーを作成します。

1. ご自分の Azure 資格情報を使用して、Storage Explorer にサインインします。
1. ご自分の Batch アカウントにリンクされているストレージ アカウントを使用し、「[BLOB コンテナーを作成する](../vs-azure-tools-storage-explorer-blobs.md#create-a-blob-container)」の手順に従って、2 つの BLOB コンテナー (1 つは入力ファイル用、1 つは出力ファイル用) を作成します。
    * この例では、入力コンテナーを `input`、出力コンテナーを `output` と呼ぶことにします。
1. 「[BLOB コンテナー内の BLOB を管理する](../vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container)」の手順に従い、Storage Explorer を使用して [`iris.csv`](https://www.kaggle.com/uciml/iris/version/2#Iris.csv) を入力コンテナー `input` にアップロードします

## <a name="develop-a-script-in-python"></a>Python スクリプトを開発する

以下の Python スクリプトは、`input` コンテナーから `iris.csv` データセットを読み込み、データ操作処理を実行して、その結果を `output` コンテナーに戻して保存します。

``` python
# Load libraries
from azure.storage.blob import BlockBlobService
import pandas as pd

# Define parameters
storageAccountName = "<storage-account-name>"
storageKey         = "<storage-account-key>"
containerName      = "output"

# Establish connection with the blob storage account
blobService = BlockBlobService(account_name=storageAccountName,
                               account_key=storageKey
                               )

# Load iris dataset from the task node
df = pd.read_csv("iris.csv")

# Subset records
df = df[df['Species'] == "setosa"]

# Save the subset of the iris dataframe locally in task node
df.to_csv("iris_setosa.csv", index = False)

# Upload iris dataset
blobService.create_blob_from_path(containerName, "iris_setosa.csv", "iris_setosa.csv")
```

このスクリプトを `main.py` として保存し、**Azure Storage** コンテナーにアップロードします。 BLOB コンテナーにアップロードする前に、必ずその機能をローカルでテスト、検証してください。

``` bash
python main.py
```

## <a name="set-up-an-azure-data-factory-pipeline"></a>Azure Data Factory パイプラインを設定する

このセクションでは、Python スクリプトを使用してパイプラインを作成、検証します。

1. [こちらの記事](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)の「Data Factory の作成」セクションに記載された手順に従ってデータ ファクトリを作成します。
1. **[Factory Resources]\(Factory リソース\)** ボックスの + (正符号) ボタンを選択し、 **[パイプライン]** を選択します。
1. **[全般]** タブで、パイプラインの名前を「Run Python」に設定します。

    ![[全般] タブで、パイプラインの名前を「Run Python」に設定する](./media/run-python-batch-azure-data-factory/create-pipeline.png)

1. **[アクティビティ]** ボックスの **[Batch サービス]** を展開します。 **[アクティビティ]** ツールボックスからパイプライン デザイナー サーフェスにカスタム アクティビティをドラッグします。
1. **[全般]** タブで、[名前] に「**testPipeline**」と指定します。

    ![[全般] タブで、[名前] に「testPipeline」を指定する](./media/run-python-batch-azure-data-factory/create-custom-task.png)
1. **[Azure Batch]** タブで、前の手順で作成した **Batch アカウント**を追加し、**接続をテスト**して成功することを確認します。

    ![[Azure Batch] タブで、前の手順で作成した Batch アカウントを追加し、接続をテストする](./media/run-python-batch-azure-data-factory/integrate-pipeline-with-azure-batch.png)

1. **[設定]** タブで「`python main.py`」というコマンドを入力します。
1. **[Resource Linked Service]\(リソースのリンクされたサービス\)** で、前の手順で作成したストレージ アカウントを追加します。 接続をテストしてその成功を確認します。
1. **[フォルダーのパス]** で、Python スクリプトおよび関連する入力が格納されている **[Azure Blob Storage]** コンテナーの名前を選択します。 これで、Python スクリプトの実行前に、選択したファイルがコンテナーからプールのノード インスタンスにダウンロードされます。

    ![[フォルダーのパス] で、Azure Blob Storage コンテナーの名前を選択する](./media/run-python-batch-azure-data-factory/create-custom-task-py-script-command.png)
1. キャンバスの上にあるパイプライン ツール バーの **[検証]** をクリックして、パイプライン設定を検証します。 パイプラインが正常に検証されたことを確認します。 検証出力を閉じるには、&gt;&gt; (右矢印) ボタンを選択します。
1. **[デバッグ]** をクリックしてパイプラインをテストし、正しく動作することを確認します。
1. **[発行]** をクリックしてパイプラインを発行します。
1. **[トリガー]** をクリックすると、バッチ処理の一部として Python スクリプトが実行されます。

    ![[トリガー] をクリックすると、バッチ処理の一部として Python スクリプトが実行される](./media/run-python-batch-azure-data-factory/create-custom-task-py-success-run.png)

### <a name="monitor-the-log-files"></a>ログ ファイルを監視する

スクリプトを実行して警告またはエラーが発生した場合は、`stdout.txt` または `stderr.txt` を調査することで、ログに記録された出力についての詳しい情報を把握できます。

1. Batch Explorer の左側から **[ジョブ]** を選択します。
1. データ ファクトリによって作成されたジョブを選択します。 プールに `custom-activity-pool` という名前を付けた場合は、`adfv2-custom-activity-pool` を選択します。
1. 終了コードがエラーとなったタスクをクリックします。
1. `stdout.txt` および `stderr.txt` を見て、問題を調査、診断します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Batch を使用して Azure Data Factory のパイプラインから Python スクリプトを実行する例を詳しく見てきました。

Azure Data Factory についてさらに学習するには、次の記事を参照してください。

> [!div class="nextstepaction"]
> [Azure Data Factory](../data-factory/introduction.md)
> [パイプラインとアクティビティ](../data-factory/concepts-pipelines-activities.md)
> [カスタム アクティビティ](../data-factory/transform-data-using-dotnet-custom-activity.md)
