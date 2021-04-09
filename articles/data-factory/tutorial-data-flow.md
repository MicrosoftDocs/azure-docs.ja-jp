---
title: マッピング データ フローを使用してデータを変換する
description: このチュートリアルでは、Azure Data Factory で マッピング データ フローを使用してデータを変換するための詳細な手順を説明します
author: dcstwh
ms.author: weetok
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/11/2021
ms.openlocfilehash: 52509dbf529076eaa0af73ea48db9a7f3fd10241
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584518"
---
# <a name="transform-data-using-mapping-data-flows"></a>マッピング データ フローを使用してデータを変換する

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory を初めて使用する場合は、「[Azure Data Factory の概要](introduction.md)」を参照してください。

このチュートリアルでは、Azure Data Factory ユーザー インターフェイス (UX) を使い、マッピング データ フローを使用して Azure Data Lake Storage (ADLS) Gen2 ソースから ADLS Gen2 シンクにデータをコピーして変換するパイプラインを作成します。 このチュートリアルの構成パターンは、マッピング データ フローを使用してデータを変換するときに拡張することができます

 >[!NOTE]
   >このチュートリアルでは、一般にデータ フローをマップすることを目的としています。 データ フローは、Azure Data Factory および Synapse パイプラインの両方で使用できます。 Azure Synapse パイプラインのデータ フローを初めて使用する場合は、[Azure Synapse パイプラインを使用したデータ フロー](https://docs.microsoft.com/azure/synapse-analytics/concepts-data-flow-overview)に関するページに従ってください 
   
このチュートリアルでは、次の手順を実行します。

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * Data Flow アクティビティを含むパイプラインを作成します。
> * 4 つの変換を使用して、マッピング データ フローを構築します。
> * パイプラインをテスト実行します。
> * Data Flow アクティビティを監視します。

## <a name="prerequisites"></a>前提条件
* **Azure サブスクリプション**。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* **Azure ストレージ アカウント**。 ADLS ストレージを、*ソース* と *シンク* のデータ ストアとして使用します。 ストレージ アカウントがない場合の作成手順については、[Azure のストレージ アカウントの作成](../storage/common/storage-account-create.md)に関するページを参照してください。

このチュートリアルで変換するファイルは MoviesDB です。このファイルは、[こちら](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv)にあります。 GitHub からファイルを取得するには、コンテンツを任意のテキスト エディターにコピーして、.csv ファイルとしてローカルに保存します。 ファイルをご自分のストレージ アカウントにアップロードするには、[Azure portal を使用した BLOB のアップロード](../storage/blobs/storage-quickstart-blobs-portal.md)に関するページを参照してください。 例では、'sample-data' という名前のコンテナーを参照しています。

## <a name="create-a-data-factory"></a>Data Factory の作成

この手順では、データ ファクトリを作成し、Data Factory UX を開いて、データ ファクトリにパイプラインを作成します。

1. **Microsoft Edge** または **Google Chrome** を開きます。 現在、Data Factory の UI がサポートされる Web ブラウザーは Microsoft Edge と Google Chrome だけです。
2. 左側のメニューで、 **[リソースの作成]**  >  **[統合]**  >  **[Data Factory]** を選択します。

   ![[新規] ウィンドウでの [Data Factory] の選択](./media/doc-common-process/new-azure-data-factory-menu.png)

3. **[新しいデータ ファクトリ]** ページで、 **[名前]** に「**ADFTutorialDataFactory**」と入力します。

   Azure データ ファクトリの名前は *グローバルに一意* にする必要があります。 データ ファクトリの名前の値に関するエラー メッセージが表示された場合は、別の名前を入力してください。 (yournameADFTutorialDataFactory など)。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関するページを参照してください。

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="重複する名前に関する、新しい Data Factory のエラーメッセージ。":::
4. データ ファクトリを作成する Azure **サブスクリプション** を選択します。
5. **[リソース グループ]** で、次の手順のいずれかを行います。

    a. **[Use existing (既存のものを使用)]** を選択し、ドロップダウン リストから既存のリソース グループを選択します。

    b. **[新規作成]** を選択し、リソース グループの名前を入力します。 
         
    リソース グループの詳細については、[リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/management/overview.md)に関するページを参照してください。 
6. **[バージョン]** で、 **[V2]** を選択します。
7. **[場所]** で、データ ファクトリの場所を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリによって使用されるデータ ストア (Azure Storage、SQL Database など) やコンピューティング (Azure HDInsight など) は、他のリージョンに存在していてもかまいません。
8. **［作成］** を選択します
9. 作成が完了すると、その旨が通知センターに表示されます。 **[リソースに移動]** を選択して、Data factory ページに移動します。
10. **[Author & Monitor]\(作成と監視\)** を選択して、別のタブで Data Factory (UI) を起動します。

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Data Flow アクティビティを含むパイプラインの作成

この手順では、Data Flow アクティビティを含むパイプラインを作成します。

1. **[Let's get started]\(始めましょう\)** ページで **[Create pipeline]\(パイプラインの作成\)** を選択します。

   ![パイプラインの作成](./media/doc-common-process/get-started-page.png)

1. パイプラインの **[全般]** タブで、パイプラインの **名前** として「**TransformMovies**」と入力します。
1. ファクトリの上部のバーで、 **[Data Flow のデバッグ]** スライダーをオンにスライドします。 デバッグ モードを使用すると、ライブ Spark クラスターに対する変換ロジックの対話型テストが可能になります。 Data Flow クラスターのウォームアップには 5 から 7 分かかるため、ユーザーが Data Flow の開発を計画している場合は、最初にデバッグを有効にすることをお勧めします。 詳細については、[デバッグ モード](concepts-data-flow-debug-mode.md)に関するページを参照してください。

    ![Data Flow アクティビティ](media/tutorial-data-flow/dataflow1.png)
1. **[アクティビティ]** ウィンドウで、 **[移動と変換]** アコーディオンを展開します。 ウィンドウから **Data Flow** アクティビティをパイプライン キャンバスにドラッグ アンド ドロップします。

    ![Data Flow アクティビティをドロップできるパイプライン キャンバスを示すスクリーンショット。](media/tutorial-data-flow/activity1.png)
1. **[Adding Data Flow]\(Data Flow の追加\)** ポップアップで、 **[Create new Data Flow]\(新しい Data Flow の作成\)** を選択し、データ フローに **TransformMovies** という名前を付けます。 終了したら、[完了] をクリックします。

    ![新しいデータ フローを作成するときにデータ フローの名前を指定する場所を示すスクリーンショット。](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>データ フロー キャンバスでの変換ロジックの作成

Data Flow を作成すると、データ フロー キャンバスが自動的に表示されます。 この手順では、ADLS ストレージ内の moviesDB.csv を取得し、1910 年から 2000 年までのコメディの平均評価を集計するデータ フローを作成します。 次に、このファイルを ADLS ストレージに書き戻します。

1. データ フロー キャンバスで **[Add Source]\(ソースの追加\)** ボックスをクリックして、ソースを追加します。

    ![[変換元の追加] ボックスを示すスクリーンショット。](media/tutorial-data-flow/dataflow2.png)
1. ソースに **MoviesDB** という名前を付けます。 **[新規]** をクリックして、新しいソース データセットを作成します。

    ![変換元の名前を指定した後で [新規] を選択する場所を示すスクリーンショット。](media/tutorial-data-flow/dataflow3.png)
1. **[Azure Data Lake Storage Gen2]** を選択します。 [続行] をクリックして続行します。

    ![Azure Data Lake Storage Gen2 のタイルを示すスクリーンショット。](media/tutorial-data-flow/dataset1.png)
1. **[DelimitedText]** を選択します。 [続行] をクリックして続行します。

    ![DelimitedText のタイルを示すスクリーンショット。](media/tutorial-data-flow/dataset2.png)
1. データセットに **MoviesDB** という名前を付けます。 リンクされたサービスのドロップダウンで、 **[新規]** を選択します。

    ![[リンクされたサービス] ドロップダウン リストを示すスクリーンショット。](media/tutorial-data-flow/dataset3.png)
1. リンクされたサービスの作成画面で、ADLS gen2 のリンクされたサービスに **ADLSGen2** という名前を付けて、使用する認証方法を指定します。 次に、接続の資格情報を入力します。 このチュートリアルでは、アカウント キーを使用してストレージ アカウントに接続しています。 **[テスト接続]** をクリックすると、資格情報が正しく入力されたことを確認できます。 完了したら [作成] をクリックします。

    ![リンクされたサービス](media/tutorial-data-flow/ls1.png)
1. データセットの作成画面に戻ったら、 **[ファイル パス]** フィールドの下でファイルが配置されている場所を入力します。 このチュートリアルでは、moviesDB.csv ファイルはコンテナー sample-data に配置されています。 ファイルにはヘッダーが含まれているため、 **[First row as header]\(最初の行をヘッダーにする\)** をオンにします。 ストレージ内のファイルからヘッダー スキーマを直接インポートするには、 **[From connection/store]\(接続/ストアから\)** を選択します。 完了したら、[OK] をクリックします。

    ![データセット](media/tutorial-data-flow/dataset4.png)
1. デバッグ クラスターが起動している場合は、ソース変換の **[Data Preview]\(データのプレビュー\)** タブに移動し、 **[更新]** をクリックして、データのスナップショットを取得します。 データ プレビューを使用すると、変換が正しく構成されていることを確認できます。

    ![データをプレビューして変換が正しく構成されていることを確認できる場所を示すスクリーンショット。](media/tutorial-data-flow/dataflow4.png)
1. データ フロー キャンバスでソース ノードの横にあるプラス アイコンをクリックして、新しい変換を追加します。 最初に追加する変換は、**フィルター** です。

    ![データ フロー キャンバス](media/tutorial-data-flow/dataflow5.png)
1. フィルター変換に **FilterYears** という名前を付けます。 **[フィルター適用]** の横にある式ボックスをクリックして、式ビルダーを開きます。 ここでフィルター条件を指定します。

    ![[フィルター適用] 式ボックスを示すスクリーンショット。](media/tutorial-data-flow/filter1.png)
1. データ フローの式ビルダーでは、さまざまな変換で使用する式を対話形式で作成できます。 式には、組み込み関数、入力スキーマの列、ユーザー定義のパラメーターを含めることができます。 式の作成方法の詳細については、[Data Flow の式ビルダー](concepts-data-flow-expression-builder.md)に関するページを参照してください。

    このチュートリアルでは、1910 年から 2000 年の間に公開された、ジャンルがコメディの映画をフィルター処理します。 現在、年は文字列になっているため、```toInteger()``` 関数を使用して整数に変換する必要があります。 以上演算子 (>=) と以下演算子 (<=) を使用して、年のリテラル値 1910 と 2000 に対する比較を行います。 これらの式を and (&&) 演算子を使用して結合します。 式は次のようになります。

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    コメディ映画を見つけるには、```rlike()``` 関数を使用して、ジャンル列でパターン 'Comedy' を検索します。 rlike 式と年の比較を結合して、次を取得します。

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    デバッグ クラスターがアクティブになっている場合は、 **[更新]** をクリックして使用された入力と比較した式の出力を表示して、ロジックを確認できます。 データ フローの式言語を使用してこのロジックを実現する方法に対する正解は複数あります。

    ![Assert](media/tutorial-data-flow/filter2.png)

    式の操作が完了したら、 **[Save and Finish]\(保存して終了する\)** をクリックします。

1. フィルターが正しく機能していることを確認するには、**データ プレビュー** をフェッチします。

    ![フェッチした [データのプレビュー] を示すスクリーンショット。](media/tutorial-data-flow/filter3.png)
1. 次に追加する変換は、 **[Schema modifier]\(スキーマ修飾子\)** の下にある **[集計]** 変換です。

    ![集計スキーマ修飾子を示すスクリーンショット。](media/tutorial-data-flow/agg1.png)
1. 集計変換に **AggregateComedyRatings** という名前を付けます。 **[グループ化]** タブで、ドロップダウンから **[year]** を選択し、映画の公開年ごとに集計をグループ化します。

    ![[Aggregate Settings]\(集計の設定\) の下にある [グループ化] タブの年オプションを示すスクリーンショット。](media/tutorial-data-flow/agg2.png)
1. **[集計]** タブに移動します。左側のテキスト ボックスで、集計列に **AverageComedyRating** という名前を付けます。 式ビルダーを使用して集計式を入力するには、右側の式ボックスをクリックします。

    ![[Aggregate Settings]\(集計の設定\) の下にある [集計] タブの年オプションを示すスクリーンショット。](media/tutorial-data-flow/agg3.png)
1. 列 **[Rating]** の平均値を取得するには、```avg()``` 集計関数を使用します。 **Rating** は文字列で、```avg()``` で受け入れられるのは数値入力なので、```toInteger()``` 関数を使用して値を数値に変換する必要があります。 式は次のようになります。

    ```avg(toInteger(Rating))```

    完了したら、 **[Save and Finish]\(保存して終了する\)** をクリックします。

    ![保存された式を示すスクリーンショット。](media/tutorial-data-flow/agg4.png)
1. 変換出力を表示するには、 **[Data Preview]\(データのプレビュー\)** タブに移動します。 **year** と **AverageComedyRating** の 2 つの列だけがあることに注目してください。

    ![Aggregate](media/tutorial-data-flow/agg3.png)
1. 次に、 **[Destination]\(変換先\)** の下で **[シンク]** 変換を追加します。

    ![[Destination]\(変換先\) でシンク変換を追加する場所を示すスクリーンショット。](media/tutorial-data-flow/sink1.png)
1. シンクに **Sink** という名前を付けます。 **[新規]** をクリックして、シンク データセットを作成します。

    ![シンクの名前を指定して新しいシンク データセットを作成できる場所を示すスクリーンショット。](media/tutorial-data-flow/sink2.png)
1. **[Azure Data Lake Storage Gen2]** を選択します。 [続行] をクリックして続行します。

    ![選択できる Azure Data Lake Storage Gen2 のタイルを示すスクリーンショット。](media/tutorial-data-flow/dataset1.png)
1. **[DelimitedText]** を選択します。 [続行] をクリックして続行します。

    ![データセット](media/tutorial-data-flow/dataset2.png)
1. シンク データセットに **MoviesSink** という名前を付けます。 リンクされたサービスの場合、手順 6 で作成した ADLS gen2 のリンクされたサービスを選択します。 データの書き込み先となる出力フォルダーを入力します。 このチュートリアルでは、コンテナー 'sample-data' 内のフォルダー ' output ' に書き込んでいます。 フォルダーは、事前に存在している必要はなく、動的に作成することができます。 **[First row as header]\(最初の行をヘッダーにする\)** をオンに設定し、 **[スキーマのインポート]** で **[なし]** を選択します。 [完了] をクリックします。

    ![シンク](media/tutorial-data-flow/sink3.png)

これで、データ フローの構築が完了しました。 これをパイプラインで実行する準備ができました。

## <a name="running-and-monitoring-the-data-flow"></a>Data Flow を実行して監視する

パイプラインを発行する前にデバッグすることができます。 この手順では、データ フロー パイプラインのデバッグ実行をトリガーします。 データのプレビューではデータが書き込まれませんが、デバッグ実行によってシンクの変換先にデータが書き込まれます。

1. パイプライン キャンバスに移動します。 **[デバッグ]** をクリックして、デバッグ実行をトリガーします。

    ![[デバッグ] が強調して示されているパイプライン キャンバスを示すスクリーンショット。](media/tutorial-data-flow/pipeline1.png)
1. Data Flow アクティビティのパイプライン デバッグでは、アクティブなデバッグ クラスターが使用されますが、それでも初期化には少なくとも 1 分かかります。 進行状況は **[出力]** タブで追跡することができます。実行が正常に完了したら、眼鏡のアイコンをクリックして [監視] ウィンドウを開きます。

    ![パイプライン](media/tutorial-data-flow/pipeline2.png)
1. [監視] ウィンドウには、各変換手順で使用した行数と所要時間が表示されます。

    ![各変換ステップで費やされた行数と時間を確認できる監視ペインを示すスクリーンショット。](media/tutorial-data-flow/pipeline3.png)
1. 変換をクリックすると、データの列とパーティション分割に関する詳細情報が表示されます。

    ![監視](media/tutorial-data-flow/pipeline4.png)

このチュートリアルに正しく従った場合は、シンク フォルダーに 83 行と 2 列が書き込まれているはずです。 BLOB ストレージをチェックすることで、データが正しいことを確認できます。

## <a name="next-steps"></a>次のステップ

このチュートリアルのパイプラインでは、1910 年から 2000 年のコメディの平均評価を集計し、そのデータを ADLS に書き込むデータ フローを実行します。 以下の方法を学習しました。

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * Data Flow アクティビティを含むパイプラインを作成します。
> * 4 つの変換を使用して、マッピング データ フローを構築します。
> * パイプラインをテスト実行します。
> * Data Flow アクティビティを監視します。

[データ フローの式言語](data-flow-expression-functions.md)の詳細を確認します。
