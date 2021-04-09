---
title: 'クイックスタート: マッピング データ フローを使用してデータを変換する'
description: このチュートリアルでは、Azure Synapse Analytics でマッピング データ フローを使用してデータを変換するための詳細な手順を説明します。
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/03/2020
ms.openlocfilehash: cb0fd5464f7025d71dd384c56233aefa6a6cd364
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98218724"
---
# <a name="quickstart-transform-data-using-mapping-data-flows"></a>クイックスタート: マッピング データ フローを使用してデータを変換する

このクイックスタートでは、Azure Synapse Analytics を使い、マッピング データ フローを使用して Azure Data Lake Storage Gen2 (ADLS Gen2) ソースから ADLS Gen2 シンクにデータを変換するパイプラインを作成します。 このクイックスタートの構成パターンは、マッピング データ フローを使用してデータを変換するときに拡張することができます。

このクイックスタートでは、次の手順を実行します。

> [!div class="checklist"]
> * Azure Synapse Analytics でデータ フロー アクティビティが含まれるパイプラインを作成します。
> * 4 つの変換を使用して、マッピング データ フローを構築します。
> * パイプラインをテスト実行します。
> * Data Flow アクティビティを監視します。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* **Azure Synapse ワークスペース**:「[クイックスタート:Synapse ワークスペースを作成する](quickstart-create-workspace.md)」の手順に従い、Azure portal を使用して Synapse ワークスペースを作成します。
* **Azure ストレージ アカウント**:ADLS ストレージを、"*ソース*" と "*シンク*" のデータ ストアとして使用します。 ストレージ アカウントがない場合の作成手順については、[Azure のストレージ アカウントの作成](../storage/common/storage-account-create.md)に関するページを参照してください。

    このチュートリアルで変換するファイルは MoviesDB です。このファイルは、[こちら](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv)にあります。 GitHub からファイルを取得するには、コンテンツを任意のテキスト エディターにコピーして、.csv ファイルとしてローカルに保存します。 ファイルをご自分のストレージ アカウントにアップロードするには、[Azure portal を使用した BLOB のアップロード](../storage/blobs/storage-quickstart-blobs-portal.md)に関するページを参照してください。 例では、'sample-data' という名前のコンテナーを参照しています。

### <a name="navigate-to-the-synapse-studio"></a>Synapse Studio への移動

Azure Synapse ワークスペースが作成された後、Synapse Studio を開く方法は 2 つあります。

* [Azure portal](https://ms.portal.azure.com/#home) で Synapse ワークスペースを開きます。 [はじめに] の下の [Synapse Studio を開く] カードで、 **[開く]** を選択します。
* [Azure Synapse Analytics](https://web.azuresynapse.net/) を開き、ワークスペースにサインインします。

このクイックスタートでは、例として "adftest2020" という名前のワークスペースを使用します。 自動的に Synapse Studio のホーム ページに移動します。

![Synapse Studio のホーム ページ](media/doc-common-process/synapse-studio-home.png)

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Data Flow アクティビティを含むパイプラインの作成

パイプラインには、一連のアクティビティを実行するための論理フローが含まれています。 このセクションでは、Data Flow アクティビティが含まれるパイプラインを作成します。

1. **[統合]** タブに移動します。パイプライン ヘッダーの横にあるプラス符号のアイコンを選択し、[パイプライン] を選択します。

   ![新しいパイプラインを作成する](media/doc-common-process/new-pipeline.png)

1. パイプラインの **[プロパティ]** 設定ページで、**名前** として「**TransformMovies**」と入力します。

1. *[アクティビティ]* ペインの *[Move and Transform]\(移動と変換\)* で、 **[データ フロー]** をパイプライン キャンバス上にドラッグします。

1. **[Adding data flow]\(データ フローの追加\)** ページ ポップアップで、 **[Create new data flow]\(新しいデータ フローの作成\)**  ->  **[データ フロー]** を選択します。 完了したら、 **[OK]** をクリックします。

   ![データ フローの作成](media/quickstart-data-flow/new-data-flow.png)

1. **[プロパティ]** ページで、データ フローに **TransformMovies** という名前を付けます。

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>データ フロー キャンバスでの変換ロジックの作成

Data Flow を作成すると、データ フロー キャンバスが自動的に表示されます。 この手順では、ADLS ストレージ内の MoviesDB.csv を取得し、1910 年から 2000 年までのコメディの平均評価を集計するデータ フローを作成します。 次に、このファイルを ADLS ストレージに書き戻します。

1. データ フロー キャンバスの上にある **[Data flow debug]\(データ フローのデバッグ\)** スライダーをオンにスライドします。 デバッグ モードを使用すると、ライブ Spark クラスターに対する変換ロジックの対話型テストが可能になります。 Data Flow クラスターのウォームアップには 5 から 7 分かかるため、ユーザーが Data Flow の開発を計画している場合は、最初にデバッグを有効にすることをお勧めします。 詳細については、[デバッグ モード](../data-factory/concepts-data-flow-debug-mode.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)に関するページを参照してください。

    ![デバッグをオンにスライド](media/quickstart-data-flow/debug-on.png)

1. データ フロー キャンバスで **[Add Source]\(ソースの追加\)** ボックスをクリックして、ソースを追加します。

1. ソースに **MoviesDB** という名前を付けます。 **[新規]** をクリックして、新しいソース データセットを作成します。

    ![新しいソース データセットの作成](media/quickstart-data-flow/new-source-dataset.png)

1. **[Azure Data Lake Storage Gen2]** を選択します。 [続行] をクリックして続行します。

    ![[Azure Data Lake Storage Gen2] の選択](media/quickstart-data-flow/select-source-dataset.png)

1. **[DelimitedText]** を選択します。 [続行] をクリックして続行します。

1. データセットに **MoviesDB** という名前を付けます。 リンクされたサービスのドロップダウンで、 **[新規]** を選択します。

1. リンクされたサービスの作成画面で、ADLS Gen2 のリンクされたサービスに **ADLSGen2** という名前を付けて、使用する認証方法を指定します。 次に、接続の資格情報を入力します。 このクイックスタートでは、アカウント キーを使用してストレージ アカウントに接続しています。 **[テスト接続]** をクリックすると、資格情報が正しく入力されたことを確認できます。 完了したら **[作成]** をクリックします。

    ![ソースのリンクされたサービスの作成](media/quickstart-data-flow/adls-gen2-linked-service.png)

1. データセットの作成画面に戻ったら、 **[ファイル パス]** フィールドに、ファイルが配置されている場所を入力します。 このクイックスタートでは、"MoviesDB.csv" ファイルはコンテナー "sample-data" に配置されています。 ファイルにはヘッダーが含まれているため、 **[First row as header]\(最初の行をヘッダーにする\)** をオンにします。 ストレージ内のファイルからヘッダー スキーマを直接インポートするには、 **[From connection/store]\(接続/ストアから\)** を選択します。 完了したら、 **[OK]** をクリックします。

    ![ソース データセットの設定](media/quickstart-data-flow/source-dataset-properties.png)

1. デバッグ クラスターが起動している場合は、ソース変換の **[Data Preview]\(データのプレビュー\)** タブに移動し、 **[更新]** をクリックして、データのスナップショットを取得します。 データ プレビューを使用すると、変換が正しく構成されていることを確認できます。

    ![データのプレビュー](media/quickstart-data-flow/data-preview.png)

1. データ フロー キャンバスでソース ノードの横にあるプラス アイコンをクリックして、新しい変換を追加します。 最初に追加する変換は、**フィルター** です。

    ![フィルターを追加する](media/quickstart-data-flow/add-filter.png)

1. フィルター変換に **FilterYears** という名前を付けます。 **[フィルター適用]** の横にある式ボックスをクリックして、式ビルダーを開きます。 ここでフィルター条件を指定します。

1. データ フローの式ビルダーでは、さまざまな変換で使用する式を対話形式で作成できます。 式には、組み込み関数、入力スキーマの列、ユーザー定義のパラメーターを含めることができます。 式の作成方法の詳細については、[Data Flow の式ビルダー](../data-factory/concepts-data-flow-expression-builder.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)に関するページを参照してください。

    このクイックスタートでは、1910 年から 2000 年の間に公開された、ジャンルがコメディの映画をフィルター処理します。 現在、年は文字列になっているため、```toInteger()``` 関数を使用して整数に変換する必要があります。 以上演算子 (>=) と以下演算子 (<=) を使用して、年のリテラル値 1910 と 2000 に対する比較を行います。 これらの式を and (&&) 演算子を使用して結合します。 式は次のようになります。

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    コメディ映画を見つけるには、```rlike()``` 関数を使用して、ジャンル列でパターン 'Comedy' を検索します。 rlike 式と年の比較を結合して、次を取得します。

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    ![フィルター条件の指定](media/quickstart-data-flow/visual-expression-builder.png)

    デバッグ クラスターがアクティブになっている場合は、 **[更新]** をクリックして使用された入力と比較した式の出力を表示して、ロジックを確認できます。 データ フローの式言語を使用してこのロジックを実現する方法に対する正解は複数あります。

    式の操作が完了したら、 **[Save and Finish]\(保存して終了する\)** をクリックします。

1. フィルターが正しく機能していることを確認するには、**データ プレビュー** をフェッチします。

1. 次に追加する変換は、 **[Schema modifier]\(スキーマ修飾子\)** の下にある **[集計]** 変換です。

    ![集計の追加](media/quickstart-data-flow/add-aggregate.png)

1. 集計変換に **AggregateComedyRatings** という名前を付けます。 **[グループ化]** タブで、ドロップダウンから **[year]** を選択し、映画の公開年ごとに集計をグループ化します。

    ![集計の設定 1](media/quickstart-data-flow/aggregate-settings.png)

1. **[集計]** タブに移動します。左側のテキスト ボックスで、集計列に **AverageComedyRating** という名前を付けます。 式ビルダーを使用して集計式を入力するには、右側の式ボックスをクリックします。

    ![集計の設定 2](media/quickstart-data-flow/aggregate-settings-2.png)

1. 列 **[Rating]** の平均値を取得するには、```avg()``` 集計関数を使用します。 **Rating** は文字列で、```avg()``` で受け入れられるのは数値入力なので、```toInteger()``` 関数を使用して値を数値に変換する必要があります。 式は次のようになります。

    ```avg(toInteger(Rating))```

    完了したら、 **[Save and Finish]\(保存して終了する\)** をクリックします。

    ![コメディの評価の平均](media/quickstart-data-flow/average-comedy-rating.png)

1. 変換出力を表示するには、 **[Data Preview]\(データのプレビュー\)** タブに移動します。 **year** と **AverageComedyRating** の 2 つの列だけがあることに注目してください。

    ![集計データのプレビュー](media/quickstart-data-flow/transformation-output.png)

1. 次に、 **[Destination]\(変換先\)** の下で **[シンク]** 変換を追加します。

    ![シンクの追加](media/quickstart-data-flow/add-sink.png)

1. シンクに **Sink** という名前を付けます。 **[新規]** をクリックして、シンク データセットを作成します。

1. **[Azure Data Lake Storage Gen2]** を選択します。 [続行] をクリックして続行します。

1. **[DelimitedText]** を選択します。 [続行] をクリックして続行します。

1. シンク データセットに **MoviesSink** という名前を付けます。 リンクされたサービスの場合、手順 7 で作成した ADLS Gen2 のリンクされたサービスを選択します。 データの書き込み先となる出力フォルダーを入力します。 このクイックスタートでは、コンテナー "sample-data" 内のフォルダー "output" に書き込んでいます。 フォルダーは、事前に存在している必要はなく、動的に作成することができます。 **[First row as header]\(最初の行をヘッダーにする\)** をオンに設定し、 **[スキーマのインポート]** で **[なし]** を選択します。 完了したら、 **[OK]** をクリックします。

    ![シンク データセットのプロパティ](media/quickstart-data-flow/sink-dataset-properties.png)

これで、データ フローの構築が完了しました。 これをパイプラインで実行する準備ができました。

## <a name="running-and-monitoring-the-data-flow"></a>Data Flow を実行して監視する

パイプラインを発行する前にデバッグすることができます。 この手順では、データ フロー パイプラインのデバッグ実行をトリガーします。 データのプレビューではデータが書き込まれませんが、デバッグ実行によってシンクの変換先にデータが書き込まれます。

1. パイプライン キャンバスに移動します。 **[デバッグ]** をクリックして、デバッグ実行をトリガーします。

    ![パイプラインのデバッグ](media/quickstart-data-flow/debug-pipeline.png)

1. Data Flow アクティビティのパイプライン デバッグでは、アクティブなデバッグ クラスターが使用されますが、それでも初期化には少なくとも 1 分かかります。 進行状況は **[出力]** タブで追跡することができます。実行が正常に完了したら、眼鏡のアイコンをクリックして [監視] ウィンドウを開きます。

    ![デバッグの出力](media/quickstart-data-flow/debugging-output.png)

1. [監視] ウィンドウには、各変換手順で使用した行数と所要時間が表示されます。

    ![変換のモニター](media/quickstart-data-flow/4-transformations.png)

1. 変換をクリックすると、データの列とパーティション分割に関する詳細情報が表示されます。

    ![変換の詳細](media/quickstart-data-flow/transformation-details.png)

このクイックスタートに正しく従った場合は、シンク フォルダーに 83 行と 2 列が書き込まれているはずです。 BLOB ストレージをチェックすることで、データを確認できます。


## <a name="next-steps"></a>次のステップ

次の記事に進んで、Azure Synapse Analytics のサポートを確認します。

> [!div class="nextstepaction"]
> [パイプラインとアクティビティ](../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
> [マッピング データ フローの概要](../data-factory/concepts-data-flow-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
> [データ フローの式言語](../data-factory/data-flow-expression-functions.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)