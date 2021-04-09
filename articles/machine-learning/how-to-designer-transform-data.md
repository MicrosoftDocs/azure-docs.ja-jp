---
title: デザイナーでデータを変換する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning デザイナーでデータをインポートおよび変換して、独自のデータセットを作成する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: peterclu
ms.author: peterlu
ms.date: 06/28/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: 10904275d382c7f9dbf223ded78cab0adf14992a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101661054"
---
# <a name="transform-data-in-azure-machine-learning-designer"></a>Azure Machine Learning デザイナーでデータを変換する


この記事では、機械学習用に独自のデータを準備できるように、Azure Machine Learning デザイナーでデータセットを変換および保存する方法について説明します。

サンプルの[国勢調査の成人収入に関する二項分類](./samples-designer.md)データセットを使用して、2 つのデータセットを準備します。1 つのデータセットには米国のみの成人の国勢調査情報が含まれ、もう 1 つのデータセットには米国以外の成人の国勢調査情報が含まれています。

この記事では、次のことについて説明します。

1. データセットを変換し、トレーニング用に準備します。
1. 結果のデータセットをデータストアにエクスポートします。
1. 結果を表示します。

この操作方法は、[デザイナーのモデルを再トレーニングする方法](how-to-retrain-designer.md)に関する記事の前提条件です。 その記事では、変換されたデータセットを使用して、パイプラインのパラメーターを使用して複数のモデルをトレーニングする方法について説明しています。

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="transform-a-dataset"></a>データセットの変換

このセクションでは、サンプル データセットをインポートし、データを米国と米国以外のデータセットに分割する方法について説明します。 独自のデータをデザイナーにインポートする方法の詳細については、[データのインポート方法](how-to-designer-import-data.md)に関する記事を参照してください。

### <a name="import-data"></a>データのインポート

サンプル データセットをインポートするには、次の手順を使用します。

1. <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a> にサインインし、使用するワークスペースを選択します。

1. デザイナーにアクセスします。 **[Easy-to-use-prebuild modules]\(Easy-to-use-prebuild モジュール\)** を選択して、新しいパイプラインを作成します。

1. パイプラインを実行する既定のコンピューティング先を選択します。

1. パイプライン キャンバスの左側には、データセットとモジュールのパレットがあります。 **[データセット]** を選択します。 次に、 **[サンプル]** セクションを表示します。

1. **[Adult Census Income Binary classification]\(国勢調査の成人収入に関する二項分類\)** データセットをキャンバスにドラッグ アンド ドロップします。

1. **[Adult Census Income]\(国勢調査の成人収入\)** データセット モジュールを右クリックし、 **[視覚化]**  >  **[データセットの出力]** を選択します。

1. データ プレビュー ウィンドウを使用して、データセットを探索します。 "native-country" 列の値に特に留意してください。

### <a name="split-the-data"></a>データを分割する

このセクションでは、[Split Data モジュール](algorithm-module-reference/split-data.md)を使用して、"native-country" 列の "United-States" を含む行を識別し、分割します。 

1. キャンバスの左側にあるモジュール パレットで **[データの変換]** セクションを展開し、 **[Split Data]** モジュールを見つけます。

1. **[Split Data]** モジュールをキャンバスにドラッグし、そのモジュールをデータセット モジュールの下にドロップします。

1. データセット モジュールを **[Split Data]** モジュールに接続します。

1. **[Split Data]\(データの分割\)** モジュールを選択します。

1. キャンバスの右側にあるモジュールの詳細ウィンドウで **[Splitting mode]\(分割モード\)** を **[正規表現]** に設定します。

1. **正規表現**: `\"native-country" United-States` を入力します。

    **正規表現** モードでは、1 つの列に値があるかどうかがテストされます。 Split Data モジュールの詳細については、関連する[アルゴリズム モジュールのリファレンス ページ](algorithm-module-reference/split-data.md)を参照してください。

パイプラインは次のようになっているはずです。

:::image type="content" source="./media/how-to-designer-transform-data/split-data.png" alt-text="パイプラインと Split Data モジュールの構成方法を示すスクリーンショット":::


## <a name="save-the-datasets"></a>データセットの保存

データを分割するようにパイプラインを設定したので、データセットを保持する場所を指定する必要があります。 この例では、**Export Data** モジュールを使用して、データセットをデータストアに保存します。 データストアの詳細については、「[Azure Storage サービスに接続する](how-to-access-data.md)」を参照してください

1. キャンバスの左側にあるモジュール パレットで **[Data Input and Output]\(データの入力と出力\)** セクションを展開し、 **[Export Data]** モジュールを見つけます。

1. 2 つの **[Export Data]** モジュールを **[Split Data]** モジュールの下にドラッグ アンド ドロップします。

1. **Split Data** モジュールの各出力ポートを、別々の **Export Data** モジュールに接続します。

    パイプラインは次のようになります。

    ![Export Data モジュールの接続方法を示すスクリーンショット](media/how-to-designer-transform-data/export-data-pipeline.png).

1. **[Split Data]** モジュールの最も "*左*" のポートに接続されている **[Export Data]** モジュールを選択します。

    **Split Data** モジュールでは、出力ポートの順序が重要です。 最初の出力ポートには、正規表現が true である行が含まれます。 今回のケースでは、最初のポートには米国ベースの収入の行が含まれ、2 番目のポートには米国以外のベースの収入の行が含まれます。

1. キャンバスの右側にあるモジュールの詳細ウィンドウで、次のオプションを設定します。
    
    **Datastore type (データストアの種類)** :Azure Blob Storage

    **データストア**:既存のデータストアを選択するか、[New datastore]\(新しいデータストア\) を選択して、ここで作成します。

    **パス**: `/data/us-income`

    **ファイル形式**: csv

    > [!NOTE]
    > この記事では、現在の Azure Machine Learning ワークスペースに登録されているデータストアにアクセスできることを前提としています。 データストアのセットアップ方法の手順については、「[Azure Storage サービスに接続する](how-to-connect-data-ui.md#create-datastores)」を参照してください。

    データストアがない場合は、ここで作成できます。 例として、この記事では、ワークスペースに関連付けられている既定の BLOB ストレージ アカウントにデータセットを保存します。 これにより、データセットは `data` という新しいフォルダーの `azureml` コンテナーに保存されます。

1.  **[Split Data]** モジュールの最も "*右*" のポートに接続されている **[Export Data]** モジュールを選択します。

1. キャンバスの右側にあるモジュールの詳細ウィンドウで、次のオプションを設定します。
    
    **Datastore type (データストアの種類)** :Azure Blob Storage

    **データストア**:上記と同じデータストアを選択します

    **パス**: `/data/non-us-income`

    **ファイル形式**: csv

1. **[Split Data]** の左側のポートに接続されている **[Export Data]** モジュールに **[パス]** `/data/us-income` があることを確認します。

1. 右側のポートに接続されている **[Export Data]** モジュールに **[パス]** `/data/non-us-income` があることを確認します。

    パイプラインと設定は、次のようになります。
    
    ![Export Data モジュールの構成方法を示すスクリーンショット](media/how-to-designer-transform-data/us-income-export-data.png).

### <a name="submit-the-run"></a>実行の送信

パイプラインがデータの分割とエクスポートを行うように設定されたので、パイプラインの実行を送信します。

1. キャンバス上部の **[Submit]\(送信\)** を選択します。

1. **[パイプライン実行のセットアップ]** ダイアログで **[新規作成]** を選択して、実験を作成します。

    実験では、関連するパイプライン実行を論理的にグループ化します。 このパイプラインを今後実行する場合は、ログと追跡のために同じ実験を使用する必要があります。

1. "split-census-data" のようなわかりやすい実験名を指定します。

1. **[Submit]\(送信\)** をクリックします。

## <a name="view-results"></a>結果の表示

パイプラインの実行が完了したら、Azure portal の BLOB ストレージに移動して結果を表示できます。 データが正常に分割されたことを確認するために、**Split Data** モジュールの中間結果を表示することもできます。

1. **[Split Data]\(データの分割\)** モジュールを選択します。

1. キャンバスの右側にある [モジュールの詳細] ペインで **[Outputs + logs]\(出力 + ログ\)** を選択します。 

1. **[Results dataset1]\(結果のデータセット 1\)** の横にある視覚化アイコン ![視覚化アイコン](media/how-to-designer-transform-data/visualize-icon.png) を選択します。 

1. "native-country" 列に "United-States" という値のみが含まれていることを確認します。

1. **[Results dataset2]\(結果のデータセット 2\)** の横にある視覚化アイコン ![視覚化アイコン](media/how-to-designer-transform-data/visualize-icon.png) を選択します。 

1. "native-country" 列に "United-States" という値が含まれていないことを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

引き続き、このハウツーのパート 2 である「[Azure Machine Learning デザイナーを使用してモデルを再トレーニングする](how-to-retrain-designer.md)」に進む場合は、このセクションをスキップしてください。

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>次のステップ

この記事では、データセットを変換し、登録されているデータストアに保存する方法を学習しました。

変換されたデータセットとパイプライン パラメーターを使用して機械学習モデルをトレーニングするには、「[Azure Machine Learning デザイナーを使用してモデルを再トレーニングする](how-to-retrain-designer.md)」でこのハウツー シリーズの次のパートに進んでください。