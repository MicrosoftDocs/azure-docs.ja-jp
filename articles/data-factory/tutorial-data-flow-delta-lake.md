---
title: Delta Lake ETL とデータ フロー
description: このチュートリアルでは、データ フローを使用して Delta Lake 内のデータを変換および分析するための詳細な手順を説明します。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 02/09/2021
ms.openlocfilehash: cb8d44353e826df14ed3baab2c4ca66ffed4a569
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100416792"
---
# <a name="transform-data-in-delta-lake-using-mapping-data-flows"></a>マッピング データ フローを使用して Delta Lake のデータを変換する

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory を初めて使用する場合は、「[Azure Data Factory の概要](introduction.md)」を参照してください。

このチュートリアルでは、データ フロー キャンバスを使用してデータ フローを作成します。これにより、Azure Data Lake Storage (ADLS) Gen2 内のデータを分析して変換し、Delta Lake に格納することができます。

## <a name="prerequisites"></a>前提条件
* **Azure サブスクリプション**。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* **Azure ストレージ アカウント**。 ADLS ストレージを、*ソース* と *シンク* のデータ ストアとして使用します。 ストレージ アカウントがない場合の作成手順については、[Azure のストレージ アカウントの作成](../storage/common/storage-account-create.md)に関するページを参照してください。

このチュートリアルで変換するファイルは MoviesDB です。このファイルは、[こちら](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/moviesDB2.csv)にあります。 GitHub からファイルを取得するには、コンテンツを任意のテキスト エディターにコピーして、.csv ファイルとしてローカルに保存します。 ファイルをご自分のストレージ アカウントにアップロードするには、[Azure portal を使用した BLOB のアップロード](../storage/blobs/storage-quickstart-blobs-portal.md)に関するページを参照してください。 例では、'sample-data' という名前のコンテナーを参照しています。

## <a name="create-a-data-factory"></a>Data Factory の作成

この手順では、データ ファクトリを作成し、Data Factory UX を開いて、データ ファクトリにパイプラインを作成します。

1. **Microsoft Edge** または **Google Chrome** を開きます。 現在、Data Factory の UI がサポートされる Web ブラウザーは Microsoft Edge と Google Chrome だけです。
1. 左側のメニューで、 **[リソースの作成]**  >  **[統合]**  >  **[Data Factory]** を選択します。
1. **[新しいデータ ファクトリ]** ページで、 **[名前]** に「**ADFTutorialDataFactory**」と入力します。
1. データ ファクトリを作成する Azure **サブスクリプション** を選択します。
1. **[リソース グループ]** で、次の手順のいずれかを行います。

    a. **[Use existing (既存のものを使用)]** を選択し、ドロップダウン リストから既存のリソース グループを選択します。

    b. **[新規作成]** を選択し、リソース グループの名前を入力します。 
         
    リソース グループの詳細については、[リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/management/overview.md)に関するページを参照してください。 
1. **[バージョン]** で、 **[V2]** を選択します。
1. **[場所]** で、データ ファクトリの場所を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリによって使用されるデータ ストア (Azure Storage、SQL Database など) やコンピューティング (Azure HDInsight など) は、他のリージョンに存在していてもかまいません。
1. **［作成］** を選択します
1. 作成が完了すると、その旨が通知センターに表示されます。 **[リソースに移動]** を選択して、Data factory ページに移動します。
1. **[Author & Monitor]\(作成と監視\)** を選択して、別のタブで Data Factory (UI) を起動します。

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>データ フロー アクティビティが含まれるパイプラインの作成

この手順では、データ フロー アクティビティが含まれるパイプラインを作成します。

1. **[Let's get started]\(始めましょう\)** ページで **[Create pipeline]\(パイプラインの作成\)** を選択します。

   ![パイプラインの作成](./media/doc-common-process/get-started-page.png)

1. パイプラインの **[全般]** タブで、パイプラインの **名前** として「**DeltaLake**」と入力します。
1. ファクトリの上部のバーで、 **[Data Flow のデバッグ]** スライダーをオンにスライドします。 デバッグ モードを使用すると、ライブ Spark クラスターに対する変換ロジックの対話型テストが可能になります。 Data Flow クラスターのウォームアップには 5 から 7 分かかるため、ユーザーが Data Flow の開発を計画している場合は、最初にデバッグを有効にすることをお勧めします。 詳細については、[デバッグ モード](concepts-data-flow-debug-mode.md)に関するページを参照してください。

    ![Data Flow アクティビティ](media/tutorial-data-flow/dataflow1.png)
1. **[アクティビティ]** ウィンドウで、 **[移動と変換]** アコーディオンを展開します。 ウィンドウから **Data Flow** アクティビティをパイプライン キャンバスにドラッグ アンド ドロップします。

    ![Data Flow アクティビティをドロップできるパイプライン キャンバスを示すスクリーンショット。](media/tutorial-data-flow/activity1.png)
1. **[Data Flow の追加]** ポップアップで、 **[新しい Data Flow の作成]** を選択し、データ フローに **DeltaLake** という名前を付けます。 終了したら、[完了] をクリックします。

    ![新しいデータ フローを作成するときにデータ フローの名前を指定する場所を示すスクリーンショット。](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>データ フロー キャンバスでの変換ロジックの作成

このチュートリアルでは、2 つのデータ フローを生成します。 最初のデータ フローは、上記のムービーの CSV ファイルから新しい Delta Lake を生成するためにシンクする単純なソースです。 最後に、以下のフロー デザインを作成して、Delta Lake のデータを更新します。

![最終的なフロー](media/data-flow/data-flow-tutorial-6.png "最終的なフロー")

### <a name="tutorial-objectives"></a>チュートリアルの目標

1. 上記の MoviesCSV データセット ソースを取得し、そこから新しい Delta Lake を作成します。
1. 1988 年のムービーの評価を '1' に更新するためのロジックを作成します。
1. 1950 年のすべてのムービーを削除します。
1. 1960 年のムービーを複製して 2021 年用の新しいムービーを挿入します。

### <a name="start-from-a-blank-data-flow-canvas"></a>空のデータ フロー キャンバスから開始する

1. [ソース変換] をクリックします。
1. 下側パネル 1 のデータセットの横にある [新規] をクリックします。ADLS Gen2 の新しいリンクされたサービスを作成します。
1. データセットの種類として [区切りテキスト] を選択します。
1. データセットに "MoviesCSV" という名前を付けます。 
1. 前述のストレージにアップロードした MoviesCSV ファイルを指します。
1. コンマ区切りに設定し、先頭行にヘッダーを含めます。 
1. [ソース プロジェクション] タブに移動し、[データ型の検出] をクリックします。
1. プロジェクション セットを作成したら、続行することができます。 
1. シンク変換を追加します。
1. Delta はインライン データセット型です。 ADLS Gen2 ストレージ アカウントを指す必要があります。
   
   ![インライン データセット](media/data-flow/data-flow-tutorial-5.png "インライン データセット")

1. ADF で Delta Lake を作成するストレージ コンテナー内のフォルダー名を選択します。
1. パイプライン デザイナーに戻り、[デバッグ] をクリックしてデバッグ モードでパイプラインを実行し、キャンバス上のこのデータ フロー アクティビティだけを使用します。 これにより、ADLS Gen2 に新しい Delta Lake が生成されます。
1. [ファクトリ リソース] から、[新規] > [データ フロー] をクリックします。 
1. ソースとして再度 MoviesCSV を使用して、[データ型の検出] をもう一度クリックします。
1. グラフのソース変換にフィルター変換を追加します。
1. 操作しようとしている 3 年間と一致するムービー行のみを許可します。これは 1950 年、1988 年、1960 年になります。
1. フィルター変換に派生列変換を追加して、1988 年のムービーごとに評価を '1' に更新します。
1. 同じ派生列で、既存の年を取得し、その年を 2021 に変更して、2021 年のムービーを作成します。 1960 年を選択してみましょう。
1. 3 つの派生列は次のようになります。

   ![派生列](media/data-flow/data-flow-tutorial-2.png "派生列")
   
1. ```Update, insert, delete, and upsert``` ポリシーは、行の変更変換で作成されます。 派生列の後に、行の変更変換を追加します。
1. 変更行ポリシーは、次のようになります。

   ![行の変更](media/data-flow/data-flow-tutorial-3.png "行の変更")
   
1. 変更行の種類ごとに適切なポリシーを設定したので、シンク変換に適切な更新ルールが設定されていることを確認します。

   ![Sink](media/data-flow/data-flow-tutorial-4.png "シンク")
   
1. ここでは、ADLS Gen2 データ レイクに Delta Lake シンクを使用して、挿入、更新、削除を許可しています。 
1. キー列は、movie 主キー列と year 列で構成される複合キーです。 これは、1960 年行を複製して仮の 2021 年ムービーを作成したためです。 こうして一意性を提供することによって、既存の行を検索するときの競合が回避されます。

### <a name="download-completed-sample"></a>完成したサンプルのダウンロード
[これは、レイクで行を更新または削除するためのデータ フローを持つ Delta パイプラインのサンプル ソリューションです。](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/DeltaPipeline.zip)

## <a name="next-steps"></a>次のステップ

[データ フローの式言語](data-flow-expression-functions.md)の詳細を確認します。
