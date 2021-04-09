---
title: 'ML スタジオ (クラシック): Azure Machine Learning に移行する - データセットの再構築'
description: スタジオ (クラシック) のデータセットを Azure Machine Learning デザイナーで再構築する
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 02/04/2021
ms.openlocfilehash: 4c04dd5a2b41b3db54b20c9e514767453951cc35
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103565617"
---
# <a name="migrate-a-studio-classic-dataset-to-azure-machine-learning"></a>スタジオ (クラシック) のデータセットを Azure Machine Learning に移行する

この記事では、スタジオ (クラシック) のデータセットを Azure Machine Learning に移行する方法について説明します。 スタジオ (クラシック) からの移行の詳細については、[移行の概要に関する記事](migrate-overview.md)を参照してください。

データセットを Azure Machine Learning に移行するには、3 つのオプションがあります。 各セクションを読み、シナリオに最適なオプションを決定します。


|データの保存場所 | 移行オプション  |
|---------|---------|
|スタジオ (クラシック)     |  オプション 1: [データセットをスタジオ (クラシック) からダウンロードし、Azure Machine Learning にアップロードします](#download-the-dataset-from-studio-classic)。      |
|クラウド ストレージ     | オプション 2: [クラウド ソースからデータセットを登録します](#import-data-from-cloud-sources)。 <br><br>  オプション 3: [データのインポート モジュールを使用して、クラウド ソースからデータを取得します](#import-data-from-cloud-sources)。        |

> [!NOTE]
> Azure Machine Learning では、データセットを作成および管理するための[コードファースト ワークフロー](../how-to-create-register-datasets.md)もサポートされています。 

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Azure Machine Learning ワークスペース。 [Azure Machine Learning ワークスペースを作成](../how-to-manage-workspace.md#create-a-workspace)します。
- スタジオ (クラシック) から移行するデータセット。


## <a name="download-the-dataset-from-studio-classic"></a>スタジオ (クラシック) からデータセットをダウンロードする

スタジオ (クラシック) のデータセットを Azure Machine Learning に移行する最も簡単な方法は、データセットをダウンロードして Azure Machine Learning に登録することです。 これにより、データセットの新しいコピーが作成され、Azure Machine Learning データストアにアップロードされます。

スタジオ (クラシック) のデータセットは、次の種類を直接ダウンロードできます。

* プレーン テキスト (.txt)
* コンマ区切り値 (CSV) - ヘッダー付き (.csv) またはヘッダーなし (.nh.csv)
* タブ区切り値 (TSV) - ヘッダー付き (.tsv) またはヘッダーなし (.nh.tsv)
* Excel ファイル
* Zip ファイル (.zip)

データセットを直接ダウンロードするには、次のようにします。
1. スタジオ (クラシック) のワークスペース ([https://studio.azureml.net](https://studio.azureml.net)) にアクセスします。
1. 左側のナビゲーション バーで、 **[データセット]** タブを選択します。
1. ダウンロードするデータセットを選択します。
1. 下部のアクション バーで、 **[ダウンロード]** を選択します。

    ![スタジオ (クラシック) でデータセットをダウンロードする方法を示すスクリーンショット](./media/migrate-register-dataset/download-dataset.png)

次のデータ型については、 **[Convert to CSV]\(CSV への変換\)** モジュールを使用してデータセットをダウンロードする必要があります。

* SVMLight データ (svmlight) 
* Attribute Relation File Format (ARFF) データ (.arff) 
* R オブジェクトまたはワークスペース ファイル (.RData)
* データセットの種類 (.data)。 データセットの種類は、スタジオ (クラシック) のモジュール出力用の内部データ型です。

データセットを CSV に変換し、結果をダウンロードするには、次のようにします。

1. スタジオ (クラシック) のワークスペース ([https://studio.azureml.net](https://studio.azureml.net)) にアクセスします。
1. 新しい実験を作成する
1. ダウンロードするデータセットをキャンバスにドラッグ アンド ドロップします。
1. **[Convert to CSV]\(CSV への変換\)** モジュールを追加します。
1. **[Convert to CSV]\(CSV への変換\)** 入力ポートをデータセットの出力ポートに接続します。
1. 実験を実行します。
1. **[Convert to CSV]\(CSV への変換\)** モジュールを右クリックします。
1. **[Results dataset]\(結果データセット\)**  >  **[ダウンロード]** を選択します。

    ![CSV への変換パイプラインを設定する方法を示すスクリーンショット](./media/migrate-register-dataset/csv-download-dataset.png)

### <a name="upload-your-dataset-to-azure-machine-learning"></a>データセットを Azure Machine Learning にアップロードする

データ ファイルをダウンロードしたら、Azure Machine Learning でデータセットを登録できます。

1. Azure Machine Learning スタジオ ([ml.azure.com](https://ml.azure.com)) に移動します。
1. 左側のナビゲーション ペインで、 **[データセット]** タブを選択します。
1. **[データセットの作成]**  >  **[ローカル ファイルから]** を選択します。
    ![[データセット] タブとローカル ファイルを作成するためのボタンを示すスクリーンショット](./media/migrate-register-dataset/register-dataset.png)
1. 名前と説明を入力します。
1. **[データセットの種類]** で **[表形式]** を選択します。

    > [!NOTE]
    > データセットとして ZIP ファイルをアップロードすることもできます。 ZIP ファイルをアップロードするには、 **[データセットの種類]** で **[ファイル]** を選択します。

1. **[Datastore and file selection]\(データストアとファイルの選択\)** で、データセット ファイルをアップロードするデータストアを選択します。

    既定では、データセットは、Azure Machine Learning により既定のワークスペース blobstore に格納されます。 データストアの詳細については、「[Azure のストレージ サービスに接続する](../how-to-access-data.md)」を参照してください。

1. データ解析設定とデータセットのスキーマを設定します。 次に、設定を確認します。

## <a name="import-data-from-cloud-sources"></a>クラウド ソースからデータをインポートする

データが既にクラウド ストレージ サービス内にあり、データをそのネイティブな場所に保持する場合。 次のいずれかのオプションを使用できます。

|インジェスト方法|説明|
|---| --- |
|Azure Machine Learning データセットを登録する|ローカルとオンラインのデータ ソース (Blob、ADLS Gen1、ADLS Gen2、ファイル共有、SQL DB) からデータを取り込みます。 <br><br>データ ソースへの参照を作成します。これは、実行時に遅延評価されます。 このオプションは、このデータセットに繰り返しアクセスし、データのバージョン管理や監視などの高度なデータ機能を有効にする場合に使用します。
|データのインポート モジュール|オンライン データ ソース (Blob、ADLS Gen1、ADLS Gen2、ファイル共有、SQL DB) からデータを取り込みます。 <br><br> データセットは、現在のデザイナーのパイプライン実行にのみインポートされます。


>[!Note]
> スタジオ (クラシック) ユーザーは、次のクラウド ソースが Azure Machine Learning ではネイティブにサポートされていないことに注意してください。
> - Hive クエリ
> - Azure テーブル
> - Azure Cosmos DB
> - オンプレミス SQL Database
>
> ユーザーは Azure Data Factory を使用して、サポートされているストレージ サービスにデータを移行することをお勧めします。  

### <a name="register-an-azure-machine-learning-dataset"></a>Azure Machine Learning データセットを登録する

クラウド サービスから Azure Machine Learning にデータセットを登録するには、次の手順に従います。 

1. [データストアを作成](../how-to-connect-data-ui.md#create-datastores)します。これにより、クラウド ストレージ サービスが Azure Machine Learning ワークスペースにリンクされます。 

1. [データセットを登録](../how-to-connect-data-ui.md#create-datasets)します。 スタジオ (クラシック) のデータセットを移行する場合は、 **[表形式]** データセット設定を選択します。

データセットを Azure Machine Learning に登録したら、デザイナーで使用できるようになります。
 
1. デザイナーの新しいパイプライン ドラフトを作成します。
1. 左側のモジュール パレットで、 **[データセット]** セクションを展開します。
1. 登録したデータセットをキャンバスにドラッグします。 

### <a name="use-the-import-data-module"></a>[データのインポート] モジュールを使用する

デザイナー パイプラインにデータを直接インポートするには、次の手順に従います。

1. [データストアを作成](https://github.com/MicrosoftDocs/azure-docs-pr/blob/master/articles/machine-learning/how-to-connect-data-ui.md#create-datastores)します。これにより、クラウド ストレージ サービスが Azure Machine Learning ワークスペースにリンクされます。 

データストアを作成したら、デザイナーの [ **[データのインポート]** ](../algorithm-module-reference/import-data.md) モジュールを使用して、データストアからデータを取り込むことができます。

1. デザイナーの新しいパイプライン ドラフトを作成します。
1. 左側のモジュール パレットで、 **[データのインポート]** モジュールを見つけてキャンバスにドラッグします。
1. **[データのインポート]** モジュールを選択し、右側のパネルの設定を使用してデータ ソースを構成します。

## <a name="next-steps"></a>次のステップ

この記事では、スタジオ (クラシック) のデータセットを Azure Machine Learning に移行する方法について説明しました。 次の手順では、[スタジオ (クラシック) のトレーニング パイプラインを再構築します](migrate-rebuild-experiment.md)。


スタジオ (クラシック) 移行シリーズの他の記事を参照してください。

1. [移行の概要](migrate-overview.md)に関するドキュメントを参照してください。
1. **データセットを移行します**。
1. [スタジオ (クラシック) のトレーニング パイプラインを再構築します](migrate-rebuild-experiment.md)。
1. [スタジオ (クラシック) の Web サービスを再構築します](migrate-rebuild-web-service.md)。
1. [Azure Machine Learning の Web サービスをクライアント アプリと統合します](migrate-rebuild-integrate-with-client-app.md)。
1. [R スクリプトの実行を移行します](migrate-execute-r-script.md)。
